# Linux Cron

## Run command in specific user environment

    * * * * * /usr/bin/bash -l -c <command>

## Simulate cron execution

Store CRON environment entries and run script as cron with it.

    * * * * *   /usr/bin/env > /tmp/cron-env

Let's have a testing script `runAsCron.sh` with this content

    #!/bin/bash
    /usr/bin/env -i $(cat /tmp/cron-env) "$@"

Then simulate run as if it were in crontab

    ./runAsCron.sh /path/to/script-for-debug.sh --with params ...

