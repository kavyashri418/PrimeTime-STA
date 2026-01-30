## Additional Checks and Constraints

## Asynchronous Clear/Reset Pins
## Timing Report Recovery
## Path Groups
## Clock Gating Checks
## Which Clock Edges are used
## Timing Reports for Clock Gating
```
set_clock_gating_check -setup 0.20 [get_clocks SYS_CLK]
report_timing -to [get_pins U10/A]
```

## Data-to-Data Check
## Specifing Data-to-Data Checks
## Another Application for a Data-to-Data Check
- WRT pin is driven by a data path, not a clock signal
- Setup and hold check can be specified between WRT and ADDR1
  - In Library or timing model as non-sequential setup and hold checks(or)
  - As a user-specified data-to-data check

## Clock Min Pulse width

## Summary Min Pulse Width Reports
```
pt_shell> report_min_pulse_width -path_type short
```

## Summary Report for All Timing Checks

## Latch Based Analysis
- Latches are level-sensitive sequential devices
- A latch has a D->Q timing arc, in addition to G->Q
  - The D->Q arc is used if time borrowing is involved
  - The G->Q arc is used with zero time borrowing [register-like timing]
 
## Latches with Time Borrowing: Path to L2/D

## Time Borrowing Information in Report

## Time Borrowed in Previous Stage is now Returned
From the report
     The time borrowed in previous stage returned = 0.85
     Startb point of timing path = L2/D pin
     D to Q time = 0.64

## Next Generation Latch Analysis
- Reports intermediate latches as through logic
- Intermediate latches no longer break up register to register path
- Each latch data pin is a through point
- The latch data pins are no longer valid startpoints [unlike old latch analysis]
- Allows improved CRPR, leakage, and ECO fixing
- Enabled with
```
set_app_var timing_enable_through_paths true
```

## Data Arrives Before Transparency: through LAT/D
- LAT/D is a through pin
- Timing path is in between register to register through latch
- Report contains open and close edges of transparency window
- Transparency window shows different latencies for open and close edges
- Arrival at LAT/Q = data arrival at LAT/D + the D-Q delay of latch
- Keyword "early" indicates data arrives LAT/D before transparency

## Data Arrives During Transparency: through LAT/D
- LAT/D is a througgh pin
- Report contains open and close edge of transparency window
- Arrival at LAT/Q is the sum of data arrival at LAT/D and D-Q delay of latch

## Data Arrives After Transparency: through LAT/D
- This is a recovery path, since the transparency window is missed
- To avoid pessimism at downstream logic, the arrival time at LAT/D is limited to close edge
- Data at Q pin of the latch is the sum of close edge and D-Q delay of the latch
- Keyword "missed" indicates data arrives LAT/D after the close of transparency

## Multicycle Paths

## Default Reported Path is Single - Cycle

## Specifying Multicycle Path for Hold
```
set multicycle_path -setup 6 -to [get_pins "*reg[*]/D"]
set_multicycle_path -hold 5 -to [get_pins "*reg[*]/D"]
```

## Reporting a Multicycle Path with ``` report_timing ```

## Combinational Feedback Loops

## STA and Combinational Loops
- Combinational loops are NOT verifiable using STA
- PrimeTime will disable a single timing arc to break this loop

## Issues with Combinational Loops
- Verify timing of combo loop with a simulation tool
- Inform the owner of the design
- Examine the timing arc broken by PrimeTime
- If necessary, break the combinational loop manually

## Non Unate Cells(Arcs) in Clock Paths
- A non-unate path in clock network detected. Propagating both inverting and noninverting senses of clock 'CLK_TEST' from pin 'AND/Z'
- A non-unate path is one where:
  - The edge sensitivity is unclear in the library
  - The edge sensitivity is both positive and negative unate
- By default, PrimeTime propagates both senses

## Non Unateness and set_sense
- A non-unate path in clock network detected. Propagating both inverting and noninverting senses of clock 'CLK' from pin 'MUX/Z'
- In this example, a setup check uses: slowest(inverting) path for launch fastest(non-inverting) path for capture (pessimistic)
- We can restrict the clock sense with one of these set_sense commands:
```
set_sense -positive MUX/Z ; From MUX/Z,
propagate the non-inverting (pin A) path
(or)
set_sense -negative MUX/Z ; From MUX/Z,
propagate the inverting (pin B) path
```

## Summary
- Exercise the timing checks during STA
 - Recovery, Removal
 - Clock-gating checks
 - Data-to-Data checks
 - Minimum pulse width checks
- Analyze timing that involves
 - Latches with time borrowing
 - Multicycle paths
 - Combinational feedback loops
 - Non-unate cells along clock paths
