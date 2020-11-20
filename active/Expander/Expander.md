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
 > This spec corresponds to [this issue](https://github.com/microsoft/microsoft-ui-xaml/issues/3279) on the WinUI repo. 

Throughout Windows, different expander controls are used by various apps and surfaces. There’s currently no consistent way to address this common UX pattern. This control is needed for situations where expanding (instead of overlaying) content is needed.  An Expander control is motivated by its use in many app scenarios and supporting developers in migrating from WPF and WCT.  

# Description
<!-- Use this section to provide a brief description of the feature.
For an example, see the introduction to the PasswordBox control 
(http://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box). -->
An Expander control is a UI component that provides a standard interaction for pushing adjacent content while expanding and collapsing. An Expander is independent of the contents inside it, including controls. 

## Is this the right control?
An Expander is not intended for flyout-type behaviors where a UI component overlays on top of adjacent content. In other words, Expander will 'push' and not overlay or fly out.

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

## Create an Expander
XAML
~~~~
<controls:Expander AutomationProperties.AutomationId="CollapsedExpander" AutomationProperties.Name="Expander2">
    <controls:Expander.Header>
        <TextBlock Style="{StaticResource HeaderTextBlockStyle}" Text="This expander is collapsed by default." />
    </controls:Expander.Header>
    <TextBlock AutomationProperties.AutomationId="CollapsedExpanderContent">Content</TextBlock>
</controls:Expander>
~~~~

# Remarks
<!-- Explanation and guidance that doesn't fit into the Examples section. -->

<!-- APIs should only throw exceptions in exceptional conditions; basically,
only when there's a bug in the caller, such as argument exception.  But if for some
reason it's necessary for a caller to catch an exception from an API, call that
out with an explanation either here or in the Examples -->

# API Notes
<!-- Option 1: Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. For properties, specify the default value of the property if it
isn't the type's default (for example an int-typed property that doesn't default to zero.) -->

<!-- Option 2: Put these descriptions in the below API Details section,
with a "///" comment above the member or type. -->
## Properties
| Name | Description | Default |
| :---------- | :------- | :------- |
| ExpandDirection | Sets the direction of expansion | Down = 0 |
| IsExpanded | Whether or not control is expanded | False |

## Events
| Name | Description | 
| :---------- | :------- | 
| Expanded | Occurs when expanded |
| Collapsed| Occurs when collapsed |

# API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->
~~~~
Public enum ExpandDirection 
{ 
 Down = 0 
 Up = 1 
} 
 
public class Expander : ContentControl 
{ 
 Expander(); 

 public object Header {get;set;} 
 public DataTemplate HeaderTemplate { get;set; } 
 public DataTemplate HeaderTemplateSelector {get;set;} 

 public static readonly DependencyProperty HeaderProperty; 
 public static readonly DependencyProperty HeaderTemplateProperty; 
 public static readonly DependencyProperty HeaderTemplateSelectorProperty {get;} 

 public bool IsExpanded { get;set} 
 public ExpandDirection ExpandDirection { get;set;} 

 protected virtual void OnExpanded(); 
 protected virtual void OnCollapsed(); 

 public event Expanded; 
 public event Collapsed; 

 public static readonly DependencyProperty IsExpandedProperty; 
 public static readonly DependencyProperty ExpandDirectionProperty; 
} 
~~~~

## Theme Resources

| Name | Description | 
| :---------- | :------- | 
| ExpanderHeaderBackground | Header background color| 
| ExpanderChevronBackground | Chevron background color| 
| ExpanderChevronForeground | Chevron foreground color| 
| ExpanderChevronMargin | Chevron margin thickness| 
| ExpanderChevronGlyph | Chevron glyph| 
| ExpanderChevronWidth | Chevron width (should this be renamed to 'weight'?)| 
| ExpanderPopinVerticalOffset | vertical offset | 

# Appendix
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->

# Inputs and Accessibility
## UI Automation Patterns
Expander will use a ExpandCollapsePattern. Expanding/Collapsing the expander will raise [RaisePropertyChanged](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.automation.peers.automationpeer.raisepropertychangedevent?view=winrt-19041) with the property changed being the [ExpandCollapseProperty](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.automation.expandcollapsepatternidentifiers.expandcollapsestateproperty?view=winrt-19041) property.

## Keyboard
* Only the header takes focus (not the entire Expander, or the chevron)
*	When focus is on the Header - hit space/enter/right key to expand it, and focus does not move
* Space and enter should expand and collapse 
* right arrow will expand, left arrow will collapse

## GamePad
TBD

# Open Questions

## Input and Accessibility
* Is there a RTL consideration for right/left keyboarding behavior?
## General
*
