#### Index

1. [Naming & Syntax Conventions](#naming--syntax-conventions)
    - [Defining Variables & Setting Values](#defining-variables--setting-values)
    - [Handlers & Variables](#handlers--variables)
    - [Calling Nested Scripts](#calling-nested-scripts)
    - [Defining Search Rectangles](#defining-search-rectangles)
    - [Single-Line Expressions Inside Conditionals](#single-line-expressions-inside-conditionals)
2. [Handling Image Paths](#handing-image-paths)
    - [Adding New Keys](#adding-new-keys)
    - [Appending to Returned Value](#appending-to-returned-value)
    - [Keyed Image Paths](#keyed-image-paths)
    - [Naming for Various Image States](#naming-for-various-image-states)
3. [Localizations, Hex Codes & SUT Information](#localizations-hex-codes--sut-information)

## Naming & Syntax Conventions

The following sections contain information specific to the relevant project standards. However, here are some quick general tips:

  - Provide single and equal spacing around operators and within lists.

### Defining Variables & Setting Values

When defining a variable or changing the value inside a container, use the `put <value> into <container>` syntax, unless specified in an exception below. This is for consistency and the convenience of being able to highlight and run a selection.

Exceptions to this include setting the value of a global SenseTalk variable or defining a property list in multi-line format:

```
set the remoteWorkInterval to 0.7

set myPropertyList to {
    fooKey: "value1",
    barKey: "value2",
}
```

### Handlers & Variables

Although SenseTalk and Eggplant Functional default to PascalCase, handlers and variables should use a strict **camelCase** format. This includes acronyms (ex. `formattedUrl`). Property list keys should be formatted with **snake_case**.

### Calling Nested Scripts

Scripts that are nested inside folders should be called with the multi-line string operator. If used repeatedly, put the script path into a variable and reference using dot syntax.

```
put <<applications/calculator>> into calculator

(calculator).launchProgram
(calculator).pressClearButton
assert that (calculator).displayedValue() equals zero
(calculator).checkOperators
(calculator).checkNumpad
(calculator).closeProgram
```

### Defining Search Rectangles

All search rectangles should be defined by **topLeft** and **bottomRight** anchors, respectively. Additionally, any offset logic should be done via addition, even when dealing with negative numbers.

```
put foundImageLocation + (-15, 300) into myRect
```

### Single-Line Expressions Inside Conditionals

When a condition includes only a single-line expression, simply include it after the `then` keyword instead of wrapping the expression with `if` and `end if` keywords. The same logic follows for single-line `else` blocs and `try` blocs without exception handling.

```
if imageFound(imageName)
    click foundImageLocation()
end if

if imageFound(imageName) then click foundImageLocation()
else logError "Missing image."

try to click imageLocation(imageName)
```

## Handling Image Paths

In SenseTalk, the path for an image is defined with a string representing the location relative to the suite's `Images/` directory. In order to reduce maintenance and ensure data-safety across usage, path values will be defined within and returned from the `imgPath` script.

This script accepts a required first parameter which must match a `String` key defined within the associated script's property list of available image paths. Any additional parameters (optional) passed in will be treated as a list of arguments for the purposes of replacing placeholders in the returned raw value ([see below for more information](#keyed-image-paths)).

Root paths and other helpful strings will be defined *outside* and above the property list; deeper nested folders will then be able to branch off and combine these values. See the following example:

```
put "applications/calculator/" into calcPath
put"systems/$0/" into systemPath

set imagePaths to {

    ### CALCULATOR
    calc_clear_button: calcPath & "clear_button",
    calc_decimal_button: calcPath & "decimal_button",


    ### SYSTEMS > WINDOWS
    win_taskbar_icons: systemPath & "taskbar_icons/$1",

}
```

### Adding New Image Keys

Keys should be adding in their most appropriate section; create a new commented section if necessary. Additionally, keys should be formatted in [**snake_case**](https://user-images.githubusercontent.com/4438390/68147781-d6cb9c00-ff08-11e9-9eb8-5db1e505c729.png). Lastly, keys should be appropriately named, organized alphabetically within their respective section, and include a trailing comma to account for future additions.

**Important:** Image states (e.g. disabled, hover, selected) should **not** be included as an available values within the property list. Image state identifiers should be added onto the returned value in-line.

```
put imgPath("calc_close_window_button") & "hover/" into closeButtonHover   // correct usage
put imgPath("calc_close_window_button_hover") into closeButtonHover        // incorrect
```

### Appending to Returned Value

When adding additional path information to the value returned from the script, ensure the string always ends with a forward-slash `/`. This is to reduce confusion and ensure that any later additions do not need to add a leading slash.

### Keyed Image Paths

Some raw image paths may include keyed placeholders, allowing for arguments to be passed in as the second (optional) parameter to the script. These will replace the keys sequentially. In other words, the first argument will replace the first available key in the resulting image path, and so on.

```
put imgPath("win_taskbar_icons")                             // systems/$0/taskbar_icons/$1/
put imgPath("win_taskbar_icons", "windows", "calculator")    // systems/windows/taskbar_icons/calculator/
```

### Naming for Various Image States

When necessary, use the appropriate adjective in the list below when capturing images of element states, ordered by an example folder hierarchy:

- active: represents an active/running application, foreground element, etc.
  - default: the base enabled state for all image elements
  - disabled: the base disabled state
  - hover: when the cursor is on top of, but not interacting with, a
  - selected: representing a default state where the user has interacted with the element
- inactive: a suspended or background element

## Localizations, Hex Codes & SUT Information

The same philosophy and format used for the `imgPath` script has been applied to these (and likely more) sections. See individual scripts for more information such as current keys and additional methods available.
