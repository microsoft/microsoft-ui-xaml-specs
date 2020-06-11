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
> This spec corresponds to [issue 913](https://github.com/microsoft/microsoft-ui-xaml/issues/913) on the WinUI repo.

Users should be informed about status changes that occur on an app level (Teaching tips are specifically built for informing users of nonessential options that would improve their experience – there should be a overarching UWP UI element to inform users about essential changes too.)


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

An InfoBar is a persistent, actionable, app-wide, notification intended for displaying critical status messages that impact app perception or user experience.




## Is this the right control?
Use an InfoBar control when a user needs to acknowledge or take action on a message. By default the notification will remain in the content area until dismissed by the user.

Do not use an InfoBar control to confirm or respond to a user action, for transient alerts, or for non-essential messages.

### Scenarios
Common scenarios that **directly** impact app perception or experience ⚠

- Internet connectivity lost
- No microphone plugged in when attempting to record
- Can't connect to your phone
- The subscription to the application is expired

Common scenarios that **indirectly** impact app perception or experience ℹ

- A call has begun recording
- The subscription to the application is close to expiring
- The application in an altered, compatibility mode
- The terms of service have been updated

<!-- 
Control	| Intrusiveness	|	Information Severity	|	Modality	|	Scope	|	Invocation	|	Dismissal	|	User Action
| -	| -	| -	| -	| -	| -	| -	| -	|
InfoBar ⚠	|	Medium	|	High	|	Non-blocking	|	Global	|	Programmatic	|	Programmatic	|	Acknowledge/View
InfoBar ℹ	|	Medium	|	Medium	|	Non-blocking	|	Global	|	Programmatic	|	Manual	|	Acknowledge/Dismiss -->


For more info about choosing the right notification control, see the [Dialogs and Flyouts](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/dialogs-and-flyouts/) article.
 
<!-- 
Control |	Intrusiveness |	Information Severity |	Modality |	Scope	Invocation |	Dismissal |	Potential User Action
|- | - | -| -| -| - | - | - |
Content Dialog |	High |	High |	Blocking |	Global |	User-triggered |	Manual |	Interact/Retreat
InfoBar |	Medium |	High/Medium |	Non-blocking |	Global |	Programmatic |	Manual  or Programmatic |	Interact/View
Flyouts |	Medium |	Medium |	Non-blocking |	Contextual |	User-triggered |	Light-dismiss |	Interact/View
Teaching Tip |	Medium |	Low |	Blocking |	Global/Contextual |	User-triggered |	Manual or Light-Dismiss |	Interact/Acknowledge/View
(Toast) |	Low |	Low |	Non-blocking |	Global |	Programmatic or user-triggered |	Timed |	Interact/View -->

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
> Note here about how it will eventually be two styles, starting with one: bar

A status banner can be of one of two visual modes with several configurations in each.

A status banner can show non-dismissable notifications in the **toast** visual mode when a critical status affects the functionality of the application.

[A sketch of a status banner with a title of, Internet Disconnected, without a dismiss button](images/InternetDisconnectedToast.png)

When the user can take action to resolve a status, a status banner can include an action button and appear at the top of the content pane as an app-wide **bar**.

*echo in your room img*

For informational statuses, a status banner in the **toast** visual mode can be dismissed by the user and include hyperlinks to direct the user to further information.

*now recording img*



## Create a status banner 
The XAML below describes a bar-style status banner with the default styling for a critical notification. A status banner can be created anywhere in the element tree or code behind. In this example, the banner is located in a ResourceDictionary, expanding to fill the width of the stack panel, like a bar.

XAML
```XAML
<StackPanel x:Name="ContentArea" Content="Document">
    <StackPanel.Resources>
        <controls:StatusBanner x:Name="UnsuccessfulSaveBanner"
            Type="Warning"
            Title="Error while saving"
            Message="Your document was unable to be saved.">
        </controls:StatusBanner>
    </StackPanel.Resources>
</StackPanel>
```

C#
```C#
public MainPage()
{
    this.InitializeComponent();

    if(!SaveSuccessful())
    {
        UnsuccessfulSaveBanner.IsOpen = true;
        SetHaveDisplayedErrorBanner();
    }
}
```

Here is the visual representation with of status banner in the page.

<!-- TODO: Insert a 'warning' status banner with title, message, and close button--> [img]

<!--## Pop-up status banner 
In some app scenarios, an app-wide bar does not fit for functional or aesthetic reasons. If a status banner is defined independent of another control, the visual layout will default to a to a toast with the same visual components as the aforementioned bar. The status banner will display relative to the edges of the xaml root and by default will be located in the bottom right corner of the application.

XAML
```XAML
<StackPanel x:Name="ContentArea" Content="Document" />

<controls:StatusBanner x:Name="ConnectionErrorBanner"
    Type="Critical"
    Icon="NetworkOffline"
    Title="No Internet"
    Message="Reconnect to save your work.">
</controls:StatusBanner>
```
[A sketch of a sample application with a status banner as a toast in the bottom right of the content area. The status banner's title is "No Internet" and it's message is "Reconnect to save your work"](images/No_Internet_Toast.png) -->

## Programmatic dismiss in status banner
A status banner can be dismissed by the user via the close button or programmatically. If the notification is required to be in view until the status is resolved you can set the IsProgrammaticDismissal property to true to remove the default dismiss button. 

 > Note: Include a message highlighting the risks with removing a close button. How it can be very intrusive and a back-up removal should be included.

XAML
```XAML
<StackPanel x:Name="ContentArea" Content="Document">
    <StackPanel.Resources>
        <controls:StatusBanner x:Name="UnsuccessfulSaveBanner"
            Type="Warning"
            Title="Error while saving"
            Message="Your document was unable to be saved."
            IsProgrammaticDismiss="True">
        </controls:StatusBanner>
    </StackPanel.Resources>
</StackPanel>
```
<!-- TODO: Insert img of 'warning' status banner with title and message--> [img]

## Banner types: styling
The type of the status banner can be set via the Type property to automatically set a consistent accent color and icon dependent on the criticality of the notification.

Preset color and icon combos, TBD in collaboration w/ design:
- Critical: Fluent red (#D13438) & ErrorBadge (EEA39)
- Warning: Fluent orange (#FF8C00) & Error (E783)
- Informational: Theme accent or Fluent blue (#0078D7) & Info (E946)
- Success: Fluent green (#107C10) & StatusCircleCheckmark (F13E)
- Default if no type is set: Fluent gray (#6979E) & no icon

TBD: Should these be part of light-weight styling to be set across app? Like data validation? Or separate?

<!-- TODO: Insert img of success & default bar examples for right now, the others are throughout the document--> [img]

## Custom styling: accent color and icon
Outside of the pre-defined banner types, the AccentColor and IconSource properties can be set to customize the styling. 

A color can be set via the AccentColor property with a hex code, i.e. #800000 for maroon. 
TBD: should other color definition methods be supported like RGB? Are there already Fluent aliases for many hex codes?


Alongside color, a custom icon can appear left of the Title and Message in the status banner. If a default styling is chosen, most styles have an associated icon already defined. This icon can be removed or added as a custom icon using the IconSource property. Recommended icon sizes include (TBD)px.

XAML
```XAML
<StackPanel x:Name="ContentArea" Content="Document" />

<controls:StatusBanner x:Name="ConnectionErrorBanner"
    AccentColor="#800000"
    Title="No Internet"
    Message="Reconnect to save your work.">
    <controls:StatusBanner.IconSource>
        <controls:SymbolIconSource Symbol="NetworkOffline" />
    </controls:StatusBanner.IconSource>
</controls:StatusBanner>
```


[A sketch of a sample application with a status banner as a bar in the top of the content area. The status banner's title is "No Internet" and it's message is "Reconnect to save your work". It has a maroon accent color and a "Network Offline" icon.](images/No_Internet_Bar.png)

## Add buttons
By default, an 'X' close button will appear as the right most component in the bar. To customize this button a The close button can be customized with the CloseButtonContent property, in which case the Action button & CloseButtonContent customization

An additional action button can be added by setting the ActionButtonContent and ActionButtonCommand properties. Additional action buttons can be added via custom content.
<!-- TODO: add link to custom content header in 'content' -->

XAML
```XAML
<StackPanel x:Name="ContentArea" Content="Document" />

<controls:StatusBanner x:Name="ConnectionErrorBanner"
    Type="Critical"
    Title="No Internet"
    Message="Reconnect to save your work."
    ActionButtonContent="Open Network Settings"
    ActionButtonCommand="RedirectToNetworkSettings"
    CloseButtonContent="Dismiss">
    <controls:StatusBanner.IconSource>
        <controls:SymbolIconSource Symbol="NetworkOffline" />
    </controls:StatusBanner.IconSource>
</controls:StatusBanner>
```

<!-- TODO: Insert img of Internet connectivity example w/ buttons--> [img]

## Custom content
"Content can be added to a teaching tip using the Content property. If there is more content to show than what the size of a teaching tip will allow, a scrollbar will be automatically enabled to allow a user to scroll the content area."

XAML
```XAML
<StackPanel x:Name="ContentArea" Content="Document" />

<controls:StatusBanner x:Name="RecentUpdateBanner"
    Type="Informational"
    Title="Update Complete!"
    Message="You've been updated to the latest version --">    
        // Is there a way to ensure this Hyperlink control can be inline with the Message when set in the Content?
        <HyperlinkButton
            Content="Release Notes"
            NavigateUri="https://www.microsoft.com/app/releasenotes" />
</controls:StatusBanner>
```

<!-- TODO: Insert img of informational 'Update available' with hyperlink to view release notes--> [img]

## Message wrapping behavior
TBD: define message wrapping behavior
- A "too long" message depends on width of container and message area.
- Truncation with expansion symbol? Or longer bar to accommodate? Or scroll bar?

## Updating a status banner
TBD: define updating behavior
- What properties can be updated?
- Is there an animation to occur?
- How is dismissal handled?

## Multiple status banners
TBD: define stacking behavior
- Visual appearance for each mode
- Recommendation for max number to appear of each mode at a Timed
- Do they stack top to bottom or vice versa?
  - For toast mode, does placement 
- What is the behavior 

## (Toast exclusive property) Preferred Placement
TBD after the first (bar) mode is mostly defined.
- Same as non-targeted teaching tip placement modes?
    - 9 options -- four corners, four edges, center
        - Officially 13 options to allow aliases for corners (BottomRight == RightBottom)
- If PreferredPlacement property is defined for banners in bar mode what happens?

## What mode of status banner should I use?
TBD after the first (bar) mode is mostly defined. 


# Remarks
## Recommendations
TBD
## Anti-patterns
TBD
- Is there a limit to how often a status banner can appear/disappear from view?
  - Prevent "flashing" banner bars
- Only *one* status banner should appear for every scenario 
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

### Notable Properties  

| Name | Description |
|:-:|:--|
| Type | Gets or sets a value that indicates the accent color and icon to style the status banner |

### Events    
TBD: Same as Teaching Tip at the moment
| Name | Description |
|:-:|:--|
| ActionButtonClick | Occurs after the action button has been tapped. |
| CloseButtonClick | Occurs after the close button has been tapped. |
| Closed | Occurs after the status banner is closed. |
| Closing |Occurs just before the status banner begins to close. |

# API Details
<!-- The exact API, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/) -->

# Appendix
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->

### Visual Components

 | Component |  Notes |
|:---:|:---|
| Container | - Specific details TBD
| Title | - Semi-bolded <br> - Recommended to be 50 characters or less
| Message | - Text wrapping behavior TBD <br> - Recommended to be 512 characters or less 
| AccentColor | - Defined by either the Type of the status banner or by hex code
| Icon | - Defined by either the Type of the status banner or by IconSource <br>
| Close button | - Will appear as 'X' by default <br> - Can be customized as a button <br> - Can be removed via IsProgrammaticDismissal
| Action button | - Optional <br> - Additional action buttons may be added through custom XAML content in the Message
| Content | - Can be customizable to include text, hyperlinks, and any other XAML content <br> - Appears between the Title/Message and any Action or Close buttons

TBD: Same as TeachingTip at the moment
## Behavioral Components

 | Property | Notes |
|:---:|:---|
| Opening | * A tip is shown by setting its IsOpen property to true. <br> * Tips will animate on opening. <br> * When a tip does not have enough available window space to fully show in any location [see Placement], it will not open and will instead overwrite IsOpen to false. |
| Closing | There are three ways a tip can close: The program sets the IsOpen property to false, the user invokes the Close button, or the tip closes due to light dismiss. Use the TeachingTipCloseReason to determine which case has occurred. Closing can be prevented by setting the Cancel property to true. You can use a deferral to respond asynchronously to the event. |
| Placement | * Placement modes for targeted teaching tips will follow the precedent of Flyout. Full placement mode will be replaced by Center which positions Tail at the center of the element. <br> * Placement modes for non-targeted tips will include each side, corner, and center of the application window. <br> * The following properties are not preferred in tip placement: <br> &nbsp;&nbsp;&nbsp;&nbsp; * There is not enough space for the tip to show without clipping. <br> &nbsp;&nbsp;&nbsp;&nbsp; * The target is not large enough to maintain the tip's alignment and the Tail's 12px margin from the edge of the tip. <br> &nbsp;&nbsp;&nbsp;&nbsp; * The target element is too large to maintain edge alignment while keeping the Tail centered on the target. |
| Motion | * Tips have built in open and close animations that can be customizable using Storyboards.|
| Out of Window Bounds | * Tips can escape window bounds on newer OS versions via the ShouldConstrainToRootBounds property.  When this property is set to false, the tip uses screen boundaries instead of window boundaries during its placement algorithm. |