PyNetMonDLL
===========

Python network packet sniffer for Winsock, written in C++
Useful for intercepting/modifying/filtering network traffic in a specific process

How it works
===========
When this DLL is loaded into a target process, the following occurs:
    - A console is allocated (AllocConsole) and stdout,stdin,stderr is redirected to this console (freopen)

    - Search for INI configuration file (config.ini) in same directory as target executable
        key "hookfile" tells DLL what python script file to send net traffic to

    - The winsock functions "send", "sendto", "recv", and "recvfrom" are located in ws2_32.dll and
        a jump instruction is written in the prologue of each of these functions to a special hook
        function.

      The special hook function will invoke python functions with the same name (send,sendto,recv,recvfrom), from
        the script file specified in config.ini
    