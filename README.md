
# query-list

A Sass mixin powered by [mq-scss](https://www.npmjs.com/package/mq-scss) that is a work around for the lack of css container queries.

This mixin does **not** detect the size of the element. That is impossible in css without real container queries. What this does allow you to do though is list a set of media queries that a set of styles should respond to when an element is within various container elements.

---

<details>
<summary><strong>Table of Contents</strong></summary>

* [Why use this?](#why-use-this)
* [Installation](#installation)
* [Usage](#usage)
  * [Syntax](#syntax)
  * [Basic example](#basic-example)
  * [Using with BEM](#using-with-bem)
  * [Query List reuse](#query-list-reuse)
  * [Applying a default query](#applying-a-default-query)
  * [Creating a Query List outside of an element](#creating-a-query-list-outside-of-an-element)
  * [Debugging](#debugging)
* [Change Log](#change-log)
</details>

---

## Why use this?

I was inspired to write this mixin after reading [this article](https://www.filamentgroup.com/lab/element-query-workarounds.html) by Scott Jehl. In the article, Scott explains that without being able to use container queries, we often have to write unmaintainable blocks of repetitive code for the sake of modules living in different parts of a layout.

I've ran into this issue myself on multiple occasions. When I saw Scott's half finished idea of what a good media query mixin could look like, I ran with it and fleshed it out a bit.

[The article](https://www.filamentgroup.com/lab/element-query-workarounds.html) explains the problem that this mixin is trying to solve very well. I won't try to explain it again here. Read over the article and you will see why this mixin can come in handy. :)

## Installation

```````````
npm install query-list --save
```````````

Import mq-scss and query-list at the top of your main Sass file in that order (note that the exact path will differ depending on your folder structure).

``````scss
@import "../node_modules/mq-scss/mq";
@import "../node_modules/query-list/mql";
``````

It will automatically install mq-scss for you when you install query-list.

## Usage

### Syntax

This is the general syntax for the mixin:

``````scss
@include mql((
  '.container-selector-1' : ($range, $breakpoint-1[, $breakpoint-2]),
  '.container-selector-2' : ($range, $breakpoint-1[, $breakpoint-2])
)[, $isInside: true]){
  //styles go here
}
``````

`($range, $breakpoint-1[, $breakpoint-2])` is an [mq-scss](https://www.npmjs.com/package/mq-scss) media query variable. Being powered by mq-scss, the media query possibilities at your finger tips are immense. See the [mq-scss documentation](https://www.npmjs.com/package/mq-scss) for more information on how to write the actual media queries for this mixin.

### Basic example

Here is an example of how you might use this:

``````scss
.element {
  @include mql((
    '.container-1' : (max, 1000px),
    '.container-2' : (min, 1000px)
  )){
    background: red;
  }
}
``````

That would create this css:

``````css
@media (max-width: 1000px) {
  .container-1 .element {
    background: red;
  }
}

@media (min-width: 1001px) {
  .container-2 .element {
    background: red;
  }
}
``````

Setting min-width to +1 the value given is an mq-scss feature. See the [mq-scss documentation](https://www.npmjs.com/package/mq-scss#minmax-width) for more details.

### Using with BEM

If you are using BEM syntax, this also works:

````scss
.element {
  &__childElement {
    @include mql((
        '.container-1' : (max, 1000px),
        '.container-2' : (min, 1000px)
    )){
      background: red;
    }
  }
}
````

That will create this css:

``````css
@media (max-width: 1000px) {
  .container-1 .element__childElement {
    background: red;
  }
}

@media (min-width: 1001px) {
  .container-2 .element__childElement {
    background: red;
  }
}
``````

### Query List reuse

If you need to use the same set of queries multiple times, I would recommend saving the Query List into a variable first:

``````scss
$MQL-element--large: (
  '.container-1' : (max, 1000px),
  '.container-2' : (min, 1000px)
);

.element {
  @include mql($MQL-element--large){
    background: red;
  }

  &__childElement {
    @include mql($MQL-element--large){
      background: blue;
    }
  }
}
``````

To create this css:

``````css

@media (max-width: 1000px) {
  .container-1 .element {
    background: red;
  }
}

@media (min-width: 1001px) {
  .container-2 .element {
    background: red;
  }
}

@media (max-width: 1000px) {
  .container-1 .element__childElement {
    background: blue;
  }
}

@media (min-width: 1001px) {
  .container-2 .element__childElement {
    background: blue;
  }
}

``````

Don't worry about the repetition of the media queries in the output css. Gzipping makes the file size increase from repeated media queries [quite negligible](https://benfrain.com/inline-or-combined-media-queries-in-sass-fight/).

### Applying a default query

If for whatever reason you want to include a query in the list that is not restricted to any particular container, you can use an empty string as the selector:

``````scss
.element {
  @include mql((
    '' : (max, 1000px),
    '.container' : (min, 1000px)
  )){
    background: red;
  }
}
``````

Which will generate this css:

``````css
@media (max-width: 1000px) {
  .element {
    background: red;
  }
}

@media (min-width: 1001px) {
  .container .element {
    background: red;
  }
}
``````
### Creating a Query List outside of an element

Setting the second parameter in the `mql` mixin to `false` will allow you to place the query list outside elements. (Feature introduced in version 2.0.0).

````scss
@include mql((
    '.container-1' : (max, 1000px),
    '.container-2' : (min, 1000px)
), false){
  .element {
    background: red;
  }
  .otherThing {
    background: green;
  }
}
````

That would create this css:

``````css
@media (max-width: 1000px) {
  .container-1 .element {
    background: red;
  }
  .container-1 .otherThing {
    background: green;
  }
}

@media (min-width: 1001px) {
  .container-2 .element {
    background: red;
  }
  .container-2 .otherThing {
    background: green;
  }
}
``````

### Debugging

If you are debugging a particular query-list, set it's `$debug` property to `true` for extra information to be printed to the console.

````scss
.element {
  @include mql((
    '.container-1' : (max, 1000px),
    '.container-2' : (min, 1000px)
  ), $debug: true){
    background: red;
  }
}
````

The above code will output this to the console:

````
node_modules/query-list/mql.scss:11 DEBUG: "selector:" .container-1 .element
node_modules/mq-scss/_mq.scss:380 DEBUG: max, 1000px
node_modules/mq-scss/_mq.scss:289 DEBUG: joinQueries_statement max
node_modules/mq-scss/_mq.scss:289 DEBUG: joinQueries_statement 1000px
node_modules/mq-scss/_mq.scss:248 DEBUG: get_values_result (range: max, breakpoint_1: 1000px, breakpoint_2: null, media: "")
node_modules/mq-scss/_mq.scss:90 DEBUG: calculateMQ (range: max, breakpoint_1: 1000px, breakpoint_2: 0, mediaType: "")
node_modules/mq-scss/_mq.scss:386 DEBUG: !!!!! FINAL OUTPUT: @media (max-width: 1000px)
node_modules/mq-scss/_mq.scss:435 DEBUG:
node_modules/query-list/mql.scss:11 DEBUG: "selector:" .container-2 .element
node_modules/mq-scss/_mq.scss:380 DEBUG: min, 1000px
node_modules/mq-scss/_mq.scss:289 DEBUG: joinQueries_statement min
node_modules/mq-scss/_mq.scss:289 DEBUG: joinQueries_statement 1000px
node_modules/mq-scss/_mq.scss:248 DEBUG: get_values_result (range: min, breakpoint_1: 1000px, breakpoint_2: null, media: "")
node_modules/mq-scss/_mq.scss:90 DEBUG: calculateMQ (range: min, breakpoint_1: 1000px, breakpoint_2: 0, mediaType: "")
node_modules/mq-scss/_mq.scss:386 DEBUG: !!!!! FINAL OUTPUT: @media (min-width: 1001px)
node_modules/mq-scss/_mq.scss:435 DEBUG:
````

## Change Log

### v2.0.0

- Changed from `@include ql()` to `@include mql()`. This decision was made so that it makes more sense when combined with the mq-scss `@include mq()` mixin.
- Added the `$isInside` parameter.
- Updated to use whatever the latest version of `mq-scss` currently is.
- Added the ability to turn on debugging.