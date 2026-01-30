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
<img width="530" height="172" alt="Screenshot 2026-01-31 021408" src="https://github.com/user-attachments/assets/aa7a205a-2e3c-4753-9330-bd7da813c726" />

## Generated Clocks: Internally Derived Clocks
```
create generated clock -divide by 2 -name div clk -source [get ports Clk] FF3/Q
```
<img width="607" height="263" alt="Screenshot 2026-01-31 021425" src="https://github.com/user-attachments/assets/d314ed92-ee0b-4989-bfd2-936dbda49c21" />

## Generated Clocks: Source Latency
<img width="637" height="336" alt="Screenshot 2026-01-31 021438" src="https://github.com/user-attachments/assets/a040abb5-c44c-4de6-9fcb-d417ce695278" />

## Generated Clocks: Determining Generated Clock Waveform
- A generated clock specification has:
  - Master source pin/port
  - Generated clock source pin/port
- What is the master source pin really used for?
- The master source pin/port is only used for:
  - Determining the master clock identity if- ``` master_clock ``` is not specified
  - Detecting the master clock's polarity (non-inverted or inverted)
- It is not used to steer the latency backtrace
<img width="281" height="292" alt="Screenshot 2026-01-31 021454" src="https://github.com/user-attachments/assets/d434a39f-40ee-46d9-b0a6-234b19f7acff" />

## Zero Source Latency for Generated Clock: Problem
- Why is the clock source latency for the generated clock zero
<img width="322" height="141" alt="Screenshot 2026-01-31 021518" src="https://github.com/user-attachments/assets/9cf84fbf-c316-4aa4-9d87-d8428174afec" />
 
- UITE-461 Error
<img width="632" height="56" alt="Screenshot 2026-01-31 021526" src="https://github.com/user-attachments/assets/d76b0338-ce6b-48c9-8367-2353e1f9f055" />

## The Generated Clock Specified
- The design is
<img width="524" height="87" alt="Screenshot 2026-01-31 021536" src="https://github.com/user-attachments/assets/4830553f-5733-4eac-b16f-127faf65047c" />

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
<img width="312" height="273" alt="Screenshot 2026-01-31 021545" src="https://github.com/user-attachments/assets/a3a65a39-1d32-4156-aa87-72d9c1cd761d" />

## Zero Source Latency for Generated Clock: Solution
- Modify the source pin of the generated clock specification
```
create_generated_clock -name CLKdiv2 -divide_by 2 -source [get_pins Udiv/CP] [get_pins Udiv/Q]
```
<img width="489" height="109" alt="Screenshot 2026-01-31 021554" src="https://github.com/user-attachments/assets/8cce77eb-45fa-42f2-9f76-03023026bba5" />
<img width="300" height="125" alt="Screenshot 2026-01-31 021606" src="https://github.com/user-attachments/assets/ecc5f923-ff1d-4cfd-a936-b118b0f5c128" />

## Source Synchronous Interface
- The chip specification includes a min/max path requirement with respect to ``` Out_Clk ```
- The entire latency from the input port ``` My_Clk ``` to the output port ``` Out_Clk ``` is a component of the path requirement
<img width="387" height="248" alt="Screenshot 2026-01-31 021614" src="https://github.com/user-attachments/assets/8296d73e-1200-468d-8525-9659739ff25b" />

## Source Synchronous Interface: Outgoing Generated Clock
- PrimeTime calculates source latency for generated clocks. (if primary clock is propagated)
<img width="439" height="210" alt="Screenshot 2026-01-31 021626" src="https://github.com/user-attachments/assets/312ab0c4-3876-4e59-8b6b-85632d0c637d" />

## Use report_clock for all Clocks
<img width="542" height="282" alt="Screenshot 2026-01-31 021640" src="https://github.com/user-attachments/assets/ee2cd273-dc9a-48d0-b19c-18a13da66ac3" />

## How many clocks are in your design?
<img width="581" height="152" alt="Screenshot 2026-01-31 021652" src="https://github.com/user-attachments/assets/0251e442-4526-435d-b2e8-6f58a3e5a5d3" />

## Ignorable ``` Check_timing ``` warning
- ``` check_timing ``` warns of output clock ports unconstrained for output delay - this warning can be ignored
<img width="514" height="250" alt="Screenshot 2026-01-31 021701" src="https://github.com/user-attachments/assets/356deb3f-2e72-48aa-ab1a-b753e35a8a25" />

## Source Latency Calculation Reporting for Generated Clocks
- Use the ``` full_clock_expanded ``` option to report source latency details for generated clocks
<img width="598" height="275" alt="Screenshot 2026-01-31 021711" src="https://github.com/user-attachments/assets/52e7509d-f0ab-4f1b-b7c0-2fbfb1a825df" />

## Timing Between (A)synchronous Clocks
<img width="594" height="373" alt="Screenshot 2026-01-31 021720" src="https://github.com/user-attachments/assets/ebec4442-aca4-4b2a-902a-61dd653df093" />

## Exhaustive Analysis!= Intended Analysis
- STA is exhaustive lead to analysis that is unintended
- For example, in the example above, the path between ``` Test_clk ``` and ``` Design_Clk ``` could be analyzed
- Clock relationships must, therefore, be defined correctly
<img width="557" height="166" alt="Screenshot 2026-01-31 021729" src="https://github.com/user-attachments/assets/80c684a7-cd8c-4cc6-ba4e-92594b9a80cb" />

## Interacting Clocks and Multiple STA Runs
- One way to ensure timing is verified under all SEL1/SEL2 combinations: 4 STA runs per corner using ``` set_case_analysis ```
<img width="566" height="224" alt="Screenshot 2026-01-31 021738" src="https://github.com/user-attachments/assets/0c15ce73-884b-43de-a8e7-32759566cd82" />

## Single Analysis with Multiple Clocks!
- Step 1 Create all clocks
<img width="580" height="217" alt="Screenshot 2026-01-31 021753" src="https://github.com/user-attachments/assets/ac04e3ac-acf1-470f-bf8b-080bd346858a" />

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
<img width="563" height="221" alt="Screenshot 2026-01-31 021815" src="https://github.com/user-attachments/assets/c73d0dd9-08d8-4f24-b706-42f87fc271e3" />

## Asynchronous Clocks
- FF2 does not need to be checked for reliable data capture
<img width="557" height="185" alt="Screenshot 2026-01-31 021821" src="https://github.com/user-attachments/assets/ee6a031c-9fb8-42ea-b951-01f14450ae51" />

```
specify asynchronous clock groups
set_clock_group -name async_group -asynchronous \
                -group Clk -group Clk2
```

## Identify All Clock Crossings
<img width="530" height="251" alt="Screenshot 2026-01-31 021831" src="https://github.com/user-attachments/assets/4420fecc-4e02-4c27-8646-f32780244f0a" />

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
<img width="481" height="272" alt="Screenshot 2026-01-31 021849" src="https://github.com/user-attachments/assets/75c312ee-1476-4e2b-962d-7ad7bf52b3ba" />

## Clock Edges used for Setup and Hold
- PrimeTime picks the most restrictive pair of edges for setup and for hold. It determines which edges to use as follows:
  - Evaluate waveforms over the smallest common base period
  - For each capture edge, find the closest setup launch edge. Call these the primary pairs
  - Out of the primary pairs, pick the most restrictive setup launch and capture edges
  - For each primary pair, draw two hold relationships: Launch to (Capture-1); (Launch+1) to capture. From all of these hold relationships, pick the most restrictive
<img width="503" height="170" alt="Screenshot 2026-01-31 021856" src="https://github.com/user-attachments/assets/8ba10e6c-1e86-4f37-af3b-d23719383889" />

## Messages During Timing Updates
```
set_app_var timing_update_status_level high
```
Large base periods can alert the potential clock definition problems
<img width="575" height="149" alt="Screenshot 2026-01-31 021906" src="https://github.com/user-attachments/assets/e7b431fe-586d-49d9-a1b7-b59d1d6b211b" />

## Timing Reports for Asynchronous Clocks'
- FF2 does not need to be checked for reliable data capture
<img width="632" height="294" alt="Screenshot 2026-01-31 021914" src="https://github.com/user-attachments/assets/9566c58e-60ce-48c2-92fe-0dae5025ff5d" />

## Reports for Unconstrained Paths
<img width="559" height="360" alt="Screenshot 2026-01-31 021926" src="https://github.com/user-attachments/assets/9b2ebb1f-e4e4-4bad-aaa7-6ebe8de1c87d" />

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
<img width="543" height="162" alt="Screenshot 2026-01-31 021939" src="https://github.com/user-attachments/assets/e07c8664-776e-416c-8eb7-1c76226e50e0" />

## Finding CRP in a Timing Report
<img width="620" height="384" alt="Screenshot 2026-01-31 021949" src="https://github.com/user-attachments/assets/75c9cb44-1446-4aed-aa60-fea45e73d14b" />

## Summary
- Gather basic information about the design clocks on a unfamiliar designs
- How many clocks
- What type and where are the clocks defined
- Which clocks are interacting
- What is Clock Reconvergence Pessimism Removal (CRPR)
