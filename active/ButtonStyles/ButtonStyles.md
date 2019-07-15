# Button Styles detailed design spec / redline
This spec lists out all the details of button styles design that should be updated as part of [#1054](https://github.com/microsoft/microsoft-ui-xaml/issues/1054). Depending on schedule and other unforseen circumstances, not all could be implemented so this does not represent a final plan yet.

## Button styles
There are three main button styles Windows application might consider using depeneding on type of usage:
1. Fill Primary
2. Fill Accent
3. Outline

![StyleDefinitions](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/chigy/ButtonStyles/active/ButtonStyles/Images/StyleDefinitions.png)

Outline style is less visible to users compared to Fill style and are more appropriate to be used on form type of UI where it is not the [**default button**](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/dialogs-and-flyouts/dialogs#defaultbutton).

Fill style is more visible especially against background that is not a solid color. Windows shell tend to use this style more as the buttons tend to be drawn on top of other UI element that is not solid white or black.

There is no strict rules for which style to use, however use of the button in a consistent manner within your UI that has the same meaning will help your user navigate your app's UI with less effort.

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

| State | 1. Fill Primary | 2. Fill Accent | 3. Outline |
| :---------- | :------- | :------- | :------- |
| Rest |  |  |  |
| Hover |  |  |  |
| Pressed |  |  |  |
| Disabled |  |  |  |

### Dark Theme Color values

| State | 1. Fill Primary | 2. Fill Accent | 3. Outline |
| :---------- | :------- | :------- | :------- |
| Rest |  |  |  |
| Hover |  |  |  |
| Pressed |  |  |  |
| Disabled |  |  |  |


