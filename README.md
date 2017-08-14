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

### mne-python
We’ll want the development version. On Linux and OS X, this will do the trick:
```bash
$ git clone git://github.com/mne-tools/mne-python.git
$ cd mne-python
$ python setup.py develop
```
Windows is a little trickier…
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
