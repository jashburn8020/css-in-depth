# CSS in Depth

Code listings from [CSS In Depth](https://manning.com/books/css-in-depth) by Keith J. Grant

At first glance, it may appear that some listings are missing—Because working examples requires both HTML and CSS, I have put most listings in an HTML file, using `<style>` tags for the CSS. This means that both an HTML listing and CSS listing are combined in one file in the repository.

For example, in Chapter 1, listing 1.1 is HTML code and listing 1.2 is CSS that is meant to be applied to that HTML. I have included these both in the repository in the file named ch01/listing-1.2.html. Changes are made to this CSS in ch01/listing 1.3; these are included in ch01/listing-1.3.html, along with the corresponding HTML from listing 1.1.

- [CSS in Depth](#css-in-depth)
  - [Terminology](#terminology)
  - [1. Cascade, specificity, and inheritance](#1-cascade-specificity-and-inheritance)
    - [The cascade](#the-cascade)
      - [Understanding stylesheet origin](#understanding-stylesheet-origin)
      - [Understanding specificity](#understanding-specificity)
      - [Understanding source order](#understanding-source-order)
      - [Two rules of thumb](#two-rules-of-thumb)
    - [Inheritance](#inheritance)
    - [Special values](#special-values)
      - [Using the `inherit` keyword](#using-the-inherit-keyword)
      - [Using the `initial` keyword](#using-the-initial-keyword)
    - [Shorthand properties](#shorthand-properties)
      - [Beware shorthands silently overriding other styles](#beware-shorthands-silently-overriding-other-styles)
      - [Understanding the order of shorthand values](#understanding-the-order-of-shorthand-values)
  - [2. Working with relative units](#2-working-with-relative-units)
    - [The power of relative values](#the-power-of-relative-values)
      - [The struggle for pixel-perfect design](#the-struggle-for-pixel-perfect-design)
      - [The end of the pixel-perfect web](#the-end-of-the-pixel-perfect-web)
    - [`Em`s and `rem`s](#ems-and-rems)
      - [Using `em`s to define `font-size`](#using-ems-to-define-font-size)
      - [Using `rem`s for `font-size`](#using-rems-for-font-size)
    - [Stop thinking in pixels](#stop-thinking-in-pixels)
      - [Setting a sane default font size](#setting-a-sane-default-font-size)
      - [Making the panel responsive](#making-the-panel-responsive)
      - [Resizing a single component](#resizing-a-single-component)
    - [Viewport-relative units](#viewport-relative-units)
      - [Using `vw` for font size](#using-vw-for-font-size)
      - [Using `calc()` for font size](#using-calc-for-font-size)
    - [Unitless numbers and line-height](#unitless-numbers-and-line-height)
    - [Custom properties (aka CSS variables)](#custom-properties-aka-css-variables)
      - [Changing custom properties dynamically](#changing-custom-properties-dynamically)
      - [Changing custom properties with JavaScript](#changing-custom-properties-with-javascript)
  - [Sources](#sources)

## Terminology

- **Declaration**
  - made up of a **property** (`color`) and a **value** (`black`)

```css
color: black;
```

- **Declaration block**
  - a group of declarations inside curly braces
  - a declaration block is preceded by a **selector** (`body`)

```css
body {
  color: black;
  font-family: Helvetica;
}
```

- **Ruleset**
  - the selector and declaration block
  - also called a rule
- **at-rules**
  - language constructs beginning with an "at" symbol
  - `@import` rules or `@media` queries

## 1. Cascade, specificity, and inheritance

### The cascade

- CSS is about declaring rules
  - if this class is added to that element, apply these styles
  - if element X is a child of element Y, apply those styles
- Predicting how rules behave requires an understanding of the cascade
- Conflicting declarations
  - the cascade considers three things to resolve the difference:
    1. **Stylesheet origin**: where the styles come from
       - your styles are applied in conjunction with the browser's default styles
    2. **Selector specificity**: which selectors take precedence over which
    3. **Source order**: order in which styles are declared in the stylesheet
- Declaration precedence:
  - conflicting declarations
    - different **origin** or importance?
      - yes: use declaration with higher-priority origin
      - no: is one an inline style (scope)?
        - yes: use inline declaration
        - no: do selectors have different **specificity**?
          - yes: use declaration with higher specificity
          - no: use declaration that comes later in **source order**

#### Understanding stylesheet origin

- There are different types, or origins, of stylesheets
  - author styles: yours
  - user agent styles: browser's default styles
    - lower priority
- User agent styles
  - examples:
    - list has a left padding and a list-style-type of disc to produce the bullets
    - links are blue and underlined
    - heading and the list have top and bottom margins
- Overriding user agent styles
  - [`ch01/listing-1.3.html`](ch01/listing-1.3.html)
- Important declarations
  - a declaration can be marked important by adding `!important`
    - `color: red !important;`
  - treated as a higher-priority origin
  - overall order of preference, in decreasing order:
    1. author important
    2. author
    3. user agent

#### Understanding specificity

- If conflicting declarations can't be resolved based on their origin, the browser next tries to resolve them by looking at their specificity
- 99% of the styles on your website come from the same origin
- The browser evaluates specificity in two parts
  - styles applied inline in the HTML
  - styles applied using a selector
- **Inline styles**
  - "scoped" declarations, which override any declarations applied from your stylesheet or a `<style>` tag
  - to override inline declarations, you'll need to add an `!important` to the declaration
    - shifting it into a higher-priority origin
    - if the inline styles are marked important, then nothing can override them
- **Selector specificity**
  - rules of specificity:
    - if a selector has more _IDs_, it wins (it's more specific)
    - if that results in a tie, the selector with the most _classes_ wins
    - if that results in a tie, the selector with the most _tag names_ wins
  - example with decreasing specificity:

```css
#page-title {
  color: red;
}

.page-header .title {
  color: green;
}

body header.page-header h1 {
  color: orange;
}

html body header h1 {
  color: blue;
}
```

- Notes:
  - pseudo-class selectors (for example, `:hover`) and attribute selectors (for example, `[type="input"]`) each have the same specificity as a class selector
  - the universal selector (`*`) and combinators (`>`, `+`, `~`) have no effect on specificity
- **A notation for specificity**
  - **_ID_, _class_, _tag_** (highest to lowest priority)
  - examples:
    - `html body header h1`: 0, 0, 4
    - `body header.page-header h1`: 0, 1, 3
    - `.page-header .title`: 0, 2, 0
    - `#page-title`: 1, 0, 0
- **Specificity considerations**
  - when you give several declarations an `!important`, then the origins match and the regular specificity rules apply
  - it is generally best to keep specificity low when you can, so when you need to override something, your options are open

#### Understanding source order

- If the origin and the specificity are the same, then the declaration that appears later in the stylesheet, or appears in a stylesheet included later on the page, takes precedence
- **Link styles and source order**
  - selectors for styling links should go in a certain order because source order affects the cascade
  - mnemonic to remember this order: LoVe/HAte - link, visited, hover, active

```css
a:link {
  color: blue;
  text-decoration: none;
}

a:visited {
  color: purple;
}

a:hover {
  text-decoration: underline;
}

a:active {
  color: red;
}
```

- **Cascaded values**
  - a declaration that "wins" the cascade is called a _cascaded value_

#### Two rules of thumb

- 2 common rules of thumb for working with the cascade:
  1. **Don't use IDs in your selector**
     - when you need to override the selector, you often don't have another meaningful ID you can use
       - you wind up having to copy the original selector and add another class to distinguish it from the one you are trying to override
  2. **Don't use `!important`**
- An important note about importance
  - if you're creating a JavaScript module for distribution (such as an NPM package), don't apply styles inline via JavaScript if it can be avoided
    - if you do, you're forcing developers using your package to either accept your styles exactly or use `!important` for every property they want to change
  - instead, include a stylesheet in your package
    - if your component needs to make style changes dynamically, it's almost always preferable to use JavaScript to add and remove classes to the elements

### Inheritance

- If an element has no cascaded value for a given property, it may inherit one from an ancestor element
- Not all properties are inherited
  - properties pertaining to text
    - `color`, `font` , `font-family`, `font-size`, `font-weight`, `font-variant`, `font-style`, `line-height`, `letter-spacing`, `text-align`, `text-indent`, `text-transform`, `white-space`, and `word-spacing`
  - list properties:
    - `list-style`, `list-style-type`, `list-style-position`, and `list-style-image`
  - table border properties:
    - `border-collapse` and `border-spacing`
- The inheritance will pass from element to element until it’s overridden by a cascaded value

### Special values

- 2 special values that you can apply to any property to help manipulate the cascade: `inherit` and `initial`

#### Using the `inherit` keyword

- Inheritance to take place when a cascaded value is preventing it
- See `color` of `.footer a` in [`ch01/listing-1.15.html`](ch01/listing-1.15.html)
  - `color: inherit;`
    - overrides the blue link color from `a:link`
    - inherits from its parent, `<footer>` (`.footer`)
- You can also use the `inherit` keyword to force inheritance of a property not normally inherited, such as `border` or `padding`

#### Using the `initial` keyword

- Undo styles applied to an element
- Every CSS property has an initial, or default, value
  - assign the value `initial` to that property effectively resets to its default value
- See `color` of `.footer a` in [`ch01/listing-1.16.html`](ch01/listing-1.16.html)
- Note:
  - declaring `display: initial` is equivalent to `display: inline`
  - it won't evaluate to `display: block` regardless of what type of element you apply it to
  - initial resets to the initial value for the property, not the element; `inline` is the default value for the `display` property

### Shorthand properties

- Examples:
  - `font`:
    - `font-style`, `font-weight`, `font-size`, `line-height`, and `font-family`
    - `font: italic bold 18px/1.2 "Helvetica", "Arial", sans-serif;`
  - `background`:
    - `background-color`, `background-image`, `background-size`, `background-repeat`, `background-position`, `background-origin`, `background-chip`, and `background-attachment`
  - `border`:
    - `border-width`, `border-style`, and `border-color`
    - `border-width`:
      - top, right, bottom, and left border width

#### Beware shorthands silently overriding other styles

- Most shorthand properties let you omit certain values and only specify the bits you're concerned with
  - doing this still sets the omitted values; they'll be set implicitly to their _initial_ value
  - silently override styles you specify elsewhere
- See `font` of `.title` in [`ch01/listing-1.17.html`](ch01/listing-1.17.html)
  - avoid using `font` except to set general styles on the `<body>` element

#### Understanding the order of shorthand values

- Shorthand properties try to be lenient when it comes to the order of the values
  - when it's clear to the browser which value specifies the property
- But there are many properties where the values can be more ambiguous
  - e.g., `margin` and `padding`
  - in these cases, the order of the values is significant

## 2. Working with relative units

- Units, such as `em` and `rem`, are not absolute, but relative
- The value of relative units changes, based on external factors
  - e.g., the meaning of `2 em` changes depending on which element (and sometimes even which property) you're using it on

### The power of relative values

#### The struggle for pixel-perfect design

- In the web environment, the user can have their browser window set to any number of sizes, and the CSS has to apply to it
- Furthermore, users can resize the page after it's opened, and the CSS needs to adjust to new constraints
- Styles can't be applied when you create your page; the browser must calculate those when the page is rendered onscreen
- This adds a layer of abstraction to CSS
  - we can't style an element according to an ideal context; we need to specify rules that'll work in any context where that element could be placed

#### The end of the pixel-perfect web

- Relative units are one of the tools CSS provides to work at this level of abstraction
- Instead of setting a font size at 14 px
  - you can set it to scale proportionally to the size of the window
  - or, you can set the size of everything on the page relative to the base font size, and then resize the entire page with a single line of code

### `Em`s and `rem`s

- In CSS, `1 em` means the font size of the current element
  - its exact value varies depending on the element you're applying it to
  - see [`ch02/listing-2.1.html`](ch02/listing-2.1.html)
- Values declared using relative units are evaluated by the browser to an absolute value, called the _computed value_
- Using `em`s can be convenient when setting properties like `padding`, `height`, `width`, or `border-radius`
  - these will scale evenly with the element if it inherits different font sizes, or if the user changes the font settings
  - see [`ch02/listing-2.3.html`](ch02/listing-2.3.html)

#### Using `em`s to define `font-size`

- `font-size` `em`s are derived from the _inherited_ font size
  - see [`ch02/listing-2.5.html`](ch02/listing-2.5.html)
- For most browsers, the default font size is 16 px
- The keyword value `medium` calculates to 16 px
- `Em`s for font size together with `em`s for other properties
  - when you use `em`s for both font size and any other properties on the same element
    - the browser must calculate the font size first, and then it uses that value to calculate the other values
    - both properties can have the same declared value, but they'll have different computed values
    - see [`ch02/listing-2.6.html`](ch02/listing-2.6.html)
- The shrinking font problem
  - shrinking text occurs when you nest lists several levels deep and apply an `em`-based font size to each level
    - the selector targets every `<ul>` on the page; so when these lists inherit their font size from other lists, the `em`s compound
    - see: [`ch02/listing-2.7.html`](ch02/listing-2.7.html)
    - fix, see: [`ch02/listing-2.9.html`](ch02/listing-2.9.html)

#### Using `rem`s for `font-size`

- The HTML DOM root node is the ancestor of all other elements in the document
  - has a special pseudo-class selector (`:root`)
  - specificity of a class
- `rem` is short for _root em_
  - `rem`s are relative to the root element
  - see: `[ch02/listing-2.10.html`](ch02/listing-2.10.html)
- Note: accessibility - use relative units for font size
  - setting browser default font size does not resize fonts defined using pixels or other absolute units
  - because a default font size is vital to some users, particularly those who are vision-impaired, you should always specify font sizes with relative units or percentages
- Use:
  - `rem`s for font sizes
  - pixels for borders
  - `em`s for most other measures, especially paddings, margins, and border radius

### Stop thinking in pixels

#### Setting a sane default font size

- Let's say you want your default font size to be 14 px
  - 14/16 = 0.875em
  - set the default font size at `:root`
  - see: [`ch02/listing-2.14.html`](ch02/listing-2.14.html)

#### Making the panel responsive

- You can use some _media queries_ to change the base font size, depending on the screen size
  - an `@media` rule used to specify styles that will be applied only to certain screen sizes or media types (for example, print or screen)
  - a key component of responsive design

```css
/* Applies to all screens, but is overridden for larger screens */
:root {
  font-size: 0.75em;
}

/* Applies only to screens 800 px and wider, overriding the original value */
@media (min-width: 800px) {
  :root {
    font-size: 0.875em;
  }
}

/* Applies only to screens 1,200 px and larger, overriding both values */
@media (min-width: 1200px) {
  :root {
    font-size: 1em;
  }
}
```

- See: [`ch02/listing-2.15.html`](ch02/listing-2.15.html)
- These two media queries near the top of your stylesheet can eliminate the need for dozens of media queries throughout the rest of your CSS
- You can change font size globally by only touching one line of code

#### Resizing a single component

- Sometimes you might need a larger version of the same part of your interface on certain parts of the page

```css
.panel {
  /* Establishes a predictable font size for the component */
  font-size: 1rem;
  padding: 1em;
  border: 1px solid #999;
  border-radius: 0.5em;
}

.panel > h2 {
  margin-top: 0;
  /* Uses ems to make other fonts relative to the established parent font size */
  font-size: 0.8em;
  font-weight: bold;
  text-transform: uppercase;
}

/* Compound selector targets elements with both panel and large classes */
.panel.large {
  font-size: 1.2rem;
}
```

- See [`ch02/listing-2.16.html`](ch02/listing-2.16.html)

### Viewport-relative units

- Viewport-relative units:
  - `vh`: 1/100th of the viewport height
  - `vw`: 1/100th of the viewport width
  - `vmin`: 1/100th of the smaller dimension, height or width (IE9 supports vm instead of vmin)
  - `vmax`: 1/100th of the larger dimension, height or width (not supported in IE or, at the time of writing, Edge)
- Viewport-relative lengths are great for things like making a large hero image fill the screen
  - your image can be inside a long container, but setting the image height to `100vh`, makes it exactly the height of the viewport

#### Using `vw` for font size

- Consider what would happen if you applied `font-size: 2vw` to an element
  - desktop monitor at 1,200 px, this evaluates to 24 px
  - tablet with a screen width of 768 px, it evaluates to about 15 px
  - 24 px is a bit too large on a big screen, 7.5 px on an iPhone 6
    - use `calc()` function to make the extremes less severe

#### Using `calc()` for font size

- Basic arithmetic with two or more values
- Useful for combining values that are measured in different units
- Supports addition (`+`), subtraction (`-`), multiplication (`*`) and division (`/`)
- Example:

```css
:root {
  font-size: calc(0.5em + 1vw);
}
```

- `0.5em` operates as a sort of minimum font size, and the `1vw` adds a responsive scalar
  - base font size that scales from 11.75 px on an iPhone 6 up to 20 px in a 1,200 px browser window
  - see: [`ch02/listing-2.19.html`](ch02/listing-2.19.html)

### Unitless numbers and line-height

- Some properties allow for unitless values
  - e.g., `line-height`, `z-index`, and `font-weight`
- The `line-height` property is unusual in that it accepts both units and unitless values
  - you should typically use unitless numbers because they're inherited differently
  - when an element has a value defined using a length (`px`, `em`, `rem`, and so forth), its computed value is inherited by child elements
  - when you use a unitless number, that declared value is inherited, meaning its computed value is recalculated for each inheriting child element
- See:
  - unitless line height: [`ch02/listing-2.21.html`](ch02/listing-2.21.html)
  - em line height: [`ch02/listing-2.22.html`](ch02/listing-2.22.html)

### Custom properties (aka CSS variables)

- You can declare a variable and assign it a value; then you can reference this value throughout your stylesheet
  - particularly useful for recurring values like colors
  - must begin with two hyphens (`--`)
  - must be declared inside a declaration block
  - a function called `var()` allows the use of variables
    - accepts a second parameter, which specifies a fallback value
- See: [`ch02/listing-2.26.html`](ch02/listing-2.26.html)
- Note: If a `var()` function evaluates to an invalid value (e.g., `padding: var(--brand-color)`), the property will be set to its initial value

#### Changing custom properties dynamically

- You can define the same variable inside multiple selectors, and the variable will have a different value for various parts of the page
  - due to cascade and inheritance
- See: [`ch02/listing-2.29.html`](ch02/listing-2.29.html)

#### Changing custom properties with JavaScript

- Custom properties can also be accessed and manipulated live in the browser using JavaScript
- See: [`ch02/listing-2.31.html`](ch02/listing-2.31.html)
- With this technique, you can use JavaScript to re-theme your site, live in the browser
  - you could also highlight certain parts of the page or make any number of other on-the-fly changes

## Sources

- Grant, Keith J. _CSS in depth_. Shelter Island, NY: Manning Publications Co, 2018.
