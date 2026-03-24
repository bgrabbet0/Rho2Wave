================================================================================
                    Rho2Wave — TD-DFT Sonification Tool
                              README
================================================================================

Rho2Wave converts Time-Dependent Density Functional Theory (TD-DFT) output
into stereo audio and spectrograms. It maps quantum-chemical excited-state
properties onto audible parameters, encoding molecular chirality into the
stereo field.


SUPPORTED INPUT (for the moment...)
---------------
- Gaussian .log files
- ORCA .out files
Format is auto-detected. Both file types can be mixed in a single batch.


WHAT THE PROGRAM PRODUCES
-------------------------
For each input file, Rho2Wave creates a subfolder containing:

  <molecule>.wav              Stereo audio file
  <molecule>_spectrogram2d.png   2D heatmap (Left/Right side by side)
  <molecule>_spectrogram3d.png   3D surface plot (Left/Right side by side)
  <molecule>_summary.txt         CSV with states, energies, R values, L/R weights


HOW TD-DFT DATA MAPS TO SOUND
------------------------------
  Excitation energy  -->  Pitch (frequency)
  Oscillator strength -->  Loudness (amplitude)
  Dipole orientation  -->  Stereo position (left/right panning)
  Rotatory strength   -->  Chirality encoding (L/R bias + phase offset)

Each excited state becomes an audible sine tone. The full sonification is the
sum of all tones shaped by an ADSR envelope.

For chiral molecules, enantiomers produce mirrored stereo images: tones that
are louder in the left ear for the (+)-enantiomer become louder in the right
ear for the (-)-enantiomer.


WORKFLOW
--------
1. Run a TD-DFT calculation (Gaussian or ORCA) on your molecule.
   
2. Open Rho2Wave and drag your .log or .out file(s) into the file area.
   Multiple files can be processed in one batch.

3. Adjust parameters if desired (defaults work well for most molecules).

4. Click Run. Progress bars show the status.

5. Click "Open output folder" to access the WAV, PNGs, and summary or navigate to initial directory.

6. Listen to the WAV through headphones and examine the spectrograms.


GUI LAYOUT
----------
File area (top)       Drag-and-drop zone for input files. Supports .log and
                      .out. Select files and press Delete to remove them.

Output root           Optional directory override. If empty, outputs go next
                      to the input file.

Outputs checkboxes    Toggle WAV, 2D PNG, 3D PNG, and Summary TXT generation.

Parameter tabs:

  Audio / Mapping     Core settings in two columns.
    fmin / fmax       Frequency range in Hz (default 100-1000).
    duration          WAV length in seconds (default 5).
    mapping           Linear or logarithmic pitch mapping.
    stereo            Dipole projection plane: cpl, xz, xy, yz, or mono.
    chirality mode    off / amp / phase / hybrid (default hybrid).
    chirality gauge   length or velocity rotatory strength.
    Advanced section  (click to expand)
      sample rate     Audio sample rate in Hz (default 44100).
      decay           Envelope release time in seconds.
      amplitude floor Minimum amplitude for weak transitions.
      chirality alpha Amplitude bias sensitivity.
      chirality beta  Phase offset in radians.
      R ref percentile  Normalisation reference for |R|.
      R gamma         Power-law contrast exponent.
      chiral gain     Linear scaling of normalised R.
      stereo mix      Crossfade: 0 = dipole panning, 1 = chirality only.
      side gain       Mid/Side amplification of L-R difference.

  Spectrogram         Controls for the STFT analysis.
    percentile min    dB floor as percentile.
    percentile max    dB ceiling as percentile.
    nfft              FFT size (frequency resolution).
    noverlap          STFT overlap in samples.
    downsample bins   Grid cap for 3D surface rendering.

  PNGs                Visual output settings.
    colormap          inferno, viridis, magma, cividis, plasma.
    3D / 2D dpi       Resolution of spectrogram images.

Preset buttons        Save and load parameter sets as JSON files.
Run / Pause / Cancel  Batch processing controls.
Show log              Expand the log panel to see per-file status messages.
Open output folder    Opens the most recent output directory.


CHIRALITY MODES
---------------
off       No chirality encoding. L/R balance from dipole orientation only.
          Enantiomers sound identical.

amp       Rotatory strength biases L/R volume. Positive R shifts left,
          negative R shifts right.

phase     Rotatory strength sign introduces a phase offset between L/R.
          Creates spatial displacement audible through headphones.

hybrid    Both amplitude and phase encoding (strongest effect).
          Default and recommended setting.


READING THE SPECTROGRAMS
------------------------
Each spectrogram shows Left and Right channels side by side.

- Horizontal axis: time (seconds).
- Vertical axis: mapped frequency (Hz).
- Colour: signal intensity in dB (brighter = louder).

Horizontal bands correspond to excited states. Band brightness reflects
oscillator strength. L/R brightness differences reveal chirality: a band
that is bright on the left and dim on the right has positive R, and vice
versa. Enantiomers show mirrored L/R patterns.


READING THE SUMMARY CSV
------------------------
Columns: State, E(eV), wavelength(nm), f, R(gauge), L_weight, R_weight

L_weight and R_weight show how each state's energy is distributed between
the stereo channels. Values near 0.5/0.5 indicate weak or no chirality
encoding. Values near 0.0/1.0 indicate strong lateralisation driven by
a large rotatory strength.


PRESETS
-------
Save preset: exports all current parameters as a JSON file.
Load preset: restores parameters from a previously saved JSON.
Presets ensure reproducible results across different machines and sessions.


TIPS
----
- !!!USE HEADPHONES!!!
- Start with default parameters. Adjust fmin/fmax if the pitch range feels
  too narrow or too wide for your molecule.
- For chiral molecules, process both enantiomers in one batch and compare
  the spectrograms side by side.
- Toggle chirality mode from off to hybrid to hear what chirality adds.
- The amplitude floor parameter (default 0.02) keeps weak transitions
  audible. Set to 0 to hear only the dominant states.
- Increase duration for molecules with many closely spaced states to give
  your ear time to resolve the individual tones.


REQUIREMENTS (if running from source, standalone exe works and is recommended)
--------------------------------------
Python 3.10+
PySide6
numpy
matplotlib
Pillow

Install with: pip install PySide6 numpy matplotlib Pillow


================================================================================
