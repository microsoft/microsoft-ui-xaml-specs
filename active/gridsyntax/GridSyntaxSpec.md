> See comments in Markdown for how to use this spec template

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

The existing syntax supports specialized functionality such as:
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
Grid's usability, intuitiveness, and developer satsifaction will be improved upon by utilizing a more succinct syntax, as will be described throughout this spec.
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
A new XAML language feature that allows read-only collection-typed properties to be edited by providing a comma-delimited list (or parentheses and comma-delimited for nested lists). In terms of Grid, this means that ColumnDefinitions and RowDefinitions properties are  able to be defined by providing a list of Width values and list of Height values, respectively. However, this feature is also applicable to other scenarios within the XAML language where collection-typed read-only properties are updated (see Remarks for examples of such scenarios). 

An additional update assigns the content property of ColumnDefinition to its Width property, and the content property of RowDefinition to its Height property.

# Examples
### New succinct syntax
The code below has the same functionality as the code shown above with the original syntax. It creates a grid and defines five different rows and columns, each with their own specific height/width, and adds them to the Grid. 
```xml
<Grid ColumnDefinitions="1*, 2*, Auto, *, 300"
      RowDefinitions="1*, Auto, 25, 14, 20">
</Grid>
```

### New succinct syntax for more complex types
The code below shows an example of how the syntax would be used for collection-typed properties that have more complex value types, i.e. Points. This shows how collections will be delimited by a comma as well as parentheses for these more complex types that require it.
```xml
<Foo PointList="(1,2), (9,4), (2,6)"/>
```

### Grid-specific syntax that uses new feature of assigned content properties
The code below has the same functionality as the code shown above with the original syntax, but uses the ColumnDefinition and RowDefinition content properties to assign heights to each row and widths to each column.
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
### Other Use Cases for Succinct Syntax
The succinct syntax was created with the goal of making Grid more intuitive. However, this new syntax will be implemented as a language feature and work properly for any read-only collection-typed property. The current syntaxes will still be functional for all other scenarios, as it will be with Grid. Examples of some of these scenarios include, but are not limited to:
#### CalendarView
Current Syntax to populate SelectedDates collection:
```xml
calendarView1.SelectedDates.Add(new DateTime(1977, 1, 5));
calendarView1.SelectedDates.Add(new DateTime(1997, 6, 19));
```
New, equally functional syntax using proposed XAML language feature:
```xml 
<CalendarView SelectedDates='1/5/1977, 6/19/1997' />
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
<ListView Items="Hello, world" />
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
the most recently defined Row Height and Column Width values will be used, (the ones defined line by line) rather than the values provided in the ColumnDefinitions and RowDefinitions lists. The user will not be alerted of this error.


# API Notes
<!-- Option 1: Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. For properties, specify the default value of the property if it
isn't the type's default (for example an int-typed property that doesn't default to zero.) -->

<!-- Option 2: Put these descriptions in the below API Details section,
with a "///" comment above the member or type. -->

# API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->

# Appendix
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->
