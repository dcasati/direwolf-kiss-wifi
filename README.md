# direwolf-kiss-wifi
Setup `direwolf` as a KISS TNC over wifi. These instructions were tested on Ubuntu 22.04 (x86) and on the Raspian 11 Bullseye [km4ack build-a-pi](https://github.com/km4ack/pi-build).

For this to work you will need to compile `direwolf` from it's [dev branch](https://github.com/wb2osz/direwolf/tree/dev). On Debian based OSes these are the steps:

```bash
sudo apt-get install \
    git \
    gcc \
    g++ \
    make \
    cmake \
    libasound2-dev \
    libudev-dev \
    libavahi-client-dev
```

From here, you can clone and compile direwolf:

```bash
cd ~
git clone https://www.github.com/wb2osz/direwolf
cd direwolf
git checkout dev
mkdir build && cd build
cmake ..
make -j4
sudo make install
make install-conf
```

With that, let's create a a systemd unit to start `direwolf` by default during boot. In this example I'm using the `User=pi` and `Group=pi`. Change this to reflect your environment.

```bash
[Unit]
Description=Direwolf
After=network.target

[Service]
Type=forking
User=pi
Group=pi
ExecStart=/usr/bin/tmux new-session -d -s direwolf '/usr/local/bin/direwolf -c /home/pi/direwolf.conf'
Restart=always

[Install]
WantedBy=default.target
```

Once the system is running you can check the status of `direwolf` by attaching to it's `tmux` session.

```bash
tmux attach -t direwolf    
```

Here is a sample output

```bash
Dire Wolf DEVELOPMENT version 1.7 E (Sep 14 2022)
Includes optional support for:  gpsd hamlib cm108-ptt dns-sd

Reading config file /home/pi/direwolf.conf
Audio device for both receive and transmit: plughw:2,0  (channel 0)
Channel 0: 1200 baud, AFSK 1200 & 2200 Hz, A+, 44100 sample rate / 3.
Ready to accept AGW client application 0 on port 8000 ...
Ready to accept KISS TCP client application 0 on port 8001 ...
DNS-SD: Avahi: Announcing KISS TCP on port 8001 as 'Dire Wolf on raspberrypi'
DNS-SD: Avahi: Service 'Dire Wolf on raspberrypi' successfully registered.

Now connected to IGate server noam.aprs2.net (137.220.54.84)
Check server status here http://137.220.54.84:14501

```