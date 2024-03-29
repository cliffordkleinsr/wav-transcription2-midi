# Newer PyTorch Implementation of Onsets and Frames Adapted Fom `Jongwook onsets-and-frames`

This is a [PyTorch](https://pytorch.org/) implementation of Google's [Onsets and Frames](https://magenta.tensorflow.org/onsets-frames) model, using the [Maestro dataset](https://magenta.tensorflow.org/datasets/maestro) for training and the Disklavier portion of the [MAPS database](http://www.tsi.telecom-paristech.fr/aao/en/2010/07/08/maps-database-a-piano-database-for-multipitch-estimation-and-automatic-transcription-of-music/) for testing. And further adapted from [Jongwook onsets-and-frames](https://github.com/jongwook/onsets-and-frames/tree/master) to accomodate more recent pytorch implementations specifically `torch-2.2.0+cu12.3`

## Instructions

This project is quite resource-intensive; 32 GB or larger system memory and 8 GB or larger GPU memory is recommended. 

### Downloading Dataset

To download the Maestro dataset use the link [provided](https://magenta.tensorflow.org/datasets/maestro). Bear in mind that this dataset is  **~100Gb**

### Transcribing
To use the Model provided from `Jongwook onsets-and-frames` you need to modify one torch source file specifically `torch.nn.modules.rnn.py` by replacing it with the file at the root of the repositort aliased `rnn.py`
Download and Place the [model](https://drive.google.com/file/d/1Mj2Em07Lvl3mvDQCCxOYHjPiB-S0WGT1/view) at the root of the repository in your local directory.
Then you can run the command:
```python
python transcribe.py -i 'folder-with-wavs'
```

### Training

All package requirements are contained in `requirements.txt`. To train the model, run:

```bash
pip install -r requirements.txt
python train.py
```

`train.py` is written using [sacred](https://sacred.readthedocs.io/), and accepts configuration options such as:

```bash
python train.py with logdir=runs/model iterations=1000000
```

Trained models will be saved in the specified `logdir`, otherwise at a timestamped directory under `runs/`.

### Testing

To evaluate the trained model using the MAPS database, run the following command to calculate the note and frame metrics:

```bash
python evaluate.py runs/model/model-100000.pt
```

Specifying `--save-path` will output the transcribed MIDI file along with the piano roll images:

```bash
python evaluate.py runs/model/model-100000.pt --save-path output/
```

In order to test on the Maestro dataset's test split instead of the MAPS database, run:

```bash
python evaluate.py runs/model/model-100000.pt Maestro test
```

## Implementation Details

This implementation contains a few of the additional improvements on the model that were reported in the Maestro paper, including:

* Offset head
* Increased model capacity, making it 26M parameters by default
* Gradient stopping of inter-stack connections
* L2 Gradient clipping of each parameter at 3
* Using the HTK mel frequencies

Meanwhile, this implementation does not include the following features:

* Variable-length input sequences that slices at silence or zero crossings
* Harmonically decaying weights on the frame loss

Despite these, this implementation is able to achieve a comparable performance to what is reported on the Maestro paper as the performance without data augmentation.


