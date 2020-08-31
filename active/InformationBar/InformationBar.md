# Background
> This spec corresponds to [issue 913](https://github.com/microsoft/microsoft-ui-xaml/issues/913) on the WinUI repo.

Users should be informed about essential status changes that occur on an app level.
These status changes affect the app as a whole and can be either critical or informational.
Critical status changes like lost internet connectivity are directly impactful to app functionality while informational status changes like an update has completed and been applied are indirectly impactful to app functionality.
These notifications and corresponding information should be presented in a consistent, predictable, and relevant way to the user depending on the specific scenario.

For example the "Error while saving" message in this sample app:

![initial example](images/initial-example.jpg)

Currently, [TeachingTip](https://docs.microsoft.com/uwp/api/Microsoft.UI.Xaml.Controls.TeachingTip), 
[ContentDialog](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentDialog), 
and customizations of other [Flyouts](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Flyout) and dialogs exist as options to show these notifications but these controls were not specifically designed to handle app-wide status change notifications. 
Due to their visual layouts, inherent intrusiveness, or available features they are not sufficient for displaying notifications at an app-wide level.

This spec introduces an InfoBar WinUI (Xaml) control that an app can use for these kinds of messages.

# InfoBar Class

An InfoBar is a persistent, actionable, app-wide notification intended for displaying critical and/or actionable information that impact app perception or user experience.

## Is this the right control?
Use an InfoBar control when a user should be informed of, acknowledge, or take action on a message. By default the notification will remain in the content area until dismissed by the user but will not necessarily break user flow.

An InfoBar will take up space in your layout and behave like any other child elements. It will not cover up other content or float on top of it.

Do not use an InfoBar control to confirm or respond directly to a user action, for time-sensitive alerts, or for non-essential messages.

### Remarks
Use an InfoBar that dismiss via the user or when the status is resolved for scenarios that **directly** impact app perception or experience ⚠

- Internet connectivity lost
- Error while saving a document when triggered automatically, not related to specific user action
- No microphone plugged in when attempting to record
- Can't connect to your phone
- The subscription to the application is expired

Use an InfoBar that dismiss via the user or a timer for scenarios that **indirectly** impact app perception or experience ℹ

- A call has begun recording
- Update applied with link to 'Release Notes'
- The terms of service have been updated and require acknowledgement
- An app-wide backup has successfully, asynchronously completed
- The subscription to the application is close to expiring


#### When should a different control be used?

There are some scenarios where a ContentDialog, Flyout, or TeachingTip may be more appropriate to use.

- For scenarios where a persistent notification is not needed, e.g. displaying information in context of a specific UI element, a [Flyout](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/dialogs-and-flyouts/flyouts) is a better option. 
- For scenarios where the application is confirming a user action, showing information the user ***must*** read, use a [ContentDialog](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/dialogs-and-flyouts/dialogs).
  - Additionally, if a status change to the app is so severe that it needs to block all further ability for the user to interact with the app, use a ContentDialog.
- For scenarios where a notification is a transient teaching moment, a [TeachingTip](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/dialogs-and-flyouts/teaching-tip) is a better option.

For more info about choosing the right notification control, see the [Dialogs and Flyouts](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/dialogs-and-flyouts/) article.

# Examples

An info bar can have several configurations, here are some notable ones.

When an InfoBar is conveying information of a common severity, like displaying an error or warning, it can be set to have one of many severity levels to use consistent [Fluent](https://docs.microsoft.com/en-us/windows/apps/fluent-design-system) styling for it's identifiers.
![A mockup of an InfoBar in a Warning state with a close button and a message](images/Warning_NoButton.png)

If a call to action is needed, an info bar can have customizable action or hyperlink buttons.
![A mockup of an InfoBar](images/Default_MessageHyperlink.png)

The info bar can also be customized with its optional properties and with XAML Content to include extra buttons and other UI elements.
![A mockup of an InfoBar with custom status color and content](images/Custom_IconColor.png)

## Create an InfoBar

The XAML below describes an inline InfoBar with the default styling for an error  notification. An info bar can be created anywhere in the element tree or code behind. In this example the InfoBar expands to fill the width of the StackPanel layout control it resides in.

By default, the InfoBar will not be visible. Set the IsOpen property to true in the XAML or code behind to display the information.

XAML
```xml
<Grid>
    <!--Other UI content -->
    <StackPanel x:Name="ContentArea">
        <controls:InfoBar x:Name="UnsuccessfulSaveNotification"
            Severity="Warning"
            Title="Error while saving"
            Message="Your document was unable to be saved.">
        </controls:InfoBar>
    </StackPanel>
    <!--Other UI content -->
<Grid>
```
C#
```C#
public MainPage()
{
    this.InitializeComponent();

    if(!SaveSuccessful())
    {
        UnsuccessfulSaveNotification.IsOpen = true;
    }
}
```

Here is the visual representation of the info bar in the page.

![A mockup of an InfoBar in a Warning state with a close button and a message](images/Warning_NoButton.png)

## Using pre-defined severity levels
The type of the info bar can be set via the Severity property to automatically set a consistent status color, icon, and assistive technology settings dependent on the criticality of the notification.

Mockups in various Severities to be added.
![Mockup of InfoBar with no content in different Severity colors and icons for light mode](images/Docked_ToolkitSeverity.png)

![Mockup of InfoBar with no content in different Severity colors and icons for dark mode](images/Docked_ToolkitSeverityDark.png)

## Programmatic dismiss in info bar
An info bar can be dismissed by the user via the close button or programmatically. If the notification is required to be in view until the status is resolved and you would like to remove the ability for the user to dismiss the info bar, you can set the IsUserDismissable property to false.
By default, the close button will appear as an 'X' and the IsUserDismissable property is therefore set to true.


XAML
```xml
<StackPanel x:Name="ContentArea">
    <controls:InfoBar x:Name="NoInternetNotification"
        Severity="Error"
        Title="No Internet"
        Message="Reconnect to continue working."
        IsUserDismissable="False">
    </controls:InfoBar>
</StackPanel>
```
![Mockup of an InfoBar in a Critical state with no close button](images/Critical_IconNoClose.png)

## Custom styling: status color and icon
Outside of the pre-defined severity levels, the Background and IconSource properties can be set to customize the icon and background color. The info bar will retain the assistive technology settings of the severity defined, or default if none was defined.

A custom background color can be set via the standard Background property and will override the color set by Severity. Please keep in mind content readability and accessibility when setting your own color.

A custom icon can be set via the IconSource property. By default, an icon will be visible. This icon can be removed by setting the IsIconVisible property to false. For custom icons, the recommended icon size is 20px.

XAML
```xml
<StackPanel x:Name="ContentArea">
    <controls:InfoBar x:Name="NewPhotosNotification"
        Title="New Photos From Cloud"
        Message="Sync your latest photos to the Cloud"
        Background="#FF557EB9"
        IconSource="Camera">
        <controls:InfoBar.ActionButton>
            <Button Content="Upload" Command="{x:Bind UploadPhoto}"/>
        </controls:InfoBar.ActionButton>
    </controls:InfoBar>
</StackPanel>
```

![A mockup of an InfoBar in the top of the content area. The notification's title is "New Photos From Cloud" and it's message is "Sync your latest photos to the Cloud". It has a bright green accent color and a "Camera" icon.](images/Custom_IconColor.png)

## Add an action button
By default, an 'X' close button will appear as the right most component in the bar.

An additional action button can be added by defining your own button that inherits [ButtonBase](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Controls.Primitives.ButtonBase) and setting it in the ActionButton property. Custom styling will be applied to action buttons of type Button and HyperlinkButton for consistency and accessibility. Outside of the ActionButton, additional action buttons can be added via custom content and will appear below the message.

XAML
```xml
<StackPanel x:Name="ContentArea">
    <controls:InfoBar x:Name="UnsuccessfulSaveNotification"
        Severity="Warning"
        Title="Error while saving"
        Message="Your document was unable to be saved.">
        <controls:InfoBar.ActionButton>
            <Button Content="Try Again" Command="{x:Bind SaveDocuments}"/>
        </controls:InfoBar.ActionButton>
    </controls:InfoBar>
</StackPanel>
```

![A mockup of an InfoBar with a single line message and an action button](images/Warning_Button.png)

XAML
```xml
<StackPanel x:Name="ContentArea">
    <controls:InfoBar x:Name="ConnectionErrorNotification"
        Title="Error while saving"
        Message="Lorem ipsum long message">
        <controls:InfoBar.ActionButton>
            <HyperlinkButton
                Content="www.microsoft.com" 
                NavigateUri="http://www.microsoft.com"/>
        <controls:InfoBar.ActionButton/>
    </controls:InfoBar>
</StackPanel>
```
![A mockup of an InfoBar with a single line message and hyperlink](images/Default_MessageHyperlink.png)
![A mockup of an InfoBar with a message expanding multiple lines and a hyperlink](images/Docked_MultiLineIconTitleHyperlink.png)

## Custom content
Content can be added to an InfoBar using the Content property. It will appear below the message and above the action or hyperlink buttons if defined and multiline. The InfoBar will expand to fit the content defined.

XAML
```xml
<StackPanel x:Name="ContentArea">
    <controls:InfoBar x:Name="TermsAndConditionsNotification"
        Title="Backup in progress"  
        Message="Your documents are being saved to the cloud">
        <controls:InfoBar.Content>
            <ProgressBar IsIndeterminate="True"/>
        </controls:InfoBar.Content>
    </controls:InfoBar>
</StackPanel>
```

![A mockup of an InfoBar in its default state with an indeterminate progress bar](images/Default_CustomContent.gif)


## Content wrapping
By default, the text set in the Message property will wrap vertically in the control underneath the other visual components.

XAML
```xml
<StackPanel x:Name="ContentArea">
    <controls:InfoBar x:Name="DefaultErrorNotification"
        Severity="Error"
        Title="Message Title"  
        Message="This message is very long, so long in fact it needs to wrap to a second line in the notification">
    </controls:InfoBar>
</StackPanel>
```

![A mockup of a multiline message InfoBar](images/Docked_MultiLineIconTitle.png)
![A mockup of a multiline title and message InfoBar](images/Docked_MultiLineLongTitle.png)

## Canceling and deferring close
The Closing event can be used to cancel and/or defer the close of an InfoBar. This can be used to keep the InfoBar open or allow time for an action or custom animation to occur. When the closing of an InfoBar is canceled, IsOpen will go back to true, however, it will stay false during the deferral. A programmatic close can also be canceled.

XAML
```xml
<controls:InfoBar x:Name="UpdateAvailable"
    Title="Update Available"
    Message="Please close this tip to apply required security updates to this application"
    Closing="InfoBar_Closing">
</controls:InfoBar>
```
C#
```C#
public void InfoBar_Closing(InfoBar sender, InfoBarClosingEventArgs args)
{
    if (args.Reason == InfoBarCloseReason.CloseButton) {
        // if scenario failed
        args.Cancel = true;
    }
    
}
```

# Inputs and Accessibility

## UI Automation Patterns 

InfoBar will use a Pane control pattern for inline notifications and will implement a custom "information" Landmark.

### Keyboard Navigation 
- No action is needed to invoke the InfoBar
- After navigating to the control via tabbing, the user is automatically focused into the control once navigating to the first actionable item.
- Once focused, tabbing will iterate through all actionable items in the control in order. When tab is pressed on the last element in the InfoBar, focus will continue throughout the page.
  - A user can also use the left and right arrow keys to navigate between the available buttons
- To close the InfoBar the action or close button needs to be pressed.
  - Note: Escape will not close the InfoBar and will instead bubble up the command to the parent components.

### Gamepad

- The actionable items in the control can be navigated to via spatial navigation.
- The 'A' button will interact with the item in focus, such as "press" the action or close buttons
- The InfoBar can be closed via pressing the 'X' close button, pressing the action button, or the 'B' button which will return focus to the element previously in focus.
### Assistive Technologies

InfoBar will leverage the existing APIs used by Windows Notifications. 

The behavior of the InfoBar will change for assistive technologies like Narrator depending on the Severity set by the developer. As Error and Warning InfoBars are intended to be used for scenarios that directly impact app experience they should interrupt the user more than InfoBars that are informational. View [NotificationProcessing ](https://docs.microsoft.com/en-us/windows/win32/api/uiautomationcore/ne-uiautomationcore-notificationprocessing) docs for more information on the varied intended behavior.

| Severity |  NotificationProcessing | Behavior in Narrator|
|:--- | :---| :---|
| Error | NotificationProcessing_ImportantAll| Add new item to the end of the queue. It doesn’t matter the source, what is currently speaking or any other items in the queue queued with a higher priority than normal text. <br><br> This will cause it to get spoken after the current utterance/string finishes. Meaning it won't interrupt the speech but it will queue itself next to be spoken, ahead of anything already queued. <br><br> Focus and keyboard will NOT interrupt or flush, pressing control will silence and flush all |
| Warning | NotificationProcessing_ImportantAll| See above |
| Success | Processing_All| Add new item to the end of the queue. <br><br> Will be added to the end of the queue meaning all existing queued text will need to be spoken before this text will be spoken. <br><br>Focus, keyboard and control will silence/flush them all
| Default | Processing_All | See above

#### Narrator

- Entry behavior for InfoBars based on Severity:
  - Error and Warning: The most recently alerted InfoBar will take priority over other queued content and Narrator will say "Click up to move to new information from" + App Name + Notification Contents. 
    - The InfoBar message will not be silenced via keyboarding or focus.
  - Default and Success: The InfoBar will appear to the user after the current queued content is iterated through and Narrator will then say "Click up to move to new information from" + App Name + Notification Contents. 
    - The InfoBar message can be easily silenced via keyboarding, focus change, or the control key. However, a user can also navigate to the InfoBar via tabbing if silenced.
- For all InfoBars, Ctrl + Narrator + Up arrow will move focus to the first actionable item in the InfoBar after the user is notified and read the element in focus.
- A user can press F6 to navigate to the first actionable item in the InfoBar.
- For touch screen devices, swiping will navigate through all actionable items, regardless of group, in order. When Swiping on the last element in the notification, focus will move to Narrator's fullscreen invisible Close Button and the user may double tap the screen to close the window. Swiping again will move focus out of the notification.

## Enter and Exit Usability
### Flashing content
The InfoBar should not appear and disappear from view rapidly to prevent flashing on the screen. Avoid flashing visuals for people with photosensitivities and to improve the usability of your application.

For InfoBars that automatically enter and exit the view via an app status condition, we recommend you include logic in your application to prevent content from appearing or disappearing rapidly or multiple times in a row. However, in general, this control should be used for long-lived status messages.

### Inline messages offsetting content
For InfoBars that are inline with other UI content, keep in mind how the rest of the page will responsively react to the addition of the element.

Messages with a substantial height could dramatically alter the layout of the other elements on the page. 
If the InfoBar appears or disappears rapidly, especially in succession, the user may be confused with the changing visual state.

# Globalization and Localization

## Color and Icon
When customizing the color and icon outside of the preset Severity levels, keep in mind user expectations for the connotations from the set of standard icons and colors.

Additionally, the preset Severity colors have already been designed for theme changes, high-contrast mode, color confusion accessibility, and contrast with foreground colors. We recommend to use these colors when possible and to include custom logic in your application to adapt to the various color states and accessibility features.

Please view the UX guidance for [Standard Icons](https://docs.microsoft.com/en-us/windows/win32/uxguide/vis-std-icons) and [Color](https://docs.microsoft.com/en-us/windows/win32/uxguide/vis-color) to ensure your message is communicated clearly and accessible to users.

### Severity
 Avoid setting the Severity property for a notification that does not match the information communicated in the Title, Message, or custom content.
 
 The accompanying information should aim to communicate the following to use that Severity.
 - Error: An error or problem that has occurred.
 - Warning: A condition that might cause a problem in the future.
 - Success: A long-running and/or background task has completed.
 - Default: General information that requires the user's attention.

Icons and color should not be the only UI components signifying meaning for your notification. Text in the notification's Title and/or Message should be included to display information.

## Message 

Text in your notification will not be a constant length in all languages. For the Title and Message property this may impact whether your notification will expand to a second line. 
We recommend you avoid positioning based on message length or other UI elements set to a specific language.

The notification will follow standard mirroring behavior when localized to/from languages that are right to left (RTL) or left to right (LTR). The icon will only mirror if there is directionality.

Please view the guidance for [Adjust layout and fonts, and support RTL](https://docs.microsoft.com/en-us/windows/uwp/design/globalizing/adjust-layout-and-fonts--and-support-rtl) for more information about text localization in your notification.

# Remarks
## Usage Recommendations

### When to show an info bar?
An InfoBar should be shown when the state of the application is different from typical, expected functionality or when the user **needs** to acknowledge or potentially react to the presented information.

Recommended patterns for error notifications where the application is in a negatively altered state
  - Example: Internet connectivity is required for the application to function and is not present
    - An error info bar should remain in view until the connection is restored if most functionality is unavailable.
    - If some app functionality is possible, an error info bar should appear and able to be dismissed by the user.
    - If/when the internet is restored the existing notification should update if it exists, and a new one should be created if the previous one had been dismissed. THe new/updated success-styled InfoBar should inform the user that "Internet is reconnected" so that they are aware app functionality is restored.
  - Example: The user's subscription to your application has expired and many features are deactivated
    - An error info bar could remain in view with a potential action button to resubscribe.
    - Another option is to allow the user to dismiss a default-style InfoBar depending on how vital a subscription is to your application.

Recommended patterns for informational notifications where the user needs to view or react to essential information
 - Example: Terms and Conditions of the application have been updated and need to be acknowledged for continued use
   - A default info bar could appear notifying the user the T&C have updated and link to the contents. The user acknowledges these updated T&C through dismissing the notification.
 - Example: A virus scan that was running in the background of your application has completed
   - A success-styled info bar could appear notifying the user the scan has completed and direct them to view the results.

 Note: Users should **always** have the option to dismiss informational notifications.

# API Notes

### Notable Properties  

| Name | Description |
|:-:|:--|
| IsOpen| Gets or sets a value that determines the visibility of the InfoBar. By default, is set to false. |
| Severity | Gets or sets a value that indicates the  color and icon to style the InfoBar. It will also define the assistive technology settings. |
| IsUserDismissable| Gets or sets a boolean that indicates whether the user will be able to dismiss the InfoBar.


### Events  
| Name | Description |
|:-:|:--|
| CloseButtonClick | Occurs after the close button has been tapped. |
| Closed | Occurs after the info bar is closed. |
| Closing |Occurs just before the info bar begins to close. |


# API Details

```c++
enum InfoBarCloseReason
{
    CloseButton = 0,
    Programmatic = 1,
};

enum InfoBarSeverity
{
    Error = 0,
    Warning = 1,
    Success = 2,
    Default = 3,
}

runtimeclass InfoBarClosedEventArgs
{
    InfoBarCloseReason Reason{ get; };
};

runtimeclass InfoBarClosingEventArgs
{
    InfoBarCloseReason Reason{ get; };
    Boolean Cancel;
    Windows.Foundation.Deferral GetDeferral();
};

unsealed runtimeclass InfoBarTemplateSettings : Windows.UI.Xaml.DependencyObject
{
    InfoBarTemplateSettings();
    Windows.UI.Xaml.Controls.IconElement IconElement;

    static Windows.UI.Xaml.DependencyProperty IconElementProperty{ get; };
}

unsealed runtimeclass InfoBar : Windows.UI.Xaml.Controls.ContentControl
{
    InfoBar();

    String Title;
    String Message;

    Boolean IsOpen;
    Boolean IsUserDismissable;
    Boolean IsIconVisible;

    Windows.UI.Xaml.Controls.Primitives.ButtonBase ActionButton;

    Windows.UI.Xaml.Style CloseButtonStyle;
    Windows.UI.Xaml.Input.ICommand CloseButtonCommand;
    Object CloseButtonCommandParameter;

    InfoBarSeverity Severity;
    IconSource IconSource;

    InfoBarTemplateSettings TemplateSettings{ get; };
    Object Content{ get; set; };
    Windows.UI.Xaml.DataTemplate ContentTemplate{ get; set; };

    event Windows.Foundation.TypedEventHandler<InfoBar, Object> CloseButtonClick;
    event Windows.Foundation.TypedEventHandler<InfoBar, InfoBarClosingEventArgs> Closing;
    event Windows.Foundation.TypedEventHandler<InfoBar, InfoBarClosedEventArgs> Closed;

    static Windows.UI.Xaml.DependencyProperty IsOpenProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty TitleProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty MessageProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty SeverityProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty IconSourceProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty IsIconVisibleProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty IsUserDismissableProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty CloseButtonStyleProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty CloseButtonCommandProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty CloseButtonCommandParameterProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty ActionButton{ get; };
    static Windows.UI.Xaml.DependencyProperty ContentProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty ContentTemplateProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty TemplateSettingsProperty{ get; };
}
```

## Theme Resources
| Name | Description |
|:-:|:--|
|InfoBarSeverityErrorBackground | Sets the background color of the InfoBar when in the Error severity. |
|InfoBarSeverityWarningBackground | Sets the background color of the InfoBar when in the Warning severity. |
|InfoBarSeveritySuccessBackground | Sets the background color of the InfoBar when in the Success severity. |
|InfoBarSeverityDefaultBackground | Sets the background color of the InfoBar when in the Default severity. |
|InfoBarHyperlinkForeground | Sets the hyperlink button text color. <br> - Note: This is set to keep hyperlinks accessible on the variously colored backgrounds defined by the severity background colors.
|InfoBarFontSize | Sets the text font size of the InfoBar.
|InfoBarMinHeight | Sets the minimum height of the InfoBar.
|InfoBarCloseButtonSize | Sets the close button area size of the InfoBar.
|InfoBarCloseButtonGlyphSize | Sets the close button glyph size of the InfoBar.

# Appendix

### Design References

UI Elements for InfoBar
![Toolkit page for InfoBar UI elements](images/Docked_Toolkit.png)


### Visual Components

 | Component |  Notes |
|:---:|:---|
| Container | - We recommend to place InfoBars in a layout control where the control can expand horizontally to the width of the content area.
| Icon | - Defined by either the Severity or by IconSource <br>
| Title | - Semi-bolded and appears right of the Icon <br> - Recommended to be 50 characters or less
| Message | - Will appear to the right of the Title in single-height notifications, otherwise will be on a new line <br> - Recommended to be 512 characters or less
| Hyperlink | - Will appear to the right of the Message in single-height notifications, otherwise will be on a new line
| Close button | - Will appear as 'X' by default <br> - Can be removed via IsProgrammaticDismissal
| Action button |  - Additional action buttons may be added through custom XAML content
| Content | - Can be customizable to include text, hyperlinks, and any other XAML content <br> - Appears between the Title/Message and any Action or Close buttons

## Behavioral Components
 | Property | Notes |
|:---:|:---|
| Opening | - An info bar is shown by setting its IsOpen property to true. |
| Closing | There are two ways an info bar can close: <br>- The program sets the IsOpen property to false <br> - The user invokes the Close button. <br> Use the InfoBarCloseReason to determine which case has occurred. <br> Closing can be prevented by setting the Cancel property to true. You can use a deferral to respond asynchronously to the event. |

## Data and Intelligence Metrics
Recommendations from ryandemo:
- How many buttons included correlated to severity of status message
- Track popularity of each layout mode
- Average length of time the notifications display on screen until dismissal, correlated to severity
- How often color and/or icon customization Occurs
- How often multiple info bars appear at once and the typical distribution
- F6 usage

## Intended features for InfoBar v2
- Built-in support for floating notifications, DisplayMode property to switch between "Docked" (current) and "Floating" mode
  - Options to show the InfoBar as a PopUp with simple positioning properties
- Positioning and re-positioning for multiple notifications
  - e.g. providing a built-in way to support a group of notifications in the bottom right corner
- Truncation option that allows the user to expand and collapse an InfoBar with multiple lines of content
- Potentially, an ActionContentArea to insert custom content in the same horizontal space as the other UI elements.
