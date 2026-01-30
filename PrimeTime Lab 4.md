## Constraining Multiple Clocks

## Clocks and STA: Three Types of Clocks
STA is dictated largely by the design clocks. Faster, easier debugging of timing violations with familiarity of the design clocks
- Three types of Clocks:
- Primary, Generated and Virtual
- Asynchronous, Synchronous, and Exclusive Clocks

## What are Primary Clocks?
- Primary clocks are typically created at input ports
```
create_clock -period 4 [get_ports Clk]
```
- Clocks are propagated through combinational logic

## Generated Clocks: Internally Derived Clocks
```
create generated clock -divide by 2 -name div clk -source [get ports Clk] FF3/Q
```

## Generated Clocks: Source Latency

## Generated Clocks: Determining Generated Clock Waveform
- A generated clock specification has:
  - Master source pin/port
  - Generated clock source pin/port
- What is the master source pin really used for?
- The master source pin/port is only used for:
  - Determining the master clock identity if- ``` master_clock ``` is not specified
  - Detecting the master clock's polarity (non-inverted or inverted)
- It is not used to steer the latency backtrace

## Zero Source Latency for Generated Clock: Problem
- Why is the clock source latency for the generated clock zero
- UITE-461 Error

## The Generated Clock Specified
- The design is
- The generated clock with ``` -divide_by 2 ``` is specified at Udiv/Q as below
```
create generated clock -name Clkdiv2 -divide by 2 -source [get_ports CLK] [get_pins Udiv/Q]
```

## Unsatisfiable Generated Clock
- Gen Clock: Generated clock specification assumes a rise_rise and rise_fall edge relationship
  - This edge relationship cannot exit as there is an inversion in the clock path
- Design: The falling edges at the input port CLK result in rising or falling edges at the output of the dividing flip-flop
  - That means that only fall_rise and fall_fall relationship can exist
- The design cannot provide the required propagation paths for this generated clock specifications, zero source latency is used

## Zero Source Latency for Generated Clock: Solution
- Modify the source pin of the generated clock specification
```
create_generated_clock -name CLKdiv2 -divide_by 2 -source [get_pins Udiv/CP] [get_pins Udiv/Q]
```

## Source Synchronous Interface
- The chip specification includes a min/max path requirement with respect to ``` Out_Clk ```
- The entire latency from the input port ``` My_Clk ``` to the output port ``` Out_Clk ``` is a component of the path requirement

## Source Synchronous Interface: Outgoing Generated Clock
- PrimeTime calculates source latency for generated clocks. (if primary clock is propagated)

## Use report_clock for all Clocks

## How many clocks are in your design?

## Ignorable ``` Check_timing ``` warning
- ``` check_timing ``` warns of output clock ports unconstrained for output delay - this warning can be ignored

## Source Latency Calculation Reporting for Generated Clocks
- Use the ``` full_clock_expanded ``` option to report source latency details for generated clocks

## Timing Between (A)synchronous Clocks

## Exhaustive Analysis!= Intended Analysis
- STA is exhaustive lead to analysis that is unintended
- For example, in the example above, the path between ``` Test_clk ``` and ``` Design_Clk ``` could be analyzed
- Clock relationships must, therefore, be defined correctly

## Interacting Clocks and Multiple STA Runs
- One way to ensure timing is verified under all SEL1/SEL2 combinations: 4 STA runs per corner using ``` set_case_analysis ```

## Single Analysis with Multiple Clocks!
- Step 1 Create all clocks
- Step 2 Specify exclusive clock groups
```
set_clock_groups -logically_exclusive -group CLK1 -group CLK2
set_clock_groups -logically_exclusive -group CLK3 -group CLK4
Now perform a single analysis run with all clock combinations
```

## Different Example: Multiple Clocks
```
set_clock_groups -name logic_excl_grp -logically_exclusive \
    -group "CLK1 CLK3" \
    -group "CLK2 CLK4"
Now perform a single analysis run with all clock combinations
```

## Asynchronous Clocks
- FF2 does not need to be checked for reliable data capture
```
specify asynchronous clock groups
set_clock_group -name async_group -asynchronous \
                -group Clk -group Clk2
```

## Identify All Clock Crossings

## How to perform these checks
- Interactively
```
pt_shell> restore_session orca_savesession
pt_shell> check_timing -v -override clock_crossing
```

- During Initial Run
```
lappend timing_check_defaults clock_crossing
Performs default checks + clock_crossing
check timing -verbose
```

## Interpret Timing Reports Between Clocks

## Clock Edges used for Setup and Hold
- PrimeTime picks the most restrictive pair of edges for setup and for hold. It determines which edges to use as follows:
  - Evaluate waveforms over the smallest common base period
  - For each capture edge, find the closest setup launch edge. Call these the primary pairs
  - Out of the primary pairs, pick the most restrictive setup launch and capture edges
  - For each primary pair, draw two hold relationships: Launch to (Capture-1); (Launch+1) to capture. From all of these hold relationships, pick the most restrictive
 
## Messages During Timing Updates
```
set_app_var timing_update_status_level high
```
Large base periods can alert the potential clock definition problems

## Timing Reports for Asynchronous Clocks'
- FF2 does not need to be checked for reliable data capture

## Reports for Unconstrained Paths

## Two General Guidelines
- There should be no constrained paths between asynchronous clock domains
```
report_clock -group
check_timing -v -override clock_crossing
```

- For the best runtime, turn off reporting of unconstrained paths when generating a large number of reports(warning UITE-413)
```
alias unc_on (set_app_var timing_report_unconstrained_paths true)
alias unc_off (set_app_var timing_report_unconstrained_paths false)
```

## Clock Reconvergence Pessimism (CRP)
- CRP = Latest arrival time to common point - Earliest arrival time to common point
- By default, PrimeTime removes clock reconvergence pessimism
```
timing_remove_clock_reconvergence_pessimism = "true"
```
## Finding CRP in a Timing Report

## Summary
- Gather basic information about the design clocks on a unfamiliar designs
- How many clocks
- What type and where are the clocks defined
- Which clocks are interacting
- What is Clock Reconvergence Pessimism Removal (CRPR)
