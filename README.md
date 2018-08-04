# Heart Rate Monitoring during exercise

## Overview

Photoplethysmography(PPG) is a popular method for heart rate (HR) monitoring which allows monitoring at the peripheral positions such as earlobes, fingertips or wrists. PPG sensors which are embedded in these wearable devices emit light to the skin and measure the changes of intensity of the light. The periodicity of these measurements corresponds to the cardiac rhythm, and thus, HR can be estimated from the PPG signal. PPG signal thus obtained has motional artefacts which corresponds to the noise. Using the inverse filtering and noise smoothing techniques like wiener filtering followed by linear regression for curve fitting.

## Databases

During data recording of around 5 minutes, each subject ran on a treadmill with changing speeds varying from 0-8-15-8-15-0 or 0-6-12-6-12-0 (in kmph). Its PPG, Accelerometer and ECG signals were simultaneously sampled at 125 Hz.

## Data Standards

HR is assumed to be in the range [30 BPM – 180 BPM], which corresponds to [0.5 Hz – 3 Hz] frequency range. Anything beyond this range is not taken into consideration using the inbuilt filter functions of MATLAB. The given data is sampled at 125 hertz which we downsaple it to 25 hertz. The fft of the signal does not take all the values at once, but the window length of 1000, which gives the frequency resolution of 0.37BPM in the frequency domain. The window length of wiener filter is set to 10. All this ranges are determined empirically and statistically.

## Wiener Filtering

Wiener filter is the core of the approach to remove the noise corresponding to the motional artefacts from the sampled signal to acquire noiseless original signal. It minimizes the mean square error between the sampled HR from PPG and the HR from ECG. It works on the fft of the sampled signal to remove the noise spectrum.
W(f)=Pyy(f)−Pnn(f)= Pxx(f) ;Pyy(f)=Pxx(f)+Pnn(f); Pyy(f) Pxx(f)+Pnn(f)

Pyy is the power spectrum corresponding to the noisy signal, Pxx is that corresponding to noiseless signal and Pnn corresponding to the noise. Overall filtering is carried out using two simultaneous filters, one with the normalized absolute value and other with the squared values. The outputs of both Wiener fIlters are normalized by their standard deviation and averaged to represent the final spectral envelope of the cleaned PPG signal.

## Phase Vocoder

HR cannot change abruptly in time-domain and thus the phase corresponding in the frequency-domain also would not change abruptly. The Rayleigh frequency is the minimum frequency that can be estimated which is limited by the size of the window of the analysed data (8s) and equals to, as in this case, (1/8)*30 = 3.75 BPM. The phase vocoder technique is employed to refine the initial HR estimate through the estimation of the instantaneous frequency as the rate of change of phase angle at time. Phase changes between two consecutive frames encode the deviation of the true frequency from the bin frequency. Thus the phases from the chosen peak in the magnitude spectrum from the current and previous frames can be used to refine the initial frequency estimation:
Φ=Θ2−Θ1+2Πk 2Π(t2−t1)
where Θ2 and Θ1 are the two phases from the current and previous frames, respectively; t2 and t1, are the time stamps of the two frames, here (t2 - t1) is a windows shift and is equal to 2s, k is a positive integer. The values of
Φ are computed for several n using equation, and the value of Φ which is closest to the initial peak is chosen.
Linear Regression
HR is not a random signal, but is dependent to some extent on the previous values of HR. Linear regression and polygon fitting techniques is applied to smoothen the resulting graphs.

## Results

The above algorithm has linearithmic time complexity for fft and linear time complexity for any of the remaining techniques used in the algorithm making the overall time complexity O(n logn), which is quite fast computationally.
Comparing the actual HR of the subject with the ones obtained using the above algorithm, we get the following worst case, best case and average case of deviation.

Performance of the algorithm over the given datasets:

| Test Case | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 |
| - | - | - | - | - | - | - | - | - | - | - | - | - |
| Error | 1.23 | 1.49 | 0.69 | 0.99 | 0.80 | 0.97 | 0.64 | 0.87 | 0.53 | 1.89 | 1.05 | 0.91 |

Mean Error = 1.00
Mean Deviation Error = 1.21
