# Background
<!-- Use this section to provide background context for the new API(s) 
in this spec. -->
The Xaml [ItemsRepeater](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.ItemsRepeater) control displays a list of objects using a specified template (similar to a [ListView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView)). You provide the list of objects with the [ItemsRepeater.ItemsSource](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.ItemsRepeater.ItemsSource) property. The list need only be an IIterable but can be an IVector for better perf. ItemsRepeater exposes a read-only [ItemsRepeater.ItemsSourceView](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.ItemsRepeater.ItemsSourceView) property that provides a view on this list (an [ItemsSourceView](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.ItemsSourceView)), which has helpers like Count and GetAt over what might be just an IIterable.

Currently the ItemsSourceView class does not expose an IndexOf function. However during development, this method was used multiple times internally on derivatives of the class. To make the usage of this existing function easier on subclasses, this spec adds an IndexOf function to the ItemsSourceView.

# Description

The IndexOf function returns the index of the given element in the items source the ItemsSourceView is used for.
Note that if an element is not contained in the items source, the function will return -1.
> Spec note: returning -1 matches the behavior of for example IndexOf on [UIElementCollection](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.UIElementCollection) and List<T>.
# Examples
C# example

```c#
/// ************** Behavior ************ ///

var itemsSourceView = new ItemsSourceView()
{
  // Initialize
};

// Get an item
var myItem = MyItemsSource.GetAt(1);

Assert.AreEqual(1,itemsSourceView.IndexOf(myItem));

/// ****** Use case in event handler ****** ///

public void ItemInvoked(object sender, RoutedEventArgs e)
{
  int index = myItemsSourceView.IndexOf(sender as IInspectable); 

  // Do something with index, here e.g. :
  Console.log("Item " + index + " of items source was invoked");
}

```


# Remarks

The IndexOf function of ItemsSourceView base class throws a winrt hresult_not_implemented exceptions.
If an element is not present in the collection, the method should return -1.

# API Notes
ItemsSourceView.IndexOf(IInspectable item): Returns index in the items source. If an item is not in the items source, it will return -1.
# API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->

```MIDL

unsealed runtimeclass ItemsSourceView: Windows.UI.Xaml.Interop.INotifyCollectionChanged
{
     // ...
    Int32 IndexOf(IInspectable item);

}

```

# Appendix
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->

When IndexOf of the ItemsSourceView class get's invoked, should we instead of throwing `hresult_not_implemented` return -1 instead?
