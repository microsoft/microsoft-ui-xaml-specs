# Overview
DynamicAutomationPeer extends the functionality of AutomationProperties and allow customer to override following UI Automation features in an declarative way:
1. UI Automation ControlType. For example, change Button to CheckBox control type.
2. UI Automation Control Pattern. For example, making Button to support ToggleProvider, then provides ToggleState, also invoke ToggleAction to allow customer to have new control pattern without a new AutomationPeer.

# Background
In React Native, accessibility is implemented in an elegant way, and it doesn't need customer to implement any automation peer.

[accessibilityLabel](https://facebook.github.io/react-native/docs/accessibility#accessibilitylabel-ios-android) is similar to AutomationProperties.Name.


[accessibilityRole](https://facebook.github.io/react-native/docs/accessibility#accessibilityrole-ios-android) communicates the purpose of a component to the user of an assistive technology.
It's similar to [AutomationControlType](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Automation.Peers.AutomationControlType) in Xaml.

[accessibilityStates](https://facebook.github.io/react-native/docs/accessibility#accessibilitystates-ios-android) 
Describes the current state of a component to the user of an assistive technology. It includes arrays of values like checked, expanded. Which is similar to ToggleState, ExpandCollapseState in Xaml.


[Accessibility Actions](https://facebook.github.io/react-native/docs/accessibility#accessibility-actions) allow an assistive technology to programmatically invoke the actions of a component. In order to support accessibility actions, a component must do two things:
- Define the list of actions it supports via the accessibilityActions property.
- Implement an onAccessibilityAction function to handle action requests.


Below is an example of how the application changed the control type to checkbox, also implemented ToggleProvider and ExpandCollapseProvider in React Native.

```
<View
    accessible={true}
    accessibilityLabel='AccessibilityAction'
    accessibilityRole='CheckBox'
    accessibilityStates={[myToggleState, 'expanded']}
    accessibilityActions={[
        { name: 'toggle', label: 'toggle' },
        { name: 'expand', label: 'expand' },
        { name: 'collapse', label: 'collapseIt' },
    ]}
    onAccessibilityAction={(event: { nativeEvent: { actionName: any; }; }) => {
        switch (event.nativeEvent.actionName) {
            case 'toggle':
                setAccessibilityAction('toggle action success');
                break;
            case 'expand':
                setAccessibilityAction('expand action success');
                break;
            case 'collapseIt':
                setAccessibilityAction('collapseIt action success');
                break;
        }
    }}
>
    <Text>accessibilityAction:{accessibilityAction}</Text>
</View>
```

In Xaml, It's hard for us to support it without a new AutomationPeer. We need to create an automation peer for each new control even if the real difference is just one line of code. Although it's not always the case, but we have painful experience to implement AutomationPeer for RadioButtons.

In RadioButtons, in order to change the ListViewItem automation control type to RadioButton, to support it:
1. Subclass ListViewItemAutomationPeer just so I could return AutomationControlType::RadioButton from GetAutomationControlTypeCore()
2. Subclass ListViewItem just so I could return my peer from OnCreateAutomationPeer
3. Subclass ListView so that I could make it spin out my sub classed ListViewItems
6 files and 3 new public types all for one line of code!!

# Description
This is a follow up on proposal [Provides Dynamic AutomationPeer](https://github.com/microsoft/microsoft-ui-xaml/issues/1349). The goal is to extend the functionality of AutomationProperties of Xaml and allow user to override AutomationPeer in a declarative way. This is just addon features and existing customer will not be impacted.

Below is just for illustration and is not the actual implementation. Assume AutomationProperties implemented IAutomationPropertiesOverride and allow customer to override ControlType, ToggleProvider and ExpandCollapseProvider

```
interface IAutomationPropertiesOverride
{
    AutomationControlType ControlTypeOverride {get; set;}
    ToggleState ToggleStateOverride {get; set;}
    ExpandCollapseState ExpandCollapseStateOverride {get; set;}    
    event TypedEventHandler<UIElement, IToggleActionEventArgs> ToggleAction;
    event TypedEventHandler<UIElement, IExpandCollapseActionEventArgs> ExpandCollapseStateAction;
}
```

Then we can implement the React Native version of accessibility in Xaml like below
```
<View
    AutomationProperties.Name="{x:Bind ViewName}" 
    AutomationProperties.ControlTypeOverride=AutomationControlType.CheckBox
    AutomationProperties.ToggleStateOverride="{x:Bind ToggleState}" 
    AutomationProperties.ToggleAction="ToggleAction_Invoked"
    AutomationProperties.ExpandCollapseStateOverride=ExpandCollapse.Expanded
    AutomationProperties.ExpandCollapseStateAction="ExpandCollapseStateAction_Invoked"
>
    <TextBox Text={Binding ActionOutput} />
</View>

class MyView 
{
    String ViewName {get; set;}
    ToggleState MyToggleState {get; set;}

    private void ToggleAction_Invoked(object sender, ToggleActionEventArgs e)
    {

    }

    private void ExpandCollapseStateAction_Invoked(object sender, ExpandCollapseActionEventArgs e)
    {
        if (e.IsCollapseAction()) {
            // Collapse
        } else {
            // Expand
        }
    }

}
```

Also it's allowed to do it by code out side of Xaml
```
class View: Control {
    public View(){
        AutomationProperties.SetControlTypeOverride(this, AutomationControlType.CheckBox);
        AutomationProperties.SetToggleAction(this, toggleActionHandler);
        ...
    }
}
```

## Scope
| Capability | Priority |
| :---------- | :------- |
| Allow user to override ControlType by AutomationProperties.ControlTypeOverride|Must|
| AutomationProperties.ControlTypeOverride takes high priority than GetAutomationControlTypeCore of automationPeer of the control  |Must|
| Allow user to override the state of an Automation Provider  |Must|
| If user override the state, raise property changed event automatically if state changed |Must|
| Allow user to override the action of an Automation Provider  |Must|
| If user override the action of an Automation Provider,  The new pattern for this provider should be returned to automation client even if user didn't specify it  GetPatternCore for the control |Must|
| For Providers of a control supported, but user didn't override, it should be still visible to automation client |Must|


# Open Question
## ToggleProviderOverride or ToggleStateOverride+ToggleAction
For each provider,  it provides two functions:
1. Specify a state like ToggleState and ExpandCollapseState
2. Invoke a action like Toggle, Expand and Collapse

I don't know if `option 1` is doable, but it's more readable.

Option 1
```
AutomationProperties.ToggleProviderOverride = "{x:bind}"

AutomationProperties.ToggleProviderOverride.ToggleState="{x:Bind ToggleState}
AutomationProperties.ToggleProviderOverride.ToggleAction="{x:Bind ToggleAction_Invoked}" 

```

Option 2
```
AutomationProperties.ToggleStateOverride="{x:Bind ToggleState}" 
AutomationProperties.ToggleAction="ToggleAction_Invoked"
```

## Add `Handled` to ActionEventArgs?
If an control supports ExpandCollapseProvider, and customer overrides ExpandCollapseProvider. Do we need to add `Handled` to ExpandCollapseActionEventArgs, then it fallbacks to Control's ExpandCollapseProvider if Handled is false

# Examples



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
