.. _troubleshooting:

Troubleshooting
===============

Sometimes things break or don't work as expected. This section describes
several troubleshooting tools provided by VyOS that can help when something
goes wrong.

Basic Connectivity Verification
-------------------------------

Verifying connectivity can be done with the familiar `ping` and `traceroute`
commands. The options for each are shown (the options for each command were
displayed using the built-in help as described in the :ref:`cli`
section and are omitted from the output here):

.. code-block:: none

  vyos@vyos:~$ ping
  Possible completions:
    <hostname>    Send Internet Control Message Protocol (ICMP) echo request
    <x.x.x.x>
    <h:h:h:h:h:h:h:h>

Several options are available when more extensive troubleshooting is needed:

.. code-block:: none

  vyos@vyos:~$ ping 10.1.1.1
  Possible completions:
    <Enter>       Execute the current command
    adaptive      Ping options
    allow-broadcast
    audible
    bypass-route
    count
    deadline
    flood
    interface
    interval
    mark
    no-loopback
    numeric
    pattern
    quiet
    record-route
    size
    timestamp
    tos
    ttl
    verbose

.. code-block:: none

  vyos@vyos:~$ traceroute
  Possible completions:
    <hostname>    Track network path to specified node
    <x.x.x.x>
    <h:h:h:h:h:h:h:h>
    ipv4          Track network path to <hostname|IPv4 address>
    ipv6          Track network path to <hostname|IPv6 address>

However, another tool, mtr_, is available which combines ping and traceroute
into a single tool. An example of its output is shown:

.. code-block:: none

  vyos@vyos:~$ mtr 10.62.212.12

                             My traceroute  [v0.85]
  vyos (0.0.0.0)
  Keys:  Help   Display mode   Restart statistics   Order of fields   quit
                                    Packets               Pings
  Host                            Loss%   Snt   Last   Avg  Best  Wrst StDev
  1. 10.11.110.4                   0.0%    34    0.5   0.5   0.4   0.8   0.1
  2. 10.62.255.184                 0.0%    34    1.1   1.0   0.9   1.4   0.1
  3. 10.62.255.71                  0.0%    34    1.4   1.4   1.3   2.0   0.1
  4. 10.62.212.12                  0.0%    34    1.6   1.6   1.6   1.7   0.0

.. note:: The output of ``mtr`` consumes the screen and will replace your
   command prompt.

Several options are available for changing the display output. Press `h` to
invoke the built in help system. To quit, just press `q` and you'll be returned
to the VyOS command prompt.

Monitoring
----------

VyOS features several monitoring tools.

.. code-block:: none

  vyos@vyos:~$ monitor 
  Possible completions:
    bandwidth     Monitor interface bandwidth in real time
    bandwidth-test
                  Initiate or wait for bandwidth test
    cluster       Monitor clustering service
    command       Monitor an operational mode command (refreshes every 2 seconds)
    conntrack-sync
                  Monitor conntrack-sync
    content-inspection
                  Monitor Content-Inspection
    dhcp          Monitor Dynamic Host Control Protocol (DHCP)
    dns           Monitor a Domain Name Service (DNS) daemon
    firewall      Monitor Firewall
    https         Monitor the Secure Hypertext Transfer Protocol (HTTPS) service
    lldp          Monitor Link Layer Discovery Protocol (LLDP) daemon
    log           Monitor last lines of messages file
    nat           Monitor network address translation (NAT)
    openvpn       Monitor OpenVPN
    protocol      Monitor routing protocols
    snmp          Monitor Simple Network Management Protocol (SNMP) daemon
    stop-all      Stop all current background monitoring processes
    traceroute    Monitor the path to a destination in realtime
    traffic       Monitor traffic dumps
    vpn           Monitor VPN
    vrrp          Monitor Virtual Router Redundancy Protocol (VRRP)
    webproxy      Monitor Webproxy service
  

Traffic Dumps
^^^^^^^^^^^^^

To monitor interface traffic, issue the :code:`monitor traffic interface <type> <name>`
command, replacing `<type>` and `<name>` with your desired interface
type and name, respectively.

.. code-block:: none

  vyos@vyos:~$ monitor traffic interface eth0 
  tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
  listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
  15:54:28.581601 IP 192.168.0.1 > vyos: ICMP echo request, id 1870, seq 3848, length 64
  15:54:28.581660 IP vyos > 192.168.0.1: ICMP echo reply, id 1870, seq 3848, length 64
  15:54:29.583399 IP 192.168.0.1 > vyos: ICMP echo request, id 1870, seq 3849, length 64
  15:54:29.583454 IP vyos > 192.168.0.1: ICMP echo reply, id 1870, seq 3849, length 64
  ^C
  4 packets captured
  4 packets received by filter
  0 packets dropped by kernel
  vyos@vyos:~$

To quit monitoring, press `Ctrl-c` and you'll be returned to the VyOS command
prompt.

Traffic can be filtered and saved.

.. code-block:: none

  vyos@vyos:~$ monitor traffic interface eth0 
  Possible completions:
    <Enter>       Execute the current command
    filter        Monitor traffic matching filter conditions
    save          Save traffic dump from an interface to a file


Interface Bandwidth
^^^^^^^^^^^^^^^^^^^

to take a quick view on the used bandwidth of an interface use the ``monitor
bandwidth`` command

.. code-block:: none

  vyos@vyos:~$ monitor bandwidth interface eth0

show the following:

.. code-block:: none

         B                      (RX Bytes/second)
    198.00 .|....|.....................................................
    165.00 .|....|.....................................................
    132.00 ||..|.|.....................................................
     99.00 ||..|.|.....................................................
     66.00 |||||||.....................................................
     33.00 |||||||.....................................................
           1   5   10   15   20   25   30   35   40   45   50   55   60

       KiB                      (TX Bytes/second)
      3.67 ......|.....................................................
      3.06 ......|.....................................................
      2.45 ......|.....................................................
      1.84 ......|.....................................................
      1.22 ......|.....................................................
      0.61 :::::||.....................................................
           1   5   10   15   20   25   30   35   40   45   50   55   60

Interface performance
^^^^^^^^^^^^^^^^^^^^^

To take a look on the network bandwidth between two nodes, the ``monitor
bandwidth-test`` command is used to run iperf.

.. code-block:: none

  vyos@vyos:~$ monitor bandwidth-test
  Possible completions:
    accept        Wait for bandwidth test connections (port TCP/5001)
    initiate      Initiate a bandwidth test

* The ``accept`` command opens a listening iperf server on TCP Port 5001
* The ``initiate`` command connects to that server to perform the test.

.. code-block:: none

  vyos@vyos:~$ monitor bandwidth-test initiate
  Possible completions:
    <hostname>    Initiate a bandwidth test to specified host (port TCP/5001)
    <x.x.x.x>
    <h:h:h:h:h:h:h:h>


Monitor command
^^^^^^^^^^^^^^^

The ``monitor command`` command allows you to repeatedly run a command to view
a continuously refreshed output. The command is run and output every 2 seconds,
allowing you to monitor the output continuously without having to re-run the
command. This can be useful to follow routing adjacency formation.

.. code-block:: none

  vyos@router:~$ monitor command "show interfaces"

Will clear the screen and show you the output of ``show interfaces`` every
2 seconds.

.. code-block:: none

  Every 2.0s: /opt/vyatta/bin/vyatta-op-cmd-wrapper    Sun Mar 26 02:49:46 2019

  Codes: S - State, L - Link, u - Up, D - Down, A - Admin Down
  Interface        IP Address                        S/L  Description
  ---------        ----------                        ---  -----------
  eth0             192.168.1.1/24                    u/u
  eth0.5           198.51.100.4/24                   u/u  WAN
  lo               127.0.0.1/8                       u/u
                   ::1/128
  vti0             172.25.254.2/30                   u/u
  vti1             172.25.254.9/30                   u/u

Clear Command
-------------

Sometimes you need to clear counters or statistics to troubleshoot better.

To do this use the ``clear`` command in Operational mode.

to clear the console output

.. code-block:: none

  vyos@vyos:~$ clear console

to clear interface counters

.. code-block:: none

  # clear all interfaces
  vyos@vyos:~$ clear interface ethernet counters
  # clear specific interface
  vyos@vyos:~$ clear interface ehternet eth0 counters

The command follow the same logic as the ``set`` command in configuration mode.

.. code-block:: none

  # clear all counters of a interface type
  vyos@vyos:~$ clear interface <interface_type> counters
  # clear counter of a interface in interface_type
  vyos@vyos:~$ clear interface <interface_type> <interace_name> counters


to clear counters on firewall rulesets or single rules

.. code-block:: none

  vyos@vyos:~$ clear firewall name <ipv4 ruleset name> counters
  vyos@vyos:~$ clear firewall name <ipv4 ruleset name> rule <rule#> counters

  vyos@vyos:~$ clear firewall ipv6-name <ipv6 ruleset name> counters
  vyos@vyos:~$ clear firewall ipv6-name <ipv6 ruleset name> rule <rule#> counters


Basic System Information
------------------------

Boot steps
^^^^^^^^^^

VyOS 1.2 uses `Debian Jessie`_ as the base Linux operating system. Jessie was
the first version of Debian that uses systemd_ as the default init system.

These are the boot steps for VyOS 1.2

1. The BIOS loads Grub (or isolinux for the Live CD)
2. Grub then starts the Linux boot and loads the Linux Kernel ``/boot/vmlinuz``
3. Kernel Launches Systemd ``/lib/systemd/systemd``
4. Systemd loads the VyOS service file
   ``/lib/systemd/system/vyos-router.service``
5. The service file launches the VyOS router init script
   ``/usr/libexec/vyos/init/vyos-router`` - this is part of the vyatta-cfg_
   Debian package

  1. Starts FRR_ - successor to `GNU Zebra`_ and Quagga_

  2. Initialises the boot configuration file - copies over
     ``config.boot.default`` if there is no configuration
  3. Runs the configuration migration, if the configuration is for an older
     version of VyOS
  4. Runs The pre-config script, if there is one
     ``/config/scripts/vyos-preconfig-bootup.script``
  5. If the config file was upgraded, runs any post upgrade scripts
     ``/config/scripts/post-upgrade.d``
  6. Starts ``rl-system`` and ``firewall``
  7. Mounts the ``/boot`` partition
  8. The boot configuration file is then applied by ``/opt/vyatta/sbin/
     vyatta-boot-config-loader/opt/vyatta/etc/config/config.boot``

    1. The config loader script writes log entries to
       ``/var/log/vyatta-config-loader.log``

  10. Runs ``telinit q`` to tell the init system to reload ``/etc/inittab``
  11. Finally it runs the post-config script
      ``/config/scripts/vyos-postconfig-bootup.script``

.. _Quagga: https://www.quagga.net/
.. _`GNU Zebra`: https://www.gnu.org/software/zebra/
.. _FRR: https://frrouting.org/
.. _vyatta-cfg: https://github.com/vyos/vyatta-cfg
.. _systemd: https://freedesktop.org/wiki/Software/systemd/
.. _`Debian Jessie`: https://www.debian.org/releases/jessie/
.. _mtr: http://www.bitwizard.nl/mtr/
.. _tshark: https://www.wireshark.org/docs/man-pages/tshark.html
.. _`PCAP filter expressions`: http://www.tcpdump.org/manpages/pcap-filter.7.html
