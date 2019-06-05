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

Number box is a numerics only text control with support for validation, increment stepping, and computing inline calculations.

**Important APIs:** [NumberBox class](https://docs.microsoft.com/en-us/uwp/api/microsoft.ui.xaml.controls.numberbox) [TODO - generated with MSDN documentation on publication.]

## Is this the right control? 

Use a **NumberBox** to capture and display mathematical input. A **NumberBox** can also be enabled with basic calculator support to compute multiplication, division, addition, and subtraction across parenthetical order with standard operator precedence.

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

### Create a simple NumberBox

XAML
```XAML
<NumberBox AutomationProperties.Name="simple NumberBox" Header="Enter equation:" PlaceholderText="A + B * C" />
```

![](images/numberbox-placeholder-text.png)

### Enable calculation support

XAML
```XAML
<NumberBox AutomationProperties.Name="NumberBox for simple calculations" 
    Header="Enter equation:" 
    PlaceholderText="A + B * C" 
    AcceptsCalculation="True" />
```

![](images/numberbox-acceptscalculation.png)

### Add increment and decrement stepping

XAML
```XAML
<NumberBox AutomationProperties.Name="NumberBox for items in order" 
    StepSize="0.1"
    UpDownButtonsEnabled="True"
    HyperDragEnabled="True" 
    HyperScrollEnabled="True" />
```

![](images/numberbox-updownbuttons.png)

### Format display and output

XAML
```XAML
<NumberBox AutomationProperties.Name="NumberBox for solution components" 
    Header="Enter a percentage:"
    PlaceholderText="2/3" 
    MinValue="0"
    MaxValue="100" 
    AreLeadingZerosTrimmed="False"
    DecimalPrecision="5"
    DoesInputRound=False" />
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
runtimeclass NumberBoxValueChangedEventArgs
{
    String Text;
    Double Value
};

unsealed runtimeclass NumberBox : Windows.UI.Xaml.Controls.TextBox
{
    NumberBox();
    
    Double Value;
    
    Boolean AcceptsCalculation;
    Boolean UpDownButtonsEnabled;
    Boolean HyperDragEnabled;
    Boolean HyperScrollEnabled;
    
    Double StepSize;
  
    Boolean AreLeadingZerosTrimmed;
    Double DecimalPrecision;
    Boolean DoesInputRound;
    
    Double MinValue;
    Double MaxValue;
    

    NumberBoxTemplateSettings TemplateSettings{ get; };

    event Windows.Foundation.TypedEventHandler<NumberBox, NumberBoxValueChangedEventArgs> ValueChanged;

    static Windows.UI.Xaml.DependencyProperty ValueProperty{ get; };
    
    static Windows.UI.Xaml.DependencyProperty AcceptsCalculationProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty UpDownButtonsEnabledProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty HyperDragEnabledProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty HyperScrollEnabledProperty{ get; };   
    
    static Windows.UI.Xaml.DependencyProperty StepSizeProperty{ get; };
    
    static Windows.UI.Xaml.DependencyProperty AreLeadingZerosTrimmedProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty DecimalPrecisionProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty DoesInputRoundProperty{ get; };
    
    static Windows.UI.Xaml.DependencyProperty MinValueProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty MaxValueProperty{ get; };
}
```

## Appendix
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->

## Open Questions

* For touch/virtual keyboards, can we intelligently swap to the numerical input keyboard?

* Would anyone be against a validation system which automatically reverts unacceptable input back to the previous value while exposing events that would allow the developer to cancel this behavior and instead decide what to do/raise error indicators or messages?

*  Should we rely on the Xaml ControlTemplate for creating a NumberBox with disjoint UpDownButtons (i.e., this is not common enough to justify supporting out of the box) or should we include a property for setting whether the UpDownButtons appear appear contiguous vs. disjoint?

![one diagram showing UpDownButtons side-by-side and another diagram showing the down button to the left of the NumberBox and the up button to the right of the NumberBox](https://user-images.githubusercontent.com/16964652/58919787-3ae0fe80-86e4-11e9-8136-4c5b204825b0.png)


* Is there value in creating a preview for calculation results? @mdtauk created a few example visualizations: 

![NumberBox with a tool tip above to show a preview of the calculation results](https://user-images.githubusercontent.com/16964652/58919441-fbfe7900-86e2-11e9-8d2b-dd4dadfa74c5.png)

![NumberBox with a calculation in progress and grayed out text previewing the calculation results](https://user-images.githubusercontent.com/16964652/58919587-76c79400-86e3-11e9-886c-e57a9e64e48d.png)
