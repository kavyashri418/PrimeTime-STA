## Writing constraints and validating them

## Requirement for Complete STA
- For complete static timing analysis, you must:
  - Completely constrain the design
  - Exercise all needed timing checks
<img width="825" height="172" alt="Screenshot 2026-01-28 171734" src="https://github.com/user-attachments/assets/f2ff6946-1ce8-46d9-837a-3d2a76951114" />
 
- Two validation commands:
check_timing               issues missing constraint warnings
report_analysis_coverage   flags about untested timing checks

## Constraint Warning: No Input Delay
Given this warning, all we want to find out:
- What is the input port A connected to? (or is it unconnected?)
- What paths are affected by this warning? (or is case analysis missing on input port?)
<img width="842" height="189" alt="Screenshot 2026-01-28 171759" src="https://github.com/user-attachments/assets/4c096ab6-825c-4d6a-9a47-57265da3cec9" />

## Constraint Warning: No Output Delay
Given this warning, all we want to find out:
- What is the output port M connected to? Register(s)? Input port(s)?
- What paths are affected by this warning? (or is this an outgoing clock port?)
<img width="831" height="199" alt="Screenshot 2026-01-28 171810" src="https://github.com/user-attachments/assets/0ac7d2d2-559d-4eee-a1a8-50e4b4d3884b" />

## Constraints Warning: No Clock
Given this warning, all we want to find out:
- Where should a clock be created to drive the affected ports? (The CLK port in this example)
<img width="830" height="202" alt="Screenshot 2026-01-28 171822" src="https://github.com/user-attachments/assets/3f9e27f1-4975-4acc-92e1-4f7006aca88f" />

## Timing Checks
- The command ``` report_analysis_coverage ``` checks each exisitng timing check in a design and if any is unexercised issues a warning and a reason.
- Our job is to determine if the timing check is needed and then debug what is causing the timing check to be unexercised.
<img width="814" height="204" alt="Screenshot 2026-01-28 171834" src="https://github.com/user-attachments/assets/01d80a1e-07dc-4be0-8d1a-649383b3f9a9" />

## Untested Timing Checks: false_paths
``` report_analysis_coverage ``` gives ``` false_paths ``` as the reason when a user specifies ``` set_false_path ```, asynchronous or exclusive clock groups.
<img width="374" height="156" alt="Screenshot 2026-01-28 171848" src="https://github.com/user-attachments/assets/7542ed86-e8e3-4cae-a17b-1141de2be4a9" />
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
<img width="605" height="270" alt="Screenshot 2026-01-28 171901" src="https://github.com/user-attachments/assets/d78e1bc9-a477-4ebd-bac2-001dbb0bd2a8" />

Given this warning, all we want to find out:
- Are disabled timing arcs due to user specified case value or a signal tied high or low?
- Which user specified case value is causing this specified disabled timing check?

## Job Aid Commands
<img width="700" height="451" alt="Screenshot 2026-01-28 171915" src="https://github.com/user-attachments/assets/e7dcb422-6eed-49e4-a34f-d8e587b31aa0" />

## Example Design with a Constraint Issue
<img width="648" height="375" alt="Screenshot 2026-01-28 171929" src="https://github.com/user-attachments/assets/4f5005b6-be05-4208-aea5-f74923462557" />

Use the following command to find the start point on the clock network:
```
all_fanin -startpoints -flat -to F1/CLk
```

## How does all_fanin Work
<img width="671" height="399" alt="Screenshot 2026-01-28 171945" src="https://github.com/user-attachments/assets/53ddaa8a-dae9-47fb-90e0-1bf789fcc403" />

## Further Details on Start Point
Use the following commands to find the start point cell details:
```
report_cell -connections -verbose F2
```
<img width="685" height="365" alt="Screenshot 2026-01-28 172005" src="https://github.com/user-attachments/assets/db901a6f-302d-43f1-86e6-eabd29b745e3" />

## Sketch Cell Connections
<img width="703" height="401" alt="Screenshot 2026-01-28 172025" src="https://github.com/user-attachments/assets/92afbe16-c18b-4f77-a153-beea454bef78" />

## Find the Master Clock for Defining Generated Clock
Use the following command to identify the master clock name for the div-by 2 flop:
```
get_attribute [get_pins F2/CLK] clocks
```
<img width="740" height="356" alt="Screenshot 2026-01-28 172037" src="https://github.com/user-attachments/assets/6b42e5e9-1e24-4701-a79a-2953971c10e6" />

## Find disabled Timing Arcs Along Path
Use the following command to identify disabled arcs along the clock path:
```
report_disable_timing [all_fanin -only_cells -to F1/CLK -flat]
```
<img width="712" height="333" alt="Screenshot 2026-01-28 172046" src="https://github.com/user-attachments/assets/0a02c311-4f6d-4166-8acd-d6799fb0617a" />

## Identify Causes for Disabled Arcs
<img width="701" height="402" alt="Screenshot 2026-01-28 172056" src="https://github.com/user-attachments/assets/ee6c986d-0b0a-4766-8cd7-9cad4220dbe2" />

## Find the user Specified Case Values
Use the following command to identify user specified case value:
```
report_case_analysis -to [get_pins U23/S]
```
<img width="703" height="354" alt="Screenshot 2026-01-28 172109" src="https://github.com/user-attachments/assets/9a49e729-31d8-4471-ab2b-4d6e8a3de551" />

## Dismiss the clock gatting logic as Valid Clock start points
```
all_fanout -flat -endpoints -from L1/Q
```
<img width="744" height="430" alt="Screenshot 2026-01-28 172120" src="https://github.com/user-attachments/assets/de83f02e-3446-49d8-bee0-f2c8b9a3872d" />

## Identifying Constraints from Timing Reports
- Clock Constraints
- Interface Constraints

## Clock Latency Components
Clock Network Delay (seen in a default timing report)
```
create_clock period 4 [get_ports Clk]
set_clock_latency -source 2 [get_clocks CLK]
```
<img width="638" height="347" alt="Screenshot 2026-01-28 172136" src="https://github.com/user-attachments/assets/77c33db9-9161-4595-91fa-6c084d101654" />

## Pre Versus Post CTS
Ideal Clocks Pre CTS, user specifies clock network latency
Propagated Clocks Post CTS, PT calculates clock network latency
```
set_clock_latency 1 [get_clocks Clk]
(or)
set_propagated_clock [all_clocks]
```
<img width="688" height="213" alt="Screenshot 2026-01-28 172150" src="https://github.com/user-attachments/assets/9a0d663a-85d0-4c3c-a14e-efeadf98bfee" />

## Specifying Clock Jitter
<img width="333" height="125" alt="Screenshot 2026-01-28 172201" src="https://github.com/user-attachments/assets/5fa812fe-e201-4f16-ad3e-4d7474c44d9e" />


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
<img width="671" height="348" alt="Screenshot 2026-01-28 172214" src="https://github.com/user-attachments/assets/7169d395-f156-4006-8e28-2725ed8ad0d4" />

## Duty - Cycle Jitter
```
create_clock x -name mclk -period 10
set_clock_jitter -cycle 2 -duty_cycle 3 -clock mclk
```
<img width="669" height="336" alt="Screenshot 2026-01-28 172225" src="https://github.com/user-attachments/assets/89c2aa1f-1ce7-4a58-8466-874912cc3520" />

## No Jitter
```
create_clock x -name mclk -period 10
set_clock_jitter -cycle 2 -duty_cycle 3 -clock mclk
```
<img width="716" height="343" alt="Screenshot 2026-01-28 172238" src="https://github.com/user-attachments/assets/014d3a61-a659-4d81-98a0-428880457687" />

## Clock Uncertainity and Skew
Clock uncertainty is another constraint specified by the designer
```
set_clock_uncertainty 0.4 [get_clocks clk]
```
Pre CTS Clock Uncertainty = Clock Skew + Margin
Post CTS Clock Uncertainty = Margin
- Traditional specifications have combined clock jitter into clock uncertainty. However, the correct way to apply clock jitter woruld be by using set_clock_jitter
<img width="557" height="142" alt="Screenshot 2026-01-28 172247" src="https://github.com/user-attachments/assets/645fae4e-91ac-4458-9604-4643857edcb3" />

## Clock Constraints From Timing Report
<img width="529" height="392" alt="Screenshot 2026-01-28 172258" src="https://github.com/user-attachments/assets/dd087469-e9c2-4d07-a2a0-4d6e0df84f73" />

## Reporting Clock Source Latency and Clock Network Cells
```
report_timing -path full_clock
```
<img width="679" height="432" alt="Screenshot 2026-01-28 172308" src="https://github.com/user-attachments/assets/921643a5-ee0f-449e-b83b-c14403ebb9b7" />

## Interface Paths: Input Ports
Input delay is a constraint that represents the arrival times at the input ports of all design with respect to the launch clock.
<img width="625" height="280" alt="Screenshot 2026-01-28 172317" src="https://github.com/user-attachments/assets/47b06df9-eb68-46a9-89d6-f8bc9b171e58" />

## Input Delay Constraints From Timing Reports
<img width="517" height="314" alt="Screenshot 2026-01-28 172327" src="https://github.com/user-attachments/assets/cad5ee1b-4ee4-42b8-8f51-9151efb3ae27" />

## A Cleaner Solution
- One clean solution -include the clock latency as part of the input delay constraint
```
set_input_delay -network_latency_included \
    -source_latency_included -max 2.6 -clock clk [get_ports A]
```
<img width="661" height="268" alt="Screenshot 2026-01-28 172335" src="https://github.com/user-attachments/assets/2026a210-5ef6-4490-9294-554be7539e2f" />

- When debugging violations on interface paths - discuss how the clock latencies are being represented:
Network latency defined on ```Clk``` is used by IO path if "ideal" can not be used with "propagated" clocks

## Interface Paths: Output ports
- Output delay is a constraint that represents the setup and hold at the output ports of the design with respect to the capture clock.
```
set_output_delay 0.40 -max -clock -network -source [get_ports M]
```
<img width="663" height="256" alt="Screenshot 2026-01-28 172346" src="https://github.com/user-attachments/assets/d205005a-5116-4cc2-83b7-a8b3a79132d1" />

## Output Delay Constraints From Timing Report
<img width="554" height="335" alt="Screenshot 2026-01-28 172359" src="https://github.com/user-attachments/assets/967ae103-78bc-4056-ac33-5573221d221b" />

## Constraints Output Port for Setup and Hold requirements
```
set_output_delay -max
   Describes the external setup time requirement on the output ports
set_output_delay -min
   Describes the external hold time requirement on the output ports
```
<img width="493" height="233" alt="Screenshot 2026-01-28 172412" src="https://github.com/user-attachments/assets/08e543e6-9cc7-477e-9ffd-e5069ee1fe2e" />

```
set_output_delay -max 2.5 -clock Clk [all_outputs]
set_output_delay -min -0.2 -clock Clk [all_outputs]
```

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

## Summary
- Verify that the design is completely constrained using ``` check_timing ```
- Find number of timing checks in the design and how many are excercised/not excercised using ``` report_analysis_coverage ```
- Using a job Aid of 9 commands, debug the issues found above
- Identify the clock and interface constraints out of timing reports.


