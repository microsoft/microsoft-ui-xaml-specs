
# Background

The Xaml [ProgressBar](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.ProgressBar) control is used to show progress of an operation, where the typical visual appearance is a bar that animates a filled area as progress continues. (There's also a [ProgressRing](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ProgressRing) control that shows a circle rather than a line.)

The update in this spec is for a feature to make the bar appear such that the rectangle wraps around from one side to the other (when in indetermine mode). This is implemented by having two rectangles, so during the "wrap" what you see is actually a partial rectangle on the right and another partial one on the left:

![image](./IndeterminatePB.gif)

The current single rectangle is controlled by two read-only properties, :  
`ContainerAnimationStartPosition` and  
`ContainerAnimationEndPosition`. 

These are exposed by the ProgressBar's [TemplateSettings](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ProgressBar.TemplateSettings), and consumed by the control template. 

The new APIs are a second pair of these properties to similarly control the second rectangle:  
`ContainerAnimationStartPosition2` and  
`ContainerAnimationEndPosition2`

Naming note: the "2" suffix looks like a version indicator, which would be an unusual naming pattern. But it's actually referring to the second rectangle. So it would be better to rename the existing properties with a "1" suffix, similar to [ScaleAnimation.Control1/2](https://docs.microsoft.com/uwp/api/Windows.UI.Core.AnimationMetrics.ScaleAnimation.Control1), but that would be a breaking change to existing API.


# Examples

From the ProgressBar's new built-in control template, the following is used to animate the second rectangle by binding to the `ContainerAnimationStartPosition2` and `ContainerAnimationEndPosition2` properties:

```Xml
<DoubleAnimationUsingKeyFrames
    Storyboard.TargetName="IndeterminateProgressBarIndicator2"
    Storyboard.TargetProperty="(UIElement.RenderTransform).(CompositeTransform.TranslateX)">

    <SplineDoubleKeyFrame KeyTime="0" 
    Value="{Binding RelativeSource={RelativeSource TemplatedParent},
Path=TemplateSettings.ContainerAnimationStartPosition2}" 
    KeySpline="0.4, 0.0, 0.6, 1.0" />

    <SplineDoubleKeyFrame KeyTime="0:0:0.75" 
    Value="{Binding RelativeSource={RelativeSource TemplatedParent}, 
Path=TemplateSettings.ContainerAnimationStartPosition2}" KeySpline="0.4, 0.0, 0.6, 1.0" />

    <SplineDoubleKeyFrame KeyTime="0:0:2" 
    Value="{Binding RelativeSource={RelativeSource TemplatedParent}, Path=TemplateSettings.ContainerAnimationEndPosition2}" KeySpline="0.4, 0.0, 0.6, 1.0" />

</DoubleAnimationUsingKeyFrames>
```


# Remarks

The `ContainerAnimationStartPosition2` and `ContainerAnimationEndPosition2` are intended to be used together, and can be used with the `ContainerAnimationStartPosition` and `ContainerAnimationEndPosition` properties.

# API Notes

New properties on [ProgressBarTemplateSettings](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.ProgressBarTemplateSettings):

| API | Note |
| - | - |
| ContainerAnimationStartPosition2 | Gets the "From" point of the container animation that animates the ProgressBar. This is intended to be used with the ContainerAnimationEndPosition2 property |
| ContainerAnimationEndPosition2 | Gets the target "To" point of the container animation that animates the ProgressBar. This is intended to be used with the ContainerAnimationStartPosition2 property|


# API Details

```c++
[webhosthidden]
unsealed runtimeclass ProgressBarTemplateSettings : Windows.UI.Xaml.DependencyObject
{
    ...

    Double ContainerAnimationStartPosition2;
    static Windows.UI.Xaml.DependencyProperty ContainerAnimationStartPosition2Property{ get; };

    Double ContainerAnimationEndPosition2;
    static Windows.UI.Xaml.DependencyProperty ContainerAnimationEndPosition2Property{ get; };

}
```
