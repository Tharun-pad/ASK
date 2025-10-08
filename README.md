# ASK
# Aim
Write a simple Python program for the modulation and demodulation of ASK and FSK.
# Tools required
# Program
## ASK
```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import butter, lfilter
# Butterworth low-pass filter for demodulation
def butter_lowpass_filter(data, cutoff, fs, order=5):
  nyquist = 0.5 * fs
  normal_cutoff = cutoff / nyquist
  b, a = butter(order, normal_cutoff, btype='low', analog=False)
  return lfilter(b, a, data)
# Parameters
fs = 1000 
f_carrier = 50 
bit_rate = 10 
T = 1 
t = np.linspace(0, T, int(fs * T), endpoint=False)
# Message signal (binary data)
bits = np.random.randint(0, 2, bit_rate)
bit_duration = fs // bit_rate
message_signal = np.repeat(bits, bit_duration)
# Carrier signal
carrier = np.sin(2 * np.pi * f_carrier * t)
# ASK Modulation
ask_signal = message_signal * carrier
# ASK Demodulation
demodulated = ask_signal * carrier 
filtered_signal = butter_lowpass_filter(demodulated, f_carrier, fs)
decoded_bits = (filtered_signal[::bit_duration] > 0.25).astype(int)
# Plotting
plt.figure(figsize=(12, 8))
plt.subplot(4, 1, 1)
plt.plot(t, message_signal, label='Message Signal (Binary)', color='b')
plt.title('Message Signal')
plt.grid(True)
plt.subplot(4, 1, 2)
plt.plot(t, carrier, label='Carrier Signal', color='g')
plt.title('Carrier Signal')
plt.grid(True)
plt.subplot(4, 1, 3)
plt.plot(t, ask_signal, label='ASK Modulated Signal', color='r')
plt.title('ASK Modulated Signal')
plt.grid(True)
plt.subplot(4, 1, 4)
plt.step(np.arange(len(decoded_bits)), decoded_bits, label='Decoded Bits', color='r', marker='x')
plt.title('Decoded Bits')
plt.tight_layout()
plt.show()
```

## FSK
```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import butter, lfilter

# Butterworth low-pass filter for demodulation
def butter_lowpass_filter(data, cutoff, fs, order=5):
    nyquist = 0.5 * fs
    normal_cutoff = cutoff / nyquist
    b, a = butter(order, normal_cutoff, btype='low', analog=False)
    return lfilter(b, a, data)

# Parameters
fs = 1000
f0 = 30
f1 = 70
bit_rate = 10

T = 1
t = np.linspace(0, T, int(fs * T), endpoint=False)

# Message signal (binary data)
bits = np.random.randint(0, 2, bit_rate)
bit_duration = fs // bit_rate
message_signal = np.repeat(bits, bit_duration)

# FSK Modulation
fsk_signal = np.zeros(len(t))
start = 0
for i in range(len(bits)):
    end = start + bit_duration
    if bits[i] == 1:
        fsk_signal[start:end] = np.sin(2 * np.pi * f1 * t[start:end])
    else:
        fsk_signal[start:end] = np.sin(2 * np.pi * f0 * t[start:end])
    start = end

# FSK Demodulation
ref_f0 = np.sin(2 * np.pi * f0 * t)
ref_f1 = np.sin(2 * np.pi * f1 * t)

# Cutoff frequency for low-pass filter
f2 = 100

corr_f0 = butter_lowpass_filter(fsk_signal * ref_f0, f2, fs)
corr_f1 = butter_lowpass_filter(fsk_signal * ref_f1, f2, fs)

decoded_bits = []
for i in range(0, len(corr_f0), bit_duration):
    start = i
    end = i + bit_duration
    if end > len(corr_f0):
        break
    energy_f0 = np.sum(corr_f0[start:end]**2)
    energy_f1 = np.sum(corr_f1[start:end]**2)
    decoded_bits.append(1 if energy_f1 > energy_f0 else 0)

# Plotting
plt.figure(figsize=(12, 12))

plt.subplot(6, 1, 1)
plt.plot(t, message_signal, label='Message Signal (Binary)', color='b')
plt.title('Message Signal')
plt.grid(True)
plt.legend()

plt.subplot(6, 1, 2)
plt.plot(t, fsk_signal, label='FSK Modulated Signal', color='r')
plt.title('FSK Modulated Signal')
plt.grid(True)
plt.legend()

plt.subplot(6, 1, 3)
plt.plot(t, ref_f0, label='Reference Signal for bit = 0 (f0)', color='g')
plt.title('Carrier Signal for bit = 0 (f0)')
plt.grid(True)
plt.legend()

plt.subplot(6, 1, 4)
plt.plot(t, ref_f1, label='Reference Signal for bit = 1 (f1)', color='orange')
plt.title('Carrier Signal for bit = 1 (f1)')
plt.grid(True)
plt.legend()

plt.subplot(6, 1, 5)
plt.step(np.arange(len(decoded_bits)), decoded_bits, label='Final Demodulated Signal', color='k')
plt.title('Final Demodulated Signal')
plt.grid(True)
plt.legend()

plt.tight_layout()
plt.show()
```
# Output Waveform
## ASK
<img width="1190" height="790" alt="image" src="https://github.com/user-attachments/assets/593498c7-c825-4785-ade5-01f53eb4fec0" />
## FSK
<img width="1201" height="1012" alt="image" src="https://github.com/user-attachments/assets/ace18357-9950-4f8f-ba59-16f7978ee2f2" />

# Results

Thus, the Amplitude Shift Keying (ASK) technique was successfully implemented and visualized using Python.

# Hardware experiment output waveform.
