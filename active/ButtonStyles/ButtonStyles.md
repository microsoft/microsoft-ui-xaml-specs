# Button Styles detailed design spec / redline
This spec lists out all the details of button styles design that should be updated as part of [#1054](https://github.com/microsoft/microsoft-ui-xaml/issues/1054). Depending on schedule and other unforseen circumstances, not all could be implemented so this does not represent a final plan yet.

## Button styles
There are three main button styles Windows application might consider using depeneding on type of usage:
1. Standard (fill)
2. Primary
3. Ghost (outline)

![StyleDefinitions](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/chigy/ButtonStyles/active/ButtonStyles/Images/StyleDefinitions3.png)

Outline style is less visible to users compared to Fill style and are more appropriate to be used on form type of UI where it is not the [**default button**](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/dialogs-and-flyouts/dialogs#defaultbutton).

* Standard (fill) – It is the default for Windows and most common button style. It can be used on most backgrounds reliably and will keep UI clear and understandable.
* Primary – It is for highlighting your primary action at a given moment. A very good example is use of this style in [Dialog control](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/dialogs-and-flyouts/dialogs#defaultbutton). Use only one of these at a time in order to keep a clear emphasis on a single course of action.
* Ghost (outline) – As the name indicates, it is subtle style and may not work well on images or non uniformly colors surfaces.

Either fill or ghost style buttons may be used as desired for your application, however use them in a consistent manner within your UI. This will help your user navigate your app's UI with less effort.

## The anatomy of button
![anatomy](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/chigy/ButtonStyles/active/ButtonStyles/Images/Anatomy.png)

### Basic button UI elements
Button has the following UI elements that determines visuals:
1. Text
2. The box fill
3. Keyboard focus rect (not part of the control)
4. The box outline

### Button colors for states
* Button expresses the state of the button by use of fill color of the box.
* Text color is only manipulated when in disabled state.
* Outline color does not change based on the state.

## Default design specification
### UI element sizes

| UI element | Size | 
| :---------- | :------- |
| Text | 14px (standard text size) |
| The box outline | 1px |


### Light Theme Color values

| State | 1. Standard (fill) | 2. Primary | 3. Ghost (outline) |
| :---------- | :------- | :------- | :------- |
| Rest |  |  |  |
| Hover |  |  |  |
| Pressed |  |  |  |
| Disabled |  |  |  |

### Dark Theme Color values

| State | 1. Standard (fill) | 2. Primary | 3. Ghost (outline) |
| :---------- | :------- | :------- | :------- |
| Rest |  |  |  |
| Hover |  |  |  |
| Pressed |  |  |  |
| Disabled |  |  |  |


