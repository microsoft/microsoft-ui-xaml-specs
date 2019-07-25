<!--See comments in Markdown for how to use this spec template-> 

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

>See proposal documents for:
>
> [determinate mode for ProgressRing](https://github.com/microsoft/microsoft-ui-xaml/issues/688) 
>
> [ProgressRing style](https://github.com/microsoft/microsoft-ui-xaml/issues/837)
>
> [Guidance for Progress controls](https://github.com/microsoft/microsoft-ui-xaml/issues/880)

Progress controls indicate to a user that an operation is occuring. This includes ProgressBar and ProgressRing controls. ProgressBar has both a determinate and indeterminate mode. ProgressRing only has an indeterminate mode. 

Important APIs: [ProgressBar](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.progressbar) and [ProgressRing](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.progressring)
# Description
<!-- Use this section to provide a brief description of the feature.
For an example, see the introduction to the PasswordBox control 
(http://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box). -->
 Represents a control that indicates that an operation is ongoing. The typical visual appearance is a ring-shaped "spinner" that animates a filled area as progress continues.

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
[Please see this table of current ProgressBar and ProgressRing modes and states.](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/kathyang/progress-styles/active/progress-styles/progress-styles.md)

The following examples show how to use the IsIndeterminate property to change the mode of the ProgressRing and the Value property to change the proportionate amount indicated. 

## Indeterminate ProgressRing

```xml
<ProgressRing IsActive="True" Height="100" Width="100" IsIndeterminate="True"/>
```

![](images/ProgressRing-indeterminate.PNG)
## Determinate ProgressRing

```xml
<ProgressRing IsActive="True" Height="100" Width="100" Value="75"/>
```
![](images/ProgressRing-determinate.PNG)

Below are tables showing how ProgressRing is affected by the IsDeterminate, IsActive, Value, ShowPaused, and ShowError properties.

## Not Active ProgressRing
When IsActive is False, ProgressRing will always appear blank:
![](images/ProgressRing-determinate-not-active.PNG)

## Indeterminate ProgressRing
IsDeterminate is True

| Value | ShowPaused | ShowError| Image | 
|:--|:-:| :-:| :-:|
|  | False | False | ![](images/ProgressRing-indeterminate.PNG) |
|  | True | False | ![](images/ProgressRing-determinate-not-active.PNG) |
|  | False | True | ![](images/ProgressRing-determinate-not-active.PNG) |
|  | True | True | ![](images/ProgressRing-determinate-not-active.PNG) | 
| 75 | False | False | ![](images/ProgressRing-indeterminate.PNG) |
| 75 | True | False | ![](images/ProgressRing-determinate-not-active.PNG) |
| 75 | False | True | ![](images/ProgressRing-determinate-not-active.PNG) |
| 75 | True | True | ![](images/ProgressRing-determinate-not-active.PNG) | 


## Determinate ProgressRing
IsDeterminate is False

| IsActive | Value | Image |
|:--|:-:|:-:|
| True |  | ![](images/ProgressRing-determinate-not-active.PNG) |
| False |  | ![](images/ProgressRing-determinate-not-active.PNG) |
| True | 0 | ![](images/ProgressRing-determinate-not-active.PNG) |
| True | 75 | ![](images/ProgressRing-determinate.PNG) |
| False | 75 | ![](images/ProgressRing-determinate-not-active.PNG) | 

*Note that the designs for ProgressRing are not finalized. See [here](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/user/chigy/ControlUpdates/active/ControlUpdates/images/Progress.png) for early designs. 

# Remarks
<!-- Explanation and guidance that doesn't fit into the Examples section. -->

<!-- APIs should only throw exceptions in exceptional conditions; basically,
only when there's a bug in the caller, such as argument exception.  But if for some
reason it's necessary for a caller to catch an exception from an API, call that
out with an explanation either here or in the Examples -->

With the addition of a determinate mode of ProgressRing, the ShowPaused and showError properties that ProgressBar already has should be aligned in ProgressRing. ProgressRing can be used in scenarios where progress is paused, or an error occured during the process. 

The IsIndeterminate property of ProgressRing will default to True for back compatability.

# API Notes
<!-- Option 1: Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. For properties, specify the default value of the property if it
isn't the type's default (for example an int-typed property that doesn't default to zero.) -->

<!-- Option 2: Put these descriptions in the below API Details section,
with a "///" comment above the member or type. -->
Below are properties being added to ProgressRing:

|Name | Description | 
|:--|:-:|
| IsIndeterminate | Defaults to True. Gets or sets a value that indicates whether the progress ring reports generic progress with a repeating pattern or reports progress based on the Value property |
| Value | Gets or sets the current setting of the control, between 0 and 1| 
| ShowPaused | Defaults to False. Gets or sets a value that indicates whether the progress bar should use visual states that communicate a Paused state to the user.|
| ShowError | Defaults to False. Gets or sets a value that indicates whether the progress bar should use visual states that communicate an Error state to the user. |

# API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->

# Appendix
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->

# Open Questions

1) ProgressRing derives from Control but ProgressBar derives from RangeBase. RangeBase "represents an element that has a value within a specific range". Is there value to changing ProgressRing's base class? And if there is value, is it possible to do so at this time? (question modified from [here](https://github.com/microsoft/microsoft-ui-xaml-specs/pull/36#discussion_r305069598))

3) Adding IsIndeterminate would result in closer functional parity between ProgressRing and ProgressBar - should these controls be combined into one, with two views? (question modified from [here](https://github.com/microsoft/microsoft-ui-xaml-specs/pull/36#discussion_r305118085)

The Value property for ProgressBar is a number out of 100 (i.e. it can hold integers from 0 to 100, inclusive). Should the Value property for ProgressRing also be out of 100, or be out of 360 to reflect the circular shape?
