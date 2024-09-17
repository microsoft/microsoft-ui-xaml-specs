
# Background

Some app developers choose to extend their app content into the title bar, for example:
```c#
CoreApplication.GetCurrentView().TitleBar.ExtendViewIntoTitleBar = true;`
```

When developers do so, the shell continues to reserve a visually transparent title bar for window drag operations. 
This has the effect of making UI elements at the top of the app window impossible to click with pointer input.

[NavigationView](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/navigationview) draws a back button and/or its hamburger button near the top of the control, potentially underneath the transparent title bar. To help app developers optimize interactivity and avoid frustrated users, starting in RS5 we added logic to automatically provide top padding and avoid hit test occlusion by the system title bar.

While this feels like the correct default behavior, we do not currently provide a mechanism for app developers to opt out of this automatic padding. And if the app uses [Window.SetTitleBar](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Window.SetTitleBar) to the element which is to be used as the drag region, the default drag region is removed, making other element hit-testable again. This resolves the interaction problem, but NavigationView's padding remains, providing now unwanted whitespace.

This spec adds an API to make this padding behavior overridable.

# Description

App developers have greater control over [customizing](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/navigationview#navigation-view-customization) the margins and padding near the top of their instance of NavigationView. 

# Navigation view customization

(The following is an update to the [Navigation View Customization](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/navigationview#navigation-view-customization) guide.)

## Top whitespace
Some apps choose to [customize their window's title bar](https://docs.microsoft.com/en-us/windows/uwp/design/shell/title-bar), potentially extending their app content into the title bar area. 

When NavigationView is the root element in apps that extend into the title bar **using the [ExtendViewIntoTitleBar](uwp/api/Windows.ApplicationModel.Core.CoreApplicationViewTitleBar.ExtendViewIntoTitleBar.md) API**, the control automatically adjusts the position of its interactive elements to prevent overlap with [the draggable region](https://docs.microsoft.com/en-us/windows/uwp/design/shell/title-bar#draggable-regions). This picture shows a NavigationView as the root of a Window that is extending into the title bar:

![An app extending into the title bar](NavigationView_WithTitleBarPadding.png)

If your app specifies the draggable region by calling the [Window.SetTitleBar](/uwp/api/windows.ui.xaml.window.settitlebar.md) method and you would prefer to have the back and menu buttons draw closer to the top of your app window, set `IsTitleBarAutoPaddingEnabled` to False:

![App extending into the title bar without extra padding](NavigationView_NoTitleBarPadding.png)

````Xaml
<muxc:NavigationView IsTitleBarAutoPaddingEnabled="False">
````
# Remarks
To further adjust the position of NavigationView's header area, override the *NavigationViewHeaderMargin* XAML theme resource.  

# API Details

```c++
unsealed runtimeclass NavigationView : Windows.UI.Xaml.Controls.ContentControl
{
    ...

    // Specifies whether to automatically push NavigationView's interactive content down by the 
    // height of the title bar when the NavigationView is a root element of an app that extends 
    // into the title bar. The default value is True.
    Boolean IsTitleBarAutoPaddingEnabled { get; set; };
}
```

