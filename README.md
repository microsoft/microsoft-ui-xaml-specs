# Windows UI Library (WinUI) Specs

This repository contains archived and in-progress spec documents for current and proposed APIs in the Windows UI Library (WinUI):  
https://github.com/Microsoft/microsoft-ui-xaml

Documents in this repo are used as part of Microsoft's WinUI spec review process for features in development and are not intended to be up-to-date developer documentation.

For up-to-date developer documentation on WinUI and related APIs please see:  
https://docs.microsoft.com

For product issues, bugs, and feature requests please use the main WinUI repo:  
https://github.com/Microsoft/microsoft-ui-xaml

## Contributing

This repo is maintained by the Microsoft WinUI team.

### Please DO:

* provide feedback on **active pull requests**, which represent new features that are being reviewed
* provide feedback on spec documents in the ```active``` folder, which represent new features that are being worked on for a future release

### Please DO NOT:

* provide feedback on spec documents in the ```archive``` folder since those features have already been released. Please contribute feedback or suggested changes to docs.microsoft.com instead.
* send use unsolicited PRs that add new specs. To propose a new feature, please follow the WinUI contribution process [described here](https://github.com/Microsoft/microsoft-ui-xaml/blob/master/docs/feature_proposal_process.md) which starts with opening an issue in the WinUI repo:  
https://github.com/Microsoft/microsoft-ui-xaml
* rely on this repo for final developer documentation. Please see docs.microsoft.com.

### Spec workflow

For WinUI team members, the workflow to author specs is:

1. Ensure your proposal is approved and in plan.

2. Create a new branch: ```/user/<username>/<feature>```

3. Create a new folder for your spec under the ```active``` folder: ```/active/<feature>```

4. Author your spec using the [spec template](/spec_template.md).  
Please use relative links for images or other assets in the folder.

5. When your spec is ready for review, open a new pull request to merge your spec to the ```master``` branch.  
Update your proposal issue with a link to the spec PR

6. When your spec is complete and signed off, merge the PR.

7. Use your completed spec as the basis for docs.microsoft.com articles and [Controls Gallery](https://github.com/Microsoft/Xaml-Controls-Gallery) samples.

8. Once the feature is documented, add the following header to your spec and move the spec from ```/active``` to ```/archive/<release>```:
   > This spec is archived and may not be up to date.  
For up-to-date developer documentation please see: ```<insert link>```

## Legal Notices

Microsoft and any contributors grant you a license to the Microsoft documentation and other content
in this repository under the [Creative Commons Attribution 4.0 International Public License](https://creativecommons.org/licenses/by/4.0/legalcode),
see the [LICENSE](LICENSE) file, and grant you a license to any code in the repository under the [MIT License](https://opensource.org/licenses/MIT), see the
[LICENSE-CODE](LICENSE-CODE) file.

Microsoft, Windows, Microsoft Azure and/or other Microsoft products and services referenced in the documentation
may be either trademarks or registered trademarks of Microsoft in the United States and/or other countries.
The licenses for this project do not grant you rights to use any Microsoft names, logos, or trademarks.
Microsoft's general trademark guidelines can be found at http://go.microsoft.com/fwlink/?LinkID=254653.

Privacy information can be found at https://privacy.microsoft.com/en-us/

Microsoft and any contributors reserve all others rights, whether under their respective copyrights, patents,
or trademarks, whether by implication, estoppel or otherwise.
