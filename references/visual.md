# Phase 3 — Visual and runtime integrity

Goal: catch what looks wrong and what breaks under the surface. A page can pass functional testing and still be shipping-blocking if it is visually broken or throwing errors.

## Visual

For every page a core flow touches, and every page a persona reached:

- **Layout at real widths.** Resize the browser and check desktop, tablet, and phone widths. Use `resize_window`. The common breakpoints to hit: around 375px (phone), 768px (tablet), 1440px (desktop).
- **Overflow and clipping.** Text cut off, content spilling past containers, horizontal scrollbars where there should be none, modals taller than the viewport with no scroll.
- **Long and empty content.** The longest realistic string in every text slot. The empty state when there is no data yet. Both are where layouts fall apart.
- **Alignment and spacing.** Things that should line up, line up. No elements overlapping, no buttons half off screen.
- **Images and media.** They load. Broken image icons are a finding. Wrong aspect ratios, stretched logos.
- **Interactive states.** Hover, focus, active, disabled. A button that gives no feedback on click. A focus ring missing on a form field (also an accessibility issue worth noting).
- **Loading and transition states.** What shows while data loads. A flash of broken layout before content arrives. A spinner that never resolves.
- **Dark mode**, if the app claims to support it. Check contrast and that nothing is invisible (white text on white).

Take screenshots of anything broken. Describe the break precisely in the report: which element, which width, what is wrong.

## Runtime

On every page you touch:

- **Read the console.** Use `read_console_messages`. Errors and warnings are findings. A page that renders fine but logs a React key error, an uncaught promise, or a failed fetch is hiding a problem.
- **Read the network tab.** Use `read_network_requests`. Look for: failed requests (4xx, 5xx), requests that hang, calls firing repeatedly (a render loop hammering the API), and anything returning sensitive data it should not (flag for Phase 4).
- **Check timing.** A page or action that takes many seconds with no feedback is a finding, even if it eventually works.

Severity guide for this phase: a console error on a core flow or a layout break that blocks use on a common device width is High. Cosmetic misalignment is Low. A 500 in the network tab is at least High, often Critical.
