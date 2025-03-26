# Google

## Create script to delete all messages with label

To automatically delete sent emails, create filter `from ME@gmail.com` and add label `ToBeDeleted`.  
Then create new script on [script.google.com](https://script.google.com)

    function deleteSentMails() {
    var label = GmailApp.getUserLabelByName("ToBeDeleted");  
    var threads = label.getThreads();  
    for (var i = 0; i < threads.length; i++) {  
        threads[i].moveToTrash();
        } 
    }

And add trigger to run it every day for example between 02:00 and 03:00.

## Chrome

### Delete HSTS for domain / host

`chrome://net-internals/#hsts`

### Continue to page with SSL error

Anywhere on page just write `thisisunsafe`

### Ignore Invalid Certificate

When messages like `NET::ERR_CERT_AUTHORITY_INVALID` or `NET::ERR_CERT_COMMON_NAME_INVALID` appear.

    Google\ Chrome --ignore-certificate-errors
    chromium-browser --start-fullscreen --ignore-certificate-errors --test-type
    # more to test --disable-popup-blocking --kiosk --disable-web-security
