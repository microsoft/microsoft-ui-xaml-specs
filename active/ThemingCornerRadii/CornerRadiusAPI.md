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
The ``CornerRadius`` property for controls and their templates can be easily customized/themed through the introduction of new global [Lightweight styling](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/xaml-styles#lightweight-styling) resources, and through the improvements to the ``CornerRadius`` property on controls being rounded, for more granular, per-control edge rounding.

# Examples
The following examples cover how to use the global and control-specific property to round the corners of most controls.

## Setting the CornerRadius of controls
There are two primary ways to set the corner radius' of controls, depending on the scope and level of granularity desired when customizing/setting the corners:

- Through changing the global corner radius resources
- Through explicit or implicit styling and the ``CornerRadius`` property on the control element

### Global CornerRadius changes
To make large changes globally and consistently to all controls within a scope, defining or overriding the ``ControlCornerRadius`` and ``OverlayCornerRadius`` resources from generic.xaml will affect all controls that benefit from rounded edges.

``` xml
<Application.Resources>
  <Thickness x:Key="ControlCornerRadius">2</Thickness>
  <Thickness x:Key="OverlayCornerRadius">4</Thickness>
</Application.Resources>
```

### Targetted CornerRadius changes
To make more granular, or targetted CornerRadius changes to a control that benefits from the new rounded edges, you can do so by setting the ``CornerRadius`` property. This can be done either through an implicit style (which can be scoped), or via the ``CornerRadius`` property on the control itself.

#### Implicit CornerRadius styling
``` xml
<Style TargetType="Button">
  <Setter Property="CornerRadius" Value="6"/>
</Style>
```

#### Explicit CornerRadius styling
``` xml
<Button CornerRadius="6" Content="My Rounded Button"/>
```

Depending on the control you set the ``CornerRadius`` property on, it will effect it differently. If you want to edit the radiis more individually (e.g. the ProgressBar's fill roundness versus ProgressBar's background roundess), consider using the control's template.

# Remarks
<!-- Explanation and guidance that doesn't fit into the Examples
section.  For example, see the Remarks for the MediaPlayerElement
(https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaPlayerElement#remarks). -->


# API Notes
<!-- Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. -->

|Global Lightweight resource| Default value |
|:-|:-:|
| ControlCornerRadius | 2 |
| OverlayCornerRadius | 4 |

## Affected controls
>Below is a list of controls who's ``CornerRadius`` propteries are getting either bound to ``ControlCornerRadius`` or ``OverlayCornerRadius``, with a column indicating which attributes of those particular controls' template will get rounded, as some control templates have multiple places where their CornerRadius property must be applied.

### ControlCornerRadius
The following table details out the controls who's ``CornerRadius`` properties will be bound to the global ``ControlCornerRadius`` resource.

|#| Control | Affected template attribute/style |
|:-:|:-|:-|
|1| Button | CornerRadius Property (Setter in Style) |
|2| Checkbox | RootGrid (Grid), NormalRectangle (Checkbox Box Rectangle) |
|3| TextBox | BorderElement (Border) |
|4| AutoSuggestBox | AutoSuggestBoxTextBoxStyle (CornerRadius Property definition) |
|5| Slider | HorizontalTrackRect, HorizontalDecreaseRect, VerticalTrackRect, VerticalDecreaseRect (All Rectangles) |
|6| ComboBox | CornerRadius Property (Setter in Style) |
|7| DatePicker | CornerRadius Property (Setter in Style) |
|8| TimePicker | CornerRadius Property (Setter in Style) |
|9| CalendarDatePicker | CornerRadius Property (Setter in Style) |
|10| ProgressBar | DeterminateRoot (Fill, is a Border element) ProgressBarIndicator (Background, is a Rectangle element) |
|11| RichEditBox | CornerRadius Property (Setter in Style) |
|12| ToggleButton | CornerRadius Property (Setter in Style) |
|13| SplitButton | CornerRadius Property (Setter in Style) |
|14| DropDownButton | CornerRadius Property (Setter in Style) |
|15| PasswordBox | CornerRadius Property (Setter in Style) |
|16| RichEditBox | CornerRadius Property (Setter in Style) |
|17| Tab Control | *tbd* |
|18| ToggleSplitButton | CornerRadius Property (Setter in Style) *Top,Bottom only on one side for each button "split"* |
|19| FlipView | *tbd* |
|20| GridView | *tbd* |
|21| ListView | *tbd* |
|22| TreeView | *tbd* |

#### Control "Bars"
The following controls receiving rounded corners don't back backplates that can round per-say, but instead have visual components (dubbed "bars") that indicate selection or state that will be getting rounded.

These "bars" are bound to the global ``ControlCornerRadius`` resource.

|#| Control | Affected template attribute/style |
|:-:|:-|:-|
|1| NavigationView | NavigationViewItemPresenter *(Top and Left style)* SelectionIndicator (Rectangle) |
|2| Pivot | FocusFollower (Rectangle) |
|3| ScrollIndicator | *tbd* |
|4| ColorPicker | *tbd* |
|5| MediaPlayerElement | *tbd* |

### OverlayCornerRadius
The following table details out the controls who's ``CornerRadius`` properties will be bound to the global ``OverlayCornerRadius`` resource.

|#| Control | Affected template attribute/style |
|:-:|:-|:-|
|1| ContentDialog | CornerRadius Property (Setter in Style) |
|2| Flyout | CornerRadius Property (Setter in Style) |
|3| ComboBox Popup | PopupBorder (Border) |
|4| DatePicker Flyout | DatePickerFlyoutPresenter (CornerRadius property) |
|5| TimePicker Flyout | TimePickerFlyoutPresenter (CornerRadius property) |
|6| MenuFlyout | MenuFlyoutPresenter (DefaultMenuFlyoutPresenterStyle CornerRadius propery) |
|7| CommandBar Overflow | OverflowContentRoot (Grid) |
|8| Tooltip | LayoutRoot (ContentPresenter CornerRadius property) |
|9| AutoSuggestBox Popup | SuggestionsContainer (Border) |
|10| ToggleSplitButton Dropdown | *tbd* |
|11| MenuBar Flyout | *tbd* |
|12| TeachingTip | *tbd* |
|13| CalendarDatePicker | FlyoutPresenter CornerRadius (Setter in AttachedFlyout Style) |
|14| CommandBarFlyout | *tbd* |

# Open Questions

- Some controls' templates use the ``Rectangle`` primitive, which do not have a ``CornerRadius`` property, but instead have ``RadiusX`` and ``RadiusY`` that achieve the same effect. This means if the app-developer sets the ``CornerRadius`` property on the control and that control's only rounding happens on a ``Rectangle`` primitive, some translation will be required between the ``CornerRadius`` property values to the ``Rectangle``'s ``RadiusX`` and ``RadiusY`` values.
   - Open question issue [filed here](https://github.com/microsoft/microsoft-ui-xaml/issues/755)
- We need to test the interitance/tree walks with the corner radius values to ensure that all corner radius properties *will indeed* be affected and follow the two new global corner radius resources being introduced.
- There is currently no good/easy way to get telemetry data from resource additions like this. For now we are okay with this, but in WinUI3.0, there may be a better way to track work like this.
