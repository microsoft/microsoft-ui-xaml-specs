
# Animated Icon

## Background
Today, developers do not have a straightforward way to add an animate icon in controls where they can use a static icon. Some examples of where a developer may want to add an animated icon are in a NavigationViewItem or in a Button. There is also a desire to have an animated icon respond to user interactivity via state changes to either draw attention to an action a user can take or to add user delight. 

In the current developer workflow, developers may need to write a lot of custom code to get their generated animation to properly respond to user interaction using the AnimatedVisualPlayer control. An AnimatedIcon control would reduce, or ideally eliminate the amount of code-behind needed to implement most scenarios.

## Description
An AnimatedIcon is a UI component which plays Lottie animations in response to user interaction and visual state changes. An AnimatedIcon can be used anywhere a static icon control that inherits from IconElement is used today. 

On Windows, the VisualStateManager is used to manage the logic for transitioning between states for controls. Each control has a specified VisualStateGroup, which contains mutually exclusive VisualState objects. For example, a Button may have a VisualStateGroup called “CommonStates”, which specifies a VisualState object for “Normal” “Pressed”, “PointerOver”, and “Disabled”.

A control will use the VisualStateManager to determine what state the animated icon is transitioning to and play the corresponding animation segment of the icon. The icon file will need to have markers named in a standard way (described below) so AnimatedIcon can map the correct animation segment with the visual state the control is transitioning to. 

**Important APIs:** [AnimatedIcon class](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.AnimatedIcon)

## Is this the right control? 
Use an AnimatedIcon control when you want to tie a Lottie animated icon to a user interaction in your UI. Do not use an AnimatedIcon control if you want to just play a Lottie animation once or if you can to control the playback of the animation. Instead, use the AnimatedVisualPlayer control. 

An AnimatedIcon control can be used anywhere an IconElement can be used in WinUI. 

# Remarks
## How is this different than Animated Visual Player?
AnimatedIcon inherits from IconElement to make it easier for developers to swap out a static icon for an animated icon that responds to visual state changes. Inheriting from IconElement also limits the scope of where this control is used. AnimatedVisualPlayer (AVP) is meant to be a general Lottie player that can be used anywhere in an application where AnimatedIcon is intended to only be used where icons are used in WinUI. 

AVP allows developers to control animations using the following methods: Pause, PlayAsync, Resume, SetProgress, and Stop. When using AVP, developers may need to write a lot of code when getting their generated animation to properly respond to user interaction and state changes. This is the main distinction between the workflows for AVP and AnimatedIcon. An AnimatedIcon will be used when animations need to be driven by state changes and user interaction in a markup friendly way. It would greatly reduce, or ideally eliminate the amount of code-behind needed to implement such scenarios.

# Examples
## Pre-requisites for adding an animated icon to an application: 
1.	Download the Lottie file for the icon you are looking to add to your application.
2.	Run the Lottie file through LottieGen.
3.	Take the output file from LottieGen and place it in your project.

### Scenario: App developer wants to swap out a static icon with an animated icon
An icon animation will be activated by changes of state in XAML input controls (e.g. Button, and ToggleSwitch). For example, NavigationViewItem has states for rest/pointer over/press and AnimatedIcon will animate the state transitions by having named markers in the Lottie file such as:  

- NormalToPointerOver
- NormalToPressed
- PointerOverToNormal
- PointerOverToPressed
- PressedToNormal
- PressedToPointerOver

“Normal” is the state where the user is not interacting with the button. 

Below is an example of how to add a Hamburger animated icon to a Hierarchical NavigationView item. 
You will add the icon like you would any other icon type that inherits from IconElement. 

Before: 

XAML
```xml
< muxc:NavigationView.MenuItems >
    < muxc:NavigationViewItem Content = "Menu Item1" Icon =”GlobalNavigationButton” Tag="SamplePage" x:Name = "SamplePageItem" >
</ muxc:NavigationView.MenuItems >
```
After: 

XAML
```xml
< muxc:NavigationView.MenuItems >
    < muxc:NavigationViewItem Content = "Menu Item1" Tag="SamplePage" x:Name = "SamplePageItem" >
        < muxc:NavigationViewItem.Icon >
            < controls:AnimatedIcon x:Name = "HamburgerIcon" >
                < controls:AnimatedIcon.Source >
                    < local:IAnimatedIconSource />
                </ controls:AnimatedIcon.Source>
            </ controls:AnimatedIcon >
    </ muxc:NavigationViewItem.Icon >
</ muxc:NavigationView.MenuItems >
```

The other work will be to set up the visual state manager to play the correct segment of the animation. For this icon, we will add setters for the Normal, Pointer Over, and Pressed states of the navigation view item. 

XAML
```xml
< VisualStateManager.VisualStateGroups >
    < VisualStateGroup x: Name =”CommonStates”>
        < VisualState x: Name =”Normal”>
            < VisualState.Setters >
                < Setter Target = "Icon.(AnimatedIcon.StateProperty)" Value = "Normal" />
            </ VisualState.Setters >   
        </ VisualState >       
        < VisualState x: Name =”PointerOver”>           
            < VisualState.Setters >            
                < Setter Target = "Icon.(AnimatedIcon.StateProperty)" Value = "PointerOver" />               
            </ VisualState.Setters >               
        </ VisualState >              
        < VisualState x: Name =”Pressed”>                  
            < VisualState.Setters >                 
                < Setter Target = "Icon.(AnimatedIcon.StateProperty)" Value = "Pressed" />                     
            </ VisualState.Setters >                    
        </ VisualState >                    
        </ VisualStateGroup >                    
    </ VisualStateManager.VisualStateGroups >
    < ContentPresenter
    x: Name = "ContentPresenter"
    Background = "{TemplateBinding Background}"
    BorderBrush = "{TemplateBinding BorderBrush}"
    BorderThickness = "{TemplateBinding BorderThickness}"
    Content = "{TemplateBinding Content}"
    ContentTemplate = "{TemplateBinding ContentTemplate}"
    ContentTransitions = "{TemplateBinding ContentTransitions}"
    Padding = "{TemplateBinding Padding}"
    HorizontalContentAlignment = "{TemplateBinding HorizontalContentAlignment}"
    VerticalContentAlignment = "{TemplateBinding VerticalContentAlignment}"
    AutomationProperties.AccessibilityView = "Raw" />
< Grid x: Name = "Icon" HorizontalAlignment = "Right" Content = "{TemplateBinding Icon}/>
```

# API Notes
### Notable Properties  
| Name | Type |	Description | Default |
|:--- | :---| :---| :---|
| TryCreateAnimatedVisualSource | Method | Method to get an IAnimatedVisualSource from an IRichAnimatedVisualSource | null |
| Markers |	IMapView | Returns a dictionary of all marker strings and their associated values found in the icon’s JSON file. The strings and values are based on what the designer names the markers in the Lottie After Effects file.|	null |
| SetForegroundColorProperty |	Method	| Takes a string to set the color of a named theme property in the Lottie file.| null |
| SetAccentColorProperty |	Method | Takes a string to set the color of a named theme property in the Lottie file. | null |
| StateProperty	| Dependency Property |Attached property that gets the current visual state the icon is animating from. | "Normal" |
| Source | IAnimatedIconSource |Gets or sets the icon visual produced from LottieGen. |	null |
| FallbackIcon | FontIconSource | Gets or sets the font icon that will be used as a fallback when the OS is older than RS5. | null |

# API Details
```c++
interface IAnimatedIconVisualSource
{
    IAnimatedVisual TryCreateAnimatedVisual(Windows.UI.Composition.Compositor compositor);

    Windows.Foundation.Collections.IMapView<String, Double> Markers { get; };
    void SetForegroundColorProperty(String propertyName, Windows.UI.Color value);
    void SetAccentColorProperty(String propertyName, Windows.UI.Color value);
};

unsealed runtimeclass AnimatedIcon : Windows.UI.Xaml.Controls.IconElement
 {
    AnimatedIcon();

    IAnimatedIconVisualSource Source{ get; set; };
    FontIconSource FallbackIcon{get; set;};

    [MUX_DEFAULT_VALUE("Normal")]       
    static Windows.UI.Xaml.DependencyProperty StateProperty{ get; };

    static Windows.UI.Xaml.DependencyProperty SourceProperty{ get; };
}
```

# Appendix
## Fallback for older operating systems 
AnimatedIcon will provide an API for a developer to add an icon from a font family so if the operating system is older than RS5, AnimatedIcon will display the font icon provided by the developer and the icon will remain static when the user interacts with it. 

## Design Requirements 
Designers will need to add markers named like the following to all Lottie files that will be used with AnimatedIcon. The bare minimum of the markers that are needed for icons being used in a button are: 
- NormalToPointerOver
- NormalToPressed
- PointerOverToNormal
- PointerOverToPressed
- PressedToNormal
- PressedToPointerOver

### Proposed solution for formatting marker strings 
Markers will need to be named a specific way so AnimatedIcon can map the correct animation segment with the correct visual state transition. This solution is conforming to a standard set of strings that are already defined in the visual state manager for each state. Here is an example of how the markers would look in a Lottie JSON file. 

"markers":[{"tm":0,"cm":"NormalToPointerOver_Start","dr":0},{"tm":9,"cm":"NormalToPointerOver_End","dr":0},
{"tm":10,"cm":"NormalToPressed_Start","dr":0},{"tm":19,"cm":"NormalToPressed_End","dr":0},
{"tm":20,"cm":"PointerOverToNormal_Start","dr":0},{"tm":29,"cm":"PointerOverToNormal_End","dr":0},
{"tm":30,"cm":"PointerOverToPressed_Start","dr":0},{"tm":39,"cm":"PointerOverToPressed_End","dr":0},
{"tm":40,"cm":"PressedToNormal_Start","dr":0},{"tm":49,"cm":"PressedToNormal_End","dr":0},
{"tm":50,"cm":"PressedToPointerOver_Start","dr":0},{"tm":69,"cm":"PressedToPointerOver_End","dr":0},
{"tm":90,"cm":"PressedToNormal_Start","dr":0},{"tm":99,"cm":"PressedToNormal_End","dr":0},
{"tm":100,"cm":"PressedToPointerOver_Start","dr":0},{"tm":101,"cm":"PressedToPointerOver_End","dr":0}]



## Inputs and Accessibility
This control is going to be part of the Icon family of controls so the accessibility requirements will reflect what the other icon controls have. AnimatedIcon will need to ensure that the high contrast brushes propagate correctly if the user changes their settings to a high contrast theme. The control will also need to ensure that when the user changes the theme to light or dark, the icon changes its theme as well. 
