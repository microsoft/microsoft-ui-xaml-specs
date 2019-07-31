# Button Styles detailed design spec / redline
This spec provides the systematic approach to coherent visual styles used for Buttons. This also have overlap to what we are doing with the CheckBox visual change, but this spec covers Button only.

## Shared Visual Styles
Many of our controls use the following visual styles:
1. Accent
2. Fill
3. Outline
4. Ghost

Here is an example of these visual styles used for Buttons. 
![StyleDefinitions](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/chigy/ButtonStyles/active/ButtonStyles/Images/Visibility.png)



* **Accent**
    * A visual style suitable for highlighting your primary action at a given moment. A very good example is the use of this style in [ContentDialog control's DefaultButton](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/dialogs-and-flyouts/dialogs#defaultbutton). 
    * Use only one of these at a time in order to keep a clear emphasis on a single course of action.
* **Fill**
    *  The default style used for Buttons on Windows, thus built as a default button visual style for WinUI. 
    *  It can be used on most backgrounds reliably and will keep UI clear and understandable.
* **Outline**
    * This style is less visible to users compared to Fill style and is appropriate to use on forms-like UIs where less visual hierarchy might be desirable.
    * This style is more subtle compared with Fill and Accent, and may not work well over images or non-uniformly colored surfaces.
* **Ghost**
   * This style should be used for clickable/touchable components when the visual appearance of a button in its rest state is not desirable.
   * This style is very sublte and blends into the UI surface. Use this style with caution because users may not realize the component is interactive.
   
You can choose to use buttons using either Fill, Outline, or Ghost visual styles as desired for your application, however use them in a consistent manner within your UI. Using a single style consistently will help your users navigate your app's UI with less effort.

## The anatomy of Button
![anatomy](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/chigy/ButtonStyles/active/ButtonStyles/Images/Anatomy.png)

### Basic Button elements
Button is composed of the following UI elements:
1. Text
2. Box fill
3. Keyboard focus rect (not part of the control)
4. Box outline

### Button colors for states
* Button expresses its interaction state through the use of box fill color.
* Text color changes only in the disabled state.
* Outline color does not change based on the state.

## Default design specification
### UI element sizes

| UI element | Size | 
| :---------- | :------- |
| Text | 14px (standard text size) |
| Box outline | 1px |


### Light Theme Color values

| State | Accent style | Fill style | Outline style | Ghost style |
| :---------- | :------- | :------- | :------- | :------- |
| Rest |1. #FFFFFFFF (100% White) *no change* <br/>2. 100% Accent color *no change* |1. #FF000000 (100% Black) *no change* <br/>2. #33000000 (20% Black) *no change* |1. #FF000000 (100% Black) <br/>2. #FFFFFFFF (100% White) <br/>4. #66000000 (40% Black) |1. #FF000000 (100% Black) <br/>2. Transparent color |
| Hover |1. #FFFFFFFF (100% White) *no change* <br/>2. 100% AccentLight1 color |1. #FF000000 (100% Black) *no change* <br/>2. #1A000000 (10% Black) |1. #FF000000 (100% Black) <br/>2. #1A000000 (10% Black) <br/>4. #66000000 (40% Black) |1. #FF000000 (100% Black) <br/>2. #1A000000 (10% Black) |
| Pressed |1. #FFFFFFFF (100% White) *no change* <br/>2. 100% AccentDark1 color|1. #FF000000 (100% Black) *no change* <br/>2. #66000000 (40% Black) *no change*  |1. #FF000000 (100% Black) <br/>2. #33000000 (20% Black) <br/>4. #66000000 (40% Black) |1. #FF000000 (100% Black) <br/>2. #33000000 (20% Black) |
| Disabled |1. #66000000 (40% Black) *no change* <br/>2. #33000000 (20% Black) *no change* |1. #66000000 (40% Black) *no change* <br/>2. #33000000 (20% Black) *no change* |1. #66000000 (40% Black) <br/>2. #FFFFFFFF (100% White) <br/>4. #66000000 (40% Black) |1. #66000000 (40% Black) <br/>2. Transparent color |

### Dark Theme Color values

| State | Accent style | Fill style | Outline style | Ghost style |
| :---------- | :------- | :------- | :------- | :------- |
| Rest |1. #FFFFFFFF (100% White) *no change* <br/>2. 100% Accent color *no change* | 1. #FFFFFFFF (100% White) *no change* <br/>2. #33FFFFFF (20% White) *no change* |1. #FFFFFFFF (100% White) <br/>2. #FF000000 (100% Black) <br/>4. #66FFFFFF (40% White) |1. #FFFFFFFF (100% White) <br/>2. Transparent color |
| Hover |1. #FFFFFFFF (100% White) *no change* <br/>2. 100% AccentDark1 color |1. #FFFFFFFF (100% White) *no change* <br/>2. #1AFFFFFF (10% White) |1. #FFFFFFFF (100% White) <br/>2. #1AFFFFFF (10% White) <br/>4. #66FFFFFF (40% White) |1. #FFFFFFFF (100% White) <br/>2. #1AFFFFFF (10% White) |
| Pressed |1. #FFFFFFFF (100% White) *no change* <br/>2. 100% AccentLight1 color |1. #FFFFFFFF (100% White) *no change* <br/> 2. #66FFFFFF (60% White) *no change* |1. #FFFFFFFF (100% White) <br/>2. #33FFFFFF (20% White) <br/>4. #66FFFFFF (40% White) |1. #FFFFFFFF (100% White) <br/>2. #33FFFFFF (20% White) |
| Disabled |1. #66FFFFFF (40% White) *no change* <br/>2. #33FFFFFF (20% White) *no change* |1. #66FFFFFF (40% White) *no change* <br/>2. #33FFFFFF (20% White) *no change* |1. #66FFFFFF (40% White) <br/>2. #FF000000 (100% Black) <br/>4. #66FFFFFF (40% White)  |1. #66FFFFFF (40% White) <br/>2. Transparent color |


