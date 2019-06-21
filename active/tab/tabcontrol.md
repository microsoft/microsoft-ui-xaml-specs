<!-- The purpose of this spec is to describe a new feature and
its APIs that make up a new feature in WinUI. -->

<!-- There are two audiences for the spec. The first are people
that want to evaluate and give feedback on the API, as part of
the submission process.  When it's complete
it will be incorporated into the public documentation at
docs.microsoft.com (http://docs.microsoft.com/uwp/toolkits/winui/).
Hopefully we'll be able to copy it mostly verbatim.
So the second audience is everyone that reads there to learn how
and why to use this API. -->

# Background
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

> See [proposal document](https://github.com/Microsoft/microsoft-ui-xaml/issues/304) for more background

The Tab control is a way to display a set of tabs and their respective content. Tab controls are useful for displaying several pages (or documents) of content while giving a user the capability to rearrange, open, or close new tabs. 

![Tabs in Edge](https://user-images.githubusercontent.com/25991996/52679758-f6d6ad80-2eea-11e9-9955-fd111c26d982.png)

Tab-like UI comes in two distinct styles that affect not only the visualization of the control, but also the user experience:
* Static tabs
    * Generally display a set number of pages
    * User can change between pages, but cannot open new pages, close pages, or rearrange pages
* Document tabs
    * Display a variable number of pages
    * User can add, remove, and rearrange pages
    * User can "tear off" tabs into their own window or "recombine" tabs from one group into another group

The Xaml platform already provides several controls to represent "static-style tabs" including [Pivot](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/pivot) and top [NavigationView](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/navigationview#display-modes). However, these controls do not well support "document-style" tabs due to a variety of limitations, including:
* Significant effort up to (and including) retemplating to build simple tabs
* No built-in support for closing tabs
* No built-in support for drag/drop into new windows
* No built-in support for moving a tab from a window and combining it with another window
* Limited keyboard and accessibility support

This spec therefore covers "document-style tabs" specifically as this platform gap isn't easily solved with an app-side workaround. 

# Description
<!-- Use this section to provide a brief description of the feature.
For an example, see the introduction to the PasswordBox control 
(http://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box). -->

The Tab control is a collection of tabs that each represents a new page or document in your app. Tab controls are useful when your app has several pages of content and the user expects to be able to add, close, and rearrange the pages.

**Is this the right control?**

Use a TabControl to help the user manage multiple app pages or documents within the same window. 

Do not use a TabControl to display a static set of tabs that the user cannot rearrange, open, or close. Use a Pivot or top NavigationView instead. 

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

## To replicate the behavior of Microsoft Edge

``` xml
<TabControl TabWidthBehavior="Equal"
            CanCloseTabs="True"
            CloseButtonOverlay="OnHover"
            CanDragItems="True"
            CanReorderItems="True"
            TabDraggedOutside="OpenTabInNewWindow">
    <TabControl.TabActionContent>
        <Button Style="{ThemeResource AddNewTabButtonStyle}" Click="NewTab_Click" />
    </TabControl.TabActionContent>
    ...
</TabControl>
```

## Put tabs in the titlebar area

![A sample app that shows the Tab control can extend into the titlebar area](images/tab-extend-to-title.png)

This sample demonstrates how to extend the TabControl into the title bar area and also specify a portion of the UI as the draggable region. Per the [titlebar customization guidelines](https://docs.microsoft.com/en-us/windows/uwp/design/shell/title-bar), we must set a [specific drag region](https://docs.microsoft.com/en-us/windows/uwp/design/shell/title-bar#draggable-regions). If we don't specify the drag region, the entire titlebar area remains draggable (meaning input won't be routed to the tabs, making the tabs unclickable).

``` xml
<Page>
    <Grid>
        <TabControl>
            <TabItem Icon="Home" Header="Home" IsCloseable="False" />
            <TabItem Icon="Document" Header="Document 1" />
            <TabItem Icon="Document" Header="Document 2" />
            <TabItem Icon="Document" Header="Document 3" />
        </TabControl>

        <Grid x:Name="CustomDragRegion" Width="200" Height="40" HorizontalAlignment="Right" VerticalAlignment="Top" />
    </Grid>
</Page>
```

``` csharp
// Customize the titlebar area using the guidance from here: https://docs.microsoft.com/en-us/windows/uwp/design/shell/title-bar
public MainPage()
{
    this.InitializeComponent();

    var coreTitleBar = CoreApplication.GetCurrentView().TitleBar;
    coreTitleBar.ExtendViewIntoTitleBar = true;

    Window.Current.SetTitleBar(CustomDragRegion);
}
```

## Create a new window when tearing out tabs

![An example picture showing that a tab can be torn out and moved to its own window.](images/tab-new-window.png)

See the [TabView tear out sample](https://github.com/windows-toolkit/Sample-TabView-TearOff/tree/master/TabViewTear) for a more complete sample.

``` xml
<TabControl CanDragItems="True"
            CanReorderItems="True"
            TabDraggedOutside="TabControl_TabDraggedOutside">
```
``` csharp
// NOTE: The app is responsible for writing this code. We will provide a sample that may look something like:
private async void TabControl_TabDraggedOutside(object sender, TabDraggedOutsideEventArgs e)
{
    // Create a new AppWindow
    AppWindow newWindow = await AppWindow.TryCreateAsync();

    // Create the content for the new window
    var newPage = new MainPage();

    // Remove tab from existing list
    Tabs.Items.Remove(e.Tab);

    // Add tab to list of Tabs on new page
    newPage.AddItemToTabs(e.Tab);

    // Set the Window's content to the new page
    ElementCompositionPreview.SetAppWindowContent(newWindow, newPage);

    // Show the window
    await newWindow.TryShowAsync();
}
```

## Databind to a set of tabs 

``` xml
<TabControl ItemsSource="{x:Bind TabItemCollection}" />
```

# Remarks
<!-- Explanation and guidance that doesn't fit into the Examples
section.  For example, see the Remarks for the MediaPlayerElement 
(https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaPlayerElement#remarks). -->

## Keyboarding behavior
### TAB and Arrow key behavior
The TabControl has areas containing left content, right content, and the Tab content (containing 0 or more Tabs). When the user hits the TAB key, focus moves between the content areas. When focus moves into the Tab content area, the selected Tab gains focus. Once the user is in the Tab content area, pressing the arrow key will move focus between the tabs. Arrow focus is trapped inside the Tab content area.

### Selecting a Tab
When a Tab has focus, pressing SPACE or ENTER will select that Tab.

### Shortcuts for moving between tabs
Ctrl+TAB will select the next Tab.
Ctrl+Shift+TAB will select the previous Tab.
Tab selection cycles (meaning if the user has selected the last tab and presses Ctrl+TAB, the first tab becomes selected).

### Closing a Tab 
Hitting CTRL+F4 will close the selected Tab (assuming it is closable). 

### Keyboard guidance for App Developers
The above sections outline built-in keyboarding behavior provided by the TabControl. However, there are certain expected keyboard shortcuts that you will be responsible for implementing. 
* If your app does not already handle the CTRL+T shortcut, consider using it to open a new tab.
* If your app does not already handle the CTRL+W shortcut, consider using it to close the selected tab.
* Consider maintaining a list of recently closed Tabs. If the user presses CTRL+SHIFT+T, they expect recently closed tabs to be reopened.
* Modern browsers support selecting tabs when pressing CTRL and a number. Consider handling Ctrl+1 through 8, which should select that number Tab. Additionally, you can consider handling Ctrl+9 which selects the last tab (regardless of the number of tabs).
* If users can perform more commands on a Tab than just closing the Tab (for example, pinning a Tab or duplicating a Tab), consider adding a context menu to the Tab. 

# API Notes
<!-- Give a one or two line description of each API (type
and member), or at least the ones that aren't obvious
from their name.  These descriptions are what show up
in IntelliSense. -->

### TabControl properties and events

| Property | Type | Description |
|:-------- |:---- |:----------- |
| AddTabButtonVisibility | Visibility | Determines if the plus button appears to the right of the tab strip |
| TabStripHeader | object | Content to the left of the tab strip. |
| TabStripFooter | object | Content to the right of the tab strip. |

| Event | Description |
|---|---|
| AddTabButtonClick | Fires when the + button is clicked. |
| TabClosing | Fires when a tab is about to be closed. Can be cancelled to prevent closure. |
| TabDraggedOutside | Fires when a Tab is dragged outside of the Tab bar. |

### TabItem properties and events

| Property | Type | Description |
|:-------- |:---- |:----------- |
| Content | object | The main content that appears in the tab. |
| Header | object | The content that appears inside the tab itself.  |
| HeaderTemplate | DataTemplate | Template for the header object. |
| Icon | IconElement | Icon for the tab. |
| IsClosable | bool | Determines if the tab shows a close button. |

| Event | Description |
|---|---|
| TabClosing | Fires when a tab's close button is clicked. |

# API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->

# Appendix
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->

![Parts of a tab item](https://user-images.githubusercontent.com/25991996/53277900-35beed00-36bb-11e9-910e-36119f7ad4af.png)

![Position of TabHeader TabFooter and TabActionContent](https://user-images.githubusercontent.com/25991996/53277918-4c654400-36bb-11e9-9d0d-4fac948bc9f0.png)

# Open Questions
