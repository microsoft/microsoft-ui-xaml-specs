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

### Behavioral Components

| Property | Notes |
|:---:|:---|
| Value/Text Changed | * When Text is changed via codebehind or by user input ("Enter" is pressed or the NumberBox loses focus) the NumberBox will be prompted to update its Text and Value properties. A TextChanged event will be raised that will exposes the new Text and the old Value and allows the developer to intercept the and manipulate these properties (such as for manually handling validation error). Value will then be updated to the Text input converted to a Double. <br><br> * Conversely, code behind updates to Value will raise a ValueChanged event that will exposes the new Value and the old Text and allows the developer to intercept the and manipulate these properties (such as for manually handling validation error). Text will then be updated to the Value input converted to a String.    |
| Validation | * Input that is non-numerical forumalic or exceeds the min/max values will trigger an [Input Validation warning]( https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/lucashaines/inputvalidation/active/InputValidation/InputValidation.md). <br><br> * The developer can intercept the ValueChanged or TextChanged events (which exposes the changed property and the one to be updated) and manually manipulate invalid input before an [Input Validation warning]( https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/lucashaines/inputvalidation/active/InputValidation/InputValidation.md) is displayed. <br><br> * Enabling the IsInvalidInputOverwritten property will revert invalid input to the last valid state without displaying [Input Validation warning]( https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/lucashaines/inputvalidation/active/InputValidation/InputValidation.md). E.g., if IsInvalidInputOverwritten is enabled, StepSize=0.2, MaxValue=3.0, and the value 2.9 is incremented, 3.1 will register as invalid and it will be overwritten back to 2.9. |
| StepSize vs. MaxValue/MinValue |  |

## API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->

```c++ 
runtimeclass NumberBoxValueChangedEventArgs
{
    String Text;
    Double Value
};

runtimeclass NumberBoxTextChangedEventArgs
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
    
    Boolean IsInvalidInputOverwritten
    

    NumberBoxTemplateSettings TemplateSettings{ get; };

    event Windows.Foundation.TypedEventHandler<NumberBox, NumberBoxValueChangedEventArgs> ValueChanged;
    event Windows.Foundation.TypedEventHandler<NumberBox, NumberBoxTextChangedEventArgs> TextChanged;

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
    
    static Windows.UI.Xaml.DependencyProperty IsInvalidInputOverwrittenProperty{ get; };
}
```

## Appendix
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->

## Open Questions

* Consenus on Validation behavior. [See API Notes > Behavioral Components]

* Is there value in creating a preview for calculation results? @mdtauk created a few example visualizations: 

![NumberBox with a tool tip above to show a preview of the calculation results](https://user-images.githubusercontent.com/16964652/58919441-fbfe7900-86e2-11e9-8d2b-dd4dadfa74c5.png)

![NumberBox with a calculation in progress and highlight text previewing the calculation results](https://user-images.githubusercontent.com/7389110/58920708-5b807700-872b-11e9-9924-21a7b7d37e68.png)

* InputScope: Number or FormulaNumber?

*  Should we rely on the Xaml ControlTemplate for creating a NumberBox with disjoint UpDownButtons (i.e., this is not common enough to justify supporting out of the box) or should we include a property for setting whether the UpDownButtons appear appear contiguous vs. disjoint?

![one diagram showing UpDownButtons side-by-side and another diagram showing the down button to the left of the NumberBox and the up button to the right of the NumberBox](https://user-images.githubusercontent.com/16964652/58919787-3ae0fe80-86e4-11e9-8136-4c5b204825b0.png)

* Pending custom requirement for vertical UpDownButtons (compact scenarios).

* Is there any localization need for switching the sides of the UpDownButtons? 

* Can touch/virtual keyboards intelligently adapt to numeral-formulaic input?

