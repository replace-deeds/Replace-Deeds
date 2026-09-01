# Replace Deeds — Update Notes

## ✅ Implemented (8 of 9 requested features — Fake Interface intentionally skipped for now)

1. **XP Shop warning popup** — buying a shop item with insufficient XP now shows a clear
   centered popup ("Not Enough XP — you need X, you have Y, need Z more") instead of a
   toast.

2. **Chat: images/videos + WhatsApp-style delete**
   - New 📎 button next to the chat input lets you attach a photo or short video.
   - Images are auto-compressed and sent inline.
   - **Video limitation (please read):** there is no file-storage server in this app —
     only Firestore, which caps each message at ~1MB. So videos must be small (roughly
     under 700KB — a few seconds, low resolution). Anything bigger is rejected with a
     friendly message. This is a hard platform limit, not a bug.
   - Any message can be deleted (🗑️ on each bubble). It's replaced everywhere with
     "Deleted by sender" / "Deleted by receiver" (or "Deleted by @username" in groups),
     exactly like WhatsApp.

3. **Owner Dashboard: stores username, name, father's name** (password is written to a
   separate `credentials` collection at signup, visible only from the owner side of the
   app) — **plus a "Forgot Password?" link** on the login screen that opens a support
   ticket. The user fills in username/name/father's name/message, and can then message
   back and forth with the owner right there to prove their identity.
   - **Honest limitation:** this app has no server-side admin function, so it **cannot
     actually change another user's cloud password** by itself — only Firebase's own
     Admin SDK (a backend you'd have to set up separately) can do that. What this DOES
     give you is a clean identity-verification conversation; once you're sure it's really
     them, you'd reset their password manually from the Firebase Console, or help them
     recall it. I didn't want to fake a "reset" button that doesn't really work.

4. **Chat "Request Access" button** — appears for anyone without messaging access yet.
   Their request (with an optional message) shows up right inside your existing "Manage
   Connections" screen, sorted to the top with a 🔔 New Request tag, next to the same
   approve toggle you already use.

5. **Archive completed habits** — new Settings toggle. When on, any habit at 100% today
   collapses into a "✅ Completed (n)" group at the bottom of its section instead of
   staying in the main list.

6. **Move a habit between sections** — long-press (about half a second) on any habit,
   pick the target section from the popup. Streak, XP, and history stay exactly as they
   were — only which section it lives in changes.

7. **Undo / Redo** — the History tab now has ↩️ Undo / ↪️ Redo buttons. They step through
   your last ~30 changes (add/edit/delete habit or section, add/edit/delete task,
   progress changes, XP shop purchases, habit moves) one at a time, in order — like
   Ctrl+Z / Ctrl+Y. (Not an arbitrary "undo just this one change from three days ago
   without touching anything after it" — that's not really possible in general once later
   changes depend on it, so this follows the standard undo-stack model instead.)

8. **Habit time + ordering + notifications**
   - Add/Edit Habit now has an optional time field. Habits within each section are
     automatically ordered by time (earliest first); habits with no time stay at the end.
   - A 🔔 "Notify me at this time" checkbox appears once a time is set — turning it on
     asks for notification permission.
   - When it fires, you get a random phrase in your app language (English, Urdu, Hindi,
     French, or Chinese — matching the app's existing language options), e.g. "⏰ Time for
     Fajr Prayer!" — with about 15 different phrasings per language so it doesn't feel
     robotic.
   - **Honest limitation:** this checks every 20 seconds *while the app is open or
     running in the background tab*. A plain web app with no push-notification server
     cannot wake itself up at an exact time after being fully closed by the phone's OS —
     that needs a real backend push service. This covers the large majority of real
     day-to-day use, but isn't a guaranteed alarm clock.

## ⏸️ Not done yet (by your request)

**9. Fake/Secondary Interface** — skipped for now, as you asked. Some groundwork for it
(a way to keep two completely separate profiles under one PIN-protected lock screen) was
sketched but is **not wired up or tested** — please don't enable/rely on anything
related to it yet. Happy to build it properly whenever you're ready.

## Before you rely on this in production

Please test on a real device, especially:
- Adding/editing habits with the new time & notification fields
- Sending an image and a video in chat, then deleting a message from both sides
- The Forgot Password ticket flow end-to-end (needs your Firestore rules to allow writes
  to the new `credentials` and `recovery_requests` collections — check your Firebase
  Console rules if requests fail)
- Undo/Redo after a few different kinds of changes
