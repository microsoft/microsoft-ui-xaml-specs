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
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->

# API Notes

### Notable Properties
The following properties similarly to how items do in ListView, however there are some behavioral differences due to the fact that these items function as grouped RadioButtons.

| Name | Description |
|:-:|:--|
| Header | Places a text label above the list of RadioButtons and is read out by the narrator on focus. |
| SelectedIndex | Gets or sets the index of the selected item. Selection is denoted by that item's RadioButton icon being checked. |
| Items | Gets or sets an object source used to generate the content of the ItemsControl. All items placed within the RadioButtons will get a RadioButton icon inline with the item. |
