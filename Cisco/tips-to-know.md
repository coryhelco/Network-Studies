# TIPS TO KNOW

## tcl scripts

- Ping all devices with tcl shell


``` 
tclsh
foreach VAR {
150.1.1.1
150.1.2.2
150.1.3.3
150.1.4.4
150.1.5.5
150.1.6.6
150.1.7.7
150.1.8.8
150.1.9.9
150.1.10.10
} { ping $VAR }
```