# PacketKnockOut
Packet Knock-Out is an exploration in a method of data exfiltration via packet port numbers.

The exfiltration engine described herein is comprised of two program files, both written in Python. The environmental expectation is that both the client and server will be operating in a Linux environment with IPTables and Systemctl, although nothing precludes the client software from operating in alternate environments. It is expected that the user have a firm grasp of command line interfaces.

Exfild.py is the server-side packet decoding engine, and handles incoming packets within an expected range. With proper permissions (i.e. root), this engine will set up firewall rules (utilizing the popular IPTables packet engine) and create the requisite logging rules (utilizing rsyslog). Regardless of permissions, the tool will process incoming packets utilizing calculations based on the options provided, and supports multiplex connections based on incoming IP address (such that disparate remote sites can send data in at the same time). To operate Exfild.py, one must provide several pieces of information. If there is any doubt, consult the help file utilizing the –h flag. At a high level, the following information is needed:

Encoding: -e (null, b16, b32, b64): Specifies the type of encoding that the system should expect. This allows the engine to decode the incoming information, and also is utilized when calculating which ports need to have logging enabled on the firewall.

Firewall Mod: -f: Creates necessary IPTables rules for logging packets, creates necessary RSyslog configuration to log to specified log file, and restarts RSyslog service with Systemctl. THIS REQUIRES Exfild.py TO RUN AS ROOT.

Log Location: -l (location): Specifies the location to monitor for packet logs. Utilized with the above option to create RSyslog configurations, and utilized for main program loop as a continuous Tail.

Offset: -o (offset): Specifies a flat offset for all expected packets. Added to the character value, and the result is the port number. Utilized when calculating which ports need to have logging enabled on the firewall.

Termination Signature: -t (term_sig): Specify (in ASCII decimal) the character the engine expects the message to end with. This needs to be outside the effective range of the encoding you utilize, or else parts of your message may terminate prematurely.

Verbosity: -v/-vv: Show debug messages. Two v’s may be utilized for increased verbosity.

The system will tear down firewall rules when it is terminated with a ctrl-c, providing a measure of plausible deniability.

--------------------------------------------------------------------------------------------------------------------------------------

Exfil.py is the client-side encoding and exfiltration engine, and handles passing data out of the system being researched. This engine requires no special considerations, and does not require root permissions to operate. A pseudorandom payload is generated for each packet to obscure the purpose of the transmission. To operate Exfil.py, one must provide several pieces of information. If there is any doubt, consult the help file utilizing the –h flag. At a high level, the following information is needed:

Delay: -d (delay): Specifies a per-packet delay in seconds, allowing the user to burst the data or use a low and slow approach to exfiltration. May be provided in sub-second increments (.1, .05, etc…).

Encoding: -e (null, b16, b32, b64): Specifies the type of encoding that the system should provide. This is highly recommended for Binary files, as they often times include non-printable characters. Be aware that encoding a file does increase the number of packets that must be sent, and may result in loss of information if a packet does not reach the server.

File Path: -f (path): Specify the file you wish to transmit.

Offset: -o (offset): Specifies a flat offset for all packets. Added to the character value, and the result is the port number.

Server: -s (server address): Specifies the remote server that will (presumably) have the listening daemon running. Note that if you provide a URL instead of an IP address, additional DNS queries will be made, possibly alerting others to the activity.

Termination Signature: -t (term_sig): Specify (in ASCII decimal) the character the engine expects the message to end with. This needs to be outside the effective range of the encoding you utilize, or else parts of your message may terminate prematurely.

Verbosity: -v/-vv: Show debug messages. Two v’s may be utilized for increased verbosity.

The program will self-close upon completion of the transmission. Efforts have been made to prevent erroneous input from being accepted, but a certain degree of technical acumen is expected.
