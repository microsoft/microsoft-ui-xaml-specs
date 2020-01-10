# Background

The WebView2 is the WinUI 3.0 version of the WebView control. It includes usage of the updated Microsoft Edge browser based on the Chromium web engine.
<br> 
<br>
[Documentation for the WinUI 2.0 version of the WebView control](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Controls.WebView)
<br>
<br>
[Discussion of the core Microsoft Edge WebView2](https://docs.microsoft.com/en-us/microsoft-edge/hosting/webview2)

# Description

The WebView is a control that allows for HTML content to be hosted within an application. It renders web content using the Microsoft Edge rendering engine.
Use a WebView control to display richly formatted HTML content from a remote web server, dynamically generated code, or content files in your app. Rich content can also contain script code and communicate between the script and your app’s code.

# Examples

**NavigateToString:**
The following code example demonstrates how to load local HTML into a WebView2 control.

```
myWebView2.NavigateToString("<html><body><h2>This is an HTML fragment</h2></body></html>”);
```
<br>

**Source:**
To set the initial content of the WebView2, set the Source property in XAML. The XAML parser automatically converts the string to a Uri.

```
<!-- Source file is on the web. -->
<WebView2 x:Name="MyWebView_1" Source="http://www.contoso.com"/>

<!-- Source file is in local storage. -->
<WebView2 x:Name="MyWebView_2" Source="ms-appdata:///local/intro/welcome.html"/>

<!-- Source file is in the app package. -->
<WebView2 x:Name="MyWebView_3" Source="ms-appx-web:///help/about.html"/>

<!-- no provided uri initialization -->
<WebView2 x:Name="MyWebView_4"/>
```
<br>

**ExecuteScriptAsync:**
You can interact with the content of the WebView2 by using the ExecuteScriptAsync method to invoke or inject script into the WebView2 content, and the WebMessageReceived event to get information back from the WebView2 content.
To invoke JavaScript inside the WebView2 content, use the ExecuteScriptAsync method. ExecuteScriptAsync returns a string which is the object result of the script execution converted to JSON and then returned as a string.

```
// You can use ExecuteScriptAsync to inject  //content into the web page.
// Here, the text of a Xaml TextBox is written to a div in an HTML page hosted in //myWebView2.
private async void Button_Click(object sender, RoutedEventArgs e)
{
    String functionString = 
String.Format(”document.getElementById('nameDiv').innerText = 'Hello, {0}';”, nameTextBox.Text);
await myWebView2.ExecuteScriptAsync(new string[] { functionString });
}
```

<br>

**WebMessageReceived:**
Scripts in the WebView2 content can use window.chrome.webview.postMessage.postMessage(“Message”) with JSON parameters to send information back to your app. To receive these messages, handle the WebMessageReceived event.


```
// Event allows for notification when a webMessage event is fired from the JavaScript on the html side of the webview.

myWebView2.WebMessageReceived += (WebView2 sender, WebView2WebMessageReceivedEventArgs args) =>
{
    // Important to validate that the Uri is what we expect from that webview.
    string uriAsString = sender.UriSource.ToString();

    if (args.Source == uriAsString)
    {
        HandleWebMessageAsString(args.WebMessageAsString);
        HandleWebMessageAsJson(args.WebMessageAsJson);
    }

    else
    {
        // If the source is not validated, don't process the message.
        return;
    }
};
```
<br>

**NavigationStarting:**
Occurs before the WebView2 navigates to new content. You can cancel navigation in a handler for this event by setting the WebView2NavigationStartingEventArgs.Cancel property to true.


```
myWebView.NavigationStarting += myWebView_NavigationStarting;

private void myWebView_NavigationStarting(WebView2 sender, WebView2NavigationStartingEventArgs args)
{
    if (!IsAllowedUri(args.Uri))
    {
        args.Cancel = true;
    }
}
```
<br>

**ContentLoading:**
ContentLoading fires when new content has started loading on the webview's main frame or if a same page navigation occurs (such as through fragment navigations or history.pushState navigations). This follows the NavigationStarting event and precedes the NavigationCompleted event.

<br>

**SourceChanged:**
Occurs when the 'Source' property is changed

<br>

**NavigationCompleted**:
Occurs when the WebView2 has finished loading the current content or if the navigation has failed. To determine whether the navigation has failed, check the IsSuccess and WebErrorStatus properties of the WebViewNavigationCompletedEventArgs class.


```
myWebView2.NavigationCompleted += myWebView2_NavigationCompleted;

private void myWebView2_NavigationCompleted(WebView2 sender, WebView2NavigationCompletedEventArgs args)
{
    if (args.IsSuccess == true)
    {
        statusTextBlock.Text = "Navigation to “ +
        args.Uri.ToString() + " completed successfully.";
    }

    else
    {
    statusTextBlock.Text = "Navigation to: " + args.Uri.ToString() + " failed with error ” + args.WebErrorStatus,ToString();
    }
}
```

<br>

**Reload:**
Fires navigation event without altering the navigation stack.

<br>

# Remarks

// This API may ship before the WinRT CoreWebView object is included, meaning functionality of the Webview2 will be limited to the APIs included on this object.

# API Notes

**Properties:**
<br>
Source – Gets or sets the Uniform Resource Identifier (URI) source of the HTML content to display in the WebView2 control.

CoreWebView2 – Stores the core WebView object with full access to Anaheim WebView API set.
<br>

**Methods:**
<br>
void NavigateToString(String htmlContent) – Loads the specified HTML content as a new document.

Windows.Foundation.IAsyncOperation ExecuteScriptAsync(String javascriptCode) –

Executes the specified script function from the currently loaded HTML, with specific arguments, as an asynchronous action.

void Reload() – Reloads the current content in the WebView2.

void Stop() – Stops the current WebView2 navigation or download.
<br>

**Events:**
<br>
WebMessageReceived – Occurs when the content contained in the WebView2 control passes a string to the application by using JavaScipt.

NavigationStarting – Occurs before the WebView2 navigates to new content. You can cancel navigation in a handler for this event by setting the WebViewNavigationStartingEventArgs.Cancel property to true.

ContentLoading - ContentLoading fires when new content has started loading on the webview's main frame or if a same page navigation occurs (such as through fragment navigations or history.pushState navigations). This follows the NavigationStarting event and precedes the NavigationCompleted event.

SourceChanged - Occurs when the 'Source' property is changed

NavigationCompleted – Occurs when the WebView2 has finished loading the current content or if the navigation has failed.

<br>

# API Details

API Details

```
namespace MU\_XC\_NAMESPACE
{
    [WUXC\_VERSION\_PREVIEW]
    [webhosthidden]

    runtimeclass WebView2ElementNavigationCompletedEventArgs
    {
        Boolean IsSuccess{ get; };
        Windows.Web.WebErrorStatus WebErrorStatus{ get; };
    }

    [WUXC\_VERSION\_PREVIEW]
    [webhosthidden]
    runtimeclass WebView2ElementWebMessageReceivedEventArgs
    {
        String Source{ get; };
        String WebMessageAsJson{ get; };
        String WebMessageAsString{ get; };
    }

    [WUXC\_VERSION\_PREVIEW]
    [webhosthidden]
    runtimeclass WebView2ElementNavigationStartingEventArgs
    {
        String Uri{ get; };
        Boolean IsUserInitiated{ get; };
        Boolean IsRedirected{ get; };
        // RequestHeaders will be included once an appropriate winrt collection is available.
        Boolean Cancel{ get; set; };
    }

    [WUXC\_VERSION\_PREVIEW]
    [webhosthidden]
    [MUX\_PROPERTY\_CHANGED\_CALLBACK(TRUE)]
    [MUX\_PROPERTY\_CHANGED\_CALLBACK\_METHODNAME("OnPropertyChanged")]
    unsealed runtimeclass WebView2Element : Windows.UI.Xaml.Controls.Control
    {
        WebView2Element();
        Windows.Foundation.IAsyncOperation ExecuteScriptAsync(String javascriptCode);

        Windows.Foundation.Uri UriSource{ get; set; };
        Boolean CanGoForward{ get; set; };
        Boolean CanGoBack{ get; set; };
        void Reload();
        void GoForward();
        void GoBack();
        void NavigateToString(String htmlContent);


        static Windows.UI.Xaml.DependencyProperty UriSourceProperty{ get; };
        static Windows.UI.Xaml.DependencyProperty CanGoForwardProperty{ get; };
        static Windows.UI.Xaml.DependencyProperty CanGoBackProperty{ get; };

        event Windows.Foundation.TypedEventHandler<WebView2Element, WebView2ElementNavigationCompletedEventArgs> NavigationCompleted;
        event Windows.Foundation.TypedEventHandler<WebView2Element, WebView2ElementWebMessageReceivedEventArgs> WebMessageReceived;
        event Windows.Foundation.TypedEventHandler<WebView2Element, WebView2ElementNavigationStartingEventArgs> NavigationStarting;
    }
}
```
