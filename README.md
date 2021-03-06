# LPCNet

Low complexity WaveRNN-based speech coding by [Jean-Marc Valin](https://jmvalin.ca/)

# Introduction

Work in progress software for researching low CPU complexity algorithms for speech synthesis and compression by applying Linear Prediction techniques to WaveRNN. High quality speech can be synthesised on regular CPUs (around 1 GFLOP) with SIMD support (AVX, AVX2, NEON currently supported).

The BSD licensed software is written in C and Keras. For training, a GTX 1080 Ti or better is recommended.

This software is an open source starting point for WaveRNN-based speech coding.

# Quickstart

1. Set up a Keras system with GPU.

1. Generate training data:
   ```
   make dump_data
   ./dump_data -train input.s16 features.f32 pcm.s16
   ```
   where the first file contains 16 kHz 16-bit raw PCM audio (no header) and the other files are output files. This program makes several passes over the data with different filters to generate a large amount of training data.

1. Now that you have your files, train with:
   ```
   ./train_lpcnet.py features.f32 pcm.s16
   ```
   and it will generate a wavenet*.h5 file for each iteration. If it stops with a 
   "Failed to allocate RNN reserve space" message try reducing the *batch\_size* variable in train_wavenet_audio.py.

1. You can synthesise speech with Python and your GPU card:
   ```
   ./dump_data -test test_input.s16 test_features.f32
   ./test_lpcnet.py test_features.f32 test.s16
   ```
   Note the .h5 is hard coded in test_lpcnet.py, modify for your .h file.

1. Or with C on a CPU:
   First extract the model files nnet_data.h and nnet_data.c
   ```
   ./dump_lpcnet.py lpcnet15_384_10_G16_64.h5
   ```
   Then you can make the C synthesiser and try synthesising from a test feature file:
   ```
   make test_lpcnet
   ./dump_data -test test_input.s16 test_features.f32
   ./test_lpcnet test_features.f32 test.s16
   ```
 
# Speech Material for Training 

Suitable training material can be obtained from the [McGill University Telecommunications & Signal Processing Laboratory](http://www-mmsp.ece.mcgill.ca/Documents/Data/).  Download the ISO and extract the 16k-LP7 directory, the src/concat.sh script can be used to generate a headerless file of training samples.
```
cd 16k-LP7
sh ~/CELP/src/concat.sh
```

# Reading Further

1. [LPCNet: DSP-Boosted Neural Speech Synthesis](https://people.xiph.org/~jm/demo/lpcnet/)
1. Sample model files:
https://jmvalin.ca/misc_stuff/lpcnet_models/

