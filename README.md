# 50W Class AB Amplififier

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
condudctivity between the transistors. 

### Voltage Amplification/Drive Stage
I went for a very simple single-sided design here. I've gotten many suggestions that I should try a dual differential design instead, but 
the current design seems good enough, so I stuck with it. The VAS has a 50p compensation capacitor which appears to be enough to provide 
ample phase margin.

### Output stage biasing
The output stage is 
