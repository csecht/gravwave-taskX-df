# gravwave-taskX-df
Scripts to automatically adjust concurrency of E@H gravitational wave tasks running on AMD GPUs. Can also be used to just monitor status of BOINC GW tasks.

These taskXDF bash shell scripts are for Linux systems with one or more AMD GPUs running Einstein@Home gravitational wave GPU tasks (O3AS) through BOINC. Supported AMD GPUs are Ellesmere, Polaris, Vega, or Navi. nVIDIA cards are not currently supported. The program will work in mixed GPU task queues with Gamma Ray (FGRPB1G) tasks. Other task apps have not been tested.

The package consists of the files, taskXDF, taskXDF-timer, and taskXDF.cfg. All files should be run from within the same directory.

The package monitors GPU memory usages and delta frequency (DF) values of current and pending tasks, then evaluates whether to increase, decrease, or maintain the task multiple for running concurrent tasks (task X). Changes to task X automatically are made by the script by changing the value of <gpu_usage> in the app_config.xml file. Script actions and metrics are reported to the terminal window and to a log file, taskXDF.log.

Script execution on timed intervals is controlled through the timer script, taskXDF-timer, which is run from the command line. A 1-and-done status report is available with the --status parameter entered as "taskXDF-timer --status" or "taskXDF --status". Changes to taskX can be avoided while still running timed intervals by setting "monitor_only=yes" in the taskXDF.cfg configuration file.

Additional user-specified run parameters are set in taskXDF.cfg. This configuration file can be edited with any text editor and can be edited on-the-fly while the timer is running, which may be useful to optimize settings as GW tasks run. Read the file comments to understand the settings.

The timer script is executed with a time parameter argument in seconds; e.g., `~$ ./taskXD-timer 60`. 
Usage details can be read with `~$ ./taskXD-timer --help`

Additional details for use are provided in the comments of the scripts and .cfg text file. Please read through those before using to understand what's going on. 

These scripts are written in bash ver.5.

Successful execution will display like this in the terminal window:
```
Currently running taskXDF-timer with an interval of 60 seconds. (stop with CTRL+C)
A status report is issued every 5 minutes, as set in the taskXDF.cfg file.
Running...

date     time   │ queued │ VRAM% │ GTT% │ taskGB │ X │ DFs: [running] [waiting] [ready]
————————————————│————————│———————│——————│————————│———│————————————————————————————————————————
Jan 10 14:45:49 │     20 │    45 │ 0.77 │   0.67 │ 4 │ [.20 .20 .20 .20 ] [na] [.20 ]
Jan 10 14:50:50 │     20 │    45 │ 0.77 │   0.67 │ 4 │ [.20 .20 .20 .20 ] [na] [.20 ]
```

NOTES and TIPS:
To run the main script through the timer script and avoid the need to provide authentication to edit <gpu_usage> you may want to share permissions of app_config.xml between you and boinc:
```
~$ sudo usermod -a -G $LOGNAME boinc
~$ sudo usermod -a -G boinc $LOGNAME
```
then check with:
```
~$ groups boinc
~$ groups $LOGNAME
```

After adding yourself to the boinc group, a system reboot is needed for the new permissions to take effect.
Check that the boinc group has write permission for the app_config.xml file, e.g.:

`-rw-rw-r-- 1 boinc boinc 1523 Aug 23 08:02 /var/lib/boinc-client/projects/einstein.phys.uwm.edu/app_config.xml`

You will need to manually launch the timer script on each machine reboot.
To keep taskX adjustments responsive to changing DFs of GW tasks, use a 60 second time interval argument when launching the timer script.

A status report will display upon execution and list the current time. Subsequent reports will display according to the clock time intervals set by the 'mod' variable in the .cfg file. For example, if mod is 5 minutes, then a report will display at 10:05, 10:10, etc., even though the program started running at 10:03.

Rarely, a GPU card running gravitation wave tasks will lose compute ability, thus
delaying progress and, hours later, either time-out or exit with a 
computation error. By default, this script provides a warning when a task takes 
more than 40 minutes to complete. This time can be set in the taskXDF.cfg file. 
There an option in taskXDF.cfg to suspend a too-long task; the default setting is
auto_suspend=no. You can change this to auto_suspend=yes, but be sure to set a reasonable
task time limit.
