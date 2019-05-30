# Number box

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

## Background
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


## Description
<!-- Use this section to provide a brief description of the feature.
For an example, see the introduction to the PasswordBox control 
(http://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box). -->

Number box is a numerics only text control with support for input formatting, validation, basic calculation, and increment stepping.

**Important APIs:** [NumberBox class](https://docs.microsoft.com/en-us/uwp/api/microsoft.ui.xaml.controls.numberbox)

## Is this the right control? 

Use a **NumberBox** control to receive and display formatted numeric values such as currency, IP addresses, and telephone numbers. A **NumberBox** can also be enabled with basic calculator support to compute multiplication, division, addition, and subtraction across parenthetical order with standard operator precedence.

## Examples
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

### Create a NumberBox

XAML
```XAML
<NumberBox AutomationProperties.Name="simple NumberBox"/>
```

### Add placeholder text

XAML
```XAML
<NumberBox AutomationProperties.Name="simple NumberBox" Header="Enter equation:" PlaceholderText="A + B * C" />
```

### Enable calculation support

XAML
```XAML
<NumberBox AutomationProperties.Name="NumberBox for simple calculations" 
    Header="Enter equation:" 
    PlaceholderText="A + B * C" 
    AcceptsCalculation="True" />
```

### Add formatting

```XAML
<NumberBox AutomationProperties.Name="NumberBox for IP addresss" 
    PlaceholderText="000.000.000.000"
    FormatType="IPAddress" />
```

```XAML
<NumberBox AutomationProperties.Name="NumberBox for international telephone" 
    PlaceholderText="0 (123) 456-7890"
    FormatType="InternationalTelephone" />
```

```XAML
<NumberBox AutomationProperties.Name="NumberBox for custom input" 
    PlaceholderText="00 ft 00 in"
    CustomFormatString="## ft ## in"
    StripLeadingZeros="False" />
```

### Add increment and decrement stepping

XAML
```XAML
<NumberBox AutomationProperties.Name="NumberBox for items in order" 
    Text="2"
    StepSize="2"
    UpDownArrowsEnabled="True"
    UpDownDragEnabled="True" />
```

### Add a prefix and suffix 

XAML
```XAML
<NumberBox AutomationProperties.Name="NumberBox for IP address" 
    PlaceholderText="0.00" 
    PrefixText="$:" />
```

XAML
```XAML
<NumberBox AutomationProperties.Name="NumberBox for cents" 
    PlaceholderText="0" 
    SuffixText="MB" />
```

### Add mininum and maximum values

XAML
```XAML
<NumberBox AutomationProperties.Name="NumberBox for items in order" 
    PlaceholderText="1" 
    MinValue="1"
    MaxValue="99" />
```

## Remarks
<!-- Explanation and guidance that doesn't fit into the Examples section. -->

<!-- APIs should only throw exceptions in exceptional conditions; basically,
only when there's a bug in the caller, such as argument exception.  But if for some
reason it's necessary for a caller to catch an exception from an API, call that
out with an explanation either here or in the Examples -->

## API Notes
<!-- Option 1: Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. For properties, specify the default value of the property if it
isn't the type's default (for example an int-typed property that doesn't default to zero.) -->

<!-- Option 2: Put these descriptions in the below API Details section,
with a "///" comment above the member or type. -->

## API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->

```c++ 
enum NumberBoxFormatType
{
    IPAddress,
    InternationalTelephone,
    Currency,
};

unsealed runtimeclass NumberBox : Windows.UI.Xaml.Controls.TextBox
{
    NumberBox();
    
    String PrefixText;
    String SuffixText;
    
    Boolean AcceptsCalculation;
    Boolean UpDownArrowsEnabled;
    Boolean UpDownDragEnabled;
  
    NumberBoxFormatType FormatType;
    String CustomFormatString;
    Boolean StripLeadingZeros;
   
    Integer MinValue;
    Integer MaxValue;

    static Windows.UI.Xaml.DependencyProperty PrefixTextProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty SuffixTextProperty{ get; };
    
    static Windows.UI.Xaml.DependencyProperty AcceptsCalculationProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty UpDownArrowsEnabledProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty UpDownDragEnabledProperty{ get; };
    
    static Windows.UI.Xaml.DependencyProperty FormatTypeProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty CustomFormatStringProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty StripLeadingZerosProperty{ get; };

    static Windows.UI.Xaml.DependencyProperty MinValueProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty MaxValueProperty{ get; };
}
```

## Appendix
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->

## Appendix

* Should we support some basic format types or only custom format strings? 

