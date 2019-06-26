# Checkbox detailed design spec / redline

## The anatomy of checkbox
### Basic checkbox sizing
Checkbox has the following UI components that determines visuals that impacts its sizing:
1. The box fill
2. The box border
3. The glyph
4. Text

### Checkbox colors
Following colors are used to denote the state of the checkbox:
1. Fill color of the box
2. Foreground color of the border
3. Foreground color of the glyphs
4. Foreground color for the text

![anatomy](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/chigy/ControlUpdates/active/ControlUpdates/DetailedDesignSpec/Images/Anatomy.png)

### Basic checkbox styles
Checkbox has mainly 3 styles:
1. Outline unselected
2. Fill selected
3. Fill unselected
Using these three basic styles on different controls based on typical use of the control, we create coherent experience for application surfaces.

![styles](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/chigy/ControlUpdates/active/ControlUpdates/DetailedDesignSpec/Images/BaseDesign.png)
