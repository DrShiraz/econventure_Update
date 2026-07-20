# EconVenture — Classroom deployment guide

This is a live-classroom game. It is designed for a teacher to run with a section (or several sections at once), pace the class concept by concept, and see who is where in real time. It has an offline solo-practice fallback, but the main mode assumes a classroom code that the teacher creates.

## What you need before class

1. A Google account (any).
2. A place to host the HTML files. Two options: GitHub Pages (free, five minutes), or your own web server. All eleven HTML files are static; any static host works.
3. Ten minutes for a one-time setup, done once per instructor account. From then on, creating a new class takes ten seconds.

## How the class works (one paragraph)

You open the faculty console, click "Create classroom", and get a six-letter class code (something like `7KQ2MB`). You write it on the board. Students open the Hub URL, type their name, roll number, and the class code, and land in your classroom. You now control what they can see. Level 1 (the sixteen-concept Founder's Journey) is walled: each concept is gated by a one-question opening check, then the concept, then two closing questions, and a student cannot enter concept N+1 unless you have opened it. Level 2 (the ten managerial-economics acts) is gated per act the same way. You watch the roster fill in as students join, see who is on which concept, and see the pre-vs-post score gap concept by concept, so you know what to spend time on.

## One-time setup (ten minutes, do once)

**Step 1: host the eleven HTML files.**
- Fastest path is GitHub Pages. Create a new empty repo (e.g. `econventure`), upload the whole bundle to the root, and enable Pages under Settings → Pages → Deploy from main branch, root folder. You get a URL like `https://<yourname>.github.io/econventure/`.
- Open `EconVenture_Hub.html` in a text editor. Find the line near the top: `const BASE = 'https://YOURNAME.github.io/econventure/';`. Replace with your actual URL. Save. Commit. Push. The Hub now knows where the other files live.

**Step 2: set up the backend (Apps Script).**
- Go to script.google.com → New project.
- Paste `EconVenture_AppsScript_Code.gs` into `Code.gs`. It replaces the default file.
- At the top of that file, change `var ADMIN_PASSWORD = 'change-me-now';` to something only you know.
- File → New → HTML file → name it `admin`. Delete the default content. Paste in `EconVenture_AppsScript_admin.html`.
- Repeat once more: File → New → HTML file → name it `projector`. Paste in `EconVenture_AppsScript_projector.html`. This is the read-only view for the classroom projector.
- Deploy → New deployment → Web app → Execute as **Me** → Access **Anyone**. Copy the `/exec` URL you're given. This is your **classroom URL**.

**Step 3: connect the Hub and Founder to the backend.**
- Open `EconVenture_Hub.html` and `EconVenture_Founder.html`. In each, find `const CLASS_URL = '';` near the top. Paste your `/exec` URL between the quotes. Save. Commit. Push.
- This is what lets students on GitHub Pages (or any host) talk to the Apps Script server for class codes, gates, and quiz logging.

**Step 4: bookmark your faculty console.**
- Take your `/exec` URL, add `?view=admin` to the end. That's your console. Sign in with the admin password you set.

Done. Test it once: create a classroom, open the Hub in another browser, join with the code, confirm you see yourself in the roster.

## Running a live class

**Before the students arrive:**
- Open the faculty console. Sign in.
- Click "Create classroom", give it a name (e.g. "MBA-1 Section A, Managerial Econ, 22 Feb"). You get a code.
- Write the class code on the board, big.

**When students arrive:**
- They open the Hub URL (bookmark it for them).
- They type their name, roll number, and the class code. That's all.
- The moment they join, they appear in your roster with a LIVE tag.

**During class:**
- The console has two grids. The top grid is the eleven acts (the Hub-level journey: Level 1 Aggregation, Level 1 Founder's Journey, Level 2 Acts 1 through 8, the Competitive Run). The bottom grid is the sixteen concepts inside the Founder's Journey (Scarcity, Opportunity cost, Cost of capital, and so on).
- Click any tile to open or close that specific act or concept. Students see the change within ten seconds.
- "Open next" buttons open the next locked item, so you don't have to remember what's open.
- "Open all" and "Close all" are there for when you want to release the whole thing or lock everything down for a discussion.
- If a student asks a question about an earlier concept, click that concept's tile to open it. They can revisit any concept you have open, no matter how far ahead they were.

**The "Class on hold" switch:** freezes everyone in place. Their screens show a "Class is on hold" message. Use it when you want everyone to look up.

**Roster panel:** shows each student's name, roll, current position (which concept or act they're on), and whether they've been active in the last forty seconds (LIVE tag). Refreshes every ten seconds.

**Learning panel:** shows, concept by concept, the average score on the opening question versus the average on the two closing questions. If the gap is small, they didn't learn much from that concept and you might want to teach it live. If it's negative, the closing questions confused them and you should look at the questions.

## Live projector view (optional, recommended)

Put the class-level pulse on the classroom projector so students see where the group is.

Inside a classroom row in the faculty console, click **Projector URL**. You get a URL like `https://…/exec?view=projector&code=7KQ2MB&t=xh3k…`. Open it in a fresh browser tab on the projector machine. No login. The URL is bound to that one classroom by a token, so it is safe to leave visible without exposing the console.

What the projector shows:

- The concept or act you most recently opened (whichever tile you clicked last on the console). Big title, easy to read from the back of the room.
- A grid of student tiles, colour-coded: **green** if they finished this concept's closing quiz or moved past it, **amber** if they are on it right now, **grey** if they have not reached it. A dot pulses on tiles for students who have pinged the server in the last 45 seconds.
- Live counts across the top: how many finished, how many on it now, how many not yet, and total joined.
- Right-hand panel with the **learning gap** for the current concept: average opening score, average closing score, and the point gap between them, live. Positive gap in green ("concept landed"), negative gap in red ("this concept needs a live review").

A toggle at the bottom lets you display roll numbers (default), names, or anonymous labels (S1, S2, S3…). If your students prefer not to have their names on a projector, keep the default or switch to anonymous.

Refreshes every six seconds. If the network hiccups, it retries silently and you see nothing.



- The Hub. Progress bar, list of the eleven acts, completed acts marked with a green checkmark. Only what you have opened is clickable.
- A "Back to Hub" link in the top-right corner of every act. They can never get stuck in one file.
- Inside each Level 1 concept, an opening question (single-select, four options), the concept, then two closing questions. The Hub's progress and the faculty analytics both reflect these.
- If they close the tab and come back, they land where they left off. Progress saves in the browser.

## Gate enforcement — strict pacing

Once a student joins a classroom, the pacing wall is **strict** and network-required:

- The Hub only shows acts the teacher has opened. Everything else is locked.
- Every Level 1 concept and every Level 2 act checks the classroom gate on load and every six seconds. If the current stage is not open, the whole screen is replaced with a "Waiting for your teacher" wall. There is no way to click past it.
- If the class server is unreachable, the student sees "Reconnecting to your class" and cannot proceed. The player's progress is saved so nothing is lost when they reconnect. This is deliberately fail-closed: no network, no play.
- If the teacher clicks "Class on hold", every student's screen shows "Class is on hold" within six seconds.
- Every act sends a position ping every twenty seconds. The teacher's roster shows exactly which concept or act each student is on.
- Every act sends a completion signal when the student commits their lock-in. The projector view and the console dashboard both mark those students green.

**The one exception:** if the student never entered a class code (blank field on the Hub), they are practising solo. Solo mode is unlocked and has no gating — this is the fallback for a student on a home network or a demo user. To force everyone in your class through the gate, keep your class code visible; students who leave it blank have effectively opted out of pacing.

## Offline / solo-practice fallback

If a student can't reach the class server (no internet, or you forgot to paste `CLASS_URL`), the Hub tells them so and lets them practice without a class code. Every stage is open in that mode, but each Level 1 concept still walls itself with the opening and closing checks, so the learning structure is preserved. Quiz answers are not logged. This mode exists so nobody is locked out by network trouble; it is not a substitute for classroom mode in the syllabus.

## Common problems

**"No classroom with that code"** — the student typed the code wrong. The codes never contain the letters `O`, `I`, `L` or the digits `0`, `1` (to remove ambiguity), so `0` is always `O` and vice-versa is impossible. Ask them to re-check.

**Roster is empty even after students say they joined** — most likely `CLASS_URL` is not pasted in the Hub file, or the Apps Script deployment doesn't have "Access: Anyone". Recheck both, redeploy if needed.

**A concept doesn't open even after you click the tile** — check that "Pacing enforced" is on and "Class on hold" is off. The change reaches the student within ten seconds.

**A student says the game is stuck** — ask them to hit the Hub link in the top-right and click Continue on the current act. If a page dies mid-render, this is the reset button.

**Faculty analytics show no data** — students may still be on the concept content, not through the closing questions yet. Data appears the moment the first student submits a closing question for that concept.

## The eleven acts, in order

1. Level 1 · Aggregate demand & supply
2. Level 1 · Founder's Journey (the sixteen-concept walk-through)
3. Level 2 · Act 1: Welfare — CS, PS, DWL
4. Level 2 · Act 2: Price discrimination
5. Level 2 · Act 3: Versioning
6. Level 2 · Act 4: Income elasticity
7. Level 2 · Act 5: Cross-price elasticity
8. Level 2 · Act 6: Resource allocation
9. Level 2 · Act 7: Perfect competition (short-run and long-run entry)
10. Level 2 · Act 8: Oligopoly (Cournot plus a rival-strategy repeated game)
11. Finale · Competitive Run (four-quarter tournament)

The sixteen Level-1 concepts inside the Founder's Journey are: Scarcity, Opportunity cost, Cost of capital, NPV & capital budgeting, The demand curve, Determinants of demand, Elasticity of our product, Types of cost, Capacity & make-or-buy, Cost & supply curves, Supply meets demand, Elasticity & revenue, Profit-max price (MR=MC), Market structures, Strategic play, The competitive run.

## Class-code required (per-classroom, per-deployment)

If you want a locked-down college deployment where nobody plays without a class code, you have two switches:

- **Per-classroom.** In the faculty console, next to the "Pacing enforced" toggle, is a **"Class code required"** switch. Turn it on for that classroom to refuse any student who tries to solo through it. (Solo mode still exists for other Hub visitors; this only affects students who already joined this class code.)
- **Deployment-wide.** In `EconVenture_Hub.html`, set `const REQUIRE_CLASS_CODE = true;` near the top. Now the Hub's login screen refuses to accept a blank class code from anyone — every student on this hosted deployment must join a class.

Leave both off for demo or take-home use.

## Faculty overrides — send a student back, push forward, kick out

The console's roster panel now shows an **Actions** column for each student, with two buttons:

- **Send to…** — prompts you for a destination. Type a concept name (e.g. "Elasticity"), a concept number 0-15, or "act 5" for a Level 2 act. The student's browser picks up the directive on its next ping (within ~20 seconds) and jumps to the specified stage. Useful when a student is stuck on the wrong concept, or you want to walk one student through revision while others move on.
- **Kick** — removes the student from the class. They are signed out, their classroom identity is wiped, and they are returned to the Hub. Their existing quiz answers stay logged; only the live classroom membership is removed. To rejoin, they type the code again.

If you push a directive by mistake, a **×** button appears next to the pending directive to clear it before the student picks it up.

## Level 2 wall quizzes

Every Level 2 act (Acts 1 through 8), the Level 1 Aggregation act, and the Live tournament now have the same wall structure as the Founder's Journey concepts: one opening question before the act renders, one closing question after the student commits their lock-in. That is 20 questions total across the ten acts. Every answer is logged, keyed by `concept id = 200 + actIdx`, and shows up in the console's per-concept learning gap panel and on the projector view. If a student has already answered a wall question for an act (persisted in their browser), the wall doesn't fire again on re-entry.



Ten HTML acts, the Hub, the Live tournament, the Apps Script backend (`Code.gs` and `admin.html`), and the documentation. All the HTML files are single files with no external dependencies except the Apps Script call. Everything is under 300 KB total.

## Google Sign-In (optional but recommended)

You can let students sign in with their college Google account instead of typing name and roll. Benefits: verified identity (real names, real emails, no typos in the roster), cross-device resume (start on phone, finish on laptop), and no shared or forgotten roll numbers.

**One-time setup (five minutes):**

1. Go to https://console.cloud.google.com. Create a new project called `EconVenture` (or pick an existing one).
2. In the sidebar, go to **APIs & Services → OAuth consent screen**. Choose **External** if your college is on regular Gmail, **Internal** if it is a Google Workspace organisation and you want to restrict sign-in to your domain only. Fill the app name (`EconVenture`) and support email. Save.
3. Sidebar → **Credentials → Create Credentials → OAuth client ID**. Application type: **Web application**. Name: `EconVenture Hub`. Under **Authorized JavaScript origins**, add the exact URL where your Hub is hosted (e.g. `https://yourname.github.io`). Do **not** include the path, just the origin. Save.
4. Copy the **Client ID** (a long string ending in `.apps.googleusercontent.com`).
5. Open `EconVenture_Hub.html`. Find `const GOOGLE_CLIENT_ID = '';` near the top. Paste your client ID between the quotes. Save. Push.

That's it. The Hub login screen now shows a "Sign in with Google" button. Students who sign in are verified by Google; the roster shows a green ✓ next to their name; their Google email is stored in the sheet.

If a student cannot use Google Sign-In (blocked network, old browser, personal choice), the name+roll fallback still works below the Google button. Nobody is locked out.

**Leave `GOOGLE_CLIENT_ID` blank** to hide the Google button entirely (name+roll only). Useful for demos or take-home work.

## Cloud save — cross-device resume

Once a student joins a class with a Google account (or with a class code plus roll), their entire progress is pushed to your Google Sheet every 30 seconds and on every page close. When they return — same device, different device, next class — the Hub pulls the latest save and picks up exactly where they left off.

What is saved: every Level 1 concept answer, every Level 2 act state, every lock-in, current position, everything the game remembers locally.

What is NOT saved: the specific slider positions of a mid-drag interaction (those are transient). If a student closes the tab mid-slider, they land back at whatever slider position was there when they last committed, which is the sensible behaviour.

**Load timing:** the cloud pull happens once, right after the student joins a class with the code, and the local save takes over from there. If two devices are open at once, the last-write-wins rule applies and one of the two positions will be lost. Best practice is to sign out on one device before switching to another.

**Storage cost:** Google Sheets. Each student's payload is up to 200 KB; 30 students is roughly 6 MB per class. Well within the free tier.

## Data privacy

Everything a student enters (name, roll, quiz answers, position) is written only to your own Google Sheet, in your own Google Drive, controlled by your Apps Script deployment. Anthropic, EconVenture, and any third party see none of it. If you want to comply with an institutional data policy, share the sheet, share the Apps Script project, and you have the full audit trail.
