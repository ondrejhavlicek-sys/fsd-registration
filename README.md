# FSD registration – desktop prototype

Clickable desktop prototype of the makro distribuce online registration flow, built from the
[FSD Registration Figma file](https://www.figma.com/design/qmooL2xbDMdRtxCZNhP0S6/FSD-Registration).

Everything lives in one self-contained file, `registration-desktop.html` (HTML, CSS, JS and embedded
images). Open it in a browser; there is no build step.

## Flow

Landing page → 1 Úvod → 2 Registrace uživatele → 3 Registrace firmy (IČO lookup) →
4 Ověření totožnosti (Bank iD, optional power of attorney) → 5 Dokončení registrace.

There is also a **service availability check** (landing page → "Ověřit dostupnost doručení"): enter an
address, refine the pin on the map, then see delivery days and times. Delivery restrictions can be added
to change them; addresses in Znojmo are not served.

Use the **Prototype scenarios** box (bottom right) to switch between a statutory body and a
power-of-attorney case, to make the power-of-attorney check fail, or to jump to any screen.
Demo IČO that is found: `03255654`.

Flows for leaving and resuming a registration are described in [docs/unfinished-registration.md](docs/unfinished-registration.md).
