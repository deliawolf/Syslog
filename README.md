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

## Configuration



