# Background

The Xaml [TreeView](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.TreeView) control shows a list of hierarchical data, similar to a ListView. 

When TreeView was first released, you populated it using the [RootNode property](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.TreeView.RootNodes), giving it a tree of nodes, each node referencing a data item. There was negative feedback about this nodes approach, though, from developers preferring a more data binding friendly pattern along the lines of [ListView.ItemsSource](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsControl.ItemsSource). 

So a [TreeView.ItemsSource](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.TreeView.ItemsSource) property was added, and now you can use either the nodes approach or the ItemsSource approach. But unlike ListView, TreeView doesn't have the corresponding SelectedItem and SelectedItems properties for single- and multi-select. Those are being added in this spec.

Also being added in this spec, for the case where the RootNode is being used instead of ItemsSource, is an API to select a single node. That is, there is a [SelectedNodes](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.TreeView.SelectedNodes) property today, and this spec is adding a singular SelectedNode property.

Related GitHub issues:  
https://github.com/microsoft/microsoft-ui-xaml/issues/197  
https://github.com/microsoft/microsoft-ui-xaml/issues/124  
https://github.com/microsoft/microsoft-ui-xaml/issues/386

# Description

Extend TreeView selection API sets to make selections easier.

- Add `SelectedNode`: get/set the selected TreeViewNode for single selection
- Add `SelectedItem`: get/set the selected item for single selection
- Add `SelectedItems`: get the SelectedItems collection.
- Fix existing `SelectedNodes` property: Currently this only works in multi-select mode ([SelectionMode](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.TreeView.SelectionMode) set to `Multiple`). It will now also work in single selection mode (consistent with ListView).

# Examples

## TreeView.SelectedNode
Ouput the depth of the selected tree view node.
```C#
TreeViewNode selectedNode = treeView.SelectedNode;
if (selectedNode != null)
{
    Debug.WriteLine($"Selected node depth: {selectedNode.Depth}");
}
```

## TreeView.SelectedItem
Bind the TreeView control to a View Model with the data source and the currently selected item.
```Xaml
<TreeView ItemsSource="{x:Bind Descendants}"
            SelectedItem="{x:Bind CurrentDescendant, Mode=TwoWay}" />
```

## TreeView.SelectedItems
```C#
void AddToSelection(TreeView treeView, object item)
{
    IList<Object> selectedItems = treeView.SelectedItems;
    selectedItems.Add(item);
}
```

# API Details

```
namespace Microsoft.UI.Xaml.Controls
{
    [webhosthidden]
    unsealed runtimeclass TreeView : Windows.UI.Xaml.Controls.Control
    {
        ...

        TreeViewNode SelectedNode{ get; set; };
        Object SelectedItem{ get; set; };
        Windows.Foundation.Collections.IVector<Object> SelectedItems{ get; };

        ...
    }
}
```

# Remarks

SelectedItem(s) returns Object(collection of objects), it will be the ItemsSource type object when using databinding, or TreeViewNode when not using databinding.

Prior to WinUI 2.2, the [TreeView.SelectedNodes](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.TreeView.SelectedNodes) property can only be used when the [SelectionMode](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.TreeView.SelectionMode) is set to `Multiple`.

# API Notes

## Class: TreeView
| Member Name | Description |
|:- |:--|
| SelectedNode | Gets or sets the selected TreeViewNode. |
| SelectedNodes | The collection of nodes that are selected in the tree. The default is an empty collection. |
| SelectedItem | Gets or sets the selected item. |
| SelectedItems | The collection of items that are selected in the tree. The default is an empty collection. |
