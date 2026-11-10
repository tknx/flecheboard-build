# Building flècheboard

> **Work in progress.** This is an ongoing hobby project; the guide changes as the board does.

flècheboard is a wall-mounted foil training target: five lit 100 mm buttons in an X pattern, plus a small power button. A Raspberry Pi runs it. It lights targets for the fencer to hit and times every touch, and a phone shows the results. It has three drills, chosen by pressing the power button 1, 2 or 3 times:
- **Random:** one to three targets light up; hit any of them, as fast as you can.
- **Programming:** tap out a pattern with its rhythm, and the board plays it back for you to hit.
- **Simon Says:** the board shows a sequence; hit it back in order. Each round adds one more.

A speaker calls the drills, and an LED strip round the edge shows green, yellow or red for each touch. Off-target hits are felt by a small accelerometer.

**The software** (the program on the Pi, and the phone app it serves) is available on request. It was written entirely by AI and comes with no guarantees: use it at your own risk. It runs on DietPi (Debian) on the Pi and sets the Pi up itself. A few steps below need it, and say so; if you'd rather write your own, [Writing your own software](#writing-your-own-software) lists what the hardware expects.

## Ingredients

**Board**
- 1× sheet of 3/4 in plywood. Cut a 24 × 30 in face from it, plus strips for a 22 × 28 in frame (outside size), 3 in deep. Most stores will cut the sheet for you.
- 2× 24 in square, 3/4 in gym puzzle mats (EVA foam). Trimming off the interlocking teeth can leave a mat slightly under 24 in, so check yours still covers the face's 24 in width, or buy a third.
- Contact cement
- 1680D ballistic nylon to cover the foam (one piece, about 30 × 36 in: the face plus its edges and 2 in onto the back)
- 3M Super 77 spray adhesive (nylon to foam)
- Wood glue
- 4× steel corner braces, for the inside corners of the frame
- GRK multipurpose screws for the frame. Anything screwed into the back of the face (speaker, accelerometer standoff) must be shorter than the plywood is thick, about 5/8 in, or it comes through into the foam.
- Blue Loctite (threadlocker)

**Buttons**
- 5× 100 mm arcade buttons (the targets)
- 1× 33 mm arcade button (power/control)
- **Clear skateboard grip tape** (e.g. Mr Grip clear, about 9 × 33 in sheet): the bare caps are too slick and a foil point skates off. Clear grip tape has real grit and still lets the lamps glow through. Not black grip tape (it hides the lamps), and not "rubberized" or shower anti-slip tape (embossed vinyl, no grit).
- Lamps must be **5 V** LEDs, wedge type, in holders. Many arcade buttons ship with 12 V bulbs, so check before you buy.
- Each button's thread has to reach through the 3/4 in plywood with room for its nut. Check the thread length before you buy.
- A 3D-printed flange for each button: 5 large, 1 small. Files: [`hardware/flanges/`](hardware/flanges/) (see box step 6).

**Electronics**
- Raspberry Pi 3B+ and a microSD card (32 GB is plenty)
- 8-channel 5 V relay board with optocouplers, high/low-level trigger jumpers and **screw-terminal inputs** (DC+, DC−, IN1–IN8), e.g. JESSINIE or ANMBEST. The relays click each time a lamp switches; for silent switching, see [Quieter lamps](#quieter-lamps-a-mosfet-board-instead-of-relays).
- 5 V power supply, the sealed brick kind (like a laptop charger), sold as an "LED strip power adapter": **10 A** is enough for everything; **20 A** if you'll add the RGB surround and want it near full brightness. The original build uses a Henxlco 5 V 10 A (50 W) adapter. Avoid an open-frame supply with screw terminals: its mains connections are bare, inside a box that gets hit all day. These bricks end in a 5.5 × 2.5 mm barrel plug, and barrel jacks are usually rated 3–5 A and can run hot near 10 A, so cut the plug off and crimp ring terminals onto the two leads instead (check which lead is + with a multimeter). The software limits the strip to the supply's rating minus 5 A, so tell it which you have.
- An inline fuse holder and a 3 A fuse, for the Pi's 5 V feed (it bypasses the Pi's own fuse)
- 2× screw bus bars ("marine bus bar"), one for +5 V and one for GND, with at least 14 screws on the GND bar and 11 on the +5 V bar, which already covers the audio and RGB upgrades (see Power distribution below). The original build uses a JUNXILPPD 150 A bus bar and an RVBOATPAT 275 A bus bar (14 × #8 screws). Plus ring terminals to fit their screws.
- 1× small Wago lever connector, to join the five target switch grounds
- 14 AWG wire, for the supply-to-bus feeds
- 18 AWG wire, for the 5 V runs from the buses to each device
- 22 AWG tinned stranded wire, for signals
- Wire ferrules and a ferrule crimper, for every wire end that goes into a screw terminal
- Screw-terminal GPIO board ("GPIO screw terminal HAT") for the Pi: clamped wires can't shake loose under impacts
- Industrial-strength adhesive Velcro, to stick the Pi, the relay board and the power supply inside the frame. (The original build used 3D-printed plates for the boards; Velcro is easier, and it's how the supply is held.)
- ADXL345 accelerometer breakout (e.g. Adafruit 1231) for off-target misses, with a 2.54 mm screw-terminal block soldered to its header, and a short standoff (3D printed, or a small block of wood) to mount it
- Insulated female spade connectors (quick-disconnects) for the button switch and lamp tabs, and a crimper for them. Check the tab size on your buttons (usually 4.8 mm, .187 in).
- Wire labels (a label printer, or write-on labels)
- Velcro cable ties and stick-on cable-tie mounts

## Tools
- Drill and screwdrivers
- Bi-metal hole saws with an arbor:
  - 100 mm: the target recesses in the foam (the original build used a [Rannb 100 mm](https://www.amazon.com/dp/B07CWR6XLV))
  - 51 mm: the power button's recess in the foam
  - 35 mm: the button holes through the plywood
  - 76 mm: the speaker hole, through the foam and the plywood (audio upgrade)
- 3D printer (button flanges)
- Clamps
- Utility knife with fresh blades, and a cutting mat
- Clothes iron and a cotton cloth (to take the creases out of the nylon)
- Soldering iron (the accelerometer's terminal block)
- Wire strippers, hot-glue gun, and a multimeter

## Build the box

1. **Frame.** From the plywood, make a 22 × 28 in rectangular frame, 3 in deep. Glue and screw the 24 × 30 in face on top, centred, so it overhangs the frame by 1 in all round. That reveal is where the LED strip goes. Glue and screw everything, and fit a steel corner brace inside each corner; it has to be rock solid. Then drill two holes in the frame: one in the top wall, close to where the Pi will go, for the LED strip's wires (see [RGB surround](#rgb-surround)), and one in the bottom wall for the power cord.
2. **Lay out the targets.** T3 sits at the exact centre of the face; mark the others relative to it. Offsets are in inches, as seen by the fencer facing the board:

   | Button | Position | Offset from centre (x right, y up) |
   |---|---|---|
   | T1 | high left | (−6, +8) |
   | T2 | high right | (+6, +8) |
   | T3 | centre | (0, 0) |
   | T4 | low left | (−6, −8) |
   | T5 | low right | (+6, −8) |
   | P | power (33 mm) | about (0, −10.5): centred, below T4 and T5 |
   | S | speaker (audio upgrade) | about (0, +11): centred at the top |

   Drill a small pilot hole through the plywood at each centre point.
3. **Foam: mark and cut the holes.** The foam is covered in nylon *before* it goes on the board (step 4). Wrapping it once it's glued down means peeling its edges back to tuck the nylon under, and that leaves the sides puffy (we tried it). So mark and cut its holes while it's loose:
   - Trim the gym mats to the face's size (24 × 30 in) and glue the two pieces edge to edge with contact cement, so they handle as one slab.
   - Lay the slab on the face, edges flush, and weigh or clamp it so it can't shift. Drill back through the pilot holes from behind, into the foam, to mark each button's centre and the speaker's. Mark which edge is the top.
   - Cut the recesses through the foam, centred on those marks: 100 mm hole saw for the targets, 51 mm for the power button, and 76 mm for the speaker at S. A hole saw grabs and tears nylon, which is why the holes are cut before the cover goes on.
4. **Cover the foam in ballistic nylon.** The nylon spreads each hit and stops a point that lands just beside a flange from sinking into the foam and catching under the flange's edge. (An auto or marine upholstery shop does this kind of job every day, if you'd rather hand it over.)
   - **Creases.** Iron the sheet face down, on the back, through a cotton cloth, at the iron's lowest (synthetic) setting with steam. Press for 5–10 s rather than sliding, then weigh it flat while it cools. Never let the iron touch a coated back directly, and try it on an offcut first.
   - **Front.** Lay the nylon face down (coated side up, if it has one) and spray it and the foam's front with 3M Super 77; test a foam offcut first, as some foams soften. Lay the foam on it, front down, and press it flat from the centre out.
   - **Edges.** Spray the foam's edges and fold the nylon up them and onto the back by about 40–50 mm, pulling snug but not so hard it rounds the foam's edges. Glue the flaps to the back with contact cement. At each corner, cut the two flaps at 45° so they meet in a diagonal seam, like a picture frame, with no doubled layer.
   - **Holes.** With the foam face down on a cutting mat, cut from the back with a utility knife and a fresh blade: looking into each hole you can see exactly where its wall meets the nylon, so a cut can't run past the edge (from the front, with scissors, it's hard to see and easy to overshoot). Make radial cuts from the middle out to the wall, about every 1–2 cm, keeping the blade off the foam. Trim the triangles' points so each flap is about 15 mm long, spray the hole's wall, and press the flaps up onto it. The flange covers the edge anyway (step 10).
5. **Holes in the face.** Cut each button hole through the plywood with the 35 mm hole saw. If you'll add audio, also cut the speaker hole at S with the 76 mm hole saw. The speaker mounts from behind, so the hole only has to clear the cone; check your speaker's rim is wider than 76 mm, so it has wood to seal against (the 3FE25's body is 81 mm).
6. **Print flanges.** 3D print the button flanges from [`hardware/flanges/`](hardware/flanges/): 5 × `flange-target.stl` and 1 × `flange-power.stl`.
   - **Target flange:** a 101 mm sleeve, 18.5 mm tall (just under the foam's thickness), with a lip round the top (81 mm opening). It sits in the 100 mm recess: the sleeve presses the nylon flaps against the recess wall, and the lip covers the gap round the button.
   - **Power flange:** fills the 51 mm recess, with a 33.6 mm bore for the button that flares out at the top, and a 61 mm rim that sits on the foam's surface over the nylon's cut edge.
7. **Glue the covered foam on.** Contact cement on the plywood and on the foam's back, flaps included; let both go dry to the touch. It grabs on contact and can't be slid into place afterwards, so lay dowels or wax-paper strips across the plywood, line the slab up (top edge up, edges flush, each hole over its button hole), then pull the dowels out from the centre outwards, pressing as you go. Let it cure for 24 hours.
8. **Grip the caps.** Cut a circle of clear skateboard grip tape for each 100 mm target cap and stick it on, so a foil point catches instead of skating off.
9. **Speaker (audio upgrade).** Fix the grille on the face over the speaker hole: it sits on the foam, screwed through into the plywood. Tighten its screws only until the grille is snug; any tighter and it presses into the foam and puckers the nylon round it. Hang the speaker behind the hole, screwed to the back of the face (see [Audio](#audio)). The speaker sits close to the frame's top wall, so turn it so its mounting tabs are diagonal, or check they clear the wall.
10. **Fit buttons.** Fit each button from the front with its flange (step 6) over the nylon's cut edge, then fix it from behind with the button's own nut. Put a drop of blue Loctite on each thread so the nuts don't shake loose under hits. Check each button clicks freely with a normal foil touch. To replace a button later, undo its nut from behind and lift the button and flange out from the front; the nylon stays as it is.

## Electronics

Each button has two independent parts, wired separately:
- **The switch** goes straight to the Pi.
- **The lamp** is switched by a relay.

1. **Mount the boards.** Stick the Pi, the relay board and the power supply to the back of the face with industrial Velcro: it also cushions them, so impacts on the face don't travel straight into the boards. Put the Pi near the strip's hole in the top wall, and the supply low down, near the cord's hole in the bottom wall. Screw the two bus bars down between them (short screws). Fit the screw-terminal GPIO board on the Pi's header. Every Pi connection below goes to a screw terminal, labelled by its GPIO number or header pin. Mounting first lets you cut every wire to length.

   **Or DIN rail** (a tidier option, not built yet): screw short lengths of 35 mm DIN rail to the back of the face and clip everything onto them: DIN-rail PCB mounts or cases for the Pi and the relay board, DIN terminal blocks with jumper bars for the +5 V and GND buses instead of bus bars, and a DIN-rail fuse holder for the Pi's fuse. Every part then unclips for a swap. Fix the rail to the face through rubber anti-vibration bushings (the kind used to mount hard drives), so hits on the face don't travel straight into the boards.
2. **Switches to the Pi.** On each button's microswitch, wire **NO** to the button's GPIO. Leave NC unconnected. Join the five target switches' **COM** wires in a small Wago and run one wire from it to ground at header pin 25 (five wires in one screw terminal won't hold). Or give each COM its own screw on the GND bus bar, as the original build does; the Wago is easier. The power button's COM goes to pin 9. "GPIO n" is the label on pinout diagrams and terminal boards; the header pin is the physical position (pin 1 is at the end away from the USB ports).

   | Button | GPIO | Header pin | Ground |
   |---|---|---|---|
   | T1 | GPIO 17 | 11 | 25 (via the Wago) |
   | T2 | GPIO 27 | 13 | 25 |
   | T3 | GPIO 22 | 15 | 25 |
   | T4 | GPIO 23 | 16 | 25 |
   | T5 | GPIO 24 | 18 | 25 |
   | Power | GPIO 3 | 5 | 9 |

   The power button must be on GPIO 3: it's the only pin that wakes a Pi that has shut down, so the same button turns the board back on.
3. **Relay board to the Pi.** Set **every trigger jumper to H** (high-level trigger). Leave IN7 and IN8 unconnected. DC+ goes to the +5 V bus and DC− to the ground bus (step 5), not the Pi.

   | Relay board | GPIO | Header pin |
   |---|---|---|
   | DC+ | — | +5 V bus |
   | DC− | — | Ground bus |
   | IN1 (T1 lamp) | GPIO 4 | 7 |
   | IN2 (T2 lamp) | GPIO 5 | 29 |
   | IN3 (T3 lamp) | GPIO 6 | 31 |
   | IN4 (T4 lamp) | GPIO 7 | 26 |
   | IN5 (T5 lamp) | GPIO 8 | 24 |
   | IN6 (power lamp) | GPIO 2 | 3 |

4. **Lamps to the relays.** Jumper the COM terminals of relays 1–6 together and feed them 5 V. Wire each lamp with one tab to its relay's **NO** terminal and the other tab to ground. Either tab can go to NO. The LED bulbs only work one way round: if a lamp doesn't light when its relay clicks, rotate the bulb 180° in its holder.
5. **Power.** Run the supply's leads (14 AWG if you extend them) to the two bus bars, then wire out from them to each device separately (star wiring), using 18 AWG for those runs. Crimp a ring terminal onto every wire end that goes to a bus bar.

   **Power distribution.** One wire per screw where you can; two ring terminals can share a screw. Label each bus bar.

   | Bus bar | Wires | Connections |
   |---|---|---|
   | GND | 14 | PSU −, Pi pins 6 + 14, relay board DC−, 6 lamp returns, audio amp GND, RGB strip ×2, level shifter |
   | +5 V | 9 (+2 spare) | PSU +, Pi pins 2 + 4, relay board DC+, relay COM chain, audio amp VIN, RGB strip ×2, level shifter |

   Connect now:
   - **Pi:** 5 V through the 3 A fuse to header pins 2 **and** 4, GND to pins 6 and 14. This feeds the Pi directly, bypassing its own fuse, so never plug in its micro-USB at the same time. **Before the Pi goes on the terminal board,** power the buses and check with a multimeter that the terminals for pins 2 and 4 read +5 V against pin 6, and that nothing else does. Pins 1 (3.3 V) and 3 sit right next to the 5 V pins; a stray strand there kills the Pi.
   - **Relay board:** DC+ and DC−.
   - **Relay COMs:** 5 V, as in step 4.
6. **Test one relay.** With everything powered, briefly touch a wire from header pin 1 (3.3 V) to IN1: relay 1 should click on, and off again when you take the wire away, and its lamp should light. Then do the same for each of the others.
7. **Impact sensor.** An ADXL345 accelerometer board (with a screw-terminal block soldered on) feels hits that miss the targets. Mount it on its standoff, screwed to the back of the face near its centre, clear of the frame and the wiring. Wire it to the Pi:

   | ADXL345 | Header pin |
   |---|---|
   | VIN | 17 (3.3 V) |
   | GND | 34 |
   | SDA | 32 (GPIO 12) |
   | SCL | 33 (GPIO 13) |
   | INT1 | 36 (GPIO 16) |

   On the Adafruit board, leave CS, SDO, INT2 and 3Vo unconnected. On cheaper clone boards (GY-291), tie CS to 3.3 V and SDO to GND, so the chip answers on I2C at address 0x53. The Pi's own I2C pins (GPIO 2 and 3) are taken by a relay and the power button, so the Pi runs a second I2C bus on these two pins. With the software installed, `i2cdetect -y 3` should show `53`.

   Keep the board still while the software starts: it measures the sensor at rest then, to cancel out gravity.

   **Sensitivity** (in the app). A slider sets how hard a jolt counts as a hit (in g). Start at **0.25 g**. What the original build measured, sensor just left of the middle:

   | What | Peak jolt |
   |---|---|
   | Board sitting still (sensor noise) | 0.05–0.09 g |
   | All six relays switching at once | 0.10 g |
   | Gentle presses on a button | not felt |
   | Lunge, upper-left corner | 0.34 g |
   | Lunge, near the sensor | 1.03 g |
   | Lunge, lower-right corner | 1.67 g |

   The threshold must sit below your weakest hit (usually the corner farthest from the sensor) and above the 0.10 g the board makes by itself. To check it, start a Random drill and hit lit targets and the plain face, including each corner. Each target hit should count as a hit and each face hit as a miss. If some face hits don't register, lower it. If knocks on the frame count as misses, raise it.
8. **Check the wiring** (with the software). Its wiring test lights each lamp in turn and asks which one lit, then asks you to press each button, and records which pin each one is on. So a lamp or button on the wrong pin is found and fixed in a minute, without rewiring.
9. **Tidy.** Tie each wire run down near the board it serves, so cable weight and swing never pull on a connection. Label both ends of every wire. Leave a loop in the power cord just inside the bottom wall and tie it down, so a tug on the cord pulls on the frame, not on the supply.

## Hanging it

How you hang it depends on what you have. Whatever you choose, the board takes repeated hits, so hold it at the top **and** the bottom (so it can't lift, swing or rattle), fix into something solid (studs, a rack, a bar; never drywall anchors), and put felt or rubber pads on the back where it touches. Set the height to suit the fencer. Some ideas:

- **French cleat:** a 45° cleat screwed to the wall studs and a matching one on the back of the frame. Easy to lift off; add a block or a screw at the bottom so it can't lift when hit.
- **Stall bars (wall bars):** the original build's plan. A hook block in each top corner of the frame, in place of the top corner braces (three layers of 3/4 in plywood glued together, glued into the corner, screwed through the side wall and bolted through the top wall with carriage bolts, kept behind the LED strip) drops over a rung. A rubber tarp strap through two holes in the bottom of the frame, hooked to a lower rung, stops it lifting.
- **Power rack or pull-up bar:** the same hooks over a crossbar, or J-hooks; strap the bottom to an upright or a lower crossbar.
- **Straight onto the wall:** screw the frame to the studs through its top and bottom walls, or hang it from two heavy-duty hooks in studs with a strap or bracket at the bottom.

## Additional upgrades

Both are fitted on the original build and the software drives them. Either can be left out: without a sound card the board runs silently, and without a strip it runs without the coloured feedback.

### Audio
- MAX98357A I2S amplifier board. An NS4168 also works, but its CTRL pin must be tied high.
- Speaker: any 3" full-range speaker, 4 Ω, with high sensitivity (about 88 dB or more), so it's loud enough at fencing distance. The original build uses a **FaitalPRO 3FE25, 4 Ω version** (91 dB, 100 Hz–20 kHz, 20 W; 81 mm body, 106 mm across mounting tabs, 55 mm deep), which is more than it needs; the Peerless TC8FD05-04 (89 dB, 81 mm square) is a cheaper option. Don't use the tiny speaker that comes in amp kits; it's far too quiet. One speaker only: the amp is mono.
- A 3.5" metal speaker grille, to cover the 76 mm hole (the original build used an [X AUTOHAUX 3.5" car speaker grille](https://www.amazon.com/dp/B0B2RY86H1)), and screws for the speaker (shorter than the plywood is thick).
- Mounting: forward-facing through the face at position S (box steps 3, 5 and 9). Rear-mount it: screw it through its tabs to the back of the plywood, with a foam, rubber or printed TPU gasket under its rim and blue Loctite on the screws. Don't mount it facing up inside the frame: with the board on the wall, the cavity is closed and the sound comes out muffled.
- Wiring: BCLK → pin 12, LRC → pin 35, DIN → pin 40, VIN → +5 V bus, GND → GND bus. Leave the amp's SD pin unconnected: the board's own pull-up keeps the amp on. Connect the speaker across the amp's two outputs; neither speaker wire goes to ground.

### RGB surround
- SK6812 RGBW LED strip, 5 V, about 2.5 m, stuck round the outside of the frame's walls, tight under the face's 1 in overhang. Its wires come in through the hole in the top wall near the Pi (box step 1), which keeps the data wire short; start the strip there. The original build uses a ~30 LEDs/m strip: 77 LEDs. The software needs your strip's LED count, which LEDs sit either side of each corner (it colours the corners in Simon Says), and any LED you can't see (e.g. one buried in the cable hole), and it has a strip test to find them.
- 330 Ω resistor (in the data wire)
- 1000 µF electrolytic capacitor, rated 10 V or more, across the strip's power input
- Optional: 74AHCT125 level shifter (see wiring)
- Wiring: data from pin 19 (GPIO 10) → 330 Ω resistor → strip DIN, with a ground wire from pin 20 run alongside it. Keep the data wire short (under ~30 cm). Power the strip from both ends off the bus bars. Fit the capacitor at the data-input end, across + and −, with its striped (−) leg to ground: fitted backwards, it can burst.
- **Level shifter.** The Pi's data signal is 3.3 V, a little below what the strip is specified for. Most strips work anyway over a short wire (the original build's does), but if yours glitches (random colours, flicker on the first LEDs), a 74AHCT125 boosts it to 5 V: pin 14 to +5 V and pin 7 to ground (both already counted on the bus bars), pin 1 to ground, Pi pin 19 to pin 2, and pin 3 → 330 Ω → strip DIN. Tie the unused inputs (pins 5, 9 and 12) to ground.

### Quieter lamps: a MOSFET board instead of relays
The relays click each time a lamp switches, so a fencer can hear a target light up before seeing it. A MOSFET board switches the lamps silently. The original build still uses relays, so this swap hasn't been built and tested yet.
- **Board:** a 6-channel optocoupler-isolated MOSFET driver board (sold as "6-channel MOSFET module / optocoupler isolation / driver board"), LR7843 MOSFETs, load side DC 5–48 V. Buy the **3.3 V signal version**, and check it really is 6 channels.
- **Not suitable:** single LR7843 or D4184 modules (they need at least 6 V), Omron G3MB "SSR" boards (AC only), plain PC817 isolation boards (signal only, they can't power a lamp), and ULN2803 boards (they drop about 1 V, so the lamps go dim).
- **Signal side:** set all six H/L jumpers to **H** (input high = lamp on, the same as the relays, so the software needs no change). IN1–IN6 go to the same pins as the relay inputs (GPIO 4, 5, 6, 7, 8 and 2). VCC goes to 3.3 V on header pin 1, and GND to a Pi ground.
- **Load side:** VIN to the +5 V bus, GND to the ground bus. Each lamp: the tab that would go to a relay's NO terminal goes to OUTn+, and the tab that would go to ground goes to OUTn−. This frees seven bus connections: the relay COM chain and the six lamp returns.
- **Before wiring all six:** measure one input's current with a multimeter (a Pi pin can supply about 16 mA, and about 50 mA across all pins), and check one lamp lights at full brightness.

## Writing your own software

What the hardware expects from whatever runs on the Pi:
- **Switches:** pressed = the pin reads low. Turn on the Pi's internal pull-up on each switch pin. Debounce them (the original uses 15 ms).
- **Lamps:** a pin high = lamp on (relays and MOSFET board both jumpered to H).
- **Impact sensor:** ADXL345 at I2C address 0x53 on the software bus (bus 3), with its tap interrupt on INT1 (GPIO 16).
- **Strip:** SK6812 RGBW, driven from SPI0 MOSI (GPIO 10).
- **Audio:** the MAX98357A appears as an ordinary I2S sound card.

And the Pi's `/boot/firmware/config.txt` needs these lines (the software's installer writes them; a change needs a reboot):

| Line | Why |
|---|---|
| `gpio=2,4,5,6,7,8=op,dl` | Holds the lamp pins low from power-on, so no lamp flashes at boot |
| `dtoverlay=max98357a,no-sdmode` | The I2S amp. Without `no-sdmode`, the driver takes GPIO 4, which is T1's lamp |
| `dtoverlay=spi0-0cs,no_miso` | SPI for the strip, without taking GPIO 7 and 8 (T4 and T5's lamps) or GPIO 9. Don't turn SPI on the usual way (`dtparam=spi=on` or raspi-config): that takes GPIO 7 and 8 |
| `dtoverlay=i2c-gpio,bus=3,i2c_gpio_sda=12,i2c_gpio_scl=13` | The second I2C bus for the impact sensor; also load the `i2c-dev` module. Don't turn on the Pi's normal I2C: its pins are the power button and a lamp |
