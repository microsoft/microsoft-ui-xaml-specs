# Background
Xaml ships with a set of resources (frequently called "generic.xaml") which define how controls appear.
The [WinUI](https://docs.microsoft.com/en-us/uwp/toolkits/winui/) package, which ships
several Xaml controls as a Nuget package, also ships an updated set of resources, in the form
of the 
[XamlControlsResources](https://docs.microsoft.com/en-us/uwp/api/microsoft.ui.xaml.controls.xamlcontrolsresources)
API. You bring this into your app by adding it to your Application.Resources:

````xaml
<Application>
    <Application.Resources>
        <XamlControlsResources xmlns="using:Microsoft.UI.Xaml.Controls"/> 
    </Application.Resources>
</Application>
````xaml

This spec adds a new property to XamlControlResources that updates the resources to make
the controls more "dense". For example, reducing the default spacing between controls.
Overall this allows for more UI to fit into the same screen space.
Density is described in
[Suggestion: Add Compact Dictionary to WinUI 2.1](https://github.com/Microsoft/microsoft-ui-xaml/issues/98).

# Description
<!-- Use this section to provide a brief description of the feature.
For an example, see the introduction to the PasswordBox control 
(http://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box). -->
UseCompactResources property is added XamlControlsResources to allow customer to enable Compact.

# Examples

```
    runtimeclass XamlControlsResources : Windows.UI.Xaml.ResourceDictionary
    {
        ...
        {
            Boolean UseCompactResources{ get; set; };
            static Windows.UI.Xaml.DependencyProperty UseCompactResourcesProperty{ get; };
        }
    }
```

# Remarks
```
<Application>
    <Application.Resources>
        <XamlControlsResources xmlns="using:Microsoft.UI.Xaml.Controls"  UseCompactResources="True"/> 
    </Application.Resources>
</Application>
```


# API Notes


# API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->
## Class: XamlControlsResources 
| Member Name | Description |
|:- |:--|
| UseCompactResources | True to enable Compact Resources. False when parameter is not set |

# Appendix
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->
