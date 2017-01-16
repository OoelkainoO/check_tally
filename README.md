Introduction
------------

check_tally is a Nagios monitor script to monitor and report per minute & per hour failed login attempts.  Using and configuring Nagios is beyond the scope of this document.   Please refer to Nagios & NRPE installation and administration documentation for instructions to integrate check_tally into Nagios.

Usage
-----

/usr/local/nagios/libexec/check_tally -?

check_tally [ -U ] [ -w WARNING ] [ -c CRITICAL ] [ -u USER ] [ -d ] 

    [ -U ]          Update Only.   This option is used in cronjobs to write current tally to log.

    [ -w WARNING ]  Set WARNING level.  Will report WARNING if failed logins/hour is greater than WARNING.

    [ -c CRITICAL ] Set CRITICAL level. Will report WARNING if failed logins/hour is greater than CRITICAL.

    [ -u USER ]     Specify USER.  Used for cronjobs and reporting.

    [ -d ]          Enable DEBUG.   Detailed output in /var/log/check_tally_USER.log


Installation
------------------------

  * NOTE:  It's required to configure pam_tally2.so in pam before proceeding.   It will enable the pam_tally couner and will also help prevent brute force attacks to your system. See man pam_tally2 for more details.

  * Add the following line to the "auth" section of /etc/pam.d/password-auth-ac.
```
auth        required      pam_tally2.so  file=/var/log/tallylog deny=3 even_deny_root unlock_time=60
```

  * Add the following line to the "account" section of /etc/pam.d/password-auth-ac.
```
account     required      pam_tally2.so
```



*  The check_tally script must be copied to the Nagios libexec directory (typically /usr/local/nagios/libexec/).  Set appropriate permissions and owner to the script.


```
cp check_tally /usr/local/nagios/libexec/check_tally
chmod 750 /usr/local/nagios/libexec/check_tally
chown root:nagios /usr/local/nagios/libexec/check_tally
```


  * check_tally must be run every minute for each user you want to monitor.  This saves the current tally in log files stored in /var/log/tally/.  To monitor root user, add the following entry to the root's crontab.

```
* * * * * /usr/local/nagios/libexec/check_tally -U -u root -d  >/dev/null 2>&1
```


  * To monitor using Nagios, add check_tally to the nrpe.cfg config file.  For example append the following line to /usr/local/nagios/etc/nrpe.cfg.  $ARG1$=WARNING (default 10), $ARG2$=CRITICALi (default 20), $ARG3$=user (default root).

```
command[check_tally]=/usr/local/nagios/libexec/check_tally -w $ARG1$ -c $ARG2$ -u $ARG3$
```



License
-------

Copyright (C) 2015 Jonathan Senkerik

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.


Support
-------
  Website : http://www.jintegrate.co

  github  : http://github.com/josenk/check_tally

  Please support my work and efforts contributing to the Linux community.

