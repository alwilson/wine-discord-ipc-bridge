Wine Discord IPC Bridge
=======================

This program enables other programs which are running under Wine to send Rich
Presence data to a Linux Discord client.

This is mainly a proof-of-concept, demonstrating that this is possible, the
code itself is a mish-mash of copy-paste from Microsoft docs and
[linux-discord-rpc.dll](https://github.com/goeo-/discord-rpc/blob/linux-under-wine/src/connection_win.cpp).

The way it works is simply by bridging the gap between Windows named pipes
(`\\.\pipe\discord-ipc-0`) and Unix domain sockets
(`/run/user/{userid}/discord-ipc-0`).

Compiling
=========

    i686-w64-mingw32-gcc -masm=intel main.c -o winediscordipcbridge

Usage
=====

Just run this program first, wait for it to start listening to the pipe, and
then launch your program/game. The two programs need to be running under the
same wineprefix.

If you're using Steam Play/Proton, add the following to your game's launch
options, replacing the necessary fields. Your `{userid}` is likely 1000.
This starts the bridge in the background to act as a 'debugger' before the
game starts and opens up the folder the Linux Discord socket lives in to be
read/write. This worked for Deep Rock Galactic, hopefully the timing of the
bridge works correctly for other games.

Properties -> General -> Launch Options:

    PROTON_REMOTE_DEBUG_CMD=/path/to/winediscordipcbridge.exe PRESSURE_VESSEL_FILESYSTEMS_RW="/run/user/{userid}" %command%

The script `winediscordipcbridge-steam.sh` worked on older versions but
[parsing changed and there's problems](https://github.com/0e4ef622/wine-discord-ipc-bridge/issues/17) getting it to run in the new runtime and
containers Steam uses.

This program should automatically stop once the other program stops, although
it'll keep running if nothing ever connects.

I've personally tested this with osu! on both 32-bit and 64-bit wineprefixes,
and Muse Dash using the script to launch from Steam.

https://github.com/lhark/truckersmp-cli also [reported success](https://github.com/0e4ef622/wine-discord-ipc-bridge/issues/6#issuecomment-712266806).
