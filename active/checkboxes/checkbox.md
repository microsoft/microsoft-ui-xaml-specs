# Checkbox detailed design spec / redline
This spec lists out all the details of checkbox design that should be updated as part of [#839](https://github.com/microsoft/microsoft-ui-xaml/issues/839). Depending on schedule and other unforseen circumstances, not all could be implemented so this does not represent a final plan yet.

## The anatomy of checkbox
![anatomy](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/chigy/checkboxes/active/checkboxes/Images/Anatomy.png)
### Basic checkbox UI elements
Checkbox has the following UI elements that determines visuals:
1. The box fill
2. The box border
3. The glyph
4. The indeterminate marker
5. Text

### Checkbox colors
Following colors are used to denote the state of the checkbox:
1. Fill color of the box
2. Color of the border
3. Foreground color of the glyph
4. The fill color
5. Foreground color for the text

### Basic checkbox styles
Checkbox has mainly 3 styles:
1. Outline unselected
2. Fill selected
3. Fill unselected

![styles](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/chigy/checkboxes/active/checkboxes/Images/BaseDesign.png)

Visually, fill style is little more visible and highlights the controls better than the Outline style.

For Windows, we default to using Outline style for Unselected and Fill style for Selected with exception of GridView.
GridView is often used on top of colored tile or images and default Fill visual is being used to make the checkbox more visible at unselected state.

For details on which control use what style, refer to later section.

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
| Rest | #000000 80% (no change) | SystemAccentColor | #E6E6E6 100% (no change)  |
| Hover | #000000 60% (this might be wrong if we implement [#953](https://github.com/microsoft/microsoft-ui-xaml/issues/953)) | #E6E6E6 100% (no change)| SystemAccentColorDark1 (if we implement [#953](https://github.com/microsoft/microsoft-ui-xaml/issues/953)) | #E6E6E6 100% (no change) |
| Pressed | #000000 100% (this might be wrong if we implement [#953](https://github.com/microsoft/microsoft-ui-xaml/issues/953))| SystemAccentColorLight1 (if we implement [#953](https://github.com/microsoft/microsoft-ui-xaml/issues/953)) | #E6E6E6 100% (no change) | #E6E6E6 100% (no change) |
| Disabled | #000000 40% (no change) | #000000 20% | #E6E6E6 100% (no change) |

### Dark Theme Color values

Still waiting for detail

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
