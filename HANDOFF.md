> This repo hosts the review build. The handoff zip has the same files with descriptive image names (site/img/*.jpg); here, images keep their asset IDs in img/.

# IV iron landing page: engineering handoff

Landing and waitlist page for Optimal Clinic's IV iron infusion program (630 Huronia Rd, Unit 5, Barrie), launching November 2026. Patients join a founding waitlist. Referring doctors and nurse practitioners get a one-page fax referral form.

- **Live preview (review build):** https://optimal-research-team.github.io/iron-infusions-waitlist/
- **Same page, doctor view:** https://optimal-research-team.github.io/iron-infusions-waitlist/?for=doctors
- **Referral form:** https://optimal-research-team.github.io/iron-infusions-waitlist/referral-form.html
- **Repo:** https://github.com/Optimal-Research-Team/iron-infusions-waitlist
- **Status (Oct 14, 2026):** design is final pending clinical sign-off on copy (see "Content still pending sign-off"). The forms are not connected to anything yet.

## What's in this folder

| Path | What it is |
|---|---|
| `site/` | Working prototype. `index.html` (landing page), `referral-form.html`, `support.js` (rendering runtime), `img/` (all images, descriptive names) |
| `referral-form.pdf` | Print-ready Letter PDF of the referral form, for hosting and faxing |
| `screenshots/` | Real-font reference renders: full page (desktop 1440, mobile 390), hero in both tab states, referral form |
| `source/` | Claude Design source files (`Main.dc.html`, `Referral-Form.dc.html`, `canvas.json`), same content as `site/` with asset IDs instead of file names |

## Quick start

```bash
cd site
python3 -m http.server 8000
# open http://localhost:8000  (and /?for=doctors for the doctor view)
```

Serve it over HTTP rather than opening the file directly, so the runtime and images load.

## How the prototype is built (and what to do with it)

- It was exported from Claude Design. `index.html` is a template that `support.js` renders in the browser at load (it bundles React and a small runtime).
  - Markup lives inside `<x-dc>`. `{{ name }}` are template values, and `<sc-if>` / `<sc-for>` are conditionals and loops.
  - The page logic is a single `class Component` at the bottom of `index.html`: state, validation, FAQ data, quick-check logic and scroll behaviour.
  - Styles are inline, plus one `<style>` block inside `<helmet>` for hover states, animations and breakpoints.
- **Treat it as a pixel-accurate reference, and rebuild it in the production stack for beoptimal.ca.**
  - Don't ship `support.js` to production.
  - Copy, layout, spacing, tokens and behaviour below are the spec. The screenshots show the intended render with the real fonts.
- Breakpoints:
  - **1100px:** trims the hero menu, widens the hero text column, shrinks the clinic postcard.
  - **960px:** hides the step connectors.
  - **860px:** the hero stacks (photo first), the postcard is hidden, and a clinic card appears under the band.
  - **760px:** mobile sticky CTA, swipeable cards, compact cards and figures.

## Page structure (top to bottom)

1. **Announcement bar** (green): "IV iron opens in Barrie, November 2026. Join the waitlist →" links to `#waitlist`.
2. **Menu pill:**
   - Desktop links: Symptoms, How it works, Our clinic, Pricing, FAQ, plus a "Save my spot" button.
   - Between 861–1100px it shows only How it works, Our clinic, Pricing.
   - On phones it's the logo and the button only.
3. **Hero**, a two-column split on cream (in the style of beOptimal.ca):
   - H1 "Exhausted by low iron? Restore it in as little as *one visit*.", with a forced line break after "iron?" on desktop only.
   - One-sentence subhead.
   - **Tab switch: "For patients" (default) / "Refer a patient":**
     - *For patients:* email field and "Save my founding spot →", with "No referral needed. Free · Takes 10 seconds" under it.
     - *Refer a patient:* "One page, mostly checkboxes. Fax it with recent labs to (833) 370-0320", an "Open the referral form →" button, and "We call your patient to book, and send you our assessment and results."
     - `?for=doctors` (also `providers`, `np`, `md`) opens the "Refer a patient" tab on load.
   - Right column: the infusion photo in a rounded card, an "Opening November 2026" chip, and the clinic postcard overlapping the lower-left corner (it links to `#clinic`).
4. **Scrolling band** (dark green marquee) with 8 points: no referral needed · we order your bloodwork · NP on site · no drive to Toronto · 1–2 infusions · iron often covered by drug plans · recheck at 6–8 weeks · opening date.
   - Pauses on hover. Static with horizontal scroll under `prefers-reduced-motion`.
5. **Mobile-only clinic card** (≤860px): lobby photo, "Our clinic · Huronia Rd, Barrie", "See inside →" links to `#clinic`.
6. **Symptoms** `#symptoms`:
   - Fig. 1, a figure with 6 symptom callouts.
   - **Quick check**: 8 toggleable symptoms.
     - At 1–2 ticks the result says "worth keeping an eye on" and shows a link button.
     - At 3 or more it says "worth checking your iron" and shows an inline email field.
7. **Who it's for**: 4 cards (heavy periods & perimenopause, postpartum, athletes, gut conditions), each with an engraving. On phones they become horizontal cards.
8. **Why pills fail** `#why-iv`:
   - **Fig. 2 "Two routes to your iron stores":** pills → gut wall → dashed grey arrow, and IV → vein → solid green arrow. Both lead to "Your iron stores" (liver).
     - The arrows animate (pill dashes crawl, IV line glints).
     - On phones the two lanes become two columns that flow down into the stores box.
   - **Fig. 3 "How fast your iron stores fill":** an SVG line chart. The IV line draws in when it scrolls into view (IntersectionObserver).
     - Dashed grey is pills, solid green is IV. Healthy-range band and recheck band.
     - Illustrative only, with no numbers on the axes.
   - "Done waiting on pills?" CTA.
   - **"Three ways to fix low iron"**: comparison cards for iron pills, hospital infusion clinic, and IV iron at Optimal (highlighted). Swipeable on phones.
9. **How it works** `#how`:
   - **Path A "My doctor referred me"**, with a referral-form panel. **Path B "I'm booking myself"**, with a "Book directly" panel.
   - Then shared steps 3–5: pick up your iron at the pharmacy → infusion visit → "Feel it, then see it" recheck. Swipeable on phones.
10. **Our clinic** `#clinic`: a scroll-snap carousel of 5 real clinic photos with prev/next buttons and a "n / 5" counter.
11. **Meet Rubeana**: photo, name card, bio, and a callout on her role in the program.
12. **Pricing** `#pricing`:
    - "Iron Restore package" at a $495 founding rate, with $595 crossed out.
    - A "How it adds up" breakdown: package / iron medication ~$530 (often covered) / second infusion $195.
    - A right-fit promise strip.
13. **FAQ** `#faq`: 13 accordion items, with the data in `FAQ` in the logic class. The same Q&As are in the FAQPage JSON-LD.
14. **Waitlist form** `#waitlist`: three columns (waterfront photo / pitch / form) that stack on smaller screens.
    - Email, plus four optional tap-to-choose questions (below).
    - Microcopy, and "Prefer to talk? Call (437) 370-0291".
15. **Footer**: contact details, fax, and a link to the referral form.
16. **Sticky bars** that appear after 640px of scroll: a slim header on desktop, a bottom bar on phones (≤760px).

## What needs wiring (the prototype fakes these)

1. **Waitlist capture.** There are three entry points: the hero, the quick check and the waitlist form. All three share one email state, and any of them marks the visitor as joined.
   - Validation: `/^\S+@\S+\.\S+$/`. Error text: "Please enter a valid email — that's all we need."
   - Today, submitting just flips local state. Wire it to the clinic's email platform or CRM.
   - The hero submit also scrolls to `#waitlist` so people can answer the optional questions.
   - Suggested payload:

     | Field | Values |
     |---|---|
     | `email` | required |
     | `segment` | Heavy periods / perimenopause · Postpartum · Athlete / active · Gut or absorption condition · Just exhausted — suspect low iron |
     | `ferritin_checked` | Yes — it was low · Yes — told it was "normal" · No / not sure |
     | `oral_iron` | Yes — didn't work or couldn't tolerate · Yes — currently taking · No |
     | `drug_coverage` | Yes · No · Not sure |
     | `source` | `hero` / `quick_check` / `waitlist_form` |
     | `quick_check` | ticked symptoms |
     | UTM params | from the URL |

   - **CASL (Canada's anti-spam law):** confirm the consent wording with whoever owns compliance. The form currently says "We'll only email you about the iron program. Unsubscribe anytime." Every email needs sender identification and a working unsubscribe.
2. **Confirmation number and waitlist count.** The post-signup "#133" is driven by a placeholder count (132). Use a real count or remove it before launch.
3. **Referral form.** Host `referral-form.pdf` and/or `referral-form.html`. It's linked from the hero "Refer a patient" tab, the Path A panel and the footer.
4. **Analytics events** (suggested):
   - `waitlist_submit` with `source`
   - `quick_check_tick`
   - `quick_check_capture_shown`
   - `hero_tab_refer`
   - `referral_form_open`
   - `cta_click` with location
   - `phone_click`
   - `carousel_next` / `carousel_prev`
   - `faq_open` with the question

## SEO and sharing

- **Title:** "IV Iron Infusions in Barrie | One-Visit Iron Restoration | Optimal Clinic".
- **Meta description** and **Open Graph / Twitter tags** are in the `<head>` of `site/index.html`.
  - `og:url` and `og:image` currently point at the GitHub Pages URL. Update them to the production domain.
  - `img/og-image.jpg` is 1200×630.
- **Structured data:** JSON-LD for `MedicalClinic` (address, phone, fax, email) and `FAQPage` (all 13 Q&As).
- **`noindex, nofollow` is set on purpose** for the review build. Remove it at launch, and add a canonical URL.
- **Heading outline:** one H1 in the hero, then an H2 per section. The symptoms H2 is keyword-first: "Low iron doesn't always look like anemia."

## Design tokens

| Token | Value | Use |
|---|---|---|
| Cream | `#FFFCF7` | page background |
| Forest | `#2C4E25` | primary buttons, accents, announcement bar |
| Forest deep | `#1C3118` / `#1E3519` | scrolling band, dark cards |
| IV green | `#467F3C` | IV line in Figs 2–3 |
| Sage | `#87A482` | ticks, icons on dark |
| Pills grey | `#8E877C` | pills line (dashed) in Figs 2–3 |
| Ink | `#252525` | headings |
| Body | `#474747` | body text |
| Muted | `#5E5B55` | captions |
| Beige panel | `#F4EFE6` | panels, figure plates |
| Tab track | `#EFEAE1` | hero switch background |
| Border | `#E3DED4` (cards), `#DDD6CA` (hero inputs) | |

- **Fonts:** Castoro (400, regular and italic) for headings, Public Sans (400–700) for UI and body, both from Google Fonts.
- **Radii:** 48px (pills and buttons), 16px (cards), 24px (hero photo and doctor panel), 10–12px (small images).
- **Shadows:** soft and forest-tinted, e.g. `0 8px 24px rgba(30,53,25,0.08)`.

## Assets (`site/img/`)

| File | Where | Source |
|---|---|---|
| `hero-infusion.jpg` | hero photo card, social preview | AI-generated (not a real patient) |
| `clinic-reception-lounge.jpg` | hero postcard, mobile clinic card, carousel 1 | clinic photo |
| `clinic-waiting-lounge.jpg` | carousel 2 | clinic photo |
| `clinic-quiet-corner.jpg` | carousel 3 (cropped from portrait) | clinic photo |
| `clinic-reception-hallway.jpg` | carousel 4 | clinic photo |
| `clinic-front-desk.jpg` | carousel 5 | clinic photo |
| `fig1-symptoms-figure.jpg` | symptoms Fig. 1 | AI-generated engraving |
| `who-*.jpg` (4) | Who it's for cards | AI-generated engravings |
| `fig2-pills.jpg`, `fig2-gut-wall.jpg`, `fig2-iv-bag.jpg`, `fig2-vein.jpg` | Fig. 2 lanes | AI-generated engravings |
| `fig2-liver.jpg` | Fig. 2 "Your iron stores" | engraving from the ALE results project |
| `path-b-blood-tubes.jpg` | Path B card corner | AI-generated engraving |
| `rubeana-deol.jpg` | Meet Rubeana | portrait (Rubeana to approve) |
| `waitlist-barrie-waterfront.jpg` | waitlist card | AI-generated (not a real patient) |
| `og-image.jpg` | social preview | crop of the hero photo |

- Alt text for every image is in `index.html`. The decorative engravings use `alt=""`.
- Engravings sit on white and use `mix-blend-mode: multiply` so they take on the panel colour.

## Accessibility

- `prefers-reduced-motion` stops the scrolling band, the Fig. 2 arrows and the chart draw-in.
- The hero switch uses `role="tablist"` / `role="tab"` with `aria-selected`, and a sage `:focus-visible` ring.
- Quick-check rows are buttons with `aria-pressed`.
- Tap targets are at least 44px. Body text is mostly 16px or larger; captions and fine print are at least 13.5px.
- The chart has a descriptive `aria-label`.

## Content still pending sign-off (do not launch until cleared)

- **Health Canada approval for Monoferric.** It's approved for iron-deficiency anaemia in adults who can't tolerate oral iron or didn't respond to it. These go beyond that and need the Clinical Director's call on advertising:
  - the athletes card ("long before anemia shows up")
  - the referral form's "Low iron, no anemia" option
  - the ferritin-under-30 criterion
- **"About 1 in 4 women of reproductive age"** (symptoms intro) has no source yet.
- **"Many people feel their energy return within 1–2 weeks"** (step 5, FAQ) has no source yet.
- **Pricing structure** is under review: the current $495 + $195 second infusion, versus a possible $595 / $695 all-inclusive.
- **Rubeana's photo and bio** need her approval.
- **Waitlist count / confirmation number** placeholder (see above).
- **Launch date** "November 2026" is a single value (`launchLabel`) used across the page.
