pd_ack_to_nagios_ack_poller.pl
===============

to supplement https://github.com/PagerDuty/pagerduty-nagios-pl

pagerduty_nagios.pl does a good job of getting nagios events into
pagerduty... this script provides the reverse, for a more
round-trip-synced experience

more specifically, it polls pagerduty for new incidents since the last
poll, checks whether they are acknowledged or resolved, and if so sends
a Nagios ack to the corresponding nagios alerts.  i.e., if you ack in
pagerduty, via sms or web ui or phone app, that ack will find it's way
back to your nagios instance.

this works for both service and host events

sending acks to Nagios can be done via webhooks, but customers who don't
want to open the firewall to incoming HTTP traffic will like this alternative

works only with PagerDuty's V2 API token

set up a cron like so:

    * * * * * nagios /usr/local/bin/pd_ack_to_nagios_ack_poller.pl -p [my_pagerduty_token]

note this will generally need to be run as the nagios user so that it has write access to the nagios command pipe.

other important options are

    --nagios_status_file <_file> | -s <_file> (default /var/cache/nagios/status.dat)
    --nagios_command_pipe <_file> | -c <_file> (default /var/spool/nagios/cmd/nagios.cmd)

these locations are dependent on your install, so locate them first before running
