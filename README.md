## Introduction to STA PRIMETIME

## What is PRIMETIME?
- Performs Static Timing Analysis (STA)]
- Performs signal integrity (SI) analysis for timing and noise
- 1B+ gate capacity and performance using hierarchical & distributed analysis
- Provides signoff accuracy for all technology nodes
- Creates timing models for hierarchical analysis & IP re-use
- Provides multi-mode multi-corner analysis
- Provides efficient multi-voltage analysis
- Generates ECO guidance for timing closure

## PRIMETIME in the Implementation Flow
- Industry standard STA, Timing Closure and Signoff
- Run at all gate level after design transformations
  - Before handing off to manufacturing
  - Generally, between tools
  - Sometimes, between transformation within a tool
- Run whenever there is change in
  - Design netlist
  - Design constraints

## What is STA?
- STA verifies if design timing criteria are met
  - Verifies timing between synchronous clocks
  - Does not verify functionality --> This is done by formal verification
- Is Exhaustive
  - Every timing path in the design is analyzed
  - Uses formal, mathematical techniques instead of simulation vectors
- Is Fast
  - Significantly faster than gate-level simulation
  - Orders of magnitude faster than transistor-level simulation
 
## STA - Basic Approach
- STA breaks down netlist into individual timing arcs
- Three different timing arc types are calculated
  - Cell delay arc
  - Net delay arc
  - Cell timing check
- Timing values are stored on the Timing Graph
- We refer to this as Graph Based Analysis (GBA)
- Report is based on timing paths
- Each path has a startpointg and an endpoint

## STA is a Constraint Driven
- STA needs additional constraints for timing analysis
- Design environment needs to be specified as well
- Incomplete or inaccurate constraints will lead to incorrect analysis
- Applying, interpreting, and debugging constraints

## Signoff Considerations'
- Design size determines flow decision
  - Flat and hierarchical analysis capabilities are available
- Analysis must account for crosstalk effects
  - PrimeTime SI checks for above impact on the delay and noise
- In advanced process nodes, design waveforms can significantly deviate from characterization waveforms
  - PrimeTIme has many techniques to ensure signoff accuracy for all technology nodes
- Generating ECO guidance would be required for timing closure
  - Physically aware ECO considers physical implementation achievability for improved closure
- Design timing must be analyzed in multiple modes and PVT corners
  - Distributed Multiscenario Analysis (DMSA) provides efficient analysis
 
## Timing Checks Verified by PrimeTime
## Summary Report of Timing Checks Verified

## STA is Path Based
- Timing path start points include:
  - Clock pins of registers
  - Input ports
- Timing path end points include:
  - All input pins of registers except clock pins
  - Output ports

| Path Type | Start Point | End Point | 
|----------|----------|----------|
| Input Path  | Start Point | Register Non Clock Pin |
| Reg-Reg Path | Register Clock Pin | Register Non Clock Pin |
| Output Path | Register Clock Pin | Output Port |
| Combo Path | Input Port | Output Port |

## Summary Report of Violating Path Types
Are violations internal or in the I/O?

## Setup Timing Analysis

## Timing Report for Setup

## Hold Timing Analysis

## Timing Report for Hold

## Clock Reconvergence Pessimism
- CRP = Latest RISE arrival time to common point - Earliest RISE arriavl time to common point
- CRP = Latest FALL arriavl time to common point - Earliest FALL arriavl time to common point

## Primetime Inputs and Outputs

## Timing Analysis Flow in PrimeTIme
- STA flow is divided into several steps
  - Steps 1-3 read data and
  - Analysis begin at Step-4
- The STA flow is repeated until signoff is achieved

## Load Design and Check
## Load Libraries and Check
## Parasitic Annotation for Delay Calculation
## Read Parasitics
- It's recommended that parasitics are read in using one of the two formats:
  - Galaxy Parasitic Database (GPD)
  - Standard Parasitic Exchange Format (SPEF)
## Check Parasitic Annotation
## Source Constraints and Check for Correctness
## Confirming Constraint Completeness
## Checking for Ignored Timing Exceptions
- User-specified exceptions to PrimeTime single-cycle synchronous STA are
  - False paths
  - Multi-cycle paths
  - set_max_delay and set_min_delay
- Incorrectly-specified exceptions are ignored

## Coverage Analysis
```
pt_shell> report_analysis_coverage
```

## Generate Detailed Reports
- Generate detailed reports
- Examine generated reports for:
 - Timing Violations, Constraints and Exceptions applied

## Save Session
- Save session
```
save_session session_directory
```

- Check for save session completeness
```
unix% ls -al $session_directory
```

- Can restore with the same version that was used to save to session
- If -version compatible option is used with save_session, then the session can be restored in the same or later PrimeTime releases

- Check version of PrimeTime used to save session
```
unix% more $session_directory/Readme
```

- Check for the libraries used
```
unix% more $session_directory/lib_map
```

- Check if a saved session restores
```
restore_session $session_directory
```

## Running PrimeTime
- Source PrimeTime Run script in batch mode
```
unix% pt_shell -f run.tcl | tee -i run.log
(or)
unix% pt_shell -f ./pt_scripts/pt.tcl | tee -i run.log
```

- To run PrimeTime script interactively do the following
```
pt_shell> source run.tcl -echo verbose
(or)
pt_shell> redirect run.log [source run.tcl -echo -verbose]
```

