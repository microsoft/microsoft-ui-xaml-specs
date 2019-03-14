# Background
Xaml controls are typically implemented using an element tree that’s defined in a template.
For example the template for a ComboBox control has a TextBlock in it to display the selected value.
Several controls have properties that are only expected to be useful to these templates, 
and so there’s a common pattern for the control to expose them in a TemplateSettings property. 
For example [ComboBox.TemplateSettings](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Controls.ComboBox.TemplateSettings) 
is used by the ComboBox’s template. See [this doc page](https://docs.microsoft.com/en-us/windows/uwp/xaml-platform/template-settings-classes) for more details. 

The new API here adds a PaneTitle property to NavigationView's existing
[TemplateSettings property](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.NavigationViewTemplateSettings).
This helps the control template decide if it should show the
[NavigationView.PaneTitle](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.NavigationView.PaneTitle)
property, as it should be ignored if the
[NavigationView.PaneHeader](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.NavigationView.PaneHeader)
property is set. (The RS5 PaneHeader property supersedes/replaces the RS4 PaneTitle property.)


# Description
Provides calculated PaneTitle that can be referenced as TemplatedParent sources when defining templates for a NavigationView control. Not intended for general use.

# Examples

```
[webhosthidden]
unsealed runtimeclass NavigationViewTemplateSettings : Windows.UI.Xaml.DependencyObject
{
    ...

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
| PaneTitle | The NavigationView.PaneTitle displayed in the control. Empty when NavigationView.PaneHeader is set |

