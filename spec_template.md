> See comments in Markdown for how to use this spec template

To do:
* PR ProtectedCursor updates
* PR RGB updates

<!-- The purpose of this spec is to describe new APIs, in a way
that will transfer to docs.microsoft.com (DMC).

There are two audiences for the spec. The first are people
that want to evaluate and give feedback on the API, as part of
the submission process.  When it's complete
it will be incorporated into the public documentation at
docs.microsoft.com (http://docs.microsoft.com/uwp/toolkits/winui/).
Hopefully we'll be able to copy it mostly verbatim.
So the second audience is everyone that reads there to learn how
and why to use this API. Some of this text also shows up in
Visual Studio Intellisense.

For example, much of the examples and descriptions in the RadialGradientBrush API spec
(https://github.com/microsoft/microsoft-ui-xaml-specs/blob/master/active/RadialGradientBrush/RadialGradientBrush.md)
were carried over to the public API page on DMC
(https://docs.microsoft.com/en-us/windows/winui/api/microsoft.ui.xaml.media.radialgradientbrush?view=winui-2.5)

Once the API is on DMC, that becomes the official copy, and this
spec becomes an archive. For example if the description is updated,
that only needs to happen on DMC and needn't be duplicated here.
-->

Title
==

# Background

<!-- 
Use this section to provide background context for the new API(s) 
in this spec. Try to briefly provide enough information to be able to read
the rest of the document.

This section and the appendix are the only sections that likely
do not get copied to DMC; they're just an aid to reading this spec.

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

# Conceptual pages (How To)

<!-- 
All APIs have a page on DMC, some APIs or groups of APIs have an additional high level,
conceptual page (internally called a "how-to" page). This section can be used for that content.

For example, there are several Xaml controls for different forms of text input,
and then there's also a conceptual pages that discusses them collectively
(https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/text-controls)

Another way to use this section is as a draft of a blog post that introduces the new feature.
-->

# API Pages

<!--
Each level-2 section within this section is a type or member description. Since DMC has
a page for each type and member, each one of these sections will become a page.

Notes:
* The first line of each of these sections should become that first line on the DMC page,
  that then becomes the description you see in Intellisense.
* Each page can have description, examples, and remarks.
  Remarks are where the documentation calls out special considerations that the developer should be aware of.
* Add an "Other members" section after a type to give a for example a table
  of the other members and their Intellisense description.

Example specs:
* RGB (new class and members, uses Other Members section)
* ProtectedCursor (add a member to an existing class)

Tips:
* It can be helpful at the top of an API page (or after the Intellisense text) to add the API signature in C#
* It's not necessary to have a section for every member. Many members are obvious from their name and IDL.
-->

## MySample class

_One or two line description (text that should show up as Intellisense for the class). Include default value for properties_

_Fuller description, and/or remarks_

## MySample.PropertyOne property

_One or two line description (text that should show up as Intellisense for the member)_

## Other MySample members

_Members that don't need more than Intellisense text, so don't need the overhead of their own section. Include default value for properties._

| Name | Description
|-|-|
| PropertyTwo | The second property (defaults to -1) |
| MethodOne | The first method |

# API Details

<!-- 
The exact APIs, in MIDL3 format (https://docs.microsoft.com/en-us/uwp/midl-3/)
 -->

# Appendix

<!-- 
Anything else that you want to write down about implementation notes and for posterity,
but that isn't necessary to understand the purpose and usage of the API.

This or the Background section are a good place to describe alternative designs
and why they were rejected.
-->
