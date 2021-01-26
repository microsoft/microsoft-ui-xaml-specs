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
There’s currently no consistent and Fluent way to address the common UX pattern of a breadcrumb. This control is needed for situations where the user's navigation trail (in a file system or menu system) needs to be persistently visible. A Breadcrumb control is movitated by its use in many app scenarios and supporting developers in migrating from WPF.
<!-- This section and the appendix are the only sections that likely
do not get copied to docs.microsoft.com; they're just an aid to reading this spec. -->

<!-- If you're modifying an existing API, included a link here to the
existing page(s) -->

<!-- For example, this section is a place to explain why you're adding this API rather than
modifying an existing API. -->

<!-- For example, this is a place to provide a brief explanation of some dependent
area, just explanation enough to understand this new API, rather than telling
the reader "go read 100 pages of background information posted at ...". -->


# Description
<!-- Use this section to provide a brief description of the feature.
For an example, see the introduction to the PasswordBox control 
(http://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box). -->
A Breadcrumb control provides a persisting 'trail' that shows the user's navigation path. This is not the 'history' of the user's navigation, but the direct path of pages or folders from the root node to their current position. For situations where resizing means the full path can't be shown, the Breadcrumb will 'crumble' starting at the root node. In addition, the leaf node can be displayed as multiple lines of text if crumbling all the way rfom the root node to the leaf node's parent is not enough for a resizing scenario. 

## V1/V2 Breadcrumb

The work for Breadcrumb has been scoped to V1 and V2 stages of the API. In V1 Breadcrumb no chevrons are interactable to show children. V2 Breadcrumb will add functionality to have flyouts from chevrons for every node, to view children of that node. This spec focuses on V1 Breadcrumb, but keeping in mind that V1 should be positioned in a way to have V2 features smoothly added later. 

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
## Create a Breadcrumb
 ~~~~ 
     <local:Breadcrumb ItemsSource="{x:Bind Nodes}"> 
        <local:Breadcrumb.ItemTemplate> 
            <DataTemplate x:DataType="local:Node"> 
                <local:BreadcrumbItem Content="{x:Bind Label}"/> 
            </DataTemplate> 
        </local:Breadcrumb.ItemTemplate> 
    </local:Breadcrumb> 
~~~~

# Remarks
<!-- Explanation and guidance that doesn't fit into the Examples section. -->

<!-- APIs should only throw exceptions in exceptional conditions; basically,
only when there's a bug in the caller, such as argument exception.  But if for some
reason it's necessary for a caller to catch an exception from an API, call that
out with an explanation either here or in the Examples -->

# API Notes
<!-- Option 1: Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. For properties, specify the default value of the property if it
isn't the type's default (for example an int-typed property that doesn't default to zero.) -->

| Name | Description | Default |
| :---------- | :------- | :------- |
| ItemsSource | provides the content of the Breadcrumb | Null |
| ItemClicked | Raised when a user interaction causes a jump to the CurrentItem.  | N/A |

Note that V2 of Breadcrumb will add flyouts from chevrons to view children of a node. Additional properties will be added to the API to enable this functionality. 

<!-- Option 2: Put these descriptions in the below API Details section,
with a "///" comment above the member or type. -->

# API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->

## API
~~~~

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

 ~~~~

# Appendix
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->

# Inputs and Accessibility
## UI Automation Pattern
Breadcrumb will use the [InvokePattern](!https://docs.microsoft.com/en-us/dotnet/framework/ui-automation/implementing-the-ui-automation-invoke-control-pattern).

## Keyboard
* The user can tab into the Breadcrumb and tab out. The first tab in gives focus to the first (leftmost) node. 
Once the Breadcrumb has focus, the user can use: 
* the left/right arrows to navigate from node to node 

## Narrator
When the focus is on a node, Narrator will announce the name of the node as well as "n of m" to convey which node in the path it is. 

# Open Questions

## General
* Should developers be able to customize the crumbling behavior (ex: to crumble starting at the 2nd node onward?) Or even use an icon instead of ellipses? 
* Should developers be able to customize and address resizing situations where wrapping text in the leaf node does not answer their app scenario? Should the leaf node be able to crumble partially? 

## Design
* In V2 Breadcrumb, should the chevrons' interactability be indicated through design? Would the node-chevron spacing be the same or different for enabled vs disabled chevrons? 

## Input and Accessibility
* How should Breadcrumb's spacing/touch targets change for keyboard/mouse versus a touchscreen? 

### Keyboard
* the enter/space keys to jump to the current node?
* Should the down arrow (or alt-down) drop down the children of the current node? Potentially also space and enter to drop down the children? the escape key to close the drop down?
* the up/down arrows to navigate from child to child in the drop down?
* File Exploreer exhibits wrapping arrowing behavior - pressing right arrow on the rightmost node brings you back to the root. Is this a behavior WinUI Breadcrumb should enable?

### Narrator
* How should Narrator behavior in a scenario with crumbled nodes?


