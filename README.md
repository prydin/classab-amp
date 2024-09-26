# 50W Class AB Amplififier

![3D rendering](images/3d.png)

## Disclaimer
This amplifier has been built and testad and, at least subjectively speaking, sounds great. However, I'm not a professional designer
and this is my first "serious" attempt at amplifier design. I invite you to build this amplifier if you like, but don't expect it to
perform like a professionally designed high-end amplifier. That said, I think it sounds pretty darn good. 

## Background
This is my take on a classic Class AB amplifier. I did it for two reasons: I wanted to learn how to design and build a Class AB amplifier and
I needed better sound than crappy Bluetooth speakers in my office. I settled for 50W because that's all the power I need for my application
and the voltages involved are a lot more forgiving than what you'd have with a 300W monster. 

Some people have said it looks a lot like Doug Self's "Blameless Amplifier". I take that as a compliment, but the fact is that I didn't even
know how Self was when I started building it. The design is just based on various ideas from other amplifiers that I put together and tried to
make it my own.

## Repository structure
* `kicad` - Schematic and PCB layout in KiCAD format
*  `spice` - LTSpice simulations

## Overall design
The design is a simple single differential stage amplifier with a single transistor VAS driving two Darlington pairs for the final current 
gain stage. 

### Input stage
After the customary DC-blocking capacitor and RF filter, the signal is fed into a long tailed pair differential amplifier that's enhanced to 
an active current sink and current mirror loads. The active sink and load helps improve both distortion and frequency response. The bias 
current of the LTP is set to approximately 600uA. The LTP and its current mirrors are mounted on a separate copper island for maximum heat
condudctivity between the transistors. The feedback is fed through a 22k/1k voltage divider, yielding about 23x amplification. This is a 
conservative estimate and may have to be adjusted when testing the physical build. The active current sink and load seem to offer a robust
power supply rejection. 

### Voltage amplification/drive stage
I went for a very simple single-sided design here. I've gotten many suggestions that I should try a dual differential design instead, but 
the current design seems good enough, so I stuck with it. The VAS has a 47p compensation capacitor which appears to be enough to provide 
ample phase margin. After some testing, I also found that a 4.7nF Miller capacitance on the VAS current sink greatly helped to maintain 
stability.

### Output stage biasing
The output stage is biased using a servo transistor driven by a constant current sink. The BD139 for the servo as chosen just because it's
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

### Output coil
The output coil is realized simply by winding 10-12 turns of 16 AWG wire around R21. See the photo below!
![Coil](images/coil.png)

### Power improvements
Altough this hasn't been tested or thorouhgly analyzed, it should be possible to increase the rail voltages to +-40V, which yields about
160W according to simultions. For this to work, the gain needs to be made higher by increasing the value of the feedback resistor. The
voltage divider for the short circuit protection also needs to be adjusted to allow more curent. Finally, the biasing may need to be adjusted
as well. 

### Stability
The amplifier is very stable in simulations, but my original design suffered from some 800-900kHz 200mV p/p oscillations at idle. After 
some troubleshooting, it turned out that the bias circuitry had some local stability issues. To remedy this, a 4.7nF Miller capacitance was
added to Q8. I also found that a larger capacitance (~50nF) across collector and emitter on Q8 helped with stability. The CE capacitance was
later omitted due to bandwidth concerns. 

### Power supply
The amplifier is intended to be powered by an unregulated linear power supply like the one supplied in this repository. Each amplifier board
is fitted with 4700uF on each rail, per channel and the rectifier board has an additional 4700uF on each rail. In simulations, measurements 
and listening tests, this seems sufficient. However, the rectifier board has room for two additional capacitors if so desired.

In my build, I'm using a 2x25V toroidial transformer. Where I live, this ended up giving me 2x39V rectified voltage, which is a bit more
than I would have liked, especially since it puts the BC547/557 very close to their CE voltage limit. So I would recommend you either go 
with a slightly lower voltage or swap the transistors in the differential stage to 2N5551/5401 or something simiar. 

### PCB Design
I am *NOT* an expert at PCB design. In fact, I'm very much a novice and not very good at it, so the PCB layout is probably somewhat naive. 
I settled for a ground plane and laterally adjacent power planes rather than star ground. That said, with proper ground wiring, there is 
no audible hiss or hum. If you have ideas on how to improve the PCB design (or the overall design), feel free to fork and issue a pull
request. I'll take any help I can get.

# Build and bring-up

## Mechanincal design
Mechnical design is not my forte, so you're probably best off to come up with your own design here. I went with a 12' x 12' metal case
and found that it easily fit all the components. In my 50W design, I'm simply using the case as heat sink. This works well for any power
you would put out in a normal home and the case get barely luke warm. For higher power levels or listening at a sustained high volume, 
a dedicated, finned heat sink is probably a better option.

## Thermal coupling
It is *extremely* important that Q7, Q8, Q9, Q10 and Q12 are thermally coupled, i.e. mounted on the same heat sink. Failure to do this 
may result in thermal runaway and catastrophic damage to the output stage! 

## Bring-up
This is the procedure I followed when bringing it up the first time. It saved me on the first bring-up since I had accidentally mixed up 
the transistors in the LTP which put DC on the speaker output.

1. Disconnect the speakers.
2. Turn the bias trimmer to its lowest setting (highest resistance)
3. Connect a current limited power supply with -12V, 0V and +12V to the power inputs. Limit the current to 200mA. The amplifier should draw less than 100mA.
4. Measure the DC voltage across the speaker terminals. It should be less than 50mA. 
5. Connect an oscilloscope to the output. There should be no oscillations.
6. Slowly bring the voltage to +/-24V. The current draw should still be well below 100mA.
7. Connect a 8Ω resistive dummy load to the output. The current draw should not increase significantly.
8. Connect a signal generator to the input and send in a 10mV p/p 1kHz sine singal. You should see approximately 200mV p/p on the speaker output.
9. Increase the current limit to 500mA and try the same with a 100mV p/p and 200mV p/p signal.
10. Increase the current limit to 1A and try a 300mV p/p input.
11. Connect the speakers and feed a music signal. It it may not sound perfect, since lab supplies aren't designed to power amplifiers, but there should be no severe distortion. 
13. Hook up the amplifier to the power supply that will be used for the final build. I put 3A fuses in series with each rail.
14. Measure DC voltage on the speaker outputs. The DC voltage 
15. Measure the voltage between the emitters on the output transistors (Q10 and Q12). Adjust the bias trim until the voltage reaches 8mV. This corresponds to a bias current of 18mA. You may go higher or lower depending on your preferences. Just watch the temperatures!
16. Bring up the amplifer and test it using a dummy load and signal generator as described above.
17. If all has gone well up until this point, connect your speakers and crank up your favorite tune. ENJOY!

# Schematic
![Schematic](images/schematic.png)

## SPICE simulations

All simulations are done with an 8 ohm resistive dummy load, 1V input at 1kHz.

### THD
At a fairly moderate quiescent current of 15mA, I get a THD of 0.019%. A THD below 0.1% is considered to be inaudible, so these numbers
are fine by me. I know there are audiophiles who insist on THD numbers <0.0001%, but I'm not one of them. Inaudible is just fine by me.

I've gotten suggestions that adding an emitter follower to the VAS might improve THD. I might try that, but I have a feeling there will 
be more pressing matters than getting the THD even more inaudible. 

```Fourier components of V(out)
DC component:-0.00675533

Harmonic	Frequency	 Fourier 	Normalized	 Phase  	Normalized
 Number 	  [Hz]   	Component	 Component	[degree]	Phase [deg]
    1   	 1.000e+3	 2.087e+1	 1.000e+0	   90.66°	    0.00°
    2   	 2.000e+3	 1.490e-3	 7.140e-5	  -77.43°	 -168.10°
    3   	 3.000e+3	 3.155e-4	 1.512e-5	   38.31°	  -52.35°
    4   	 4.000e+3	 3.378e-4	 1.619e-5	  106.45°	   15.78°
    5   	 5.000e+3	 5.623e-4	 2.694e-5	 -129.10°	 -219.76°
    6   	 6.000e+3	 7.723e-5	 3.700e-6	  129.91°	   39.24°
    7   	 7.000e+3	 3.638e-4	 1.743e-5	 -163.17°	 -253.83°
    8   	 8.000e+3	 6.704e-5	 3.213e-6	 -144.07°	 -234.73°
    9   	 9.000e+3	 3.393e-4	 1.626e-5	 -174.70°	 -265.37°
   10   	 1.000e+4	 1.427e-4	 6.838e-6	 -141.03°	 -231.69°
Partial Harmonic Distortion: 0.008339%
Total Harmonic Distortion:   0.018652%
```

![FFT](images/fft.png)

### AC Analysis
The amplifier has a 3dB bandwidth of about 72kHz, which is by no means in the audiophile range, but it keeps any poles far away from the audible 
range to make both the amplitude and phase response vritually flat where it matters. I am of the opinion that nothing >20kHz has any impact on
the listening experience.

![Bode plot](images/bode.png)

