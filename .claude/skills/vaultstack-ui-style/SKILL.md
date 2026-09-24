---
name: vaultstack-ui-style
description: The owner's UI/UX standard and working style for VaultStack (lhcmandela/vaultstack, a mobile-first fishing bookkeeping PWA) and their other mobile web apps. Use this whenever the user asks to redesign, restyle, polish, "improve the UI", add a screen, change icons, add animations, fix a modal/sheet, or otherwise touch the look and feel of VaultStack or a similar mobile app — even if they only say "make it look better", "do it like before", "Apple style", or send a reference screenshot. Also use it for any VaultStack change that could affect sales, credit or bookkeeping numbers.
---

# VaultStack UI style

The owner (a Python developer, not a front-end specialist) iterated a long way to reach a design they love. This skill records it so they never have to describe it again. Follow it by default; if they ask for something different, their new request wins — then remember the direction they moved in.

## How to work with this user

These habits mattered as much as the visuals:

1. **Inspect before changing.** Read the relevant code first and say briefly what you found (including real bugs you notice). The app is one large `index.html` (React 18 UMD + in-browser Babel, inline styles + a CSS block, `localStorage` storage, PWA service worker `sw.js`).
2. **UI work means UI only.** When the request is visual, do not change bookkeeping logic, calculations, data shape, persistence or navigation. Prove it: diff the logic functions (`normalizeState`, `workerNumbers`, `computeFinancials2`, `vsHashPin`/`vsVerifyPin`, `vsBio*`, sale/credit helpers, `buildStatement`) against `main` and report "N/N unchanged".
3. **Test in a real browser at phone size** (390×844, plus 375×667 for "without scrolling" claims) with Playwright, driving the actual flows: log sale / expense, credit and repayments, worker loan/payment/bonus, lock + PIN, onboarding, dark mode. Report the numbers you checked.
4. **Show, don't describe.** Send screenshots of the working app after every change. For subjective choices (an icon, a layout), render 3–4 options side by side, recommend one, and let them pick (e.g. they chose the cedi coin for the Sales tab from four drawn options).
5. **Branch, then ask.** Develop on the session branch, push, send screenshots, and ask "Should I move it to `main`?" Only fast-forward `main` (their GitHub Pages site) after they say yes.
6. **Explain in plain words**, short sections, tables for before/after. No jargon without a one-line explanation.
7. **Never present placeholder data as real** (e.g. sea conditions offline must say "Offline" with no verdict, never fake numbers).

## Visual language (the approved design)

Apple-inspired (iOS 26 "Liquid Glass" era), calm and professional, with the owner's sea-blue identity. Not a copy of Apple's assets.

- **Colour:** Apple system neutrals + one tint, sea blue `#0A7CC1` (dark mode `#40B4F2`). Green only for money coming in, orange for money owed, red for destructive/danger. Automatic dark mode via `prefers-color-scheme`. Use the tokens in `references/tokens.css` exactly.
- **Type:** `-apple-system, BlinkMacSystemFont, "SF Pro Text", "Inter", …` with `font-optical-sizing:auto` and slight negative tracking. Large page titles **34px / weight 700** — the owner tried 42px/800 and rejected it as "too bold, not pleasing in person". Tabular numbers for money; show `GH₵` small and grey before the figure.
- **Layout:** grouped background `#F2F2F7`, white cards and inset grouped lists with 22px radius, hairline separators, 16px side gutters, generous spacing. Section headers 22px bold ("Latest Activity", "Owed to You").
- **Navigation:** floating Liquid Glass tab bar (translucent, blurred capsule) with 5 tabs — Home, Sales, Expenses, Workers, More — and a separate round sea-blue **+** button that opens "New Sale / New Expense". Glass round back buttons on detail pages.
- **Home:** date eyebrow + "Home" title + initials avatar (menu: Lock Now, Settings); the season as a sea-blue Wallet-style card ("Money in the pot"); directly below it, two large **Log Sale** (green) and **Log Expense** (orange) cards that must be visible without scrolling; then Latest Activity and a landings bar chart. (The "Each unit is worth" and "Crew payout" tiles were removed at the owner's request.)
- **Sheets/modals:** bottom sheets with grabber, round grey ✕ on the left, centred title; Apple Cash–style big amount + plain keypad; date and customer as capsule chips.
- **Lists:** coloured rounded-square icon plates (Settings style), swipe left for Delete / Paid, "Record" capsule buttons (App Store "GET" style), Contacts-style worker page, Weather-app-style Sea Conditions with a go/no-go scale.
- **Lock:** Face ID first when enabled; iOS-style round passcode pad otherwise.

## Icons

One family everywhere: 24pt grid, round caps and joins, stroke weight chosen from rendered size (≈2.3 at 14px → 1.75 at 25px → 1.5 at 40px+), simple and recognisable, no decoration. Tab bar: outline when idle, solid with knock-out detail when selected; idle tabs dark grey, selected sea blue. Current tab set: house, **cedi coin** (Sales), **receipt** (Expenses), two people (Workers), ellipsis circle (More). The owner rejected a fish for Sales. Never use emoji, 3D, cartoon or mixed-style icons.

## Interaction rules

- **Sheets lock the page behind them.** Portal sheets to `<body>`, freeze the page scroller while any sheet is open, allow touch/wheel only inside the sheet body (`overscroll-behavior: contain`). Test with simulated touch on every sheet.
- **Success feedback:** after a log is *actually persisted* (not merely submitted), show a small centred glass HUD with a green circle and a check that draws itself, ~1.2s, plus a short message ("Sale saved · GH₵ 800.00 on credit"). Never on validation failure; on a failed save show "Not saved on this phone. Please try again." No confetti, bounce, sounds or gamification.
- Toasts and HUDs must never cover keypad keys or buttons.
- Respect `prefers-reduced-motion`.

## Avoid (the owner has rejected these)

Generic AI-looking gradients as decoration, heavy navy hero blocks with bright cyan buttons, too many nested cards, oversized decorative elements, complicated or heavy typography (weight 800 titles), emoji, cartoon/3D icons, placeholder data shown as real, and large pop-ups.

## Bookkeeping principles that the UI must respect

- **Credit is part of a sale, never an extra sale.** One sale = one total; `sale.credits[]` lists customers who took part of it on credit, each with their own `payments[]`. Cash received = total − outstanding credit. Repayments never change Total Sales. Credit on a sale can never exceed the sale. Test the scenario: sale 5,000 → Ama 500, Esi 300, Kojo 200 → cash 4,000 → Ama repays 200 → total still 5,000, outstanding 800, cash 4,200 → all repaid → outstanding 0, cash 5,000.
- Worker payouts, expenses and seasons must stay untouched unless the owner explicitly asks.
- Old saved data must convert on load with identical totals.

## Before you finish any UI task

- [ ] Logic functions unchanged vs `main` (or the requested logic change is tested with numbers)
- [ ] Every affected flow driven in a phone-sized browser, light and dark mode, no page errors
- [ ] Screenshots sent; subjective choices offered as rendered options
- [ ] Pushed to the branch; asked before moving to `main`
