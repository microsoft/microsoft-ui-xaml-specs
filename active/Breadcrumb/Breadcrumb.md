
# Background
There’s currently no consistent and Fluent way to address the common UX pattern of a breadcrumb.
This control is needed for situations where the user's navigation trail (in a file system or menu system) needs
to be persistently visible.
A Breadcrumb control is movitated by its use in many app scenarios and supporting developers in migrating from WPF.
This is not the 'history' of the user's navigation, but the direct path of pages or folders from
the root node to their current position. 

A Breadcrumb control with 3 nodes:

![Breadcrumb default with 3 nodes](images/Breadcrumb_default.PNG)

A Breadcrumb "crumbling" with 3 visible nodes:

![Breadcrumb crumbling with 3 visible nodes](images/Breadcrumb_crumbling.PNG)

For situations where resizing of the Breadcrumb means the full path can't be shown,
the nodes will 'crumble' starting at the root node. This crumbling behavior hides nodes (starting with the root node) within a flyout in order to prioritize displaying the current node. 

A Breadcrumb control with the last node truncated:

![Breadcrumb_crumbled with last node truncated](images/Breadcrumb_truncation.PNG)
In addition,the leaf node can truncated if crumbling from the root node to the leaf node's parent is not enough for a resizing scenario. 

This control is similar to an ItemsControl (has an ItemsSource property), but does not derive from it.

# Is this the right control?
Use the Breadcrumb control for a persisting 'trail' that shows the user's navigation path. This is useful for scenarios where the user may navigate many layers deep, or need to return easily to pages at any level. This is also commonly seen in apps that explore a file system with a heirarchical folder structure. 


## V1/V2 Breadcrumb

The work for Breadcrumb has been scoped to V1 and V2 stages of the API.
In V1 Breadcrumb no chevrons are interactable to show children.
V2 Breadcrumb will add functionality to have flyouts from chevrons for every node,
to view children of that node. This spec focuses on V1 Breadcrumb,
but keeping in mind that V1 is positioned in a way to have V2 features smoothly added later. 

# API Pages

## Examples
A Breadcrumb control with 3 nodes:

![Breadcrumb default with 3 nodes](images/Breadcrumb_default.PNG)

A Breadcrumb "crumbling" with 3 visible nodes:

![Breadcrumb crumbling with 3 visible nodes](images/Breadcrumb_crumbling.PNG)

A Breadcrumb control with the last node truncated:

![Breadcrumb_crumbled with last node truncated](images/Breadcrumb_truncation.PNG)

The following example creates a simple Breadcrumb control with three nodes:

![Breadcrumb default with 3 nodes](images/Breadcrumb_default.PNG)

XAML
~~~~ 
     <muxc: Breadcrumb x:Name="DefaultBreadcrumb"/> 
~~~~

C#
~~~~ 
     DefaultBreadcrumb.ItemsSource = new string[]{ "Node", "Node" , "Node"}
~~~~

More examples with a GIF of crumbling behavior to be added.

## Breadcrumb member notes

| Name | Description | Default |
| :---------- | :------- | :------- |
| DropDownItemTemplate | Gets or sets the template to display the drop-down of nodes | 
| ItemsSource | Gets or sets the content of the Breadcrumb | null |
| ItemTemplate | Gets or sets the template to display an item|  | 
| ItemClicked | Raised when a user interaction causes a jump to the CurrentItem.  | N/A |

Spec note: the V2 of Breadcrumb will add flyouts from chevrons to view children of a node.
Additional properties will be added to the API to enable this functionality. 

# API Details

```cs
runtimeclass BreadcrumbItemClickedEventArgs
{
    Object Item { get; };
}

unsealed runtimeclass BreadcrumbItem : Windows.UI.Xaml.Controls.ContentControl
{
    BreadcrumbItem();
}

unsealed runtimeclass BreadcrumbLayout : NonVirtualizingLayout
{
    BreadcrumbLayout();
}

[MUX_PROPERTY_CHANGED_CALLBACK(TRUE)]
[MUX_PROPERTY_CHANGED_CALLBACK_METHODNAME("OnPropertyChanged")]
unsealed runtimeclass Breadcrumb : Windows.UI.Xaml.Controls.Control
{
    Breadcrumb();

    Object DropdownItemTemplate{ get; set; };
    Object ItemsSource{ get; set; };
    Object ItemTemplate{ get; set; };

    event Windows.Foundation.TypedEventHandler<Breadcrumb, BreadcrumbItemClickedEventArgs> ItemClicked;

    static Windows.UI.Xaml.DependencyProperty DropdownItemTemplateProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty ItemsSourceProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty ItemTemplateProperty{ get; };
}
```

# Appendix

## UI Automation Pattern

Breadcrumb will use the
[InvokePattern](!https://docs.microsoft.com/dotnet/framework/ui-automation/implementing-the-ui-automation-invoke-control-pattern). The flyouts use the [ExpandCollapsePattern](https://docs.microsoft.com/dotnet/api/system.windows.automation.expandcollapsepattern).

## Keyboard

* The user can tab into the Breadcrumb and tab out.
The first tab in gives focus to the first (highest-level) node.
Once the Breadcrumb has focus, the user can use the left/right arrows to navigate from node to node.
* If the Breadcrumb is crumbled, down/up will open the flyout from
the ellipsis and down/up will navigate between the flyout items

## Gamepad

* Spatial navigation to focus and select the nodes. A flyout can be opened using the A button. 

## Screen reader

When the focus is on a node, the screen reader will announce the node
as well as "n of m" to convey which node in the path it is.
If the Breadcrumb has been crumbled due to resizing,
the screen reader will announce 'collapsed' and 'expanded' for the flyout of crumbled nodes. 
