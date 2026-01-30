## Generate Timing Reports and Analysing them

## Timing Analysis Flow in PrimeTime Generating Reports
- STA flow is divided into several steps as seen below
- Steps 1-3 data
- Analysis begin at step 4

## Generating Reports Methodology Summary Reports

## Quality of Report: report_qor
```
pt_shell> report_qor
```
Two violations to the same endpoint but in different path groups are reported as two different violations.

## Where are the Violations? report_global_timing
- Are violations internal or in I/O?
```
pt_shell> report_global_timing
``` 
By default, each violating endpoint is only counted once and only one worst slack at each violating endpoint contributes to the total negative slack.

## Slack Summary at pins/ports: report_global_slack
- Reporting setup and hold slacks in the design (or at specified pins and ports)
```
pt_shell> report_global_slack [all_outputs]
```

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

Lists all violating endpoints sorted by slack within each path group. Path groups are displayed in alphabetical order

## checks Exercised: report_analysis_coverage
```
pt_shell> report_analysis_coverage
```

## Listing All pins with Setup Violations
```
report_analysis_coverage -status violated -check setup
```
This command reports the number of violating endpoints, not the number of violations. There could be multiple violations per endpoint

## Examples Using report_analysis_coverage 
- Listing output port names that have untested ``` out_setup ``` checks
- Listing output port names with untested out_setup checks and excluding untested no_paths

## Generating Reports Methodology: Detailed Reports

## Timing Path Details: Built From Timing Arcs
- PrimeTime assembles timing arcs into paths
- report_timing displays the arcs o paths
- Cell timing arcs are defined in the library
  - Propagation Delay
  - Timing Check
 - Net timing arcs are defined by the netlist and back-annotated data

## Common Types of Timing Arcs in Libraries
 Timing arc "unate" specified how the output changes relative to input transitions

## Rise/Fall Transitions Along a Timing Path
 - FF1 rising edge flop=> 'r' at FF1/CLK
 - FF1/Q can transition from 0->1 or 1->0
 - Worst hold slack is reported with a "f" transition at FF!/Q

## Generating Timing Reports Specifing Endpoint Transition
```
report_timing -tc FF2/D -delay max_fall
```

## Reporting Library Arc: Inverter
Use the following commands to list all loaded libraries in PrimeTime memory
- list_libs
- list_libraries
- get_libs

## Reporting Library Arcs: FLip-Flop

## Default Behaviour of report_timing
- The command report_timing, by default
  - generates one report
  - with the worst slack
  - in the entire design
  - for setup time
 
## report_timing for Each Path Group
 - To generate one report
   - with the worst slack
   - for each path group
   - for setup time

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

## Exercise on nworst vs. max_paths Options

## Reporting Multiple Paths to a Single Endpoint
```
report_timing -to FF4/D -max_paths 3 ---> 1 path is reported
```
```
report_timing -to FF4/D -nworst 3 ----> 3 paths are reported
```

## Cover Design Reporting (report_timing -cover_design)
Need: To report the worst case path through every violating pin in the design
Eliminates the need for guessing the correct max_paths/nworst settings!

## Reporting Violating Paths Through Pin Sets
Need: To report violating paths going through given pins
```
report_timing -cover_design -through {U1/A A2/Z}
```
5 paths are returned with slacks -3, -2.5, -2, -1 and -0.6

## Reporting Worst Violatng Paths through Pin Sets
Need: To report the worst violating paths going through given pins
```
report_timing -cover_through {U1/A A2/Z}
```
2 paths are returned with slacks -3 and -2

## Reporting Summary Using report_timing
Top 10 timing paths for hold captured by the falling edge of SRAM_CLK and reported as a summary

## Another Summary Report Using report_timing
Example: Reporting a summary of top 10 violating endpoints for setup for the clock SYS_2x_CLK

## report_timing for Specific Path Type
- The ``` start_end_type ``` option enables focused reporting of one of four classes of paths:
 - reg_to_reg -from register to register
 - reg_to_out -from register to output port
 - in_to_reg -from input port to register
 - in_to_reg -from input port to output port

## Filtering Paths by Slack Value

## Unexpected Report with ``` report_timing -slack_greater ```
- When use the ``` report_timing ``` command with the ```-slack_greater_than ``` 0 option, PrimeTime reports that there are no paths
- However, if I increase the nworst value, PrimeTime reports the paths, Why?

## With a Large -nworst: report_timing -slack_greater
- When nworst is increased, PrimeTime reports paths with slack>zero
- The -slack_greater_than option is a post-processing filter
 - Step 1: PrimeTime gathers the specified paths without the -slack_greater_than option
 - Step 2: Applies the filtering for slack greater than 0

## Reporting Paths From, To, Through or Exclude
Examples of
- Start points: Non-clock input ports, clock pins or a clock object
- End points: Output ports, non-clock input pins of registers/latches, clock object
- Through/Exclude points Any pin, port or net
Can also use te following "shortcuts" for -to and -from:
- Clock names (objects)
- Register/Latch cell names (not recommended)

## Clock ports vs Clock Objects
```
report_timing -from [get_ports clk] will return: (not a valid start point(port with a clock definition))
- Nothing-this is not a timing path
- A single timing path from the clk port
report_timing -from [get_clocks clk] will return:
- Nothing-this is not a timing path
- A single, worst timing path for setup launched by the clock clk
```

## Recommendations - Being Specific
- Use the specific pin names, not just the cell name
 - Best runtime
 - Get the Intended timing paths

## Half Cycle Path: Clock Edges Used for Setup and Hold


## Reporting a Half Cycle Path: Setup
```
report_timing fall_from [get_clocks clk] rise_to [get_clocks clk]
```

## Generating Reports Methodology: Delay Calculation

## Including Input Pins in a Timing Report

## Cell Delay Calculation Report

## Net Delay Calculation Report

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



 
