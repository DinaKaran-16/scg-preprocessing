SCG Beat Segmentation Pipeline (PhysioNet CEBS)

A small, reproducible pipeline that turns raw seismocardiogram (SCG) and ECG recordings into clean, R-peak-aligned beat windows ready for analysis or machine learning.

Built with Python, SciPy, NumPy and wfdb, using the open PhysioNet Combined measurement of ECG, Breathing and Seismocardiograms dataset.

Example segmented SCG beat

<!-- Save a beat plot from the notebook as docs/example_beat.png, ideally SCG with the ECG window below it -->
What it does
Loads multi-channel recordings (ECG Lead II and SCG, sampled at 5,000 Hz) with wfdb.
Checks the spectrum of the SCG with a Welch power spectral density and confirms the dominant peak falls in the expected heart-rate band (30-180 BPM).
Filters the SCG with a zero-phase Butterworth bandpass (4th order, 0.5-32 Hz, sosfiltfilt).
Detects R-peaks on the inverted Lead II ECG, using a minimum-distance constraint derived from the allowed heart-rate range.
Segments one SCG window per beat: 5 ms before to 450 ms after each R-peak, which is 2,275 samples at 5 kHz.
Cross-checks each window by plotting the same time range on the ECG, so misaligned beats are visible.
Exports all beats as a NumPy array.

Output from the 20 healthy subjects (b001-b020): 7,080 beats, shape (7080, 2275), saved as scg_beats.npy.

Quick start
bash
git clone https://github.com/DinaKaran-16/[repo-name].git
cd [repo-name]
pip install -r requirements.txt
Download the dataset from PhysioNet: Combined measurement of ECG, Breathing and Seismocardiograms (search the title).
Open notebooks/scg_segmentation.ipynb and set DATASET_DIR to the dataset's files folder.
Run all cells. Beats are written to scg_beats.npy.

requirements.txt:

wfdb
numpy
scipy
matplotlib
Key parameters
Parameter	Value	Why
Sampling rate	5,000 Hz	Native rate of the dataset
SCG bandpass	0.5-32 Hz, Butterworth order 4	Removes baseline drift and high-frequency noise; zero-phase so timing is preserved
R-peak source	Inverted ECG Lead II	Peak polarity in this dataset
Heart-rate limits	40-110 BPM (resting subjects)	Sets minimum peak spacing
Beat window	-5 ms to +450 ms around R	Covers the systolic and early diastolic SCG events
Known limitations

I list these because they matter when you reuse the pipeline on your own data.

R-peaks are detected on the raw (unfiltered) ECG using a distance constraint only. There is no amplitude or prominence threshold, so very noisy recordings can produce false peaks.
No per-beat quality filtering yet. Beats with motion artifacts or misdetected R-peaks are kept.
Healthy, resting subjects only. Heart-rate limits and window length are tuned for this population, not for exercise or pathological data.
Validation is visual: the same window is plotted on the ECG for cross-checking. There is no automated alignment metric yet.
