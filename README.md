# LazyLoad Explainer
## Introduction
Web pages often have images or embedded content like ads out of view near the bottom of the page, and users don't always end up scrolling all the way down. This means that if a web browser can avoid loading this content that the user never sees, then it could help speed up more important content near the top of the page, reduce network data usage, and reduce memory usage.

LazyLoad is one such solution, which works by waiting to load images and iframes that are out of view until the user scrolls near them. Some JavaScript libraries exist that let pages lazily load images or other kinds of content in various ways, but by having native support for lazy loading in the browser itself, this will make it easier for websites to take advantage of lazy loading. This also opens up the possibility for browsers to automatically find and lazily load content that's well suited to being lazily loaded, without requiring the web page's developers to do anything, which could help a very large number of users.

Web pages will be able to use a `load` attribute on `<img>` and `<iframe>` elements to control and interact with the browser's lazy loading behavior.
## Goals

- Allow browsers to defer the loading of out-of-view images and iframes until the user scrolls near it.
- Provide a way for web pages to determine if the browser supports LazyLoad natively from JavaScript.
- Provide a way for web pages to communicate to the browser that individual elements on the page are good or bad candidates for being lazily loaded.

## Non-goals

- There's no aim to expose the parameters for how near the user would have to scroll to lazily loaded content before it would start loading in.
- There's no aim to add or trigger any new event that fires at the time that an image or iframe is deferred for lazy loading, although the individual element's load event will wait until it fully loads in.

## When do the images and iframes start loading in?
Deferred content should start loading in once the user scrolls near it. Exactly how close "near" means is left up to the browser itself, but the idea is that the browser should typically start loading deferred content some distance before it comes into view, such that there's a good chance the image or iframe will be finished loading by the time it's scrolled into view.
## Detecting LazyLoad support
In JavaScript, this can be done in the [same way](https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Feature_detection) that many other features can be detected from JS, by checking to see if the `load` property is defined on image or iframe elements, e.g.:

```
if ('load' in HTMLImageElement.prototype) alert("yes"); else alert("no");
```

## Ways the `load` attribute can be used:
### Set `load="lazy"` on an image/iframe
E.g. `<img src="cat.jpg" load="lazy" />`

Signals to the browser that the image/iframe is a good candidate for lazy loading.
### Set `load="eager"` on an image/iframe
E.g. `<img src="cat.jpg" load="eager" />`

Signals to the browser that the image/iframe is not a good candidate for lazy loading, and should be loaded right away instead of being lazily loaded. If an element was already deferred, and the attribute is then later set to `load="eager"`, then the element should start loading.
### Set `load="auto"` or leave the attribute unset on an image/iframe
E.g. `<img src="cat.jpg" load="auto" />` or just `<img src="cat.jpg" />`

The browser will determine on its own whether or not to lazily load the image/iframe. Leaving the load attribute unset is the same as setting `load="auto"`. If the browser decides to lazily load this content, it should be careful to avoid breaking pages or negatively impact user experience.
## Onload events
Lazily loaded out-of-view images and iframes won't block the document's onload event. Since these images and iframes might never end up being loaded unless the user scrolls near them, it doesn't make sense to block the document's onload event on these elements since that could block the event endlessly.

Also, the onload event of a lazily loaded element will wait for the element to fully load, which would typically happen after the user scrolls near the element and the element starts loading. If there are multiple lazily loaded elements near each other, then there's no guarantees about what order they'll start loading in or finish loading in.

For example, suppose there's a simple webpage:

```
<body onload='console.log("Body has loaded!");'>
<img src='visible-cat.jpg' onload='console.log("visible cat");' />
<div style='height:10000px;'></div>
<img load='lazy' src='lazy-cat.jpg' onload='console.log("lazy cat");' />
<img load='eager' src='eager-cat.jpg' onload='console.log("eager cat");' />
</body>
```

First, both the `visible-cat.jpg` and `eager-cat.jpg` image elements will load; the `visible-cat.jpg` element will start loading immediately because it's in view, and would be loaded regardless of the value of the `load` attribute on it. The `eager-cat.jpg` element would be loaded immediately because it's marked `load="eager"`. Both of those elements' onload events would fire as well, causing the strings "visible cat" and "eager cat" to be logged to the console once they load.

The `lazy-cat.jpg` image element has been deferred and so doesn't block the document's load event. Next, the body's onload event would fire, causing "Body has loaded!" to be logged to the console.

Then, suppose the user scrolls down the page all the way to the `lazy-cat.jpg` image element, which starts loading once the user scrolls near it. Once the `lazy-cat.jpg` element finishes loading, that `lazy-cat.jpg` element's onload event fires and causes the string "lazy cat" to be logged to the console.
## Further reading

- The [specifications draft](https://github.com/whatwg/html/pull/3752/files)
- The [Chrome Platform Status entry](https://www.chromestatus.com/feature/5645767347798016)
- The [design doc](https://docs.google.com/document/d/1e8ZbVyUwgIkQMvJma3kKUDg8UUkLRRdANStqKuOIvHg/edit) for LazyLoad in Chrome

