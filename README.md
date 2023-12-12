# Syslog

Logging can be implemented locally on a router, but this method is not scalable. In addition, if a router reloads, all the logs that are stored on the router will be lost. Therefore, it is important to implement logging to an external destination.

Logging to external destinations can be implemented by using various mechanisms:

1. Cisco device syslog messages, which include OS notifications about unusual network activity or administrator implemented debug messages.
2. SNMP trap notifications about network device status or configured thresholds being reached.
3. Exporting of network traffic flows using NetFlow.

When implementing logging, it is also important that dates and times are accurate and synchronized across all the network infrastructure devices. Without time synchronization, it is very difficult to correlate different sources of logging.

## Understanding Syslog

During operation, network devices generate messages about different events. These messages are sent to an operating system process, which helps them proceed to the destination. Syslog is a protocol that allows a machine to send event notification messages across IP networks to event message collectors.

1. Emergency (Severity 0) System is unusable
2. Alert (Severity 1) Immediate action needed
3. Critical (Severity 2) Critical condition
4. Error (Severity 3) Error condition
5. Warning (Severity 4) Warning condition
6. Notification (Severity 5) Normal but significant condition
7. Informational (Severity 6) Informational message
8. Debugging (Severity 7) Debugging message

Cisco devices can display syslog messages on various interfaces or be configured to capture them in a log:

1. Console: By default, logging is enabled on the console port. Hence, the console port always processes syslog output even if you are using some other port or method (such as aux, vty, or buffer) to capture the output.
2. AUX and VTY Ports: To receive syslog messages when connected to the AUX port or remotely logged into the device via Telnet or SSH through the vty lines, type the terminal monitor command.
3. Memory Buffer: Logging to memory logs messages to an internal buffer. The buffer is circular in nature, so newer messages overwrite older messages after the buffer is filled. The buffer size can be changed, but to prevent the router from running out of memory, do not make the buffer size too large. To enable system message logging to a local buffer, use the logging buffered command in global configuration mode. To display messages that are logged in the buffer, use the show logging command. The first message displayed is the oldest message in the buffer.
4. Syslog Server: To log system messages and debug output to a remote host, use the logging host command in the global configuration mode. This command identifies a remote host (usually a device serving as a syslog server) to receive logging messages. By issuing this command more than once, you can build a list of hosts that receive logging messages.
5. Flash Memory: Logging to buffer poses an issue when trying to capture debugs for an intermittent issue or during high traffic. When the buffer is full, older messages are overwritten. And when the device reboots, all messages are lost. Using persistent logging allows you to write logged messages to files on a router's flash disk. To log messages to flash, use the logging persistent command.

```
R1#
*Feb 14 09:40:10.326: %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up
```
Above we can see that the line protocol of interface GigabitEthernet0/1 went up but there’s a bit more info than just that. Let me break down how Cisco IOS formats these log messages:

1. timestamp: Feb 14 0:40:10.326
2. facility: %LINEPROTO
3. severity level: 5
4. mnemonic: UPDOWN
5. description: Line protocol on Interface GigabitEthernet0/1, changed state to up

## Configuration

![Creating a Syslog](https://raw.githubusercontent.com/deliawolf/Syslog/main/Syslog.png)

Setup the logging server
```
R1# configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)# logging 10.1.1.10
```
Setup logging trap level
```
R1(config)# logging trap informational
R1(config)# end
R1#
*Dec  1 08:04:49.998: %SYS-5-CONFIG_I: Configured from console by console
R1#
*Dec  1 08:04:51.027: %SYS-6-LOGGINGHOST_STARTSTOP: Logging to host 10.1.1.10 port 514 started - CLI initiated
```
```
R1# show logging
```

## Additioanl Configuration

Logging has maximum size for storing the log, the log will reset when the devices is restarted.
change the buffers size to 16384 Bytes
```
R1(config)#logging buffered 16384
R1#show logging | include Log Buffer
```
With the logging console command, I can decide what severity levels I want to see on the console. The default is to show everything up to debug messages which is fine:
```
R1(config)#logging console debugging 
```
I can do the same thing for syslog messages when you are logged in through telnet or SSH:
```
R1(config)#logging monitor debugging 
```
Since the local storage of the router or switch is limited, perhaps you want to store only warnings and higher severity levels:
```
R1(config)#logging buffered warnings 
```
The timestamp is pretty much self explanatory, without it you would never know when an event has occured. It is possible to disable it and/or replace it with sequence numbers. Here’s a quick example:
```
R1(config)#no service timestamps 
R1#
%LINK-5-CHANGED: Interface FastEthernet0/1, changed state to administratively down
```
Here’s how to enable sequence numbers:
```
R1(config)#service sequence-numbers 
R1#
000045: %SYS-5-CONFIG_I: Configured from console by console
000046: %LINK-3-UPDOWN: Interface FastEthernet0/1, changed state to up
```
