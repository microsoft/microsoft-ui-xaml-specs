

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
Certain controls within UWP XAML already have rounded corners, while GridView and ListView do not. This creates a strong visual inconsistency in which certain parts of a WinUI app follow a modern design language and certain parts contrast that by looking dated. 

This spec will detail a number of changes to update the designs of ListView and GridView so that they are more visually aligned with modern WinUI controls such as NavigationView. At a high level, some key parts of these updated styles are: rounded corners, a new selection indicator, rounded checkboxes and new checkbox design for multiple selection, and an improved border design for GridView. The spec will also go over the API changes necessary to support these new designs.

# Description
<!-- Use this section to provide a brief description of the feature.
For an example, see the introduction to the PasswordBox control 
(http://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box). -->
This section will provide mockups and further design details.

**GridView in single selection mode:**

The first item in the second row under "New" is hovered; the third item is  selected. 
![visual-updates1.PNG](visual-updates1.PNG)

**GridView in multiple selection mode:**
![visual-updates2.PNG](visual-updates2.PNG)

**ListView in single selection mode:**
![visual-updates3.PNG](visual-updates3.PNG)

**ListView in multiple selection mode:**
![visual-updates4.PNG](visual-updates4.PNG)

**More ListView examples:**

The first example on the top left shows a rounded focus rect.
The second example on the top right shows extended selection mode. 

The samples in the bottom row show ListView items with different heights. 
![visual-updates-5.PNG](visual-updates-5.PNG)

### Selection Indicator specifics
The selection indicator should be centered with a fixed margin on the top and bottom, and a minimum height of 16px. With this system, items with a line height of one or two will have the same size pill (minimum size pill), but it will grow larger as the line height increases. 

## Detailed Task Breakdown for styling implementation

1. GridView visual improvements

    a. Items have rounded corners at rest.

    b. Items have a new rounded inner white border when in selected states (including multiple and single selection modes).

    c. In all non-rest states, items have a rounded grey backplate. For Selected and Pressed states, backplate slightly darker than hover backplate. 

    d. In hover state specifically: Items have a rounded, light grey border and a backplate with a light grey fill (same color as ListView).

    e. Existing accent color border that’s displayed in selected states will have rounded corners to match items.

    f. Focus rect will need to be rounded to adhere to shape of backplate.

2. ListView visual improvement

    a. A rounded gray backplate will be added within the footprint of the item, with 2px top/bottom  and 4px left/right margin from the overall footprint on all sides. This backplate will appear in all non-rest states.

    b. An accent color selection indicator visual will appear left-aligned to the backplate with no left margin when an item is selected/clicked in any state. 

    c. Items will need a 2px top/bottom margin to be distinct from one another. This margin should be taken from the backplate. The overall footprint of item shouldn’t change. 

    d. Focus rect will need to be rounded to adhere to backplate rather than overall footprint.

    e. APIs will be added to support the new selection indicator and backplate. See API notes for more details.

3. Checkbox visual changes for ListView and GridView

    a. Round and update checkbox visuals used for multiple selection mode in ListView and GridView so that they match the current styling and behavior of CheckBox control (in terms of color, font, shape, animation).

    b. GridView checkbox should have rounded corners a dark grey border.

    c. On pressed and disabled states, GridView checkbox border color lightens. 

4. Inset backplate interaction for ListView controls

    a. ListView items will now have an inset rounded backplate that is smaller than their overall footprint. The overall footprint should still be clickable and have the same interaction as if you’d clicked on the inset backplate.

5. Add animation for selection indicator

    a. Add animations for reveal/dismissal of selection indicator for ListView controls. The animation will consist of the indicator going from invisible -> small -> full size upon reveal, and full size -> smaller -> invisible upon dismissal. This animation will apply to both single and extended selection modes if applicable.

6. Miscellaneous

    a. Implement global resources that allow the developer to un-round any/all GridView or ListView item corners, similar to the ControlCornerRadius resource that currently exists but does not directly affect ListView/GridViewItems.

    b. If the developer chooses to un-round corners, borders on selected GridView items should be un-rounded as well. Checkboxes within GridViewItems in multiple selection mode should be inset 1px from their original location in this scenario. Focus rects for ListView and GridView  should be un-rounded.

# API Notes

<!-- Option 1: Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. For properties, specify the default value of the property if it
isn't the type's default (for example an int-typed property that doesn't default to zero.) -->

<!-- Option 2: Put these descriptions in the below API Details section,
with a "///" comment above the member or type. -->

## New APIs:

`public boolean ListViewItemPresenter.SelectionIndicatorVisualEnabled { get; set; }`

Gets or sets a value that indicates whether the selection indicator should appear when ListView items are selected.

`public Windows.UI.Xaml.CornerRadius ListViewItemPresenter.SelectionIndicatorCornerRadius { get; set; }`

Gets or sets the corner radius value for the selection indicator.

`public Windows.UI.Xaml.CornerRadius ListViewItemPresenter.CheckBoxCornerRadius { get; set; }`

Gets or sets the corner radius value for the checkbox shown in multiple selection mode.

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.CheckBoxDisabledBrush { get; set; }`

Gets or sets the brush used to render the check box background of disabled items.

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.CheckBoxSelectedBrush { get; set; }`

Gets or sets the brush used to render the check box background of selected items. 

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.CheckBoxSelectedPointerOverBrush { get; set; }`

Gets or sets the brush used to render the check box background of selected pointer-over items. 

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.CheckBoxSelectedPressedBrush { get; set; }`

Gets or sets the brush used to render the check box background of selected pressed items. 

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.CheckBoxSelectedDisabledBrush { get; set; }`

Gets or sets the brush used to render the check box background of selected disabled items. 

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.CheckBoxBorderBrush { get; set; }`

Gets or sets the brush used to render the check box border of ListView items. 

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.CheckBoxPointerOverBorderBrush { get; set; }`

Gets or sets the brush used to render the check box border of pointer-over ListView items. 

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.CheckBoxPressedBorderBrush { get; set; }`

Gets or sets the brush used to render the check box border of pressed ListView items. 

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.CheckBoxDisabledBorderBrush { get; set; }`

Gets or sets the brush used to render the check box border of disabled ListView items. 

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.SelectedDisabledBackground { get; set; }`

Gets or sets the brush used to render the background of selected disabled ListView items.

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.SelectionIndicatorBrush { get; set; }`

Gets or sets the brush used to render the selection indicator shown on selected ListView items. 

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.SelectionIndicatorPointerOverBrush { get; set; }`

Gets or sets the brush used to render the selection indicator shown on a selected ListViewItem that has the pointer over it. 

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.SelectionIndicatorPressedBrush { get; set; }`

Gets or sets the brush used to render the selection indicator shown on a selected ListViewItem that is being pressed. 

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.SelectionIndicatorDisabledBrush { get; set; }`

Gets or sets the brush used to render the selection indicator shown on a selected disabled ListViewItem. 

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.SelectionIndicatorVisualEnabledProperty{ get; };`

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.SelectedDisabledBackgroundProperty{ get; };`

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.SelectionIndicatorBrushProperty{ get; };`

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.SelectionIndicatorPointerOverBrushProperty{ get; };`

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.SelectionIndicatorPressedBrushProperty{ get; };`

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.SelectionIndicatorDisabledBrushProperty{ get; };`

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.SelectionIndicatorModeProperty{ get; };`

`public ListViewItemPresenterSelectionIndicatorMode ListViewItemPresenter.SelectionIndicatorMode { get; set; }`

Specifies the alignment/style of the selection indicator that appears when ListView items are selected.

`public enum ListViewItemPresenterSelectionIndicatorMode`

Defines constants that specify the alignment/style of the selection indicator that appears when ListView items are selected.

Fields:

| Field  | Value | 
|--------|:-----:|
|   0 - Inline    |  Indicator visual appears inline with the ListViewItem content, aligned left by default.     | 
|   1 - Overlay  |   Indicator visual shows over the ListViewItem content, aligned left by default.    |


<br/>

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.PointerOverBorderBrush { get; set; }`

Gets or sets the brush used to render the outer border shown on a GridViewItem that has the pointer over it. 

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.SelectedBorderBrush { get; set; }`

Gets or sets the brush used to render the outer selection border shown on selected GridView items. The existing SelectedPointerOverBorderBrush brush is used when the pointer is over the item. The thickness is driven by SelectedBorderThickness.

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.SelectedPressedBorderBrush { get; set; }`

Gets or sets the brush used to render the outer selection border shown on selected & pressed GridView items.

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.SelectedDisabledBorderBrush { get; set; }`

Gets or sets the brush used to render the outer selection border shown on selected & disabled GridView items.

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.SelectedInnerBorderBrush { get; set; }`

Gets or sets the brush used to render the inner selection border shown on selected GridView items. The thickness is hard-coded to 1px.

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.CheckBoxPointerOverBrush { get; set; }`

Gets or sets the brush used to render the pointer-over selection check box.

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.CheckBoxPressedBrush { get; set; }`

Gets or sets the brush used to render the pressed selection check box.

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.CheckPressedBrush { get; set; }`

Gets or sets the brush used to render the pressed selection checkmark.

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.CheckDisabledBrush { get; set; }`

Gets or sets the brush used to render the disabled selection checkmark.

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.PointerOverBorderBrushProperty{ get; };`

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.SelectedBorderBrushProperty{ get; };`

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.SelectedPressedBorderBrushProperty{ get; };`

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.SelectedDisabledBorderBrushProperty{ get; };`

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.CheckBoxPointerOverBrushProperty{ get; };`

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.CheckBoxPressedBrushProperty{ get; };` 

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.CheckBoxDisabledBrushProperty{ get; };` 

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.CheckBoxSelectedBrushProperty{ get; };` 

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.CheckBoxSelectedPointerOverBrushProperty{ get; };` 

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.CheckBoxSelectedPressedBrushProperty{ get; };` 

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.CheckBoxSelectedDisabledBrushProperty{ get; };` 

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.CheckBoxBorderBrushProperty{ get; };`

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.CheckBoxPointerOverBorderBrushProperty{ get; };` 

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.CheckBoxPressedBorderBrushProperty{ get; };` 

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.CheckBoxDisabledBorderBrushProperty{ get; };` 

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.CheckPressedBrushProperty{ get; };` 

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.CheckDisabledBrushProperty{ get; };` 

`static Windows.UI.Xaml.DependencyProperty ListViewItemPresenter.SelectedInnerBorderBrushProperty{ get; };`


## New Visual State Group and States:

### For reference, existing visual group and states for the SelectionMode==Multiple option: 

**VisualStateGroup MultiSelectStates**

VisualState MultiSelectDisabled  for selectionMode != Multiple

VisualState MultiSelectEnabled   for selectionMode == Multiple


### New visual group and states for the Selection==Single or Extended options: 

**VisualStateGroup SelectionIndicatorStates**

VisualState SelectionIndicatorDisabled  for selectionMode == None or Multiple

VisualState SelectionIndicatorEnabled   for selectionMode == Single or Extended

## Changes to existing APIs:
All of the following APIs will affect the rounded backplate of the item, rather than the overall footprint as they did prior. 

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.PointerOverBackground { get; set; }`

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.SelectedBackground { get; set; }`

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.SelectedPointerOverBackground { get; set; }`

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.PressedBackground { get; set; }`

`public Windows.UI.Xaml.Media.Brush ListViewItemPresenter.SelectedPressedBackground { get; set; }`

## New ThemeResources

### ListView:

```xml
<ListViewItemPresenterSelectionIndicatorMode x:Key="ListViewItemSelectionIndicatorMode">Inline</ListViewItemPresenterSelectionIndicatorMode>

<x:Boolean x:Key="ListViewItemSelectionIndicatorVisualEnabled">True</x:Boolean>

<StaticResource x:Key="ListViewItemSelectionIndicatorDisabledBrush" ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="ListViewItemSelectionIndicatorPressedBrush" ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="ListViewItemSelectionIndicatorPointerOverBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="ListViewItemSelectionIndicatorBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="ListViewItemCheckBoxDisabledBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="ListViewItemCheckBoxSelectedBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="ListViewItemCheckBoxSelectedPointerOverBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="ListViewItemCheckBoxSelectedPressedBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="ListViewItemCheckBoxSelectedDisabledBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="ListViewItemCheckBoxBorderBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="ListViewItemCheckBoxPointerOverBorderBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="ListViewItemCheckBoxPressedBorderBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="ListViewItemCheckBoxDisabledBorderBrush"  ResourceKey="SystemAccentColor"/> 

<CornerRadius x:Key="ListViewItemSelectionIndicatorCornerRadius">2</CornerRadius>

<CornerRadius x:Key="ListViewItemCheckBoxCornerRadius">3</CornerRadius>

```

### GridView:

```xml
<StaticResource x:Key="GridViewItemBackgroundSelectedDisabled"  ResourceKey="SystemAccentColor"/> 

<StaticResource x:Key="GridViewItemSelectedBorderBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="GridViewItemSelectedPointerOverBorderBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="GridViewItemSelectedPressedBorderBrush"  ResourceKey="SystemAccentColor"/> 

<StaticResource x:Key="GridViewItemSelectedDisabledBorderBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="GridViewItemCheckBoxDisabledBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="GridViewItemCheckBoxSelectedBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="GridViewItemCheckBoxSelectedPointerOverBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="GridViewItemCheckBoxSelectedPressedBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="GridViewItemCheckBoxSelectedDisabledBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="GridViewItemCheckBoxBorderBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="GridViewItemCheckBoxPointerOverBorderBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="GridViewItemCheckBoxPressedBorderBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="GridViewItemCheckBoxDisabledBorderBrush"  ResourceKey="SystemAccentColor"/>

<StaticResource x:Key="GridViewItemSelectedInnerBorderBrush"  ResourceKey="SystemAccentColor"/>

<CornerRadius x:Key="GridViewItemCheckBoxCornerRadius">3</CornerRadius>
```
