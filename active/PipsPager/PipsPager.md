# Background

In many applications, media and data is shown in various layouts like lists, grids, and tables.
Instead of scrolling or panning through the content,
users are able to page through the content with previous and next buttons or jump to specific pages. 

This UI pagination behavior is supported in WinUI currently with the PagerControl. 
(See PagerControl [spec](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/savoyschuler/pagercontrol/active/PagerControl/PagerControl.md) 
and PagerControl [proposal](https://github.com/microsoft/microsoft-ui-xaml/issues/60) 
for the latest updates.) The PagerControl works with page numbers in a few different formats:

![PagerControl example](images/PagerControl.jpg)

A common UI pattern not yet supported by the existing PagerControl visualizations appears in
media viewer scenarios like photo carousels or app lists.
In these scenarios, the pages are commonly indicated with 'dot' glyphs instead of
numbers to encourage discovery of content and reduce unneccessary UI elements on the page.
Conceptually, this is similar to an unordered list represented by bullet points instead of numbers. 

![A sketch of five horizontal dots with the first selected and the previous and next buttons are visible](images/PipsPager_Visible.gif)

The new PipsPager control can  be used when the page content is not explicitly ordered and
a symbol or glyph can instead represent the page number.

# API Pages

## PipsPager class

Represents a control that provides a interaction and navigation of a paginated layout view 
(ListView, GridView, ItemsRepeater, DataGrid, etc.) when the page numbers do not need to be visually known.

The PipsPager, like the other pager controls, is independent of the layout views and
does not control any of the data being displayed in the layout view.

### Is this the right control?

Use a PipsPager when the content in the layout is not explicitly ordered by relevancy or 
you desire a glyph-based representation of numbered pages.
This UI is commonly used in photo viewers, app lists, and when display space is limited.
The PipsPager can be oriented vertically or horizontally.

### What is a pip?

Pips represent a unit of numerical value, typically represented as dots, however can be customized to other glyphs like dashes or squares.
In the PipsPager control by default, a solid dot will represent each page of the layout. 
They can be selected by the user to navigate to a specific page.

### Examples

#### Create a PipsPager

A PipsPager with five visible pips that can be selected by the user. 
By default, the previous and next buttons are collapsed, the pips are oriented horizontally, and the total number of pages is infinite.

![A prototype of five horizontal dots with the first selected.](images/PipsPager_Default.png)

XAML
```XAML 
<muxc:PipsPager x:Name="DefaultPipsPager" />
```

#### PipsPager with visible 

The visibility of the previous and next buttons can be set via the
`PreviousButtonVisibility` and `NextButtonVisibility` properties. 
These buttons allow the user to select a page. The possible values are:
- Visible: The button is visible and selectable. An exception to this is that if the selected page is on the
associated "edge" of the button then the button will be hidden.
  - The edge for the previous button is the first page and the edge for the next button is the last page.
  - Note: Hidden means that the button is invisible but does take up layout space.
- VisibleOnPointerOver: The behavior is the same as Visible *except* that the button is only Visible when
the user is hovering over the paging UI with their cursor.
- Collapsed: The button is not visible to the user and does **not** take up layout space.

![A sketch of five horizontal dots with the first selected and the previous and next buttons are visible](images/PipsPager_Visible.gif)

XAML
```XAML 
<muxc:PipsPager x:Name="VisibleButtonPipsPager"
  NumberOfPages="5"
  PreviousButtonVisibility=Visible
  NextButtonVisibility=Visible />
```

#### Vertical PipsPager with VisibleOnPointerOver button visibility

The PipsPager can also be oriented vertically which does not change its behavior or interaction.
The various button visibility modes can apply to either orientation.
The below example highlights the VisibleOnPointerOver button visibility. This button visibility 

![A prototype of five vertical dots with the second initially selected and the previous and next buttons appearing and disappearing based on user hover and the selected page](images/PipsPager_VisibleOnPointerOver.gif)

XAML
```XAML 
<muxc:PipsPager x:Name="VerticalPipsPager"
  NumberOfPages="5"
  Orientation=Vertical 
  PreviousButtonVisibility=VisibleOnPointerOver 
  NextButtonVisibility= VisibleOnPointerOver />
```

#### Number of pages displayed less than total

If the number of pages of content is large and do not need to be navigated to at once,
the MaxVisiblePips property can be set to limit the number of visible, interactable, pips.
If the NumberOfPages is greater than the MaxVisiblePips then the pips will scroll so that the selected page is
centered in the control. If the NumberOfPages is equal to or less than the MaxVisiblePips,
no scrolling will occur and the number of pips shown will be the same as the NumberOfPages.

By default, the number of visible pips is 5.

![A prototype of twelve horizontal dots with the first initially selected and the dots scrolling when the subsequent pages are selected](images/PipsPager_MaxVisiblePips.gif)

XAML
```XAML
<muxc:PipsPager x:Name="ScrollingPipsPager"
  NumberOfPages="20"
  MaxVisiblePips="10" />
```

#### Integrate PipsPager with a Collection control

Commonly, a PipsPager is used in conjunction with a Collection control.
The below example shows how to bind a PipsPager with a FlipView to provide
an extra way to navigate through content and indicate its page.

Note: To use the PipsPager as a page indicator **only**,
set IsEnabled to false in the control to disable user interaction.

![A prototype of five dots, the PipsPager, laid out horizontally underneath a FlipView showing a picture of a bicycle. The third dot in the PipsPager is selected indicated the 3rd page of content.](images/PipsPager_FlipViewExample.png)

XAML
``` XAML
<StackPanel>
  <FlipView x:Name="Gallery" MaxWidth="400" Height="270" ItemsSource="{x:Bind Pictures}">
      <FlipView.ItemTemplate>
          <DataTemplate x:DataType="x:String">
              <Image Source="{x:Bind Mode=OneWay}"/>
          </DataTemplate>
      </FlipView.ItemTemplate>
  </FlipView>

  <muxc:PipsPager x:Name="FlipViewPipsPager"
      HorizontalAlignment="Center"
      Margin="0, 10, 0, 0"
      NumberOfPages="{x:Bind Pictures.Count}"
      SelectedPageIndex="{x:Bind Path=Gallery.SelectedIndex, Mode=TwoWay}" />
</StackPanel>
```
#### Pip and Button customization

The buttons and pips can be customized via the PreviousButtonStyle, NextButtonStyle, SelectedPipStyle, and DefaultPipStyle.
As a note, setting the PreviousButtonStyle and NextButtonStyle can overwrite what is set in PreviousButtonVisibility andNextButtonVisibility.
Additionally, the SelectedPipStyle and DefaultPipStyle can overwrite what is set in the theme resources.

![A prototype of five dots, the PipsPager, laid out horizontally with caret-style navigational arrows](images/PipsPager_CustomNavButtons.png)

XAML
``` XAML
<Page.Resources>
    <Style x:Key="NavButtonBaseStyle" TargetType="Button" BasedOn="{StaticResource NavigatonButtonBaseStyle}">
        <Setter Property="Width" Value="30" />
        <Setter Property="Height" Value="30" />
        <Setter Property="FontSize" Value="12" />
    </Style>

    <Style x:Key="PreviousButtonStyle" BasedOn="{StaticResource NavButtonBaseStyle}" TargetType="Button">
        <Setter Property="Content" Value="&#xEDDB;" />
    </Style>

    <Style x:Key="NextButtonStyle" BasedOn="{StaticResource NavButtonBaseStyle}" TargetType="Button">
        <Setter Property="Content" Value="&#xEDDC;" />
    </Style>
</Page.Resources>

<muxc:PipsPager x:Name="CustomNavButtonPipsPager"
  PreviousButtonStyle="{StaticResource PreviousButtonStyle}"
  NextButtonStyle="{StaticResource NextButtonStyle}" />
```
## PipsPager Theme Resources

> Note: Some are TBD.

| Name | Description | Type
|:-:|:--| :-- |
| PipsPagerDefaultIndicator | Sets the default pip glyph in MDL2 icon set | String |
| PipsPagerSelectedIndicator | Sets the pip glyph when selected in MDL2 icon set | String |
| PipsPagerDefaultIndicatorFontSize | Sets the size in pixels of the pip glyph | Double |
| PipsPagerSelectedIndicatorFontSize | Sets the size in pixels of the pip glyph when selected | Double |
| PipsPagerDefaultForegroundBrush | Sets the foreground brush of the default pip glyphs| SolidColorBrush
| PipsPagerSelectedForegroundBrush | Sets the foreground brush of the pip glyph when selected | SolidColorBrush
| PipsPagerPointerOver | Sets the pointer over/hover visual state for the pip display mode | Brush
| PipsPagerPressed | Sets the pressed visual state for the pip display mode | Brush
| PipsPagerSelected | Sets the selected visual state for the pip display mode | Brush
| PipsPagerMargin | Sets the margin, in pixels, between each pip glyph | Thickness
| PipsPagerIndicatorWidth | Sets the bounding box width for each pip glyph | Double
| PipsPagerIndicatorHeight | Sets the bounding box height for each pip glyph | Double


## PipsPager member notes

| Name | Description| Default | 
|:---:|:---|:---|
| NumberOfPages | Sets the max number of pages the index control will iterate through. The default will represent an infinite page range. | -1
| Previous and Next Style | Give the developer the option to customize the style by changing the text or glyph for the edge buttons. | N/A
| SelectedPageIndex | The 0 based index that is currently selected. It will default to the first index. | 0
| MaxVisiblePips | The maximum number of pips that will appear in the control at once. If the NumberOfPages is greater than this value, the UI will scroll so that the selected pip is centered. | 5
| Orientation | The orientation of the control. Can be either Vertical or Horizontal. | Horizontal
| PipsPagerSelectedIndexChangedEvent | Event that is fired after the user selects a pip or the directional buttons. This event will return the index number of the page that the user selected. | N/A

## PipsPagerButtonVisibility enum

Defines constants that specify how a the buttons of a PipsPager are to be displayed. Defaults to Collapsed.

| Value | Description |
|:---:|:---|
| Visible | The button is visible and selectable. An exception to this is that if the selected page is on the associated "edge" of the button then the button will be hidden |
| VisibleOnPointerOver | The behavior is the same as Visible *except* that the button is only Visible when the user is hovering over the paging UI with their cursor. |
| Collapsed | The button is not visible to the user and does **not** take up layout space. |


# API Details

```IDL
enum PipsPagerButtonVisibility
{
  Visible,
  VisibleOnPointerOver,
  Collapsed, 
}

runtimeclass PipsPagerSelectedIndexChangedEventArgs
{
    Int32 NewPageIndex{get; };
    Int32 PreviousPageIndex{get; };
};

unsealed runtimeclass PipsPagerTemplateSettings : Windows.UI.Xaml.DependencyObject
{
    Windows.Foundation.Collections.IVector<Object> PipsPagerItems { get; }; 
}


unsealed runtimeclass PipsPager : Windows.UI.Xaml.Controls.Control
{
    PipsPager();

    [MUX_DEFAULT_VALUE("-1")]
    Int32 NumberOfPages;

    [MUX_DEFAULT_VALUE("0")]
    Int32 SelectedPageIndex;

    [MUX_DEFAULT_VALUE("5")]
    Int32 MaxVisiblePips;

    [MUX_DEFAULT_VALUE("Horizontal")]
    Windows.UI.Xaml.Controls.Orientation Orientation;

    [MUX_DEFAULT_VALUE("Collapsed")]
    PagerControlButtonVisibility PreviousButtonVisibility;    
    [MUX_DEFAULT_VALUE("Collapsed")]
    PagerControlButtonVisibility NextButtonVisibility;

    Windows.UI.Xaml.Style PreviousButtonStyle;
    Windows.UI.Xaml.Style NextButtonStyle; 

    Windows.UI.Xaml.Style SelectedPipStyle;
    Windows.UI.Xaml.Style DefaultPipStyle;


    event Windows.Foundation.TypedEventHandler<PipsPager, PipsPagerSelectedIndexChangedEventArgs> SelectedIndexChanged;

    PipsPagerTemplateSettings TemplateSettings{ get; };

    static Windows.UI.Xaml.DependencyProperty NumberOfPagesProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty SelectedPageIndexProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty MaxVisiblePipsProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty OrientationProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty PreviousButtonVisibilityProperty { get; };
    static Windows.UI.Xaml.DependencyProperty NextButtonVisibilityProperty { get; };
    static Windows.UI.Xaml.DependencyProperty PreviousButtonStyleProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty NextButtonStyleProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty SelectedPipStyleProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty DefaultPipStyleProperty{ get; };
}
```

![](images/pager-control-vertical-pips-scroll-ex.jpg)


# Input & Accessibility

## Keyboard

- Tab navigation into the PipsPager proceeds through the first actionable item. This would be the previous button (if not Collapsed) or the first pip. Any directional buttons can be navigated to via tab if they are not Collapsed.
- The arrow keys can be used to navigate between the pips and directional buttons. Independent of the orientation set, the left and up arrow keys will change focus to the previous pip or to the previous button and the right and down arrow keys will change focus to the next pip or the next button.

## Gamepad

- The PipsPager can be navigated to via spatial navigation. The selected pip will automatically take focus and the pips will be selected when navigated to and focused. Independent of the PipsPagerButtonVisibility settings, the navigation buttons will not be visible to the user. 
  - In horizontal orientation left and right spatial navigation will focus and select the pips. The PipsPager will be navigated out of when the user navigates up or down or when the user attempts to navigate past the first or last page. We recommend that developers do not place UI directly to the left or right of a horizontally oriented PipsPager. 
  - In vertical orientation up and down spatial navigation will focus and select the pips. The PipsPager will be navigated out of when the user navigates left or right or when the user attempts to navigate past the first or last page. We recommend that developers do not place UI directly above or below a vertically oriented PipsPager. 

## Touch

For touch-optimized experiences, we recommend that developers integrate the PipsPager with a view control (i.e. FlipView, see guidance [above](#integrate-pipsPager-with-a-collection-control)) to take advantage of on-content pagination with touch.

The user can also touch to select the individual pip if able.

## Screen readers

- When the focus is on the control, the screen reader will announce "pager".
- When the focus is on the previous or next button the screen reader will announce "first page", "last page", "next page", or "previous page". 
  - If the buttons have text properties set by the developer, the screen reader will announce that text instead of the default announcement.
- When the focus is on a pip, the screen reader will announce "page x of y".
- When the pip is selected, the screen reader will announce "page x of y selected".


# Open Questions

- How can we optimize for touch input at this form factor?
  - Relatedly, would the PipsPager benefit from a 'Standard' visualization where the pips and navigational buttons increase in size and are touch accessible by default.
- Should selection follow focus in keyboard and Gamepad input?
