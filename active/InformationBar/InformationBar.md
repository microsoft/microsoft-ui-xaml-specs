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

Users should be informed about essential status changes that occur on an app level.
These status changes affect the app as a whole and can be either critical or informational.
Critical status changes like lost internet connectivity are directly impactful to app functionality while informational status changes like an update has completed and been applied are indirectly impactful to app functionality.
These notifications and corresponding information should be presented in a consistent, predictable, and relevant way to the user depending on the specific scenario.

Currently, Teaching Tip, Content Dialog, and customizations of other flyouts and dialogs exist as options to show these notifications. 
Due to their design intentions, intrusiveness, or features they are not sufficient for displaying notifications at an app-wide level.


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

A StatusBanner is a persistent, actionable, app-wide notification intended for displaying critical or informational status messages that impact app perception or user experience.




## Is this the right control?
Use an StatusBanner control when a user needs to be informed of, acknowledge, or take action on a message. By default the notification will remain in the content area until dismissed by the user but will not necessarily break user flow.

Do not use an StatusBanner control to confirm or respond directly to a user action, for transient alerts, or for non-essential messages.

### Scenarios
Use a StatusBanner that dismiss via the user or when the status is resolved for scenarios that **directly** impact app perception or experience ⚠

- Internet connectivity lost
- Error while saving a document when triggered automatically, not related to specific user action
- No microphone plugged in when attempting to record
- Can't connect to your phone
- The subscription to the application is expired

Use a StatusBanner that dismiss via the user or a timer for scenarios that **indirectly** impact app perception or experience ℹ

- A call has begun recording
- Update applied with link to 'Release Notes'
- The terms of service have been updated
- An app-wide backup has successfully, asynchronously completed
- The subscription to the application is close to expiring


<!-- 
Control	| Intrusiveness	|	Information Severity	|	Modality	|	Scope	|	Invocation	|	Dismissal	|	User Action
| -	| -	| -	| -	| -	| -	| -	| -	|
InfoBar ⚠	|	Medium	|	High	|	Non-blocking	|	Global	|	Programmatic	|	Programmatic	|	Acknowledge/View
InfoBar ℹ	|	Medium	|	Medium	|	Non-blocking	|	Global	|	Programmatic	|	Manual	|	Acknowledge/Dismiss -->

### When should a different control be used?
There are some scenarios where a Content Dialog, Flyout, or Teaching Tip may be more appropriate to use.

- For scenarios where a persistent notification is not needed, i.e. displaying information in context of a specific UI element, a [Flyout](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/dialogs-and-flyouts/flyouts) is a better option. 
- For scenarios where the application is confirming a user action or is showing information the user ***must*** read, use a [Content Dialog](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/dialogs-and-flyouts/dialogs).
- For scenarios where the application is informing the user of a new feature or walking through its use, a [Teaching Tip](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/dialogs-and-flyouts/teaching-tip) is a better option.

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
> Note: In this version of the specification, only a single LayoutMode is implied. In the future, more documentation will be added on a second LayoutMode. The LayoutMode property will change the visual layout of the components, not the functionality. The current LayoutMode shown is "Bar", where the components are vertically aligned in a single plane. The second LayoutMode, "Card" or "Toast", will look more similar to a system notification in it's component layout, details TBD.

A status banner can have several configurations, here are some notable ones.

When a status banner is conveying information of a common criticality, a banner can be set to have one of many BannerTypes to use consistent Fluent styling for it's identifiers.
![TODO](images/Warning_DefaultClose.jpg)

If a call to action is needed, a status banner can have customizable action and close buttons.
![TODO](images/Critical_CustomButtons.jpg)

The status banner can also be customized with XAML Content to include hyperlinks, extra buttons, and other UI elements.
![TODO](images/Information_CustomContent.jpg)


## Create a status banner 
The XAML below describes a bar-style status banner with the default styling for a critical notification. A status banner can be created anywhere in the element tree or code behind (Toast TBD). In this example, the banner is located in a ResourceDictionary, expanding to fill the width of the stack panel, like a bar.

XAML
```xml
<StackPanel x:Name="ContentArea" Content="Document">
    <StackPanel.Resources>
        <controls:StatusBanner x:Name="UnsuccessfulSaveBanner"
            BannerType="Warning"
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

Here is the visual representation of the status banner in the page.

![TODO](images/Warning_DefaultClose.jpg)

<!--## Pop-up status banner 
In some app scenarios, an app-wide bar does not fit for functional or aesthetic reasons. If a status banner is defined independent of another control, the visual layout will default to a to a toast with the same visual components as the aforementioned bar. The status banner will display relative to the edges of the xaml root and by default will be located in the bottom right corner of the application.

XAML
```c#
<StackPanel x:Name="ContentArea" Content="Document" />

<controls:StatusBanner x:Name="ConnectionErrorBanner"
    BannerType="Critical"
    Icon="NetworkOffline"
    Title="No Internet"
    Message="Reconnect to save your work.">
</controls:StatusBanner>
```
[A sketch of a sample application with a status banner as a toast in the bottom right of the content area. The status banner's title is "No Internet" and it's message is "Reconnect to save your work"](images/No_Internet_Toast.png) -->

## Banner types: consistent styling
The type of the status banner can be set via the BannerType property to automatically set a consistent status color and icon dependent on the criticality of the notification.

Preset color and icon combos, TBD in collaboration w/ design:
- Critical: Fluent red (#D13438) & ErrorBadge (EEA39)
- Warning: Fluent orange (#FF8C00) & Error (E783)
- Informational: Theme accent or Fluent blue (#0078D7) & Info (E946)
- Success: Fluent green (#107C10) & StatusCircleCheckmark (F13E)
- Default if BannerType isn't set: Fluent gray (#6979E) & no icon
- TBD: Should there be a 'None' for no icon or color? How else could that scenario be possible?

TBD: Should these be part of light-weight styling to be set across app? Like data validation? Or separate?

Status banner in default styling
![TODO](images/Default_Default.jpg)

Status banner in success styling
![TODO](images/Success_DefaultClose.jpg)


## Programmatic dismiss in status banner
A status banner can be dismissed by the user via the close button or programmatically. If the notification is required to be in view until the status is resolved you can set the IsProgrammaticDismissal property to true to remove the default dismiss button. 

 > Note: Include a message highlighting the risks with removing a close button. How it can be very intrusive and a back-up removal should be included.

XAML
```xml
<StackPanel x:Name="ContentArea" Content="Document">
    <StackPanel.Resources>
        <controls:StatusBanner x:Name="UnsuccessfulSaveBanner"
            BannerType="Warning"
            Title="Error while saving"
            Message="Your document was unable to be saved."
            IsProgrammaticDismiss="True">
        </controls:StatusBanner>
    </StackPanel.Resources>
</StackPanel>
```
![TODO](images/Warning_ProgrammaticClose.jpg)

## Custom styling: status color and icon
Outside of the pre-defined banner types, the StatusColor and IconSource properties can be set to customize the styling. 

A color can be set via the StatusColor property with a hex code, i.e. #800000 for maroon. 
TBD: should other color definition methods be supported like RGB? Are there already Fluent aliases for many hex codes?


Alongside color, a custom icon can appear left of the Title and Message in the status banner. If a default styling is chosen, most styles have an associated icon already defined. This icon can be removed or added as a custom icon using the IconSource property. Recommended icon sizes include (TBD)px.

XAML
```xml
<StackPanel x:Name="ContentArea" Content="Document">
    <StackPanel.Resources>
        <controls:StatusBanner x:Name="ConnectionErrorBanner"
            Title="No Internet"
            Message="Reconnect to save your work.">
            <controls:StatusBanner.IconSource>
                <controls:SymbolIconSource Symbol="NetworkOffline" />
            </controls:StatusBanner.IconSource>
            <controls:StatusBanner.StatusColor>
                <Color x:Key="Maroon">#800000</Color>
            </controls:StatusBanner.StatusColor>
        </controls:StatusBanner>
    </StackPanel.Resources>
</StackPanel>
```


![A sketch of a sample application with a status banner as a bar in the top of the content area. The status banner's title is "No Internet" and it's message is "Reconnect to save your work". It has a maroon accent color and a "Network Offline" icon.](images/Critical_Color.jpg)

## Add buttons
By default, an 'X' close button will appear as the right most component in the bar. The close button can be customized with the CloseButtonContent property.

An additional action button can be added by setting the ActionButtonContent and ActionButtonCommand properties. Additional action buttons can be added via custom content.
<!-- TODO: add link to custom content header in 'content' -->

XAML
```xml
<StackPanel x:Name="ContentArea" Content="Document">
    <StackPanel.Resources>
        <controls:StatusBanner x:Name="ConnectionErrorBanner"
            BannerType="Critical"
            Title="No Internet"
            Message="Reconnect to save your work."
            ActionButtonContent="Reconnect"
            ActionButtonCommand="RedirectToNetworkSettings"
            CloseButtonContent="Dismiss">
            <controls:StatusBanner.IconSource>
                <controls:SymbolIconSource Symbol="NetworkOffline" />
            </controls:StatusBanner.IconSource>
        </controls:StatusBanner>
    </StackPanel.Resources>
</StackPanel>
```

![TODO](images/Critical_CustomButtons.jpg)

## Custom content
TBD: Same as Teaching Tip at the moment

Content can be added to a status banner using the Content property. If there is more content to show than what the size of a status banner will allow, a scrollbar will be automatically enabled to allow a user to scroll the content area.
XAML
```xml
<StackPanel x:Name="ContentArea" Content="Document">
    <StackPanel.Resources>
        <controls:StatusBanner x:Name="RecentUpdateBanner"
            BannerType="Informational"
            Title="Update Complete!"  
                <TextBlock Text="You've been updated to the latest version &#8211;>
                    <Hyperlink
                        Content="Notes"
                        NavigateUri="https://www.microsoft.com/app/releasenotes" />
                </TextBlock>
        </controls:StatusBanner>
    </StackPanel.Resources>
</StackPanel>
```

![TODO](images/Information_CustomContent.jpg)

## Message wrapping behavior
TBD: define message wrapping behavior
- A "too long" message depends on width of container and message area.
- Truncation with expansion symbol? Or taller bar to accommodate?

## Updating a status banner
TBD: define updating behavior
- What properties can be updated?
- Is there an animation to occur?
- How is dismissal handled?

## Multiple status banners
TBD: define stacking behavior
- Do they stack top to bottom or vice versa?
- Is it a collection that can get added to and expanded with each new banner? Or is the space for banners predefined and the banners will compress/use a scroll bar to fit?
- Visual appearance for each mode
- Recommendation for max number to appear of each mode at a time

## Canceling and deferring close
TBD: define event behavior, similar to TeachingTip?

## (Toast exclusive property) Preferred Placement
TBD after the first (bar) mode is mostly defined.
- Same as non-targeted teaching tip placement modes?
    - 9 options -- four corners, four edges, center
        - Officially 13 options to allow aliases for corners (BottomRight == RightBottom)
- If PreferredPlacement property is defined for banners in bar mode what happens?

## What mode of status banner should I use?
TBD after the first (bar) mode is mostly defined.

# Inputs and Accessibility
## UI Automation patterns
TBD?

## Keyboard Navigation
TBD

## Narrator
TBD

## Gamepad
TBD

# Remarks
## Recommendations
TBD
- Dark mode guidances
- Conveying meaning via color AND icon for accessibility
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
| BannerType | Gets or sets a value that indicates the  color and icon to style the status banner |
| ShowCloseButton| Gets or sets a boolean that indicates whether a close button will appear


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
```c++ 
// TODO: investigate and develop
enum StatusBannerCloseReason
{
    CloseButton,
    Programmatic,
};

enum StatusBannerType
{
    Critical,
    Warning,
    Informational,
    Success,
}

// TODO: For toast mode only
/*enum StatusBannerPlacementMode
{
    Auto,
    Top,
    TopRight,
    TopLeft,
    Right, 
    RightTop,
    RightBottom,
    Bottom,
    BottomRight,
    BottomLeft,
    Left, 
    LeftTop,
    TopBottom,
    Center,
};*/


runtimeclass StatusBannerClosedEventArgs
{
    StatusBannerCloseReason Reason{ get; };
};

runtimeclass StatusBannerClosingEventArgs
{
    StatusBannerCloseReason Reason{ get; };
    Boolean Cancel;
    Windows.Foundation.Deferral GetDeferral();
};

// TODO: is there anything else needed in TemplateSettings?
unsealed runtimeclass StatusBannerTemplateSettings : Windows.UI.Xaml.DependencyObject
{
    StatusBannerTemplateSettings();

    // TODO: what do these highlight margins define?
    Windows.UI.Xaml.Thickness TopRightHighlightMargin;
    Windows.UI.Xaml.Thickness TopLeftHighlightMargin;

    Windows.UI.Xaml.Controls.IconElement IconElement;

    static Windows.UI.Xaml.DependencyProperty TopRightHighlightMarginProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty TopLeftHighlightMarginProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty IconElementProperty{ get; };
}

unsealed runtimeclass StatusBanner : Windows.UI.Xaml.Controls.ContentControl
{
    StatusBanner();

    String Title;
    String Message;

    Boolean IsOpen;
    Boolean ShowCloseButton;

    Object ActionButtonContent;
    Windows.UI.Xaml.Style ActionButtonStyle;
    Windows.UI.Xaml.Input.ICommand ActionButtonCommand;
    Object ActionButtonCommandParameter;

    Object CloseButtonContent;
    Windows.UI.Xaml.Style CloseButtonStyle;
    Windows.UI.Xaml.Input.ICommand CloseButtonCommand;
    Object CloseButtonCommandParameter;

    // TODO: investigate & develop
    Boolean ShouldConstrainToRootBounds;

    /*  Needed for toast mode only
    Windows.UI.Xaml.Thickness PlacementMargin;
    StatusBannerPlacementMode PreferredPlacement; */

    StatusBannerType BannerType;
    Color StatusColor;
    IconSource IconSource;

    StatusBannerTemplateSettings TemplateSettings{ get; };

    event Windows.Foundation.TypedEventHandler<StatusBanner, Object> ActionButtonClick;
    event Windows.Foundation.TypedEventHandler<StatusBanner, Object> CloseButtonClick;
    event Windows.Foundation.TypedEventHandler<StatusBanner, StatusBannerClosingEventArgs> Closing;
    event Windows.Foundation.TypedEventHandler<StatusBanner, StatusBannerClosedEventArgs> Closed;

    static Windows.UI.Xaml.DependencyProperty IsOpenProperty{ get; };

    static Windows.UI.Xaml.DependencyProperty TitleProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty MessageProperty{ get; };

    static Windows.UI.Xaml.DependencyProperty ActionButtonContentProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty ActionButtonStyleProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty ActionButtonCommandProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty ActionButtonCommandParameterProperty{ get; };

    static Windows.UI.Xaml.DependencyProperty CloseButtonContentProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty CloseButtonStyleProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty CloseButtonCommandProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty CloseButtonCommandParameterProperty{ get; };

    // TODO: investigate & develop
    static Windows.UI.Xaml.DependencyProperty ShouldConstrainToRootBounds{ get; };
    /* Needed for toast mode only
    static Windows.UI.Xaml.DependencyProperty PlacementMarginProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty PreferredPlacementProperty{ get; };*/

    static Windows.UI.Xaml.DependencyProperty BannerTypeProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty StatusColorProperty{ get; };
    static Windows.UI.Xaml.DependencyProperty IconSourceProperty{ get; };

    static Windows.UI.Xaml.DependencyProperty TemplateSettingsProperty{ get; };
}
```
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
| StatusColor | - Defined by either the BannerType or by hex code
| Icon | - Defined by either the BannerType or by IconSource <br>
| Close button | - Will appear as 'X' by default <br> - Can be customized as a button <br> - Can be removed via IsProgrammaticDismissal
| Action button | - Optional <br> - Additional action buttons may be added through custom XAML content in the Message
| Content | - Can be customizable to include text, hyperlinks, and any other XAML content <br> - Appears between the Title/Message and any Action or Close buttons

## Behavioral Components
TBD: Same as TeachingTip at the moment
 | Property | Notes |
|:---:|:---|
| Opening | * A status banner is shown by setting its IsOpen property to true. <br> * Status banners will animate on opening. <br> * When a status banner does not have enough available window space to fully show in any location [see Placement], it will not open and will instead overwrite IsOpen to false. |
| Closing | There are two ways a status banner can close: The program sets the IsOpen property to false, the user invokes the Close button. Use the StatusBannerCloseReason to determine which case has occurred. Closing can be prevented by setting the Cancel property to true. You can use a deferral to respond asynchronously to the event. |
| Motion | * Status banners have built in open and close animations that can be customizable using Storyboards.|

## Data and Intelligence Metrics
TBD