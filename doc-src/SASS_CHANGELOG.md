# Sass Changelog

* Table of contents
{:toc}

## 2.2.0

The 2.2 release marks a significant step in the evolution of the Sass language. The focus has been to increase the power of Sass to keep your stylesheets maintainable by allowing new forms of abstraction to be created within your stylesheets and the stylesheets provided by others that you can download and import into your own. The fundamental units of abstraction in Sass are variables and mixins. Please read below for a list of changes:

### Sass, Interactive

The sass command line option -i will allow you to quickly and interactively experiment with SassScript expressions. Example:

    $ sass -i
    >> 5px
    5px
    >> 5px + 10px
    15px
    >> !five_pixels = 5px
    5px
    >> !five_pixels + 10px
    15px

### SassScript

The features of SassScript have been greatly enhanced with new control structures, new fundamental data types, and variable scoping.

#### New Data Types

SassScript now has four fundamental data types:

1. Number
2. String
3. Boolean
4. Colors

### More Flexible Numbers

Like JavaScript, SassScript numbers can now change between floating point and integers. No explicit casting or decimal syntax is required. When a number is emitted into a CSS file it will be rounded to the nearest thousandth, however the internal representation maintains much higher precision.

#### Numeric Units

While Sass has long supported numbers with units, it now has a much deeper understanding of them. The following are examples of legal numbers in SassScript:

    0, 1000, 6%, 2px, 5pc, 20em, or -2foo.

Numbers of the same unit may always be added and subtracted. Numbers that have units that Sass understands and finds comparable, can be combined, taking the unit of the first number. Numbers that have non-comparable units may not be added nor subtracted. Any attempt to do so will cause an error. However, a unitless number takes on the unit of the other number during a mathematical operation. For example:

    >> 3mm + 4cm
    43mm
    >> 4cm + 3mm
    4.3cm
    >> 3cm + 2in
    8.08cm
    >> 5foo + 6foo
    11foo
    >> 4% + 5px
    SyntaxError: Incompatible units: 'px' and '%'.
    >> 5 + 10px
    15px

Sass allows compound units to be stored in any intermediate form, but will raise an error if you try to emit a compound unit into your css file.

    >> !em_ratio = 1em / 16px
    0.063em/px
    >> !em_ratio * 32px
    2em
    >> !em_ratio * 40px
    2.5em

#### Colors

A color value can be declared using a color name, hexadecimal, shorthand hexadecimal, rgb, or hsl. When outputting
a color into css, the color name is used, if any, otherwise it is emitted as hexadecimal value. Examples:

    > #fff
    white
    >> white
    white
    >> #FFFFFF
    white
    >> hsl(180, 100, 100)
    white
    >> rgb(255, 255, 255)
    white
    >> #AAA
    #aaaaaa

Math on color objects is performed piecewise on the rgb components. However, these operations rarely have meaning in the design domain.

    >> #aaa + #123
    #bbccdd
    >> #333 * 2
    #666666

#### Booleans

Boolean objects can be created by comparison operators or via the `true` and `false` keywords. Booleans can be combined using the `and`,
`or`, and `not` keywords.

    >> true
    true
    >> true and false
    false
    >> 5 < 10
    true
    >> not (5 < 10)
    false
    >> not (5 < 10) or not (10 < 5)
    true

### Control Structures

New directives provide branching and looping within a sass stylesheet based on SassScript expressions. See the [Sass Reference](SASS_REFERENCE.md.html#control_structures) for complete details.

#### @for

The `@for` directive Loop over a set of numbers defining the current number into the variable specified for each loop.

    @for !x from 1px through 5px
      .border-#{!x}
        border-width= !x

compiles to:

    .border-1px {
      border-width: 1px; }

    .border-2px {
      border-width: 2px; }

    .border-3px {
      border-width: 3px; }

    .border-4px {
      border-width: 4px; }

    .border-5px {
      border-width: 5px; }

#### @if / @else if / @else

The branching directives `@if`, `@else if`, and `@else` let you select between several branches of sass to be emitted, based
on the result of a SassScript expression. Example:

    !type = "monster"
    p
      @if !type == "ocean"
        color: blue
      @else if !type == "matador"
        color: red
      @else if !type == "monster"
        color: green
      @else
        color: black

is compiled to:

    p {
      color: green; }

#### @while

The `@while` directive lets you iterate until a condition is met. Example:

    !i = 6
    @while !i > 0
      .item-#{!i}
        width = 2em * !i
      !i = !i - 2

is compiled to:

    .item-6 {
      width: 12em; }

    .item-4 {
      width: 8em; }

    .item-2 {
      width: 4em; }


### Interpolation

Interpolation has been added. This allows SassScript to be used to create dynamic properties and selectors. It also cleans up
some uses of dynamic values when dealing with compound properties. Using interpolation, the result of a SassScript expression
can be placed anywhere:

    !x = 1
    !d = 3
    !property = "border"
    div.#{!property}
      #{!property}: #{!x + !d}px solid
      #{!property}-color: blue

is compiled to:

    div.border {
      border: 4px solid;
      border-color: blue; }


