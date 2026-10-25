<div align="center">

# Multi-Step Subscription Form

A responsive, accessible, four-step subscription form built with semantic HTML, Tailwind CSS and vanilla JavaScript.
It's a solution to the [Frontend Mentor multi-step form challenge](https://www.frontendmentor.io/challenges/multistep-form-YVAnSdqQBJ).

![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=flat-square&logo=html5&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-06B6D4?style=flat-square&logo=tailwindcss&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat-square&logo=javascript&logoColor=black)
![No build step](https://img.shields.io/badge/build-none_required-success?style=flat-square)
![Frontend Mentor](https://img.shields.io/badge/Frontend_Mentor-Challenge-3F54A3?style=flat-square&logo=frontendmentor&logoColor=white)

<img src="docs/screenshots/desktop-step-2.png" alt="Desktop view of the plan selection step with yearly billing enabled" width="820">

</div>

---

## Table of contents

- [Overview](#overview)
- [Features](#features)
- [Screenshots](#screenshots)
- [Getting started](#getting-started)
- [Project structure](#project-structure)
- [How it works](#how-it-works)
  - [State and navigation](#state-and-navigation)
  - [Pricing model](#pricing-model)
  - [Validation rules](#validation-rules)
  - [Responsive layout](#responsive-layout)
  - [Accessibility](#accessibility)
- [Design decisions](#design-decisions)
- [Testing](#testing)
- [Known limitations and roadmap](#known-limitations-and-roadmap)
- [Acknowledgments](#acknowledgments)
- [Author](#author)

---

## Overview

Users sign up for a gaming subscription in four steps:

| Step | Purpose |
| --- | --- |
| **1. Your info** | Collects name, email address and phone number, with inline validation. |
| **2. Select plan** | Offers Arcade, Advanced or Pro on monthly or yearly billing. |
| **3. Add-ons** | Lets the user add optional extras: online service, larger storage and a customizable profile. |
| **4. Summary** | Shows the plan, add-ons and calculated total, with a shortcut back to change the plan. |
| **Confirmation** | Shows a thank-you screen after the order is confirmed. |

The whole app is one static `index.html` file with no dependencies to install and no build step.

## Features

- **Step-by-step navigation:** users can move forward and back freely, and every selection is kept.
- **Monthly/yearly billing toggle:** all prices, the "2 months free" badges and the summary total update together.
- **Live order summary:** it's calculated from the current selections, with a **Change** link back to plan selection.
- **Form validation** covers all three cases the challenge requires:
  - a required field is left empty,
  - the email address is badly formatted,
  - a step is submitted without a plan selected.
- **Responsive layout:** a card with a sidebar on desktop, and a stacked layout with a pinned action bar on mobile.
- **Hover and focus states** on every interactive element, with visible outlines for keyboard focus.
- **Accessible by default:** native form controls, a correctly announced on/off switch, error messages that are read out, and focus moved to each new step.
- **Self-hosted Ubuntu font** (400, 500 and 700 weights), so there are no requests to third-party font services.

## Screenshots

<table>
  <tr>
    <th>Desktop</th>
    <th>Mobile</th>
  </tr>
  <tr>
    <td><img src="docs/screenshots/desktop-step-1.png" alt="Desktop: personal info step" width="520"></td>
    <td><img src="docs/screenshots/mobile-step-1.png" alt="Mobile: personal info step" width="220"></td>
  </tr>
  <tr>
    <td><img src="docs/screenshots/desktop-validation.png" alt="Desktop: validation errors on empty fields" width="520"></td>
    <td><img src="docs/screenshots/mobile-validation.png" alt="Mobile: validation errors on empty fields" width="220"></td>
  </tr>
  <tr>
    <td><img src="docs/screenshots/desktop-step-3.png" alt="Desktop: add-ons step with two add-ons selected" width="520"></td>
    <td><img src="docs/screenshots/mobile-step-3.png" alt="Mobile: add-ons step with two add-ons selected" width="220"></td>
  </tr>
  <tr>
    <td><img src="docs/screenshots/desktop-step-4.png" alt="Desktop: order summary with yearly totals" width="520"></td>
    <td><img src="docs/screenshots/mobile-step-4.png" alt="Mobile: order summary with yearly totals" width="220"></td>
  </tr>
  <tr>
    <td><img src="docs/screenshots/desktop-step-5.png" alt="Desktop: thank-you confirmation screen" width="520"></td>
    <td><img src="docs/screenshots/mobile-step-5.png" alt="Mobile: thank-you confirmation screen" width="220"></td>
  </tr>
</table>

## Getting started

### Prerequisites

You need a modern browser and an internet connection, because Tailwind CSS loads from its CDN.

### Run locally

```bash
git clone https://github.com/AlexisG1993/multi-step-form.git
cd multi-step-form
```

Then open `index.html` in your browser. To serve it over HTTP instead, which is closer to a real deployment, run either of these:

```bash
npx serve .
# or
python -m http.server 8000
```

### Deploy

The project is a static site, so it can be deployed as-is to GitHub Pages, Vercel or Netlify. Point the host at the repository root; there is no build command and no output directory to configure.

## Project structure

```text
multi-step-form/
├── index.html              # Markup, Tailwind config and application logic
├── assets/
│   ├── fonts/              # Self-hosted Ubuntu (Regular, Medium, Bold)
│   └── images/             # Sidebar backgrounds, plan icons, checkmark, favicon
├── design/                 # Reference mockups from Frontend Mentor (desktop, mobile, active states)
├── docs/
│   └── screenshots/        # Screenshots used in this README
├── style-guide.md          # Colour palette and typography from the brief
├── preview.jpg             # Challenge preview image
└── AGENTS.md / CLAUDE.md   # AI-assistant guidance shipped with the challenge
```

## How it works

### State and navigation

All application state is a single object:

```js
const state = { step: 1, yearly: false };
```

The DOM is the source of truth for selections. The chosen plan is whichever radio button is checked, and the add-ons are whichever checkboxes are checked. This keeps the state object small and means the browser handles keyboard interaction and form semantics with no extra code.

- `showStep(n)` shows the matching `<section data-step>`, updates the progress indicator with `aria-current="step"`, swaps the primary button between **Next Step** and **Confirm**, and moves focus to the new step's heading.
- The form's `submit` event drives forward navigation. Validation runs first, and only steps 1 and 2 can block progress.
- `renderPrices()` and `renderSummary()` are pure render functions that read from `state` and the form controls, so switching billing period never needs extra bookkeeping.

### Pricing model

Prices are stored once as monthly values. Yearly prices are derived as **10 × the monthly price**, which matches the design's "2 months free" offer.

| Item | Monthly | Yearly |
| --- | ---: | ---: |
| Arcade | $9/mo | $90/yr |
| Advanced | $12/mo | $120/yr |
| Pro | $15/mo | $150/yr |
| Online service | +$1/mo | +$10/yr |
| Larger storage | +$2/mo | +$20/yr |
| Customizable profile | +$2/mo | +$20/yr |

### Validation rules

| Field | Rule | Message |
| --- | --- | --- |
| Name | Required | `This field is required` |
| Email | Required and must match `name@domain.tld` | `Invalid email address` |
| Phone | Required, at least 7 characters of digits, spaces, `()` or `-`, with an optional leading `+` | `Invalid phone number` |
| Plan | One option must be selected | `Please select a plan` |

Validation is designed not to nag:

- **On submit**, every field is checked and focus moves to the first invalid one.
- **On blur**, a field that has a value is re-checked, so a malformed email is caught as soon as the user leaves the field.
- **On input**, an existing error clears as soon as the value becomes valid. New errors never appear while the user is still typing.

The browser's built-in validation popups are turned off with `novalidate`, so the custom inline messages in the design are shown instead.

### Responsive layout

The layout is mobile-first, with a single breakpoint at Tailwind's `md` size (768px).

| | Mobile (< 768px) | Desktop (≥ 768px) |
| --- | --- | --- |
| Progress | Numbered circles over a background illustration | Vertical sidebar with step labels |
| Content | Floating card that overlaps the header | Fixed 450px column inside a 940px card |
| Plans | Stacked rows, icon beside text | Three columns, icon above text |
| Actions | White bar pinned to the bottom of the viewport | Inline at the bottom of the card |

Both layouts come from the **same DOM**; only Tailwind's responsive classes change. There are no duplicated components and no JavaScript layout switching.

### Accessibility

- **Native controls everywhere:** plans are radio buttons, add-ons are checkboxes and navigation uses buttons, each visually restyled. Keyboard support (Tab, arrow keys, Space) comes from the browser.
- **The billing toggle** is a `<button role="switch">` with `aria-checked`, so screen readers announce it as an on/off switch.
- **Errors are linked to their fields** with `aria-describedby`, flagged with `aria-invalid`, and announced through `aria-live="polite"` regions.
- **Focus moves on every step change** to the new heading (`tabindex="-1"`), so screen-reader and keyboard users are told what changed.
- **Progress is exposed** through a labelled `<nav>` containing an ordered list, with `aria-current="step"` on the active step.
- **Decorative images** use empty `alt` attributes, and grouped controls sit inside `<fieldset>` elements with visually hidden legends.

## Design decisions

The layout matches the reference mockups to within about 3px. In a few places the implementation intentionally differs from them:

| Decision | Reasoning |
| --- | --- |
| **No plan is pre-selected** | The mockups show Arcade selected, but the brief requires an error when "a step is submitted, but no selection has been made", which can only happen if nothing is selected to begin with. |
| **Summary shows "(Yearly)" on yearly billing** | One mobile mockup reads "Arcade (Monthly)" next to yearly prices, which looks like a design error. |
| **Total always has a `+` prefix** | The desktop mockup uses "+$12/mo" and the mobile one "$120/yr". The desktop version was used for both. |
| **The confirmation screen keeps step 4 highlighted** | Matches the mockups. The thank-you screen isn't a separate numbered step. |

## Testing

The project was checked against the reference designs by rendering every step in headless Chrome ([Puppeteer](https://pptr.dev/)) at **1440×800** and **375×667**, then measuring spacing, sizing and alignment against the mockups in `design/`.

Manual checklist covered:

- [x] Empty submit on step 1 shows an error on all three fields
- [x] A malformed email shows `Invalid email address`
- [x] Continuing from step 2 with no plan shows `Please select a plan`
- [x] Switching between monthly and yearly updates plan prices, add-on prices and the summary
- [x] **Go Back** and **Change** keep all selections
- [x] The form can be completed with the keyboard only
- [x] Hover and focus states appear on inputs, cards, checkboxes, the toggle and buttons
- [x] Layouts hold from 320px up to wide desktop screens

## Known limitations and roadmap

- **Tailwind is loaded from its CDN**, which compiles styles in the browser at runtime. That's fine for a challenge, but a production build should compile and purge the CSS with the Tailwind CLI.
- **Nothing is submitted.** Confirming shows the thank-you screen but doesn't send data anywhere. A real version would post to an API and handle loading and error states.
- **Progress doesn't persist.** Reloading the page resets the form. Saving to `sessionStorage` would let users resume where they left off.
- **Automated tests:** the Puppeteer walkthrough could be turned into Playwright end-to-end tests with an accessibility check such as axe-core.

## Acknowledgments

- Challenge, design and assets by [Frontend Mentor](https://www.frontendmentor.io).
- Typeface: [Ubuntu](https://fonts.google.com/specimen/Ubuntu) by Dalton Maag.

## Author

- GitHub: [@AlexisG1993](https://github.com/AlexisG1993)
