# Form Sprint

A practice race for first-come accommodation forms. Players ready up in a lobby, the host starts a synced countdown, everyone gets the form at the same instant, and each submit time goes on a leaderboard.

It is a static site (host it free on GitHub Pages) plus a free Firebase Realtime Database for the live sync. No server to run, and no Claude account needed.

## One-time setup (about 10 minutes)

1. **Create a Firebase project** at https://console.firebase.google.com (free Spark plan is enough). Turn Google Analytics off.
2. **Add a web app**: Project overview > the `</>` icon. Copy the `firebaseConfig` values.
3. **Paste them into `index.html`** in the `FIREBASE_CONFIG` block near the bottom.
4. **Create the database**: Build > Realtime Database > Create database. Pick the region closest to your players, start in locked mode. If the `databaseURL` it shows differs from the one in the config, use the one it shows.
5. **Turn on sign-in methods**: Build > Authentication > Get started > Sign-in method. Enable **Anonymous** (players) and **Email/Password** (host).
6. **Create the host account**: Authentication > Users > Add user. Use your own email and a strong password. This is the only account that can start rounds.
7. **Set the rules**: open `database.rules.json`, replace `HOST_EMAIL_HERE` with that exact host email, then paste the whole file into Realtime Database > Rules and Publish.
8. **Push to GitHub**: put `index.html` (and these other files) in a repo, then Settings > Pages > Deploy from branch > `main` / root. Your site is at `https://<username>.github.io/<repo>/`.
9. **Allow the GitHub domain**: Authentication > Settings > Authorized domains > Add `<username>.github.io`.

## Running a round

1. Everyone opens the site, types a name and taps **I'm ready**.
2. The host taps **Host login** (bottom of the lobby), signs in with the host email and password, then taps **Start countdown**.
3. A 5-second countdown runs on every screen. At zero the form opens and each player's clock starts.
4. Submit logs the time. **Back to lobby** (host) resets for another round.

## How it stays fair

- The host writes one start time to the database. Every browser counts down to that same moment.
- Clocks are corrected with Firebase's server time offset, so a player's wrong device clock does not matter.
- Only the host account can write the start time. Each player can write only their own result, once per round.
- The rules reject a result whose time is more than 3 seconds shorter than the time elapsed on the server, so claiming a faster time than reality fails. Claiming a slower one is allowed and only hurts the player.

## Known limits

- The Firebase `apiKey` in `index.html` is public by design. The rules in `database.rules.json` are what protect the data.
- A player can clear their browser data to get a new anonymous identity and appear twice on the leaderboard.
- Presence entries clean up when a player disconnects; a player who loses signal may show as ready for a few seconds.
- The form options and prices are copied from a real form for practice. Room choices for the Jacksonville options are made up.
