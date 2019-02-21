# Background

RadioButtons is our answer to enabling better keyboarding and narrator support for RadioButtons that need to be presented in a related list.

Currently, the only way to group RadioButtons is through using a StackPanel and individually labeling each RadioButton through its GroupName property in order for narrator to read out the options somewhat understandably.

However, using that method still left gaps in the narrator experience and StackPanel does not behave the way a user would expect when navigating the list via keyboard - RadioButtons was introduced to help solve those issues.

# Description

RadioButtons is a new control that enables you to create related lists of RadioButtons easily, while also fully supporting keyboarding and narrator functionality.

# Examples

The following example demonstrates how to create a small collective list of RadioButtons using RadioButtons.

```xaml
<RadioButtons Header="App Mode" SelectedIndex="2">
    <x:String>Dark</x:String>
    <x:String>Light</x:String>
    <x:String>Windows Default</x:String>         
</RadioButtons>
```

### DataBinding with RadioButtons
An example of simple data binding of the ItemsSource with RadioButtons.

```xaml
<preview:RadioButtons x:Name="RadioButtonGroup" Header="App Mode" ItemsSource="{x:Bind radioButtonItems}" />
```

```C#
public sealed partial class MainPage : Page
{
    public class RadioButtonItems
    {
        public string label;
        public override string ToString()
        {
            return label;
        }
    }

    List<RadioButtonItems> radioButtonItems;

    public MainPage()
    {
        this.InitializeComponent();

        radioButtonItems = new List<RadioButtonItems>();
        radioButtonItems.Add(new RadioButtonItems() { label = "Item 1" });
        radioButtonItems.Add(new RadioButtonItems() { label = "Item 2" });
        radioButtonItems.Add(new RadioButtonItems() { label = "Item 3" });

    }
}
```

### Multiple Columns
Some groups of RadioButtons may want a multi-column layout. This is an example on how to set that up.

```xaml
<preview:RadioButtons x:Name="RadioButtonGroup" Header="App Mode" MaximumColumns="3">
    <x:String>Column 1</x:String>
    <x:String>Column 1</x:String>
    <x:String>Column 2</x:String>
    <x:String>Column 2</x:String>
    <x:String>Column 3</x:String>
</preview:RadioButtons>
```

# API Details

### RadioButtons

```
namespace Microsoft.UI.Xaml.Controls
{
[webhosthidden]
[contentproperty("Items")]
unsealed runtimeclass RadioButtons : Windows.UI.Xaml.Controls.Control
{
    RadioButtons();

    Object ItemsSource;
    Windows.Foundation.Collections.IVector<Object> Items{ get; };
    Windows.UI.Xaml.DataTemplate ItemTemplate;

    Windows.UI.Xaml.DependencyObject ContainerFromItem(Object item);
    Windows.UI.Xaml.DependencyObject ContainerFromIndex(Int32 index);

    Int32 SelectedIndex;
    Object SelectedItem;
    event Windows.UI.Xaml.Controls.SelectionChangedEventHandler SelectionChanged;

    Int32 MaximumColumns;
    Object Header;

    static Windows.UI.Xaml.DependencyProperty ItemsSourceProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty ItemsProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty ItemTemplateProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty SelectedIndexProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty SelectedItemProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty MaximumColumnsProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty HeaderProperty{ get; };
}
}
```

### RadioButtons Automation Pier

```
namespace Microsoft.UI.Xaml.Automation.Peers
{
[webhosthidden]
unsealed runtimeclass RadioButtonsListViewItemAutomationPeer : Windows.UI.Xaml.Automation.Peers.ListViewItemAutomationPeer
{
    RadioButtonsListViewItemAutomationPeer(RadioButtonsListViewItem owner);
}
}
```

### RadioButtons Primitives

```
namespace Microsoft.UI.Xaml.Controls.Primitives
{
[webhosthidden]
unsealed runtimeclass RadioButtonsListView : Windows.UI.Xaml.Controls.ListView
{
    RadioButtonsListView();
}

[webhosthidden]
[default_interface]
unsealed runtimeclass RadioButtonsListViewItem : Windows.UI.Xaml.Controls.ListViewItem
{
    RadioButtonsListViewItem();
}
}
```

# API Notes

| Name | Description |
|:-:|:--|
| Header | Places a text label above the list of RadioButtons and is read out by the narrator on focus. |
| HeaderTemplate | Identifies the Header dependency property. |
| SelectedItem | Gets or sets the index of the selected item. Selection is denoted by that item's RadioButton icon being checked. |
| SelectedIndex | Gets or sets the selected item.|
| Items | Gets or sets an object source used to generate the content of the ItemsControl. All items placed within the RadioButtons will get a RadioButton icon inline with the item. |
| ItemsSource | Gets or sets an object source used to generate the content of the ItemsControl. |
| MaximumColumns | Defines the number of columns to divide the RadioButton group items into. |
