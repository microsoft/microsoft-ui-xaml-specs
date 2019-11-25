# Background

TreeView has built-in support for drag&drop reorder but currently there is no easy way to figure out where the item is being dropped. 

# Description

TreeView's data usually has hierarchical structure by nature and "index" doesn't work very well in this scenario. Parent + SubIndex is a more accurate way to locate items in TreeView. 

A `NewParent` property in TreeViewDragItemsCompletedEventArgs should be sufficient to represent the drop location. To get SubIndex, developers can do NewParent.Children.IndexOf().

# Examples

```C#
private void TreeView_DragItemsCompleted(MUXC.TreeView sender, MUXC.TreeViewDragItemsCompletedEventArgs args)
{
    var parent = args.NewParent;
}
```

# API Details

```
[webhosthidden]
runtimeclass TreeViewDragItemsCompletedEventArgs
{
    ...

    Object NewParent{ get; };

    ...
}
```

# Remarks

Similar to other "Item" APIs in TreeView, NewParent returns a loosely typed Object, it can be cast to ItemsSource type when using databinding or TreeViewNode when not using databinding.

# API Notes

## Class: TreeViewDragItemsCompletedEventArgs
| Member Name | Description |
|:- |:--|
| NewParent | Gets the loosely typed object that is the new parent of drag items. |