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
The `TreeView` control is lacking an event to track when selection has changed. While other list based controls (`ListView`, `ComboBox`, ...) have such API the `TreeView` control does not. 

The original issue in the WinUI repo for this is here: https://github.com/microsoft/microsoft-ui-xaml/issues/322

# Description
<!-- Use this section to provide a brief description of the feature.
For an example, see the introduction to the PasswordBox control 
(http://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box). -->
This API is adding an event whenever the selected item of a `TreeView` control has changed.

# Examples
<!-- Use this section to explain the features of the API, showing
example code with each description. The general format is: 
  feature explanation,
  example code
  feature explanation,
  example code
  etc.-->
```
private void Initialize()
{
    var treeView = new TreeView();
    treeView.SelectionChanged += OnSelectionChanged;
}

private void OnSelectionChanged(object sender, TreeViewSelectionChangedEventArgs e)
{
    foreach (var addedItem in e.AddedItems)
    {
        // ...
    }

    foreach (var removedItems in e.RemovedItems)
    {
        // ...
    }
}
```


<!-- Code samples should be in C# and/or C++/WinRT -->

<!-- As an example of this section, see the Examples section for the PasswordBox control 
(https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box#examples). -->


# Remarks
<!-- Explanation and guidance that doesn't fit into the Examples section. -->

<!-- APIs should only throw exceptions in exceptional conditions; basically,
only when there's a bug in the caller, such as argument exception.  But if for some
reason it's necessary for a caller to catch an exception from an API, call that
out with an explanation either here or in the Examples -->
The API is available as preview already in WinUI and was merged in this PR: https://github.com/microsoft/microsoft-ui-xaml/pull/2063

# API Notes

N/A
<!-- Option 1: Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. For properties, specify the default value of the property if it
isn't the type's default (for example an int-typed property that doesn't default to zero.) -->

<!-- Option 2: Put these descriptions in the below API Details section,
with a "///" comment above the member or type. -->

# API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->
```
runtimeclass TreeViewSelectionChangedEventArgs
{
    Windows.Foundation.Collections.IVector<Object> AddedItems{ get; };
    Windows.Foundation.Collections.IVector<Object> RemovedItems{ get; };
}

runtimeclass TreeView : Windows.UI.Xaml.Controls.Control {
    event Windows.Foundation.TypedEventHandler<TreeView, TreeViewSelectionChangedEventArgs> SelectionChanged;
}
```

# Appendix
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->
The underlying implementation of this event is using two different approaches depending on multiselection status. 

When multiselection **is not** enabled on the treeview control, the implementation is just using the `SelectionChanged` event of the `ListView` control used internally.

When multiselection **is** enabled on the treeview control, selection changed is tracked differently.

While there are two code paths, this does not imply any user facing difference in how the API can be consumed.