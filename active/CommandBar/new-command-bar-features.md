
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

This spec will address three main shortcomings in the [CommandBar (CB)](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.commandbar?view=winrt-19041) and [CommandBarFlyout (CBF)](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.commandbarflyout?view=winrt-19041) space, and propose solutions to these shortcomings. All of these solutions will lead to a more customizable, flexible commanding experience. 

A CommandBar provides access to app-level or page-specific commands. A CommandBarFlyout is a CommandBar that appears in a Flyout menu, and must be invoked.

*CommandBar:*
![CommandBar example](images/commandbar-example.png)

*CommandBarFlyout* (invoked for example with a mouse right click):

![CommandBarFlyout example](images/commandbarflyout-example.png)

### Issue #1: You can only use a select few object types inside of a CommandBar/CommandBarFlyout. 

When using the CB or CBF controls today, the only supported types for items are [AppBarButton](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.appbarbutton?view=winrt-19041), [AppBarToggleButton](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.appbartogglebutton?view=winrt-19041), and [AppBarSeparator](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.appbarseparator?view=winrt-19041). When you use an AppBarButton in a CB or CBF, all of the interactions and styles needed to conform with the parent control are included - height, width, the background color when hovered/pressed, the font size, etc. 

It is possible to add other types of objects into your CB or CBF, using the wrapper class [AppBarElementContainer](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.appbarelementcontainer?view=winrt-19041). This allows for objects of different types (such as SplitButton or DropdownButton) to be placed in a CB or CBF, but it does not provide the proper styling to make these objects conform. Visually, they won't blend in with the AppBarButton objects being displayed in the CB/CBF without serious styling work. 

### Issue #2: RadioMenuFlyoutItems don't provide nested functionality.
CommandBar and CommandBarFlyout items often have their own attached [MenuFlyout](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.menuflyout?view=winrt-19041), via the AppBarButton.Flyout property. See a simple example of this below:

![Simple MenuFlyout within a CommandBar](images/simple-menuflyout.PNG)
 Items within a MenuFlyout (such as "Sort by artist name") can also have their own MenuFlyouts, creating a  cascading or nested MenuFlyout. See an example of this below. 
 
![Image of a cascading menu](images/nested-menuflyout.PNG)
In order to create this cascading/nested menu today, you can use a class called [MenuFlyoutSubItem](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.menuflyoutsubitem?view=winrt-19041) as an item in the menu. 
This class represents a "parent" MenuFlyout item. It has an
[Items](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.menuflyout.items?view=winrt-19041) property, where you can assign all of its child items.  Here's the above example with labels showing which item is which type:

![Anatomy of a MenuFlyout](images/anatomy-diagram.PNG)

You also have the option of using [RadioMenuFlyoutItem](https://docs.microsoft.com/en-us/windows/winui/api/microsoft.ui.xaml.controls.radiomenuflyoutitem?view=winui-2.5), which has a [GroupName](https://docs.microsoft.com/en-us/windows/winui/api/microsoft.ui.xaml.controls.radiomenuflyoutitem.groupname?view=winui-2.5#Microsoft_UI_Xaml_Controls_RadioMenuFlyoutItem_GroupName) property. Items with the same GroupName will be a part of the same selection model - only one of the items in a Group can be "selected" and display the selection indicator at a time:

![Image of a menu flyout with radio items](images/radiomenuflyoutitems.png)

The issue arises when you want to combine these two concepts. It would be useful to have RadioMenuFlyoutItems have their own child items that participate in the same Group as their parent. This would allow for cleaner UIs by decreasing the size of MenuFlyouts that have a long list of RadioMenuFlyoutItems. See an example below:

![Nested radio menu flyout example](images/commandbar2.png)

### Issue #3: CommandBarFlyout does not have an "always expanded" state.
Currently, a CBF with secondary commands always shows a […] button that allows the user to collapse the CBF into just the top bar (primary commands). There's currently no way to have a CBF with secondary commands that is always expanded when invoked. 

When the CBF is invoked via touch, only the primary commands are shown (with the option for the user to expand the list of secondary commands). The developer is currently not able to control how the CBF appears when invoked, and cannot control whether it can be collapsed or not. 


# Description
<!-- Use this section to provide a brief description of the feature.
For an example, see the introduction to the PasswordBox control 
(http://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box). -->

### Feature #1. Built-in styles to support adding SplitButton to CommandBar and CommandBarFlyout
WinUI will provide built in styles for SplitButton that will allow them to conform with the look and feel of an AppBarButton. These styles will change properties such as the SplitButton's height/width, font size, color, interactions (color changes), and more. 

There will be two styles provided: `CommandBarSplitButtonStyle` and `CommandBarFlyoutSplitButtonStyle`. These styles can be accessed as ThemeResources in any WinUI app. 

### Feature #2. RadioMenuFlyoutSubItem
RadioMenuFlyoutSubItem is a new class that represents a "parent" RadioMenuFlyout item. It is very similar to the [MenuFlyoutSubItem](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.menuflyoutsubitem?view=winrt-19041) class.

All child items of a RadioMenuFlyoutSubItem should be of type [RadioMenuFlyoutItem](https://docs.microsoft.com/en-us/windows/winui/api/microsoft.ui.xaml.controls.radiomenuflyoutitem?view=winui-2.5) (although this is not enforced by the API). If a child item is selected, the parent item will show as selected. All child items should have the same GroupName as siblings of the parents. 

The parent (RadioMenuFlyoutSubItem) cannot be selected. Thus it does not have GroupName or IsChecked properties. It can only achieve selection (and show the selection indicator) if it is bubbled up from one of its child items. Therefore, only one item out of the child items and other top-level RadioMenuFlyout items in the same Group can be selected at a time. 

If a child item becomes selected, the selection indicator will bubble up to its parent when its MenuFlyout is closed. If a child is selected and the MenuFlyout is open, both the parent and child will show the selection indicator (similar to a hierarchical NavigationView).

These are the three main visual states for this new API, using the same Xaml Controls Gallery sample that was previously shown:

*Child item selected, flyout open*

![Child item selected, flyout open](images/commandbar2.png)

*Child item selected, flyout closed*

![Child item selected, flyout closed](images/commandbar3.png)

*Other top-level item selected*

![Top-level item selected](images/commandbar1.PNG)	 

### Feature #3. AlwaysExpanded API
This will be a new API added to CommandBarFlyout that will give the developer the ability to keep the CommandBarFlyout in its expanded mode at all times.

When the AlwaysExpanded property is set to true, the [...] button will not appear, and the user will not be able to collapse the CommandBarFlyout. Once the [...] button is collapsed, other AppBarButtons/CommandBarFlyout items should be able to take its space. 

This property will only have an effect if the CommandBarFlyout has secondary commands. If there are no secondary commands, the CommandBarFlyout will always be in collapsed mode (hence why there’s no AlwaysCollapsed property). 

CommandBarFlyout can still be collapsed/expanded by the developer programmatically even when this property is set to true. 


# Examples
<!-- Use this section to explain the features of the API, showing
example code with each description. The general format is: 
  feature explanation,
  example code
  feature explanation,
  example code
  etc.-->
  
<!-- Code samples should be in C# and/or C++/WinRT -->

<!-- As an example of this section, see the Examples section for the PasswordBox control 
(https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box#examples). -->

### Display a SplitButton in a CommandBarFlyout that opens its menu flyout to the right (using new style)

```xml
<CommandBarFlyout x:Name="ImageCommandsFlyout" >  
    <AppBarButton Icon="OutlineStar" ToolTipService.ToolTip="Favorite" 
                  Label="Star"/>
    <AppBarButton Icon="Copy" ToolTipService.ToolTip="Copy" Label="Copy"/>
    <AppBarButton Icon="Share" ToolTipService.ToolTip="Share" 
                  Label="Share"/>
    <CommandBarFlyout.SecondaryCommands>
        <AppBarElementContainer>
            <SplitButton ToolTipService.ToolTip="Insert"
                         Style="{ThemeResource
                         SplitButtonCommandBarFlyoutStyle}">
                <SplitButton.Content>
                    <StackPanel Orientation="Horizontal">
                        <TextBlock>Insert</TextBlock>
                    </StackPanel>
                </SplitButton.Content>
                <SplitButton.Flyout>
                    <MenuFlyout Placement="RightEdgeAlignedTop">
                        <MenuFlyoutItem Text="Insert above"/>
                        <MenuFlyoutItem Text="Insert between"/>
                        <MenuFlyoutItem  Text="Insert below"/>
                    </MenuFlyout>
                </SplitButton.Flyout>
            </SplitButton>
        </AppBarElementContainer>
        <AppBarButton Label="Select all"/>
        <AppBarButton Label="Delete" Icon="Delete"/>
    </CommandBarFlyout.SecondaryCommands>
</CommandBarFlyout>
```

### Create a CommandBar with items that have Radio Buttons in their MenuFlyout

```xml
<CommandBar DefaultLabelPosition="Right" Grid.Row="1" Margin="50">
    <AppBarToggleButton Icon="Shuffle" Label="Shuffle" />
    <AppBarToggleButton Icon="RepeatAll" Label="Repeat" />
    <AppBarSeparator/>
    <AppBarButton Icon="Back" />
    <AppBarButton Icon="Stop" />
    <AppBarButton Icon="Go" Label="Sort by">
        <AppBarButton.Flyout>
            <MenuFlyout>
                <RadioMenuFlyoutItem Text="Name" GroupName="SortOption"/>
                <RadioMenuFlyoutItem Text="Date" GroupName="SortOption" 
                                     IsChecked="True"/>
                <RadioMenuFlyoutItem Text="Size" GroupName="SortOption"/>
                <RadioMenuFlyoutSubItem Text="Other">
                    <RadioMenuFlyoutItem Text="Album name" GroupName="SortOption"/>
                    <RadioMenuFlyoutItem Text="Artist name" GroupName="SortOption"/>
                    <RadioMenuFlyoutItem Text="Genre" GroupName="SortOption"/>
                </RadioMenuFlyoutSubItem>
            </MenuFlyout>
        </AppBarButton.Flyout>
    </AppBarButton>

    <AppBarSeparator/>
    <AppBarButton Icon="Play" Label="Play" />
    <AppBarButton Icon="Forward" Label="Forward" />

</CommandBar>
```


# API Notes
<!-- Option 1: Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. For properties, specify the default value of the property if it
isn't the type's default (for example an int-typed property that doesn't default to zero.) -->

<!-- Option 2: Put these descriptions in the below API Details section,
with a "///" comment above the member or type. -->

## Notable New APIs

| Name | Description |
|:--|:--|
| `CommandBarFlyout.AlwaysExpanded` Property | Gets or sets a value that indicates whether or not the CommandBarFlyout should always stay in its Expanded state and block the user from entering the Collapsed state. Defaults to false. |
| `RadioMenuFlyoutSubItem` Class  | Represents a radio menu item that displays a sub-menu of other radio menu items in a MenuFlyout control.   |
| `RadioMenuFlyoutSubItem.Items` Property | Gets the collection used to generate the content of the sub-menu.  |
| `RadioMenuFlyoutSubItem.Icon` Property | Gets or sets the graphic content of the radio menu flyout subitem (parent item). |
| `RadioMenuFlyoutSubItem.Text` Property | Gets or sets the text content of a RadioMenuFlyoutSubItem.  |


## New ThemeResources

```xml
<Style x:Key="SplitButtonCommandBarFlyoutStyle" TargetType="SplitButton"></Style>`

<Style x:Key="SplitButtonCommandBarStyle" TargetType="SplitButton"></Style>
```

The following properties are copied over from `MenuFlyoutSubItem`. These should be implemented for `RadioMenuFlyoutSubItem` as well, using the existing `MenuFlyoutSubItem` theme resources as values.

| Property            | Value | 
|---------------------------------------------|---------------------------------------------------|
| RadioMenuFlyoutSubItemBackground | Background color of entire control bounds at rest                         |
| RadioMenuFlyoutSubItemBackgroundPointerOver | Background color on hover                         |
| RadioMenuFlyoutSubItemBackgroundPressed     | Background color when pressed                     |
| RadioMenuFlyoutSubItemBackgroundDisabled    | Background color when disabled                    |
| RadioMenuFlyoutSubItemForeground            | Text color at rest                                |
| RadioMenuFlyoutSubItemForegroundPointerOver | Text color on hover                               |
| RadioMenuFlyoutSubItemForegroundPressed     | Text color when pressed                           |
| RadioMenuFlyoutSubItemForegroundDisabled    | Text color when disabled                          |
| RadioMenuFlyoutSubItemChevron               | Chevron color at rest                             |
| RadioMenuFlyoutSubItemChevronPointerOver    | Chevron color on hover                            |
| RadioMenuFlyoutSubItemChevronPressed        | Chevron color when pressed                        |
| RadioMenuFlyoutSubItemChevronDisabled       | Chevron color when disabled                       |
| RadioMenuFlyoutSubItemChevronSubMenuOpened  | Chevron color when opened                         |


# API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->

```csharp
unsealed runtimeclass CommandBarFlyout : Windows.UI.Xaml.Controls.Primitives.FlyoutBase
{
    CommandBarFlyout();

    Windows.Foundation.Collections.IObservableVector<Windows.UI.Xaml.Controls.ICommandBarElement> PrimaryCommands { get; };
    Windows.Foundation.Collections.IObservableVector<Windows.UI.Xaml.Controls.ICommandBarElement> SecondaryCommands { get; };

    // vv NEW vv
    boolean AlwaysExpanded { get; set; }
    
    static Windows.UI.Xaml.DependencyProperty AlwaysExpandedProperty { get; }

    // ^^ NEW ^^

};
```

```csharp
unsealed runtimeclass RadioMenuFlyoutSubItem : Microsoft.UI.Xaml.Controls.MenuFlyoutItemBase
{
    RadioMenuFlyoutSubItem();

    IList<MenuFlyoutItemBase> Items { get; }
    IconElement Icon { get; set; }
    string Text { get; set; }

    static Windows.UI.Xaml.DependencyProperty Items { get; }
    static Windows.UI.Xaml.DependencyProperty Icon { get; }
    static Windows.UI.Xaml.DependencyProperty Text { get; }

}
```


<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->
