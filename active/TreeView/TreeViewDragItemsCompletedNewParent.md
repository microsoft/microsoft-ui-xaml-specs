# Background

TreeView has built-in support for drag&drop reorder but currently there is no easy way to figure out where the item is being dropped. 

# Description

TreeView's data usually has hierarchical structure by nature and "index" doesn't work very well in this scenario. Parent + SubIndex is a more accurate way to locate items in TreeView. 

A `NewParentItem` property in TreeViewDragItemsCompletedEventArgs should be sufficient to represent the drop location. To get SubIndex, developers can do NewParentItem.Children.IndexOf().

# Examples

Imagine we have a tree like below with team name and their players.

- Team 1
  - Player 1
  - Player 2
- Team 2
  - Player 3

Drag Player2 and then drop it inside Team2 above Player3. In DragItemsCompleted event handler we can do the following to produce a status update text "Player 2 has changed to Team 2".

```C#
        private void TreeView_DragItemsCompleted(TreeView sender, TreeViewDragItemsCompletedEventArgs args)
        {
            var playerName = args.Items[0] as string;
            var teamName = args.NewParentItem as string;

            StatusTextBlock.SetText($"{playerName} has changed to {teamName}");
        }
```

# API Details

```
[webhosthidden]
runtimeclass TreeViewDragItemsCompletedEventArgs
{
    ...

    Object NewParentItem{ get; };

    ...
}
```

# Remarks

Similar to other "Item" APIs in TreeView, NewParent returns a loosely typed Object, it can be cast to ItemsSource type when using databinding or TreeViewNode when not using databinding.

# API Notes

## Class: TreeViewDragItemsCompletedEventArgs
| Member Name | Description |
|:- |:--|
| NewParentItem | Gets the loosely typed object that is the new parent of drag items. |