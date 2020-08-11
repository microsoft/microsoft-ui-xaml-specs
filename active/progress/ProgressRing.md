# Background
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
 Represents a control that indicates that an operation is ongoing. The typical visual appearance is a ring-shaped "spinner" that animates a filled area as progress continues.

 By default, the progress ring will display an indeterminate spinner that continuously animates until the control is no longer visible. You can change the behavior to dislpay a determinate ring that fills in based on the value you provide.

# Examples

The following examples show how to use the IsIndeterminate property to change the mode of the ProgressRing and the Value property to change the proportionate amount indicated. 

## Indeterminate ProgressRing

```xml
<ProgressRing IsActive="True" Height="100" Width="100"/>
```

![](images/ProgressRing-indeterminate.jpg)
## Determinate ProgressRing

```xml
<ProgressRing IsActive="True" Height="100" Width="100" Value="75" IsIndeterminate="False"/>
```
![](images/ProgressRing-determinate.PNG)

Setting a Value on an Indeterminate ProgressRing should display as though there is no Value.


# API Notes
<!-- Option 1: Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. For properties, specify the default value of the property if it
isn't the type's default (for example an int-typed property that doesn't default to zero.) -->

<!-- Option 2: Put these descriptions in the below API Details section,
with a "///" comment above the member or type. -->
ProgressRing can now be used in scenarios where progress is determinate or indeterminate. With this additional capability, the determinate ProgressRing does not represent a "hung" state where the end user cannot interact with the app. 

Previously, guidance recommended that ProgressRing only be used when the user cannot continue to interact with the app, but this is no longer the only use case and ProgressRing can be used in scenarios where user interaction can continue while the ring is spinning. An example of this would be when a video or image is loading and a progress ring is displayed while the media is loading but the end user can still interact with other UI elements on the page while they wait for the media to load.

Below are properties being added to ProgressRing. The IsIndeterminate property of ProgressRing will default to True for backwards compatability.

|Name | Type | Description | Default | 
|:--|:-:|:-:|:-:|
| IsIndeterminate | boolean | Gets or sets a value that indicates whether the progress ring reports generic progress with a repeating pattern (indeterminate progress) or reports progress based on the Value property (determinate progress) | True | 
| IsActive | boolean | Gets or sets a value that indicates whether the progress ring is being displayed to the end user. If IsActive is false, the ProgressRing is not shown, but space is reserved for it in the UI layout. To not reserve space for it, set its Visibility property to Collapsed. | True |
| Value | double | Gets or sets the current setting of the range control, which may be coerced. The value ranges from 0 to 100. | 0-100 | 
| DeterminateSource | AnimatedVisualSource | Gets or sets the animation file used for the determinate state of the ring. | N/A | 
| IndeterminateSource | AnimatedVisualSource | Gets or sets the animation file used for the indeterminate state of the ring. | N/A | 

# API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->
```xaml
Unsealed runtimeclass ProgressRing : Microsoft.UI.Xaml.Controls.Control.RangeBase
{
    ProgressRing();
 
    Boolean IsActive;
    Boolean IsIndeterminate; 

    IAnimatedVisualSource DeterminateSource{ get; set; };
    IAnimatedVisualSource IndeterminateSource{ get; set; };

    ProgressRingTemplateSettings TemplateSettings{ get; };
 
    static Microsoft.UI.Xaml.DependencyProperty IsActiveProperty{ get; set};
    static Microsoft.UI.Xaml.DependencyProperty IsIndeterminateProperty{  get; };
    static Mircorsoft.UI.Xaml.DependencyProperty DeterminateSourceProperty{ get; };
    static Microsoft.UI.Xaml.IndeterminateSourceProperty{ get; };
}
```


# Appendix
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->

## Design Behavior Details
The determinate progress ring will be an empty ring when the progress is set to a value of 0. When value is set to 1, a blue dot will appear and then continue to fill as the value increases. When the value is set to 98 or 99, the ring will not be completely filled to indicate to the user that progress is not complete. 
