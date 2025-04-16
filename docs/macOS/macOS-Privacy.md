# masOS Privacy

## Reset access to Camera and Microphone

    sudo tccutil reset Camera
    sudo tccutil reset Microphone

## Reset access for Terminal app

    sudo tccutil reset Camera com.apple.Terminal
    sudo tccutil reset All com.apple.Terminal

## Reset ALL

    sudo tccutil reset All

## man tccutil

    sudo tccutil command service [bundle_id]

service could be

    Accessibility
    AddressBook
    AppleEvents      ### Automation
    Calendar
    Camera
    Microphone
    Photos
    Reminders
    ScreenCapture
    SystemPolicyAllFiles
    SystemPolicyDesktopFolder
    SystemPolicyDeveloperFiles
    SystemPolicyDocumentsFolder
    SystemPolicyDownloadsFolder
    SystemPolicyNetworkVolumes
    SystemPolicyRemovableVolumes
    SystemPolicySysAdminFiles

## Check app access

[Use Apparency app](https://www.mothersruin.com/software/Apparency/use.html)

