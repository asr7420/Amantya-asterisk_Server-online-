# Amantya-asterisk_Server-online-

Asterisk 20 VoIP Server Deployment
Documentation
Date: December 17, 2025
Asterisk Version: 20 LTS (20.17.0)
Channel Driver: PJSIP (pjproject)
Operating System: Ubuntu Linux
Deployment Status: Deployed and Verified


1. Overview

   
This document describes the complete installation, configuration, and verification of an Asterisk 20
VoIP server deployed on Ubuntu Linux. The system uses the PJSIP channel driver, as chan_sip is
deprecated and disabled by default in Asterisk 20. The deployment includes NAT traversal
configuration to allow media flow through firewalls. Audio, video, and voicemail services have been
tested and verified.


3. Installation Method

   
Asterisk was installed using source compilation. The bundled pjproject library was used to avoid
SSL and symbol conflicts with system libraries.


5. System Preparation

   
sudo apt update && sudo apt upgrade -y
sudo apt install build-essential wget git autoconf subversion pkg-config libnewt-dev libncurses5-dev


7. Source Code Download and Compilation

   
cd /usr/src
sudo wget http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-20-current.tar.gz
sudo tar zxf asterisk-20-current.tar.gz
cd asterisk-20.*
sudo
sudo
sudo
sudo
sudo
sudo
make distclean
./configure --with-pjproject-bundled
make -j$(nproc)
make install
make config
ldconfig


9. Configuration Files

    
5.1 /etc/asterisk/pjsip.conf
[transport-udp]
type=transport
protocol=udp
bind=0.0.0.0
[7001]
type=endpoint
context=internal
disallow=all
allow=ulaw,h264,vp8
auth=auth7001aors=7001
force_rport=yes
rewrite_contact=yes
rtp_symmetric=yes
direct_media=no
[auth7001]
type=auth
auth_type=userpass
username=7001
password=7001
[7001]
type=aor
max_contacts=5
[7002]
type=endpoint
context=internal
disallow=all
allow=ulaw,h264,vp8
auth=auth7002
aors=7002
force_rport=yes
rewrite_contact=yes
rtp_symmetric=yes
direct_media=no
[auth7002]
type=auth
auth_type=userpass
username=7002
password=7002
[7002]
type=aor
max_contacts=5
5.2 /etc/asterisk/extensions.conf
[internal]
exten
exten
exten
exten
exten =>
=>
=>
=>
=> 7001,1,Answer()
7001,2,Dial(PJSIP/7001,60)
7001,3,Playback(vm-nobodyavail)
7001,4,VoiceMail(7001@main)
7001,5,Hangup()
exten
exten
exten
exten
exten =>
=>
=>
=>
=> 7002,1,Answer()
7002,2,Dial(PJSIP/7002,60)
7002,3,Playback(vm-nobodyavail)
7002,4,VoiceMail(7002@main)
7002,5,Hangup()
exten => 8001,1,VoicemailMain(7001@main)
exten => 8001,2,Hangup()
exten => 8002,1,VoicemailMain(7002@main)
exten => 8002,2,Hangup()
5.3 /etc/asterisk/voicemail.conf
[main]
7001 => 7001,User One
7002 => 7002,User Two
11. Firewall Configuration
sudo ufw allow 5060/udp
sudo ufw allow 10000:20000/udp7. Verification
Endpoints 7001 and 7002 successfully registered. Two-way audio and video calls were verified.
Voicemail routing on no-answer was confirmed. NAT traversal ensured successful media flow
across firewalls.



<img width="1366" height="768" alt="Screenshot from 2025-12-17 16-37-38" src="https://github.com/user-attachments/assets/a4e40db1-ec0c-48c7-acc5-3c9e2a132a40" />


<img width="1366" height="768" alt="Screenshot from 2025-12-16 17-25-33" src="https://github.com/user-attachments/assets/2775e2d0-5fcf-4ea3-9e56-1c594af35b4b" />

