
# Background

_This spec adds a serviceProvider to the existing [MarkupExtension](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Markup.MarkupExtension) class. ([Proposal](https://github.com/microsoft/microsoft-ui-xaml/issues/741))_

Xaml markup has a [markup extension](https://docs.microsoft.com/en-us/dotnet/desktop-wpf/xaml-services/markup-extensions-overview) feature which is a builder pattern, similar to a .Net StringBuilder. It lets you create, modify, and then retrieve a value.

When you write a custom markup extension (see the Description section below), you override the `ProvideValue` method to return the built value. The ProvideValue method is different in  Xaml (WinUI) than it is in WPF, because WPF passes an extra [IServiceProvider](http://msdn.microsoft.com/library/System.IServiceProvider) parameter:

Xaml:

```cs
protected override object ProvideValue(IServiceProvider serviceProvider)
{
    return DateTime.Now.ToString(Format);
}
```

WPF:

```cs
protected override object ProvideValue(IServiceProvider serviceProvider)
{
    return DateTime.Now.ToString(Format);
}
```

The service provider can be used, for example, to discover the property being assigned to.

This spec is adding a new version of ProvideValue that passes a service provider.

> To do: This is defining an IXamlServiceProvider, can/should this project in .Net to [System.IServiceProvider](http://msdn.microsoft.com/library/System.IServiceProvider)?

# Description

A developer defines a custom markup extension by subclassing the MarkupExtension class and overriding the ProvideValue method. This can then be used in Xaml markup: the markup extension can be created using the "{ }" markup expression syntax, and the return value from ProvideValue will be set as the property value.

For example, given this markup extension:

```cs
public class CurrentDate : MarkupExtension
{
    public string Format { get; set; } = "";
    protected override object ProvideValue()
    {
        return DateTime.Now.ToString(Format);
    }
}
```

you can do this in Xaml markup:

```xml
<TextBlock>
    Today is:  
    <Run Text="{local:CurrentDate Format=d}"/>
</TextBlock>
```

On an en-US machine, this will produce something like:
 
![MarkupExtension sample](MESample.png)

## MarkupExtension.ProvideValue virtual method

A markup extension overrides the ProvideValue method, and returns a value from it that the Xaml loader sets as a property value.

There are two versions of ProvideValue, one that passes no parameters and one that passes a service provider. From the service provider, a markup extension implementation can retrieve the following services:
* `IProvideValueTarget`, which provides information about the type/property the markup extension is being applied to. This aligns with WPF's [IProvideValueTarget](https://docs.microsoft.com/dotnet/api/System.Windows.Markup.IProvideValueTarget).
* `IRootObjectProvider`, which provides a reference to the root object in the markup. This aligns with WPF's [IRootObjectProvider](https://docs.microsoft.com/dotnet/api/System.Xaml.IRootObjectProvider).
* `IUriContext` , which provides the base URI of the markup. This aligns with WPF's [IUriContext](https://docs.microsoft.com/dotnet/api/System.Windows.Markup.IUriContext).



# Examples

## IProvideValueTarget

The following example shows a custom markup extension whose provided value changes based on the specific property being targeted by the custom markup extension. In this case, the developer wants something that will automatically use an AcrylicBrush for the [Control.Background](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Control.Background) or [Border.Background](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Border.Background) properties, but uses a [SolidColorBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush) for all other properties.

```cs
public class BrushSelectorExtension : MarkupExtension
{
    public Color Color { get; set; }

    protected override object ProvideValue(IXamlServiceProvider serviceProvider)
    {
        Brush brushToReturn = new SolidColorBrush() { Color = Color }; ;

        var provideValueTarget = serviceProvider.GetService(typeof(IProvideValueTarget))
            as IProvideValueTarget;
        if (provideValueTarget.TargetProperty is ProvideValueTargetProperty targetProperty)
        {
            if (targetProperty.Name == "Background"
                && (targetProperty.DeclaringType == typeof(Control) 
                    || targetProperty.DeclaringType == typeof(Border)))
            {
                brushToReturn = new AcrylicBrush()
                    { TintColor = Color, TintOpacity = 0.75 };
            }
        }

        return brushToReturn;
    }
}
```

```xml
<StackPanel>
    <Button Foreground="{local:BrushSelector Color=Blue}" 
            Background="{local:BrushSelector Color=Gold}">
        Go bears!
    </Button>
    <Rectangle x:Name="SolidColor" Fill="{local:BrushSelector Color=Green}" />
</StackPanel>
```


## IRootObjectProvider

> Some more realistic example?

For example, with this markup extension:

```cs
public class TestMarkupExtension : MarkupExtension
{
    public override object ProvideValue(IXamlServiceProvider serviceProvider)
    {
        var target = serviceProvider.GetService(typeof(IRootObjectProvider)) as IRootObjectProvider;
        return target.RootObject.ToString();
    }
}
```

the TextBlock in this markup will display “App1.MainPage”:

```xml
<Page x:Class="App52.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App52"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}" >

    <Grid>
        <TextBlock Text="{local:TestMarkupExtension}" />
    </Grid>
</Page>
```

## IUriContext 

> Todo

# API Notes/Remarks

## IXamlServiceProvider interface
Gets the service object of the specified type.

There are two versions of the virtual ProvideValue method, you should only override one.

> Issue: how do we know which version has been overridden? We have the same scenario with [DataTemplateSelector.SelectTemplateCore](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.DataTemplateSelector.SelectTemplateCore). Proposal: the virtual (base) version of the parameterless override returns an internal sentinal value, and is called first.

## IProvideValueTarget interface
Provides a target object and property.

Xaml markup extensions offer this interface in the IXamlServiceProvider parameter.  The target object/property are the instance and property identifier that the markup extension is being set on.

## IRootObjectProvider interface
Describes a service that can return the root object of the markup tree being parsed.

Xaml markup extensions offer this interface in the IXamlServiceProvider parameter.  The root object is the root of the markup being loaded.

## IUriContext interface
Provided by the Xaml loader to markup extensions to expose the base URI of the markup being loaded

Xaml markup extensions offer this interface in the IXamlServiceProvider parameter.  The URI is that of the markup file being loaded (the `resourceLocator' value passed to [Application.LoadComponent](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Application.LoadComponent)).



# API Details

namespace Microsoft.UI.Xaml
{
    interface IXamlServiceProvider 
    {
        Object GetService(Windows.UI.Xaml.Interop.TypeName type);
    };
}

namespace Microsoft.UI.Xaml.Markup
{

    unsealed runtimeclass MarkupExtension
    {
        overridable Object ProvideValue();

        // ** New **    
        overridable Object ProvideValue(Microsoft.UI.Xaml.IXamlServiceProvider serviceProvider);
    };


    interface IProvideValueTarget 
    {
        Object TargetObject{ get; };
        Object TargetProperty{ get; };
    };

    interface IRootObjectProvider 
    {
        Object RootObject{ get; };
    };

    interface IUriContext 
    {
        Windows.Foundation.Uri BaseUri{ get; };
    };

    interface IXamlTypeResolver 
    {
        Windows.UI.Xaml.Interop.TypeName Resolve(String qualifiedTypeName);
    };

    unsealed runtimeclass MarkupExtension
    {
        [method_name("CreateInstance")] MarkupExtension();
    
        [contract(Windows.Foundation.UniversalApiContract, 5)]
        [overridable_name("Windows.UI.Xaml.Markup.IMarkupExtensionOverrides", 393779bf-b9c0-4ffb-a57f-58e7356e425f)]
        {
            overridable Object ProvideValue();
        }
    
        [contract(Windows.Foundation.UniversalApiContract, 8)]
        {
            overridable Object ProvideValue(Windows.UI.Xaml.IXamlServiceProvider serviceProvider);
        }
    };

    runtimeclass ProvideValueTargetProperty
    {
        ProvideValueTargetProperty();
        String Name{ get; };
        Windows.UI.Xaml.Interop.TypeName Type{ get; };
        Windows.UI.Xaml.Interop.TypeName DeclaringType{ get; };
    };
}



# Appendix
