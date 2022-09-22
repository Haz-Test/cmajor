---
title: Piano
parent: Code Examples
has_children: false
nav_order: 1
---

# Piano

This example is found in the piano folder in the example patches section. There, you'll find `Piano.cmajor` and `Piano.cmajorpatch` as well as 5 audio samples.

<p align="center">
  <img src="https://haz-test.github.io/cmajor/docs/Assets/ExamplesPages/PianoFiles.png" title="Piano File Directory">
</p>

To start this example, open the `Piano.cmajor` file. We'll explain what's happening here first and how the externals are linked to the `Piano.cmajorpatch`.

## A Brief Overview

This is a MIDI Lo-Fi piano. It takes midi noteon & noteoff values to create a simplistic synth.

### `graph piano [[ main ]]`

The [[ main ]] annotation highlights the entry point for the program. This graph is used to connect the processor nodes with the input events and output stream. Inputs and output streams are declared first.

```cpp
output stream float out;
input event std::midi::Message midiIn;
input gain.volume;
```

This provides the MIDI data, a gain control and an output stream for audio data.

Graph nodes are created and initialised, before being connected with the connection keyword.

```cpp
node
{
envelope     = std::envelopes::FixedASR (0.0f, 0.3f);
attenuator   = std::levels::DynamicGain (float);
noteSelector = NoteSelector;
samplePlayer = std::audio_data::SamplePlayer (PianoSample::source);
}

connection
{
eventIn -> noteSelector;
noteSelector.content -> samplePlayer.content;
noteSelector.speedRatio -> samplePlayer.speedRatio;
samplePlayer -> attenuator.in;
eventIn -> envelope -> attenuator.gain;
attenuator -> out;
}
```

The connections provide an insight into the programs structure. The input event corresponds to the note selector, connecting to the sample player for a note, attenuating with dynamic gain from the user interface, outputting audio to the output stream.

### processor NoteSelector

This processor takes the note on event and triggers a piano sample of the correct octave to play. It utilises the `std::audio_data::SamplePlayer` function from the standard library.

### struct PianoSample

The PianoSample struct is used to store the audio data and root notes where it was pitched. The external keyword is used to access the five piano samples.

## `Piano.cmajorpatch`

To fully understand the external keyword, we will look at the `.cmajorpatch` file associated with the `.cmajor` file.

