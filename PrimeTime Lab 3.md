9## Generate Timing Reports and Analysing them

## Timing Analysis Flow in PrimeTime Generating Reports
- STA flow is divided into several steps as seen below
- Steps 1-3 data
- Analysis begin at step 4
<img width="555" height="192" alt="Screenshot 2026-01-31 023238" src="https://github.com/user-attachments/assets/7c02ea73-5011-47b2-9fb0-9879dfc115c6" />

## Generating Reports Methodology Summary Reports
<img width="627" height="364" alt="Screenshot 2026-01-31 023247" src="https://github.com/user-attachments/assets/b7068e22-146d-4961-9bfe-bfd054706e8d" />

## Quality of Report: report_qor
```
pt_shell> report_qor
```
Two violations to the same endpoint but in different path groups are reported as two different violations.
<img width="540" height="322" alt="Screenshot 2026-01-31 023301" src="https://github.com/user-attachments/assets/0696b2c4-ee41-4039-8e8c-b344ebfd79d4" />

## Where are the Violations? report_global_timing
- Are violations internal or in I/O?
```
pt_shell> report_global_timing
``` 
By default, each violating endpoint is only counted once and only one worst slack at each violating endpoint contributes to the total negative slack.
<img width="578" height="240" alt="Screenshot 2026-01-31 023331" src="https://github.com/user-attachments/assets/487a4b77-f7bc-42a0-9543-8f16120dc91d" />

## Slack Summary at pins/ports: report_global_slack
- Reporting setup and hold slacks in the design (or at specified pins and ports)
```
pt_shell> report_global_slack [all_outputs]
```
<img width="623" height="129" alt="Screenshot 2026-01-31 023347" src="https://github.com/user-attachments/assets/cfaf88ec-d371-47d8-b370-ad2fca9ba740" />

Reports worst setup and hold slack among all paths through pins/ports specified (Default: all pins/ports of the design)
- A "*" indicates the absence of slack information at a given pin/port
- To prevent potentially long runtime, prior to running the report, use:
```
set_app_var timing_save_pin_arrival_and_slack true
```

## Violations Sorted By Clock: report_constraint -all
Some benefits of organizing violations by clock domain:
- Some clocks may be more critical than others
- Clock skew or a "known" issue with a particular clock may be causing multiple violations
<img width="497" height="226" alt="Screenshot 2026-01-31 023357" src="https://github.com/user-attachments/assets/74089b05-4b35-4972-b853-79bcbec18ec6" />

Lists all violating endpoints sorted by slack within each path group. Path groups are displayed in alphabetical order

## checks Exercised: report_analysis_coverage
```
pt_shell> report_analysis_coverage
```
<img width="656" height="188" alt="Screenshot 2026-01-31 023406" src="https://github.com/user-attachments/assets/bb92f81d-2737-4067-88f8-3bd0dd738885" />

## Listing All pins with Setup Violations
```
report_analysis_coverage -status violated -check setup
```
This command reports the number of violating endpoints, not the number of violations. There could be multiple violations per endpoint
<img width="611" height="264" alt="Screenshot 2026-01-31 023417" src="https://github.com/user-attachments/assets/3ca7af83-ef5f-4087-905e-ad772a74eb91" />

## Examples Using report_analysis_coverage 
- Listing output port names that have untested ``` out_setup ``` checks
<img width="602" height="108" alt="Screenshot 2026-01-31 023426" src="https://github.com/user-attachments/assets/1d4acbcb-fc9e-4876-822e-73e6df121b72" />

- Listing output port names with untested out_setup checks and excluding untested no_paths
<img width="608" height="123" alt="Screenshot 2026-01-31 023436" src="https://github.com/user-attachments/assets/11b475ff-a162-4547-9c72-9e3bc0aa070b" />

## Generating Reports Methodology: Detailed Reports
<img width="636" height="374" alt="Screenshot 2026-01-31 023446" src="https://github.com/user-attachments/assets/5df0b3c6-0da0-4281-b45d-e2b3103a75ee" />

## Timing Path Details: Built From Timing Arcs
- PrimeTime assembles timing arcs into paths
- report_timing displays the arcs o paths
- Cell timing arcs are defined in the library
  - Propagation Delay
  - Timing Check
 - Net timing arcs are defined by the netlist and back-annotated data
<img width="421" height="173" alt="Screenshot 2026-01-31 023453" src="https://github.com/user-attachments/assets/9a958c76-74c8-4d1b-9dde-033b937759c2" />

## Common Types of Timing Arcs in Libraries
 Timing arc "unate" specified how the output changes relative to input transitions
<img width="620" height="332" alt="Screenshot 2026-01-31 023504" src="https://github.com/user-attachments/assets/9a67e147-c092-4207-9922-459b547cd028" />

## Rise/Fall Transitions Along a Timing Path
 - FF1 rising edge flop=> 'r' at FF1/CLK
 - FF1/Q can transition from 0->1 or 1->0
 - Worst hold slack is reported with a "f" transition at FF!/Q
<img width="433" height="269" alt="Screenshot 2026-01-31 023512" src="https://github.com/user-attachments/assets/b057876b-e2fd-4f11-a9a3-338dbeb382cf" />

## Generating Timing Reports Specifing Endpoint Transition
```
report_timing -tc FF2/D -delay max_fall
```
<img width="653" height="282" alt="Screenshot 2026-01-31 023523" src="https://github.com/user-attachments/assets/d9df759f-4554-4bee-a17f-d409732faecb" />

## Reporting Library Arc: Inverter
Use the following commands to list all loaded libraries in PrimeTime memory
- list_libs
- list_libraries
- get_libs
<img width="654" height="343" alt="Screenshot 2026-01-31 023531" src="https://github.com/user-attachments/assets/53367bcc-8a6d-49bc-afeb-bcb3dd238625" />

## Reporting Library Arcs: FLip-Flop
<img width="562" height="361" alt="Screenshot 2026-01-31 023541" src="https://github.com/user-attachments/assets/14093e2a-f05f-497d-9ee4-7aeb4cbe1c20" />

## Default Behaviour of report_timing
- The command report_timing, by default
  - generates one report
  - with the worst slack
  - in the entire design
  - for setup time
 <img width="328" height="227" alt="Screenshot 2026-01-31 023549" src="https://github.com/user-attachments/assets/04c6e199-9a20-4d4c-a555-81fcd71ead0d" />

## report_timing for Each Path Group
 - To generate one report
   - with the worst slack
   - for each path group
   - for setup time
<img width="659" height="212" alt="Screenshot 2026-01-31 023559" src="https://github.com/user-attachments/assets/3b4823e1-bce0-4674-921b-7a2b04028567" />

## The nworst vs. max_paths Options of report_timing
-nworst: Consider these many paths per endpoint
 - Defaults to 1
 - If -nworst>1
    - max_paths=nworst
    - slack_lesser_than = 0
-max_paths: Display these many paths in the design
 - Defaults to the nworst setting
 - If-max_paths>1
   - slack_lesser_than=0
<img width="257" height="346" alt="Screenshot 2026-01-31 023608" src="https://github.com/user-attachments/assets/5209f51b-cecd-45fd-9f83-ec8f2cd56f36" />

## Exercise on nworst vs. max_paths Options
<img width="653" height="345" alt="Screenshot 2026-01-31 023617" src="https://github.com/user-attachments/assets/9711ba2c-ac11-4670-a32f-734a371c3845" />

## Reporting Multiple Paths to a Single Endpoint
```
report_timing -to FF4/D -max_paths 3 ---> 1 path is reported
```
```
report_timing -to FF4/D -nworst 3 ----> 3 paths are reported
```
<img width="551" height="175" alt="Screenshot 2026-01-31 023643" src="https://github.com/user-attachments/assets/caf572c5-5894-42ab-8820-bd57037b58fa" />

## Cover Design Reporting (report_timing -cover_design)
Need: To report the worst case path through every violating pin in the design
Eliminates the need for guessing the correct max_paths/nworst settings!

<img width="592" height="215" alt="Screenshot 2026-01-31 023654" src="https://github.com/user-attachments/assets/efd651a6-e26a-4765-aacc-29fc3742bb4d" />

## Reporting Violating Paths Through Pin Sets
Need: To report violating paths going through given pins
```
report_timing -cover_design -through {U1/A A2/Z}
```
5 paths are returned with slacks -3, -2.5, -2, -1 and -0.6
<img width="589" height="233" alt="Screenshot 2026-01-31 023701" src="https://github.com/user-attachments/assets/3d577478-c4f1-4d69-81fd-a78baba26d1b" />

## Reporting Worst Violatng Paths through Pin Sets
Need: To report the worst violating paths going through given pins
```
report_timing -cover_through {U1/A A2/Z}
```
2 paths are returned with slacks -3 and -2
<img width="620" height="226" alt="Screenshot 2026-01-31 023709" src="https://github.com/user-attachments/assets/17967b9e-962a-4167-aea8-863e89d1f41e" />

## Reporting Summary Using report_timing
Top 10 timing paths for hold captured by the falling edge of SRAM_CLK and reported as a summary
<img width="504" height="229" alt="Screenshot 2026-01-31 023718" src="https://github.com/user-attachments/assets/ac8d712a-d1ce-4348-9f08-f58d6e0fc8b8" />

## Another Summary Report Using report_timing
Example: Reporting a summary of top 10 violating endpoints for setup for the clock SYS_2x_CLK
<img width="655" height="185" alt="Screenshot 2026-01-31 023728" src="https://github.com/user-attachments/assets/8b114be6-aa10-48a7-b049-e064a9de6d19" />

## report_timing for Specific Path Type
- The ``` start_end_type ``` option enables focused reporting of one of four classes of paths:
 - reg_to_reg -from register to register
 - reg_to_out -from register to output port
 - in_to_reg -from input port to register
 - in_to_reg -from input port to output port
<img width="637" height="224" alt="Screenshot 2026-01-31 023738" src="https://github.com/user-attachments/assets/b2d9ad3f-8370-4fbc-b880-359b2794e06d" />

## Filtering Paths by Slack Value
<img width="655" height="332" alt="Screenshot 2026-01-31 023750" src="https://github.com/user-attachments/assets/83f1bdf7-88ef-41d0-aa08-ad53442a8826" />

## Unexpected Report with ``` report_timing -slack_greater ```
- When use the ``` report_timing ``` command with the ```-slack_greater_than ``` 0 option, PrimeTime reports that there are no paths
- However, if I increase the nworst value, PrimeTime reports the paths, Why?
<img width="483" height="193" alt="Screenshot 2026-01-31 023759" src="https://github.com/user-attachments/assets/dc913083-b7cd-4cc6-becf-da75666a399f" />

## With a Large -nworst: report_timing -slack_greater
- When nworst is increased, PrimeTime reports paths with slack>zero
- The -slack_greater_than option is a post-processing filter
 - Step 1: PrimeTime gathers the specified paths without the -slack_greater_than option
 - Step 2: Applies the filtering for slack greater than 0
<img width="645" height="260" alt="Screenshot 2026-01-31 023809" src="https://github.com/user-attachments/assets/6bd50885-f56e-4d6f-8575-8960ad8454b0" />

## Reporting Paths From, To, Through or Exclude
Examples of
- Start points: Non-clock input ports, clock pins or a clock object
- End points: Output ports, non-clock input pins of registers/latches, clock object
- Through/Exclude points Any pin, port or net
Can also use te following "shortcuts" for -to and -from:
- Clock names (objects)
- Register/Latch cell names (not recommended)
<img width="373" height="220" alt="Screenshot 2026-01-31 023832" src="https://github.com/user-attachments/assets/e2c52d5a-678f-46d0-ab3a-f9a2c5c27f90" />

## Clock ports vs Clock Objects
report_timing -from [get_ports clk] will return: (not a valid start point(port with a clock definition))
- Nothing-this is not a timing path
- A single timing path from the clk port
report_timing -from [get_clocks clk] will return:
- Nothing-this is not a timing path
- A single, worst timing path for setup launched by the clock clk

<img width="599" height="205" alt="Screenshot 2026-01-31 023843" src="https://github.com/user-attachments/assets/8afb999d-e2c6-4e70-af3b-3e77bc481294" />

## Recommendations - Being Specific
- Use the specific pin names, not just the cell name
 - Best runtime
 - Get the Intended timing paths
<img width="183" height="172" alt="Screenshot 2026-01-31 023852" src="https://github.com/user-attachments/assets/3f100f0c-bb14-4783-8da9-3f22f0de558c" />

## Half Cycle Path: Clock Edges Used for Setup and Hold
<img width="621" height="358" alt="Screenshot 2026-01-31 023912" src="https://github.com/user-attachments/assets/394d2aa3-93f3-4688-9965-e0b72ac86017" />

## Reporting a Half Cycle Path: Setup
```
report_timing fall_from [get_clocks clk] rise_to [get_clocks clk]
```
<img width="644" height="330" alt="Screenshot 2026-01-31 023922" src="https://github.com/user-attachments/assets/be908e42-6525-4a35-9406-ece930da470e" />

## Generating Reports Methodology: Delay Calculation
<img width="612" height="366" alt="Screenshot 2026-01-31 023929" src="https://github.com/user-attachments/assets/52ad349c-de61-4d09-871f-228f99d739e9" />

## Including Input Pins in a Timing Report
```
pt_shell> report_timing
```
<img width="378" height="124" alt="Screenshot 2026-01-31 023939" src="https://github.com/user-attachments/assets/9c281245-2bd5-406f-8a7e-2c979efcaafc" />

```
pt_shell> report_timing -input_pins
```
<img width="648" height="147" alt="Screenshot 2026-01-31 023948" src="https://github.com/user-attachments/assets/2aad5e03-6eec-4bd5-8dfe-90c6597bea51" />

## Cell Delay Calculation Report
<img width="660" height="320" alt="Screenshot 2026-01-31 024000" src="https://github.com/user-attachments/assets/dd94da0f-bac1-44a0-be4c-5e58955815cc" />

## Net Delay Calculation Report
<img width="658" height="300" alt="Screenshot 2026-01-31 024010" src="https://github.com/user-attachments/assets/382648f0-f7a2-4dd0-8014-fbffcf14e96b" />

Objective - To understand how to generate timing reports and understand how to analyze them.

## Relevant Directory
```
.synopsys_pt.setup - automatically read PT setup file
orca_savesession - saved session directory
RUN.tcl - run script for orca_savesession
```

## Task 1: Setup PrimeTime for Lab3
Invoke PrimeTime from the lab3 reports workshop lab directory. Restore PrimeTime session using the orca_savesession directory.
Note: The orca_savesession can be recreated, if needed using:
```
pt shell - RUN.tcl | tee -i run. log
```

Note: Any PARA-124 errors during the execution of RUN.tcl can be safely ignored for the purpose of our labs.

- Find the variable that controls the significant digits for many reports and set it to 4 significant digits.

## Task 2: Analyse Timing Reports for Setup and Hold
- Turn Page Mode on.
- Execute the following command to generate a timing report for PCI CLK.
```
pt_shell> report_timing -group PCI_CLK
```

- Generate a timing report for hold time for the same clock group PCI_CLK.
```
pt_shell> report_timing -group PCI_CLK -delay min
```

- Generate another timing report for the same timing path for holf time but with a fall transition at the end point (instead of a rise transition). Use copy and paste to avoid mistyping the end point and start point pin names. Use the job aid labelled "timing reports" to find the appropriate switches for report_timing.

## Task 3: Identify Halk-Clock Cycle Paths
- The clock SDRAM_CLK constrains many half clock cycle paths in ORCA (ie it constraints paths from a falling edge triggered flip-flop to a rising edge triggered flip-flop and vice versa). These paths must be carefully monitored for various reasons (eg the duty cycle of SDRAM_CLK is not yet well defined or for analysis of the clock skew).
- Execute the following command to report the clock period for SDRAM CLK and use this information to answer the following questions.
```
pt_shell> report_clock SDRAM_CLK
```

- Confirm the information in the following table by generating the appropriate timing reports for the half clock cycle timing paths constrained by the clock SDRAM_CLK.

| Launch Clock Edge | Capture Clock Edge | Worst Setup Slack | Launch Clock Edge | Capture Clock Edge | Worst Hold Slack |
|----------|----------|----------|----------|----------|----------|
| Rise 0ns  | Fall 3.75ns | 0.680ns | Rise 7.5ns | Fall 3.75ns | 3.558ns |
| Fall 3.75ns   | Rise 7.50ns  | 0.635ns | Fall 3.7ns  | Rise 0ns  | 3.514ns |

- Quit PrimeTime.

## Summary
- Generate summary information for violations sorted by slack, timing check or clock group
- Generate detailed information for cell, net delays and of the paths of interest
- Get explanation for cell and net delays reported



 
