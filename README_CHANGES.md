# Replace Deeds — Update Notes

## What changed this round

### 1. Critical fix from last time
The previous update had a leftover call to a function (`DB.setNamespace`) that had
already been deleted when the Decoy Interface was removed. That crashed the app the
moment it loaded, which is why it got stuck on the intro/splash screen. That dead
call is now removed and the whole file has been re-verified (syntax-checked, every
`DB.`/`Store.`/`StreakManager.`/`HistoryLog.` method call cross-checked against what
actually exists, HTML tags balanced). The app should now get past the splash screen
normally.

### 2. New 50-quote "ultra motivational" daily quote system
Every place a motivational quote appears in the app (the banner on the main screen,
and the celebration message when you finish all your habits for the day) now pulls
from the same rotating pool of 50 quotes — intense, blood-pumping, streak-focused
lines meant to push you to actually finish, not gentle affirmations. For example:
"Get up. Show up. Never break the streak.", "The streak doesn't care how you feel.
Show up NOW.", "No shortcuts. No skips. Just relentless progress."

- One quote per day — not random anymore. It's chosen deterministically from
  today's date, so it's the same quote all day, and the full set of 50 repeats every
  50 days.
- Fully translated across all 5 languages (English, Urdu, Hindi, French, Chinese) —
  250 quotes total, matched by meaning so switching language mid-day shows the same
  quote translated, not a different one.
- The separate, gentler "streak freeze" messages (shown when you use a freeze day)
  were left as they are — different purpose, not really a "motivational quote."

## New files: firestore.rules and storage.rules

These reflect this exact version of the app's actual data model (I checked every
collection your code reads and writes -- password_requests, chat_requests,
family_members, messages, groups, etc. -- this app's structure is a bit different
from earlier versions, so these rules are written specifically to match what's in
THIS index.html, not reused from anywhere else).

You need to publish both yourself:
- firestore.rules -> Firebase Console -> your project -> Firestore Database -> Rules
  tab -> paste over what's there -> Publish.
- storage.rules -> Firebase Console -> your project -> Storage -> Rules tab -> paste
  over what's there -> Publish. This one is new because this version of the app
  uploads chat photos/videos to Firebase Storage (uploadChatMedia), which earlier
  rules never covered.

What they set up, in plain terms:
- Passwords are never stored anywhere -- the "Forgot Password" flow just sends the
  owner a request (open to anyone, even signed out, since you may not be able to
  log in when you use it); only the owner can see and resolve that list.
- family_members/profiles are readable by any signed-in user (needed for contact
  lists and chat), but only the owner can approve someone -- a regular user can
  never grant themselves access.
- Each 1:1 chat is only readable by its two participants (or the owner) -- nobody
  else can read or even discover a conversation they're not part of.
- Only a group's creator (or the owner) can add/remove its members.
- Chat photo/video uploads: only the uploader can write into their own folder
  (capped at 15MB, images/videos only); any signed-in user can view a file once
  it's been shared with them in a chat.

One detail worth knowing: the rules identify "the owner" by checking that the
signed-in account's login email is exactly owner@replacedeeds.app -- which is
exactly what a signup with username "owner" produces. If you ever rename the
owner's username away from "owner" (the OWNER_USERNAME constant near the top of
the app's code), update the matching line in firestore.rules too, or the
owner-only checks will stop working.

## Before you rely on this in production
- Publish both rule files.
- Confirm the app now passes the splash screen and loads normally.
- Check the main-screen quote and the "all done today" celebration message both show
  the same quote, and that it's the intense/motivational style, not the old gentle one.
- Switch language and confirm the quote changes to the matching translation for the
  same day (not a random different quote).
