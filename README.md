# Reactive SVG Data URIs
*An approach which enables SVG Data URIs to be reactive, just as if they were inline `<svg>` elements*

**N.B.** I had high hopes for this approach. The intention was to deploy it on user-interfaces where the same SVG icon might appear on multiple buttons, many times over. The idea was:

### Step 1
The SVG icon would be transformed as a Data URI and be referenced via a CSS `background-image`

### Step 2
Whenever the colours of the SVG icon need to change (e.g. when the user changes the app theme or updates the mode from *light mode* to *dark mode*, Javascript would grab either:

  - `let backgroundImage = myButton.style.getPropertyValue('background-image')`; or
  - `window.getComputedStyle(myButton).getPropertyValue('background-image')`

and perform a string replacement such as: 

    backgroundImage.replace('path{fill:rgb(255,255,255)', 'path{fill:' + themeColor0);
    
Thus the CSS determining the presentation of the SVG Data URI would update (via JavaScript), mimicking exactly how the presentation of the SVG would have updated if it were an inline `<svg>` element, rather than a static Data URI included in the CSS styles.

_____

## Conclusions

The two steps above sound simple enough in theory, and in a ***non-complex environment*** would, I'm certain, work perfectly well.

But in the context into which I tried to introduce this approach which included the following:

 - **Four** distinct presentational schemes:
   - 15 color themes in light mode
   - 15 color themes in dark mode (with a presentation distinct from that of light mode)
   - a *themeless* light mode (distinct from both the above)
   - a *themeless* dark mode (distinct from all three above)
 - compatibility with an older version of **iOS Safari**
 - compatibility with an even older version of **macOS Safari**

I ran into the unanticipated complication that these four distinct  presentational schemes (A, B, C, D) required no fewer than 10 separate types of transformation:

 - `A => A`
 - `B => B`
 - `A => B`
 - `B => A`
 - `A => C`
 - `C => A`
 - `B => D`
 - `D => B`
 - `C => D`
 - `D => C`

Sometimes a transformation could be effected by removing the inline `style` attribute of a `<button>` element, at other times, an inline `style` attribute would be added to the `<button>` element and, sometimes, the inline `style` attribute of the `<button>` element would be preserved and updated.

Some of these transformations required an additional `radial-gradient` component of the `<button>` element's CSS `background-image` to be preserved, others for it to be updated.

After transformation, all states of different sets of `<button>` elements required different `:hover` effects to apply. Some of these `:hover` effects were controlled via CSS alone, but others could only be presented via further javascript-initiated transformations.

Wherever an inline `style` attribute were present, a `background-image` value could be grabbed using `myButton.style.getPropertyValue('background-image')`. If the `style` attribute were absent, then it would be necessary to use `window.getComputedStyle(myButton).getPropertyValue('background-image')` which, when invoked multiple times, seemed noticeably slower on the older versions of Safari.




