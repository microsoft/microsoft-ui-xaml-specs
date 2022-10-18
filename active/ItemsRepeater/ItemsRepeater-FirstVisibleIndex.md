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

ItemsRepeater does independent scrolling, and requires a buffer area that holds non-visible items and runs layout processes on them before they become visible/scrolled onto. This buffer area size is configurable via the [HorizontalCacheLength](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.horizontalcachelength?view=winui-2.4) and [VerticalCacheLength](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.verticalcachelength?view=winui-2.4) properties. 

The [ListView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listview?view=winrt-19041) and [GridView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.gridview?view=winrt-19041) controls  have properties to access the indices of their first and last visible items in the form of the [FirstVisibleIndex](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemswrapgrid.firstvisibleindex?view=winrt-19041#Windows_UI_Xaml_Controls_ItemsWrapGrid_FirstVisibleIndex) and [LastVisibleIndex](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemswrapgrid.lastvisibleindex?view=winrt-19041) properties on their respective base panels, [ItemsStackPanel](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemsstackpanel?view=winrt-19041) and [ItemsWrapGrid](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsWrapGrid?redirectedfrom=MSDN&view=winrt-19041#Windows_UI_Xaml_Controls_ItemsWrapGrid_FirstVisibleIndex). These properties allow you to access all currently visible items. 

# Description
<!-- Use this section to provide a brief description of the feature.
For an example, see the introduction to the PasswordBox control 
(http://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box). -->

`RealizedItems` and `VisibleItems` are properties that give you access to all realized or visible items within an ItemsRepeater. These two properties both return lists of [UIElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement?view=winrt-19041) objects that make up the ItemsRepeater. 

Realized items includes all items that are currently visible and all items that are in the cache/buffer area. The size of these buffer areas are configurable via the [HorizontalCacheLength](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.horizontalcachelength?view=winui-2.4) and [VerticalCacheLength](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.verticalcachelength?view=winui-2.4) properties

Visible items refers to all items that are visible in the ItemsRepeater, whether they are fully or partially visible.

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
/* Example 1: Check if a specific item is visible by the user. If visible, inform the user that they've scrolled to the bottom of the list. */

int repeater_length = repeater.ItemsSourceView.Count;

foreach (Grid item in repeater.VisibleItems){
  // Check to see if the last item of the list is visible.
  if (repeater.GetElementIndex(item) == repeater_length - 1){
    // If so, we've reached the end of the list.
    myTextBlock.Text = "No more elements to display.";
  }
}

/* Example 2: Get the list of realized items and mark in the source collection that the item has been realized. */
int item_idx = 0;
foreach (Grid item in repeater.RealizedItems){
  item_idx = repeater.GetElementIndex(item);
  // IsRealized is a custom attribute created for objects that are a part of the repeater's ItemsSource. 
  repeater.ItemsSourceView[item_idx].IsRealized = true; 
}

/* Example 3: Find the item with the first visible index and change its color */

int firstVisibleIndex = int.MaxValue;

foreach (Grid item in repeater.VisbleItems){
  int index = repeater.GetElementIndex(item);
  firstVisibleIndex = Math.Min(index, firstVisibleIndex);
}

Grid firstVisibleItem = repeater.TryGetElement(firstVisibleIndex) as Grid;
firstVisibleItem.Background = "Light Green";
```

# API Notes
<!-- Option 1: Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. For properties, specify the default value of the property if it
isn't the type's default (for example an int-typed property that doesn't default to zero.) -->

<!-- Option 2: Put these descriptions in the below API Details section,
with a "///" comment above the member or type. -->
```csharp

public IEnumerable<UIElement> RealizedItems { get; }
public IEnumerable<UIElement> VisibleItems { get; }

// RealizedItems and VisibleItems are *unsorted* IEnumerable objects.

```

# API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->
```csharp
unsealed runtimeclass ItemsRepeater : Windows.UI.Xaml.FrameworkElement
{
    ItemsRepeater();

    // ...
    Windows.Foundation.Collections.IEnumerable<Windows.UI.Xaml.UIElement> RealizedItems { get; }

    Windows.Foundation.Collections.IEnumerable<Windows.UI.Xaml.UIElement> VisibleItems { get; }

    // ...
}
```

<!-- # Appendix
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details.  -->
