# Stakes Therapy Services — website

A three-page static site. No build step, no dependencies, no framework. Edit the
HTML directly and push.

```
index.html      Home — hero, approach, services, who it's for, about Sheena
faq.html        Common questions + crisis resources
contact.html    Phone, email, address, hours, map, appointment form
assets/
  styles.css    All styling lives here
.nojekyll       Tells GitHub Pages to serve files as-is
```

---

## Put it on GitHub Pages

1. Create a new repository (public).
2. Upload every file in this folder, keeping `assets/` as a folder.
3. Go to **Settings → Pages**.
4. Under *Source*, choose **Deploy from a branch**, branch `main`, folder `/ (root)`.
5. Save. The site is live in about a minute at
   `https://YOURNAME.github.io/REPONAME/`.

### Pointing stakestherapy.com at it

1. In **Settings → Pages → Custom domain**, enter `stakestherapy.com` and save.
   GitHub writes a `CNAME` file into the repo.
2. At your DNS host, create these records:

   | Type  | Name  | Value |
   |-------|-------|-------|
   | A     | @     | 185.199.108.153 |
   | A     | @     | 185.199.109.153 |
   | A     | @     | 185.199.110.153 |
   | A     | @     | 185.199.111.153 |
   | CNAME | www   | `YOURNAME.github.io` |

3. Wait for DNS to propagate, then tick **Enforce HTTPS** on the Pages settings.

The current site is WordPress, so don't delete that hosting until the new DNS is
confirmed working.

---

## Two things to finish before launch

### 1. The contact form

`contact.html` has a form pointing at a placeholder. Static hosting can't send
email on its own, so it needs a form relay:

1. Sign up free at [formspree.io](https://formspree.io) (50 submissions/month
   free; Basin and Getform work identically).
2. Create a form, point it at `help@stakestherapy.com`.
3. In `contact.html`, find `YOUR_FORM_ID` and replace it with the ID they give you:

   ```html
   <form class="form" action="https://formspree.io/f/xabcdefg" method="POST">
   ```

Phone and email links work immediately either way — the form is a convenience,
not the only path in.

**The form is deliberately narrow.** It asks for name, email, phone, service
type, and best times to reach you. Nothing else. The free-text box is labelled
"Best times to reach you" rather than something open-ended, and a notice above
the form tells people not to include medical or clinical details. Please don't
widen it. The form's job is "here's who I am, call me back" — not intake.

Formspree's free tier will not sign a BAA. Keeping clinical detail out of the
form is what makes it appropriate, and the wording is doing that work.

### 2. Nothing else — that's it

There's no photo slot to fill and no map to configure. Both were removed
deliberately (see *Privacy posture* below).

### About the logo

The watercolour lockup is used as-is. It already contains the words "Stakes /
Therapy Services", so there is no separate text wordmark in the header — the
image *is* the wordmark, with `alt="Stakes Therapy Services"` carrying the name
for screen readers and search engines.

Files in `assets/`:

| File | What it's for |
|------|---------------|
| `logo.png` / `logo.webp` | The lockup, 400×186. Header and footer. |
| `logo-mark.png` / `.webp` | Just the eucalyptus sprig, square. Spare. |
| `favicon-32.png` | Browser tab icon — the sprig alone. |
| `apple-touch-icon.png` | iOS home screen, 180×180. |

The header loads it through a `<picture>` element: WebP first (39KB), PNG
fallback (82KB) for anything that can't read WebP. Both are the original at
400px wide, which is roughly 3× the 120px it displays at, so it stays crisp on
retina screens.

**Sizing** is one line in `styles.css` — `.logo { height: 3.5rem }`. The footer
and the mobile breakpoint are set just below it. Width is always `auto`, so
changing the height never distorts it.

**The favicon is the sprig only.** The full lockup is a 2.15:1 horizontal
shape; squeezed into a 32px square it becomes an unreadable smear. Cropping to
the sprig keeps something recognisable at tab size. The iOS icon has an opaque
paper-coloured background because iOS composites transparency onto black.

### The palette now comes from the logo

I resampled the site's colours from the artwork so the two don't fight:

```css
--sage:     #41693F;   /* the logo's green */
--gold:     #FDC13D;   /* the logo's amber */
--gold-ink: #8C620D;
--ink:      #1F2E24;
```

**Why there are two golds.** The logo's amber measures 1.53:1 against the page
background. That's fine for a painted swoosh and unreadable as text — well
below the 4.5:1 that small type needs. So it's split: `--gold` draws lines and
borders (the dash before each section label, the crisis panel's left edge),
and `--gold-ink` — the same hue darkened to 5.1:1 — sets the small uppercase
labels. **Don't set type in `--gold`.** Everything else passes AA comfortably:
body text 13.4:1, secondary text 5.9:1, green links 5.9:1.

---

## Privacy posture

This site makes **zero third-party requests**. Load any page and nothing is
fetched from any domain but your own. That's a deliberate choice for a
healthcare provider's site, where third-party trackers are a live regulatory
question.

What that meant in practice:

- **Fonts are self-hosted.** Newsreader and Karla live in `assets/fonts/` as
  variable `.woff2` files (~154KB total, both Open Font License). No Google
  Fonts CDN, so no visitor IPs sent to Google.
- **The map was removed.** The Google Maps embed on the contact page loaded
  Google scripts and cookies on every visit. The address is still there and
  still links out to Apple Maps for directions — but only when someone
  actually clicks it.
- **No analytics, no tags, no pixels.** If you add analytics later, pick
  something cookieless and self-hostable (Plausible or GoatCounter) rather
  than Google Analytics.

The only outbound URLs left are links a visitor has to click: Facebook, Yelp,
Apple Maps, the 988 Lifeline, and the form endpoint on submit. Those are fine —
they're navigation, not silent tracking.

To verify after any future edit, open DevTools → Network, hard-reload, and
confirm every request points at your own domain.

## Editing notes

**Where things live.** Every page shares the same header and footer markup —
if you change a phone number or add a nav link, change it in all three files.
It's a small site; that's the tradeoff for having no build step.

**Colors** are CSS variables at the top of `assets/styles.css`:

```css
--paper:     #F7F8F4;   /* page background */
--ink:       #22332C;   /* text — deep cypress, not black */
--sage:      #3D6B5A;   /* buttons and links */
--sage-pale: #DCE6DC;   /* hairlines and washes */
--brass:     #B08A46;   /* small accents only */
```

Change one value there and it updates everywhere.

**Adding an FAQ** — copy any `<details class="qa">` block in `faq.html` and edit
the text. They're native HTML, so they work with keyboard and screen readers
without any JavaScript.

**The rings in the hero** aren't decoration — they expand and contract on a real
box-breathing cycle (4 seconds in, 4 hold, 4 out, 4 hold). They're disabled
automatically for anyone with reduced-motion turned on. To remove them, delete
the `<div class="breath">` block in `index.html`.

---

## What changed from the old site

- Four pages down to three; About folded into the home page.
- Light palette throughout, replacing the dark theme.
- Removed the theme's placeholder statistics (`00+ Years Experience`,
  `100% Positive Results`, `50% Psychology`) — those were never real numbers and
  claiming a 100% success rate is a liability for a licensed practice.
- Removed the newsletter signup and site search.
- Fixed the broken "Make an Appointment" button on the home page, which pointed
  at the theme vendor's demo site (`delogiswp.pixydrops.com`).
- Corrected spelling ("Psycological") and grammar ("Listen Your Issues").
- Crisis resources moved out of a collapsed FAQ into a visible panel, plus 988
  in the footer of every page.
- Removed the embedded Google Map and the Google Fonts CDN; fonts are now
  served from the repo.
- Added the watercolour logo lockup, replacing the white PNG that couldn't sit
  on a light background, and resampled the site palette from it.
- Replaced the "About" portrait with a credentials panel — licensure,
  education, EMDR training, approach. It's the first thing people check when
  choosing a therapist, and it fills the column better than a photo would.
- No stock photography, no Elementor, no plugins. Loads in well under a second.

---

## Before you launch

- [x] Address removed — practice is telehealth-only, based in Lafayette, LA,
      serving all of Louisiana virtually. Updated on home, FAQ, contact, and
      all page titles/meta descriptions.
- [ ] Confirm office hours
- [ ] Ask Sheena to review the About copy on `index.html` — it's lightly edited
      from her original text, and it should sound like her
- [ ] Decide whether to list insurance accepted / private pay, which the old site
      never mentioned and is usually the first thing people want to know
- [ ] Hook up the form
- [ ] Confirm the LCSW-BACS credentials panel on `index.html` is accurate —
      I expanded "BACS" to "Board Approved Clinical Supervisor," which is the
      standard reading, but check it against her license
- [ ] Confirm with her malpractice carrier that a non-BAA contact form with
      a no-clinical-detail notice is acceptable to them
