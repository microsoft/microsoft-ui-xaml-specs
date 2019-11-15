
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
The proposal for this feature can be found [here.](https://github.com/microsoft/microsoft-ui-xaml/issues/673) The existing API documentation for Grid can be found [here.](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.grid)


Grid is one of the most widely used controls, yet it has a noticeably long and repetitive syntax which leads to a tough learning curve for new developers. Currently, to create a Grid, developers must define each row height and column width on separate lines:
```xml
<Grid>
    <Grid.ColumnDefinitions>
          <ColumnDefinition Width="1*" />
          <ColumnDefinition Width="2*" />
          <ColumnDefinition Width="Auto" />
          <ColumnDefinition Width="*" />
          <ColumnDefinition Width="300" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
          <RowDefinition Height="1*" />
          <RowDefinition Height="Auto" />
          <RowDefinition Height="25" />
          <RowDefinition Height= "14" />
          <RowDefinition Height="20" />
    </Grid.RowDefinitions>
</Grid>
```

This code creates multiple ColumnDefinition objects and RowDefinitions objects and adds them to the Grid's ColumnDefinitions and RowDefinitions collections, respectively. ColumnDefinitions and RowDefinitions are read-only properties of Grid, and the syntax shown above is currently the only way to update these properties. Due to their read-only nature, existing typeconverters cannot be used with these properties. 

The existing syntax supports functionality such as:
- Declaring max/min height or width
```xml
<Grid MaxHeight="600" MaxWidth="600">
  ...
</Grid>
```

- Data binding for row/height values:
```xml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="{x:Bind sampleDataValue}" />
    ...
  </Grid.RowDefinitions>
</Grid>
```
Grid's usability, intuitiveness, and developer satsifaction will be improved upon by utilizing a more succinct syntax, as will be described throughout this spec. With Grid as the inspiration, other collection-type properties will be able to take advantage of a more succinct syntax as well and see the same benefits.
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


# Description
<!-- Use this section to provide a brief description of the feature.
For an example, see the introduction to the PasswordBox control 
(http://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box). -->
A new XAML language feature that allows the initialization of collection-type properties (including read-only properties) using element attribute syntax. We will also be assigning properties within Grid to make sure that this syntax works properly specifically for Grid's use cases.

1. When the parser encounters an element attribute that is assigning to a collection-type property, it recognizes that initialization of a collection from a string is being requested.
2. The parser tokenizes the attribute value (a comma-delimited string) into a series of substrings, each of which represents the initialization value of a single element of the collection
3. The parser will perform the usual "initialize an instance of a type from a string" operation, using the assigned ContentProperty with the string as an argument to create the object. See Section 6.6.4. Value Creation from Attribute Text in the [MS-XAML Spec](http://download.microsoft.com/download/0/a/6/0a6f7755-9af5-448b-907d-13985accf53e/[MS-XAML].pdf) for more details. 

In terms of Grid, this means that the ColumnDefinitions and RowDefinitions properties are able to be defined by providing a comma-delimited string of ColumnDefinitions (Width values) and a comma-delimited string of RowDefinitions (Height values), respectively. 
In order to support this language feature in Grid, a mechanism is needed that allows ColumnDefinition and RowDefinition objects to be createable from their Width/Height properties, respectively. To achieve this, the content property of ColumnDefinition is set to the Width property, and the content property of RowDefinition is set to the Height property.This will be used to make the new syntax (see below) fully functional.


# Examples
### New succinct syntax
The code below has the same functionality as the code shown above with the original syntax. It creates a grid and defines five different rows and columns, each with their own specific height/width, and adds them to the Grid. 
```xml
<Grid ColumnDefinitions="1*, 2*, Auto, *, 300"
      RowDefinitions="1*, Auto, 25, 14, 20">
</Grid>
```

### New succinct syntax for more complex types
The code below shows an example of how the syntax would be used for a collection-typed property that has a different value type. The ColorWheel's Color property is a collection of Color objects, but will be able to be initialized using a comma-separated string with this new language feature, as shown below: 

```xml
<ColorWheel Colors="Red, Green, Blue"/>
```

This shows how String values will be supported, even if they include commas or quotes inside of them. Attribute values (i.e `Words` in this example) can be enclosed with either ' or ", so whichever quotation mark is not used can be employed within the value itself, and does not need to be escaped as an XML character entity. The collection shown below has 4 elements, and 'hello, world!' is one element.

```xml
<Bar Words=" 'hello', 'world', 'hello, world!', 'it&apos;s a beautiful day'" />
```

### Grid-specific syntax using assigned ContentProperty 
The code below has the same functionality as the code shown above with the original syntax, but uses the ColumnDefinition and RowDefinition content property assignments to write it in the following way.
```xml
<Grid>
    <Grid.ColumnDefinitions>
          <ColumnDefinition>1*</ColumnDefinition>
          <ColumnDefinition>2*</ColumnDefinition>
          <ColumnDefinition>Auto</ColumnDefinition>
          <ColumnDefinition>*</ColumnDefinition>
          <ColumnDefinition>300</ColumnDefinition>
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
          <RowDefinition>1*</RowDefinition>
          <RowDefinition>Auto</RowDefinition>
          <RowDefinition>25</RowDefinition>
          <RowDefinition>14</RowDefinition>
          <RowDefinition>20</RowDefinition>
    </Grid.RowDefinitions>
</Grid>
```
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


# Remarks
<!-- Explanation and guidance that doesn't fit into the Examples section. -->

<!-- APIs should only throw exceptions in exceptional conditions; basically,
only when there's a bug in the caller, such as argument exception.  But if for some
reason it's necessary for a caller to catch an exception from an API, call that
out with an explanation either here or in the Examples -->
### Syntax Details and Corner Cases
The new succinct syntax is formed by assigning a collection-type property to a collection of values. This collection of values is encased between a set of double quotes. String values, as well as any other more complex type that may require commas within it (i.e. Point objects, sets), are encased between a set of single quotes. This ensures that string values may contain commas, and Points, sets, or any other kind of nested collection object may be supported. To use a single quote within a complex value that is encased in single quotes (i.e., to put a single quote or apostrophe inside of a string value), the HTML entity (&apos;) should be used to escape that character.

### Other Use Cases for Succinct Syntax
The succinct syntax was created with the goal of making Grid more intuitive. However, this new syntax will be implemented as a language feature and work properly for any collection-type property. The current syntaxes will still be functional for all other scenarios, as it will be with Grid. Examples of some of these scenarios include, but are not limited to:
#### CalendarView
Current Syntax to populate SelectedDates collection:
```xml
calendarView1.SelectedDates.Add(new DateTime(1977, 1, 5));
calendarView1.SelectedDates.Add(new DateTime(1997, 6, 19));
```
New, equally functional syntax using proposed XAML language feature:
```xml 
<CalendarView SelectedDates="'1/5/1977', '6/19/1997'" />
```

#### ListView
Current Syntax to populate ListView Items collection:
```xml
<ListView>
    <ListView.Items>
        <x:string>Hello</x:string>
        <x:string>World</x:string>
    </ListView.Items>
</ListView>
```
New, equally functional syntax using proposed XAML language feature:
```xml 
<ListView Items="'Hello', 'world'" />
```

### User Error
If a developer uses both syntaxes in their Grid definition, i.e.
```xml
//Warning: Incorrect syntax
<Grid ColumnDefinitions="1*, 2*, Auto, *, 300" RowDefinitions="1*, Auto, 25, 14, 20">
    <Grid.ColumnDefinitions>
          <ColumnDefinition Width="1*" />
          <ColumnDefinition Width="2*" />
          <ColumnDefinition Width="Auto" />
          <ColumnDefinition Width="*" />
          <ColumnDefinition Width="300" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
          <RowDefinition Height="1*" />
          <RowDefinition Height="Auto" />
          <RowDefinition Height="25" />
          <RowDefinition Height= "14" />
          <RowDefinition Height="20" />
    </Grid.RowDefinitions>
</Grid>
```
a duplicate property error will be thrown. This is already implemented for read/write properties, but will be implemented for read-only properties such as these as well. 

<!-- Option 1: Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. For properties, specify the default value of the property if it
isn't the type's default (for example an int-typed property that doesn't default to zero.) -->

<!-- Option 2: Put these descriptions in the below API Details section,
with a "///" comment above the member or type. -->

# API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->

**ColumnDefinition API**
```csharp
[contract(Windows.Foundation.UniversalApiContract, 1)]
[webhosthidden]

/// Sets the ColumnDefinition's content property to the Width property.
[contentproperty("Width")]

[static_name("Windows.UI.Xaml.Controls.IColumnDefinitionStatics", 06b0d728-d044-40c6-942e-ae60eac74851)]
[interface_name("Windows.UI.Xaml.Controls.IColumnDefinition", f7f1b229-f024-467f-970a-7e705615db7b)]
runtimeclass ColumnDefinition : Windows.UI.Xaml.DependencyObject
{
    ColumnDefinition();
    Windows.UI.Xaml.GridLength Width;
    Double MaxWidth;
    Double MinWidth;
    Double ActualWidth{ get; };
    static Windows.UI.Xaml.DependencyProperty WidthProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty MaxWidthProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty MinWidthProperty{ get; };
};
```

**RowDefinition API**
```csharp
[contract(Windows.Foundation.UniversalApiContract, 1)]
[webhosthidden]

/// Sets the RowDefinition's content property to the Height property.
[contentproperty("Height")]

[static_name("Windows.UI.Xaml.Controls.IRowDefinitionStatics", 5adf3fe5-2056-4724-94d6-e4812b022ec8)]
[interface_name("Windows.UI.Xaml.Controls.IRowDefinition", 4abae829-d80c-4a5e-a48c-f8b3d3b6533d)]
runtimeclass RowDefinition : Windows.UI.Xaml.DependencyObject
{
    RowDefinition();
    Windows.UI.Xaml.GridLength Height;
    Double MaxHeight;
    Double MinHeight;
    Double ActualHeight{ get; };
    static Windows.UI.Xaml.DependencyProperty HeightProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty MaxHeightProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty MinHeightProperty{ get; };
};
```

# Appendix
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->

### Syntax Considerations and Rationale
In developing the syntax, the team considered developer familiarty and patterns as much as possible to ensure that the Grid learning curve could be decreased. One of the considerations was how similar the syntax should be to JSON, as that's a popular notation style that often addresses the same problems that XAML does. 

JSON defines their arrays as: ```"words" : ["apple", "banana", "cherry"]```

XAML defines a collection-typed property as: ```<Words="'Apple', 'Banana', 'Cherry'"/>```

The syntax that we chose encloses strings individually in quotes, as JSON does, but it does so in a way that is more consistent with existing XAML style and XAML functions. An example of this syntax used elsewhere in XAML is for the x:Bind syntax:

```xml <TextBlock Text="{x:Bind VM.MyText, FallbackValue='Hello, world', Mode=TwoWay}" />```

This uses a comma as a delimiter, but also allows strings to have commas within them. For these reasons, we chose to use the syntax in which a collection can be delimited by commas but have more complex values be encased within single quotes.


### Release Details
The xmlns will not be updated for this XAML language feature addition, i.e. there will be no formal update to the XAML language. Visual Studio, however, will have to be updated to accept the new syntax structure. This feature will be released with WinUI and not with the XAML SDK.

### Data and Intelligence Metrics
#### P0: Feature Key Performance Indictors
* Syntax has increased developer/consumer satisfaction
    * KPI: At least 80% of new apps released to Windows store after release of feature are using the new syntax, and 50% of developers express a positive reaction to this change.
    * Measurement: XAML Survey if available or survey through XAML discussions/other relevant channel.
    * Measurement: How many times Grid has been implemented using the new syntax in published Windows apps.
    
* Implementation of syntax change has strengthened brand loyalty among customers/developers by assuring customers/developers that their voices are being heard and their quality of life is being considered
    * KPI: Multiple new commenters on this thread have gone to be active on other threads, and all experienced commenters from this thread have gone on to create at least one other thread or issue on the WinUI repo. 
    * Measurement: Number of *new* commenters on this thread (i.e. made their first WinUI Github comment/contribution on this thread), and how many of them went on to be active on other WinUI or Microsoft threads.
    * Measurement: Number of the commenters from this thread went on to create other issues after this one was resolved (beleiving that their voices would be heard!).

#### P1: Feature Performance Indictators
* Syntax has decreased Grid's learning curve
    * KPI: A slight increase (<5%) of apps released to the Windows store were created by new developers
    * Measurement: How many apps using this new syntax were created by new/first-time developers

# Open Questions
- Is this a read-only property, or does it work for any type of property, including read-only?
- What are the important tooling details that should be included for implementation? Some previous concerns to think about..
    - Parser/designer understanding coldefs and rowdefs
    - Should be easier since we are implementing without a type converter (using content property assignment instead). Will we face any challenges in the CreateFromString side? i.e. would we be creating unvalidateable attributes?
    - Need the ability to see what XAML version developer is using
- Finalized decision (maybe just need a refresher on this): How are we sending out this update - updating xmlns, formal update to XAML language, ms-xaml spec update?
