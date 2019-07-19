# Button Styles detailed design spec / redline
This spec provides the systematic approach to coherent visual styles used for Buttons. This also have an overlap to what we are doing with the CheckBox visual change, but this spec covers Button only.

## Shared Visual Styles
Many of our controls use the following visual styles:
1. Fill
2. Accent
3. Outline

Here is an example of these visual styles used for Buttons. 
![StyleDefinitions](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/chigy/ButtonStyles/active/ButtonStyles/Images/StyleDefinitions3.png)



* **Fill**
    *  The default style used for Buttons on Windows, thus built as a default button visual style for WinUI. 
    *  It can be used on most backgrounds reliably and will keep UI clear and understandable.
* **Accent**
    * A visual style suitable for highlighting your primary action at a given moment. A very good example is use of this style in [Dialog control's DefaultButton](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/dialogs-and-flyouts/dialogs#defaultbutton). 
    * Use only one of these at a time in order to keep a clear emphasis on a single course of action.
* **Outline**
    * This style is less visible to users compared to Fill style and are more appropriate to be used on form type of UI where less visual hierarchy might be desired.
    * It is subtler style compared with other styles and may not work well on images or non uniformly colors surfaces.

You can choose to use buttons using either fill or outline visual styles as desired for your application, however use them in a consistent manner within your UI. This will help your user navigate your app's UI with less effort.

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

| State | 1. Fill | 2. Accent | 3. Outline |
| :---------- | :------- | :------- | :------- |
| Rest |  |  |  |
| Hover |  |  |  |
| Pressed |  |  |  |
| Disabled |  |  |  |

### Dark Theme Color values

| State | 1. Fill | 2. Accent | 3. Outline |
| :---------- | :------- | :------- | :------- |
| Rest |  |  |  |
| Hover |  |  |  |
| Pressed |  |  |  |
| Disabled |  |  |  |


