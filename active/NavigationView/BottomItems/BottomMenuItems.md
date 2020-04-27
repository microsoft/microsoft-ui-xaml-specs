
# Background
Today, [NavigationView](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/navigationview) provides a place in its [Pane](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/navigationview) to show any number of items.
The primary purpose of these items is to be a list of navigation links within the app that hosts NavigationView, likely as a root element.
This navigation list is top aligned when NavigationView is shown in one of its left [display modes](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/navigationview#display-modes), and left aligned when NavigationView is in its top [display mode](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/navigationview#display-modes).
NavigationView provides a built-in [Settings item](https://docs.microsoft.com/en-us/uwp/api/microsoft.ui.xaml.controls.navigationview.settingsitem?view=winui-2.3), which is always positioned at the bottom or right of the Pane, and a [PaneFooter](https://docs.microsoft.com/en-us/uwp/api/microsoft.ui.xaml.controls.navigationview.panefooter?view=winui-2.3) area that positions content just before (either above or to the left) of the Settings item.
Although an app developer can place any content into PaneFooter, including other NavigationViewItems, this content will not participate in the selection model and animations of the primary navigation list and Settings item.

_Open question:_ Should PaneFooter be deprecated because this new API is a direct replacement?

Some apps may choose to visually group navigation items into top-aligned and bottom-aligned lists.
While this grouping is possible today using navigation view's [PaneFooter](https://docs.microsoft.com/en-us/uwp/api/microsoft.ui.xaml.controls.navigationview.panefooter?view=winui-2.3) property, the approach has interaction downsides that this feature will correct.

![NavigationView in Left display mode showing FooterMenuItems](FooterItems_Left.png)

![NavigationView in Top display mode showing FooterMenuItems](FooterItems_Top.png)

By adding FooterMenuItems, we'll unify the [Settings item](https://docs.microsoft.com/en-us/uwp/api/microsoft.ui.xaml.controls.navigationview.settingsitem?view=winui-2.3) with other navigation items in its immediate proximity.
As a result, keyboard and Narrator users will feel that the footer list behavior meets their expectations.
Selection behavior and animations will also feel better integrated into the whole NavigationView control.

# Description 
<!-- Use this section to provide a brief description of the feature.
For an example, see the introduction to the PasswordBox control 
(http://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box). -->

FooterMenuItems is a list of navigation items similar to the MenuItems list.
FooterMenuItems allows apps to visually group their navigation items into top-aligned and bottom-aligned or left-aligned and right-aligned groups. 
The default Settings item will be included as the last item in the FooterMenuItems collection. As a result, adding items into FooterMenuItems will by default display them before the Settings item. The Settings item can still be toggled using the `IsSettingsVisible` property.

By unifying bottom-or-left-aligned navigation items with the Settings item, these items will now be a part of selection, keyboarding, and narrator behavior that applies to the rest of the NavigationView.


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

## Add footer menu items in markup
```xaml
    <muxc:NavigationView>
        <muxc:NavigationView.MenuItems>
            <muxc:NavigationViewItem Icon="Save" Content="main1"/>
            <muxc:NavigationViewItem Icon="Play" Content="main2"/>
        </muxc:NavigationView.MenuItems>
        <muxc:NavigationView.FooterMenuItems>
            <muxc:NavigationViewItem Icon="Calculator" Content="bottom1" Padding="0,0,12,0"/>
            <muxc:NavigationViewItem Icon="Calendar" Content="bottom2" Padding="0,0,12,0"/>
        </muxc:NavigationView.FooterMenuItems>
        
    </muxc:NavigationView>
```


# API Notes
<!-- Option 1: Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. For properties, specify the default value of the property if it
isn't the type's default (for example an int-typed property that doesn't default to zero.) -->

<!-- Option 2: Put these descriptions in the below API Details section,
with a "///" comment above the member or type. -->

`public IList<object> FooterMenuItems { get; }`

Gets the list of objects to be used as navigation items in the footer menu.

`public object FooterMenuItemsSource { get; set; }`

Sets or gets the object that represents the navigation items to be used in the footer menu.

# API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->
```c++

[MUX_PROPERTY_CHANGED_CALLBACK(TRUE)]
[MUX_PROPERTY_CHANGED_CALLBACK_METHODNAME("OnPropertyChanged")]
unsealed runtimeclass NavigationView : Windows.UI.Xaml.Controls.ContentControl
{
    {
        Windows.Foundation.Collections.IVector<Object> FooterMenuItems{ get; };
        Object FooterMenuItemsSource { get; set; };
    }
    
    static Windows.UI.Xaml.DependencyProperty FooterMenuItemsProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty FooterMenuItemsSourceProperty{ get; };
}
```
# Appendix
## Selection
When users invoke a navigation item, that item will become selected and show its selection indicator. 
At most one navigation item can be selected at a given time.
The selection indicator will animate smoothly between items in the MenuItems list and FooterMenuItems list including the optional built-in Settings menu item.

In order to achieve consistent animations and allow for only one navigation item to be selected at a time, all navigation items should be in the same selection model. To achieve this, a large collection that consists of two smaller collections (specifically FooterMenuItems and MenuItems) will be used as the source.

## Narrator
Screen readers will announce MenuItems and FooterMenuItems as two separate lists, including position in set. 
Unlike previous behavior, the Settings item will be announced as a part of the FooterMenuItems list - specifically, its index should be announced relative to the other items in the list. For example, if there are three elements in the FooterMenuItemsList, Settings would be announced as 3 of 3.

## Keyboarding
Given the following list, keyboarding actions should be as follows:
- Main 1
- Main 2

<!-- -->

- Bottom 1
- Bottom 2
- Settings

Users should be able to Tab between these two lists.

If keyboard focus is on Main 2:
- Up arrow moves focus to Main 1
- Down arrow moves focus to Bottom 1
- Tab moves focus to bottom1 (unless an item in the bottom list is selected, in which case focus should go to that item).
- Home moves focus to Main 1
- End does nothing

If keyboard focus is on Bottom 1:
- Up arrow moves focus to Main 2
- Down arrow moves focus to Bottom 2
- Tab moves focus to the first focusable item in NavigationView's Header or Content (same behavior as on Settings item today).
- Home does nothing
- End moves focus to settings

If keyboard focus is on Settings:
- Up arrow moves focus to Bottom 2
- Down arrow does nothing (same as today)
- Tab moves focus to a Header or Content item (same as today).
- Home moves focus to Bottom 1
- End does nothing
