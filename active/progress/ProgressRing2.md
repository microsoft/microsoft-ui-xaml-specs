# Background
>See proposal documents for:
>
> [determinate mode for ProgressRing](https://github.com/microsoft/microsoft-ui-xaml/issues/688) 
>
> [ProgressRing style](https://github.com/microsoft/microsoft-ui-xaml/issues/837)
>
> [Guidance for Progress controls](https://github.com/microsoft/microsoft-ui-xaml/issues/880)

Progress controls indicate to a user that an operation is occuring. 
Xaml has two controls for this:
* the [ProgressBar](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ProgressBar)
control, which shows progress on a rectangle
* and the
[ProgressRing](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ProgressRing)
control, which shows progress on a circle.

Progress _Bar_ has both a determinate and indeterminate mode.
In determine mode the rectangle goes from empty to full, in indeterminate mode it shows an animation.
Progress _Ring_ currently only has an indeterminate mode, but this spec is adding an indeterminate mode.

ProgressBar is also getting a feature to replaces its built-in visualization (a spinning ring)
with a custom animation defined as an 
[IAnimatedVisualSource](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.IAnimatedVisualSource),
which is known as a "Lottie Animation", and already supported (playable by) an
[AnimatedVisualPlayer](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.AnimatedVisualPlayer).

# ProgressRing Description
Represents a control that indicates that an operation is ongoing. 
The typical visual appearance is a ring-shaped "spinner" that animates a filled area as progress continues.

By default, ProgressRing will display an indeterminate spinner that continuously 
animates until the control is no longer visible ?? or 
[ProgressRing.IsActive](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.ProgressRing.IsActive)
is set to false. You can change the behavior to display
a determinate ring that fills in based on the value you provide, using the `IsIndeterminate` property.

You can also replace the built-in animation shown by the ProgressRing with a custom one,
using the `DeterminateSource` and `IndeterminateSource` properties.

# Examples

The following examples show how to use the `IsIndeterminate` property to change the mode of 
the ProgressRing and the Value property to change the proportionate amount indicated. 

## Indeterminate ProgressRing

By default ProgressRing is indeterminate (a spinning circle).

> Issue: not the same without an AniGif

```xml
<ProgressRing IsActive="True" />
```
![](images/ProgressRing-indeterminate.jpg)

## Determinate ProgressRing

When `IsIndeterminate` is set, the circle will show progress around the ring,
filling it when Value gets to Maximum.

```xml
<ProgressRing IsActive="True" IsIndeterminate="False"
    Minimum="32" Maximum="212" Value="{x:Bind LiquidTemp}" />
```
![](images/ProgressRing-determinate.PNG)


## Custom animation

Show progress ring that visually looks like a thermometer boiling over.
See [AnimatedVisualPlayer](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.AnimatedVisualPlayer)
for more information on animated visuals.

> Picture would be nice

```xml
<ProgressRing IsActive="True" >
    <ProgressRing.IndeterminateSource>
        <animatedvisuals:LottieProgress />
    </ProgressRing.IndeterminateSource>
</ProgressRing>
```

# API Notes

ProgressRing can now be used in scenarios where progress is determinate or indeterminate. 
With this additional capability, 
the determinate ProgressRing does not represent a "hung" state where the end user cannot interact
with the app. 

Previously, guidance recommended that ProgressRing only be used when the user cannot continue to interact with the app, 
but this is no longer the only use case and ProgressRing can be used in scenarios where user interaction can continue 
while the ring is spinning. An example of this would be when a video is loading on a page and a progress ring is displayed 
while the media is loading. The end user can still interact with other UI elements on the page while they wait 
for the media to load.

In addition to the updated guidance, the sources for the indeterminate and determinate mode have been exposed so you can 
choose the animation file that can be played. This will give you the opportunity to customize the look and feel of your
progress ring and potentially have different animations for determinate vs. indeterminate. 

|Name | Type | Description | Default | 
|:--|:-:|:-:|:-:|
| IsIndeterminate | boolean | Gets or sets a value that indicates whether the progress ring reports generic progress with a repeating pattern (indeterminate progress) or reports progress based on the Value property (determinate progress) | True | 
| IsActive | boolean | Gets or sets a value that indicates whether the progress ring is being displayed to the end user. If IsActive is false, the ProgressRing is not shown, but space is reserved for it in the UI layout. To not reserve space for it, set its Visibility property to Collapsed. | True |
| Value | double | Gets or sets the current setting of the range control, which may be coerced. The value ranges from 0 to 100??. Has no effect when `IsIndeterminte` is true. | 0-100 | 
| Maximum | double | Gets or sets the highest possible `Value` of the range element. Has no effect when `IsIndeterminate` is true.
| Minimum | double | Gets or sets the lowest possible `Value` of the range element. Has no effect when `IsIndeterminate` is true.
| DeterminateSource | IAnimatedVisualSource | Gets or sets the animation file used for the determinate state of the ring. | N/A | 
| IndeterminateSource | IAnimatedVisualSource | Gets or sets the animation file used for the indeterminate state of the ring. | N/A | 


# API Details

```xaml
[webhosthidden]
unsealed runtimeclass ProgressRing : Windows.UI.Xaml.Controls.Control
{
    // ... (existing APIs)

    Boolean IsIndeterminate{ get; set; };
    
    IAnimatedVisualSource DeterminateSource{ get; set; };
    IAnimatedVisualSource IndeterminateSource{ get; set; };

    Double Value;
    Double Minimum;
    Double Maximum;

    static Windows.UI.Xaml.DependencyProperty IsIndeterminateProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty DeterminateSourceProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty IndeterminateSourceProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty ValueProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty MinimumProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty MaximumProperty{ get; };
}
```


# Appendix

## Design Behavior Details
The determinate progress ring will be an empty ring when the progress is set to a value of 0. 
When value is set to 1, a blue dot will appear and then continue to fill as the value increases. 
When the value is set to 98 or 99, the ring will not be completely filled 
to indicate to the user that progress is not complete. 
