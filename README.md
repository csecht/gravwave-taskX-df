# gravwave-taskX-df
Scripts to automatically adjust concurrency of E@H gravitational wave tasks running on AMD GPUs

These taskXDF bash shell scripts are for Linux systems with one or more AMD GPUs running Einstein@Home O2MDF gravitational wave GPU tasks through BOINC. Supported AMD GPUs are Ellesmere, Polaris, Vega, or Navi. nVIDIA cards are not currently supported.

The package consists of the files, taskXDF, taskXDF-timer, and taskXDF.cfg. All files should be run from within the same directory.

The package monitors GPU memory usages and delta frequency (DF) values of current and pending tasks, then evaluates whether to increase, decrease, or maintain the task multiple for running concurrent tasks (task X). Changes to task X automatically are made by the script by changing the value of <gpu_usage> in the app_config.xml file. Script actions and metrics are reported to the terminal window and a log file, taskXDF.log.

Script execution is controlled through the timer script, taskXDF-timer, run from the command line. 

Basic user-specified run parameters are set in the taskXDF.cfg. This configuration file can be edited with any text editor and can be edited on-the-fly while the timer is running, which may be useful to optimize settings as GW tasks run. Read the file comments to understand the settings.

The timer script is executed with a time parameter argument in seconds; e.g., `~$ ./taskXD-timer 60`. 
Usage details can be read with `~$ ./taskXD-timer --help`

Additional details for use are provided in the comments of the scripts and .cfg text file. Please read through those before using to understand what's going on. 

These scripts are written in bash ver.5. They have only been tested in Ubuntu 18.04 and 20.04.

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
To run the main script through the timer script you may want to change permissions of app_config.xml and its project folder to avoid the need to provide sudo authentication to edit <gpu_usage>.
To do this, make yourself a member of the boinc permissions group. Example usermod command and groups output:
```
~$ sudo usermod -a -G boinc youtheuser
~$ groups boinc
boinc : boinc video youtheuser render
```
Check that the boinc group has write permission for the app_config.xml file, e.g.:

`-rw-rw-r-- 1 boinc boinc 1523 Aug 23 08:02 /var/lib/boinc-client/projects/einstein.phys.uwm.edu/app_config.xml`

You will need to manually launch the timer script on each machine reboot.
To keep taskX adjustments responsive to changing DFs of GW tasks, use a 60 second time interval argument when launching the timer script.



