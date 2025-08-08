# LOCALIZATION

## CHAR SETS

| Format    | Size        | Description |
| -         | -           | - |
| ASCII     | 7 bits      | American Standard Code for Information Interchange|
| Unicode   | 3 bytes     | Can represent every known character|
| UTF       | 1–2 bytes   | Transforms Unicode to smaller formats like UTF-8 (1 byte) or UTF-16 (2 bytes)|

## ENVIROMENT VARIABLES

**`locale`**\
--> get locale information.

    LANG=en_US.UTF-8 < language_country.charset
    LC_CTYPE="en_US.UTF-8"
    LC_MONETARY="en_US.UTF-8"
    ...

**`locale -ck LC_MONETARY`**\
--> get detailed settings for LC_MONETARY category.\

    LC_MONETARY
    int_curr_symbol="USD"
    currency_symbol="$"
    mon_grouping=3;3
    ...

**VARIABLE TYPES**

| Variable  | Purpose |
| -         | - |
| `LANG`    | Sets the **default locale** for all categories, unless overridden|
| `LC_*`    | Allows **fine-grained control** of specific locale categories (e.g., time, number format)|
| `LC_ALL`  | **Overrides all** other locale settings, including `LANG` and all `LC_*`<br>Used for temporary overrides or debugging.<br>Only in some distros|

---

**`$ export LC_MONETARY=en_GB.UTF-8`**\
--> change monetary setting temporarily.

- **these changes are temporary**, for a permenant solution add the export command to **.bashrc** in **$HOME** folder.
- if the system is using **systemd**, the `localectl` command is availabe by default to change settings **permanently**.

```
    $ localectl
      system locale : LANG=en_US.UTF-8
      vc keymap     : us
      x11 layout    : us
```

**`$ localectl set-locale LANG=en_GB.utf8`**\
--> change lang setting permanently.

---

## TIME

|Config File        |Distro|
|-                  |-|
|**/etc/localtime** |redhat|
|**/etc/timezone**  |debian|

- this file cannot be modified manually.
- instead a suitable template file must be linked from the following folder.
    + **/usr/share/zoneinfo**

**`$ ls -al /etc/localtime`**\
--> show linked file (active timezone).

**`$ hwclock`**\
--> display hardware clock.

**`$ date`**\
--> display curent time and time zone.

| Format Code       | Description |
| -                 | - |
| **%a**            | Weekday name abbreviated|
| **%A**            | Weekday name full|
| **%b** / **%h**   | Month abbreviated|
| **%B**            | Month full|
| **%d**            | Day of month numeric|
| **%F**            | Date in SQL format|
| **%g** / **%y**   | Last two digits of year|
| **%H**            | Hour in 24h format|
| **%I**            | Hour in 12h format|
| **%j**            | Day of year|
| **%m**            | Month numeric|
| **%M**            | Minute|
| **%p**            | AM or PM|
| **%s**            | UNIX time (seconds since epoch)|
| **%S**            | Seconds|
| **%T**            | Hour\:minute\:second|
| **%u**            | Day of week numeric (1 = Monday)|
| **%U**            | Week of year numeric (starting Sunday)|
| **%w**            | Day of week numeric (0 = Sunday)|
| **%x**            | Locale date format|
| **%X**            | Locale time format|
| **%Y**            | Year|
| **%z**            | Timezone offset hhmm (**%:z** hh\:mm, **%::z** hh\:mm\:ss)|
| **%Z**            | Timezone abbreviation|

    $ date +"%A, %B %d"
    Saturday, December 04

**`$ timedatectl set-time "2021-12-12 08:30:00"`**\
--> set date-time.

**NTP**

network time protocol

> **!** `ntpd` - legacy\
> **!** `chrony` - **/etc/chrony/chrony.conf**\
> **!** `timesyncd` - **/etc/systemd/timesyncd.conf** file or **timesyncd.d folder ( multiple settings)**.