# Running LwM2M Client

## Local Leshan networking

### Native client
Setup for a single Linux native client node connected via TAP to the server node. Build with `Makefile.native`.

```
lwm2m-client node
Linux native
fd00:bbbb::2/64 (in Makefile)
   |
  TAP
   |
Leshan node
workstation (Linux)
fd00:bbbb::1/64 (in setup_native.sh)
```

Must manually run '`setup_native.sh start`' to setup TAP, and '`setup_native.sh stop`' to tear it down.

After running the `term` target to start the RIOT instance, do the following in its terminal:

```
   > ifconfig 6 add fd00:bbbb::2/64
```


### Single board client via USB (local networking)
Setup for a single physical board client node connected via USB/TAP to the server node. I use a samr21-xpro. Build with `Makefile.ula`.
```
lwm2m-client node
board (samr21-xpro)
fd00:bbbb::2/64 (via CLI)
   |
  USB/TAP
   |
Leshan node
workstation (Linux)
fd00:bbbb::1/64 (in setup_ula.sh)
```
`setup_ula.sh` executes automatically when running the `term` target, to setup and teardown the TAP interface.

After running the `term` target on the board, do the following:

**board**
```
   > ifconfig 6 add fd00:bbbb::2/64
   > nib neigh add 6 fd00:bbbb::1 <ether addr>
```
where <ether addr\> is the 6 hexadecimal colon separated MAC address of the tap interface

**workstation**
```
   $ sudo ip route add fd00:bbbb::/64 via <lladdr> dev tap0
```
where <lladdr\> is the link local address of the samr21-xpro


### Single board client via WiFi (Internet networking)
Setup for a single physical board client node connected via WiFi to a server node in the cloud. I use ESP-12x (8266) Adafruit Feather board because it has WiFi on board, and so does not require a USB connection to an Internet gateway, like the samr21-xpro. This approach means we expect the WiFi access point to provide IPv6 Internet connectivity. Build with `Makefile.inet`.
```
lwm2m-client node
board esp-12x
routeable IPv6 addr (auto-assigned)
   |
  WiFi/Internet
   |
Leshan node
cloud instance (Linux)
routeable cloud address (see SERVER_ADDR below)
```

Must define environment variables for `Makefile.inet`:
```
export RIOT_WIFI_SSID="<ssid>"
export RIOT_WIFI_PASS="<passphrase>"
export SERVER_ADDR=\\\"<server addr>\\\"

```


## With Bootstrap Server
Must add client configuration via web UI before it attempts to connect. See "Add new client bootstrap configuration" button in web UI.