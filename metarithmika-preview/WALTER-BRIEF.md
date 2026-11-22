# Brief for Walter: add motion to metarithmika.ca

Keep everything that exists today: the navy and gold design, every page, all content, the links to the Services, Pricing and other inner pages, and the expanded content that opens when you click. Change nothing in the copy or the structure, except the two layout changes in section 1.

Apply the motion below to the home page **and** every inner page (Services, Pricing, About, Clarity call, Client guarantee, Onboarding, Blog), so the whole site moves the same way.

Reference: the attached file `metarithmika-animated-preview.html` shows the exact look and motion. Open it, scroll slowly, and hover the cards.

## 1. Layout changes

1. **Phone hero (under 640px wide):** the hero photo of the accountant is a full-bleed background behind the hero text, not hidden or cropped away. Put a dark gradient over it, light at the top and almost solid navy at the bottom (`linear-gradient(180deg, rgba(17,26,34,.55) 0%, rgba(17,26,34,.92) 45%, rgba(17,26,34,.98) 100%)`), so she stays visible behind the headline and the text stays readable. Keep her face in frame with `object-position: 70% 40%`. On desktop keep the current split: text on the left, photo on the right.
2. **Facts row under the hero buttons**, on every screen size: `$250` "a month, from" · `15+` "years of experience" · `1 day` "reply time, business days" · `EN · ES · GR` "service in three languages". Numbers in the gold serif, labels small and grey, a thin gold line above the row. Four columns on desktop, two on phones.

## 2. Motion rules (use these exact values)

```css
:root {
  --ease-out: cubic-bezier(0.23, 1, 0.32, 1);
  --ease-in-out: cubic-bezier(0.77, 0, 0.175, 1);
}
```

- Animate only `transform` and `opacity` (plus `filter` in the hero entrance). Never `transition: all`.
- Every effect plays **once**. Nothing re-animates when scrolling back up.
- Hover effects only on devices with a real mouse: wrap them in `@media (hover: hover) and (pointer: fine)`.
- Respect reduced motion: under `@media (prefers-reduced-motion: reduce)` show everything in place with no movement.

## 3. What moves

| Element | Motion |
|---|---|
| Hero, on page load | The small label, the headline, the paragraph, the buttons and the facts row fade up one after another: from `opacity: 0; transform: translateY(14px); filter: blur(4px)` to normal, 700ms `var(--ease-out)`, delays 0, 80, 160, 230, 320ms. The hero photo settles from `scale(1.06)` to `scale(1)` over 1600ms `var(--ease-out)`. |
| Numbers $250 and 15+ | Count up from 0 when they appear, 1100ms, ease-out (cubic). |
| Section headings and intros | Rise into place as they enter the screen: from `opacity: 0; translateY(24px)`, 700ms `var(--ease-out)`. Trigger when 12% inside the screen. |
| Card groups (services, plans, guarantees, steps, tools, blog posts) | Same rise, one card after another: delays 0, 60, 110, 150, 190, 220ms. |
| Onboarding steps | A thin gold line above the steps draws from left to right (`transform: scaleX(0)` to `scaleX(1)`, origin left, 1200ms `var(--ease-in-out)`) when the section appears. |
| Service and plan cards, hover | Lift `translateY(-4px)` with a gold border `rgba(217,184,111,.45)` and a soft shadow, 220ms `var(--ease-out)`. On service cards the icon scales to 1.08 and turns −4°, and the "Learn more" arrow slides 4px right. |
| Tool cards, hover | Lift 3px; the round arrow fills gold and turns −45°. |
| Nav links, hover | A 1px gold underline draws from the left (`scaleX(0)` to `scaleX(1)`, 220ms). |
| All buttons | Press: `scale(0.97)` on `:active`, 160ms `var(--ease-out)`. Hover: gold buttons lighten to `#efd397`; arrows slide 3px right. |
| Expandable content (the "more" panels you open on click) | Open with a fade and a small drop: from `opacity: 0; translateY(-6px)` to normal, 250ms `var(--ease-out)`; close the same way in reverse, 200ms. Keep the content's height change instant or use the platform's accordion; never animate `height` frame by frame on anything other than an accordion. |
| Phone menu | The full-screen menu fades and slides down 8px, 200ms `var(--ease-out)`; Escape and tapping a link close it. |
| Phone booking bar | On phones, a fixed "Book a free clarity call" bar slides up from the bottom (250ms `var(--ease-out)`) once the hero button has scrolled off screen, and hides again when it is back. |
| Form fields | On focus, gold border and a soft gold ring, 160ms. |

## 4. Code you can reuse

Add `data-reveal` to any heading block or card that should rise in, and an inline `style="--d:60ms"` for its delay in a group. Add `data-count="250" data-prefix="$"` to a number that should count up.

```css
@media (prefers-reduced-motion: no-preference) {
  .js [data-reveal] { opacity: 0; transform: translateY(24px);
    transition: opacity 700ms var(--ease-out), transform 700ms var(--ease-out);
    transition-delay: var(--d, 0ms); }
  .js [data-reveal].in { opacity: 1; transform: none; }
}
.btn { transition: transform 160ms var(--ease-out), background-color 200ms ease; }
.btn:active { transform: scale(.97); }
@media (hover: hover) and (pointer: fine) {
  .card { transition: transform 220ms var(--ease-out), border-color 220ms ease, box-shadow 220ms ease; }
  .card:hover { transform: translateY(-4px); border-color: rgba(217,184,111,.45);
    box-shadow: 0 18px 40px -18px rgba(0,0,0,.6); }
}
```

```html
<script>
document.documentElement.classList.add('js');
addEventListener('DOMContentLoaded', () => {
  const reduce = matchMedia('(prefers-reduced-motion: reduce)').matches;
  const count = el => {
    const to = +el.dataset.count, pre = el.dataset.prefix || '';
    if (reduce) { el.textContent = pre + to; return; }
    const t0 = performance.now();
    const step = now => { const p = Math.min(1, (now - t0) / 1100);
      el.textContent = pre + Math.round(to * (1 - Math.pow(1 - p, 3)));
      if (p < 1) requestAnimationFrame(step); };
    requestAnimationFrame(step);
  };
  const io = new IntersectionObserver(es => es.forEach(e => {
    if (!e.isIntersecting) return;
    e.target.classList.add('in');
    e.target.querySelectorAll('[data-count]').forEach(count);
    io.unobserve(e.target);
  }), { rootMargin: '0px 0px -12% 0px' });
  document.querySelectorAll('[data-reveal]').forEach(el => io.observe(el));
});
</script>
```

## 5. Check before publishing

- Load the home page on a phone: the accountant is visible behind the hero text and the text reads clearly.
- Scroll every page top to bottom: each section rises in once, nothing flickers, nothing stays invisible.
- Turn on "reduce motion" in the phone or computer settings: everything is visible and still.
- Every button still goes where it goes today; every expandable panel still opens and closes.
