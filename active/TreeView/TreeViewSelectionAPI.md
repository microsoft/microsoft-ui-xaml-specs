# Background

`SelectedNodes` is the only API we currently have on TreeView to update selections, and it only works in multi-select mode, which means there's no easy way to programmatically change selections in single selection mode. Also, with the new data binding capability, people would prefer to work on their own data source instead of `TreeViewNode`. With current APIs, they will have to do `ContainerFromItem` and then `NodeFromContainer` to get the associated TreeViewNode in order to update SelectedNodes.

This API change introduces 3 new APIs to make things easier, `SelectedNode`, `SelectedItem` and `SelectedItems`.

Related GitHub issues:  
https://github.com/microsoft/microsoft-ui-xaml/issues/197  
https://github.com/microsoft/microsoft-ui-xaml/issues/124  
https://github.com/microsoft/microsoft-ui-xaml/issues/386

# Description

Extend TreeView selection API sets to make selections easier.

- Fix SelectedNodes: Currently this only works in multi-select mode. It should also work in single selection mode (consistent with ListView).
- Add SelectedNode: get/set the selected TreeViewNode for single selection
- Add SelectedItem: get/set the selected item for single selection
- Add SelectedItems: get the SelectedItems collection.

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

# Examples

```C#
TreeViewNode node = ...
// Set SelectedNode for single selection
treeView.SelectedNode = node;
// Get SelectedNode in single selection
TreeViewNode selectedNode = treeView.SelectedNode;


TreeViewItemsSourceType item = ...
// Set SelectedItem
treeView.SelectedItem = item;
// Get SelectedItems
TreeViewItemsSourceType selectedItem = treeView.SelectedItem;

// Get SelectedItems collection
IList<Object> selectedItems = treeView.SelectedItems();
// Modify the collection to update selections
selectedItems.Add(item);

```

# Remarks

SelectedNode(s) always returns/takes TreeViewNode.
SelectedItem(s) returns Object(collection of objects), it will be the ItemsSource type object when using databinding, or TreeViewNode when not using databinding.

# API Notes

## Class: TreeView
| Member Name | Description |
|:- |:--|
| SelectedNode | Gets or sets the selected TreeViewNode. |
| SelectedNodes | The collection of nodes that are selected in the tree. The default is an empty collection. |
| SelectedItem | Gets or sets the selected item. |
| SelectedItems | The collection of items that are selected in the tree. The default is an empty collection. |
