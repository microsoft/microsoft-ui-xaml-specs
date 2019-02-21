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

<!-- This section and the appendix are the only sections that likely
do not get copied to docs.microsoft.com; they're just an aid to reading this spec. -->

<!-- If you're modifying an existing API, included a link here to the
existing page(s) -->

<!-- For example, this section is a place to explain why you're adding this API rather than
modifying an existing API. -->

<!-- For example, this is a place to provide a brief explanation of some dependent
area, just explanation enough to understand this new API, rather than telling
the reader "go read 100 pages of background information posted at ...". -->

RadioButtons is our answer to enabling better keyboarding and narrator support for RadioButtons that need to be presented in a related list.

Currently, the only way to group RadioButtons is through using a StackPanel and individually labeling each RadioButton through its GroupName property in order for narrator to read out the options somewhat understandably.

However, using that method still left gaps in the narrator experience and StackPanel does not behave the way a user would expect when navigating the list via keyboard - RadioButtons was introduced to help solve those issues.

# Description
<!-- Use this section to provide a brief description of the feature.
For an example, see the introduction to the PasswordBox control
(http://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box). -->

RadioButtons is a class extension of ListView that enables you to create related lists of RadioButtons easily, while also fully supporting keyboarding and narrator functionality.

# Examples
<!-- Use this section to explain the features of the API, showing
example code with each description. The general format is:
  feature explanation,
  example code
  feature explanation,
  example code
  etc.-->

<!-- As an example of this section, see the Examples section for the PasswordBox control
(https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box#examples). -->

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
<!-- Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. -->

### Notable Properties
The following properties similarly to how items do in ListView, however there are some behavioral differences due to the fact that these items function as grouped RadioButtons.

| Name | Description |
|:-:|:--|
| Header | Places a text label above the list of RadioButtons and is read out by the narrator on focus. |
| SelectedIndex | Gets or sets the index of the selected item. Selection is denoted by that item's RadioButton icon being checked. |
| Items | Gets or sets an object source used to generate the content of the ItemsControl. All items placed within the RadioButtons will get a RadioButton icon inline with the item. |
