# Beyond the Code: GoHighLevel implementation guide

## The safest approach

The site is built as plain HTML, CSS and JavaScript with no build step and no module imports, so it fits a standard GoHighLevel (GHL) custom code element. The 3D hero uses Three.js, which is loaded on demand from a CDN by the script itself. You never paste a library into GHL, and if the library fails to load the page falls back to a CSS orb and everything else still works.

Do not try to run React, Vite output with ES module imports, or `import ... from` statements in a GHL code block. They will not run reliably there. If you later want a React Three Fiber version, host it outside GHL (Netlify, Vercel, GitHub Pages) and embed or link it (see "Alternative" below).

## Files

| File | What it is | Where it goes |
|---|---|---|
| `ghl-custom-code-element.html` | Styles, markup and script in one block | One Custom JS/HTML element in GHL |
| `head-code.html` | SEO, social sharing, fonts, JSON-LD | Page Settings, Head Code |
| `index.html` | Everything combined as a standalone page | Local testing, or external hosting |
| `styles.css`, `body.html`, `app.js` | The editable source parts | Edit these, then run `sh build.sh` |

## Setup steps

1. Create a new page (Funnel step or Website page) and add one **section** with one **row** and one column.
2. Set the section and row to **full width** with **zero padding and margin**. Otherwise GHL's wrapper will add gutters around the design.
3. Add a **Custom JS/HTML** element to that column and paste the contents of `ghl-custom-code-element.html`.
4. Open the page's **Settings** and find the custom code area (the exact wording differs slightly between Funnels and Websites). Paste `head-code.html` into **Head Code**.
5. In the same settings, fill in the page **title**, **meta description** and **social share image** in GHL's own SEO fields as well.
6. **Preview or publish and test on the live URL.** The builder canvas often does not run scripts, so judge the page on the preview link, not inside the editor.

Footer tracking code is for analytics only (for example a Google Analytics or Meta pixel snippet). The site does not need anything there.

## Replace the placeholders

At the top of the script in the custom code element:

- `CONFIG.name`, `CONFIG.email`, `CONFIG.github`, `CONFIG.linkedin`
- Until you replace them, those contact buttons show a friendly "this is a placeholder" notice instead of opening a dead link.

Below `CONFIG` is `DATA`, which holds all the copy: journey stages, toolkit cards, projects, problem-solving steps and "currently exploring" items.

- **Projects:** each has `image`, `link`, `repo` and `placeholder`. Add the real image path and link, then set `placeholder: false`. With no link, the button becomes "Ask about this project" and jumps to the contact form.
- **Journey and toolkit text:** this is draft wording based on your profile. Edit it so it matches your real story.
- **Head code:** replace every `REPLACE` value (domain, share image, name, profile links).

Honesty rules baked in: no testimonials, metrics, client names or certifications anywhere, and the workflows are labelled as demonstrations.

## Contact form

The built-in form opens the visitor's email app with a pre-filled message. If you would rather capture leads in GHL itself, delete the `<form id="cform">` block in `body.html`, add a native **GHL Form** element below the contact section, and style it to match. The "Ask about this project" buttons only pre-fill the built-in form, so remove those references if you drop it.

## Staged rollout

The code is organised so you can verify in stages:

1. **Foundation:** layout, navigation, scroll, reveal, journey, toolkit, projects, method, exploring, contact.
2. **3D:** the hero scene. Set `CONFIG.enable3D = false` to test without it.
3. **Animation:** hero intro, scroll progress, parallax, tilt cards (all respect reduced-motion).
4. **Workflow lab:** SVG diagrams, run demonstration, explorer, "show it in a workflow".
5. **Polish:** replace placeholders, add real project images, check accessibility and speed.

## Performance and accessibility notes

- Three.js is fetched only when the browser is idle after the page loads, and is skipped on data-saver mode and on low-power phones. Phones that do load it get fewer nodes and a lower pixel ratio.
- The animation loop pauses when the hero is off-screen or the tab is hidden.
- With "reduce motion" turned on, the scene renders once, reveals appear instantly, and the marquee becomes scrollable.
- Tabs support arrow keys, Home and End. Workflow steps are keyboard-focusable. The diagram scrolls sideways on small screens.
- Use real project images in WebP or JPEG at about 1600 x 1000 and keep each under roughly 250 KB.

## Troubleshooting

- **Page looks unstyled or has gutters:** the section or row still has padding. Set it to zero and full width.
- **Fonts look different:** the font link in `head-code.html` was not added. The script adds it as a backup, but the head is faster.
- **Fixed menu misbehaves:** if a parent GHL element has a CSS transform, `position: fixed` is relative to it. Remove entrance animations on the parent section.
- **Custom code is rejected for size:** host `app.js` somewhere public (GitHub Pages, jsDelivr) and replace the inline script with `<script src="https://.../app.js" defer></script>`.
- **No 3D:** open the browser console. A message from "Beyond the Code" explains why it fell back (blocked CDN, no WebGL, data-saver).

## Alternative: external hosting

If you want to go further (React Three Fiber, real shaders, a build pipeline), build that as its own app, host it on Netlify, Vercel or GitHub Pages, and then either point a custom domain at it or embed it in GHL with an iframe in a Custom JS/HTML element. Forms and tracking would then need to be wired separately.
