# macOS Java

## Installtion from Oracle

[Oracle Install JDK](https://docs.oracle.com/en/java/javase/15/install/installation-jdk-macos.html#GUID-F575EB4A-70D3-4AB4-A20E-DBE95171AB5F)
[Oracle Tech Downloads universal link](https://www.oracle.com/java/technologies/downloads/)

Destination is `/Library/Java/JavaVirtualMachines/` as folder.

## Uninstallation

    rm -rf  /Library/Java/JavaVirtualMachines/jdk-17.0.2.jdk/

## Installation of Azul for ARM CPU (M chips)

[https://www.azul.com/downloads/?package=jdk#download-openjdk](https://www.azul.com/downloads/?package=jdk#download-openjdk)

After unpack copy `zulu-8.jdk` to `/Library/Java/JavaVirtualMachines/`

## JNLP under Java 8 (you can try 11 or 17 also)

You don't want to use this.  

[https://openwebstart.com/download/](https://openwebstart.com/download/)
Setup Proxy and JVM.  
Then run JNLP from Firefox, it won't open due to Security. So open Settings > Security and click open anyway ...
