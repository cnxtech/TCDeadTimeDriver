# Tesla Coil Dead-Time Driver V1 (TCDeadTimeDriver-V1)

Version 1 of the Dead-Time Controller add-on board for the [oneTesla v1 Musical Tesla Coil](http://onetesla.com/).  It is used to add a Dead-Time between the gate pulses to the IGBT driver transistors to minimize overlap and prevent premature death of the transistors.

This board can be ordered from [https://www.oshpark.com/shared_projects/0FoLInjA](https://www.oshpark.com/shared_projects/0FoLInjA).

## Why?

On my oneTesla coil, I have been fighting some issues of "shoot-thru" in the half-bridge driver that causes both transistors to conduct and short, blowing both transistors and the fuse and sometimes frying the resistor on the gates and other carnage.

So, I designed this little add-on. It has greatly helped by allowing for an adjustable dead-time between the pulses to the half-bridge. The circuit uses two chips and fits in series between pin 4 of the 74HCT14 and pin 2 of each of the UCC2732x driver chips on the original oneTesla v1 main board. You do have to swap-out the UCC27321 for a second UCC27322 as this circuit needs two non-inverting drivers instead of one inverting and one non-inverting.

I'm posting it here for others who have had similar issues. It doesn't fully solve all failure modes possible, but definitely helps.


## Tools needed:

- Oscilloscope with at least two channels and delta-T markers
- Signal generator capable of producing either a +5V TTL signal or a +/- 5V (or so) AC sinewave in the 200-300kHz range
- Frequency counter.
- oneTesla v1 Musical Tesla Coil (assembled)


## To use and calibrate:

1. First, use the original circuit as-is with a scope on pin 4 of the 74HCT14 on the oneTesla main board to find the frequency of the primary circuit with the coil running generating high-voltage (Warning: Care must be exercised to isolate test equipment and keep output power minimal!!). This has to be done using the delta-T markers on the scope instead of a frequency counter because the interrupter will not have the coil running continously (and you wouldn't want to be that close to it measuring frequencies if it was). You have to trigger the scope at the right spot and crank up the intensity of the beam to handle the rescans necessary to see it (i.e. it will be dim) on analog scopes. On digital scopes, you may be able to program it to simply record the correct spot of the wave (I'm old school and tend to prefer using analog scopes, even though I own both). For reference, mine measured 286 kHz.

2. Build and Connect this add-on TCDeadTimeDriver circuit between pin 4 of the 74HCT14 and pin 2 of each of the UCC2732x driver chips, replacing the xx321 driver with a xx322 driver. This new circuit needs two NON-inverting drivers to function. The original oneTesla design has one inverting and one non-inverting. So you have to use two UCC27322 chips instead. You'll need to either cut traces or pull the pins out of the sockets and solder a short lead to connect them, as the original circuit has both pin 2s of the drivers together. This circuit needs them separated so they can connect to the two independent outputs of the SG3525. And you must disconnect the existing route of pin 4 of the 74HCT14 that's currently going to pin 2 on both drivers, as that pin must go to this circuit first and not to the drivers. Note: The output of pin 4 of the 74HCT14 must continue to connect to the clock input of the 74HCT74 so you do not want to pull its pin out of the socket. Instead, solder on a wire either at the feed-through pad that's on one of the trace-routes on the board or simply solder it to the pin with the pin still inserted in its socket, which is what I did.

3. Power-up ONLY the low-voltage circuitry and put a dual-trace scope on Out#1 and Out#2 on this add-on board, which are pins 11 and 14 of the SG3525 (or pin 2 of each of the drivers). Adjust the dead-time potentiometer (pin 7 of the SG3525) to set the desired pulse separation for the drivers, but don't go too wide with the separation or you'll run out of bandwidth on the SG3525, as it's only rated for 400kHz for the oscillator (which must run at twice the resonant frequency of the primary winding). Note: If you set it too wide, you'll find out in the next step as you won't be able to set the desired frequency without the SG3525 outputs going "Minnie-Mouse" on you. If you later want to change the dead-time, you must also readjust your frequency in the next step as the dead-time pot affects it too. Even at the minimum setting, you'll get a nice dead-time added. The exact range of dead-time you can get depends on the exact frequency you are running. For reference, my circuit (running at 286 kHz) is adjustable from 266nS to 792nS. I'm actually still running mine at around 266nS, which is significantly more than the original oneTesla circuit, which only had the propagation delay of the drivers to add dead-time.

4. Adjust the frequency set potentiometer (pin 6 of the SG3525) until the frequency is about 5% lower than the target resonant frequency of the primary. For example, I tuned mine to 270 kHz for a resonant of 286 kHz (meaning the two outputs, pins 11 and 14 of the SG3525, should indicate 286 kHz on the frequency counter and pin 4 (the Freq Out test point) of the SG3525 should read about 540 kHz, yes it exceeds the 400 kHz published limit of the SG3525, but it still works well. My chip actually functioned correctly all the way to the mid-600 kHz range, though the higher it is, the less adjustment you'll have on the dead-time control).

5. Hook a signal generator to the current sense transformer conditioning circuit on either side of the 0.1uF cap that leads into pin 1 of the 74HCT14 on the oneTesla main-board (depending on the type of signal you are generating -- one side needs a sine-wave, the other can use a square wave)...

6. Set the frequency on the signal generator for the resonant frequency measured in Step #1. Verify a corresponding signal on pin 4 of the 74HCT14. It should be a nice square-wave at your resonant frequency.

7. Use a dual-trace scope to display one of the two outputs of the SG3525 (either pin 14 or 11) and pin 3 of the SG3525 (or pin 13 of the 74LS123), which is the sync signal and will be the 2x clock source to do a phase-locked-loop on the SG3525 so that it creates the same frequency as the resonant of the primary. You should now see the SG3525 output pins resyncing in conjunction with the pulse coming into pin 3, causing the frequency on pins 14 and 11 of the SG3525 to shift accordingly, forming a simple phase-locked-loop circuit.

8. Adjust the sync-pulse width potentiometer (pin 15 of the 74LS123) until the SG3525 is able to correctly lock onto the same frequency as the signal generator is supplying. You should find that there's a band in the adjustment range of the sync pulse-width where it will pull-in to the correct frequency and lock to it. Adjust it to the middle of this adjustment band so that there's ample tolerance on either side in case the frequency drifts slightly.

9. That's it... Disconnect the test equipment, and use the coil as before...

With this circuit, I've been blowing far fewer transistors than before. If calibrated correctly, output power should be the same, if not better than without it. And if you needed to remove it for some reason, you can simply disconnect this board and jumper the pin 2 of the drivers back to their original connection point and replace the non-inverting driver with the original inverting version.

Also, I've found that 3-position Phoenix Contact connectors (normally used for attaching wires to a circuit board) will fit the transistor holes and allow a method to "socket" the IGBT transistors on the oneTesla main-board. After the 5th or 6th time of unsoldering and resoldering the transistors (a delicate, time-consuming task), I switched to that and now I can change the transistors without even removing the circuit board. Though with this circuit addition, I haven't needed to do so quite as often. :-)


### Bill of Materials:

- C1 : 220pF Ceramic
- C2 : 220pF Ceramic
- C3 : 22pF Ceramic
- C4 : 0.001uF Mica
- C5 : 0.1uF Mylar
- C6 : 0.1uF Mylar
- C7 : 0.01uF Ceramic
- C8 : 10uF 25V Electrolytic
- C9 : 10uF 25V Electrolytic
- C10 : 0.01uF Ceramic
- C11 : 0.01uF Ceramic
- D1 : 1N4148
- D2 : 1N4148
- R1 : 470 Ohm 1/4W
- R2 : 470 Ohm 1/4W
- R3 : 20K Ohm Multiturn Potentiometer
- R4 : 1K Ohm 1/4W
- R5 : 2K Ohm Multiturn Potentiometer (see fine-tuning notes below)
- R6 : 1K Ohm 1/4W (see fine-tuning notes below)
- R7 : 100 Ohm Multiturn Potentiometer
- P1 : Molex 4-Pin Header connector (0022232041) DigiKey: WM4202-ND
- P1 Mate : Molex 4-Pin Female Receptacle (0022013047) DigiKey: WM2002-ND
- P2 : Molex 3-Pin Header connector (0022232031) DigiKey: WM4201-ND
- P2 Mate : Molex 3-Pin Female Receptacle (0022013037) DigiKey: WM2001-ND
- Pins for P1 and P2 : Molex Female Crimp Pin (0008500113) DigiKey: WM1114CT-ND
- 2x 16-pin dip sockets
- U1 : 74LS123 IC (16-pin dip)
- U2 : SG3525 IC (16-pin dip)
- second UCC27322 to replace the UCC27321 on the oneTesla main board

(Note: it may work OK with a 74HCT123, but the specs on it are a bit different than the 74LS123 and I haven't tried it. I've only used the LS family part, since I happened to have one on-hand already)... So you'll have to experiment. Maybe someone can test that and post their results?

Also, you may can get away with a mylar cap for the 0.001uF timing capacitor. But it will have worse frequency drift than a mica part. As long as it stays within the 5-10% window of tolerance for the SG3525 sync logic, it should work. The mica cap is definitely better and I happened to have one on-hand, so that's what I used.


## Why the need to switch to two (non-inverting) UCC27322 drivers?

The requirement of two non-inverting drivers shouldn't be a mystery. The oneTesla Coil uses a half-bridge to drive the primary winding. The half-bridge uses two transistors to alternately drive one side of the coil to V+ and ground (one transistor takes it to V+ and the other to ground), meaning that only one of the two transistors can be turned on at a time.

In the original design, they used the square-wave output from the current sense circuit to go to the input of both driver chips and they had one inverting driver and one non-inverting driver. The non-inverting drver turned one transistor on when the square-wave was high and the inverting driver turned the other transistor on when the square-wave was low. This allowed it to toggle between the two transistors turning only one on at a time.

The problem is that transistors turn on quicker than they turn off. This means that there's a slight overlap between the on and off cycles if there isn't a slight delay between switching which transistor is turned on. If both transistors turn on simultaneously, then you have V+, which in this case is the rectified direct AC mains voltage, shorted directly to ground, a problem known as "shoot-through". That causes the transistors to fail.

Short bursts you can get away with, but it produces a lot of heat (and reduces operating efficiency and overall power in the coil) and if the overlap gets slightly longer, which can happen because they get even slower as they get hotter, then eventually --- boom! The transistors short out completely and die...

The only delay between the pulses in the original circuit design was the propagation time through the driver chips and gate drive transformer, plus the capacitance of the gates of the transistors (i.e. their charge/discharge time). Granted, the IGBT's used in the oneTesla driver design has pretty fast on and off times and nearly symmetric on/off times, which is what mostly kept them out of trouble. But still, if you load up the Tesla Coil a bit to where there is more current draw through the transistors, the switch-on vs. switch-off time differential will worsen, and they will eventually fail... And that's why I made this circuit.

The SG3525 requires a clock that's twice the frequency of the outputs its driving, so that it can use one cycle to turn on one output (one transistor) and the other cycle to turn on the other output (other transistor), completely independently. So, I used the 74LS123 circuit as a frequency doubler to double the square wave from the current sense transformer -- giving a short pulse at the beginning of both the high-period and low-period of the original circuit signal. That gets feed to the SG3525's sync input to clock it and phase lock it to the current sense transformer (and lock it to the resonant frequency of the primary winding of the Tesla Coil in the process).

The SG3525 has two outputs that can either go to a driver or straight to the transistors (depending on your circuit and needs). But the outputs are both high-going pulses for on. That means that you can't invert one of them before sending it to the transistor or you'll end up turning on both transistors at the same time (very bad). So you have to use two non-inverting drivers. That way each transistor is turned on only when it should.

And they have to be non-inverting because the SG3525 is setup to drive the outputs high for on and low for off. There's a SG3527 with them inverted. And on it, you'd need both to be inverting drivers to invert them back to the correct polarity before going to the transistors. Essentially the SG3525 is designed for driving N-channel devices (as these transistors are) and the SG3527 is designed for driving P-channel devices.

The SG3525 has a circuit that lets you use a resistor or potentiometer to set a delay time between the two output pulses, known as the dead-time. During this time, both outputs are off, allowing time for the one transistor to turn off completely before turning on the other. That was my whole reason for adding this circuit as it gave me a way of dialing in a precise nanosecond delay between switching transistors.

In my opinion, this "add-on" isn't really optional. They are just barely getting away with things in the original circuit design and if you need to switch to a different transistor, such as one able to switch higher voltage or current (as I have also been experimenting with), well, they tend to have even longer turn-off times (with a nearly same fast turn on time) which only exacerbates the problem even more.

I originally experimented with other tricks of adding dead-time. But they were all sub par in one way or another. For example, I tried things like a larger gate resistor with a diode so that the on-time gate charge time would be longer than the off-time gate discharge time to bring the on and off times back to being more symmetrical with one another. But that caused problems with the startup or "ring up" operation of the primary and tended to cause it to not want to start oscillating.

There were some other failed attempts to do similar tricks with the pulses in the current sense transformer buffer circuit. While I got several configurations to function, they were still too picky about the specific component values and exact operating conditions of the coil. So I finally punted and designed this proper circuit for it.


## Fine-tuning values to match your coil...

The values specified in the original Dead-Time Add-on Circuit worked well for my original coil configuration.  But after I made some modifications, like adding a strike-ring and a special ground-plane, I found that the resonant frequency of my secondary had dropped nearly 7% from what it was, from 246.5.kHz with a simulated streamer (284kHz or so without the simulated streamer) down to 230kHz (260kHz without the simulated streamer).

In my original "out-of-the-box" configuration, my primary resonance (which was found to be 286kHz) was really close to my secondary without the simulated streamer, but was 16% higher than the secondary with the simulated streamer.  Now, typically you'd tune them intentionally out-of-tune slightly, but generally the other way around -- making the primary a little lower than the secondary so that when streamers form (especially ones that strike a ground) and pulls down the resonant frequency of the secondary, it moves more in-tune with the primary than out.

Nonetheless, my original experimentation was with the out-of-the-box setup with the coil built as-per the instructions.  And so my original Dead-Time circuit was optimized for a 286kHz primary frequency.  But now that I've made some physical changes and caused them to be a bit out-of-tune in general, I'm looking at modifying the tank capacitor and/or winding of the primary to correct my tuning issues.

What I'm finding is that the original component selection on my Dead-Time Add-On circuit is inadequate for the potentiometer R5 (2K) and fixed-resistor R6 (1K) values  in selecting the proper frequency.

One solution is to change the potentiometer R5 from 2K to 5K.  That should give you all of the adjustment range you'll ever need.  However, you will sacrifice accuracy of fine-tuning adjustment.  Therefore, you may opt to change the fixed-resistor R6 instead, which is my choice.

Here's a little chart showing common fixed-resistor values for R6 with the original 2K adjustment potentiometer R5:

 |        | | | Zero Dead |         | | | Max Dead |         | | | Working Range |         | | | Effective Range |         |
 | Rfixed | | | FreqMin   | FreqMax | | | FreqMin  | FreqMax | | | FreqMin       | FreqMax | | | FreqMin         | FreqMax |
 |--------|-|-|-----------|---------|-|-|----------|---------|-|-|---------------|---------|-|-|-----------------|-------- |
 | 2700   | | | 151976    | 264550  | | | 139276   | 228311  | | | 151976        | 228311  | | | 151976          | 205479  |
 | 2400   | | | 162338    | 297619  | | | 147929   | 252525  | | | 162338        | 252525  | | | 162338          | 227273  |
 | 2200   | | | 170068    | 324675  | | | 154321   | 271739  | | | 170068        | 271739  | | | 170068          | 244565  |
 | 2000   | | | 178571    | 357143  | | | 161290   | 294118  | | | 178571        | 294118  | | | 178571          | 264706  |
 | 1800   | | | 187970    | 396825  | | | 168919   | 320513  | | | 187970        | 320513  | | | 187970          | 288462  |
 | 1500   | | | 204082    | 476190  | | | 181818   | 370370  | | | 204082        | 370370  | | | 204082          | 333333  |
 | 1200   | | | 223214    | 595238  | | | 196850   | 438596  | | | 223214        | 438596  | | | 223214          | 394737  |
 | 1000   | | | 238095    | 714286  | | | 208333   | 500000  | | | 238095        | 500000  | | | 238095          | 450000  |

I've shown the frequency range for both minimum and maximum dead-time adjustment.  The Working Range column shows the minimum and maximum frequency for the full-span of the dead-time adjustment (that is the maximum minimum-frequency and the minimum maximum-frequency).  And the last column, labeled Effective Range, is the same as the Working Range, but has the maximum frequency scaled down by 10%, as I've found in nearly every situation that the real frequency is about 10% less than that calculated by the SG3525 timing equations.  So that's the column you should use.

I'm sure this 10% degradation of frequency is a composite of the tolerance in the involved components as well as the SG3525 equation being only an approximation.  The minimum would similarly be scaled down by 10%, but I left it unchanged as only the tighter range should be considered the real working range.  Though, I've never seen it be off in the other direction with higher frequencies than those predicted, only lower frequencies.

On my circuit, I am changing R6 from 1K to something closer to 2K to allow me to target the new resonant frequency of my primary and to still allow for a nice range of adjustment without giving up fine-tuning ability on the potentiometer.

I also highly recommend the JavaTC applet found on [http://www.classictesla.com/](http://www.classictesla.com/).  I have found it to not only be extremely useful, but quite accurate as well, assuming you are careful in measuring things and entering correct dimensions and values.  In fact, when I entered the data for my coil, its predicted resonant frequency for both the primary and secondary was only off by a few kilohertz.

The only value I question on JavaTC is the DC Resistance of the secondary.  On mine, I measure 250.7-ohms.  But it predicted 511-ohms, which is nearly a perfect factor of 2, and makes me wonder if they are simply missing a divide by two in their model.  But otherwise, I found everything to be nearly exact -- that is, once I entered all of the new modifications I made to my coil, such as the strike-ring and ground plane positions and dimensions.


## Comparison Videos with and without this circuit

One evening while working on the tuning of my coil, I opted to disconnect and remove my Dead-Time Circuit and just get it out of the way temporarily so I wouldn't have to worry about recalibrating it while tuning the coil. This is easy to do on my setup because I put little molex header connectors on it rather than hardwiring things.

After several rounds of working with the coil tuning, I opted to play a song on it via MIDI and decided to give it a go without the Dead-Time Circuit installed. A little over a minute into the song and there's the infamous flash of green light of the fuse blowing as the transistors die. I was playing "Amazing Grace", but am thinking I should have played "Another One Bites the Dust" instead, as my pile of dead transistors continues to grow.

I then replaced the transistors and fuse and swapped the Dead-Time Circuit back in and recalibrated it for the frequency of the coil. And then immediately played "Amazing Grace" again without changing any other settings.

I've made the videos available for you to see so you can compare them and see how the coil behaves with and without the Dead-Time Circuit installed. As I mentioned above, if the circuit is properly calibrated, you'll not notice any performance differences other than your transistors won't die when running long pulse widths and higher powers. So I think this is a good demo of why I designed this circuit in the first place. It's amazing how much it helps just removing that little bit of overlap with a little Dead-Time.

Here's the links to the videos:

[Amazing Grace without the Dead Time Circuit](http://cloud.dewtronics.com/videos/oneTesla_201503070003_AmazingGrace_without_DeadTimeCkt_360p.mp4)
[Amazing Grace with the Dead Time Circuit](http://cloud.dewtronics.com/videos/oneTesla_201503070141_AmazingGrace_with_DeadTimeCkt_360p.mp4)


## Setting Sync Pulse Width Adjustment for a Frequency Range

Overall, I have been pleased with the functionality of the dead-time circuit.  In fact, I've found in my calibrating of it, that you can actually dial in a whole range of frequencies that it will phase-lock to rather than targeting a specific frequency as I describe above, even though it technically only has to ever lock into the resonant frequency of the primary itself.

To do that, hook the SG3525 output to a scope and frequency counter and turn on the low-voltage/logic power (no high voltage).   First set your desired dead-time width, as that adjustment affects the overall frequency and signal locking characteristics.  I'm still running on the lowest setting on dead-time on this coil.  Next, you set the desired lowest frequency you want it to be able to lock to using the frequency set pot, as can be noted on your frequency counter.  This value should probably be based on the resonant frequency of the coil with a simulated streamer, as that feels like the best value to use.

Next, turn down the sync pulse width (increased resistance on that pot if my memory is correct) and apply a frequency with a frequency generator to the current sense transformer circuit, start with the same frequency you dialed into the frequency set pot.  Then gradually crank up the frequency on the signal generator to the desired highest frequency of the range you wish it to lock, turning up the sync pulse width pot as you increase the frequency to the point that the SG3525 locks to that new frequency.  Or to do it quicker, you could just crank up the signal generator to the highest frequency you want to lock and turn the sync pulse width pot until it locks.

At that point, you should be able to sweep the frequency on the signal generator anywhere between the low frequency set point and the higher frequency set point and the SG3525 should lock to it, only losing lock when you exceed that range on either side.  I'm not sure the SG3525 was designed to operate as a phase locked loop, but it performs admirably as one for these frequencies.

I don't know how big of an overall range it's able to lock, but in the video linked above, mine was calibrated to lock the range of about 250kHz to 300kHz.  The primary, still with the original 0.068uF cap, is running around 287kHz as its resonant.  The resonant of my secondary, without simulated streamers is 260kHz.  With a simulated streamer it's around 225-230kHz.  Once I get my hands on a 0.083uF cap (since my coil is still not properly tuned yet), I believe it will be nearly perfectly tuned and I should be able to change my lock range to something more like 230kHz to 280kHz.



