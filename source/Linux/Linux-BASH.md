# Linux BASH

## Script dir and name

    SCRIPT_NAME=${0##*/}
    SCRIPT_PATH="$( dirname -- "${BASH_SOURCE[0]}"; )/"
    # TMP_FILE_BASE64="/dev/shm/${SCRIPT_NAME}.data.base64" ...
    # TMP_FILE_BASE64="$(mktemp)"

## Random Number, String

    # numbers (from 1 to 50)
    RANDOM_NUMBER=$(echo $(( $RANDOM % 50 + 1 )))
    # lowercase + numbers
    OPERATION_ID=$(echo $RANDOM | md5sum | head -c 20)
    # lowercase + numbers + uppercase
    SIMPLE_PASS=$(tr -dc a-zA-Z0-9 < /dev/urandom | head -c 16)

## Minutes since EPOCH

    MINUTES_SINCE_EPOCH=$(($EPOCHSECONDS/60))

## Check if variable is NUMBER

    numbers='^[0-9]+$'
    if [[ ! ${1} =~ $numbers ]] ; then echo "${SCRIPT_NAME}: [ ${1} ] is NOT a number!" ; return 1 ; fi

## Uppercase, Lowercase

    str="string"
    echo ${str^} nebo echo ${str^^}
    str="STRING"
    echo ${str,} nebo echo ${str,,}

## Substring

Cut chars from the end

    FIID_SQL=${FIID_LINE::-1}

Cut chars from the beginning

    VAR_WITHOUT_PREFIX=${VAR:4}

Show count of chars from-to particular postion

    VAR="123456789" ; echo ${VAR:3:2}

## Content that doesn't needs to be escaped

    VAR=$(cat << 'EOF'
    Single: ' and Double: " quotes variable here.
    EOF
    )

## Count new lines per second in log file

    tail -F output.txt | { count=0; old=$(date +%s); while read line; do ((count++)); s=$(date +%s); if [ "$s" -ne "$old" ]; then echo "$count lines per second"; count=0; old=$s; fi; done; }

## Redirect all script ouptut (edge case exists) to log

    d() {
        date +"%d.%m.%Y - %H:%M:%S"
    }

    LOGDIR="${SCRIPT_NAME}.logdir/"
    if [ ! -d "${LOGDIR}" ] ; then mkdir ${LOGDIR} ; fi
    LOGFILE="`date "+%Y_%m_%d"`"
    LOG="${LOGDIR}${LOGFILE}"

    ### LOG TO FILE ONLY
    echo "All outputs are redirected to [ ${LOG} ]"
    if touch ${LOG}
    then
        exec 1>>${LOG}
        exec 2>>${LOG}
    else
        echo "[ CRITICAL ] - $(d) - ${SCRIPT_NAME} ERROR: Can't create LOG FILE [ ${LOG} ]. Script FAILED !!!"
        exit 1
    fi

    ### LOG TO FILE and STDOUT
    echo "All outputs are redirected to [ ${LOG} ] and standard out"
    if touch ${LOG}
    then
        exec 3>&1 4>&2
        exec > >(tee -a "${LOG}" >&3) 2> >(tee -a "${LOG}" >&4)
    else
        echo "[ CRITICAL ] - $(d) - ${SCRIPT_NAME} : Can't create LOG FILE [ ${LOG} ]. Script FAILED !!!"
        exit 1
    fi

## Log file lines retention

    #!/bin/bash

    LOG="/path/to/logfile.log"

    ALL_LINES=$(cat ${LOG} | wc -l)
    KEEP_LINES="1000"
    if [ ${ALL_LINES} -lt ${KEEP_LINES} ] ; then echo "Nothing to delete." ; exit 0 ; fi
    DEL_LINES=$((ALL_LINES-KEEP_LINES))

    re='^[0-9]+$'
    if [[ $DEL_LINES =~ $re ]]
    then
        sed -i -e "1,${DEL_LINES}d" "${LOG}"
    else
        echo "[ ${DEL_LINES} ] is not a number!"
    fi

## Redirect only stdout to console and all others to file

    { command | tee /dev/tty; } &> /dev/null

## Counting how long a task took

    #!/bin/bash

    ### Record the start time in nanoseconds
    start_time=$(date +%s%N)

    ### TASK (simulated here)
    sleep 3.2

    ### Record the end time in nanoseconds
    end_time=$(date +%s%N)
    ### Calculate the elapsed time in nanoseconds
    elapsed_time=$((end_time - start_time))

    ### Convert nanoseconds to seconds
    elapsed_seconds=$((elapsed_time / 1000000000))

    echo "Elapsed time: $elapsed_seconds seconds"

## Parallel run of several scripts and wait for all to finish

    ./LVCheck.sh $SRC_LV_DEV "local" &
    ./LVCheck.sh $DST_LV_DEV "remote" "${USER}@${HOSTNAME}" &
    wait $(jobs -p)

## Read and process whole line with spaces

    while read LINE
    do
    echo "${LINE}"
    done < ${FILE}
    ### done < <(command)

## Date and Time

    TIME_MINUTE_AGO=`date --date '-1 min' "+%d%m %H:%M:"`

## Fast count of cycles

    count=0;
    total=0;
    for i in $(getSomeNumber)
    do
    ((count++))
    total=$(($total+$i))
    done

## Counting in BASH

#!/bin/bash

    # Original numbers
    a=50.8
    b=15.3
    # Remove decimal
    aa=$(echo ${a%.*})
    bb=$(echo ${b%.*})
    # Round numbers
    aaa=$(printf %.0f $a)
    bbb=$(printf %.0f $b)

    echo "ORIGINAL $a : $b"
    echo "REMOVED DECIMALS $aa : $bb"
    echo "ROUNED $aaa : $bbb"

    echo " ==== COUNTING ==== "
    echo "+ $((aa+bb))"
    echo "- $((aa-bb))"
    echo "/ $((aa/bb))"
    mod=$((aa%bb))
    echo "MOD $mod"
    echo "DECIMAL $(($mod/$bb))"
    echo "> BASH can NOT count with .decimals."

## Disable & Enable ALL USB script

Show all devices

    for device in $(ls /sys/bus/usb/devices/*/product); do echo $device;cat $device;done

disableUSB.sh

    #!/bin/bash

    #for device in $(ls /sys/bus/usb/devices/*/product)
    for device in $(ls /sys/bus/usb/devices/usb*/product)
    do
        DEVICE_ID=$(echo $device | sed "s;.*devices\/\(.*\)\/product.*;\1;")
        echo "DISABLING DEVICE: $DEVICE_ID ($device --- $(cat $device))"
        echo "${DEVICE_ID}" |sudo tee /sys/bus/usb/drivers/usb/unbind
    done

enableUSB.sh

    #!/bin/bash

    for device in $(ls /sys/bus/usb/devices/*/product)
    do
        DEVICE_ID=$(echo $device | sed "s;.*devices\/\(.*\)\/product.*;\1;")
        echo "ENABLING DEVICE: $DEVICE_ID ($device --- $(cat $device))"
        echo "${DEVICE_ID}" |sudo tee /sys/bus/usb/drivers/usb/bind
    done
