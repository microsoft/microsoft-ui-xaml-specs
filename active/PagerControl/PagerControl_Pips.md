# Background
There is a need for WinUI to support vertical pagination UI that resides to the left or right of the layout view with "pip" glyphs and arrow navigation. 
This pagination experience is independent of the layout UI and is mostly supported via the existing Numerical ButtonPanel DisplayMode in PagerControl. The two main customizations are below:
- Customizable glyphs that default to dots
- Vertical orientation

The below spec identifies the visual and interactive differences between the new VerticalPips DisplayMode and the ButtonPanel DisplayMode along with the available theme resources for further customization.
# Description
A new DisplayMode to PagerControl, VerticalPips is for scenarios requiring vertical pagination UI when the exact page number is not visually required; e.g. reading lists, app drawers, or photo viewers.

## What is a pip?
Pips represent a unit of numerical value, typically represented as dots, however can be customized to other glyphs like dashes or squares. For Fluent vertical pagination, by default, a solid dot will represent each page of the layout.

# Examples

## Default VerticalPips
A PagerControl set to the VerticalPips display mode, showing the default UI components.

![Vertical pips mockup with default styling](images/pager-control-vertical-pips-default.jpg)

XAML
```XAML
<Grid> 
    <muxc:PagerControl x:Name="pager"
            DisplayMode="VerticalPips"/>
    </muxc:PagerControl>
</Grid>
```
code

## ButtonPanel edge buttons for VerticalPips
A PagerControl set to the VerticalPips display mode supporting the ButtonPanel edge button property to enable persistent first and last edge buttons.

![Vertical pips mockup with default styling](images/pager-control-vertical-pips-edge-buttons.jpg)

XAML
```XAML
<Grid> 
    <muxc:PagerControl x:Name="pager"
            DisplayMode="VerticalPips"
            ButtonPanelAlwaysShowFirstAndLastPageIndex="True"/>
    </muxc:PagerControl>
</Grid>
```

## Custom pip glyphs
TBD on glyph customization implementation

# Remarks
## Configuration
The VerticalPips DisplayMode will support the UI behavior of the various configurations in the ButtonPanel DisplayMode with the defaults shown below:

| Name | Description| Default | 
|:---:|:---|:---|
| FirstButtonVisibility | Visibility of the button displaying text and/or glyph indicating that the user may navigate to the first index. | Hidden |
| PreviousButtonVisibility | Visibility of the button displaying text and/or glyph indicating that the user may navigate to the previous index.| HiddenOnEdge |
| NextButtonVisibility | Visibility of the button displaying text and/or glyph indicating that the user may navigate to the next index. | HiddenOnEdge |
| LastButtonVisibility | Visibility of the button displaying text and/or glyph indicating that the user may navigate to the last index. | Hidden |
| NumberOfPages | Sets the max number of pages the index control will iterate through. The default will represent an infinite page range. | -1 |
| ButtonPanelAlwaysShowFirstAndLastPage | Boolean to display the ellipses* and the first and last index of the numerical button panel display mode. | True |
| SelectedPageIndex | The 0 based index that is currently selected. It will default to the first index. | 0 |

*Note: A design solution is still needed to determine behavior when the number of pages is greater than the number of visible pips. I do not anticipate it will be ellipses and it's possible that this UI component to indicate an accessible but omitted range of indices is not supported with this DisplayMode.


# Input & Accessibility
The VerticalPips DisplayMode will support the same functionality that the ButtonPanel DisplayMode does with the following notes:

## Keyboarding
- Up and down arrows (instead of left and right) will navigate between the items in the control.

## Touch
For touch screen devices, a vertical swipe directly to the left or right of the pager will navigate to the previous or next page.*
 - An "up" swipe will navigate to the previous page.
 - A "down" swipe will navigate to the next page.

*Interaction still to be discussed with design.
# API Notes

## Direct API changes
Addition of the VerticalPips DisplayMode.
| Name | Description| Default | 
|:---:|:---|:---|
| PagerControlDisplayMode | Enum that contains 5 values (Auto, ComboBox, NumberBox, ButtonPanel, VerticalPips) to represent the look of the pager control. When Auto is selected, the display mode will be ComboBox if the NumberOfPages property is less than 11 otherwise it will be NumberBox. | Auto |

IDL addition:
```IDL
enum PagerControlDisplayMode
{
    Auto,
    ComboBox,
    NumberBox,
    ButtonPanel,
    VerticalPips,
};
```
## UI Components
Transforms of existing glyphs for First, Previous, Next, and Last button 270 degrees from current orientation.

## Theme Resources
Open question: Glyph customization via a Style property in API or as a theme resource?
| Name | Description | Type 
|:-:|:--| :-- |
| PagerControlVerticalPipDefaultGlyph | Sets the default vertical pip glyph | String |
| PagerControlVerticalPipSelectedGlyph* | Sets the vertical pip glyph when selected | String |
| PagerControlVerticalPipGlyphFontSize | Sets the size in pixels of the vertical pip glyph | Double |
| PagerControlVerticalPipSelectedGlyphFontSize* | Sets the size in pixels of the vertical pip glyph when selected | Double |
| PagerControlVerticalPipForegroundBrush | Sets the foreground brush of the vertical pip glyphs| SolidColorBrush
| PagerControlVerticalPipPointerOver | Sets the pointer over/hover visual state for the vertical pip display mode | StaticResource
| PagerControlVerticalPipPressed | Sets the pressed visual state for the vertical pip display mode | StaticResource
| PagerControlVerticalPipSelected | Sets the selected visual state for the vertical pip display mode | StaticResource
| PagerControlVerticalPipMargin | Sets the margin, in pixels, between each vertical pip glyph | Thickness

*These resources are definitely not defined as needed yet, only proposals based on current design explorations.

# Appendix
- The functionality for screen readers is the exact same as a ButtonPanel even though it is announcing numerical pages. This is due to the fact that the pips are still a representation of the specific page a user is on. Visually, it is less important to know the exact page number and total number of pages, however to users of screen readers, knowledge of the page in context with others remains as required.

# Open questions
## General
- Do we need to support navigation arrows displaying dependent on hover?
  - Potentially.
- Do we need to support looping pagination?
  - At this time, likely not. 
- Does an orientation property in PagerControl make sense considering the possibility of other DisplayModes supporting a vertical orientation?
  - At this time, no.

## For design
- What is the recommended size of the pips? If a user provides a larger/smaller set how will we adjust?
  - My initial thoughts: Recommended size to be consistent of the default arrow glyphs. The spacing between the pips will scale based on pixel size of chosen glyph.
- How large will the touch targets be on the glyphs?
- If we reach the max number of recommended pips, what should occur?
- By default, how are we representing a selected pip? Are we?
  - My initial thoughts: By changing the foreground color of the solid dot to the user's accent color and increasing the size of the dot.

## For dev
- Should the pip glyph customization replicate the existing pattern for First, Previous, Next, and Last button in that it is via a built-in Style property?
- _If_ we supported navigation arrows display dependent on hover, would this functionality require its own property to turn on/off this behavior?
- _If_ we supported looping pagination, I would anticipate a property to customize turning on/off that behavior. Is this something that ButtonPanel can also take advantage of?

