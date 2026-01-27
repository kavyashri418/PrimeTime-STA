## Generate Timing Reports and Analysing them

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


 
