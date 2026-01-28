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
<img width="205" height="254" alt="Screenshot 2026-01-27 171657" src="https://github.com/user-attachments/assets/359c589b-be51-4532-87d6-86dcafa8addc" />

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
<img width="587" height="146" alt="Screenshot 2026-01-27 171743" src="https://github.com/user-attachments/assets/e2f4d9f4-7b34-4eb0-8e27-917ada0fa1c8" />

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
<img width="522" height="349" alt="Screenshot 2026-01-27 171755" src="https://github.com/user-attachments/assets/5180cc37-71f5-40cf-bbc1-42ca0726cf5b" />

## Summary Report of Timing Checks Verified
<img width="658" height="330" alt="Screenshot 2026-01-27 171806" src="https://github.com/user-attachments/assets/7b75d72d-45c9-4e54-a8fd-8acab94710ac" />

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
<img width="629" height="161" alt="Screenshot 2026-01-27 171820" src="https://github.com/user-attachments/assets/36e9ec41-e215-4bc5-95de-1182fb18305a" />

## Summary Report of Violating Path Types
Are violations internal or in the I/O?
```
pt_shell> report_global_timing
```
<img width="618" height="301" alt="Screenshot 2026-01-27 171831" src="https://github.com/user-attachments/assets/1aa91297-e67a-4e83-af80-01bf7e6f7f96" />

## Setup Timing Analysis
<img width="572" height="371" alt="Screenshot 2026-01-27 171839" src="https://github.com/user-attachments/assets/061e9696-017e-46a8-bf31-1154b0ea8abb" />

## Timing Report for Setup
```
pt_shell> report_timing
```
<img width="628" height="331" alt="Screenshot 2026-01-27 171850" src="https://github.com/user-attachments/assets/ff89850f-b642-4177-841e-21ac9d8e2057" />

## Hold Timing Analysis
<img width="602" height="357" alt="Screenshot 2026-01-27 171858" src="https://github.com/user-attachments/assets/ff408e8b-9156-4db1-88e1-65c89fdd601f" />

## Timing Report for Hold
```
report_timing delay_min
```
<img width="506" height="295" alt="Screenshot 2026-01-27 171909" src="https://github.com/user-attachments/assets/9074cf04-2172-413c-8b0d-abe722132695" />

## Clock Reconvergence Pessimism
- CRP = Latest RISE arrival time to common point - Earliest RISE arriavl time to common point
- CRP = Latest FALL arriavl time to common point - Earliest FALL arriavl time to common point
<img width="480" height="163" alt="Screenshot 2026-01-27 171919" src="https://github.com/user-attachments/assets/31383591-d5ce-433d-ac8f-8b96018849e4" />

## Primetime Inputs and Outputs
<img width="483" height="336" alt="Screenshot 2026-01-27 171928" src="https://github.com/user-attachments/assets/3707d547-cc99-4781-ae85-d26118eb8223" />

## Timing Analysis Flow in PrimeTIme
- STA flow is divided into several steps
  - Steps 1-3 read data and
  - Analysis begin at Step-4
- The STA flow is repeated until signoff is achieved
<img width="612" height="221" alt="Screenshot 2026-01-27 171936" src="https://github.com/user-attachments/assets/21181409-5924-44ed-b5d9-9dd0691111d1" />

## Load Design and Check
- Read gate level netlist; See also help read*
```
read_verilog orca_routed.v.gz
```

- Set the current design to be Top level design
```
current_design ORCA
```

- Check what is current design
```
pt_shell> current_design
{"ORCA"}
```

- What Designs are loaded in memory?
```
pt_shell> get_design *
{"ORCA"}
```

- Source file name of current_design
```
pt_shell> list_designs
Design Registry:
*L ORCA    /<path to>/orca_routed.v.gz:ORCA
```

## Load Libraries and Check
- set search_path "..../ref/libs ../ref/design"
- set link_path "* sc_max.cb io_max.db"
- link_design ; Load Libraries and Resolve References

- Are the Libraries loaded?
```
list_libs (or) list_libraries (or) get_libs
Library Registry:
* cb13fs12C_tsmc_max    ../ref/libs/sc_max.db:cd13fs120_tsmc_max
  cb13ic32c_tsmc_max    ../ref/libs/io_max.db:cb13io320_tsmc_max
```

- Are Link Library DB Files followed by a "*"
```
pt_shell> printvar link_path
link_path = "*sc_max.db io_max.db"
```

- Does search_path list start with a "."?
```
Pt_shell> printvar search_path
search_path - ". ../ref/libs ../ref/design"
```

## Parasitic Annotation for Delay Calculation
```
read_parasitics my_chip.spef
```
<img width="533" height="269" alt="Screenshot 2026-01-27 171950" src="https://github.com/user-attachments/assets/a18596da-0ad3-4f08-87b4-4866a100bd4d" />

## Read Parasitics
- It's recommended that parasitics are read in using one of the two formats:
  - Galaxy Parasitic Database (GPD)
  - Standard Parasitic Exchange Format (SPEF)

- SPEF file
```
read_parasitics -format SPEF flat.spef
```

- GPD directory
```
read_parasitics -format GPD GPD_dir
```

## Check Parasitic Annotation
```
report_annotated_parasitics
report_annotated_parasitics -list_not_annotated
```
<img width="646" height="330" alt="Screenshot 2026-01-27 172004" src="https://github.com/user-attachments/assets/d57783d6-d55c-4919-9600-e093672b7aa0" />

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
<img width="624" height="265" alt="Screenshot 2026-01-27 172013" src="https://github.com/user-attachments/assets/2afea980-99fb-4b70-bcff-1508f94734d0" />


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

