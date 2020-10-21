# Background
There is a need for WinUI to support vertical pagination UI that resides to the left or right of the layout view with "pip" glyphs and arrow navigation. 
This pagination experience is independent of the layout UI and is supported via customizations of the existing Numerical ButtonPanel DisplayMode in PagerControl. The three main customizations are below:
- Glyphs (pips) to represent pages
- Vertical orientation
- Arrow visiblility dependent on user hover

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
            DisplayMode="VerticalPips"
            NumberOfPages="5"
            SelectedPageIndex="2"/>
    </muxc:PagerControl>
</Grid>
```

## Recommended VerticalPips configuration with VisibleOnHover button visibility
A PagerControl set to the VerticalPips display mode with the recommended button visibility property settings. The VisibleOnHover property, new to PagerControl, would enable the option to only show the arrow when the user is hovering over the PagerControl.

![Vertical pips mockup with default styling](images/pager-control-vertical-pips-recommended.jpg)

XAML
```XAML
<Grid> 
    <muxc:PagerControl x:Name="pager"
            DisplayMode="VerticalPips"
            NumberOfPages="5"
            SelectedPageIndex="2"
            FirstButtonVisibility="None"
            NextButtonVisibility="VisibleOnHover"
            PreviousButtonVisibility="VisibleOnHover"
            LastButtonVisibility="None"/>
    </muxc:PagerControl>
</Grid>
```

## Custom pip glyphs
[TBD](#p2) on glyph customization implementation

# Remarks
## Configuration
The VerticalPips DisplayMode will support the UI behavior of the various configurations in the ButtonPanel DisplayMode with the recommended configuration shown below:

| Name | Description| Recommended for VerticalPips | PagerControl Default |
|:---:|:---|:---| :--- |
| FirstButtonVisibility | Visibility of the button displaying text and/or glyph indicating that the user may navigate to the first index. | Hidden | HiddenOnEdge |
| PreviousButtonVisibility | Visibility of the button displaying text and/or glyph indicating that the user may navigate to the previous index.| VisibleOnHover* | HiddenOnEdge |
| NextButtonVisibility | Visibility of the button displaying text and/or glyph indicating that the user may navigate to the next index. | VisibleOnHover* | HiddenOnEdge |
| LastButtonVisibility | Visibility of the button displaying text and/or glyph indicating that the user may navigate to the last index. | Hidden | HiddenOnEdge |
| ButtonPanelAlwaysShowFirstAndLastPage | Note: This property only applies to the button panel display mode. Boolean to display the ellipses and the first and last index of the numerical button panel display mode. | N/A for VerticalPips | True | 
| NumberOfPages | Sets the max number of pages the index control will iterate through. The default will represent an infinite page range. | -1 | - 1 |
| SelectedPageIndex | The 0 based index that is currently selected. It will default to the first index. | 0 | 0 |

*VisibleOnHover button visibility enum proposed as new value and described in our [API Notes](#addition-of-visibleOnHover-to-the-PagerControlButtonVisibility-enum).
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
### Addition of VerticalPips to the DisplayMode enum
| Name | Description| Default | 
|:---:|:---|:---|
| PagerControlDisplayMode | Enum that contains 5 values (Auto, ComboBox, NumberBox, ButtonPanel, **VerticalPips**) to represent the look of the pager control. When Auto is selected, the display mode will be ComboBox if the NumberOfPages property is less than 11 otherwise it will be NumberBox. | Auto |

```IDL
enum PagerControlDisplayMode
{
    Auto,
    ComboBox,
    NumberBox,
    ButtonPanel,
    VerticalPips, // new
};
```

### Addition of VisibleOnHover to the PagerControlButtonVisibility enum
A new state to display the pagination buttons dependent on user hover over the PagerControl. 
For example, only the top arrow is shown in this usage since the user is hovering over the arrow footprint*


*Design decision [TBD](#for-design) on whether this hover is only on arrow footprint, the associated half of the control, or the entire control.

```IDL
enum PagerControlButtonVisibility
{
    Visible,
    HiddenOnEdge,
    Hidden,
    VisibleOnHover // new
};
```

## UI Components
- Transforms of existing glyphs for First, Previous, Next, and Last button 270 degrees from current orientation.
- Flexibility in control height to allow number of pips to grow infinitely


## Theme Resources
| Name | Description | Type
|:-:|:--| :-- |
| PagerControlVerticalPipDefaultGlyph | Sets the default vertical pip glyph | String |
| PagerControlVerticalPipSelectedGlyph | Sets the vertical pip glyph when selected | String |
| PagerControlVerticalPipGlyphFontSize* | Sets the size in pixels of the vertical pip glyph | Double |
| PagerControlVerticalPipSelectedGlyphFontSize* | Sets the size in pixels of the vertical pip glyph when selected | Double |
| PagerControlVerticalPipDefaultForegroundBrush* | Sets the foreground brush of the default vertical pip glyphs| SolidColorBrush
| PagerControlVerticalPipSelectedForegroundBrush* | Sets the foreground brush of the vertical pip glyph when selected | SolidColorBrush
| PagerControlVerticalPipPointerOver* | Sets the pointer over/hover visual state for the vertical pip display mode | StaticResource
| PagerControlVerticalPipPressed* | Sets the pressed visual state for the vertical pip display mode | StaticResource
| PagerControlVerticalPipSelected* | Sets the selected visual state for the vertical pip display mode | StaticResource
| PagerControlVerticalPipMargin* | Sets the margin, in pixels, between each vertical pip glyph | Thickness

*These resources would be for complex glyph customization and are a lower priority ask. See [Prioritization](#prioritization) for more info on intended features.

# Appendix
- The functionality for screen readers is the exact same as a ButtonPanel even though it is announcing numerical pages. This is due to the fact that the pips are still a representation of the specific page a user is on. Visually, it is less important to know the exact page number and total number of pages, however to users of screen readers, knowledge of the page in context with others remains as required.

# Open questions

## General
- Does an orientation property in PagerControl make sense considering the possibility of other DisplayModes supporting a vertical orientation?
  - At this time, no since the orientation property would be unique to pips.
- When NumberOfPages is zero or one, what is the expected behavior of the PagerControl?
  - The PagerControl should disappear from view, similar to a scroll bar. In Windows, even if the accessibility setting: automatically hide scroll bars in Windows is turned off (so scroll bars are always visible) scroll bars won't appear if the page doesn't require one in the first place.
- What should this be named? 😁

## For dev
- Should we set default PagerControlButtonVisibilityBehavior for each button by DisplayMode? This would entail adding an "Auto" option as the default for each DisplayMode to have their own custom visibility defaults for each button among the Visible, HiddenOnEdge, and Hidden.
  - Initial thoughts: Yes, especially if we feel the VerticalPips DisplayMode shouldn't encourage the first/last buttons by default and we have the opportunity currently to do this. However, it currently lower priority than the other required features.

## For design
- How do we show an unselected pip from a selected one?
  - My initial thoughts: Unselected pips should be the "disabled" brush color while selected should be black/white/accent color dependent on the user theme.
- What is the size of the pips/arrows/touch targets? If a user provides a larger/smaller set how will we adjust?
  - P0: Our recommended sizes for our needs consistent with the other PagerControl display modes.
  - Initial explorations for P2: The user can customize their glyph and spacing via a theme resource.
- If we reach the max number of recommended pips, what should occur? Should there be a max number?
  - P0: There shouldn't be a max number of pips and the number of pips will grow based on the NumberOfPages property.
  - Initial explorations for P2: Pip size variance and dot centering animations, see Instagram-like horizontal mockup below from [@Paelaez](https://medium.com/@Pelaez/recreating-instagrams-page-control-ebc2103b8a39)
  ![Vertical pips mockup with default styling](images/pager-control-vertical-pips-max-and-center-dots.gif)
- For the VisibleOnHover button visibility, what is the touch or hover target to show the arrow?
  - Options: On arrow footprint bounding box, on the half of the PagerControl it is attached to, on the entirety of the PagerControl, ???
- What touch gestures should we support for navigating between the layout pages?
  - P0: Swipe/flick up and down to the left and right of the PagerControl
  - P2: Long press?

# Prioritization
*Feature may move up or down in priority based on need.
## P0
- Glyphs to represent pages (pips)
- Verticality of keyboard/touch interaction and glyph orientation
- Functionality to enable the option to vary arrow visibility dependent on user hover
## P1
- A MaxPagesDisplayed property to enable the scrolling UI design when the number of pages is greater than the number of pages shown in the control.*
- Basic glyph customization:
  - Resources to change default and selected pip glyph (will keep default icon size)
  - Resources to change default and selected pip glyph foreground color (could drop in priority dependent on complexity)
- Add an 'Auto' property to the PagerControlButtonVisibility enum to set custom button defaults by DisplayMode.
- Event to fire once the user attempts to navigate past the last page to enable looping behavior in the application
## P2
- Complex glyph customization:
  - Resources to change default and selected pip glyph font size that ties to the hover target.
  - Resources to change margin between pips
  - Resources to change pointer over, pressed, and selected visual state 
- Long-press to change pages (unsure of details here)
