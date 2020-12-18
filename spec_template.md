> See comments in Markdown for how to use this spec template

To do:
* PR ProtectedCursor updates
* PR RGB updates


<!-- The purpose of this spec is to describe new APIs, in a way
that will transfer to docs.microsoft.com.

There are two audiences for the spec. The first are people
that want to evaluate and give feedback on the API, as part of
the submission process.  When it's complete
it will be incorporated into the public documentation at
docs.microsoft.com (http://docs.microsoft.com/uwp/toolkits/winui/).
Hopefully we'll be able to copy it mostly verbatim.
So the second audience is everyone that reads there to learn how
and why to use this API.

For example, much the examples and descriptions in the RadialGradientBrush API spec
(https://github.com/microsoft/microsoft-ui-xaml-specs/blob/master/active/RadialGradientBrush/RadialGradientBrush.md)
were carried over to the public API page
(https://docs.microsoft.com/en-us/windows/winui/api/microsoft.ui.xaml.media.radialgradientbrush?view=winui-2.5)

Once the API is on docs.microsoft.com (DMC), that becomes the official copy, and this
spec becomes an archive. For example if the description is updated,
that only happens on DMC and not here.
-->


# Background
<!-- 
Use this section to provide background context for the new API(s) 
in this spec. Try to briefly provide enough information to understand
the rest of the document.

This section and the appendix are the only sections that likely
do not get copied to docs.microsoft.com; they're just an aid to reading this spec.

For example, this is a place to provide a brief explanation of some dependent
area, just explanation enough to understand this new API, rather than telling
the reader "go read 100 pages of background information posted at ...".

For example, this section is a place to explain why you're adding this API rather than
modifying an existing API.

For example see the spec for the UIElement.ProtectedCursor property
(https://github.com/microsoft/microsoft-ui-xaml-specs/blob/master/active/RadialGradientBrush/RadialGradientBrush.md)
which has some of the thinking about how this Xaml API relates to existing
Composition and WPF APIs. This is interesting background but not the kind of information
that would land on DMC.
-->


# Conceptual pages

<!-- 
All APIs have a page on DMC, some APIs or groups of APIs have an additional high level,
conceptual page (sometimes called a "how-to" page). This section is for that content.

For example, there are several Xaml controls for different forms of text input,
and then there's also a conceptual pages that discusses them collectively
(https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/text-controls)
-->

<!-- Use this section to provide a brief description of the feature.
For an example, see the introduction to the PasswordBox control 
(http://docs.microsoft.com/windows/uwp/design/controls-and-patterns/password-box). -->


# API Pages

<!--
Each level-2 section within this section is a type or member description. Since DMC has
a page for each type and member, each one of these sections will become a page.

Notes:
* The first line of each of these sections should become that first line on the DMC page,
  that then becomes the description you see in Intellisense.
* Each page can have an Examples section and/or a Remarks section.
  Remarks are where the documentation calls out special considerations that the developer should be aware of.
* Add an "Other members" section after a type to give a for example a table
  of the other members and their Intellisense description.


Example layout of the API Pages:

  Type1
    Examples
    Remarks
  Type1.Member1
    Examples
  Type1.Member2
  Other Type1 Members
    (Table)
  ...
  Type2
  ...

Example specs:
* RGB (new class and members, uses Other Members section)
* ProtectedCursor (add a member to an existing class)

-->


# API Details
<!-- 
The exact APIs, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/)
 -->

# Appendix
<!-- 
Anything else that you want to write down for posterity, but 
that isn't necessary to understand the purpose and usage of the API.

For example, implementation details. 

-->
