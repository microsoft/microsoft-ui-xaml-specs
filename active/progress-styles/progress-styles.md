# Progress Styles
These tables provides a comparison of current progress styles for the ProgressBar and ProgressRing controls in different modes and states.

## ProgressBar
ProgressBar has IsIndeterminate, Value, ShowPaused, and ShowError properties. 

### ProgressBar (Indeterminate)
IsIndeterminate is True

| Value | ShowPaused | ShowError | Image |
|:--| :-:|:-:|:-:| 
|  | False | False | ![](images/ProgressBar-indeterminate-active.PNG) |
|  | True | False | ![](images/ProgressBar-indeterminate-paused.PNG) |
|  | False | True | ![](images/ProgressBar-indeterminate-error.PNG) |
|  | True | True | ![](images/ProgressBar-indeterminate-paused-error.PNG) |
| 85 | False | False | ![](images/ProgressBar-indeterminate-value-active.PNG) |
| 85 | True | False | ![](images/ProgressBar-indeterminate-value-paused.PNG) |
| 85 | False | True | ![](images/ProgressBar-indeterminate-value-error.PNG) |
| 85 | True | True | ![](images/ProgressBar-indeterminate-value-paused-error.PNG) |


### ProgressBar (Determinate)
IsIndeterminate is False

| Value | ShowPaused | ShowError | Image |
|:--| :-:|:-:|:-:| 
|  | False | False | ![](images/ProgressBar-determinate-no-value.PNG) |
|  | True | False | ![](images/ProgressBar-determinate-no-value.PNG) |
|  | False | True | ![](images/ProgressBar-determinate-no-value.PNG) |
|  | True | True | ![](images/ProgressBar-determinate-no-value.PNG) |
| 85 | False | False | ![](images/ProgressBar-determinate-active.PNG) |
| 85 | True | False | ![](images/ProgressBar-determinate-paused.PNG) |
| 85 | False | True | ![](images/ProgressBar-determinate-error.PNG) |
| 85 | True | True | ![](images/ProgressBar-determinate-paused-error.PNG) |

## ProgressRing
ProgressRing has IsActive property, and does not currently have IsIndeterminate, Value, ShowPaused, or ShowError properties.
### ProgressRing (Indeterminate)
| IsActive | Image |
|:--| :-:|
| True | ![](images/ProgressRing-indeterminate.PNG) |
| False | ![](images/ProgressRing-indeterminate-not-active.PNG) |




