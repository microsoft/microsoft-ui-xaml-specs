# Background

The RadioButtons control is our answer to enabling better keyboarding and narrator support for RadioButton elements that need to be presented in a related list.

Currently, the only way to group single RadioButton elements today is through using a StackPanel and
individually labeling each RadioButton through its GroupName property in order for narrator to read out the
options somewhat understandably.

However, using that method still leaves gaps in the narrator experience and StackPanel does not behave the
way users expect when navigating the list via keyboard - RadioButtons was introduced to help solve those
issues.

# Description

RadioButtons is a new control that enables you to create groups of RadioButton elements easily, while also
correctly supporting keyboarding and narrator functionality.

# Examples

The following example demonstrates how to create a small collective group of RadioButton elements using the
RadioButtons control.

```xaml
<muxc:RadioButtons Header="App Mode" SelectedIndex="2">
    <x:String>Dark</x:String>
    <x:String>Light</x:String>
    <x:String>Windows Default</x:String>         
</muxc:RadioButtons>
```

![alt text](DefaultRadioButtonGroup.png)

## DataBinding with RadioButtons
An example of simple data binding of the ItemsSource with RadioButtons.

```xaml
<preview:RadioButtons Header="App Mode" ItemsSource="{x:Bind radioButtonItems}" />
```

```C#
public sealed partial class MainPage : Page
{
    public class OptionDataModel
    {
        public string Label;
        public override string ToString()
        {
            return Label;
        }
    }

    List<OptionDataModel> radioButtonItems;

    public MainPage()
    {
        this.InitializeComponent();

        radioButtonItems = new List<OptionDataModel>();
        radioButtonItems.Add(new OptionDataModel() { label = "Item 1" });
        radioButtonItems.Add(new OptionDataModel() { label = "Item 2" });
        radioButtonItems.Add(new OptionDataModel() { label = "Item 3" });
    }
}
```

## Multiple Columns
Some groups of RadioButton elements may want a multi-column layout. This is an example on how to set that up.

```xaml
<muxc:RadioButtons Header="App Mode" MaxColumns="3">
    <x:String>Column 1</x:String>
    <x:String>Column 2</x:String>
    <x:String>Column 3</x:String>
    <x:String>Column 1</x:String>
    <x:String>Column 2</x:String>
    <x:String>Column 3</x:String>
</muxc:RadioButtons>
```

![alt text](multicolumns.png)

# API Details

## RadioButtons

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

    Int32 MaxColumns;
    Object Header;

    static Windows.UI.Xaml.DependencyProperty ItemsSourceProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty ItemsProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty ItemTemplateProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty SelectedIndexProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty SelectedItemProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty MaxColumnsProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty HeaderProperty{ get; };
}
}
```

## RadioButtons Automation Peer

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

## RadioButtons Primitives

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
| Header | Places a text label above the group of RadioButton elements and is exposed to the UIA tree via the DataContext property on the RadioButtons control's ContentPresenter. |
| HeaderTemplate | The template that specifies the visualization of the header object |
| SelectedItem | Gets or sets the index of the selected item. Selection is denoted by that item's RadioButton icon being checked. |
| SelectedIndex | Gets or sets the selected item.|
| Items | Gets or sets an object source used to generate the content of the ItemsControl. All items placed within the RadioButtons control will get the content of a generated RadioButton. |
| ItemsSource | Gets or sets an object source used to generate the content of the ItemsControl. |
| ItemTemplate | The template set on any generated RadioButton elements within the RadioButtons control. If the root of the ItemTemplate is a RadioButton it will be used as the generated container. |
| MaxColumns | Defines the number of columns to divide the RadioButton group items into. |

## RadioButton Elements in the RadioButtons controls
Properties set on a RadioButton that is placed in a RadioButtons control are ignored by the narrator.

For example, in this scenario:

```xaml
<preview:RadioButtons Header="App Mode">
    <RadioButton GroupName="group test">test 1</RadioButton>
    <RadioButton GroupName="group test">test 2</RadioButton>
    <RadioButton GroupName="group test">test 3</RadioButton>
</preview:RadioButtons>
```

The **GroupName** property set on the RadioButton elements above will be ignored by the narrator/screen reader.
