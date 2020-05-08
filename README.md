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

## Sources

- Grant, Keith J. _CSS in depth_. Shelter Island, NY: Manning Publications Co, 2018.
