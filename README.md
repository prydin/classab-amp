# 50W Class AB Amplififier

![3D rendering](images/3d.png)

## Disclaimer
This amplifier has not yet been built and tested. It performs beautifully in SPICE, but has yet to be tested in real life. You are
welcome to use the design, but be aware that it may not perform well or work at all.

## Background
This is my take on a classic Class AB amplifier. I did it for two reasons: I wanted to learn how to design and build a Class AB amplifier and
I needed better sound than crappy Bluetooth speakers in my office. I settled for 50W because that's all the power I need for my application
and the voltages involved are a lot more forgiving than what you'd have with a 300W monster. 

Some people have said it looks a lot like Doug Self's "Blameless Amplifier". I take that as a compliment, but the fact is that I didn't even
know how Self was when I started building it. The design is just based on various ideas from other amplifiers that I put together and try to
make it my own.

## Overall design
The design is a simple single differential stage amplifier with a single transistor VAS driving two Darlington pair for the final current 
gain stage. 

### Input stage
After the customary DC-blocking capacitor and RF filter, the signal is fed into a long tailed pair differential amplifier that's enhanced with 
an active current sink and current mirror loads. The active sink and load helped improve both distortion and frequency response. The bias 
current of the LTP is set to approximately 240uA. The LTP and its current mirrors are mounted on a separate copper island for maximum heat
condudctivity between the transistors. The feedback is fed through a 22k/1k voltage divider, yielding about 23x amplification. This is a 
conservative estimate and may have to be adjusted when testing the physical build.

### Voltage amplification/drive stage
I went for a very simple single-sided design here. I've gotten many suggestions that I should try a dual differential design instead, but 
the current design seems good enough, so I stuck with it. The VAS has a 50p compensation capacitor which appears to be enough to provide 
ample phase margin.

### Output stage biasing
The output stage is biased using a servo transistor driven by a constant current sink. The BD139 won the servo as chosen just because it's
easy to mount on a heat sink together with the power components for good heat conductivity between components. A much less powerful 
transistor works too, but would have been harder to attach to the heat sink.

### Output stage
Here I am using some very common components. BD139/BD140 transistors as the first stage of the Darlington pair and MJL3281/MLJ1302 for
the final stage. The final transistors can handle 200W, so they are definitely a bit overkill. However, they performed better than
TIP35/TIP36 in the simulator and headroom is never a bad thing to have. Before the signal reaches the speaker, it is passed through
a snubber and a Zobel network to further improve stability.

### Short circuit projection 
To protect against short circuits, overcurrent and catastrophic DC output, two transistors are fitted that measure the curremt across
the degeneration resistors and shut off the base current to the final Darlington stage when the current reaches approximately 4A. 

### Power improvements
Altough this hasn't been tested or thorouhgly analyzed, it should be possible to increase the rail voltages to +-40V, which yields about
160W according to simultions. For this to work, the gain needs to be made higher by increasing the value of the feedback resistor. The
voltage divider for the short circuit protection also needs to be adjusted to allow more curent. Finally, the biasing may need to be adjusted
as well. 

# Schematic
![Schematic](images/schematic.png)

## SPICE simulations

All simulations are done with an 8 ohm resistive dummy load, 1V input at 1kHz.

### THD
At a fairly moderate quiescent current of 15mA, I get a THD of 0.025%. A THD below 0.1% is considered to be inaudible, so these numbers
are fine by me. I know there are audiophiles who insist on THD numbers <0.0001%, but I'm not one of them. Inaudible is just fine by me.

I've gotten suggestions that adding an emitter follower to the VAS might improve THD. I might try that, but I have a feeling there will 
be more pressing matters than getting the THD even more inaudible. 

```Fourier components of V(out)
DC component:-0.00709555

Harmonic	Frequency	 Fourier 	Normalized	 Phase  	Normalized
 Number 	  [Hz]   	Component	 Component	[degree]	Phase [deg]
    1   	 1.000e+3	 2.086e+1	 1.000e+0	   90.68°	    0.00°
    2   	 2.000e+3	 3.254e-3	 1.560e-4	  -81.18°	 -171.86°
    3   	 3.000e+3	 1.410e-3	 6.760e-5	   15.46°	  -75.22°
    4   	 4.000e+3	 1.064e-3	 5.098e-5	  101.85°	   11.17°
    5   	 5.000e+3	 1.038e-3	 4.975e-5	 -151.33°	 -242.01°
    6   	 6.000e+3	 2.289e-4	 1.097e-5	  -96.94°	 -187.62°
    7   	 7.000e+3	 2.021e-4	 9.686e-6	 -154.64°	 -245.32°
    8   	 8.000e+3	 1.447e-4	 6.935e-6	  106.76°	   16.08°
    9   	 9.000e+3	 5.116e-4	 2.452e-5	 -167.01°	 -257.69°
   10   	 1.000e+4	 1.460e-4	 6.996e-6	 -115.50°	 -206.18°
Partial Harmonic Distortion: 0.018677%
Total Harmonic Distortion:   0.025368%
```

![FFT](images/fft.png)

### AC Analysis
The amplifier has a 3dB bandwidth of about 72kHz, which is by no means in the audiophile range, but it keeps any poles far away from the audible 
range to make both the amplitude and phase response vritually flat where it matters. I am of the opinion that nothing >20kHz has any impact on
the listening experience.

![Bode plot](images/bode.png)

