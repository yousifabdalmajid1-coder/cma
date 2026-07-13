# Sideby delivery guide

## Three visual concepts

### 1. Sideby Social Club — “The Living Room”

- **Emotional feeling:** Intimate reunion, anticipation, warmth inside a vast quiet digital space.
- **Color system:** Club ink `#101813`, racing green `#183C2A`, paper cream `#EEE6D8`, medium burnt orange `#C9673F`, muted gold `#D1A64D`, sage `#8CA67E`.
- **Layout idea:** The two friends occupy a central call constellation. A collapsible communication rail folds from the left; the game plane glides in from the right without displacing the call; rewards/profile float as compact orbital instruments.
- **Animation style:** Slow breathing illumination, restrained depth parallax, one traveling pulse when a friend joins, state-driven call rings, game surfaces moving on long shallow arcs.
- **Signature feature:** A luminous **friendship filament** links the two avatars. It is quiet while waiting, completes on connection, carries call/game/reward pulses, and leaves a small “memory bead” after a result.

### 2. Tideglass — “The Shared Current”

- **Emotional feeling:** Calm, fluid, restorative, then playfully energized.
- **Color system:** Abyss `#04131C`, mineral teal `#0D3C49`, sea-glass `#7DE3D1`, moon foam `#E7F6F2`, coral `#FF8F78`, deep jade `#1F7E76`.
- **Layout idea:** The call stage is a circular “tide pool”; chat travels in a vertical current; games rise as dockable islands beside it. Mobile destinations feel like four shore markers.
- **Animation style:** Tidal easing, slow field drift, surface refraction used only in hero moments, discs/pieces with weight and buoyancy.
- **Signature feature:** A **shared horizon** crosses both call and game regions, visibly rising from waiting to connection to victory.

### 3. Velvet Observatory — “Private Night”

- **Emotional feeling:** Cinematic, tactile, dramatic, like entering a private club built for two.
- **Color system:** Charcoal `#09090E`, near-black `#111713`, velvet plum `#38243F`, aged brass `#C7A66A`, ivory `#F3EBDD`, muted terracotta `#A85C43`.
- **Layout idea:** The call is a central proscenium; chat and games arrive as side-stage layers; the selected game table slides under the participants while the call remains above it.
- **Animation style:** Soft spotlight cues, velvet-like masking reveals, shallow camera pushes, weighted table movement, restrained celebratory particles.
- **Signature feature:** Two participant spotlights merge into a **session constellation**, adding a star for each shared result or achievement.

## Selected direction: THE LIVING ROOM — Sideby Social Club

Sideby Social Club is the strongest direction because its friendship filament still carries every emotional stage, but the visual system is architectural rather than atmospheric: dark moss and warm-ink planes, hard rules, scoreboard typography, terracotta interaction color, muted brass, almost no blur, restrained radii, and one consistent competition surface. The previous pale games surface has been replaced by the low-glare **After Hours** palette. **Sideby** is the product name: short, social, and built around the idea of being side by side even when apart.

The experience is continuous rather than page-based. The room begins quiet; the filament searches and completes when a friend arrives; call light gathers around the participants; video expands into a focused cinema; the game plane docks alongside without removing the call; a result sends one reward pulse toward the profile; and disconnect returns the room to a calm afterglow. Atmospheres change color, light, particles, and avatar glow, but never rearrange the interface.

## Game evolution pass

- **City Run** is now a 60-second camera-following score attack in a city larger than the visible canvas. It includes seeded shared routes, delivery contracts, ×8 combos, rechargeable boost, traffic, heat, police pursuit, crash penalties, a minimap, target progress, personal bests, synchronized starts, stale-packet rejection, and host-controlled online results.
- **Crownball** is a new original real-time momentum sport. It includes first-to-three scoring, a 75-second clock, golden-goal overtime, player energy, timed dash shots, wall rebounds, goal freezes, bot practice, same-keyboard play, touch controls, and host-authoritative online physics.
- Every game now has a persistent mastery path from Rookie through Legend and selectable FT1, FT3, FT5, or FT7 series length.
- **Memory Match** now uses consistent CSS-drawn picture cards rather than platform-dependent emoji.
- **Dots & Boxes** has 36-pixel invisible touch targets around its visually thin edges.
- **Checkers** now marks legal moves and captures, identifies the American-checkers ruleset, and correctly ends a capture sequence when a piece becomes a king.
- Remote reward packets are converted to canonical per-game rewards, and processed reward event IDs persist locally to prevent replay awards after reconnecting.

## Firestore schema and rules

Deploy the included `firestore.rules` before enabling public use. The rules assume these three collections:

- `users/{uid}` — private owner-only profile, preferences, rewards, stats, call history, and game history. This collection may never be listed.
- `usernames/{normalizedUsername}` — public username availability record with immutable `uid` and `username`.
- `publicProfiles/{uid}` — deliberately minimal public leaderboard projection. It contains only `uid`, `username`, `displayName`, avatar initials, accent, atmosphere, XP, level, all eight bounded game records, and `updatedAt`.

Write `users/{uid}` and `publicProfiles/{uid}` together in a Firestore batch whenever public values change. Query the leaderboard from `publicProfiles`, never from `users`. Security rules protect privacy and data shape, but client-awarded XP is still a casual leaderboard: a cheat-resistant competitive ranking requires trusted server adjudication such as a Cloud Function.

Do not implement offline email/password accounts. If Firebase is unavailable, allow guest mode only; never store or compare passwords in local storage.

## Exact GitHub Pages upload steps

1. Sign in to [GitHub](https://github.com) and open the repository that will host Sideby. If no repository exists, select **New repository**, enter a name such as `sideby`, choose **Public**, and select **Create repository**.
2. Open the publishing branch, normally `main`. At the repository root, select **Add file → Upload files**.
3. Upload the complete replacement file as exactly `index.html`. It must be at the repository root, not inside `outputs`, `src`, or another folder. Do not rename it.
4. Select **Commit changes**, keep **Commit directly to the `main` branch**, and confirm the commit.
5. Open **Settings → Pages** in the repository.
6. Under **Build and deployment**, set **Source** to **Deploy from a branch**.
7. Set **Branch** to `main`, set the folder to `/(root)`, and select **Save**.
8. Wait for the Pages deployment to finish. GitHub will display the live address. A project repository normally uses `https://YOUR-USERNAME.github.io/REPOSITORY-NAME/`; a user-site repository named `YOUR-USERNAME.github.io` uses `https://YOUR-USERNAME.github.io/`.
9. Open Firebase Console → the existing `callmafriends` project → **Authentication → Settings → Authorized domains**. Add `YOUR-USERNAME.github.io`. If a custom domain is used, add that exact host too.
10. In **Authentication → Sign-in method**, enable **Email/Password** and **Google**. Set the Google support email and save.
11. Open **Firestore Database → Rules**. Replace the editor contents with the complete contents of `firestore.rules`, then select **Publish**.
12. Confirm that the app writes private data to `users`, reservations to `usernames`, and leaderboard data to `publicProfiles`.
13. Visit the live Pages URL in a normal browser tab. Hard refresh once, open DevTools, and verify there are no console errors or failed Firebase, PeerJS, chess.js, or font requests.
14. Test authentication and calls from two different signed-in browser profiles or two devices. Do not test both peers in ordinary tabs sharing the same browser profile if auth isolation matters.

When deploying an update, replace the root `index.html`, commit it to the same publishing branch, wait for the Pages action to finish, and hard refresh the live site.

## Mobile testing checklist

Run the full checklist at **320 × 700**, **360 × 800**, **390 × 844**, and **430 × 932**. Test at 100% browser zoom in portrait; repeat the core call and game checks in landscape.

- [ ] There is no horizontal scrolling at any point, including auth, chat, game selection, chess promotion, results, and profile.
- [ ] The opening sequence finishes in roughly 1–2 seconds and does not trap input.
- [ ] The Google mark is correct and “Continue with Google” stays on one clean line.
- [ ] Email sign-in, account creation, password visibility, forgot password, guest mode, loading, and error states are usable.
- [ ] Username validation and availability are clear before signup; invalid characters are rejected.
- [ ] The bottom dock shows **Room, Chat, Games, Profile**, clearly marks the active destination, respects safe-area insets, and never covers controls.
- [ ] The active-call indicator and unread-chat badge update without shifting the dock.
- [ ] Opening the keyboard keeps the chat composer visible; sending a long message does not widen the page.
- [ ] All primary call controls are at least 44 × 44 CSS pixels and reachable by thumb.
- [ ] Incoming call, answer, decline, mute, camera, screen-share availability, reconnecting, and end-call states remain readable.
- [ ] Video uses the available width, stays `playsinline`, and does not jump when camera state changes.
- [ ] Ending or declining a call turns off camera/microphone indicators and clears both video elements.
- [ ] The Games destination retains call access and chat access.
- [ ] Chess fits completely inside the viewport; its clocks never resize the board.
- [ ] Chess click-to-move and pointer drag both work; a tap does not accidentally start a drag.
- [ ] The promotion selector offers queen, rook, bishop, and knight and remains within the viewport.
- [ ] Connect Four fits perfectly, previews the selected column, locks input during a drop, and does not clip the disc.
- [ ] Tic Tac Toe symbols and winning line scale cleanly.
- [ ] Every game can reset/rematch without stale overlays or duplicate rewards.
- [ ] Room atmosphere and sound preference persist after reload; sound never starts without opt-in.
- [ ] Reduced-motion mode removes parallax, ambient drift, confetti, large scaling, and smooth scrolling while preserving state clarity.
- [ ] VoiceOver or TalkBack can identify navigation, call controls, chat input, game status, chess squares/pieces, and result actions.
- [ ] Scrolling stays smooth during video, particles, Connect Four drops, chess drag, and victory moments.
- [ ] No console errors appear when permissions are denied, the friend disconnects, the network slows, or an unsupported API is hidden.

## Desktop testing checklist

Test at **1280 × 720**, **1366 × 768**, **1440 × 900**, and **1920 × 1080** in current Chrome, Edge, Firefox, and Safari where available.

- [ ] The call stage is the visual focus; communication and games are immediately distinguishable.
- [ ] Call and game regions scroll independently without scroll chaining or concealed content.
- [ ] Collapsing or resizing a rail never compresses chess, video, or call controls below their minimum usable size.
- [ ] Arrival, waiting, searching, found, connected, incoming, audio, video, sharing, playing, victory, and disconnected states transition continuously.
- [ ] Copying the identity code has visible confirmation and a safe clipboard fallback.
- [ ] Unknown PeerJS connections and calls are rejected; only the selected friend can send state or media.
- [ ] Audio call, video call, answer, decline, mute, camera toggle, end, and reconnect work in both directions.
- [ ] Call duration and connection quality update without causing layout shifts.
- [ ] Screen sharing makes the shared screen primary, retains the camera tile, leaves Stop Sharing visible, and restores the previous layout cleanly.
- [ ] Closing, disconnecting, reloading, or stopping a shared track releases every media track.
- [ ] Chat groups messages correctly, escapes all peer text, timestamps messages, auto-scrolls appropriately, and preserves manual scroll position when reviewing history.
- [ ] Game selection animates quickly while the call and chat remain available.
- [ ] Online packets cannot overwrite the wrong game after rapid switching.
- [ ] Chess enforces check, checkmate, stalemate, castling, en passant, promotion choice, draw rules, and illegal king exposure.
- [ ] Chess clocks support 1, 3, 5, and 10 minutes, remain synchronized between peers, warn at low time, and commit timeout without requiring another move.
- [ ] Checkmate locks the board, freezes clocks, identifies the winner, highlights the final move, awards once, and offers Rematch and Leave Table.
- [ ] Tic Tac Toe and Connect Four work in local and online modes and award both peers consistently.
- [ ] Rewards, level, achievements, streak, profile totals, and public leaderboard update once per eligible result.
- [ ] Keyboard-only users can reach every control; focus is visible; dialogs trap and restore focus; Escape closes dismissible overlays.
- [ ] At 200% zoom, controls remain usable and no essential content is clipped.
- [ ] With reduced motion enabled, no continuous or celebratory motion is required to understand the interface.
- [ ] DevTools shows no duplicate IDs, uncaught promises, mixed content, missing assets, Firestore permission errors, or leaked media tracks.
- [ ] A throttled Slow 3G load still presents a usable guest/local-games path and clear dependency status.

## Known browser limitations

- Camera, microphone, clipboard, and screen capture require a secure context. GitHub Pages provides HTTPS; opening `index.html` directly from disk is not a supported deployment test.
- iOS Safari and most mobile browsers do not offer practical tab or screen sharing. The share control should be hidden when `getDisplayMedia` is unavailable.
- The current screen-share design sends video only. System/tab audio is not shared unless the browser supports it and the capture request explicitly asks for audio.
- PeerJS/WebRTC can fail on symmetric NAT, enterprise networks, VPNs, or restrictive firewalls when no TURN relay is configured.
- Remote unmuted video may be blocked by autoplay policy until the user interacts with the page.
- Google popup sign-in can be blocked by popup or privacy settings. Redirect sign-in is the mobile fallback and requires the exact Pages host in Firebase Authorized Domains.
- Clipboard writes may be denied even on HTTPS. A selectable identity field and manual-copy fallback must remain available.
- Private browsing or disabled storage may prevent guest progress, atmosphere, and sound preferences from persisting.
- Safari does not support every Chromium media constraint or `RTCRtpSender.replaceTrack()` edge case identically; screen-share restoration needs explicit testing.
- `backdrop-filter`, `mask-image`, `scrollbar-gutter`, individual transform properties, and `100dvh` vary on older browsers. These effects must degrade decoratively, not functionally.
- External PeerJS, chess.js, Firebase, and font CDNs may be blocked by an extension, firewall, or outage. Local games should remain available where their dependency has loaded, and the interface should show a clear dependency error.
- GitHub Pages cannot provide custom server response headers for advanced cross-origin isolation. The current feature set should not depend on COOP/COEP-only APIs.
- Client-authored rewards cannot produce a cheat-proof public leaderboard. Trusted competitive ranking requires server-side validation.
- City Run and Crownball use a host-authoritative peer session to prevent ordinary race conditions, but a modified host client can still lie. Promote them as social/casual competition until match results are verified by a trusted backend.
- Same-keyboard matches are device-level local records; they do not identify two separately authenticated competitors.

## Preserved-features checklist

The complete replacement `index.html` must retain these implementation contracts while changing their presentation:

- [ ] The existing Firebase project configuration values and project identity.
- [ ] Firebase initialization and graceful guest fallback.
- [ ] Firebase Authentication persistence and auth-state restoration.
- [ ] Google popup/redirect result handling.
- [ ] Email/password sign-in and account creation through Firebase Authentication only.
- [ ] Guest mode without storing a password.
- [ ] Normalized, unique, permanently reserved usernames for email and Google accounts.
- [ ] Username as the public identity and safe display name.
- [ ] Private profile saving and public leaderboard projection.
- [ ] XP, coins, streaks, levels, records, histories, achievements, and reward updates.
- [ ] PeerJS identity, data connection, connection errors, and friend-to-friend state transport.
- [ ] Audio calls, video calls, incoming-call handling, answer, decline, and end-call cleanup.
- [ ] Microphone and camera controls.
- [ ] Screen sharing through sender track replacement, stop-sharing restoration, and track cleanup.
- [ ] Local and remote video elements with `playsinline` behavior.
- [ ] Safe chat rendering, timestamps, message direction, auto-scroll, unread state, and connection status.
- [ ] Existing chess.js rules engine.
- [ ] The exact existing chess-piece SVG drawing paths from `pieceSvg()`.
- [ ] Chess click selection, legal moves, click-to-move, and custom thresholded pointer drag.
- [ ] Castling, en passant, check, checkmate, stalemate, draw detection, king safety, and selectable promotion.
- [ ] Ivory and onyx chess clocks, online synchronization, timeout, and 1/3/5/10-minute modes.
- [ ] Checkmate result presentation, board lock, rewards, rematch, and exit.
- [ ] Tic Tac Toe using X and O in local and online modes.
- [ ] Connect Four local/online play and the physical dropping-disc animation.
- [ ] Dots & Boxes with claimed edges, completed-box scoring, extra turns, and online synchronization.
- [ ] Memory Match.
- [ ] City Run, an original camera-following courier score attack with contracts, combos, boost, traffic, heat, pursuit, minimap, keyboard/touch controls, seeded routes, and host-controlled peer results.
- [ ] Crownball, an original first-to-three momentum sport with physics, dash energy, bot practice, same-keyboard play, touch controls, golden goal, and host-authoritative online frames.
- [ ] Checkers.
- [ ] Game reset/rematch behavior and peer state synchronization.
- [ ] Independent desktop call/game scrolling.
- [ ] Responsive chess, Connect Four, memory, reaction, and call/video layouts.
- [ ] Four-destination phone navigation with safe-area support.
- [ ] Optional sound with no autoplay.
- [ ] Saved room atmospheres.
- [ ] Reduced-motion behavior.
- [ ] GitHub Pages deployment from one complete `index.html` with no build process.
