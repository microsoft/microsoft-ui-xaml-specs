# Background

The best practice for controls is that the XAML file includes all UI related colors, sizes and visual states.
XAML markup has expressiveness limitations that require the controls to do some calculations or value conversions
in code-behind and expose those results to the XAML to bind from. The pattern we have established for that is 
having a TemplateSettings property on the control of type <I>Foo</I>Control, where <I>Foo</I> is the name of 
the control. See [this doc page](https://docs.microsoft.com/en-us/windows/uwp/xaml-platform/template-settings-classes)
for more details. 

PersonPicture.TemplateSettings and the new type PersonPictureTemplateSettings are needed to help move some
visual state management logic out of the PersonPicture code implementation into the XAML file.


# Description

Provides calculated values that can be referenced as TemplatedParent sources when defining templates for a PersonPicture control. Not intended for general use.

# API Details

### PersonPicture

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
        String EffectiveInitials { get; };
        Windows.UI.Xaml.Media.ImageBrush EffectiveImageBrush { get; };
    }

}
```
