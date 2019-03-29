# TAG Security & Privacy Review Document
See [the questionnaire doc here](https://w3ctag.github.io/security-questionnaire/).

## 2. Questions to Consider
### 2.1. What information might this feature expose to Web sites or other parties, and for what purposes is that exposure necessary?

LazyLoad won't expose any new information to first party sites, since they could already gather all that information by using [intersection observers](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) in JavaScript.

Pages that have cross-origin images could expose some information about the user's scroll position to that third party - for example, if "a.com" has an image from "b.com" far down the page and out-of-view, and that "b.com" image was deferred, then "b.com" would know if the user scrolled down near that image since the "b.com" origin would receive the request for the image. For cross-origin frames, the frame could just install an intersection observer itself to get most of that information already though.

### 2.2. Is this specification exposing the minimum amount of information necessary to power the feature?

LazyLoad isn't exposing any new information to first parties. The small amount of information about scroll position exposed when deferring and lazily loading cross-origin images on a page is necessary to help users a lot more, since many sites use strategies like serving images off of cross-origin CDNs.

### 2.3. How does this specification deal with personal information or personally-identifiable information or information derived thereof?

LazyLoad doesn't directly deal with any PII, although it's possible that content being lazily loaded contains PII.

### 2.4. How does this specification deal with sensitive information?

LazyLoad doesn't deal with any sensitive information directly, although it's possible that content being lazily loaded contains sensitive information.

### 2.5. Does this specification introduce new state for an origin that persists across browsing sessions?

No new persistent state is introduced here.

### 2.6. What information from the underlying platform, e.g. configuration data, is exposed by this specification to an origin?

LazyLoad doesn't expose any new information from the underlying platform.

### 2.7. Does this specification allow an origin access to sensors on a user’s device?

No.

### 2.8. What data does this specification expose to an origin? Please also document what data is identical to data exposed by other features, in the same or different contexts.

See the answers to Question 2.1 and Question 2.2 above.

### 2.9. Does this specification enable new script execution/loading mechanisms?

No.

### 2.10. Does this specification allow an origin to access other devices?

No.

### 2.11. Does this specification allow an origin some measure of control over a user agent’s native UI?

No.

### 2.13. How does this specification distinguish between behavior in first-party and third-party contexts?

See the answers to Question 2.1 and 2.2 above.

### 2.14. How does this specification work in the context of a user agent’s Private \ Browsing or "incognito" mode?

There's no difference in behavior.

### 2.15. Does this specification have a "Security Considerations" and "Privacy Considerations" section?

The [design doc](https://docs.google.com/document/d/1e8ZbVyUwgIkQMvJma3kKUDg8UUkLRRdANStqKuOIvHg/edit?ts=5b841123#heading=h.k389oryrnj5o) for the Chrome implementation has these sections.

### 2.16. Does this specification allow downgrading default security characteristics?

No.

