
# Background
There’s currently no consistent and Fluent way to address the common UX pattern of a breadcrumb.
This control is needed for situations where the user's navigation trail (in a file system or menu system) needs
to be persistently visible.
A Breadcrumb control is movitated by its use in many app scenarios and supporting developers in migrating from WPF.

This control is similar to an ItemsControl (has an ItemsSource property), but does not derive from it.

## V1/V2 Breadcrumb

The work for Breadcrumb has been scoped to V1 and V2 stages of the API.
In V1 Breadcrumb no chevrons are interactable to show children.
V2 Breadcrumb will add functionality to have flyouts from chevrons for every node,
to view children of that node. This spec focuses on V1 Breadcrumb,
but keeping in mind that V1 is positioned in a way to have V2 features smoothly added later. 

# API Pages

## Breadcrumb class

Use the Breadcrumb control to private persisting 'trail' that shows the user's navigation path.

This is not the 'history' of the user's navigation, but the direct path of pages or folders from
the root node to their current position. For situations where resizing means the full path can't be shown,
the Breadcrumb will 'crumble' starting at the root node. In addition,
the leaf node can truncated if crumbling from the root node to the leaf node's parent is not enough for a resizing scenario. 

A Breadcrumb control with 3 nodes:

![Breadcrumb default with 3 nodes](images/Breadcrumb_default.PNG)

A Breadcrumb "crumbling" with 3 visible nodes:

![Breadcrumb crumbling with 3 visible nodes](images/Breadcrumb_crumbling.PNG)

A Breadcrumb control with the last node truncated:

![Breadcrumb_crumbled with last node truncated](images/Breadcrumb_truncation.PNG)

The following Xaml markup  creates a simple Breadcrumbe control with three nodes:

![Breadcrumb default with 3 nodes](images/Breadcrumb_default.PNG)
 ~~~~ 
     <muxc: Breadcrumb ItemsSource = {"Node", "Node", "Node"} /> 
~~~~
More examples to be added

## Breadcrumb member notes

| Name | Description | Default |
| :---------- | :------- | :------- |
| ItemsSource | provides the content of the Breadcrumb | Null |
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
[InvokePattern](!https://docs.microsoft.com/en-us/dotnet/framework/ui-automation/implementing-the-ui-automation-invoke-control-pattern).

## Keyboard

* The user can tab into the Breadcrumb and tab out.
The first tab in gives focus to the first (highest-level) node.
Once the Breadcrumb has focus, the user can use the left/right arrows to navigate from node to node.
* If the Breadcrumb is crumbled, down/up will open the flyout from
the ellipsis and down/up will navigate between the flyout items

## Gamepad

* Spatial navigation to focus and select the nodes.

## Narrator

When the focus is on a node, Narrator will announce the node
as well as "n of m" to convey which node in the path it is.
If the Breadcrumb has been crumbled due to resizing,
Narrator will announce 'collapsed' and 'expanded' for the flyout of crumbled nodes. 
