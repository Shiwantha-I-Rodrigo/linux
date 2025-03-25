# GUI

on linux, GUI is a series of components working together to provide a graphical interface.

## DESKTOP ENVIRONMENT

1. desktop settings

programs that allow configuration changes to the desktop environment.

2. display manager

login screen.

3. file manager

programs allowing file maintenance activities graphically.

4. icons

image representation of a file or application.

5. favourites bar

window area with icons of files or applications used regularly.

6. launch

allow searching for applications and files.

7. menus

typically accesed via an icon. may contain file actions.

8. panels

slim and rectangulat area at the top, bottom or side of the desktop.

9. system tray

special menu attached to a panel to control system.

10. widgets

applets, scrrenlets , etc provide information or functionality of a program and quckly accessible though the desktop.

11. window manager

determine how windows are presented on the desktop.

### GNOME

- 1990s
- CentOS / Ubuntu
- GNOME 3 ( aka GNOME Shell)

.

    display manager     GNOME Display Manager (GDM).
    file manager        GNOME Files (formerly Nautils).
    favourites bar      GNOME Shell dash.
    window manager      Mutter.

### KDE Plasma

- 1996 start
- 1998 release

.

    display manager     SDDM.
    file manager        Dolphin.
    favourite bar       inside application menu.
    widgets             plasmoids.
    window manager      Kwin.

### CINNAMON

- 2011
- fork of GNOME3

.

    display manager     LightDM.
    file manager        Nemo.(Nautils Fork)
    favourites bar      inside application menu.
    Widgets             Cinnamon Spices.
    window manager      Muffin.(Mutter fork).

### MATE

- 2011
- fork of GNOME2

.

    display manager     LightDM.
    file manager        caja.(Nautils Fork)
    favourites menu     via application menu launcher.
    window manager      Marco.(Metacity fork).

## ACCESSIBILITY

    **Visualy Impaired**

    Cursor Blinking     inc. cursor blinking rate.
    Cursor Size         inc. cursor size.
    High Contrast       inc. brightness of windows, darken edges, text and cursor.
    large Text          inc. font size.
    Screen Reader       read the ui out loud.
    Sound Keys          beeps when caps lock or num lock is on.
    Zoom                magnify the screen or a portion of the screen.

    **Hearing Impaired**

    Visual Flash        produce a flash on a window or screen on system sounds.

    **physically impaired**

    Bounce Keys                 componsate for single key pressed multiple times.
    Double-Click Delay          inc. amount of time alowed for a double click.
    Gestures                    activate programs via combining mouse clicks and keyboard presses.
    Hover Click                 trigger click on hover.
    Mouse Keys                  keyboard keys to emulate mouse functions.
    Repeat Keys                 mod time to acknowledge key press and hold as repeated presses.
    screen keyboard             virtual keyboard.
    simulated secondary click   set a key to held along with mouse click to emulate secondery clicks.
    slow clicks                 mod time to acknowledge as a key press.
    sticky keys                 modifier keys (ctrl, shift, etc) maintain pressed status untilsubsequent key is pressed.

## DISPLAY SERVER

desktop environment ↔ window manager ↔ display server

**compositor** a component of the display server is used to arrenge various elements\
within a window and pass the final composit image to the client.

>x11

                kernal
                    ↑
    x_client ↔ x_server
                    ↓
                compositor

>wayland

    wayland_client ↔ wayland compositor ↔ kernal

### Wayland Server

- replacement for x11 server
- 2009
- instead of weston(default) Arcan, Sway, Clayland, Lipstick,  etc.. compositors can be used with wayland.

`$ echo $WAYLAND_DISPLAY`\
if a response is returned a wayland display server is running.\
else x11 may be running.

**TROUBLESHOOTING**

in case of display issues,
1. try an x11 session.
2. check graphic card support for wayland.
3. install weston compositor, if the current distro uses another.

### X11

- 1980
- CTRL+ALT+BackSpace to restart X11 session.
- /etc/X11/xorg.conf
    + typically not used anymore, session configurations on the fly by autodetecting hardware
- incase autodetect fails,
    + `$ sudo Xorg -configure` to generate the config file in current dir.
    + move the file to proper dir.
- xorg configuration file contains,
    + input device - mouse / keyboard
    + monitor - monitor configs
    + modes - video mode
    + device - sessions video card
    + screen - resolution, color depth
    + modules - modules to be loaded
    + files - file paths for, fonts, layouts , etc...
    + server flags - global X server options
    + server layout - links input and output devices

**TROUBLESHOOTING**

1. xdpyinfo
    + information on xserver screen types, communication params, protocol extentions, etc...
2. xwininfo
    + information on windows, incase of no options an interactive mode to select windows.
    + information such as width, height, color map id, etc...
    + command will hang in case of wayland session.

## REMOTE DESKTOPS

when a fully functional desktop is required instead of SSH for a remote server,
- VNC
- Xrdp
- NX
- SPICE

### VNC

**Virtual Network Computing** employs remote frame buffer technology.

> typically display server on TCP port 5900 + n where n is the display number.

on cli point the *viewer* to hostname and tcp port,\
alternatively display number can be directly given instead of port number.\
enter the predetermined password for VNC.
can also be accessed through a java enabled or HTML5 browser.

two types of UI's
1. persistent
    - GUI state is saved for next unlock.
2. static
    - does not provide saved states.

+ PROS
    - flexibility
    - desktops availabe for multiple users
    - persistent and static desktops
    - provide desktops on demand
    - ssh tunnel can be employed on top of wiever cli command

+ CONS
    - no file (local mounting) or audio transfer
    - no printing service
    - no default traffic encryption, must use openSSH
    - password stored in plain text on server

> cli command must be issued in a graphical session, else command will fail.

### Xrdp

- support RDP (Remote Desktop Protocol)
- only server side RDp connection
- use a rdesktop, freeFDP, MicrosoftRemoteDesktop, etc... client implementation.

> typically display server on TCP port 3389.

+ PRO
    - use RDP which encrypts traffic with TLS
    - wide range of client software.
    - can connect to an existing connection (persistent).
    - provide audio and file transfer (mounting local drives on remote system)
    - /etc/xrdp/xrdp.ini
        + set security_layer directive to negotiate to negotiate with client.
            * tls - safe
            * negotiate - potentialy unsafe
            * rdp - unsafe

### NX

- was open source until version 4
- free derivitives exist (FreeNX, X2Go, ...).
- compress x11 data to minimize data over network.

+ PRO
    - excelent response time, low latency over low bandwith.
    - faster that VNC based products.
    - traffic is encrypted with OpenSSH by default.
    - support multiple users, through single port.

### SPICE

- Simple Protocol for Independent Computing Environment.
- made opensource by redhat.
- like VNC provide connections to KVM virtual machines.

+ PRO
    - platform independent.
    - can handle multiple clients.
    - speeds similar to a local connection.
    - comsumes low CPU.
    - high quality video streams.
    - live migration, no interruption in case the virtual machine is being migrated to a new host.

### FORWARDING

providing security through SSH port forwarding (SSH tunneling). OpenSSH must be running and listening on port 22.

`$ systemctl is-active sshd`

`$ systemctl is-enabled sshd`

/etc/ssh/sshd_config < check config file for "AllowTcpForwarding" is set to "yes".

> even if the "AllowTcpForwarding" is **commented out**, it is eanbled as it is the default behavior.

port forwarding can be,
    - local
    - remote
    - dynamic


#### LOCAL

1. data is sent from client to the client's OpenSSH server.
2. data is encrypted.
3. data is sent from client server (different outbound port) to the destination.

`$ ssh -L 5901:127.0.0.1:5902 -Nf robert@remote1`\
`$ ssh -L [local-port]:127.0.0.1:[remote-port] -Nf [user@RemoteHost]`

- L < local
- N < no remote terminal required.
- f < move the ssh command to background after user authentication.
- 5900 + n ports used with VNC


#### REMOTE

- the port forwarding method start at the destination host.

`$ ssh -R 5901:127.0.0.1:5902 -Nf robert@client1`\
`$ ssh -R [local-port]:127.0.0.1:[remote-port] -Nf [user@ClientHost]`

- R < Remote

### TUNNELING X11

- /etc/ssh/sshd_config < check "x11Forwarding" is set to "yes">

`$ ssh -X user@RemoteHost`