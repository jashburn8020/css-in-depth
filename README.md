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
  - [3. Mastering the box model](#3-mastering-the-box-model)
    - [Difficulties with element width](#difficulties-with-element-width)
      - [Avoiding magic numbers](#avoiding-magic-numbers)
      - [Adjusting the box model](#adjusting-the-box-model)
      - [Using universal `border-box` sizing](#using-universal-border-box-sizing)
      - [Adding a gutter between columns](#adding-a-gutter-between-columns)
    - [Difficulties with element height](#difficulties-with-element-height)
      - [Controlling overflow behavior](#controlling-overflow-behavior)
      - [Applying alternatives to percentage-based heights](#applying-alternatives-to-percentage-based-heights)
        - [CSS table layouts](#css-table-layouts)
        - [Flexbox](#flexbox)
      - [Using `min-height` and `max-height`](#using-min-height-and-max-height)
      - [Vertically centering content](#vertically-centering-content)
    - [Negative margins](#negative-margins)
    - [Collapsed margins](#collapsed-margins)
      - [Collapsing between text](#collapsing-between-text)
      - [Collapsing multiple margins](#collapsing-multiple-margins)
      - [Collapsing outside a container](#collapsing-outside-a-container)
    - [Spacing elements within a container](#spacing-elements-within-a-container)
      - [Considering changing content](#considering-changing-content)
      - [Creating a more general solution: the lobotomized owl selector](#creating-a-more-general-solution-the-lobotomized-owl-selector)
  - [4. Making sense of floats](#4-making-sense-of-floats)
    - [The purpose of floats](#the-purpose-of-floats)
    - [Container collapsing and the clearfix](#container-collapsing-and-the-clearfix)
      - [Understanding container collapsing](#understanding-container-collapsing)
      - [Understanding the clearfix](#understanding-the-clearfix)
    - [Unexpected "float catching"](#unexpected-float-catching)
    - [Media objects and block formatting contexts](#media-objects-and-block-formatting-contexts)
      - [Establishing a block formatting context](#establishing-a-block-formatting-context)
      - [Using a block formatting context for media object layouts](#using-a-block-formatting-context-for-media-object-layouts)
    - [Grid systems](#grid-systems)
      - [Understanding a grid system](#understanding-a-grid-system)
      - [Building a grid system](#building-a-grid-system)
      - [Adding gutters](#adding-gutters)
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

## 3. Mastering the box model

### Difficulties with element width

- Basic page content: [`ch03/listing-3.2.html`](ch03/listing-3.2.html)
- Put two columns in place using a float-based layout
  - see: [`ch03/listing-3.3.html`](ch03/listing-3.3.html)

```css
.main {
  float: left;
  width: 70%;
  ...;
}

.sidebar {
  float: left;
  width: 30%;
  padding: 1.5em;
  ...;
}
```

- Instead of the two columns sitting side by side, they line wrapped
  - columns took more than 100% of available width even though the widths are 70% and 30%
- Default behavior of the box model
  - width and height specified for an element are for its content
  - any padding, border, and margins are then added to that width

#### Avoiding magic numbers

- The naive fix is to reduce the width of one of the columns
  - e.g., 26% width for the sidebar
  - on smaller viewports, the sidebar will still line-wrap
- An alternative is to use the `calc()` function to reduce the width
  - e.g., `calc(30% - 3em)`

#### Adjusting the box model

- Adjust the box model behavior with its **`box-sizing`** property
  - default value is `content-box`
  - use **`border-box`**
    - combined size of the content, padding, and border
  - see: [`ch03/listing-3.4.html`](ch03/listing-3.4.html)

#### Using universal `border-box` sizing

```css
/* Universal border box fix - apply to the root element */
:root {
  box-sizing: border-box;
}

/* Box sizing isn't an inherited property; use the inherit keyword to force it to be */
*,
::before,
::after {
  box-sizing: inherit;
}
```

- See: [`ch03/listing-3.6.html`](ch03/listing-3.6.html)
- This allows you to add third-party components with their own CSS to your page, especially if their CSS wasn't written with this fix in mind
  - you can convert a third-party component into a `content-box` when necessary by targeting its top-level container
  - all elements inside the component will inherit the box sizing

```css
.third-party-component {
  box-sizing: content-box;
}
```

#### Adding a gutter between columns

- Add a margin to one of the columns to create a small gap (gutter) between columns
- Percent-based gutter margin:

```css
.sidebar {
  float: left;
  width: 29%;
  margin-left: 1%;
  ...;
}
```

- Use `calc()` to subtract the gutter from the width:

```css
.sidebar {
  float: left;
  width: calc(30% - 1.5em);
  margin-left: 1.5em;
  ...;
}
```

- See: [`ch03/listing-3.8.html`](ch03/listing-3.8.html)

### Difficulties with element height

- Typically it's best to avoid setting explicit heights on elements
- Normal document flow
  - refers to the default layout behavior of elements on the page
    - inline elements flow along with the text of the page, from left to right, line wrapping when they reach the edge of their container
    - block-level elements fall on individual lines, with a line break above and below
  - is designed to work with a constrained width and an unlimited height
  - height of a container is organically determined by its contents, not by the container itself

#### Controlling overflow behavior

- When you explicitly set an element's height, you run the risk of its contents overflowing the container
- You can control the exact behavior of the overflowing content with the `overflow` property, which supports four values:
  - `visible` (default): All content is visible, even when it overflows the container's edges
  - `hidden`: Content that overflows the container's padding edge is clipped and won't be visible
  - `scroll`: Scrollbars are added to the container so the user can scroll to see the remaining content
  - `auto`: Scrollbars are added to the container only if the contents overflow
- Horizontal overflow
  - it's possible for content to overflow horizontally, e.g., due to a long URL in a narrow container
  - you can control only horizontal overflow using the `overflow-x` property, or vertical overflow with `overflow-y`
  - these properties support the same values as the `overflow` property

#### Applying alternatives to percentage-based heights

- Specifying height using a percentage is problematic
  - percentage refers to the size of an element's containing block
  - the height of that container is typically determined by the height of its children
  - circular definition - the browser will ignore the declaration
- For percentage-based heights to work, the parent must have an explicitly defined height
- Use the viewport-relative `vh` units to make a container fill the screen
- Columns of equal height
  - the best solution is for the columns to size themselves naturally, and then extend the shorter column so that its height is equal to the height of the taller one

##### CSS table layouts

- Instead of using floats, you'll make the container a `display: table` and each column a `display: table-cell`
  - see (misaligned outer edges): [`ch03/listing-3.9.html`](ch03/listing-3.9.html)
- Fix outer edge misalignment by using negative margins, applied to a new container that wraps around the whole table
  - see: [`ch03/listing-3.10.html`](ch03/listing-3.10.html)

##### Flexbox

- By applying `display: flex` to the container, it becomes a _flex container_
  - its child elements will become the same height by default
  - you can set widths and margins on the items
    - even though this would add up to more than a 100%, the flexbox sorts it out
  - see: [`ch03/listing-3.11.html`](ch03/listing-3.11.html)

#### Using `min-height` and `max-height`

- You can use `min-height` and `max-height` to specify a minimum or maximum value, allowing the element to size naturally within those bounds
- `min-height` means the element will be at least as high as you specify, and if the content doesn't fit, the browser will allow the element to grow naturally to prevent overflow
- `max-height` allows an element to size naturally, up to a point; if that size is reached, the element doesn't become any taller, and the contents will overflow
- Similar properties `min-width` and `max-width` constrain an element's width

#### Vertically centering content

- Why doesn't `vertical-align` work?
  - only affects inline and table-cell elements
  - inline elements
    - controls alignment among other elements on the same line
    - e.g., control how an inline image aligns with the neighboring text
  - table-cell elements
    - controls the alignment of the contents within the cell
    - you can accomplish vertical centering with a CSS table layout
- A lot of the trouble comes from setting the height of a container at a constant value, and then attempting to center a dynamically sized piece of content inside it
- The simplest way to vertically center in CSS
  - give a container equal top and bottom padding, and let both the container and its contents determine their height naturally
  - see [`ch03/listing-3.12.html`](ch03/listing-3.12.html)
- **Guide to vertical centering**
  - _Can you use a natural height container?_
    - apply an equal top and bottom padding to the container to center its contents
  - _Do you need a specific height container, or do you need to avoid using padding?_
    - use `display: table-cell` and `vertical-align: middle` on your container
  - _Can you use flexbox?_
    - if you don't need to support IE9, you can center your content with flexbox
    - see chapter 5
  - _Is the inner content only one line of text?_
    - set a tall line height equal to the desired container height
    - this will force the container to grow to contain the line height
    - if the contents aren't inline, you may have to set them to `inline-block`
  - _Do you know the height of both the container and the inner content?_
    - center the contents with absolute positioning
    - see chapter 7
    - use this when all approaches mentioned here fail
  - _What if you don't know the height of the inner element?_
    - use absolute positioning in conjunction with a transform
    - see chapter 15
    - use this only if you've ruled out all other options
  - When in doubt, see <http://howtocenterincss.com>

### Negative margins

- The exact behavior of a negative margin depends on which side of the element you apply it to
  - left or top
    - moves the element leftward or upward, respectively
    - can cause the element to overlap another element preceding it in the document flow
  - right or bottom
    - doesn't shift the element
    - pulls in any succeeding element
    - a negative bottom margin is not unlike giving the element(s) beneath it a negative top margin
- When a block element doesn't have a specified width, it naturally fills the width of its container
  - negative right margin pulls the edge of the element to the right, bringing it outside the container
  - with an equal negative left margin, and both sides of the element will be extended outside the container
  - allows you to resize the table layout in [`ch03/listing-3.10.html`](ch03/listing-3.10.html) to fill the `<body>` width, despite the border spacing

### Collapsed margins

- When top and/or bottom margins are adjoining, they overlap, combining to form a single margin - _collapsing_

#### Collapsing between text

- The main reason for collapsed margins has to do with the spacing of blocks of text
- Paragraphs (`<p>`), by default, have a 1 em top margin and a 1 em bottom margin
  - applied by the user agent stylesheet
  - when you stack two paragraphs, the margins collapse, overlapping to produce only 1 em of space between the two paragraphs
- Similarly so between an `<h2>`, with a bottom margin of 0.83 em, and the following paragraph
  - the size of the collapsed margin is equal to the largest of the joined margins

#### Collapsing multiple margins

- Elements don't have to be adjacent siblings for their margins to collapse
  - if you wrap the paragraph inside an extra `div`, the visual result will be the same
- See `<h2>`, `<div>` and `<p>` in [`ch03/listing-3.13.html`](ch03/listing-3.13.html)
- Note: Margin collapsing only occurs with top and bottom margins; left and right margins don't collapse

#### Collapsing outside a container

- An element's margin collapsing outside its container typically produces an undesirable effect if the container has a background
- See `<header>` and `<h1>` in [`ch03/listing-3.13.html`](ch03/listing-3.13.html)
  - you'd want `<h1>`'s margin to stay inside `<header>`
  - fixed for the main section of the page
    - margin above "Come join us!" doesn't collapse upward outside of its container
    - margins of flexbox items don't collapse
- Padding provides another solution
  - if you add top and bottom padding to the header, the margins inside it won't collapse to the outside
  - see: [`ch03/listing-3.14.html`](ch03/listing-3.14.html)
  - now there's no margin between the header and the main content
- Ways to prevent margins from collapsing:
  - applying `overflow: auto` (or any value other than visible) to the container
    - the least intrusive solution
  - adding a border or padding between two margins
  - margins won't collapse to the outside of a container that is floated, that is an inline block, or that has an absolute or fixed position
  - when using a flexbox, margins won't collapse between elements that are part of the flex layout
    - also the case with grid layout
  - elements with a `table-cell` display don't have a margin, so they won't collapse
    - also applies to `table-row` and most other table display types
    - exceptions are `table`, `table-inline`, and `table-caption`

### Spacing elements within a container

- The interplay between the padding of a container and the margins of its content can be tricky to work with
- See `.button-link` in [`ch03/listing-3.16b.html`](ch03/listing-3.16b.html)
  - fix by using the adjacent sibling combinator (`+`) to target only `button-link`s that immediately follow other `button-link`s as siblings
  - see `.button-link+.button-link` in [`ch03/listing-3.17.html`](ch03/listing-3.17.html)

#### Considering changing content

- Add a third link to the page
  - see [`ch03/listing-3.19.html`](ch03/listing-3.19.html)
  - you can add a top margin to the sponsorship link
  - if the sidebar changes, e.g., the sponsorship link to be moved to the top, or add a new widget to the sidebar, you'll have to revisit these margins

#### Creating a more general solution: the lobotomized owl selector

- The lobotomized owl selector: `* + *`
  - targets any element that immediately follows any other element
    - selects all elements on the page that aren't the first child of their parent
- Use the lobotomized owl to add top margins to elements throughout your page
  - see: [`ch03/listing-3.20.html`](ch03/listing-3.20.html)
  - unwanted side-effect: the sidebar is an adjacent sibling of the main column, it too receives a to margin
  - revert sidebar top margin to 0
    - see [`ch03/listing-3.21.html`](ch03/listing-3.21.html)
- Using the lobotomized owl like this is a tradeoff
  - simplifies many margins throughout your page
  - you have to override it in places where you don't want it to apply

## 4. Making sense of floats

### The purpose of floats

- The original purpose for a float
  - pull an element (often an image) to one side of its container, allowing the document flow to wrap around it
  - a floated element is removed from the normal document flow and pulled to the edge of the container
  - if you float multiple elements in the same direction, they'll stack alongside one another
- Base styles for a page
  - see: [`ch04/listing-4.2.html`](ch04/listing-4.2.html)
- Tip: It's usually easiest to lay out the large regions of a page first, then work your way to the smaller elements within
- **Double container pattern**
  - constraining the width and centering content on a page
  - placing your content inside two nested containers and then set margins on the inner container to position it within the outer one
  - `<body>` serves as the outer container
    - already 100% of the page width, so you won't have to apply any new styles to it
  - inside that, wrap the entire contents of the page in a `<div class="container">`
    - serves as the inner container
    - apply a `max-width` and `auto` margins to center the contents
      - auto left and right margins will grow to fill the available space
  - see: [`ch04/listing-4.3.html`](ch04/listing-4.3.html)

### Container collapsing and the clearfix

- A few behaviors of floats might catch you off guard

#### Understanding container collapsing

- Float the four media boxes to the left
  - see [`ch04/listing-4.4.html`](ch04/listing-4.4.html)
  - white background behind page title ("Running tips"), but it stops there instead of extending down to encompass the media boxes
- Unlike elements in the normal document flow, _floated elements do not add height to their parent elements_
  - original purpose of floats: to allow text to wrap around them
  - when you float an image inside a paragraph, the paragraph does not grow to contain the image
  - if the image is taller than the text of the paragraph
    - the next paragraph will start immediately below the text of the first
    - text in both paragraphs will wrap around the float
- One way you can correct this is with the float's companion property, `clear`
  - if you place an element at the end of the main container and use clear, it causes the container to expand to the bottom of the floats
  - see: [`ch04/listing-4.5.html`](ch04/listing-4.5.html)
  - `clear: both`
    - the clearance of the generated box is set to the amount necessary to place the top border edge below the bottom outer edge of any right-floating and left-floating boxes that resulted from elements earlier in the source document
    - causes this element to move below the bottom of floated elements, rather than beside them
    - because this empty div itself is not floated, the container will extend to encompass it, thereby containing the floats above it as well

#### Understanding the clearfix

- Instead of adding an extra `div` to your markup, you'll use a pseudo-element
  - `::after` - you can effectively insert an element into the DOM at the end of the container, without adding it to the markup
- **Clearfix**
  - applied to the element that contains the floats
  - see: [`ch04/listing-4.6.html`](ch04/listing-4.6.html)

```css
/* Target at the end of the container */
.clearfix::after {
  display: block;
  /* Content value cause the pseudo-element to appear in the document */
  content: " ";
  /* Clear all floats in the container */
  clear: both;
}
```

- Inconsistency
  - margins of floated elements inside won't collapse to the outside of the clearfixed container
  - margins of non-floated elements will collapse as normal
  - the heading "Running tips" is pressed directly against the top of the white `<main>`
- Modified clearfix to contain all margins:

```css
.clearfix::before,
.clearfix::after {
  /* Prevent margins from collapsing through the pseudo elements */
  display: table;
  content: " ";
}

/* Only the ::after pseudo element needs to clear floats */
.clearfix::after {
  clear: both;
}
```

- Clearfix and `display: table`
  - `display: table` in the clearfix contains margins
    - creation of a display-table element (or pseudo-element) implicitly creates a table row within the element and a table cell within that
    - margins don't collapse through table-cell elements
      - they won't collapse through a display-table pseudo element either
  - `display: table-cell` does not have the same effect
    - the `clear` property only works when applied to block-level elements
      - a table is a block-level element, but a table cell is not
  - `display: table` to clear floats, its implied table cell to contain the margins

### Unexpected "float catching"

- Three left-floated boxes
  - box 3 doesn't float all the way to the left if box 1 is taller than box 2, instead it floats up against box 1
  - box 3 "catches" on the bottom corner of box 1

```text
┌─────┐ ┌─────┐
│  1  │ │  2  │
│     │ └─────┘
└─────┘ ┌─────┐
        │  3  │
        └─────┘
```

- Fix: the first element of each row needs to clear the float above it
  - target with the `:nth-child()` pseudo-class selector
  - see: [`ch04/listing-4.8.html`](ch04/listing-4.8.html)

```css
/* Each new row clears the row above */
.media:nth-child(odd) {
  clear: left;
}
```

- If you had three items per row: `.media:nth-child(3n+1)`
- Only works when you know how many elements are on each row
  - if the number of items can vary your best bet is to use a different layout technique such as the flexbox or inline-block elements
- Add margins to our media elements to provide a gutter between them
  - the lobotomized owl will also add a top margin to every element except the first
  - need to reset the top margin
  - see: [`ch04/listing-4.9.html`](ch04/listing-4.9.html)

### Media objects and block formatting contexts

- **Media object**
  - image on one side, and a block of text beside it
- Float the media object image to the left
  - see: [`ch04/listing-4.10.html`](ch04/listing-4.10.html)

#### Establishing a block formatting context

- Media body extends all the way to the left, envelops the floated image
  - we want to position the media body's left edge to the right of the floated image
- **Block formatting context** (BFC)
  - a region of the page in which elements are laid out
  - is part of the surrounding document flow, but it isolates its contents from the outside context
  - isolation does three things for the element that establishes the BFC:
    - it contains the top and bottom margins of all elements within it; they won't collapse with margins of elements outside of the block formatting context
    - it contains all floated elements within it
    - it doesn't overlap with floated elements outside the BFC
  - applying any of the following property values to an element triggers a BFC:
    - `float: left` or `float: right`: anything but `none`
    - `overflow: hidden`, `auto`, or `scroll`: anything but `visible`
    - `display: inline-block`, `table-cell`, `table-caption`, `flex`, `inline-flex`, `grid`, or `inline-grid`: these are called block containers
    - `position: absolute` or `position: fixed`

#### Using a block formatting context for media object layouts

- The best way to do this is often to set an `overflow` value, either `hidden` or `auto`
  - see: [`ch04/listing-4.11.html`](ch04/listing-4.11.html)
- Note: In some circumstances, the contents from one block formatting context may still overlap the contents of another
  - if the contents overflow the container (for example, the content is too wide)
  - if negative margins pull the contents outside the container

### Grid systems

- A series of class names you can add to your markup to structure portions of the page into rows and columns
  - provide no visual styles, like colors or borders
  - only set widths and positions of containers
    - inside each of these containers, you can add new elements to visually style however you want
  - general principle: put a row container around one or more column containers

#### Understanding a grid system

- Defined to hold a certain number of columns in each row; usually 12, but can vary
- Child elements of a row may have a width anywhere from one column up to 12 columns wide
- 12: divisible by two, three, four, and six - provides a lot of flexibility
  - three-column layout (three, 4-column elements)
  - four-column layout (four, 3-column elements)
  - asymmetrical layouts, such as a 9-column main element and a 3-column sidebar
- Markup
  - each row has a row container `div`
  - inside that you'll place a div for each column element with a `column-n` class
    - `n` is the number of columns across the grid

#### Building a grid system

- A row around each set of two media objects
- Wrap each media object within its own 6-column container
- Add clearfix to each row
- Float all columns to the left, and specify widths for each column value
- Media object doesn't need to float left anymore as the grid column does that for you
- Remove margins from the media object, and the nth-child selector that clears each row
- Add bottom padding on the container
  - because you removed all margins from the media object, including the bottom margin, there's no longer a gap below the last row of media objects and the bottom of their container
- See:
  - [`ch04/listing-4.14.html`](ch04/listing-4.14.html)
  - [`ch04/listing-4.16.html`](ch04/listing-4.16.html)

#### Adding gutters

- You can create gutters by adding left and right padding to each grid column
  - remove top margin from all columns, overriding the lobotomized owl
  - see: [`ch04/listing-4.17.html`](ch04/listing-4.17.html)
  - introduces a slight misalignment between a grid column and content outside the grid row
- Fix misalignment by stretching the row to be a little wider using negative margins on the left and right sides
  - see: [`ch04/listing-4.18.html`](ch04/listing-4.18.html)

## Sources

- Grant, Keith J. _CSS in depth_. Shelter Island, NY: Manning Publications Co, 2018.
- "CSS Properties." _CSS Infos_, css-infos.net/.
