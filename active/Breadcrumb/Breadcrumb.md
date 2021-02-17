Breadcrumb control
===

# Background

A "Breadcrumb" UX pattern provides an way to show a current position in context.
For example in Windows File Explorer the current directory is shown with a breadcrumb,
where clicking on any node takes you to that ancestor directory:

![File Explorer breadcrumb example](images/file-explorer-breadcrumb.jpg)

There’s currently no consistent and Fluent way to address the common UX pattern of a breadcrumb.
This control is needed for situations where the user's navigation trail (in a file system or menu system) needs
to be persistently visible.

A Breadcrumb control is movitated by its use in many app scenarios and supporting developers in migrating from WPF.
This is not the 'history' of the user's navigation, but the direct path of pages or folders from
the root node to their current position. 

## V1/V2 Breadcrumb

The work for Breadcrumb has been scoped to V1 and V2 stages of the API.
In V1 the nodes have no option to open a drop-down menu, typically used to show the node's siblings.
V2 will add this. This spec focuses on V1 Breadcrumb,
but keeping in mind that V1 is positioned in a way to have V2 features smoothly added later. 

# API Pages

## Breadcrumb class

Represents a control that shows a list of items, typically the navigation trail to the current location.

### Examples

The following example code creates a simple Breadcrumb control with three nodes.
When there is not enough width to display the whole control,
the leading nodes are replaced with a node that the user can click on,
opening the missing nodes in a flyout menu.

![Breadcrumb with nodes: Home, Documents, Design, Northwind, Images. The app is resized so that the Breadcrumb crumbles - then, clicking the ellipsis reveals the crumbled Home and Documents nodes](images/Breadcrumb_crumbling_flyout.gif)

XAML
```XAML
<Breadcrumb x:Name="Breadcrumb1" /> 
```

C#
```cs
Breadcrumb1.ItemsSource = new string[]{ "Home", "Documents", "Design", "Northwind", "Images"}
```

If the last item in a Breadcrumb is text and is too long to fit it will be truncated. If not text this item will be clipped. 

![Breadcrumb_crumbled with last node truncated](images/Breadcrumb_truncation.PNG)

## Breadcrumb.DropDownItemTemplate

Gets or sets the template to display the drop-down of nodes

The following configures a Breadcrumb so that it's drop down displays bold text.

```xaml
<Breadcrumb>
    <Breadcrumb.DropDownItemTemplate>
        <DataTemplate x:DataType='BreadcrumbDropDownItem`>
            <TextBlock Text='{x:Bind Content} FontWeight="Bold" />
        </DataTemplate>
    </Breadcrumb.DropDownItemTemplate>
</Breadcrumb>
```

## Other Breadcrumb members

| Name | Description | Default |
| :---------- | :------- | :------- |
| ItemsSource | Gets or sets the content of the Breadcrumb | null |
| ItemTemplate | Gets or sets the template to display an item|  | 
| ItemClicked | Raised when a user interaction causes a jump to the CurrentItem.  | N/A |

Spec note: the V2 of Breadcrumb will add flyouts from chevrons to view children of a node.
Additional properties will be added to the API to enable this functionality. 

# API Details

```cs
runtimeclass BreadcrumbItemClickedEventArgs
{
    Int32 Index { get; };
    Object Item { get; };
}

unsealed runtimeclass BreadcrumbItem : Windows.UI.Xaml.Controls.ContentControl
{
    BreadcrumbItem();
}

unsealed runtimeclass BreadcrumbDropDownItem : Windows.UI.Xaml.Controls.ContentControl
{
    BreadcrumbDropDownItem();
}

[MUX_PROPERTY_CHANGED_CALLBACK(TRUE)]
[MUX_PROPERTY_CHANGED_CALLBACK_METHODNAME("OnPropertyChanged")]
unsealed runtimeclass Breadcrumb : Windows.UI.Xaml.Controls.Control
{
    Breadcrumb();

    Object DropDownItemTemplate{ get; set; };
    Object ItemsSource{ get; set; };
    Object ItemTemplate{ get; set; };

    event Windows.Foundation.TypedEventHandler<Breadcrumb, BreadcrumbItemClickedEventArgs> ItemClicked;

    static Windows.UI.Xaml.DependencyProperty DropDownItemTemplateProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty ItemsSourceProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty ItemTemplateProperty{ get; };
}

unsealed runtimeclass BreadcrumbItemAutomationPeer : Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer,
Windows.UI.Xaml.Automation.Provider.IInvokeProvider
{
    BreadcrumbItemAutomationPeer(MU_XC_NAMESPACE.BreadcrumbItem owner);
}

unsealed runtimeclass BreadcrumbDropDownItemAutomationPeer : Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer,
Windows.UI.Xaml.Automation.Provider.IInvokeProvider
{
    BreadcrumbDropDownItemAutomationPeer(MU_XC_NAMESPACE.BreadcrumbDropDownItem owner);
}
```

## Theme Resources

You can modify the look of a Breadcrumb by specifying Xaml resources in your app.
For more info, see the
[lightweight styling guide](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/xaml-styles#lightweight-styling).


| Name | Description | 
| :---------- | :------- | 
| BreadcrumbNormalForegroundBrush | Sets the foreground color | 
| BreadcrumbHoverForegroundBrush | Sets the hover foreground color |
| BreadcrumbPressedForegroundBrush | Sets the pressed foreground color |
| BreadcrumbDisabledForegroundBrush | Sets the disabled foreground color |
| BreadcrumbFocusForegroundBrush | Sets the focus foreground color |
| BreadcrumbCurrentNormalForegroundBrush | Sets the foreground color for the current item |
| BreadcrumbCurrentHoverForegroundBrush | Sets the hover foreground color for the current item |
| BreadcrumbCurrentPressedForegroundBrush | Sets the pressed foreground color for the current item |
| BreadcrumbCurrentDisabledForegroundBrush | Sets the disabled foreground color for the current item |
| BreadcrumbCurrentFocusForegroundBrush | Sets the focus foreground color for the current item |
| BreadcrumbBackgroundBrush | Sets the background color |
| BreadcrumbBorderBrush | Sets the border color |
| BreadcrumbItemThemeFontSize | Sets the item font size | 

# Inputs and Accessibility 

## UI Automation
Breadcrumb is identified as a [UIA Navigation Landmark](https://docs.microsoft.com/windows/win32/winauto/landmark-type-identifiers). Each BreadcrumbItem will use a [InvokePattern](!https://docs.microsoft.com/dotnet/framework/ui-automation/implementing-the-ui-automation-invoke-control-pattern) with control type [Button](https://docs.microsoft.com/windows/win32/winauto/uiauto-supportbuttoncontroltype). 

## Keyboard

* The user can tab into the Breadcrumb and tab out.
The first tab in gives focus to the first (highest-level) node (if the breadcrumb is crumbled, the ellipsis button instead).
* Once the Breadcrumb has focus, the user can use the left/right arrows to navigate from node to node.
* If the Breadcrumb is crumbled and focus is on the ellipsis
  * invoking with space/enter key will open the flyout from the ellipsis, focus will move to the first item in the flyout list.
  * The down/up keys will navigate between the flyout items. The ellipsis is not focusable while the flyout is open.
  * From any item in the flyout the user can use right arrow to go to the node right of the ellipsis. 

## Gamepad

* Spatial navigation to focus and select the nodes. A flyout can be opened using the A button. 

## Screen reader

* When the focus is on a node, the screen reader will announce the node
as well as "n of m" to convey which node in the path it is. 
* If the Breadcrumb has been crumbled due to resizing
  * the ellipsis is read as 'more' and is not counted for m (the total number of nodes)
  * the screen reader will announce 'collapsed' and 'expanded' for the flyout of crumbled nodes
  * within the flyout, announce 'x of y' 
