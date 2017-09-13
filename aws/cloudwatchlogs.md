
install agent

```
sudo yum install -y awslogs
```

configure
```
cat /etc/awslogs/awscli.conf
[plugins]
cwlogs = cwlogs
[default]
region = us-east-2

# if not have instance profile with role
# aws_access_key_id = <YOUR ACCESS KEY>
# aws_secret_access_key = <YOUR SECRET KEY>
```

```
 cat /etc/awslogs/awslogs.conf

#
# ------------------------------------------
# CLOUDWATCH LOGS AGENT CONFIGURATION FILE
# ------------------------------------------
#
# --- DESCRIPTION ---
# This file is used by the CloudWatch Logs Agent to specify what log data to send to the service and how.
# You can modify this file at any time to add, remove or change configuration.
#
# NOTE: A running agent must be stopped and restarted for configuration changes to take effect.
#
# --- CLOUDWATCH LOGS DOCUMENTATION ---
# https://aws.amazon.com/documentation/cloudwatch/
#
# --- CLOUDWATCH LOGS CONSOLE ---
# https://console.aws.amazon.com/cloudwatch/home?region=us-east-1#logs:
#
# --- AGENT COMMANDS ---
# To check or change the running status of the CloudWatch Logs Agent, use the following:
#
# To check running status: service awslogs status
# To stop the agent: service awslogs stop
# To start the agent: service awslogs start
# To start the agent on server startup: chkconfig awslogs on
#
# --- AGENT LOG OUTPUT ---
# You can find logs for the agent in /var/log/awslogs.log
#

# ------------------------------------------
# CONFIGURATION DETAILS
# ------------------------------------------

[general]
# Path to the CloudWatch Logs agent's state file. The agent uses this file to maintain
# client side state across its executions.
state_file = /var/lib/awslogs/agent-state

## Each log file is defined in its own section. The section name doesn't
## matter as long as its unique within this file.
#[kern.log]
#
## Path of log file for the agent to monitor and upload.
#file = /var/log/kern.log
#
## Name of the destination log group.
#log_group_name = kern.log
#
## Name of the destination log stream. You may use {hostname} to use target machine's hostname.
#log_stream_name = {instance_id} # Defaults to ec2 instance id
#
## Format specifier for timestamp parsing. Here are some sample formats:
## Use '%b %d %H:%M:%S' for syslog (Apr 24 08:38:42)
## Use '%d/%b/%Y:%H:%M:%S' for apache log (10/Oct/2000:13:55:36)
## Use '%Y-%m-%d %H:%M:%S' for rails log (2008-09-08 11:52:54)
#datetime_format = %b %d %H:%M:%S # Specification details in the table below.
#
## A batch is buffered for buffer-duration amount of time or 32KB of log events.
## Defaults to 5000 ms and its minimum value is 5000 ms.
#buffer_duration = 5000
#
# Use 'end_of_file' to start reading from the end of the file.
# Use 'start_of_file' to start reading from the beginning of the file.
#initial_position = start_of_file
#
## Encoding of file
#encoding = utf-8 # Other supported encodings include: ascii, latin-1
#
#
#
# Following table documents the detailed datetime format specification:
# ----------------------------------------------------------------------------------------------------------------------
# Directive     Meaning                                                                                 Example
# ----------------------------------------------------------------------------------------------------------------------
# %a            Weekday as locale's abbreviated name.                                                   Sun, Mon, ..., Sat (en_US)
# ----------------------------------------------------------------------------------------------------------------------
#  %A           Weekday as locale's full name.                                                          Sunday, Monday, ..., Saturday (en_US)
# ----------------------------------------------------------------------------------------------------------------------
#  %w           Weekday as a decimal number, where 0 is Sunday and 6 is Saturday.                       0, 1, ..., 6
# ----------------------------------------------------------------------------------------------------------------------
#  %d           Day of the month as a zero-padded decimal numbers.                                      01, 02, ..., 31
# ----------------------------------------------------------------------------------------------------------------------
#  %b           Month as locale's abbreviated name.                                                     Jan, Feb, ..., Dec (en_US)
# ----------------------------------------------------------------------------------------------------------------------
#  %B           Month as locale's full name.                                                            January, February, ..., December (en_US)
# ----------------------------------------------------------------------------------------------------------------------
#  %m           Month as a zero-padded decimal number.                                                  01, 02, ..., 12
# ----------------------------------------------------------------------------------------------------------------------
#  %y           Year without century as a zero-padded decimal number.                                   00, 01, ..., 99
# ----------------------------------------------------------------------------------------------------------------------
#  %Y           Year with century as a decimal number.                                                  1970, 1988, 2001, 2013
# ----------------------------------------------------------------------------------------------------------------------
#  %H           Hour (24-hour clock) as a zero-padded decimal number.                                   00, 01, ..., 23
# ----------------------------------------------------------------------------------------------------------------------
#  %I           Hour (12-hour clock) as a zero-padded decimal numbers.                                  01, 02, ..., 12
# ----------------------------------------------------------------------------------------------------------------------
#  %p           Locale's equivalent of either AM or PM.                                                 AM, PM (en_US)
# ----------------------------------------------------------------------------------------------------------------------
#  %M           Minute as a zero-padded decimal number.                                                 00, 01, ..., 59
# ----------------------------------------------------------------------------------------------------------------------
#  %S           Second as a zero-padded decimal numbers.                                                00, 01, ..., 59
# ----------------------------------------------------------------------------------------------------------------------
#  %f           Microsecond as a decimal number, zero-padded on the left.                               000000, 000001, ..., 999999
# ----------------------------------------------------------------------------------------------------------------------
#  %z           UTC offset in the form +HHMM or -HHMM (empty string if the the object is naive).        (empty), +0000, -0400, +1030
# ----------------------------------------------------------------------------------------------------------------------
#  %j           Day of the year as a zero-padded decimal number.                                        001, 002, ..., 365
# ----------------------------------------------------------------------------------------------------------------------
#  %U           Week number of the year (Sunday as the first day of the week) as a zero padded          00, 01, ..., 53
#               decimal number. All days in a new year preceding the first Sunday are considered
#               to be in week 0.
# ----------------------------------------------------------------------------------------------------------------------
#  %W           Week number of the year (Monday as the first day of the week) as a decimal number.      00, 01, ..., 53
#               All days in a new year preceding the first Monday are considered to be in week 0.
# ----------------------------------------------------------------------------------------------------------------------
#  %c           Locale's appropriate date and time representation.                                      Tue Aug 16 21:30:00 1988 (en_US)
# ----------------------------------------------------------------------------------------------------------------------


[/var/log/messages]
datetime_format = %b %d %H:%M:%S
file = /var/log/messages
buffer_duration = 5000
log_stream_name = {instance_id}
initial_position = start_of_file
log_group_name = /var/log/messages
```


for httpd
```
[/var/log/httpd/access_log]
log_group_name = /var/log/httpd/access_log
log_stream_name ={instance_id}_httpd_access
datetime_format = %b %d %H:%M:%S
file = /var/log/httpd/access_log
initial_position = start_of_file
buffer_duration = 5000


[/var/log/httpd/error_log]
log_group_name = /var/log/httpd/error_log
log_stream_name = {instance_id}_httpd_error
datetime_format = [%a %b %d %H:%M:%S %Y]
file = /var/log/httpd/error_log
initial_position = start_of_file
buffer_duration = 5000
```

```
service awslogs restart
```

```
tail -10 /var/log/awslogs.log
```

```
aws logs get-log-events --log-group-name /var/log/httpd/access_log --log-stream-name i-0a0bc18e477e6af93_httpd_access 
{
    "nextForwardToken": "f/33544437833675484539481076648850149556420982990450196483",
    "events": [
        {
            "ingestionTime": 1504184621212,
            "timestamp": 1504184615135,
            "message": "182.22.59.22 - - [31/Aug/2017:13:00:16 +0000] \"GET / HTTP/1.1\" 403 4891 \"-\" \"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.113 Safari/537.36\""
        },
        {
            "ingestionTime": 1504184621212,
            "timestamp": 1504184615135,
            "message": "182.22.59.22 - - [31/Aug/2017:13:00:17 +0000] \"GET /icons/apache_pb2.gif HTTP/1.1\" 200 4234 \"http://13.58.211.21/\" \"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.113 Safari/537.36\""
        },
        {
            "ingestionTime": 1504184621212,
            "timestamp": 1504184615135,
            "message": "182.22.59.22 - - [31/Aug/2017:13:00:17 +0000] \"GET /icons/poweredby.png HTTP/1.1\" 200 3412 \"http://13.58.211.21/\" \"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.113 Safari/537.36\""
        },
        {
            "ingestionTime": 1504184621212,
            "timestamp": 1504184615135,
            "message": "182.22.59.22 - - [31/Aug/2017:13:00:17 +0000] \"GET /favicon.ico HTTP/1.1\" 404 209 \"http://13.58.211.21/\" \"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.113 Safari/537.36\""
        }
    ],
    "nextBackwardToken": "b/33544437833675484539481076648850149556420982990450196480"
}
```