# Checkbox detailed design spec / redline
This spec lists out all the details of checkbox design that should be updated as part of [#839](https://github.com/microsoft/microsoft-ui-xaml/issues/839). Depending on schedule and other unforseen circumstances, not all could be implemented so this does not represent a final plan yet.

## Checkbox styles
Checkbox has mainly 3 styles:
1. Outline unselected
2. Fill selected
3. Fill unselected

![styles](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/chigy/checkboxes/active/checkboxes/Images/BaseDesign.png)

Visually, fill style is little more visible and highlights the controls better than the Outline style.

For Windows, we default to using Outline style for Unselected and Fill style for Selected with exception of GridView.
GridView is often used on top of colored tile or images and default Fill visual is being used to make the checkbox more visible at unselected state.

For details on which control use what style, refer to later section.

## The anatomy of checkbox
![anatomy](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/chigy/checkboxes/active/checkboxes/Images/Anatomy.png)
### Basic checkbox UI elements
Checkbox has the following UI elements that determines visuals:
1. The box fill
2. The box border
3. The glyph
4. The indeterminate marker
5. Text

### Checkbox colors for states
Following colors are used to denote the state of the checkbox:
1. Fill color of the box
2. Color of the border
3. Foreground color of the glyph
4. The fill color of the indeterminate indicator
5. Foreground color for the text

## Default design specification
### UI element sizes

| UI element | Size | 
| :---------- | :------- |
| The box border | 1px border (if we implement [#835](https://github.com/microsoft/microsoft-ui-xaml/issues/835). Otherwise 2px). Rounded corner following [#524](https://github.com/microsoft/microsoft-ui-xaml/issues/524) design.|
| The glyph | 16px |
| The indeterminate marker | 8x8px |
| Text | 14px (standard text size) |

### Light Theme Color values

| State | 1. Outline unseledted | 2. Fill selected | 3. Fill unselected |
| :---------- | :------- | :------- | :------- |
| Rest | Outline: #000000 80% (no change) |	Fill: SystemAccentColor (no change) |	Fill: #E6E6E6 100% (no change) |
| Hover | Outline: #000000 100% (no change) |	Fill: SystemAccentColorLight1 |	Fill: #E6E6E6 100% (no change) |
| Pressed | Outline: #000000 100%, Fill: #000000 40% | Fill: SystemAccentColorDark1 | Fill: #E6E6E6 100% (no change) |
| Disabled | Outline: #000000 40% (no change) |Fill: #000000 40%, Checkmark: #FFFFFF 60% | Fill: #E6E6E6 100% (no change) |

### Dark Theme Color values

| State | 1. Outline unseledted | 2. Fill selected | 3. Fill unselected |
| :---------- | :------- | :------- | :------- |
| Rest | Outline: #FFFFFF 80% (no change) |	Fill: SystemAccentColor (no change) |	Fill: #E6E6E6 100% |
| Hover | Outline: #FFFFFF 100% (no change) |	Fill: SystemAccentColorDark1 |	Fill: #E6E6E6 100% |
| Pressed | Outline: #FFFFFF 100%, Fill: #FFFFFF 40% | Fill: SystemAccentColorLight1 | Fill: #E6E6E6 100% |
| Disabled | Outline: #FFFFFF 40% (no change) |Fill: #FFFFFF 40%, Checkmark: #FFFFFF 60% | Fill: #E6E6E6 100% |

## Checkbox styles
As mentioned earlier, Checkbox has Outine and Fill styles. Following shows which control use what style by default.

| Control | Unseledted | Selected |
| :---------- | :------- | :------- |
| CheckBox | Outline | Fill |
| ListItem | Outline | Fill |
| TreeView | Outline | Fill |
| GridView | Fill | Fill |
| RadioButton* | Outline | Fill |

*Even though RadioButton is not a checkbox, it needs to be following the system pattern to be coherent.
