1) Core Compute

MCU (Primary)
	•	ESP32-S3 DevKitC-1 (ESP32-S3-WROOM-1, 8MB or 16MB)
	•	Search: “ESP32-S3 DevKitC-1”

Qty: 1
Est Cost: $12–18

⸻

2) Sensors (Core System)

Temperature (critical for system value)

Option selected (production-aligned)
	•	DS18B20 Waterproof Temperature Probe (3-pack)
	•	Search: “DS18B20 waterproof temperature sensor stainless steel”

Qty: 3
Est Cost: $8–12 total

Why
	•	Digital (no ADC noise)
	•	Waterproof
	•	Proven stability

⸻

Current sensing (heating detection)

Recommended
	•	SCT-013-030 (30A / 1V output split-core CT clamp)
	•	Search: “SCT-013-030 non invasive current sensor 30A 1V”

Qty: 1
Est Cost: $8–15

Important
	•	Must be voltage output version (1V)
	•	Avoid raw current version for v1

⸻

LED detection (your differentiator)

V1 practical choice
	•	TEPT4400 ambient light sensor (phototransistor module or raw)
	•	Search: “TEPT4400 light sensor module”

Qty: 2 (buy extra)
Est Cost: $5–8

⸻

Optional (nice-to-have for stability)
	•	0.1 µF ceramic capacitors (noise filtering)
	•	10kΩ resistors (pull-down / tuning)

⸻

3) External ADC (Recommended even for strong V1)

This is a key upgrade over basic prototypes
	•	ADS1115 16-bit ADC module (I2C)
	•	Search: “ADS1115 ADC module 16 bit I2C”

Qty: 1
Est Cost: $8–12

Why
	•	Stabilizes:
	•	CT clamp readings
	•	LED sensor thresholds
	•	Reduces ESP32 ADC noise issues significantly

⸻

4) Power System

V1 (safe and simple)
	•	5V USB-C wall adapter
	•	USB-C cable

Qty: 1 each
Est Cost: $10–15

⸻

Optional (closer to production)
	•	HLK-PM01 AC-DC 5V module
	•	Search: “Hi-Link HLK-PM01 5V module”

Only use later when you’re comfortable with AC

⸻

5) Connectivity / Wiring

Clean wiring (avoid breadboard long-term)
	•	JST-XH connector kit (2.54mm)
	•	Search: “JST XH connector kit with crimp tool”

Qty: 1 kit
Est Cost: $15–25

⸻

Wire
	•	22–26 AWG stranded wire
	•	Silicone wire preferred

⸻

Breadboard (temporary only)
	•	Standard half-size breadboard

⸻

6) Mechanical / Mounting (you will print most)

Needed hardware
	•	M2 or M3 screws (assorted kit)
	•	Heat-set inserts (optional but recommended)

⸻

Non-printed items
	•	Velcro straps (for temp sensors)
	•	Zip ties
	•	Foam insulation (for pipe sensor accuracy)

⸻

7) LED Hood (critical component)

You will print this, but include:
	•	Black filament (PETG or ABS preferred)

⸻

8) Estimated Total Cost (V1)
Category
Cost
MCU
$15
Sensors
$25
ADC
$10
Power
$12
Wiring/connectors
$20
Misc
$10
Total
~$90–110


9) What This BOM Enables

With this setup, you will be able to:
	•	Detect heating cycles reliably (CT clamp)
	•	Track temperature gradients (top/bottom/outlet)
	•	Decode LED fault signals (your key differentiator)
	•	Send data over WiFi
	•	Build a realistic prototype close to product behavior

⸻

10) What You Are NOT Building Yet (Intentionally)

Do NOT include in V1:
	•	Relay / control switching
	•	Battery system
	•	Full enclosure polish
	•	Certification-level power design

⸻

11) V1 vs V2 Delta (so you don’t overbuild now)

V2 upgrades later
	•	Custom PCB (ESP32-S3 module + ADS1115 integrated)
	•	Better power supply (isolated AC-DC)
	•	Refined enclosure
	•	Cleaner connectors
	•	Possibly flow sensor

⸻

12) Build Order (important)
	1.	ESP32 + WiFi working
	2.	DS18B20 sensors working
	3.	LED sensor + serial plotter
	4.	LED decoding algorithm
	5.	CT clamp integration
	6.	ADS1115 integration (if needed)
	7.	Mount on actual heater

⸻

Bottom Line

This BOM gives you:
	•	A near-product-level prototype
	•	Minimal wasted components
	•	Clean upgrade path to V2