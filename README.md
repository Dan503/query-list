
# query-list

A Sass mixin powered by mq-scss that is a work around for the lack of container queries in css.

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
</details>

---

## Why use this?

I was inspired to write this mixin after reading [this article](https://www.filamentgroup.com/lab/element-query-workarounds.html) by Scott Jehl. In the article, Scott explains that without being able to use container queries, we often have to write unmaintainable blocks of repetitive code for the sake of modules living in different parts of a layout.

I ran into this issue myself on one of my projects. When I saw his half finished idea of what a good media query mixin could look like, I ran with it and fleshed it out a bit.

[The article](https://www.filamentgroup.com/lab/element-query-workarounds.html) explains the problem that this mixin is trying to solve very well so I won't try to explain it again here. Have a read of that article :)

## Installation

```````````
npm install query-list --save
```````````

Import mq-scss and query-list at the top of your main Sass file in that order (note that the exact path will differ depending on your folder structure).

``````scss
@import "../node_modules/mq-scss/mq";
@import "../node_modules/query-list/ql";
``````

It will automatically install mq-scss for you when you install query-list.

## Usage

### Syntax

This is the general syntax for the mixin:

``````scss
@include ql((
  '.container-selector-1' : ($range, $breakpoint-1[, $breakpoint-2]),
  '.container-selector-2' : ($range, $breakpoint-1[, $breakpoint-2])
)){
  //styles go here
}
``````

`($range, $breakpoint-1[, $breakpoint-2])` is an [mq-scss](https://www.npmjs.com/package/mq-scss) media query variable. Being powered by mq-scss, the media query possibilities at your finger tips are immense. See the [mq-scss documentation](https://www.npmjs.com/package/mq-scss) for more information on how to write the actual media queries for this mixin.

### Basic example

Here is an example of how you might use this:

``````scss
.element {
  @include ql((
    '.container-1' : (max, 1000px),
    '.container-2' : (min, 1000px)
  )){
    background: red;
  }
}
``````

That would create this css:

``````css
@media screen and (max-width: 1000px) {
  .container-1 .element {
    background: red;
  }
}

@media screen and (min-width: 1001px) {
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
    @include ql((
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
@media screen and (max-width: 1000px) {
  .container-1 .element__childElement {
    background: red;
  }
}

@media screen and (min-width: 1001px) {
  .container-2 .element__childElement {
    background: red;
  }
}
``````

### Query List reuse

If you need to use the same set of queries multiple times, I would recommend saving the Query List into a variable first:

``````scss
$QL-element--large: (
  '.container-1' : (max, 1000px),
  '.container-2' : (min, 1000px)
);

.element {
  @include ql($QL-element--large){
    background: red;
  }

  &__childElement {
    @include ql($QL-element--large){
      background: blue;
    }
  }
}
``````

To create this css:

``````css

@media screen and (max-width: 1000px) {
  .container-1 .element {
    background: red;
  }
}

@media screen and (min-width: 1001px) {
  .container-2 .element {
    background: red;
  }
}

@media screen and (max-width: 1000px) {
  .container-1 .element__childElement {
    background: blue;
  }
}

@media screen and (min-width: 1001px) {
  .container-2 .element__childElement {
    background: blue;
  }
}

``````

If you are worrying about all the repetition of media queries in the output css, you can stop now. Gzipping makes the file size increase from repeated media queries [quite negligible](https://benfrain.com/inline-or-combined-media-queries-in-sass-fight/).

### Applying a default query

If for whatever reason you want to include a query in the list that is not restricted to any particular container, you can use an empty string as the selector:

``````scss
.element {
  @include ql((
    '' : (max, 1000px),
    '.container' : (min, 1000px)
  )){
    background: red;
  }
}
``````

Which will generate this css:

``````css
@media screen and (max-width: 1000px) {
  .element {
    background: red;
  }
}

@media screen and (min-width: 1001px) {
  .container .element {
    background: red;
  }
}
``````
