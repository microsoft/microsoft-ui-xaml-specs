# Background

Xaml controls are typically implemented using an element tree that’s defined in a template. 
For example the template for a ComboBox control has a TextBlock in it to display the selected value.

Several controls have properties that are only expected to be useful to these templates, 
and so there’s a common pattern for the control to expose them in a TemplateSettings property. 
For example [ComboBox.TemplateSettings](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Controls.ComboBox.TemplateSettings) 
is used by the ComboBox’s template. See [this doc page](https://docs.microsoft.com/en-us/windows/uwp/xaml-platform/template-settings-classes) for more details. 

This API update introduces this pattern to the PersonPicture control: 
a new TemplateSettings property and associated PersonPictureTemplateSettings class.

# Description

Provides calculated values that can be referenced as TemplatedParent sources when defining templates 
for a PersonPicture control. Not intended for general use.

# API Details

```
namespace Microsoft.UI.Xaml.Controls
{
    [webhosthidden]
    unsealed runtimeclass PersonPicture : Windows.UI.Xaml.Controls.Control
    {
        ...

        PersonPictureTemplateSettings TemplateSettings { get; };

        ...
    }

    [webhosthidden]
    runtimeclass PersonPictureTemplateSettings : Windows.UI.Xaml.DependencyObject
    {
        String ActualInitials { get; };
        Windows.UI.Xaml.Media.ImageBrush ActualImageBrush { get; };
    }

}
```

# Examples

This is intended for use in the style of the `PersonPicture` control. Sample usage:

```xaml
    <Style TargetType="local:PersonPicture">
        ...
        <Setter Property="Template">
            <Setter.Value>
                <ControlTemplate TargetType="local:PersonPicture">
                    <Grid x:Name="RootGrid">
                        <VisualStateManager.VisualStateGroups>
                            <VisualStateGroup x:Name="CommonStates">
                                <!-- Visual State when a Photo is available for display -->
                                <VisualState x:Name="Photo">
                                    <VisualState.Setters>
                                        <Setter Target="PersonPictureEllipse.Fill" Value="{Binding RelativeSource={RelativeSource TemplatedParent}, Path=TemplateSettings.ActualImageBrush}"/>
                                    </VisualState.Setters>
                                </VisualState>
                    ...
                        <TextBlock
                            ...
                            Text="{Binding RelativeSource={RelativeSource TemplatedParent}, Path=TemplateSettings.ActualInitials}" />
                        
```

# API Notes

## Class: PersonPicture
| Member Name | Description |
|:- |:--|
| TemplateSettings | Provides calculated values that can be referenced as TemplatedParent sources when defining templates for a PersonPicture control. Not intended for general use. |

## Class: PersonPictureTemplateSettings 
| Member Name | Description |
|:- |:--|
| ActualInitials | Empty when an image is specified or when the PersonPicture control represents a group. |
| ActualImageBrush | The image brush to use in the control. The PersonPicture control determines which image to use based on how it was configured. This property will be null when no image should be displayed. |
