---
layout: post
title: ! 'Clearance, Varnish and Rails: An Exercise in Bug Stalking'
enki_id: 21
tags: rails, varnish, clearance, cookies
---

### The Background

I was working on upgrading a large Rails application that has a public facing side and an authenticated side (for administrators to update content etc). As a precursor to the Rails upgrade, I upgraded the Clearance gem (used for authentication) from version 0.16 to 1.0.

### The Issue

Soon after the Clearance upgrade code was pushed to production, users started complaining about getting logged out (causing them to lose what they were working on).
Seemingly at random (sometimes from clicking a link, sometimes after submitting a form, but other time both actions worked perfectly), authenticated users were being redirected to the login page. The change was quickly rolled back and everything returned to normal; meanwhile I started to investigate what went wrong.

### The Investigation

The first thing I tried to do was to reproduce the issue locally. Unfortunately, I could not. I booted up a bunch of different web servers, matched my development environment as close to production as possible and attempted to replicate every action the users normally take, but failed to get logged out while authenticated.

Undeterred, I trawled through the Clearance docs and github issues, and studied the diff between version 0.16 and 1.0 line by line trying to find something that could cause or explain 'random' authentication failure. Nothing jumped out at me.

Just in case, I double checked all the obvious Rails gotchas: CSRF tokens, ajax requests, and cookie and session settings but found nothing amiss.

I also looked at the Varnish config, but it was set up to only cache the public facing pages and allow authenticated requests to pass through to Rails.

The fact that the issue was occurring randomly kept making me think that it must be something more than a bug in Clearance or in the Rails production environment.

I needed more information about the circumstances, so my client deployed the buggy code to Production again for an hour and afterwards I studied the logs bit by bit.

Pretty soon a recurring request grabbed my attention: an ajax request that is sent to the server every 30 seconds to maintain a lock on a particular record while a user is editing that record.

The logs showed the 'lock' request coming in 30 seconds apart with successful responses, but a few minutes later one such request responded with a redirect to the login page. All subsequent requests responded in the same way: with a redirect.

Then at some point there was a user initiated request which also responded in a redirect to the login page. The user logged in, and the next 'lock' request responded successfully again. Yet again, some minutes later, they started responding with redirects.

Something was causing the user to lose their Clearance session, which only showed up for them when they took the next action (e.g. submitting the form they were working on, or navigating somewhere authenticated) - thus producing 'random' logouts.

But what was the trigger? I studied every request between the last successful response and the next unsuccessful one but the logs did not reveal it.

About this time I learned of some interesting behaviour which was occurring in the current (stable) production code. If an authenticated user navigated to the public site and submitted a form there they got logged out and redirected to the login screen on their next authenticated request.

Upon closer inspection of the public form POST request, I noticed that the request contained the Clearance remember\_token cookie with the correct value, but in the response the remember\_token cookie contained an empty value. This empty cookie would overwrite the real value in the browser, effectively logging the user out immediately, but only manifesting itself the next time the user made an authenticated request.

This seemed to be too much of a coincidence to be unrelated, but because there are only two forms on the public site and they are not used by the administrators of the system, I couldn't find the connection between them.

Frustrated with getting so close to the problem but still having no idea what was causing the log outs, I asked my client to once more put the buggy code in Production (after work hours) so that I could test it out myself.

Beginning with the knowledge of the weird empty cookie in public POST requests, I quickly realised that in this latest release, *every* request sent to the public site was responding with an empty remember\_token cookie. Was this the trigger then? I got confirmation from my client that administrators consistently use the public site and the protected site concurrently while updating content; this nicely explained the 'random' log outs they'd been experiencing.

I remembered from studying the clearance source code diff that the method that set the cookie changed slightly between versions. In version 0.16, it only set the cookie when the user is signed\_in (i.e. the server had received a valid remember\_token), whereas in version 1.0 it always set the cookie no matter what. This meant that if the server thought the user wasn't signed in, it would set the remember\_token to a blank value: exactly the behaviour I was seeing.

```bash
lib/clearance/session.rb
```
```diff
   def add_cookie_to_headers(headers)
-     if signed_in?
-        Rack::Utils.set_cookie_header!(
-          headers, REMEMBER_TOKEN_COOKIE,
-          :value => current_user.remember_token,
-          :expires => Clearance.configuration.cookie_expiration.call,
-          :secure => Clearance.configuration.secure_cookie,
-          :httponly => Clearance.configuration.httponly,
-          :path => '/'
-        )
-     end
+    Rack::Utils.set_cookie_header!(
+        headers,
+        REMEMBER_TOKEN_COOKIE,
+        :value => remember_token,
+        :expires => Clearance.configuration.cookie_expiration.call,
+        :secure => Clearance.configuration.secure_cookie,
+        :httponly => Clearance.configuration.httponly,
+        :path => '/'
+    )
    end
```

But why did the server think the user wasn't signed in on every public facing request while correctly recognising users on authenticated requests?

I had checked numerous times that the remember\_token with the correct value was being sent on every request… or was it?

### The Solution

The answer was in the varnish config all along: it was stripping cookies from public requests in order to perform transparent caching; however a typo in the config meant that while the request cookies were being stripped as intended, response cookies were not.

So varnish was stripping the remember\_token out of the request, causing Clearance to think the user was not logged in. In the latest version, it would always send back an empty cookie, overwriting the real value of the cookie in the browser and sending the user to the login page on their next request to an authenticated page.

### The Fix

The fix turned out to be as simple as correcting the config to strip the cookies from the response on the public site requests.

However, I am thinking about submitting a pull request to Clearance to only set the cookie when the remember\_token is in the request, as I am not convinced that it should be sending an empty remember\_token cookie on every unauthenticated request.
