# Linux Parsing

## Multiline parsing

Parse section beginning with `[ERROR` and ends where new line begins next `[`

    sed -n '/^\[ERROR/,/^\[/p' app.log

Parse all sections beginning with regexp `^--.*-A--$` and create file for every section named `MODSEC_<number>`

    awk '/^--.*-A--$/ {x="MODSEC_"++i}; i > 0 {print > x}' modsec_audit.log

Find first occurence of ${D} and get all content to the end of the file

    D=$(date "+%Y-%m-%d %H:%M:" --date='1 mins ago')
    sed -n '/'"$D"'/,$p' app.log

## jq - JSON parser

    {
        "data": [
            {
                "type": "vzmigrate",
                "user": "root@pam",
                "id": "101",
                "endtime": 1716975183,
                "status": "OK",
                "node": "vpvecommon3",
                "upid": "UPID:vpvecommon3:00397036:766AAF6F:6656F611:vzmigrate:101:root@pam:",
                "saved": "1",
                "starttime": 1716975121
            },
            {
                "status": "migration aborted",
                "node": "vpvecommon3",
                "upid": "UPID:vpvecommon3:00396F69:766A9641:6656F5D0:vzmigrate:101:root@pam:",
                "saved": "1",
                "starttime": 1716975056,
                "type": "vzmigrate",
                "user": "root@pam",
                "endtime": 1716975056,
                "id": "101"
            }
        ]
    }

Parse element conditionally

     jq '.data[] | select(.type == "vzmigrate" and .saved == "0")'

Parse element conditionally and return one key

    jq -r '.data[] | select(.type == "vzmigrate" and .saved == "0") | .upid'

Parse element conditionally and return more keys using more formats

    jq -r '.data[] | select(.type == "vzmigrate" and .saved == "0") | {upid, user}'           ### JSON
    jq -r '.data[] | select(.type == "vzmigrate" and .saved == "0") | "\(.upid)\t\(.user)"'   ### Separated using TABs
    jq -r '.data[] | select(.type == "vzmigrate" and .saved == "0") | "\(.upid)---\(.user)"'  ### Separated using 3 hyphens (---)
