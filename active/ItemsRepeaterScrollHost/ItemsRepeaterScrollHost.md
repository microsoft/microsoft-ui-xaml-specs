# Background
Some terminology: "WUX" is the Windows.UI.Xaml that ships in Windows.
"MUX" is the Microsoft.UI.Xaml that ships on Nuget and goes downlevel to 
earlier versions of Windows 10 than the latest.

The Xaml [ItemsRepeater](https://docs.microsoft.com/en-us/uwp/api/microsoft.ui.xaml.controls.itemsrepeater)
control ships in MUX and shows a collection of items, like a more primitive ListView control. Typically 
lists are large and need to be scrolled, so it's put into a
[ScrollViewer](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ScrollViewer) control.

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
But ScrollViewer ships in WUX, starting in RS5, and so anchoring doesn't work for an
ItemsRepeater on an app running on RS4 or below.


<!-- Use this section to provide background context for the new API(s) 
in this spec. -->

<!-- This section and the appendix are the only sections that likely
do not get copied to docs.microsoft.com; they're just an aid to reading this spec. -->

<!-- If you're modifying an existing API, included a link here to the
existing page(s) -->

<!-- For example, this section is a place to explain why you're adding this API rather than
modifying an existing API. -->

<!-- For example, this is a place to provide a brief explanation of some dependent
area, just explanation enough to understand this new API, rather than telling
the reader "go read 100 pages of background information posted at ...". -->


# Description
<!-- Use this section to provide a brief description of the feature.
For an example, see the introduction to the PasswordBox control 
(http://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box). -->


# Examples
<!-- Use this section to explain the features of the API, showing
example code with each description. The general format is: 
  feature explanation,
  example code
  feature explanation,
  example code
  etc.-->
  
<!-- Code samples should be in C# and/or C++/WinRT -->

<!-- As an example of this section, see the Examples section for the PasswordBox control 
(https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box#examples). -->


# Remarks
<!-- Explanation and guidance that doesn't fit into the Examples section. -->

<!-- APIs should only throw exceptions in exceptional conditions; basically,
only when there's a bug in the caller, such as argument exception.  But if for some
reason it's necessary for a caller to catch an exception from an API, call that
out with an explanation either here or in the Examples -->

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
