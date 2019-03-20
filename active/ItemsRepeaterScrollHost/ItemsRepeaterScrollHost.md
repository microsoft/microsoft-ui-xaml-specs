# Background
[The Windows UI Library](https://docs.microsoft.com/en-us/uwp/toolkits/winui/) (**WinUI**, aka "MUX")
ships a set of Xaml controls in Nuget, rather than as part of Windows. Apps can use the
controls in this package and target down to RS1.

WinUI includes the Xaml 
[ItemsRepeater](https://docs.microsoft.com/en-us/uwp/api/microsoft.ui.xaml.controls.itemsrepeater)
control, which shows a collection of items, like a more primitive ListView control. Typically 
lists are large and need to be scrolled, so it's typically put into a
[ScrollViewer](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ScrollViewer) control:

```xaml
<ScrollViewer>
    <ItemsRepeater ... />
</ScrollViewer>
```

When a scrolled list changes or is resized, the item which the user is currently looking at shouldn't move
or disappear. This item is called the anchor element, so ItemsRepeater and ScrollViewer coordinate
to preserve the location of the anchor element on the screen (that is, in the view port).

This coordination between ItemsRepeater and ScrollViewer is enabled by ScrollViewer implementing
[IScrollProvider](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IScrollProvider).
But ScrollViewer ships in WUX, and didn't implement that interface until RS5, and
so anchoring doesn't work for an ItemsRepeater on an app running on RS4 or below.

The solution for this is the new `ItemsRepeaterScrollHost` control in this spec.
This wraps the ScrollViewer, and and do the coordination with the ItemsRepeater.

```xaml
<ItemsRepeaterScrollHost>
    <ScrollViewer>
        <ItemsRepeater ... />
    </ScrollViewer>
</ItemsRepeaterScrollHost> 
```

# Description
<!-- Use this section to provide a brief description of the feature.
For an example, see the introduction to the PasswordBox control 
(http://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box). -->

Use the ItemsRepeaterScrollHost when you're using an 
[ItemsRepeater](https://docs.microsoft.com/en-us/uwp/api/microsoft.ui.xaml.controls.itemsrepeater) 
in a [ScrollViewer](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ScrollViewer),
and your app will run on versions of Windows prior to Windows 10 1809. If your app will only run
on versions of Windows 1809 or higher, there is no need to use this control.

# Examples
This example shows a scrollable list of people.

```xaml
<Page
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:controls="using:Microsoft.UI.Xaml.Controls">

    <controls:ItemsRepeaterScrollHost>
        <ScrollViewer>
            <controls:ItemsRepeater ItemsSource='{x:Bind PeopleCollection}' />
        </ScrollViewer>
    </controls:ItemsRepeaterScrollHost> 
    
</Page
```

# Remarks

# API Notes
<!-- Option 1: Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. For properties, specify the default value of the property if it
isn't the type's default (for example an int-typed property that doesn't default to zero.) -->

<!-- Option 2: Put these descriptions in the below API Details section,
with a "///" comment above the member or type. -->

# API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->

# Appendix
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->
