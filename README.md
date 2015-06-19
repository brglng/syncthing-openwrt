# syncthing-openwrt
Init script and usage for ARM based OpenWrt devices.

This document is only applicable for devices with an ARM chip, and only tested
with OpenWrt version BarrierBreaker 14.07.

Usage
=====

1. Login to your OpenWrt device via SSH.

2. Download and extract the tarball, and copy the syncthing binary to
   `/usr/bin` or any other location that are in your `PATH` environment
   variable.
   ```shell
   $ curl -khttps://github.com/syncthing/syncthing/releases/download/v0.11.9/syncthing-linux-arm-v0.11.9.tar.gz
   $ tar -zxf syncthing-linux-arm-v0.11.9.tar.gz 
   $ cd syncthing-linux-arm-v0.11.9
   $ cp syncthing /usr/bin/
   ```

3. Run Syncthing for the first time.
   ```shell
   syncthing
   ```
   When you see a message about your Node ID that looks like this:
   ```
   [2EQK3] 15:47:15 OK: Ready to synchronize default (read-write)
   [2EQK3] 15:47:15 INFO: Node 2EQK3ZR77PTBQGM44KE7VQIQG7ICXJDEOK34TO3SWOVMUL4QFBHA is "server1" at [dynamic]
   ```
   You can press Ctrl-C to stop it.

4. If you don't want the Syncthing Web GUI be accessible from WAN, you can
   skip 5 and 6.

5. Modify the Syncthing configuration file.
   ```shell
   vi ~/.config/syncthing
   ```
   Look for a section that looks like this:
   ```xml
   <gui enabled="true" tls="false">
     <address>127.0.0.1:8384</address>
   </gui>
   ```
   Change it to:
   ```xml
   <gui enabled="true" tls="false">
     <address>0.0.0.0:8384</address>
   </gui>
   ```

6. Open the port 8384 for WAN. Add the following in `/etc/config/firewall`:
   ```
   config rule
           option enabled '1'
           option target 'ACCEPT'
           option src 'wan'
           option proto 'tcp'
           option dest_port '8384'
           option name 'Syncthing Web'
   ```

7. Download `/etc/init.d/syncthing` from this repository and copy it to
   `/etc/init.d` and make it executable.
   ```shell
   $ curl -k https://github.com/brglng/syncthing-openwrt/raw/master/etc/init.d/syncthing
   $ cp syncthing /etc/init.d
   $ chmod +x /etc/init.d/syncthing
   ```
8. Enable and start the syncthing service.
   ```shell
   $ /etc/init.d/syncthing enable
   $ /etc/init.d/syncthing start
   ```

9. Open http://<your OpenWrt device address>:8384 on your browser.

10. If you have allowed access to your Syncthing Web GUI from WAN, make sure
    to turn on "GUI Authentication Password" and "Use HTTPS for GUI" in the
    settings.
