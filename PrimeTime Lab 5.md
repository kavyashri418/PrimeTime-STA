## Additional Checks and Constraints

## Objective - To understand how to add additional constraints apply user specified annotated delays to explore time borrowing with latches

## Asynchronous Clear/Reset Pins
<img width="478" height="341" alt="Screenshot 2026-01-31 015740" src="https://github.com/user-attachments/assets/cdb9e2f5-ee1d-4df7-8141-06b48988be33" />

## Timing Report Recovery
<img width="547" height="312" alt="Screenshot 2026-01-31 015756" src="https://github.com/user-attachments/assets/edf0e9e5-bd1e-460f-ac86-9e168e714ed3" />

## Path Groups
<img width="498" height="263" alt="Screenshot 2026-01-31 015807" src="https://github.com/user-attachments/assets/dcff835c-cf0b-4dd7-bf32-1b0a5459ca63" />

## Clock Gating Checks
<img width="606" height="209" alt="Screenshot 2026-01-31 015815" src="https://github.com/user-attachments/assets/71e39c32-991c-4c2c-b5e1-32295ecaeaba" />

## Which Clock Edges are used
<img width="541" height="159" alt="Screenshot 2026-01-31 015824" src="https://github.com/user-attachments/assets/017ce5cd-6368-42f6-a1c4-39ff8d021ab6" />
<img width="305" height="176" alt="Screenshot 2026-01-31 015831" src="https://github.com/user-attachments/assets/3fb980e3-dc1c-4372-94a6-91ba9c5ca085" />

## Timing Reports for Clock Gating
```
set_clock_gating_check -setup 0.20 [get_clocks SYS_CLK]
report_timing -to [get_pins U10/A]
```
<img width="537" height="303" alt="Screenshot 2026-01-31 015841" src="https://github.com/user-attachments/assets/aedd6328-4cf3-47c6-ae47-bfda936450fd" />

## Data-to-Data Check
<img width="560" height="371" alt="Screenshot 2026-01-31 015848" src="https://github.com/user-attachments/assets/c48110a0-391f-460e-9f32-0a84aef2da43" />

## Specifing Data-to-Data Checks
<img width="560" height="371" alt="Screenshot 2026-01-31 015848" src="https://github.com/user-attachments/assets/89d829dd-2766-4c62-9b91-140395ca1a97" />

## Another Application for a Data-to-Data Check
<img width="433" height="215" alt="Screenshot 2026-01-31 020002" src="https://github.com/user-attachments/assets/4f93510b-e01d-46ed-bbfc-291f6f3beef1" />

- WRT pin is driven by a data path, not a clock signal
- Setup and hold check can be specified between WRT and ADDR1
  - In Library or timing model as non-sequential setup and hold checks(or)
  - As a user-specified data-to-data check

## Clock Min Pulse width
<img width="566" height="373" alt="Screenshot 2026-01-31 020011" src="https://github.com/user-attachments/assets/3d143965-50cb-4f6a-955e-cb8722d8bfb8" />

## Summary Min Pulse Width Reports
<img width="537" height="240" alt="Screenshot 2026-01-31 020021" src="https://github.com/user-attachments/assets/c6fb446c-0d03-4cf4-9bc6-1441717f29d8" />

```
pt_shell> report_min_pulse_width -path_type short
```

## Summary Report for All Timing Checks
<img width="533" height="215" alt="Screenshot 2026-01-31 020030" src="https://github.com/user-attachments/assets/08f59806-4fe2-4b8a-afaa-bab979d7357e" />

## Latch Based Analysis
<img width="455" height="143" alt="Screenshot 2026-01-31 020043" src="https://github.com/user-attachments/assets/469b66ab-7b83-44f0-8c4b-ddbe975c7caa" />

- Latches are level-sensitive sequential devices
- A latch has a D->Q timing arc, in addition to G->Q
  - The D->Q arc is used if time borrowing is involved
  - The G->Q arc is used with zero time borrowing [register-like timing]
 
## Latches with Time Borrowing: Path to L2/D
<img width="555" height="355" alt="Screenshot 2026-01-31 020052" src="https://github.com/user-attachments/assets/a6344da5-b186-4efb-8d60-e99bc932d637" />

## Time Borrowing Information in Report
<img width="600" height="364" alt="Screenshot 2026-01-31 020105" src="https://github.com/user-attachments/assets/0177918f-5baa-4176-8f5c-8bf3c3c9ebf8" />

## Time Borrowed in Previous Stage is now Returned
From the report
     The time borrowed in previous stage returned = 0.85
     Startb point of timing path = L2/D pin
     D to Q time = 0.64
<img width="527" height="273" alt="Screenshot 2026-01-31 020113" src="https://github.com/user-attachments/assets/6aa25129-3915-4a00-90d3-18dbddbfc2a7" />

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
<img width="572" height="156" alt="Screenshot 2026-01-31 020124" src="https://github.com/user-attachments/assets/c14845e4-1eb4-41b4-84c9-cfe65e157aee" />

## Data Arrives Before Transparency: through LAT/D
- LAT/D is a through pin
- Timing path is in between register to register through latch
- Report contains open and close edges of transparency window
- Transparency window shows different latencies for open and close edges
- Arrival at LAT/Q = data arrival at LAT/D + the D-Q delay of latch
- Keyword "early" indicates data arrives LAT/D before transparency
<img width="230" height="307" alt="Screenshot 2026-01-31 020137" src="https://github.com/user-attachments/assets/562cd4cf-e6f2-4fd1-b639-5514773a9dc7" />

## Data Arrives During Transparency: through LAT/D
- Arrival at LAT/Q is the sum of data arrival at LAT/D and D-Q delay of latch
<img width="222" height="308" alt="Screenshot 2026-01-31 020146" src="https://github.com/user-attachments/assets/c8062313-ca71-4181-88a2-78aa916fc1c4" />

## Data Arrives After Transparency: through LAT/D
- This is a recovery path, since the transparency window is missed
- To avoid pessimism at downstream logic, the arrival time at LAT/D is limited to close edge
- Data at Q pin of the latch is the sum of close edge and D-Q delay of the latch
- Keyword "missed" indicates data arrives LAT/D after the close of transparency
<img width="233" height="324" alt="Screenshot 2026-01-31 020157" src="https://github.com/user-attachments/assets/844154ab-4ba6-4296-aa7f-bffe7f80b71b" />

## Multicycle Paths
<img width="388" height="263" alt="Screenshot 2026-01-31 020214" src="https://github.com/user-attachments/assets/a6d572ac-cbe9-4cf2-abc1-a441abd4fda1" />

## Default Reported Path is Single - Cycle
<img width="516" height="313" alt="Screenshot 2026-01-31 020223" src="https://github.com/user-attachments/assets/15c9bb6b-5515-4add-8076-5c2ea419aefd" />

## Specifying Multicycle Path for Hold
```
set multicycle_path -setup 6 -to [get_pins "*reg[*]/D"]
set_multicycle_path -hold 5 -to [get_pins "*reg[*]/D"]
```
<img width="574" height="266" alt="Screenshot 2026-01-31 020235" src="https://github.com/user-attachments/assets/34520abb-6ace-4af5-bca9-a0106361300c" />

## Reporting a Multicycle Path with ``` report_timing ```
<img width="599" height="308" alt="Screenshot 2026-01-31 020245" src="https://github.com/user-attachments/assets/50e4e495-4106-4d65-8ba1-b4ce33bb767f" />
<img width="621" height="325" alt="Screenshot 2026-01-31 020258" src="https://github.com/user-attachments/assets/60598b26-154f-490c-b620-e37a43a9210f" />

## Combinational Feedback Loops
<img width="559" height="312" alt="Screenshot 2026-01-31 020306" src="https://github.com/user-attachments/assets/db41b1a7-4e95-40a9-894d-1e96e1be300e" />

## STA and Combinational Loops
- Combinational loops are NOT verifiable using STA
- PrimeTime will disable a single timing arc to break this loop
<img width="245" height="126" alt="Screenshot 2026-01-31 020315" src="https://github.com/user-attachments/assets/608e3120-1840-4257-a21c-3f5f3ac9cec1" />

## Issues with Combinational Loops
- Verify timing of combo loop with a simulation tool
- Inform the owner of the design
- Examine the timing arc broken by PrimeTime
- If necessary, break the combinational loop manually
<img width="500" height="90" alt="Screenshot 2026-01-31 020324" src="https://github.com/user-attachments/assets/5c5818f0-fe81-4318-a415-db3253b5c174" />

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
<img width="534" height="159" alt="Screenshot 2026-01-31 020334" src="https://github.com/user-attachments/assets/a2e05ae8-ff49-4bb2-aa34-12fff6173bb1" />


## Relevant Directory
```
.synopsys_pt.setup - automatically read PT setup file
orca_savesession - saved session directory
logs/ - log files from run script
RUN.tcl - run script for ORCA
```

## Task 1: Debug PTE-070 Information Messages
- Invoke the PrimeTime additional Unix directory
```
pt shell - RUN.tcl | tee -i run.log
```

- Shown below is the full message regarding a non-unate path on the clock network. In the next step, you will be asked to generate a timing report through this pin. In order to copy and paste and avoid typos-either find this message in the log file from another terminal window or use the Uni command grep from within PrimeTime as shown
```
From ./run. log
Information: A non-unate path in clock
network detected.
Propagating both inverting and noninverting
senses of clock 'SDRAM CLK' from pin
'I_ORCA_TOP/I_SDRAM_IF/sd mux dq_out_0/z'
(PTB-070)
```

```
pt_shell> sh grep -A1 -B 1 PTE-070 run.log
```

- Note: The command sh (or alternatively exec) allows you to execute Unix commands from within the PrimeTime shell
- Generate a timing report for setup through the above pin
```
pt_shell> report_timing -input -through <through pin>
```

- Generate at least one additional timing report to show the use of a negative unate timing arc through the pin of interest

## Task 2: Debug PTE-070 Information Messages
- There is only one latch in this design. Use the following commands to find it. Take advantage of command and option completion with the tab key
```
pt shell> all_registers -level_eensitive
pt_shell> !! -clock_pin
pt_shell> all_registers -level_eensitive -data_pins
```

- Generate a timing report starting at the latch for setup time (be specific by using the clock pin as the start point and not just the cell name!). This lab will refer to this timing report as "path segment#2". The function oft his latch in the ORCA design is to generate a clock signal to turn on and off the clock SYS_CLK
- Generate a timing report for the previous stage (this lab will refer to this timing report as "path segment#1*) Use the D input pin of the latch as the end point of this timing path
- Force path segment#1 to borrow time from path segment#2 by annotating a net delay of 4ns as shown below:
```
Use cut and paste to avoid typos on the pin name
pt_shell> set_annotated_delay -net 4 \
         -to _ORCA_TOP/I_BLENDER/latched_clk_en_reg/D
```

- Generate the timing report for path segment#1 again(take advantage of the up and down arrows to scroll through the history event list)
- Re-generate the timing report for path segement#2
Note: The start point of the timing path will now be the D pin of the latch(not the clock pin as used before because you are interested in reporting the timing path that includes time borrow)
```
pt_shell> report_timing -from \
    I_ORCA_TOP/I_BLENDER/latched_clk_en_reg/D
```

- Change the latch behaviour for transparency, that is make it transparent when data arrives between the opening and closing edges of the clock
```
pt_shell> set_app_var timing_enable_through_paths true
```

- Repeat your timing report to the latch D pin. Notice that, even though the latch is transparent, you can still specify the D pin as an endpoint
```
pt_shell> report_timing -to \
     I_ORCA_TOP/I_BLENDER/latched_clk_en_reg/D
```

- Do a timing report FROM the start point you just identified
```
pt_shell> report_timing -from \
    I_ORCA_TOP/I_PARSER/blender_clk_en_reg/CP
```

- Quit PrimeTime

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
  
