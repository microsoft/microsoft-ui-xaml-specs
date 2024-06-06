
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

[Grid](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.grid?view=winrt-18362) has ColumnDefinitions and RowDefinitions properties. The values for these properties are a collection of [ColumnDefinition](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.columndefinition?view=winrt-18362) and [RowDefinition](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.rowdefinition?view=winrt-18362) objects, respectively. Currently, RowDefinition and ColumnDefinition constructors do not take any arguments. Due to this, defining a ColumnDefinition or RowDefinition can be lengthy and unintuitive, with the steps as follows:
1. Create a Column or RowDefinition object with the blank constructor.
2. Define a GridLength object with the desired value for the Height or Width property of the Row/ColumnDefinition.
3. Assign the GridLength object to the Height or Width property of the Row/ColumnDefinition. 

This spec sets out to change that process, and therefore sets out to change the [RowDefinition](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RowDefinition) and [ColumnDefinition](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.columndefinition) APIs.

This new feature will allow developers to define their column widths and row heights within the column and row definitions themselves, not only making the code-behind easier to write, but making it cleaner and more understandable to read as well. 

# Description
<!-- Use this section to provide a brief description of the feature.
For an example, see the introduction to the PasswordBox control 
(http://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box). -->

ColumnDefinition and RowDefinition constructors will be overloaded to take in one or two arguments. The first overloaded constructor for `ColumnDefinition()` will take only a double-type argument called Width (Height for `RowDefinition()`). The second overloaded constructor for ColumnDefinition() will take a double-type argument Width (Height for RowDefinition()) and a [GridUnitType](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.gridlength.gridunittype) argument.

These overloaded constructors take the same arguments as the [GridLength](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.GridLength) constructor does - a double and a GridUnitType. In turn, feeding these same arguments to a RowDefinition or ColumnDefinition constructor will create a GridLength object and assign it to the ColumnDefinition's Width property or the RowDefinition's Height property behind the scenes. 

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
Current syntax to define a Grid with one column in code-behind:
```csharp
var grid = new Grid();
var column = new ColumnDefinition();
column.Width = new GridLength(1.0, GridUnitType.Star);
grid.ColumnDefinitions.Add(column);
```

or

```csharp
var column = new ColumnDefinition
{
    Width = new GridLength(1.0, GridUnitType.Star)
};
```


New syntax to define a Grid with one column in code-behind:
```csharp
var grid = new Grid();
grid.ColumnDefinitions.Add(new ColumnDefinition(1.0, GridUnitType.Star)); // Width="1*"
```

New syntax to define a Grid with one simple column in code-behind, where the GridUnitType is a pixel:
```csharp
grid.ColumnDefinitions.Add(new ColumnDefinition(500.0)); // Width="500px"
```

<!-- # Remarks -->
<!-- Explanation and guidance that doesn't fit into the Examples section. -->

<!-- APIs should only throw exceptions in exceptional conditions; basically,
only when there's a bug in the caller, such as argument exception.  But if for some
reason it's necessary for a caller to catch an exception from an API, call that
out with an explanation either here or in the Examples -->
If defining a column or row with a GridUnitType of Auto, the value in the Width/Height parameter should be ignored - Auto should override the specified Width/Height value. 

For ColumnDefinitions or RowDefinitions that have `NaN` as their value for Height or Width in their constructor, an error should be thrown.


# API Notes
ColumnDefinition constructor will be overloaded to take a double `pixelWidth` OR take two arguments consisting of a double `width` and a GridUnitType `type`:

```csharp
public ColumnDefinition(double pixelWidth) { ... }
```

```csharp
public ColumnDefinition(double width, GridUnitType type) { ... }
```

RowDefinition constructor will be overloaded to take a double `pixelHeight` OR take two arguments consisting of a double `height` and a GridUnitType `type`:

```csharp
public RowDefinition(double pixelHeight) { ... }
```

```csharp
public RowDefinition(double height, GridUnitType type) { ... }
```

# API Details
```csharp
//RowDefinition API:
[contract(Windows.Foundation.UniversalApiContract, 1)]
[webhosthidden]
[static_name("Windows.UI.Xaml.Controls.IRowDefinitionStatics", 5adf3fe5-2056-4724-94d6-e4812b022ec8)]
[interface_name("Windows.UI.Xaml.Controls.IRowDefinition", 4abae829-d80c-4a5e-a48c-f8b3d3b6533d)]
runtimeclass RowDefinition
    : Windows.UI.Xaml.DependencyObject
{
    // vvvv
    RowDefinition(double height, GridUnitType type);
    RowDefinition(double pixelHeight);
    // ^^^^
    Windows.UI.Xaml.GridLength Height;
    Double MaxHeight;
    Double MinHeight;
    Double ActualHeight{ get; };
    static Windows.UI.Xaml.DependencyProperty HeightProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty MaxHeightProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty MinHeightProperty{ get; };
};

//ColumnDefinition API:
[contract(Windows.Foundation.UniversalApiContract, 1)]
[webhosthidden]
[static_name("Windows.UI.Xaml.Controls.IColumnDefinitionStatics", 06b0d728-d044-40c6-942e-ae60eac74851)]
[interface_name("Windows.UI.Xaml.Controls.IColumnDefinition", f7f1b229-f024-467f-970a-7e705615db7b)]
runtimeclass ColumnDefinition
    : Windows.UI.Xaml.DependencyObject
{
    // vvvv
    ColumnDefinition(double width, GridUnitType type);
    ColumnDefinition(double pixelWidth);
    // ^^^^
    Windows.UI.Xaml.GridLength Width;
    Double MaxWidth;
    Double MinWidth;
    Double ActualWidth{ get; };
    static Windows.UI.Xaml.DependencyProperty WidthProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty MaxWidthProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty MinWidthProperty{ get; };
};
```

<!-- # Appendix -->
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->
