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

    * * * * * nagios /usr/local/bin/pd_ack_to_nagios_ack_poller.pl --pagerduty_token_file /etc/pagerduty/pd_token_file -t 3

note this will generally need to be run as the nagios user so that it has write access to the nagios command pipe.

other important options are

    --nagios_status_file <_file> | -s <_file> (default /var/cache/nagios/status.dat)
    --nagios_command_pipe <_file> | -c <_file> (default /var/spool/nagios/cmd/nagios.cmd)
    --days_back <_days> | -t <_service> (the amount of time in days in the past to look for Nagios incidents - default and minimum value is 1)
    --pagerduty_token_file <_file> | -f <_file> (default /etc/nagios/pd_ack_to_nagios_ack_poller.key)

the file locations are dependent on your install, so locate them first before running

Note: Setting the --days_back parameter is so that we retrieve incidents that were created a limited time in the past. Retrieving all events would be time-prohibitive. 

> <font COLOR="RED">IMPORTANT: the larger the --days_back / -t parameter, the longer the script will run.  Be sure to check to see how long it runs using your
desired setting. Set the cron appropriately so that it does not re-run before the prior run is complete. Running every 10 to 30 minutes should be sufficient for most use-cases.</FONT>

The option to limit by the PagerDuty incident number has been removed because this approach made it possible to miss acknowledgements and resolutions. The day_back parameter allows for a better way of limiting past incidents. 
