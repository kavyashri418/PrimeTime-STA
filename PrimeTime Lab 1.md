## PrimeTime Flow

## Objective - To be familiar with Prime Time and understand how to read and analyze setup/hold reports

### Relevant Files & Directories
```
Lab01 - current working directory
common_setup.tcl - multi tool shared setup file
pt_shell.tcl - tool specific PrimeTime setup file
pt_scripts - run file directory
pt.tcl - run file
.synopsys_pt.setup - automatically read PT steup file
RUN.tcl - run script for orca_savesession
```

## Task 1: Restore a PrimeTime Session

- Invoke PrimeTime from the lab1 directory.  
```
pt shell
```

- Restore a previously saved PrimeTime session. This step will read in the design netlist, libraries, and constraints. The design is now ready for analysis.
Note: The orca_savesession below is a Unix directory.
Note: The orca_savesession can be recreated, if needed using:
```
pt_shell -f RUN.tcl | tee -i run. log
```
- Note: Any PARA-124 Errors during the execution of RUN.tcl (within the parasitics_command.log file) can be safely ignored for the purpose of the labs.
- Note: Prime Time supports command, option, variable and file completion. Type a few letters and then hit the tab key.
```
pt_shell> restore_session orca_savesession
```

- Generate coverage analysis report
```
pt_shell> report_analysis_coverage
```

- Generate global timing report
```
pt_shell> report_global_timing
```

## Task 2: Explore some helpful commands

- Execute the following three history short cut commands:
```
pt_shell> history
pt_shell> !!
pt_shell> !2
```

- Use up and down arrows to scroll through the history event list as an alternative to the previous step. Type the following to see all the available key bindings (in the default emacs editing mode).
```
pt_shell> list_key_bindings
```

- Explore the page mode alias, execute the following command, which will generate a report that scrolls off the screen.
```
pt_shell> report_timing -group [get_path_group *]
```

- Turn on the page mode
```
pt_shell> page_on
pt_shell> !rep
```

- Use the space bar and enter keys to page through a long report. Quit from a long report in page mode by typing "g*. If you want to turn off page mode, use the command alias page off.
- Find the command to restore a PrimeTime session and then display help information on this command.
```
pt_shell> help restore*
pt_shell> view man restore_session
pt_shell> restore_session -help
```
Note: The following is an alternative way to display syntax help
```
pt_shell> help -v restore_session
```

- The time unit in PrimeTime is determined by the main technology library. To find the time unit for ORCA, first list all libraries in memory.
Note: The * in the following report indicates the main library.
```
pt_shell> list_lib
```

- Generate a report for the main library which will state the time unit
Note: Use copy and paste to avoid mistyping the lib name. The time unit is at the very top of the report.
```
pt_shell> report_lib cb13fs120_tsmc_max
```

- Display units used by the current design.
```
pt_shell> report_units
```

## Task 3: Validate an Existing PrimeTime Session

- Verify that the current design is your top-level module: ORCA.
```
pt_shell> current_design
```

- Compare the unix paths of the libraries to what has been read into PrimeTime.
```
pt_shell> printvar search_path
pt_shell> printvar link_path
pt_shell> list_libraries
```
- Verify that the nets are completely annotated.
```
pt_shell> report_annotated_parasitics
```

- Verify that the design is completely constrained.
```
pt_shell> check_timing
```

- Verify that the checks in your cells are completely exercised, look at possible causes for your findings.
```
pt_shell> report_analysis_coverage
pt_shell> report_case_analysis
```

- Quit PrimeTime
```
pt_shell> quit
```

## Summary
- List the steps of Timing Analysis flow in PrimeTime
- Generate summary reports using
  - Report_analysis_coverage
  - Report_global_timing
  - Report_timing
- State the purposes of setup files and run a script
- Enable monitoring runtime and memory used


## Task 4: Execute the run script and analyze the run

- Execute the run script logging the results to the log file run. log.
```
pt_shell -f ./pt_scripts/pt.tcl | tee -i run.log
```

- If there are any errors, address these first before moving on to the next step.
- Evaluate log file. With a text editor, open log file. Search for the update timing measures (UITE-214), print_message_info output, and the exit output. Then, in profile directory, examine the file tcl_profile_sorted_by_cpu_time.

## Task 5: Analyze STA Reports

- Invoke PrimeTime and restore the session that you saved in the previous task.
```
pt_shell
pt_shell> restore_session my_savesession
```

- Execute the following to display the clocks in ORCA.
```
pt_shell> report_clock
```

- Create a single, "short" timing report for steup for the clock SYS_CLK. Use command-line expansion (the tab key) to expand both the command AND the options -group and -path
```
pt_shell> report_timing -group SYS_CLK -path short
```

- Generate a timing report for hold time.
- The following is a short cut that will execute the last command in history starting with the letters "rep" and add the switch -delay min (which will generate a report for hold time).
```
pt_shell> !rep -delay min
```

- Quit PrimeTime.
```
pt_shell> quit.
```


