# Background
PaneTitle is added to NavigationView in RS4, but it's a String. Since RS5, we introduced PaneHeader(UIElement) to deprecate PaneTitle. If both PaneTitle and PaneHeader are provided by customer, PaneHeader is used.

This API update addes PaneTitle [Template-Settings](https://docs.microsoft.com/en-us/windows/uwp/xaml-platform/template-settings-classes) to the NavigationView control.

# Description
Provides calculated PaneTitle that can be referenced as TemplatedParent sources when defining templates for a NavigatiionView control. Not intended for general use.

# Examples

```
[webhosthidden]
unsealed runtimeclass NavigationViewTemplateSettings : Windows.UI.Xaml.DependencyObject
{
    ...

    [WUXC_VERSION_MUXONLY]
    {
        String PaneTitle{ get; };
        static Windows.UI.Xaml.DependencyProperty PaneTitleProperty{ get; };
    }
}
```

# Remarks
This is intended for use in the style of the `NavigationView` control. Sample usage:

```xaml
    <Style TargetType="local:NavigationView">
 ...
                                    <TextBlock
                                        x:Name="PaneTitleTextBlock" 
                                        Grid.Column="0"
                                        Text="{Binding RelativeSource={RelativeSource TemplatedParent}, Path=TemplateSettings.PaneTitle}"
                                        HorizontalAlignment="Left"
                                        VerticalAlignment="Center"
                                        Style="{StaticResource NavigationViewItemHeaderTextStyle}"/>
```

# API Notes

## Class: NavigationViewTemplateSettings 
| Member Name | Description |
|:- |:--|
| PaneTitle | The NavigationView.PaneTitle displayed in the control. Empty when NavigationView.PaneHeader is set, |

