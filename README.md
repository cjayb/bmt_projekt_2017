# BMT projekt-uge, aug 2017

Installere [MNE — MNE 0.15.dev0 documentation](http://www.martinos.org/mne/dev/index.html) og følge [Tutorials](http://www.martinos.org/mne/stable/tutorials.html) & [Examples](http://www.martinos.org/mne/stable/auto_examples/index.html) til at analysere et visuel dataset.

## Installation

Se [Install Python and MNE-Python — MNE 0.15.dev0 documentation](http://www.martinos.org/mne/dev/install_mne_python.html).
### Anaconda

Python og alle mulige nyttige moduler fra [Continuum | Home](https://www.continuum.io)

Her er en [Youtube video](https://youtu.be/YJC6ldI3hWk) jeg ikke har set, men kunne være nyttig.

### jupyter notebook

Se [Project Jupyter](http://jupyter.org)

`conda install notebook`

To start a new notebook server, in a terminal, `cd` into a directory you want to start writing code in, then execute

`jupyter notebook`

which should open a tab in your default web browser.

In jupyter, to execute a 'code cell', hit Shift-Enter. See Google for more help on usage.

### mne-python

Dependencies-list slightly different from official mne-instructions; we'll just do:

`conda install scipy matplotlib scikit-learn notebook`

NB: without `mayavi`, `PySurfer` will not work. On 14 August, `mayavi` could not be installed due to conflicts.

We’ll want the development version. On Linux and OS X, this will do the trick:

```bash
$ git clone git://github.com/mne-tools/mne-python.git
$ cd mne-python
$ python setup.py develop
```

Windows is a little trickier…

### mne-python example datasets

Open a terminal, `cd` to a folder you want the example data to be downloaded, then execute `ipython` and copy-paste the following lines:

```python
import mne
print(mne.__version__)

from mne.datasets import sample

data_path = sample.data_path()
```

If you move the data to another location, you have to update (edit) the following file:

```bash
cat ~/.mne/mne-python.json  # this just prints the contents
```

### Optional install: Spyder

For a Matlab-like IDE, in a terminal, execute

`conda install spyder`

Then, in a terminal, start the IDE with

`spyder`

### A note on graphics/plots in jupyter notebook

To send the output of plotting commands to the notebook, place the following line __at the very top (first line)__ of the notebook (and execute it with Shift-Enter):

`%matplotlib inline`

If you instead want to have the plots appear in separate windows, execute this:

`%matplotlib qt`

__This is particularly relevant when doing interactive plotting, such as with `raw.plot()`__

## Dataset
* optages ved MEG tirsdag 9-12
* korte visuelle stimuli i 36 lokationer, mange gentagelser
	* lokationerne er beskrevet i en log-fil (radius- og theta-værdier)

## Preprocessing
The [Epoching and averaging (ERP/ERF) — MNE 0.14 documentation](http://www.martinos.org/mne/stable/auto_tutorials/plot_epoching_and_averaging.html) tutorial is a good starting point.
* extract all 36 event types from raw
	* use `event_id`-dict cleverly
		* `event_id = {‘Q1/locN’: 1, …`
		* where QN are the 4 quadrants  of the visual field and
		* locN can just be 1-36 from the log file
	* plot events
* bandbass filter raw data to range 1 - 40 Hz
* chop the data up into “Epochs” around the presentation time of each type of stimulus
	* use default rejection setting for eye blinks (EOG)
* decimate the `epochs`-object by a “suitable” ratio
	* let’s say: from sampled rate 1000 Hz to 250 Hz,  /i.e./ a factor of 4
	* [see bottom of this page](http://www.martinos.org/mne/stable/auto_tutorials/plot_artifacts_correction_filtering.html) for details
* save epochs to disk (fast loading from here on)
## Visualisation…

### Stimulus array

<img src="stimuli.jpeg" width=400>

### Plots of evoked responses

Use `evoked.plot_join()` suitably; do amplitudes and/or topographies vary in some consistent manner?

## Decoding

See [this page](https://martinos.org/mne/dev/auto_tutorials/plot_sensors_decoding.html#temporal-decoding) for a dev-version tutorial.

Let's try to decode up/down then left/right (binary classification), using the `SlidingEstimator`-example.

Another interesting thing to try could be identifying the quadrant. Multi-class decoding is a bit trickier, though, can we figure it out? The scoring function needs to be `roc_auc`, for sure.

A final potentially interesting thing to try is `GeneralizingEstimator` for the model that worked "best" in the more simple case above.

### Note on the `y`-variable (target classes)

For each classification model, `y` (a vector) needs to be created from the `events`, specifically the final column. Instead of values 101-136, we'll need to encode what we're trying to classify into (fewer) values of `y`.
