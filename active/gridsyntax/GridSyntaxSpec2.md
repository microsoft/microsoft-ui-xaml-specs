

# Background
_This adds a new short-hand Xaml syntax feature that makes it much easier to define a Grid in Xaml markup. The proposal for this feature can be found [here.](https://github.com/microsoft/microsoft-ui-xaml/issues/673) The existing API documentation for Grid can be found [here.](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.grid)_

_This file is an [updated copy](./GridSyntaxSpec.md), split off to make all of the lines commentable in PR. But this will be merged back when complete._

The Xaml Grid  is one of the most widely used panels, yet it has a noticeably long and repetitive syntax in markup, which leads to a tough learning curve for new developers. Currently, to create a Grid, developers must define each row height and column width with separate XML tags:

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

The code in that example creates the ColumnDefinition objects and RowDefinition objects and adds them to the Grid's [Grid.ColumnDefinitions](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid.ColumnDefinitions) and [Grid.RowDefinitions](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid.RowDefinitions) collections, respectively.  This is currently the only way to set these properties in markup.

The new features in this spec allow for this equivalent syntax:

```xml
<Grid ColumnDefinitions="1*, 2*, Auto, *, 300" RowDefinitions="1*, Auto, 25, 14, 20" />
```

This new syntax is enabled by a new Xaml language feature and associated APIs that can be used on any collection-typed property, and includes updates to Grid to use these APIs.

# Description

We've introduced a new Xaml language feature that allows the initialization of collection-type properties (including read-only properties) using Xaml attribute syntax. We've also updated the Grid APIs to make sure that this syntax works properly in particular for Grid's use cases.

Grid initialization in Xaml markup has always required a lot of XML:

```xml
<Grid>
    <Grid.ColumnDefinitions>
          <ColumnDefinition Width="Auto" />
          <ColumnDefinition Width="*" />
    </Grid.ColumnDefinitions>
</Grid>
```

You can now accomplish the same thing in a single tag:

```xml
<Grid ColumnDefinitions="Auto, *">
```

Before this change, if you write that shorter Xaml markup, the  Xaml loader would try to convert the string `Auto, *` into a [ColumnDefinitionCollection](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ColumnDefinitionCollection) object, as that's the type of the [Grid.ColumnDefinitions](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid.ColumnDefinitions) property. That fails because there is no such conversion available. (And it wouldn't help if there was, because the property is read-only.)

But the Xaml syntax rules now support this scenario by recognizing that the target property is a collection type, and that there's no conversion from string available, and then by treating the attribute value as a comma-separated list of collection items. The items of the collection are of type [ColumnDefinition](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ColumnDefinition), so a check is made to see if strings can be converted to ColumnDefinition instances. Since they can, the Xaml loader creates those instances and adds them to the collection.

## Role of the [ContentProperty] attribute

In order for a ColumnDefinition (or RowDefinition) to be created from a string there is a second new Xaml syntax rule: if a type can't be created from string, but the type's [[ContentProperty](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Markup.ContentPropertyAttribute)] property can, the type is created and the property set.

About this "content property", it's not literally a property named "content" (although it can be and [is sometimes](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentControl.Content)). This is essentially the default property of a class, and is marked with the [ContentProperty] attribute.

For example the [SolidColorBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush) is defined roughly like this (in C# terms):

```cs
[ContentProperty(Name = "Color")]
public sealed class SolidColorBrush : Brush
{
    ...
    public Color Color { get; set; }
}
```

Because [SolidColorBrush.Color](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush.Color) is marked as the [ContentProperty] (and because a Color can be created from a string), you've always been able to write this short-hand Xaml:

```xml
<SolidColorBrush>Red</SolidColorBrush>
```

If it weren't for the [ContentProperty] on SolidColorBrush, you'd have to use the equivalent full syntax (and that's still valid to use if you prefer):

```xml
<SolidColorBrush>
    <SolidColorBrush.Color>
        Red
    </SolidColorBrush.Color>
</SolidColorBrush>
```

## Making things work for Grid

Back to Grid, with the new syntax feature and with ColumnDefinition.Width now marked as the [ContentProperty]:

```cs
[ContentProperty(Name = "Width")]
public sealed class ColumnDefinition : DependencyObject
{
        public GridLength Width { get; set; }
}
```

you can now write

```xml
<ColumnDefinition>Auto</ColumnDefinition>
```

(There's already a converter to create a GridLength from a string.)

Putting that all together, back to the original goal:

```xml
<Grid ColumnDefinitions="Auto, *">
```

Two ColumnDefinition objects are created and added to the Grid's ColumnDefinitions collection property, which is read-only but defaults to an empty collection. The first item has its Width set to GridLength.Auto, the second has its Width set to 1 GridUnitType.Star.


## Special characters and escaping

The XML attribute value is interpreted as a comma-separated list of items, which creates a problem if you want to use a comma an item value. You can solve this using quotes, for example, this initializes a collection with two items:

```xml
<WordWheel Words = " 'Hello, world', 'how is the weather?' " />
```

(Xaml markup extensions have the same comma issue and the same quote syntax. In general the syntax rules follow those of markup extensions.)

Alternatively you can use a backslash as an escape character before the comma:

```xml
Words = " I weigh 90\,7 KG    "
```

Note that here, like everywhere in XML, we're using "opposite quotes" (whichever kind of quote you use to delineate the attribute value, don't use it _in_ the attribute value, except as an entity). So these are equvalent:

```xml
Words = " 'Hello', 'world' "
Words = ' "Hello", "world" '
```

And if you want to have an opposite quote in an item value, it too can be escaped:

```xml
Words = " 'How\'s the weather', 'It\'s sunny'  "
```

## Don't mix syntaxes

In Xaml today you can't set a property twice. For example this isn't valid because Background is getting set twice:

```xml
<Button Content='Click' Background='Red'>
    <Button.Background>
        <SolidColorBrush Color='Blue' />
    </Button.Background>
</Button>
```

That rule still applies, so the following is an error:

```xml
//Warning: Incorrect syntax
<Grid ColumnDefinitions="Auto,*,Auto">
    <Grid.ColumnDefinitions>
          <ColumnDefinition Width="*" />
          <ColumnDefinition Width="Auto" />
          <ColumnDefinition Width="*" />
    </Grid.ColumnDefinitions>
</Grid>
```

## Things that don't change

There are some interesting details with collection properties that don't change with the new syntax:
* Items are simply _added_ to the collection, so if the collection defaults to non-empty, it will have more items in it than what you see in the markup.
* The collection property must be non-null. If it's null, then the value being assigned to it must be the type of the collection, and so this new syntax isn't applicable.


## Other Use Cases

This more succinct syntax for initializing collections was created with the goal of making Grid easier and more intuitive. However, this new syntax will be implemented as a language feature and work properly for any collection-type property. The current syntaxes will still be functional for all other scenarios, as it will be with Grid. Some more examples:

### ColorWheel

The code below shows an example of how the syntax could be used for a property that's (in C# terms) an IList<Color>. 

```xml
<ColorWheel Colors="Red, Green, Blue"/>
```

### PointCollection

```xml
<Polygon Points = " '0,0', '0,1', '1,1'   ">
```

### CalendarView

Initialize the selected dates on a calendar:

```xml 
<CalendarView SelectedDates="'1/5/1977', '6/19/1997'" />
```

### ListView

Previous syntax to populate ListView Items collection (which still works):

```xml
<ListView>
    <ListView.Items>
        <x:string>Hello</x:string>
        <x:string>World</x:string>
    </ListView.Items>
</ListView>
```

New syntax:

```xml 
<ListView Items="'Hello', 'world'" />
```

## MS-XAML

| The following needs to be proposed into [MS-XAML](https://docs.microsoft.com/en-us/openspecs/microsoft_domain_specific_languages/ms-xaml/fd13e8ed-dd75-4767-92cf-e418d2c39817).

If a value cannot be created from a string, but the value can be created, and the value has a ContentProperty that can be created from string, the value is created and the string assigned to the ContentProperty. See Section 6.6.4. Value Creation from Attribute Text in the [MS-XAML Spec](http://download.microsoft.com/download/0/a/6/0a6f7755-9af5-448b-907d-13985accf53e/[MS-XAML].pdf) for more details.

When a string value is set to a collection-type property, and either the collection isn't createable from string or the property is ready only, the string is treated as a comma-delimited string, parsed into substrings, each of which is used to create a value of the collection item type, and added to the collection. If the property being assigned to already has a value, i.e. its collection already has items in it, the intialized instances will be appended to the existing collection.



# Examples

| Spec note: all of the syntax details are in the above Discussion section. The examples here are intended for the Grid docs.

## Grid with basic rows and definitions

This example creates a grid, defines five different rows and columns (each with their own specific height/width), and adds them to the Grid. 

```xml
<Grid ColumnDefinitions="1*, 2*, Auto, *, 300" RowDefinitions="1*, Auto, 25, 14, 20">
    <!-- ... -->
</Grid>
```

## Grid with custom column and row features

This example is equivalent to the previous one, except one column has a minimum width and one row has a minimum height, which requires the more complete property element syntax.

```xml
<Grid>
    <Grid.ColumnDefinitions>
          <ColumnDefinition Width="1*" MinWidth="100" />
          <ColumnDefinition Width="2*" />
          <ColumnDefinition Width="Auto" />
          <ColumnDefinition Width="*" />
          <ColumnDefinition Width="300" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
          <RowDefinition Height="1*" MinHeight="100" />
          <RowDefinition Height="Auto" />
          <RowDefinition Height="25" />
          <RowDefinition Height= "14" />
          <RowDefinition Height="20" />
    </Grid.RowDefinitions>
</Grid>
```


# Remarks

# API Details

## ColumnDefinition API

```cs
[webhosthidden]

// vvv
[contentproperty("Width")] // Sets the ColumnDefinition's content property to the Width property.
// ^^^

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

## RowDefinition API

```cs
[webhosthidden]

// vvv
[contentproperty("Height")] // Sets the RowDefinition's content property to the Height property.
// ^^^

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

## Alternatives

## Syntax Considerations and Rationale
In developing the syntax, the team considered developer familiarty and patterns as much as possible to ensure that the Grid learning curve could be decreased. One of the considerations was how similar the syntax should be to JSON, as that's a popular notation style that often addresses the same problems that Xaml does. 

JSON defines their arrays as: ```"words" : ["apple", "banana", "cherry"]```

Xaml defines a collection-typed property as: ```<Words="'Apple', 'Banana', 'Cherry'"/>```

The syntax that we chose encloses strings individually in quotes, as JSON does, but it does so in a way that is more consistent with existing Xaml style and Xaml functions. An example of this syntax used elsewhere in Xaml is for the x:Bind syntax:

```xml <TextBlock Text="{x:Bind VM.MyText, FallbackValue='Hello, world', Mode=TwoWay}" />```

This uses a comma as a delimiter, but also allows strings to have commas within them. For these reasons, we chose to use the syntax in which a collection can be delimited by commas but have more complex values be encased within single quotes.


## Release Details
The xmlns will not be updated for this Xaml language feature addition, i.e. there will be no formal update to the Xaml language. Visual Studio, however, will have to be updated to accept the new syntax structure. This feature will be released with WinUI and not with the Xaml SDK.

## Tooling/Visual Studio Considerations
In terms of usage of this new Xaml language feature in Visual Studio, VS needs a way to recognize the current Xaml version that the developer is using. This will be likely be done by accessing metadata or accessing an attached features.xml file - the VS team is still in discussion as to which method will be used, but the overall cost is not high. 

## Data and Intelligence Metrics
### P0: Feature Key Performance Indictors
* Syntax has increased developer/consumer satisfaction
    * KPI: At least 80% of new apps released to Windows store after release of feature are using the new syntax, and 50% of developers express a positive reaction to this change.
    * Measurement: Xaml Survey if available or survey through Xaml discussions/other relevant channel.
    * Measurement: How many times Grid has been implemented using the new syntax in published Windows apps.
    
* Implementation of syntax change has strengthened brand loyalty among customers/developers by assuring customers/developers that their voices are being heard and their quality of life is being considered
    * KPI: Multiple new commenters on this thread have gone to be active on other threads, and all experienced commenters from this thread have gone on to create at least one other thread or issue on the WinUI repo. 
    * Measurement: Number of *new* commenters on this thread (i.e. made their first WinUI Github comment/contribution on this thread), and how many of them went on to be active on other WinUI or Microsoft threads.
    * Measurement: Number of the commenters from this thread went on to create other issues after this one was resolved (beleiving that their voices would be heard!).

### P1: Feature Performance Indictators
* Syntax has decreased Grid's learning curve
    * KPI: A slight increase (<5%) of apps released to the Windows store were created by new developers
    * Measurement: How many apps using this new syntax were created by new/first-time developers

