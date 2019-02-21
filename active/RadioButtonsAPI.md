# Background

RadioButtons is our answer to enabling better keyboarding and narrator support for RadioButtons that need to be presented in a related list.

Currently, the only way to group RadioButtons is through using a StackPanel and individually labeling each RadioButton through its GroupName property in order for narrator to read out the options somewhat understandably.

However, using that method still left gaps in the narrator experience and StackPanel does not behave the way a user would expect when navigating the list via keyboard - RadioButtons was introduced to help solve those issues.

# Description

RadioButtons is a class extension of ListView that enables you to create related lists of RadioButtons easily, while also fully supporting keyboarding and narrator functionality.

# Examples

The following example demonstrates how to create a small collective list of RadioButtons using RadioButtons.

```xaml
<RadioButtons Header="App Mode" SelectedIndex="2">
    <x:String>Dark</x:String>
    <x:String>Light</x:String>
    <x:String>Windows Default</x:String>         
</RadioButtons>
```

# API Details

### RadioButtons

```
[WUXC_VERSION_PREVIEW]
[webhosthidden]
[contentproperty("Items")]
[MUX_PROPERTY_CHANGED_CALLBACK(TRUE)]
unsealed runtimeclass RadioButtons : Windows.UI.Xaml.Controls.Control
{
    RadioButtons();

    Object ItemsSource;
    Windows.Foundation.Collections.IVector<Object> Items{ get; };
    Windows.UI.Xaml.DataTemplate ItemTemplate;

    Windows.UI.Xaml.DependencyObject ContainerFromItem(Object item);
    Windows.UI.Xaml.DependencyObject ContainerFromIndex(Int32 index);

    [MUX_DEFAULT_VALUE("-1")]
    Int32 SelectedIndex;
    Object SelectedItem;
    event Windows.UI.Xaml.Controls.SelectionChangedEventHandler SelectionChanged;

    [MUX_DEFAULT_VALUE("1")]
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
```

### RadioButtons Automation Pier

```
[WUXC_VERSION_PREVIEW]
[webhosthidden]
unsealed runtimeclass RadioButtonsListViewItemAutomationPeer : Windows.UI.Xaml.Automation.Peers.ListViewItemAutomationPeer
{
    RadioButtonsListViewItemAutomationPeer(MU_XCP_NAMESPACE.RadioButtonsListViewItem owner);
}
```

### RadioButtons Primitives

```
[WUXC_VERSION_PREVIEW]
[webhosthidden]
unsealed runtimeclass RadioButtonsListView : Windows.UI.Xaml.Controls.ListView
{
    RadioButtonsListView();
}

[WUXC_VERSION_PREVIEW]
[webhosthidden]
[default_interface]
unsealed runtimeclass RadioButtonsListViewItem : Windows.UI.Xaml.Controls.ListViewItem
{
    RadioButtonsListViewItem();
}
```

# API Notes

### Notable Properties
The following properties similarly to how items do in ListView, however there are some behavioral differences due to the fact that these items function as grouped RadioButtons.

| Name | Description |
|:-:|:--|
| Header | Places a text label above the list of RadioButtons and is read out by the narrator on focus. |
| SelectedIndex | Gets or sets the index of the selected item. Selection is denoted by that item's RadioButton icon being checked. |
| Items | Gets or sets an object source used to generate the content of the ItemsControl. All items placed within the RadioButtons will get a RadioButton icon inline with the item. |
