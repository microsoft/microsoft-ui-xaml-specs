 

StyleVersion API
=

# Background

XAML (WinUI) has a resources feature where you can put objects in a "resource dictionary", then
use the resources by referencing the key name. All of the styling for XAML controls
is defined in a resource dictionary and referenced by the controls.
But an application can override these by defining a resource dictionary with one or more matching keys.

XAML ships such an overrides dictionary named
[XamlControlsResources](http://msdn.microsoft.com/library/Microsoft.UI.Xaml.Controls.XamlControlsResources).
This dictionary defines all the resources necessary for all of the built-in controls.
Since the resources in this dictionary can change over time, a new `ThemeVersion` property is being added.

> StylesVersion? ResourcesVersion?

The default value of this property will be the latest version, but an app can set another value to force to a
previous version.

# API Pages

## XamlControlsResources.ThemeVersion

Set this property to specify which version of resources to use for the look and styling
of XAML controls. Default value is Version2.

Version 1 aligns to WinUI 2.5, Version 2 aligns with WinUI 2.6.

> This property must be set during app startup and cannot be changed after that point.  
(When, exactly?)

> Spec note: the alternative is to have an 'Auto' value in the enum,
and then the default value of the property is always that.
That would mean, though, that we also need an ActualThemeVersion property for
controls to query

### Example

Configure an application so that it uses an older version of the control styling resources.

```xaml
<Application.Resources>
    <XamlControlsResources ThemeVersion="Version1"/>
</Application.Resources>
```

# API Details

**Namespace Microsoft.UI.Xaml.Controls**

[webhosthidden]
enum ThemeVersion
{
    Version1 = 1,
    Version2 = 2,
};

[webhosthidden]
[default_interface]
runtimeclass XamlControlsResources : Windows.UI.Xaml.ResourceDictionary
{
    // Existing APIs elided
    // ...

    {
        [MUX_DEFAULT_VALUE("winrt::ThemeVersion::Version2")]
        ThemeVersion Version{ get; set; };

        static Windows.UI.Xaml.DependencyProperty ThemeVersionProperty{ get; };
    }
}



# Appendix

Some explanation of this feature and how it relates to "System XAML" ...

System XAML ships in Windows and defines a resource dictionary with all of the resources
for the XAML controls. WinUI2 is a control library that ships in NuGet and defines the
[XamlControlsResources](http://msdn.microsoft.com/library/Microsoft.UI.Xaml.Controls.XamlControlsResources)
type, which has overrides for all of the built-in resources and more.

The resources in System XAML can change from release to release, and there is no override
API to request an older version. The version of the resources that your app gets is determined
by the version of Windows your app requests in the app manifest (MaxTestedVersion). The new API
in this spec allows you to pick the version of the WinUI2 XamlControlsResources.
