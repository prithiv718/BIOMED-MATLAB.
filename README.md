# EXPERIMENT 1: ECG NOISE REMOVAL AND QRS DETECTION

## 1. Aim

To remove noise from an ECG signal and detect QRS complexes using MATLAB.

## 2. Input

One ECG waveform and sampling frequency.

**Example:** `ecg_sample.mat` containing:
- ECG signal variable: `ecg`
- Sampling frequency: `fs = 360 Hz`

## 3. Theory / Principle

ECG contains characteristic P, QRS and T waves and may be contaminated by baseline wander, power-line interference and high-frequency noise. A band-pass filter is used to emphasize QRS energy.

Differentiation, squaring and moving-window integration further enhance the QRS complexes before peak detection.

## 4. Procedure

1. Load the ECG and inspect its duration and amplitude.
2. Remove the DC component.
3. Design and apply a 5–15 Hz Butterworth band-pass filter.
4. Differentiate and square the filtered ECG.
5. Apply a 150 ms moving-window integration.
6. Detect QRS candidates using minimum height and minimum distance.
7. Calculate RR intervals and estimated heart rate.

## 5. MATLAB Program

```matlab
%% EXPERIMENT 1 - ECG NOISE REMOVAL AND QRS DETECTION
clear; close all; clc;

%% INPUT PARAMETERS
fs = 360;                                      % Sampling frequency (Hz)
filename = 'ecg_sample.mat';

%% LOAD THE ECG
S = load(filename);

if isfield(S,'ecg')
     ecg = S.ecg;
elseif isfield(S,'ECG')
     ecg = S.ECG;
else
     error('ECG variable not found. Check the input file.');
end

ecg = double(ecg(:));
t = (0:length(ecg)-1)/fs;

fprintf('Samples = %d\n',length(ecg));
fprintf('Duration = %.2f seconds\n',t(end));

%% FIGURE 1: RAW ECG
figure('Name','Raw ECG');
plot(t,ecg,'k');
grid on;
xlabel('Time (s)'); ylabel('Amplitude');
title('Raw ECG Signal');

%% PREPROCESSING
ecg0 = ecg - mean(ecg);

%% BAND-PASS FILTER
lowCut = 5;
highCut = 15;
filterOrder = 4;

[b,a] = butter(filterOrder,[lowCut highCut]/(fs/2),'bandpass');
ecgFiltered = filtfilt(b,a,ecg0);

%% FIGURE 2: FILTERED ECG
figure('Name','Filtered ECG');
plot(t,ecgFiltered);
grid on;
xlabel('Time (s)'); ylabel('Filtered amplitude');
title('ECG after Band-Pass Filtering');

%% QRS ENHANCEMENT
dECG = [0;diff(ecgFiltered)];
squared = dECG.^2;

integrationWindow = round(0.150*fs);
integrated = movmean(squared,integrationWindow);

%% FIGURE 3: QRS ENHANCEMENT
figure('Name','QRS Enhancement');
plot(t,integrated);
grid on;
xlabel('Time (s)'); ylabel('Integrated energy');
title('Moving-Window Integrated ECG');

%% PEAK DETECTION
threshold = 0.35*max(integrated);
minimumDistance = round(0.25*fs);

[~,qrsLocs] = findpeaks(integrated,...
    'MinPeakHeight',threshold,...
    'MinPeakDistance',minimumDistance);

%% FIGURE 4: QRS DETECTION
figure('Name','Detected QRS');
plot(t,ecgFiltered,'b'); hold on;
plot(t(qrsLocs),ecgFiltered(qrsLocs),'ro',...
    'MarkerFaceColor','r');
grid on;
xlabel('Time (s)'); ylabel('ECG amplitude');
title('Filtered ECG with Detected QRS Candidates');
legend('Filtered ECG','QRS candidates');

%% HEART RATE
RR = diff(qrsLocs)/fs;

if isempty(RR)
     HR = NaN;
else
     HR = 60/mean(RR);
end

fprintf('\n===== OBSERVATIONS =====\n');
fprintf('Sampling frequency = %.2f Hz\n',fs);
fprintf('Filter range = %.2f to %.2f Hz\n',lowCut,highCut);
fprintf('Detected QRS complexes = %d\n',length(qrsLocs));
fprintf('Mean RR interval = %.4f s\n',mean(RR,'omitnan'));
fprintf('Estimated heart rate = %.2f bpm\n',HR);
```

## 6. Expected Figure / Output

### Figure 1: Raw ECG Signal
<img width="1616" height="762" alt="image" src="https://github.com/user-attachments/assets/087be14a-e80a-476b-8799-77d4e99d3218" />


### Figure 2: ECG after Band-Pass Filtering
<img width="1532" height="772" alt="image" src="https://github.com/user-attachments/assets/552414a9-ac60-4f07-96f8-afbdc13e56c9" />


### Figure 3: Moving-Window Integrated ECG
<img width="1495" height="771" alt="image" src="https://github.com/user-attachments/assets/a135ae9d-3b03-41dc-b42a-95a6ba86b8f1" />


### Figure 4: Filtered ECG with Detected QRS Candidates
<img width="1557" height="742" alt="image" src="https://github.com/user-attachments/assets/b2c52ae9-fe0b-4e84-ae33-691b0297078f" />



### Expected Observations

Record the following five observations from your MATLAB execution:

| No. | Observation | Student Observation |
|---|---|---|
| 1 | Number of samples | 3600 |
| 2 | Signal duration | 10.00 seconds |
| 3 | Filter frequency range | 360 Hz |
| 4 | Detected QRS complexes | 5-12 Hz|
| 5 | Estimated heart rate / mean RR interval | 72.38bpm/0.8301 seconds |

**Expected output interpretation:** The final plot should show a cleaner ECG waveform with markers near the QRS complexes. Heart rate is calculated from the detected RR intervals.

## 7. Result

The experiment was performed successfully using MATLAB. The input was processed according to the stated method and the expected biomedical output was obtained.

**Final result / value:** 72.28 bpm

**Conclusion:** ECG noise removal and QRS detection were successfully performed using MATLAB
