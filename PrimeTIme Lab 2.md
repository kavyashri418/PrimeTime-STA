## Writing constraints and validating them

## Requirement for Complete STA
- For complete static timing analysis, you must:
  - Completely constrain the design
  - Exercise all needed timing checks
 
- Two validation commands:
check_timing               issues missing constraint warnings
report_analysis_coverage   flags about untested timing checks

## Constraint Warning: No Input Delay
Given this warning, all we want to find out:
- What is the input port A connected to? (or is it unconnected?)
- What paths are affected by this warning? (or is case analysis missing on input port?)

## Constraint Warning: No Output Delay
Given this warning, all we want to find out:
- What is the output port M connected to? Register(s)? Input port(s)?
- What paths are affected by this warning? (or is this an outgoing clock port?)

## Constraints Warning: No Clock
Given this warning, all we want to find out:
- Where should a clock be created to drive the affected ports? (The CLK port in this example)

## Timing Checks
- The command ``` report_analysis_coverage ``` checks each exisitng timing check in a design and if any is unexercised issues a warning and a reason.
- Our job is to determine if the timing check is needed and then debug what is causing the timing check to be unexercised.

## Untested Timing Checks: false_paths
``` report_analysis_coverage ``` gives ``` false_paths ``` as the reason when a user specifies ``` set_false_path ```, asynchronous or exclusive clock groups.

```
set_false_path from F2 clock pin to F1 D pin
```

Given this warning, all we want to find out:
- What clocks are involved in these timing paths?
- What start point flops are connected to F1?
- Which false path(clock group) command caused this?

## Untested Timing Checks: constant_disabled
``` report_analysis_coverage ``` gives ``` constant_disabled ``` as a reason for propagated constants due to user specified ``` set_case_analysis ``` or to a signal tied high or low.
```
set_case_analysis 0 on scan_en port
```

Given this warning, all we want to find out:
- Are disabled timing arcs due to user specified case value or a signal tied high or low?
- Which user specified case value is causing this specified disabled timing check?

## Job Aid Commands

## Example Design with a Constraint Issue
Use the following command to find the start point on the clock network:
```
all_fanin -startpoints -flat -to F1/CLk
```

## How does all_fanin Work

## Further Details on Start Point
Use the following commands to find the start point cell details:
```
report_cell -connections -verbose F2
```

## Sketch Cell Connections

## Find the Master Clock for Defining Generated Clock
Use the following command to identify the master clock name for the div-by 2 flop:
```
get_attribute [get_pins F2/CLK] clocks
```

## Find disabled Timing Arcs Along Path
Use the following command to identify disabled arcs along the clock path:
```
report_disable_timing [all_fanin -only_cells -to F1/CLK -flat]
```

## Identify Causes for Disabled Arcs

## Find the user Specified Case Values
Use the following command to identify user specified case value:
```
report_case_analysis -to [get_pins U23/S]
```

## Dismiss the clock gatting logic as Valid Clock start points
```
all_fanout -flat -endpoints -from L1/Q
```

## Identifying Constraints from Timing Reports
- Clock Constraints
- Interface Constraints

## Clock Latency Components
Clock Network Delay (seen in a default timing report)
```
create_clock period 4 [get_ports Clk]
set_clock_latency -source 2 [get_clocks CLK]

## Pre Versus Post CTS
Ideal Clocks Pre CTS, user specifies clock network latency
Propagated Clocks Post CTS, PT calculates clock network latency
```
set_clock_latency 1 [get_clocks Clk]
(or)
set_propagated_clock [all_clocks]
```

## Specifying Clock Jitter
### What is Clock Jitter?
- Cycle-to-Cycle jitter -Variation between the edges that are in-phase (multiple clock cycles apart)
- Duty-Cycle jitter -Variation between the edges that are out-of-phase (0.5,1.5,2.5,... cycles apart)
- Clock jitter is used between the launch clocks and the capture clocks that are both generated from the same master clock
- Clock jitter is only applied to the end points

### Clock jitter can be specified reported and removed as needed
```
set_clock_jitter
report_clock_jitter
remove_clock_jitter
```

## Clock to Cycle Jitter
```
create_clock x -name mclk -period 10
set_clock_jitter -cycle 2 -duty_cycle 3 -clock mclk
```

## Duty - Cycle Jitter
```
create_clock x -name mclk -period 10
set_clock_jitter -cycle 2 -duty_cycle 3 -clock mclk
```

## No Jitter
```
create_clock x -name mclk -period 10
set_clock_jitter -cycle 2 -duty_cycle 3 -clock mclk
```

## Clock Uncertainity and Skew
Clock uncertainty is another constraint specified by the designer
```
set_clock_uncertainty 0.4 [get_clocks clk]
```
Pre CTS Clock Uncertainty = Clock Skew + Margin
Post CTS Clock Uncertainty = Margin
- Traditional specifications have combined clock jitter into clock uncertainty. However, the correct way to apply clock jitter woruld be by using set_clock_jitter

## Clock Constraints From Timing Report

## Reporting Clock Source Latency and Clock Network Cells
```
report_timing -path full_clock
```

## Interface Paths: Input Ports
Input delay is a constraint that represents the arrival times at the input ports of all design with respect to the launch clock.


## Input Delay Constraints From Timing Reports

## A Cleaner Solution
- One clean solution -include the clock latency as part of the input delay constraint
```
set_input_delay -network_latency_included \
    -source_latency_included -max 2.6 -clock clk [get_ports A]
```

- When debugging violations on interface paths - discuss how the clock latencies are being represented:
Network latency defined on ```Clk``` is used by IO path if "ideal" can not be used with "propagated" clocks

## Interface Paths: Output ports
- Output delay is a constraint that represents the setup and hold at the output ports of the design with respect to the capture clock.
```
set_output_delay 0.40 -max -clock -network -source [get_ports M]
```

## Output Delay Constraints From Timing Report

## Constraints Output Port for Setup and Hold requirements
```
set_output_delay -max
   Describes the external setup time requirement on the output ports
set_output_delay -min
   Describes the external hold time requirement on the output ports
```

```
set_output_delay -max 2.5 -clock Clk [all_outputs]
set_output_delay -min -0.2 -clock Clk [all_outputs]
```

## Summary
- Verify that the design is completely constrained using ``` check_timing ```
- Find number of timing checks in the design and how many are excercised/not excercised using ``` report_analysis_coverage ```
- Using a job Aid of 9 commands, debug the issues found above
- Identify the clock and interface constraints out of timing reports






































Objective: To understand how to validate the constraints and how to interpret constraints in a timing report.

### Relevant Files & Directories
```
.synopsys_pt.setup - automatically read PT setup file
orca_savesession - saved session directory
RUN.tcl - run script for orca_savesession
```

## Task 1: Validate Constraints
- Invoke PrimeTime and restore a previously saved PrimeTime session using the orca_savesession directory.
Note: The orca_savesession can be recreated, if needed, using:
```
pt_shell -f RUN.tcl | tee -i run. log
```
Note: Any PARA-124 errors during the execution of RUN.tcl can be safely ignored for the purpose of labs.

- Check for constraint completeness.
```
pt_shell> check_timing -verbose
```

- Check for the untested timing checks in the design
```
pt_shell> report_analysis_coverage
```

## Task 2: Analyse Timing Report for Input Delay Constraint
- Generate a report for the input delay constraints applied to the port pad [0].
```
pt_shell> report_port -input_delay pad[0]
```

## Task 3: Analyse Timing Report for Output Delay Constraint
- Generate a report for the output delay constraints applied to the port pad[0].
- Apply the following constraint which will impose a positive output delay constraint for hold on pad [0] and then re-execute the steps in this task to see the affect.
```
pt_shell> set_output_delay -min 1.0 -clock PCI_CLK pad[0]
```

- Quit PrimeTime.

