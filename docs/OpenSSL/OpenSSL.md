# OpenSSL

Cipher suite **TLS_RSA**\_WITH\_**AES_128_CBC**_**SHA** uses

* RSA as the **key exchange algorithm**
* AES\_128\_CBC (128 bits AES **cipher algorithm in CBC mode**) as the cipher (encryption) algorithm
* SHA-1 as the **message digest algorithm** for HMAC

`key exchange algorithm___cipher (encryption) algorithm___message digest algorithm`

## Create Request (need sign by CA)

    openssl req -new -newkey rsa:2048 -sha256 -keyout certificate.pem -out certificate.req -nodes
    openssl rsa -in certificate.pem -out certificate.key   ### convert PRIVATE KEY to RSA PRIVATE KEY

## Create Self-signed certificate

    openssl genrsa -out selfsigned.key 2048
    openssl req -new -x509 -key selfsigned.key -out selfsigned.crt -days 3650 -subj /CN=www.mytest-domain.com

## Convert a PFX to PEM (Base64)

Key

    openssl pkcs12 -in certificate.pfx -nocerts -out certificate.key
        ### Remove password from key
    # openssl rsa -in certificate.key -out certificate-without-password.key

Cert

    openssl pkcs12 -in certificate.pfx -clcerts -nokeys -out certificate.crt

## Convert PEM with key to PKCS12

    openssl pkcs12 -export -out certificate.pfx -inkey certificate.key -in certificate.crt -certfile CACert.crt

## Convert PEM with key to PKCS12 - MacOS version

    openssl pkcs12 -export -inkey certificate.key -in certificate.crt -name WebPrivateClientAuth -certfile CACert.crt -out certificate.pfx
    # openssl pkcs12 -noout -info -in certificate.pfx   ### INFO about pfx

## Check if cert and key fits

    openssl x509 -noout -modulus -in certificate.crt | openssl md5
    # 8a4282fa4e12f0a203b300cs33d0b91a
    openssl rsa -noout -modulus -in certificate.key | openssl md5
    # 8a4282fa4e12f0a203b300cs33d0b91a

## Check if cert and CA fits

    openssl verify -verbose -CAfile CACert.crt certificate.crt

## Add CA to Linux & Java KeyStore

    cp CACert.crt /usr/local/share/ca-certificates/
    update-ca-certificates
        ### Check if it's in system's java keystore
    for i in $(find /etc/ -name cacerts) ; do file $i ; done
        ### look for Java KeyStore, in Ubuntu it is /etc/ssl/certs/java/cacerts
    keytool -list -keystore /etc/ssl/certs/java/cacerts -storepass changeit |& head
    keytool -list -keystore /etc/ssl/certs/java/cacerts -storepass changeit |& grep ...

## Convert cert (no key) from P7B to PEM

    openssl pkcs7 -print_certs -in certificate.p7b -out certificate.cer

## Convert cert from DER to PEM

    openssl x509 -inform DER -in certificate.cer -out certificate.pem

## Check if CRL file is OK (openssl will read him) for DER format

    if openssl crl -inform DER -noout -in actual.crl &>/dev/null ; then echo CRL_OK ; else echo CRL_ERROR ; fi

## Convert CRL from PEM to DER

    openssl crl -in crl.pem -outform DER -out crl.der

## Check CRL (certificate revocation list)

    openssl crl -inform DER -text -in actual.crl
    openssl crl -inform PEM -text -in actual.crl

## Talk to SMTP server using TLS

        ### openssl s_client -starttls smtp -connect [HOST]:[PORT]
    openssl s_client -starttls smtp -connect smtp.gmail.com:587

## DEBUG SSL communication

    time (echo "Test" | openssl s_client -debug -connect HOST:PORT -servername HOST)

Performance test

    openssl s_time -connect HOST:PORT

## DEBUG SSL communication using Java

SSLPoke - install openjdk-19-jdk-headless

    git clone https://github.com/MichalHecko/SSLPoke.git
    cd SSLPoke && ./gradlew clean jar
    java -jar build/libs/SSLPoke-1.0.jar HOST PORT
    java -jar SSLPoke-1.0.jar HOST PORT
        ### or
    java -Djavax.net.ssl.trustStore=trustStore.keystore -Djavax.net.ssl.trustStorePassword=123456 -jar ./SSLPoke-1.0.jar HOST PORT

## SSL Cipher Suite Tester

    #!/bin/bash

    if [ -z "${1}" ] ; then echo "USAGE: $0 hostname:port" ; exit 1 ; fi

    ### OpenSSL requires port number.
    SERVER=${1}
    DELAY=.1

    ciphers=$(openssl ciphers 'ALL:eNULL' | sed -e 's/:/ /g')

    echo
    echo "**********************************************************************"
    echo "   Obtaining cipher list from $(openssl version)."
    echo "**********************************************************************"
    echo

    for cipher in ${ciphers[@]}
    do

        echo -n "Testing $cipher..."
        result=$(echo -n | openssl s_client -cipher "$cipher" -connect $SERVER 2>&1)

        if [[ "$result" =~ ":error:" ]]
        then
            error=$(echo -n $result | cut -d':' -f6)
            echo NO \($error\)
        else
            if [[ "$result" =~ "Cipher is ${cipher}" || "$result" =~ "Cipher    :" ]] ; then
                echo YES
            else
                echo UNKNOWN RESPONSE
                echo $result
            fi
        fi

        sleep $DELAY

    done

Usage

    ./ssl-tester.sh HOST:PORT

## Apache SSL Error - CA signature digest algorithm too weak

`[Thu Jun 03 ...] [ssl:info] [pid ...] [client ...] AH02276: Certificate Verification: Error (68): CA signature digest algorithm too weak [subject: CN=... / issuer: CN=... / serial: ... / notbefore: Dec  8 ... / notafter: Dec  8 ...]`

Lower security in `/etc/ssl/openssl.cnf` down to `CipherString = DEFAULT@SECLEVEL=1` from `=2`
