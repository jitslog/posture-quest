# Posture Quest 🔥

A daily, gamified routine to correct **anterior pelvic tilt** and build automatic
good posture. Built for an ADHD brain: real rewards you choose, a streak you don't
want to break, mystery bonuses, and visible before/after progress.

It's a single offline web app — no account, no backend. Your data lives only on
your device (`localStorage`).

## What's inside
- **Two routines** that *both* keep your streak alive: Full (~12 min, summer) and
  Quick (~5 min, busy/school days). Guided step-by-step with timers and rep counts.
- **Reward Store** — you stock it with *real* rewards (boba, gaming, a purchase),
  price them in 💎, and cash them in. That's what makes the points mean something.
- **Mystery bonus** after every session (rare jackpots) — the novelty hit.
- **Streak + weekly freeze** — one missed day won't reset you.
- **Progress check-ins** — weekly side-photo + wall test, shown over time so you
  *see* the tilt correcting.

## Run it on your computer
Just open `index.html` in any browser (double-click it). Everything works except
"install to home screen" and the service worker, which need a real URL (below).

## Put it on your phone (recommended)
A true installable app needs HTTPS, so host it free on **GitHub Pages**:

1. Create a GitHub repo and push this folder:
   ```bash
   cd posture-app
   git init && git add . && git commit -m "Posture Quest"
   gh repo create posture-quest --public --source=. --push
   ```
2. Enable Pages: repo **Settings → Pages → Branch: `main` / root → Save**.
3. After a minute you'll get a URL like `https://<you>.github.io/posture-quest/`.
4. On your phone, open that URL in Safari/Chrome → **Share → Add to Home Screen**.
   It now launches full-screen and works offline. 🎉

(No GitHub? Any static host works — Netlify drop, Cloudflare Pages, etc.)

## Move progress between phone and computer
**Setup → Export** on one device, transfer the JSON file, **Import** on the other.
No cloud involved.

## Buddy / accountability sync (optional)
Lets two people see each other's **streak, whether they've done it today, and
which session (Full/Quick)**. Photos, notes, points and rewards are **never**
shared — they stay on each device.

It needs a free Firebase project (one person creates it; both apps use the same
config):

1. Go to <https://console.firebase.google.com> → **Add project** (any name) →
   you can disable Analytics.
2. In the project: **Build → Firestore Database → Create database** → start in
   **production mode** → pick a location.
3. **Firestore → Rules** — **select all existing text and replace the whole file
   with exactly this**, then Publish. (Both `match` blocks must sit *inside*
   `match /databases/{database}/documents { … }`; open buddy groups are fine for
   non-sensitive stats — use a non-obvious buddy code.)
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /groups/{code}/members/{member} {
         allow read, write: if true;
       }
       match /users/{uid} {
         allow read, write: if request.auth != null && request.auth.uid == uid;
         match /{document=**} {
           allow read, write: if request.auth != null && request.auth.uid == uid;
         }
       }
     }
   }
   ```
4. **Project settings (gear) → General → Your apps → Web (`</>`)** → register an
   app → copy the `firebaseConfig` values.
5. Paste `apiKey`, `authDomain`, `projectId`, `appId` into the
   `window.FIREBASE_CONFIG` block near the top of `index.html`, commit, push.
6. In the app: **Setup → Do it with a buddy** → enter your name + a shared buddy
   code, tap **Connect**. Your buddy installs the app, enters their name and the
   **same code**, and you'll see each other on the Home screen.

Until the config is filled in, the app works fully — the buddy panel just shows
a "set up a buddy" prompt.

## Account sync across your own devices (email magic link)
Sign in with your email so your streak, points, rewards and history follow you
to any device. **Photos stay on the device they were taken on** (privacy + size).

Two one-time toggles in the Firebase console (same project):

1. **Authentication → Get started → Sign-in method → Email/Password → enable it,
   and turn ON "Email link (passwordless sign-in)"** → Save.
2. **Authentication → Settings → Authorized domains → Add domain →
   `jitslog.github.io`** (localhost is already allowed).
3. Make sure the Firestore rules above include the `users/{uid}` block, and Publish.

Then in the app: **Setup → Account & cloud sync** → enter your email → tap
**Email me a sign-in link** → open the email → tap the link → you're signed in and
syncing. Do the same on any other device with the same email to load everything.

## Replace the icon (optional)
`icons/icon.svg` is the app icon. Swap it for your own SVG, or add PNG sizes and
list them in `manifest.json` if you want pixel-perfect home-screen icons on older
phones.

---
*Not medical advice. If you have pain or a diagnosed back/hip condition, see a
physio before starting.*
