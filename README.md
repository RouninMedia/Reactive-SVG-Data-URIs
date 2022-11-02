# Reactive SVG Data URIs
*An approach which enables SVG Data URIs to be reactive, just as if they were inline `<svg>` elements*

**N.B.** I had high hopes for this approach*. The intention was to deploy it on user-interfaces where the same SVG icon might appear on multiple buttons, many times over. The idea was:

### Step 1
The SVG icon would be transformed as a Data URI and be referenced via a CSS `background-image`

### Step 2
Whenever the colours of the SVG icon needed to change, Javascript would grab either:

  - `let backgroundImage = myButton.style.getPropertyValue('background-image')`; or
  - `window.getComputedStyle(myButton).getPropertyValue('background-image')`

and perform a string replacement such as: 

    backgroundImage.replace('path{fill:rgb(255,255,255)', 'path{fill:' + themeColor0);


