> See comments in Markdown for how to use this spec template

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
In WPF Developers had access to Input Validation via [INotifyDataErrorInfo](https://docs.microsoft.com/en-us/dotnet/api/system.componentmodel.inotifydataerrorinfo?view=netframework-4.7.2) We are adding the same capability to UWP.
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

Most applications that take user input need to have validation logic to ensure that the user has entered the expected information. The validation checks can be based on type, range, format, or other application-specific requirements. For example:
![validation example](basicvalidationexample.png)


# Examples

## End to end input validation

In this sample, we want to display a validation error on a textbox when user input is invalid. This is the recommended model for validating fields. The validation logic with DataAnnoations displayed in this sample is proper to managed apps. Native apps can use INotifyDataErrorInfo as described below and substitute their current validation logic in place of the .Net APIs.

![first name](firstnameexample.png)

Use INotifyDataErrorInfo with x:Bind to update your view. This API can be implemented by any class, and allows the object to raise an event if one of its properties is set to an invalid value. When the source of a Xaml x:Bind implements this interface, the x:Bind will put the validation errors onto the bind target. Xaml controls such as TextBox will recognize these errors and provide visualization for them.

### XAML

a user input control with the input bound:

```xaml
<TextBox Name="ValidationTextbox" 
         Text="{x:Bind  ViewModel.Person.Name} "/>

```
#### Code - ViewModel

The “ViewModel” property is the View Model for the Page on which this TextBox is displayed (this.ViewModel).
``` csharp

public class ViewModel
{
    public ViewModel()
    {
        Person = new Person();
    }

    public Person Person { get; set; }

}
```

#### C# only - Person (Model)
Person.Name is the source of the bind (this.ViewModel.Person.Name).  Person derives from ValidationWrapper (app code) which will provide the GetValue/SetValue methods which perform data validation for Name.  It’s marked with [MinLength] and [MaxLength] data annotations.  The property accessors use GetValue/SetValue in the base ValidationWrapper class for storage and to check the validity.

``` csharp
using System.ComponentModel.DataAnnotations;
    
public class Person : ValidationWrapper
{
    [MinLength(4, ErrorMessage = "Name must be at least 4 characters")]
    [MaxLength(6, ErrorMessage = "Name must be no more than 6 characters")]
    public string Name
    {	
        get { return GetValue<string>("Name"); }
        set { SetValue("Name", value); }
    }
}
```

#### Code – INotifyDataErrorInfo base class
The NotifyDataErrorInfoBase class (app code) provides a reusable implementation of INotifyDataErrorInfo (new API).  It’s the base of ValidationWrapper, which is the base class for Person.
``` csharp
public class NotifyDataErrorInfoBase : INotifyDataErrorInfo
{
    protected readonly Dictionary<string, ObservableCollection<ValidationError>> Errors;

    protected NotifyDataErrorInfoBase()
    {
        Errors = new Dictionary<string, List<string>>();
    }

    public bool HasErrors => Errors.Any();

    public event EventHandler<DataErrorsChangedEventArgs> ErrorsChanged;

    public ObservableCollection<ValidationError> GetErrors(string propertyName)
    {
        return propertyName != null && Errors.ContainsKey(propertyName)
            ? Errors[propertyName]
            : new ObservableCollection<ValidationError>();
    }

    protected virtual void OnErrorsChanged(string propertyName)
    {
        ErrorsChanged?.Invoke(this, new DataErrorsChangedEventArgs(propertyName));
    }

    protected void ClearErrors()
    {
        foreach (var propertyName in Errors.Keys.ToList())
        {
            Errors.Remove(propertyName);
            OnErrorsChanged(propertyName);
        }
    }
}
```
#### Code – ValidationWrapper (no new APIs)
ValidationWrapper derives from the above NotifyDataErrorInfoBase, and provides GetValue/SetValue methods for storage of subclass’ properties.  The SetValue performs validation.  This also provides an implementation of INotifyPropertyChanged.

``` csharp
public class ValidationWrapper : NotifyDataErrorInfoBase
{

    #region get/set
    protected T GetValue<T>(string propertyName)
    {
        if (string.IsNullOrEmpty(propertyName))
        {
            throw new ArgumentException("Invalid property name", propertyName);
        }

        object value;
        value = default(T);

        return (T)value;
    }

    // Validate() gets called from inside SetValue
    protected void SetValue<T>(string propertyName, T value)
    {
        if (string.IsNullOrEmpty(propertyName))
        {
            throw new ArgumentException("Invalid property name", propertyName);
        }

        var current = GetValue<T>(propertyName);

        if (!value.Equals(current))
        {
            PropertyChanged?.Invoke(
                    this, new PropertyChangedEventArgs(propertyName));
            Validate(propertyName);
        }
    }
    #endregion

    #region Populate errors (C# - DataAnnotations)
    private Task Validate(string propertyName)
    {
        ClearErrors();


        return Task.Run(() =>
        {
            var val = GetPropertyValue(propertyName);
            var results = new ObservableCollection<ValidationResult>();
            var context = new ValidationContext(this, null, null) 
                            { MemberName = propertyName };
    
            // Validator is from the .Net System.ComponentModel.DataAnnotations namespace
            Validator.TryValidateProperty(val, context, results);

            if (results.Any())
            {
                    foreach (var result in results)
                    {
                        Errors[propertyName].Add(new ValidationError(result.ErrorMessage));
                    }
            }

                OnErrorsChanged(propertyName);
            }

        }
    }
    #endregion

    #region privates
    private object GetPropertyValue([CallerMemberName] string propertyName = "")
    {
        return _values.ContainsKey(propertyName) ? _values[propertyName] : null;
    }

    #endregion
    }
}
```

## Custom validation UI


#### XAML
A developer wants to create their own visuals for the error message style and not use the control’s visuals at all. They may want to do this to place messages far from the control or something else.

To do this, the developer needs to turn off the system error visuals and then display the content from the errors collection in their own UI.

![Custom UI](customuiexample.png)

```xaml
<TextBox 
        x:Name="Name" 
        Header="Name: " 
        PlaceholderText="Enter Name..." 

        Text="{x:Bind   ViewModel.Person.Name, 
                        UpdateSourceTrigger=PropertyChanged, 
                        Mode=TwoWay}" 
         UseSystemValidationVisuals="False"
        />

<!-- Custom error message template -->
<ItemsControl
    ItemsSource="{x:Bind Name.(Validation.Errors), Mode=OneWay}"
    Foreground="Red">

    <ItemsControl.ItemTemplate>
        <DataTemplate x:DataType="ValidationError">
            <StackPanel Orientation="Horizontal" Spacing="5">
                <Border Background="Red" Margin="3" CornerRadius="10" 
                        Width="12" Height="12" VerticalAlignment="Top">
                    <TextBlock Text="!" Foreground="White" 
                               HorizontalAlignment="Center" VerticalAlignment="Center"/>
                </Border>
                <TextBlock Text="{x:Bind ErrorMessage, Mode=OneWay}"
                           />
            </StackPanel>
        </DataTemplate>
    </ItemsControl.ItemTemplate>
</ItemsControl>
```

## Customizing the error template

To modify the styling and content of your error template, use the Validation.ErrorTemplate property. The Validation.ErrorTemplate refers to either the content of the tooltip or the content of the container below the control depending on the validation mode.

If you wish to override the default visuals for errors across all forms elements in your app, override the ValidationErrorTemplate DataTemplate and use in your app.

```xaml
<ResourceDictionary>
    <DataTemplate x:Key="ValidationErrorTemplate">
        <StackPanel Orientation="Horizontal">
            <Border Background="Red" Margin="3" CornerRadius="10" Width="20" Height="20" VerticalAlignment="Top">
                <TextBlock Text="!" Foreground="White"
                HorizontalAlignment="Center" VerticalAlignment="Center"/>
            </Border>
            <TextBlock Text="{x:Bind (Validation.Errors)[0].ErrorContent, Mode=OneWay}"
        </StackPanel>
    </DataTemplate>
</ResourceDictionary>

<TextBox Validation.ErrorTemplate="{StaticResource ValidationErrorTemplate}"/>
```

## Change the resource color for the error and description messages
Colors for the error and description messages are defined in a standard color/brush pattern in generic.xaml that can be overridden at an app level.

```xaml
<Application.Resources>

    <!--Color for error messages-->
    <Color x:Key="SystemErrorColor">#CE3880</Color>
    <SolidColorBrush x:Key="SystemControlErrorTextForegroundBrush" Value="SystemErrorColor"/>

    <!--Brush for description messages-->
    <SolidColorBrush x:Key="SystemControlDescriptionTextForegroundBrush" Value="SystemControlPageTextBaseMediumBrush"/>

</Application.Resources>
```

## Change description text for validation

Description text provides input to the end user on what format or content is expected from the control when it is not clear from the header and placeholder text.

Developers can choose to either show the description text for a control or not. Default is that description text is off. 

Description text should be used to complement headers and placeholders. Guidance on what each corresponds to is as follows: 
1.	Headers give you an indication of what the control represents. It is used to catch the eye of a user scanning your form or to refer to a field.
2.	Placeholders provide a succinct description of what should be input in the box. However, they aren’t an ideal pattern for guiding users input because user testing continually shows that placeholders in form fields often hurt usability more than help it. This is because: 
    - Disappearing placeholder text strains users’ short-term memory.
    -	Users may mistake a placeholder for data that was automatically filled in.
    -	The default light-grey color of placeholder text has poor color contrast against most backgrounds.
    -	Not all screen readers read placeholder text aloud
    -	Often incompatible with autofill – you need to delete field content before finding the hint.
3.	Description text is used to complement placeholder text. It provides more detailed formatting guidance that is needed while editing the field. 

#### XAML
```xaml
    <TextBox 
            x:Name="FirstName" 
            Header="First Name:" 
            Text="{x:Bind   ViewModel.Person.FirstName, 
                            UpdateSourceTrigger=LostFocus, 
                            Mode=TwoWay}"
            DescriptionText="Use alphabetical characters (a-Z)."
        />
```

#### C# (Model)
```csharp
    public class Person : ValidationWrapper
    {
        [Required]
        [RegularExpression(@"^[a-zA-Z''-'\s]{1,40}$", ErrorMessage ="Use alphabetical characters (a-Z).")]
        public string FirstName {
            get { return GetValue<string>("FirstName"); }
            set { SetValue("FirstName", value); }
        }
    }
```

## Validation for view-centric developers
Code a view-centric (Winforms-like) developer would write to enable Validation.


### 1.10.1	C# Sample
#### XAML

```xaml
<TextBox x:Name="Email" 
         Header="Email" 
         Validation.IsRequired="True" 
         TextChanged="TextBox_TextChanged"/>
```

#### C#
```csharp
private void TextBox_TextChanged(object sender, TextChangedEventArgs e)
{
    WinformsValidate(sender, e);
}

private void WinformsValidate(object sender, TextChangedEventArgs e)
{
    var foo = sender as TextBox;
    Validation.ClearErrors(foo);
    Validation.AddErrorHandler(foo, OnValidationError);

    if (!new EmailAddressAttribute().IsValid(foo.Text))
    {

        var ErrorsCollection = Validation.GetErrors(foo);
        ErrorsCollection.Add(new ValidationError("Email is not a valid email address"));
    }
}

private void OnValidationError(object sender, ValidationErrorEventArgs e)
{
    if (e.Action == ValidationErrorEventAction.Added)
        // Do something when an error message was added
    else
        // Do something when an error was removed.

        SaveButton.IsEnabled = Validation.GetErrors(e.Source);
}
```

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

## InputPropertyAttribute
The `InputPropertyAttribute` helps us ensure that we only generate validation code for the property specified by this attribute. This will help reduce code bloat by not generating code for properties that will never perform validation (like width). Controls can specify multiple properties as their input property. 

    /// <summary>
    /// Name of the property that is associated with user input.
    /// </summary>
    attribute InputPropertyAttribute
    {
         string Name;
    }

## InputValidationKind  
Determines how the control displays validation visuals.

    /// <summary>
    ///    Determines how the control displays it's validation visuals
    /// </summary>
    [webhosthidden]
    enum InputValidationKind
    {
        /// <summary>
        /// Let's the Control dictate how to display visuals. This is the default value.
        /// </summary>
        Auto,
        /// <summary>
        /// An error icon displays to the right of the control that displays the error messages in a tool tip.
        /// </summary>
        Compact,
        /// <summary>
        /// Text for the error messages is displayed underneath the control.  
        /// </summary>
        Inline,
        /// <summary>
        /// Validation visuals are not displayed at all.
        /// </summary>
        Hidden,
    };

## IInputValidationControl
Any control that wants to participate in input validation should derive from this interface. This, among other things, is what allows the markup compiler to know when to generate the code necessary for listening to the `INotifyDataErrorInfo.ErrorsChanged` event and propagate the results to the `IInputValidationControl.Errors` property.

When the `ValidationCommand` property is non-null, the following will be true:
1. When the `InputValidationKind` property is set to `Hidden`, the control will *not* invoke the `ValidationCommand.CanValidate` or `ValidationCommand.Validate` methods
2. When the `InputValidationKind` property is set to `Auto`, the control will use the value of the `ValidationCommand.InputValidationKind` property for the value. If both are `Auto` the default bevahior should be `Inline`.
3. When focus has been lost, validation should be attempted by calling the `ValidationCommand.CanValidate` method.
4. If the `IInputValidationControl.ValidationErrors` collection is non-empty, validation should be attempted by calling the `ValidationCommand.CanValidate` method.

The markup compiler uses this interface to properly generate code when using x:Bind iff:
1. The Source implements INotifyDataErrorInfo
2. The Target property is the one specified by the TargetType's InputPropertyAttribute
3. The BindingMode is set to TwoWay

The code generated provides the following functions:
1. Generate code that listens to the Source's INotifyDataErrorInfo.ErrorsChanged event and populates the Target's 'IInputValidationControl.ValidationErrors' property.
2. Generate and automatically set the ValidationContext property based on the Source property. If that property has any System.ComponentModel.DataAnnotations.ValidationAttributes, the associated property on the ValidationContext class will be set.

         /// <summary>
         /// Interface for Controls that participate in input validation.
         /// <summary/>
          [webhosthidden]
         interface IInputValidationControl 
         {
             /// <summary>
             /// Collection of errors to display based on the InputValidationKind property.
            /// </summary>
            Windows.Foundation.Collections.IObservableVector<Windows.UI.Xaml.Controls.InputValidationError> ValidationErrors{ get; };
            /// <summary>
            ///  Extra information about the Source of the validation. 
            ///  Note: This is automatically set by the XAML markup compiler. See summary above. 
            /// </summary>
            Windows.UI.Xaml.Controls.InputValidationContext ValidationContext;
            /// <summary>
            ///  DataTemplate that expresses how the errors are displayed.
            /// </summary>
            Windows.UI.Xaml.DataTemplate ErrorTemplate;
            /// <summary>
            ///  Determines how the control should visualize validation errors.
            /// </summary>
            Windows.UI.Xaml.Controls.InputValidationKind InputValidationKind;
            /// <summary>
            /// Command associated with the IInputValidationControl. See InputValidationCommand for more
            /// information.
            /// </summary>
            Windows.UI.Xaml.Controls.InputValidationCommand ValidationCommand;
         };
   
## InputValidationCommand
The command allows for easy use of input validation without the need for a tight dependency on x:Bind.  This enables developers who aren't striclty using MVVM to use input validation (i.e ReactiveUI).  The InputValidationCommand is what allows for the correct-by-design approach where controls initially validate on focus lost, but then will query for validation on property changed once in an invalid state.  This wouldn't be for free, but the amount of code here is pretty trivial. We could provide some attribute or pattern that tells the markup compiler how to generate that code automatically.  

The command can shared between different IInputValidationControls, as would most likely be the case in the scenario of a forms control.  A custom command can be made that listens for changes to all errors and knows when a form has been completed and ready for submition. The control will query the command as to whether they should perform validation.

Developers can override any of this behavior by creating their own InputValidationCommand.
 
 
    /// <summary>
    /// Command that controls how an IInputValidationControl does validation.
    /// </summary>
    [webhosthidden]
    unsealed runtimeclass InputValidationCommand
    {
        [method_name("CreateInstance")] InputValidationCommand();
        Windows.UI.Xaml.Controls.InputValidationKind InputValidationKind;
        /// <summary>
        /// Called by a control to determine if it should call the Validate method.
        /// </summary>
        Boolean CanValidate(Windows.UI.Xaml.Controls.IInputValidationControl validationControl);
        /// <summary>
        /// If CanValidate returns true, Validate is invoked to perform validation on the
        /// validationControl provided.
        /// </summary>
        void Validate(Windows.UI.Xaml.Controls.IInputValidationControl validationControl);
        /// <summary>
        /// Method that derived classes implement to provide their own implementation of the CanValidate method.
        /// </summary>
        overridable Boolean CanValidateCore(Windows.UI.Xaml.Controls.IInputValidationControl validationControl);
        /// <summary>
        /// Method that derived classes implement to provide their own implementation of the Validate method.
        /// </summary>
        overridable void ValidateCore(Windows.UI.Xaml.Controls.IInputValidationControl validationControl);
    };

## ValidationContext
The `ValidationContext` class provides context as to what is being required by validation. This class is auto-generated by the XAML markup compiler in certain scenarios (see [IInputValidationControl]()). Controls can use the provided properties on the `ValidationContext` to customize their appearance.  For example, the IsInputRequired field is can allow controls to put the little * next to a header. 

Note: Intended to be extended for things like IsPhoneNumber or IsEmail that will allow controls to have other behavior (like mask text box).

    
    /// <summary>
    /// Provides more contextual information about how to validate, display, and/or format input.
    /// </summary>
    [webhosthidden]
    unsealed runtimeclass InputValidationContext
    {
        [method_name("CreateInstance")] InputValidationContext(String memberName, Boolean isRequired);
        Boolean IsInputRequired{ get; };
        String MemberName{ get; };
    };
    
## InputValidationError
Provides the error message for what should be displayed to the user.
    
    /// <summary>
    ///  Provides the error message for what should be displayed to the user.
    /// </summary>
    [webhosthidden]
    unsealed runtimeclass InputValidationError
    {
        [method_name("CreateInstance")] InputValidationError(String errorMessage);
        String ErrorMessage{ get; };
    };


# Appendix
<!-- Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.
For example, implementation details. -->
