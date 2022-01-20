---
title: 'Hello darkness, my old friend'
author: johan
date: 2019-09-29 11:30:17
tags:
    - css
    - meta
---

If you are reading this on a computer[^1] with a dark system theme, you might notice that this blog now also has a dark theme. Although dark themes to be all the craze nowadays, I've been using dark themes for quite some time, and I've been wanting to implement a dark theme option for my blog since forever. But I could never decide on wether it was to be something that would change automatically according to the time of day, or if the sun was up or not, or something the visitor could toggle.

Well, as it turns out, while I have been procasticating, the browser vendors have solved the problem for me! Earlier this year a new CSS media query was introduced: [prefers-color-scheme](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-color-scheme).

This little gem equals `dark` if the system has a dark color scheme, and `light` otherwise. And it is supported by the latest versions of Firefox, Chrome, Safari and even Edge[^2]. It works something like this:

```css
/* Default color scheme */
body {
    background-color: #fff;
    color: #000;
}

/* Color scheme for dark mode */
@media (prefers-color-scheme: dark) {
    body {
        background-color: #000;
        color: #555;
    }
}

```

If the browser does not support prefers-color-scheme, or if it has a preferred color scheme other than "dark" (i.e. light), it will just ignore the overrides in the media query. So this is basically all I needed to do (well, I had to make a [few more changes](https://github.com/nahojd/supreme-invention/commit/c82cda61f2b9d3421a5f99977db02e6440e0cb49:wq)) to make the theme of the site follow the system theme. Sweet!

[^1]: A smartphone is a computer.
[^2]: According to [caniuse.com](https://caniuse.com/#feat=prefers-color-scheme)
