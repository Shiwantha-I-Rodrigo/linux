# LOCALIZATION

## CHAR SETS

- ASCII (American Standard Code for Information Interchange)
    + 7 bits.

- Unicode
    + 3 bytes.
    + can represent every known char.

- UTF (Unicode Transformation format)
    + transform longer Unicode format to 1 Byte (UTF-8) or 2 Byte (UTF-16) codes.

## ENVIROMENT VARIABLES

get locale information.\
`$ locale`

LANG=en_US.UTF-8 < language_country.charset
LC_CTYPE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
...

get detailed settings for LC_MONETARY category.\
`$ locale -ck LC_MONETARY`

LC_MONETARY
int_curr_symbol="USD"
currency_symbol="$"
mon_grouping=3;3
...

> settings can be changed individualy with "LC" settings or all at once using the "LANG" setting.\
> some distros require to set "LC_ALL" along with the "LANG".

`$ export LC_MONETARY=en_GB.UTF-8`

**these changes are temporary**, for a permenant solution add the export command to .bashrc in $HOME folder.

if the system is using *systemd*, the `localectl` command is availabe by default.

    $ localectl
      system locale : LANG=en_US.UTF-8
      vc keymap     : us
      x11 layout    : us

`$ localectl set-locale LANG=en_GB.utf8`

### TIME

`$ hwclock` < display hardware clock

- /etc/localtime <redhat>
- /etc/timezone <debian>

this file cannot be modified manually.\
instead a suitable template file must be linked from the following folder.

- /usr/share/zoneinfo

`$ ls -al /etc/localtime` < show linked file (active timezone)

`$ date` < display curent time and time zone

    $ date +"%A, %B %d"     >   Saturday, December 04

    %a  weekdayname abbr.
    %A  weekdayname
    %b  month   abbr. ( %h )
    %B  month
    %c  date and time
    %C  century
    %d  day of month numeric
    %D  full date numeric
    %e  day of month with spaces
    %F  date in SQL format
    %g  last wo digits of year ( %y )
    %H  hour in 24h
    %I  hour in 12h
    %j  day of year
    %m  month numeric
    %M  minuite
    %n  new line
    %p  am or pm
    %P  lowercase am or pm
    %s  UNIX time
    %S  seconds
    %t  tab
    %T  hour:minuite:second
    %u  day of week numeric (1 monday)
    %U  week of year numeric (starting sunday)
    %V  week number ISO
    %w  day of week numeric (0 sunday)
    %x  locale date format
    %X  locale time format
    %Y  year
    %z  hhmm ( %:z hh:mm / %::z hh:mm:ss)
    %Z  timezone abbr.

`$ timedatectl set-time "2021-12-12 08:30:00"`

**NTP**

network time protocol

> ntpd\ - legacy\
> chrony - /etc/chrony/chrony.conf\
> timesyncd - /etc/systemd/timesyncd.conf file or timesyncd.d folder for multiple settings