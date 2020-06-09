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

[ItemsRepeater](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.itemsrepeater?view=winui-2.4) is a flexible layout control that allows you to easily display collections of items complete with custom layouts, animations, styling, and more. Currently, there is no built-in way to access which items are visible or realized in the viewport of the ItemsRepeater. This inhibits lots of interaction-based UIs and puts lots of extra work on the developer to manually determine which items are visible. 

In uniform layouts, all visible items inside of the ItemsRepeater's viewport will appear between the first and last visible index. Since ItemsRepeater allows for custom layouts, it's common to have non-uniform layouts, where each item has a different height or width, as well as nested layouts. In these cases, the visible items are often non-contiguous in terms of their indices. For these cases, knowing the first visible index and last visible index won't be helpful, and the best way to determine what's visible is to have a list of all currently realized/visible items. 

The [ListView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listview?view=winrt-19041) and [GridView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.gridview?view=winrt-19041) controls already have properties to access the indices of their first and last visible items in the form of the [FirstVisibleIndex](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemswrapgrid.firstvisibleindex?view=winrt-19041#Windows_UI_Xaml_Controls_ItemsWrapGrid_FirstVisibleIndex) and [LastVisibleIndex](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemswrapgrid.lastvisibleindex?view=winrt-19041) properties on their respective base panels, [ItemsStackPanel](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemsstackpanel?view=winrt-19041) and [ItemsWrapGrid](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsWrapGrid?redirectedfrom=MSDN&view=winrt-19041#Windows_UI_Xaml_Controls_ItemsWrapGrid_FirstVisibleIndex). 

# Description
<!-- Use this section to provide a brief description of the feature.
For an example, see the introduction to the PasswordBox control 
(http://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box). -->

FirstVisibleIndex, LastVisibleIndex,and RealizedItems are properties of ItemsRepeater that give you access to which indices of items within an ItemsRepeater are currently visible on the screen.

The `FirstVisibleIndex` property returns the lowest index of the currently visible indices, which is the first visible index. The `LastVisibleIndex` property returns the highest index of the currently visible indices, which is the last visible index. For contiguous scenarios/layouts (uniform layouts), all items with indices between the first and last visible index are currently visible on the screen. For non-contiguous/non-uniform/nested layouts, the first and last visible index may not correlate to the first and last visible items on the screen.


The `RealizedItems` property returns all items within the ItemsRepeater that are currently realized and visible on the screen, including items that may be cut off or not 100% visible. This property is helpful for determining which items are visible in non-contiguous and non-uniform layout scenarios.

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

```csharp
// Check if an item is currently visible in a uniform layout.
int item_index = repeater.GetIndexFromElement(item);
if (item_index > repeater.FirstVisibleIndex && item_index < repeater.LastVisibleIndex){
    //item is visible, perform necessary actions
}

// In a non-contiguous layout, find the index of the first visible item.
UIElement last_item = repeater.RealizedItems[0] as UIElement;
int first_visible_idx = repeater.GetIndexFromElement(last_item);
// first_visible_idx represents the first visible index, perform necessary actions on it. 
```

# Remarks
<!-- Explanation and guidance that doesn't fit into the Examples section. -->

<!-- APIs should only throw exceptions in exceptional conditions; basically,
only when there's a bug in the caller, such as argument exception.  But if for some
reason it's necessary for a caller to catch an exception from an API, call that
out with an explanation either here or in the Examples -->

# API Notes
<!-- Option 1: Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. For properties, specify the default value of the property if it
isn't the type's default (for example an int-typed property that doesn't default to zero.) -->

<!-- Option 2: Put these descriptions in the below API Details section,
with a "///" comment above the member or type. -->
```csharp
public int FirstVisibleIndex { get; }

public int LastVisibleIndex { get; }

public object RealizedItems { get; }
// **The ItemsRepeater ItemsSource property accepts arguments of type object, so this property follows suit. See open questions below.**
```

# API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->
```csharp
unsealed runtimeclass ItemsRepeater : Windows.UI.Xaml.FrameworkElement
{
    ItemsRepeater();

    // ...

    Int32 FirstVisibleIndex { get; set; }
    Int32 LastVisibleIndex { get; set; }
    Object RealizedItems { get; set; }

    // ...
}
```

<!-- # Appendix
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details.  -->

# Open Questions
- Should the RealizedItems property return an IEnumerable<object> or List<object>? Or should it simply return an object to match the ItemsSource property?