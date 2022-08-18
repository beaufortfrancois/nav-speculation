# Navigational speculation

[Draft specifications](https://wicg.github.io/nav-speculation/)

In order to making the experience of navigating on the web faster, user agents employ prefetching and prerendering techniques. However, they have historically been [underspecified](https://w3c.github.io/resource-hints/#prerender) and [inconsistently implemented](https://caniuse.com/link-rel-prerender).

The space is full of challenges which have not been comprehensively tackled: most notably,

* avoiding user-visible side effects and annoyances from running script on the destination site; and
* fulfilling the privacy objectives of the user and the referring site.

This repository contains a set of explainers and (eventually) specifications which, combined, give a rigorous model for performing such prerendering of content, in an interoperably-implementable way. Each piece is designed to be composable and reusable; for example, [some contribute to prefetching](#prefetching), independent of prerendering, and the [opt-in](./opt-in.md) is designed to be usable by other types of alternate loading modes, such as [fenced frames](https://github.com/shivanigithub/fenced-frame/).

## Triggering preloading with speculation rules

Today, the underspecified `<link rel="prefetch">` and `<link rel="prerender">` triggers can be used to request a prefetch or prerender. However, these are limited in what they can express. The proposed `<script type="speculationrules">` JSON format allows more detailed configuration, and can be integrated well with user agent heuristics. It also avoids the interoperability and compatibility minefield of these existing `<link>` behaviors.

See [the full explainer](./triggers.md) for more.

## Cross-origin and cross-site concerns

Much of the complexity of preloading comes in when dealing with site that is cross-origin or cross-site. Origins are the web's security boundary, and sites are its privacy boundary, so any preloading across these boundaries needs to preserve the relevant properties.

Our current proposals are focused around allowing cross-origin/site prefetching. We also have some early thoughts on cross-origin/site prerendering, but have not yet committed to them. The following explainers are relevant:

* [Fetching modes](./fetch.md), discussing how to perform the fetch of the main content and any subresources while preloading.
* [`Supports-Loading-Mode`](./opt-in.md), a new header which allows target pages to opt in to being loaded in an uncredentialed mode, with the understanding that they will upgrade their content later upon activation.
* [`<meta http-equiv="supports-loading-mode">`](https://github.com/WICG/nav-speculation/blob/main/meta-processing.md), an extension to the header definition that uses a HTML preparsing pass to allow it to appear in-document.
* [Client IP anonymization](./anonymous-client-ip.md), an extension to prefetching (and maybe one day prerendering?) to require using an anonymizing proxy to hide the user's IP address.

## Prerendering details

Prerendering is more complex than prefetching, as it involves running the target page's scripts and loading its subresources. We've produced a couple of relevant explainers:

* [Same-origin prerendering](./same-origin-explainer.md), discussing what is launched in Chromium for same-origin prerendering using `<script type="speculationrules">`. See also the [origin trial documentation](same-origin-chrome-origin-trial.md).
* [UA-initiated prerendering](./ua-initiated-prerendering.md), discussing what is launched in Chromium for user agent-initiated prerendering (e.g. from the URL bar).
* [Prerendering browsing contexts](./browsing-context.md), an earlier draft discussing in more detail our grand vision for how content behaves while prerendering.
* [Prerendering state APIs](./prerendering-state.md), discussing the new `document.prerendering` API and its associated event.

## Portals

The [portals](https://github.com/WICG/portals/blob/master/README.md) proposal was one of our earliest efforts in this space. We eventually realized that it's part of a larger constellation of features, and decided to turn our attention to prefetching and prerendering, and make them rock-solid, before we return to portals. Once we're confident in the foundations of prerendering content without a visible portal onto it, we plan to return to the portals specification and rebase it on prerendering.