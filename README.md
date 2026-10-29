# Pending Off Tracker (GitHub Pages + Firebase)

Tracks public-holiday pending offs for staff. The owner records and approves; staff log in with a username (plus an optional private code), see their balance and history, and apply for future days off.

## 1. Create the Firebase project (free)
1. Go to https://console.firebase.google.com and create a project.
2. **Build > Firestore Database** > Create database (production mode).
3. **Build > Authentication > Sign-in method**: enable **Anonymous** and **Email/Password**.
4. **Authentication > Users > Add user**: create your owner account (your email and a strong password). Copy its **User UID**.
5. **Project settings > General > Your apps > Web (</>)**: register an app and copy the config values.

## 2. Fill in the files
- `config.js`: paste the Firebase config and your owner UID.
- `firestore.rules`: replace `PASTE_OWNER_UID_HERE` with the same UID, then paste the whole file into **Firestore > Rules** and Publish.

## 3. Publish on GitHub
1. Create a repository and upload `index.html`, `config.js`, `firestore.rules`, `README.md`.
2. **Settings > Pages**: Source = Deploy from branch, `main`, folder `/ (root)`.
3. In Firebase **Authentication > Settings > Authorized domains**, add `YOUR-USERNAME.github.io`.
4. Open `https://YOUR-USERNAME.github.io/REPO-NAME/`.

## 4. First use
1. Click **Owner sign in** and sign in with your owner account.
2. Click **Import the 25 staff from your sheet** to load current balances.
3. Set private codes (optional) from each staff member's panel.
4. Send staff the link. They type their username (their name as on the list) and code if you gave one.

## Notes
- Staff cannot change balances. They can only write their own requests; you approve or decline them.
- Private codes are stored scrambled (SHA-256) but the data is readable by signed-in staff, so use 6+ characters for real protection.
- Staff are identified by typed name, not a verified account. You approve every request.
- Do not put real personal data in a public repository. Staff data lives in Firebase, not in the repo.

## Option B: Host on Firebase Hosting instead of GitHub Pages
Firebase Hosting gives you a free address like `https://YOUR-PROJECT-ID.web.app`. That address is already allowed for sign-in, so you skip the authorized-domains step.

1. Install Node.js from https://nodejs.org, then in a terminal run: `npm install -g firebase-tools`
2. Run `firebase login` and sign in with the Google account that owns the project.
3. Open the project folder in the terminal.
4. Edit `.firebaserc` and replace `YOUR-FIREBASE-PROJECT-ID` with your project ID (Project settings > General).
5. Make sure `config.js` is filled in and `firestore.rules` has your owner UID.
6. Run `firebase deploy`. This publishes the app and the Firestore rules together.
7. Open the Hosting URL the command prints.

To update the app later, change the files and run `firebase deploy` again.

## Free email alerts and login codes (EmailJS, no card needed)
The app uses a free email service called EmailJS to send mail, so Firebase stays on the free plan.

**What it does**
- You get an email at admin@justoptic.com whenever a staff member applies for a pending off, and whenever someone gets a login code.
- Staff choose their name from a dropdown. If they have no code, or forgot it, they enter their email and click **Email me a code**. A random 4-digit code is emailed straight to them, with no approval needed, and becomes their code.
- When you approve or decline a request, staff see a popup in the app, and get an email if you saved their email (**Set email**).
- After logging in, staff see "You have N pending off left."

**Set up EmailJS**
1. Create a free account at https://www.emailjs.com
2. **Email Services > Add New Service**: connect the mailbox the emails should be sent from (Gmail or Outlook, or your own SMTP). Copy the **Service ID**.
3. **Email Templates > Create New Template**. Set **To Email** to `{{to_email}}`, **Subject** to `{{subject}}` and the **Content** to `{{message}}`. Save and copy the **Template ID**.
4. **Account > General**: copy your **Public Key**.
5. Paste the three values into `config.js` under `emailjs`. Check `adminEmail` too.
6. Upload the new `index.html`, `config.js` and `firestore.rules`, and publish the rules again in Firebase (Firestore > Rules).
7. If EmailJS offers a setting to allow only your website address, turn it on.

**Limits to know**
- The free EmailJS plan has a monthly email allowance. Check its pricing page for the current number.
- The alerts are sent from the staff member's browser, so they are not tamper-proof. Someone with technical knowledge could send extra alert emails until the allowance runs out. They cannot change balances or approve anything.
- Codes are made and checked in the page, because the free plan has no server. A technical person who knows a colleague's email could get that colleague's code. The first email used for a person is remembered (as a fingerprint), and a different email is refused. You are emailed every time a code is sent, so you can see it. Use **Set private code** with an empty value to reset anyone.
- Staff who have no code yet can log in with their name only, and anyone could register a code for a colleague who hasn't yet. Ask everyone to set theirs up once.
- Emails you save with **Set email** are stored where only you can read them. The email used to get a code is stored only as a fingerprint.
- To reset someone, select them and use **Set private code** with an empty value. It clears their code and saved email.
- Staff who have no code can still log in with their name only.
