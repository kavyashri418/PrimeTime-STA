## Writing constraints and validating them

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

