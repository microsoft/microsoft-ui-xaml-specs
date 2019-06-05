<!-- The purpose of this spec is to describe a new feature and
its APIs that make up a new feature in WinUI. -->

<!-- There are two audiences for the spec. The first are people
that want to evaluate and give feedback on the API, as part of
the submission process.  When it's complete
it will be incorporated into the public documentation at
docs.microsoft.com (http://docs.microsoft.com/uwp/toolkits/winui/).
Hopefully we'll be able to copy it mostly verbatim.
So the second audience is everyone that reads there to learn how
and why to use this API. -->

# Background
<!-- Use this section to provide background context for the new API(s)
in this spec. -->

<!-- This section and the appendix are the only sections that likely
do not get copied to docs.microsoft.com; they're just an aid to reading this spec. -->

<!-- If you're modifying an existing API, included a link here to the
existing page(s) -->

<!-- For example, this section is a place to explain why you're adding this API rather than
modifying an existing API. -->

<!-- For example, this is a place to provide a brief explanation of some dependent
area, just explanation enough to understand this new API, rather than telling
the reader "go read 100 pages of background information posted at ...". -->

> See [proposal document](https://github.com/Microsoft/microsoft-ui-xaml/issues/684) for information on this feature's scope, and see the [parent proposal document](https://github.com/microsoft/microsoft-ui-xaml/issues/524) for information about design application of this feature.

# Description
<!-- Use this section to provide a brief description of the feature.-->
The ``CornerRadius`` property for controls and their templates can be easily customized/themed through the introduction of new [Lightweight styling](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/xaml-styles#lightweight-styling) resources.

# Examples
The following example cover common use-case scenarios where an app-author would like to customize the corner radii of one or more controls.

## Setting the CornerRadii of controls
*lorem*

``` xml
<Application.Resources>
  <Thickness x:Key="ControlThemeCornerRadius">10</Thickness>
  <Thickness x:Key="OverlayThemeCornerRadius">10</Thickness>
</Application.Resources>
```

## Defining the CornerRadius on specific control(s)
*lorem*

``` xml
<Page.Resources>
  <Thickness x:Key="ButtonThemeCornerRadius">5</Thickness>
  <Thickness x:Key="ProgressBarThemeCornerRadius">3</Thickness>
</Page.Resources>
```

# Remarks
<!-- Explanation and guidance that doesn't fit into the Examples
section.  For example, see the Remarks for the MediaPlayerElement
(https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaPlayerElement#remarks). -->
Much like the relationship between our [System Colors](https://docs.microsoft.com/en-us/windows/uwp/design/style/color) and our [Theme Brushes](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/xaml-theme-resources#the-xaml-color-ramp-and-theme-dependent-brushes), there are two *system* [Lightweight styling](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/xaml-styles#lightweight-styling) resources that control at a high-level the general corner roundness of controls (e.g. buttons and sliders) and overlays (e.g. flyouts and popups), and then there are control-specific Lightweight styling resources that allow customizing the corner radius on a per-control basis.

Depending on the granularity desired by the app-author, they can override either or both global and control-specific Lightweight resources depending, with the control-specific resources taking priority over the global ones when both specified.

```xml
<Page.Resources>
  <Thickness x:Key="ControlThemeCornerRadius">2</Thickness>
  <Thickness x:Key="ButtonThemeCornerRadius">10</Thickness>
</Page.Resources>

<Grid>
  <StackPanel>
    <ProgressBar Maximum="100" Value="30" Width="250" Height="10"/>
    <ToggleSwitch ToolTipService.ToolTip="Toggle Switch"/>
    <Button Content="My Button"/>
  </StackPanel>
</Grid>
```

![Mixed resources cornerradius values](images\CornerRoundnessExample.png)

In this example, all controls' border radii have been set to two via the ``ControlThemeCornerRadius`` global resource, and the Button is honoring the control-specific reource ``ButtonThemeCornerRadius``.

# API Notes
<!-- Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. -->

|Global Lightweight resource| Default value |
|:-|:-:|
| ControlThemeCornerRadius | 3 |
| OverlayThemeCornerRadius | 3 |

## Affected controls
Below is a list of control-specific [Lightweight styling](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/xaml-styles#lightweight-styling) resources that are getting updated and tied to the global Lightweight resources mentioned above.

This list also covers which part or parts of a certain control's template will get this new CornerRadius value applied, as some templates have multiple places where a CornerRadius attribute must be changed.

### ControlThemeCornerRadius
The following table details out the controls who's values corresponds to the global CornerRadius value for ``ControlThemeCornerRadius``.

|#| Control | Lightweight resource(s) | Affected template attribute/style |
|:-:|:-|:-|:-|
|1| Button | ButtonThemeCornerRadius | ContentPresenter |
|2| Checkbox | CheckboxThemeCornerRadius | RootGrid (Grid), NormalRectangle (Checkbox Box Rectangle) |
|3| TextBox | TextBoxThemeCornerRadius | BorderElement (Border) |
|4| AutoSuggestBox | AutoSuggestBoxThemeCornerRadius | AutoSuggestBoxTextBoxStyle (CornerRadius Property definition) |
|5| Slider | SliderThemeCornerRadius | HorizontalTrackRect, HorizontalDecreaseRect, VerticalTrackRect, VerticalDecreaseRect (All Rectangles) |
|6| ComboBox | ComboBoxThemeCornerRadius | CornerRadius Property (Setter in Style) |
|7| DatePicker | DatePickerThemeCornerRadius | CornerRadius Property (Setter in Style) |
|8| TimePicker | TimePickerThemeCornerRadius | CornerRadius Property (Setter in Style) |
|9| CalendarDatePicker | CalendarDatePickerThemeCornerRadius | CornerRadius Property (Setter in Style) |
|10| ProgressBar | ProgressBarThemeCornerRadius | DeterminateRoot (Border) ProgressBarIndicator (Rectangle) |
|11| RichEditBox | RichEditBoxThemeCornerRadius | CornerRadius Property (Setter in Style) |
|12| ToggleButton | ToggleButtonThemeCornerRadius | ContentPresenter |
|13| ToggleSwitch | ToggleSwitchThemeCornerRadius | OuterBorder (Rectangle), SwitchKnobBounds (Rectangle) |
|14| SplitButton | SplitButtonThemeCornerRadius | ContentPresenter |
|15| DropDownButton | DropDownButtonThemeCornerRadius | ContentPresenter |

### OverlayThemeCornerRadius
The following table details out the controls who's values corresponds to the global CornerRadius value for ``OverlayThemeCornerRadius``.

|#| Control | Lightweight resource(s) | Affected template attribute/style |
|:-:|:-|:-|:-|
|1| ContentDialog | n/a | n/a |
|2| Flyout | n/a | n/a |
|3| Popup | n/a | n/a |
|4| ComboBox Dropdown | n/a | n/a |
|5| DatePicker Flyout | DatePickerFlyoutThemeCornerRadius | DatePickerFlyoutPresenter (CornerRadius property) |
|6| TimePicker Flyout | TimePickerFlyoutThemeCornerRadius | TimePickerFlyoutPresenter (CornerRadius property) |
|7| MenuFlyout | MenuFlyoutThemeCornerRadius | MenuFlyoutPresenter (DefaultMenuFlyoutPresenterStyle CornerRadius propery) |
|8| CommandBarFlyout | n/a | n/a |
|9| SplitButtonFlyout | n/a | n/a |
|10| Tooltip | ToolTipThemeCornerRadius| LayoutRoot (ContentPresenter CornerRadius property) |
|11| AutoSuggestBox Popup | AutoSuggestBoxSuggestionsContainerThemeCornerRadius | SuggestionsContainer (Border) |
|12| DropDownButton Dropdown | n/a | n/a |
|13| SplitButton Flyout | n/a | n/a |

## CornerRadiusResources API (P2)
Another step further we can take these new resource additions, is following the same model that we introduced in RS5 of the [ColorSchemeResources](https://docs.microsoft.com/en-us/windows/uwp/design/style/color#how-to-use-colorschemeresources).

On this API, we would introduce a property for each control-specific CornerRadius Lightweight styling resource.

```xml
<Grid.Resources>
    <CornerRadiusResources
      MenuFlyout="2"
      ComboBox="5"
      ProgressBar="3"/>
</Grid.Resources>
```

# API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->

# Appendix
<!-- Anything else that you want to write down for posterity, but
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->

# Open Questions
