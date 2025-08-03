# Linux Home CA (Certification Authority)

No need to install any software. I am using openssl only with small helper script.

## Create CA

    mkdir -p ca/ca-cert
    cd ca/ca-cert

    ### Create CA key:
    openssl genrsa -out ca.key 4096

    ### Create CA cert:
    export SUBJ="/C=CZ/ST=Some State/L=City/O=HomeCA/CN=MyCN/emailAddress=myEmail"
    openssl req -x509 -new -nodes -key ca.key -sha256 -days 14600 -out ca.crt -subj "$SUBJ"

    cd ../


## Create helper script

Save this script as `createCertificate.sh`

    #!/bin/bash

    CERT="${1}"
    TYPE="${2}"
    DAYS="14590"
    CA_KEY="ca-cert/ca.key"
    CA_CRT="ca-cert/ca.crt"
    CERTS="certs"

    if [ -z "${CERT}" ] || [ -z "${TYPE}" ]
    then
        echo "USAGE
            ### Create file with subject for certificate (vpn-custom-routes)
            $0 <file with SUBJECT line for new certificate> <c|s> (client|server)
    "
        echo "EXAMPLE: $0 vpn-server s"
        echo "EXAMPLE: $0 vpn-custom-routes c"
        echo "EXAMPLE: $0 www-some-client-cert c"
        exit 1
    fi

    if [ ! -f ${CERT} ]
    then
        echo "ERROR: File ${CERT} does NOT exists!"
        exit 1
    fi

    mkdir ${CERTS} &> /dev/null
    test -d ${CERTS} || { echo "Folder ${CERTS} does NOT exists! Exitting ..."; exit 1; }

    if [ -f ${CERTS}/${CERT}.key ]
    then
        echo "ERROR: Key for ${CERT} already exists!"
        exit 1
    fi

    SUBJ=$(cat ${CERT})

    echo "Subject: $SUBJ"
    echo "Generating key ..."
    openssl genrsa -out ${CERTS}/${CERT}.key 4096
    echo "Generating csr ..."
    openssl req -new -key ${CERTS}/${CERT}.key -out ${CERTS}/${CERT}.csr -subj "$SUBJ"
    echo "Generating (${TYPE}) certificate ..."

    if [ $TYPE == "c" ]
    then
        openssl x509 -req -in ${CERTS}/${CERT}.csr -CA ${CA_CRT} -CAkey ${CA_KEY} -CAcreateserial -out ${CERTS}/${CERT}.crt -days ${DAYS} -sha256 -extfile <(cat <<EOF
    basicConstraints=CA:FALSE
    keyUsage=digitalSignature
    extendedKeyUsage=clientAuth
    EOF
    )
    fi

    if [ $TYPE == "s" ]
    then
        openssl x509 -req -in ${CERTS}/${CERT}.csr -CA ${CA_CRT} -CAkey ${CA_KEY} -CAcreateserial -out ${CERTS}/${CERT}.crt -days ${DAYS} -sha256 -extfile <(cat <<EOF
    basicConstraints=CA:FALSE
    keyUsage=digitalSignature,keyEncipherment
    extendedKeyUsage=serverAuth
    EOF
    )
    fi

    openssl x509 -in ${CERTS}/${CERT}.crt -text | grep After
    ls -l ${CERTS}/${CERT}*

## Create subject files for certificates

    echo "/C=CZ/ST=Some State/L=City/O=HomeCA/CN=MyVPNHomeServer/emailAddress=myEmail" > vpn-server
    echo "/C=CZ/ST=Some State/L=City/O=HomeCA/CN=MyClientCertificate/emailAddress=myEmail" > www-my-selfsigned-client-cert

## Create example server certificate

    ./createCertificate.sh vpn-server s

## Create example client certificate

    ./createCertificate.sh www-my-selfsigned-client-cert c

