# Reactive SVG Data URIs
*An approach which enables SVG Data URIs to maintain reactive presentation, just as if they were inline `<svg>` elements*

## Fail
**N.B.** *I had high hopes for this approach. The intention was to deploy it on user-interfaces where the same SVG icon might appear on multiple buttons, many times over (always as a Data URI, referenced by the CSS `background-image` property) and ensure that it remained as easy to dynamically update the presentation of the SVG icon as it would be if it were an inline `<svg>` element.*

*I maintain that this **HTML + JS** approach **could** still be used in a relatively simple presentational environment, but otherwise I unhesitatingly advise deploying inline `<svg>` elements instead and styling them, straightforwardly with CSS.*

_______

## Outline:
In two steps, the basic idea was:

### Step 1
The SVG button icons would exist as Data URIs, referenced via the CSS `background-image` property

### Step 2
Whenever the colours of an SVG icon need to update (e.g. when the user changes the app theme or updates the mode from *light mode* to *dark mode*, Javascript would grab either:

  - `let backgroundImage = myButton.style.getPropertyValue('background-image')`; or
  - `window.getComputedStyle(myButton).getPropertyValue('background-image')`

and perform a string replacement such as: 

    backgroundImage.replace('path{fill:rgb(255,255,255)', 'path{fill:' + themeColor0);
    
Thus the CSS determining the presentation of the SVG Data URI would update (via JavaScript), mimicking exactly how the presentation of the SVG would have updated if it were an inline `<svg>` element, rather than a static Data URI included in the CSS styles.

_____

## Findings:

The two steps above sound simple enough in theory, and in a ***non-complex presentational environment*** would, I'm certain, work perfectly well.

But in the presentational environment into which I tried to introduce this approach which included the following:

 - **Four** distinct presentational schemes:
   - **A:** 15 color themes in light mode
   - **B:** 15 color themes in dark mode (with a presentation distinct from that of light mode)
   - **C:** *themeless* light mode (distinct from both the above)
   - **D:** *themeless* dark mode (distinct from all three above)
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

Within these **10 separate types of transformation**:

1) Sometimes a transformation could be effected by removing the inline `style` attribute of a `<button>` element, at other times, an inline `style` attribute would be added to the `<button>` element and, sometimes, the inline `style` attribute of the `<button>` element would be preserved and updated.

2) Some of these transformations required an additional `radial-gradient` component of the `<button>` element's CSS `background-image` to be preserved, others for it to be updated.

3) After transformation, all states of different sets of `<button>` elements required different `:hover` effects to apply. Some of these `:hover` effects were controlled via CSS alone, but others could only be presented via further javascript-initiated transformations.

4) Wherever an inline `style` attribute were present, a `background-image` value could be grabbed using `myButton.style.getPropertyValue('background-image')`. If the `style` attribute were absent, it would be necessary to use `window.getComputedStyle(myButton).getPropertyValue('background-image')`. The latter, when invoked multiple times, seemed noticeably slower on the older versions of Safari.

_____

## Conclusion:
I've been an enthusiastic fan of CSS for nearly two decades. It's very unusual for me to want to do something like re-creating what CSS already does naturally by using JavaScript to manipulate HTML attributes. I initially set about building this approach because when I introduced *dark mode* and *light mode* to the **JSON Rewriter** app, I saw that I needed to find a way to update the presentation of SVGs which were *already* in the form of *Data URIs* in the CSS.

So I turned to JavaScript string manipulation.

I anticipated this would be *incomplex* and *easily and quickly implemented*.

What I did not anticipate is that the complexity would arise not from  the **32 different states** or even the **four different *types* of state** but from the ***10 different types of transformation*** *between* the four different types of state.

After two and a half weeks tying and untying knots with JavaScript, the complexity of which genuinely surprised me, the far better, simpler, more practical solution (I now realise) is to replace the *Data URIs* in the CSS with inline `<svg>` elements in the HTML and then allow CSS to style everything normally.

________

## Bonus:
When I resolved to turn all the *Data URIs* into inline `<svg>` elements, I realised I'd be able to remove a number of functions from my **JS**, but this would come at the cost of my **HTML** increasing substantially.

I wasn't certain, but I suspected the overall size of my app would increase - I hoped by not too much.

In the event, I was surprised to discover the overall size of my app actually *decreased* by **21kB** from `188kB` to `167kB` - a decrease of **11%**.
