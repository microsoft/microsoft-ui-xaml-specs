
# Background
Currently, NavigationView's MenuItems list allows for displaying a flat list of items in the pane. 
It's common for apps to want to present users with a hierarchical navigation tree. 
This feature adds the capability to nest items within the pane. 


# Hierarchy
To show a hierarchical list of nested navigation items in the pane, use NavigationViewItem's `MenuItems` property.
Each *NavigationViewItem* can contain other NavigationViewItems and organizing elements like item headers and separators. 
Although NavigationViewItem can contain any number of nested levels, we recommend keeping your app’s navigation hierarchy shallow. 
We believe two levels is ideal for usability and comprehension.

NavigationView shows hierarchy in all its pane display modes, including Top and LeftCompact.
![NavigationView in Left, LeftCompact, and Top modes showing hierarchy](NavigationView_Hierarchy.png)


# Examples
<!-- Use this section to explain the features of the API, showing
example code with each description. The general format is: 
  feature explanation,
  example code
  feature explanation,
  example code
  etc.-->

<!-- As an example of this section, see the Examples section for the PasswordBox control 
(https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box#examples). -->

Declare app navigation hierarchy in markup.

````Xaml
<muxc:NavigationView>
        <muxc:NavigationView.MenuItems>
            <muxc:NavigationViewItem Content="Home" Icon="Home" ToolTipService.ToolTip="Home"/>
            <muxc:NavigationViewItem Content="Collections" 
                            Icon="Keyboard" ToolTipService.ToolTip="Collections">
                <muxc:NavigationViewItem.MenuItems>
                    <muxc:NavigationViewItem Content="Bookshelf" 
                                Icon="Library" ToolTipService.ToolTip="Bookshelf"/>
                    <muxc:NavigationViewItem Content="Mail" 
                                Icon="Mail" ToolTipService.ToolTip="Mail"/>
                </muxc:NavigationViewItem.MenuItems>
            </muxc:NavigationViewItem>
        </muxc:NavigationView.MenuItems>
    </muxc:NavigationView>
````


# Remarks
<!-- Explanation and guidance that doesn't fit into the Examples
section.  For example, see the Remarks for the MediaPlayerElement 
(https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaPlayerElement#remarks). -->


# API Notes
<!-- Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. -->


# API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->

# Appendix
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->
