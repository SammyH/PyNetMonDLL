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

    - The winsock functions "WSASend", "WSARecv", "send", "sendto", "recv", and "recvfrom" are located in ws2_32.dll and
        a jump instruction is written in the prologue of each of these functions to a special hook
        function.

      The special hook function will invoke python functions with the same name (WSASend,WSARecv,send,sendto,recv,recvfrom), from
        the script file specified in config.ini
 
Example Application
===========

Consider using the DLL to monitor traffic through iexplore.exe located at "C:\Program Files\Internet Explorer\iexplore.exe" (for which a logfile will be created as C:\Program Files\Internet Explorer\iexplore.txt)
First, we must create a configuration file at "C:\Program Files\Internet Explorer\config.ini", which could simply be as follows:

    hookfile=hooks

"hooks" will then be passed to "PyImport_ImportModule" to import your python script (the same as the 'import' statement in python, checks sys path and local dir).
The python module "hooks" must be then be created, for example, "C:\Program Files\Internet Explorer\hooks.py":

    def send(buf, len):
        print "send hook was called!"

Which will simply print a message to the allocated console whenever the winsock function 'send' is called.