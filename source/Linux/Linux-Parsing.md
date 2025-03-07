# Linux Parsing

## Multiline parsing

Parse section beginning with `[ERROR` and ends where new line begins next `[`

    sed -n '/^\[ERROR/,/^\[/p' app.log

Parse all sections beginning with regexp `^--.*-A--$` and create file for every section named `MODSEC_<number>`

    awk '/^--.*-A--$/ {x="MODSEC_"++i}; i > 0 {print > x}' modsec_audit.log

Find first occurence of ${D} and get all content to the end of the file

    D=$(date "+%Y-%m-%d %H:%M:" --date='1 mins ago')
    sed -n '/'"$D"'/,$p' app.log
