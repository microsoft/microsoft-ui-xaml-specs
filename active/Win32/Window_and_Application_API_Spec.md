# Window and Application API updates 

This spec describes updates to the XAML Window and Application APIs to enable them to support Desktop apps in addition to UWP.

# Background

Xaml in UWP has a [Window](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Window) class which wraps a [CoreWindow](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow), and an [Application](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Application) class which wraps a [CoreApplication](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Core.CoreApplication). For WinUI 3 this is being expanded to not require a CoreWindow or CoreApplication; Window can use an hwnd and Application can run a message pump. This spec has the API additions to Application and Window to support this.

Note that some existing APIs will also behave differently when running as a Desktop app. For example, the static Window.Current property today returns the Window for the current (calling) thread, but in a non-UWP app it will return null. Similarly the Window.CoreWindow property will be null when not running as UWP.

There is also one API not (yet) present in WinUI: [Window.SetTitleBar](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Window.SetTitleBar). That API relies on a Composition feature that only exists for system Visuals and is not yet supported for WinUI Visuals.


# Examples and API Notes

## Window class

Window represents a WinUI application window. It can be used in a [UWP](https://docs.microsoft.com/en-us/windows/uwp/get-started/universal-application-platform-guide) app or a Desktop app. When run in a UWP app there can only be one instance on a thread.

### Examples

> Spec note: The only new API in this set of examples is the Window constructor. Shown here is the general description for the class, so that we can see it in full context. But none of the behavior for UWP apps is changed.

Set the content of a given window and give it keyboard focus.

```CS
void InitializeAndActivateWindow(Window window)
{
    window.Content = new TextBlock() { Text = "Hello" };
    window.Activate();
}
```

In a UWP app the read-only [Window.Current](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Window.Current) property returns the Window for the current thread. (A thread has at most one Window and it's created automatically on each UI thread.) This property returns null on a non UI thread or in a Desktop app.

The following sets the content of the calling thread's Window in a UWP app and gives it keyboard focus.

```CS
Window.Current.Content = new TextBlock() { Text = "Hello" };
Window.Current.Activate();
```

In a Desktop app you create each Window, and you can create more than one Window on a thread.

```CS
Window window = new Window();
window.Content = new TextBlock() { Text = "Hello" };
window.Activate(); 
```

You can also define a new Window in markup:

| Spec note: no <Window.Content> tag is required because Window.Content is updating to become the [ContentPropertyAttribute](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Markup.ContentPropertyAttribute).

```XML
<Window 
    x:Class="MainWindow"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    <TextBlock>Hello</TextBlock>
</Window>
```

with corresponding code-behind:

```CS
public partial class MainWindow : Window
{
  public MainWindow()
  {
	InitializeComponent();
  }
}
```

and then you can create/activate that custom window from code:

```CS
MainWindow window = new MainWindow();
window.Activate();

```

In a UWP app the main thread already has a Window on it, which you can retrieve using the static [Window.Current](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Window.Current) property. You can create additional windows by creating additional [CoreApplicationViews](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Core.CoreApplicationView), which are always created on a new thread, and which automatically create the following for the new thread: [ApplicationView](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.ApplicationView), [CoreWindow](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow), and [Window](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Window).

Create a new Window in a UWP app (on a new thread):

```CS
_ = CoreApplication.CreateNewView().DispatcherQueue.TryEnqueue(() =>
{
    // This code runs on the new thread
    Window.Current.Content = new TextBlock() { Text = "Hello" };
    Window.Current.Activate();
});
```

To create a new Window on a new thread in a Desktop app, create the thread first:

```CS
Thread thread = new Thread(() =>
{
    Window window = new Window();
    window.Content = new TextBlock() { Text = "Hello" };
    window.Activate();
    window.Closed += (sender1, e1) => w.Dispatcher.InvokeShutdown();

    System.Windows.Threading.Dispatcher.Run(); // Issue: There should be a Window.Run()?

});
thread.SetApartmentState(ApartmentState.STA);
thread.Start(); 
```

### Remarks

Attempting to activate (`new`) a new Window in a UWP app will fail and log a debug message.

> Implementation note: This will use [RoOriginateError](https://docs.microsoft.com/en-us/windows/win32/api/roerrorapi/nf-roerrorapi-rooriginateerror) to show an explanatory message in the debugger.

Creating a new Window in a Desktop app creates a new top level hwnd.

## IWindowNative Interface **[NEW]**

This interface is implemented by Window, and in a Desktop app can be used to get the Window's underlying hwnd.

> Issue: need sample here

## Window.Icon property **[NEW]**
Gets or sets a window's icon. For each window, this icon is used in its title bar, its task bar button, and in its ALT+TAB application selection list entry.

> Note: this property is ignored in a UWP app

```CS
public ImageSource Icon { get; set; }
```

```XML
<Window
  xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
  xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
  x:Class="Sample.MainWindow"
  Icon="WinUI3Icon.ico">
</Window>
```



## Window.Title property **[NEW]**
Get or set a string to display on the Title. 

```CS
public string Title { get; set; }
```

### Example

```XML
<Window 
    x:Class="MainWindow"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    Title="Main Window" >
    
</Window>
```

### Remarks

In a UWP app, this property is a wrapper for [ApplicationView.Title](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.ApplicationView.Title), which is ignored if [AppWindowTitleBar.ExtendsContentIntoTitleBar](https://docs.microsoft.com/uwp/api/Windows.UI.WindowManagement.AppWindowTitleBar.ExtendsContentIntoTitleBar) is set to true.

In a Desktop app this is a wrapper for [SetWindowText](https://docs.microsoft.com/en-us/windows/win32/api/winuser/nf-winuser-setwindowtexta).

## Window.Current property

Gets the window of the current thread.

```CS
public static Window Current { get; }
```

### Remarks

The value of this property depends on the thread from which it is called. If called from a UI thread of a UWP app, the value is the Window instance for that thread. On any other thread, the value is null.


## Window.Closed event

Occurs when the window has closed.

### Remarks

If this was the last window to be closed for the app, the Suspending event will be raised. For a Desktop app, the application will end.


## Window.CoreWindow property: 

Gets the [CoreWindow](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow) associated with this Window. In a Desktop app this is always null.

```CS
public CoreWindow CoreWindow { get; }
```

## Window.Dispatcher property
Gets the [CoreDispatcher](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreDispatcher) object for the Window when called from a UWP app. Returns null when called from a Desktop app.

```CS
public CoreDispatcher Dispatcher { get; }
```

## Window.DispatcherQueue property **[NEW]**

Gets the [DispatcherQueue](https://docs.microsoft.com/uwp/api/Windows.System.DispatcherQueue) object for the Window.

```CS
public DispatcherQueue DispatcherQueue { get; }
```

## Window.Compositor property

Gets the Compositor for this Window. This allows to access to the Visual Layer (the layer where the XAML framework sits) and use WinRT compositor APIs inside of the Window. 

```CS
public Microsoft.UI.Composition.Compositor Compositor { get; }
```

### Remarks
There is one Compositor per thread, that means that all the Windows in the same thread share the same Compositor.


## WindowActivatedEventArgs class
Namespace: Microsoft.UI.Xaml

> Issue: should rename these to avoid collision.

> Spec note: this is a new Xaml version of the existing Windows.UI.Core.[WindowActivatedEventArgs](https://docs.microsoft.com/uwp/api/Windows.UI.Core.WindowActivatedEventArgs).

Contains the windows activation state information returned by the Window.Activated event.

```CS
public class WindowActivatedEventArgs
```

### Properties
- **Handled**: Gets or sets whether the window activation changed event was handled. **true** if the event has been handled by the appropriate delegate; **false** if it has not.

    ```CS
    public bool Handled { get; set; }
    ```


- **WindowActivatedEventArgs.WindowActivationState**: Gets the activation state of the window at the time the event was raised.

    ```CS
    public CoreWindowActivationState WindowActivationState { get; }
    ```

## VisibilityChangedEventArgs class 
Namespace: Microsoft.UI.Xaml

> Spec note: this is a new Xaml version of the existing Windows.UI.Core.[VisibilityChangedEventArgs](https://docs.microsoft.com/uwp/api/Windows.UI.Core.VisibilityChangedEventArgs).

Contains the arguments returned by the event fired when a Window instance's visibility changes.
```CS
public class VisibilityChangedEventArgs
```

### Properties:
- Handled: Gets or sets whether the window activation changed event was handled. **true** if the event has been handled by the appropriate delegate; **false** if it has not.
    ```CS
    public bool Handled { get; set; };
    ```

- Visible: Gets whether the window is visible or not. **true** if the window is visible; otherwise is **false**.
    ```CS
    public bool Visible { get; } 
    ```

## WindowSizeChangedEventArgs class
Namespace: Microsoft.UI.Xaml

> Spec note: this is a new Xaml version of the existing Windows.UI.Core.[WindowSizeChangedEventArgs](https://docs.microsoft.com/uwp/api/Windows.UI.Core.WindowSizeChangedEventArgs).

Contains the argument returned by a window size change event.

### Properties

- Handled: Gets or sets whether the  event was handled. **true** if the event has been handled by the appropriate delegate; **false** if it has not.
    ```CS
    public bool Handled { get; set; };
    ```

- WindowSizeChangedEventArgs.Size: Gets the new size of the window in units of effective (view) pixels.
    ```CS
    public Size Size { get; } 
    ```


## Application class
Namespace: Microsoft.UI.Xaml

| Application is not new to this spec, what is new are the following remarks

### Remarks

The following events and virtual methods are not invoked when running in a Desktop app:

* void overridable OnActivated(IActivatedEventArgs args)
* void overridable OnBackgroundActivated (BackgroundActivatedEventArgs)
* void overridable OnCachedFileUpdaterActivated (CachedFileUpdaterActivatedEventArgs)
* void overridable OnFileActivated (FileActivatedEventArgs)
* void overridable OnFileOpenPickerActivated (FileOpenPickerActivatedEventArgs)
* void overridable OnFileSavePickerActivated (FileSavePickerActivatedEventArgs)
* void overridable OnSearchActivated (SearchActivatedEventArgs)
* void overridable OnShareTargetActivated (ShareTargetActivatedEventArgs)
* EnteredBackgroundEventHandler EnteredBackground;
* LeavingBackgroundEventHandler LeavingBackground;
* EventHandler&lt;Object&gt; Resuming


## Application.Start method

Provides the entry point and requests initialization of the application. Use the callback to instantiate the Application class.

In a Desktop app this will run a message pump internally, and not return until the application shuts down. In a UWP app it will return immediately.


## Application.OnLaunched(LaunchActivatedEventArgs) method

Invoked when the application is launched. Override this method to perform application initialization and to display initial content in the associated Window (UWP apps) or create a new Window (Win32 apps).

> This method is typically created automatically when a project is created. You can then modify it or remove it.

```CS
protected virtual void OnLaunched(LaunchActivatedEventArgs args)
```

### Examples

In a Desktop app, use OnLaunched to create the main window and pass it the first command line argument.

```CS
protected override void OnLaunched(MUX.LaunchActivatedEventArgs e)
{
     MainWindow window = new MainWindow();
     
     if(e.Arguments != null)
     {
        string[] arguments = e.Arguments.Split(' ');
        window.FileName = arguments[0]
     }	

     window.Activate();
}
```

In a UWP app,

```CS
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
     MainPage page = new MainPage();
     
     if(e.Arguments != null)
     {
        string[] arguments = e.Arguments.Split(' ');
        page.FileName = arguments[0]
     }	

    Window.Current.Content = page;
    Window.Current.Activate();
    window.Activate();
}
```


## Application.RequiresPointerMode

Gets or sets whether a UWP app supports mouse mode, which emulates pointer interaction experiences with non-pointer input devices such as an Xbox gamepad or remote control. (All nested elements inherit this behavior.)

> Note: this property is ignored in a Desktop app

## Application.OnWindowCreated(WindowCreatedEventArgs) method
Invoked when the application creates a window.

```CS
protected virtual void OnWindowCreated(WindowCreatedEventArgs args)
```
### Parameters
args: Event data for the event. The WindowCreatedEventArgs contains the Window object.

### Remarks
- **UWP**: This method is called once for the creation of the main window, once for each hosted window, and once each time CreateNewView is called. 
- **Win32**: This method is called every time a Window object is created.


## LaunchActivatedEventArgs class

Namespace: Microsoft.UI.Xaml.Application

Provides event information when an app is launched.

> Spec note: this is a new Xaml version of the existing Windows.ApplicationModel.Activation.[LaunchActivatedEventArgs](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.LaunchActivatedEventArgs).

### Properties

- Arguments: Gets the arguments that are passed to the app during its launch.
    ```CS
    public String Arguments { get; };
    ```
- LaunchActivatedEventArgs: Gets the LaunchActivatedEventArgs from UWP. This property gets Null in Win32.
    ```CS
    public Windows.ApplicationModel.Activation.LaunchActivatedEventArgs UWPLaunchActivatedEventArgs { get; };
    ```

