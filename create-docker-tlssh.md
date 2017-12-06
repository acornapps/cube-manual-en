\#!/bin/bash

\# This script will help you setup Docker for TLS authentication.

\# Run it passing in the arguement for the FQDN of your docker server

\#

\# For example:

\#    ./create-docker-tls.sh myhost.docker.com

\#

\# The script will also create a profile.d \(if it exists\) entry

\# which configures your docker client to use TLS

\#

\# We will also overwrite /etc/sysconfig/docker \(again, if it exists\) to configure the daemon.

\# A backup will be created at /etc/sysconfig/docker.unixTimestamp

\#

\# MIT License applies to this script.  I don't accept any responsibility for

\# damage you may cause using it.

\#



set -e

STR=2048

if \[ "$\#" -gt 0 \]; then

  DOCKER\_HOST="$1"

else

  echo " =&gt; ERROR: You must specify the docker FQDN as the first arguement to this scripts! &lt;="

  exit 1

fi



if \[ "$USER" == "root" \]; then

  echo " =&gt; WARNING: You're running this script as root, therefore root will be configured to talk to docker"

  echo " =&gt; If you want to have other users query docker too, you'll need to symlink /root/.docker to /theuser/.docker"

fi



echo " =&gt; Using Hostname: $DOCKER\_HOST  You MUST connect to docker using this host!"



echo " =&gt; Ensuring config directory exists..."

mkdir -p "$HOME/.docker"

cd $HOME/.docker



echo " =&gt; Verifying ca.srl"

if \[ ! -f "ca.src" \]; then

  echo " =&gt; Creating ca.srl"

  echo 01 &gt; ca.srl

fi



echo " =&gt; Generating CA key"

openssl genrsa \

  -out ca-key.pem $STR



echo " =&gt; Generating CA certificate"

openssl req \

  -new \

  -key ca-key.pem \

  -x509 \

  -days 3650 \

  -nodes \

  -subj "/CN=$HOSTNAME" \

  -out ca.pem



echo " =&gt; Generating server key"

openssl genrsa \

  -out server-key.pem $STR



echo " =&gt; Generating server CSR"

openssl req \

  -subj "/CN=$DOCKER\_HOST" \

  -new \

  -key server-key.pem \

  -out server.csr



echo " =&gt; Signing server CSR with CA"

openssl x509 \

  -req \

  -days 3650 \

  -in server.csr \

  -CA ca.pem \

  -CAkey ca-key.pem \

  -out server-cert.pem



echo " =&gt; Generating client key"

openssl genrsa \

  -out key.pem $STR



echo " =&gt; Generating client CSR"

openssl req \

  -subj "/CN=docker.client" \

  -new \

  -key key.pem \

  -out client.csr



echo " =&gt; Creating extended key usage"

echo extendedKeyUsage = clientAuth &gt; extfile.cnf



echo " =&gt; Signing client CSR with CA"

openssl x509 \

  -req \

  -days 3650 \

  -in client.csr \

  -CA ca.pem \

  -CAkey ca-key.pem \

  -out cert.pem \

  -extfile extfile.cnf



if \[ -d "/etc/profile.d" \]; then

  echo " =&gt; Creating profile.d/docker"

  sudo sh -c "echo '\#!/bin/bash

export DOCKER\_CERT\_PATH=/home/$USER/.docker

export DOCKER\_HOST=tcp://$DOCKER\_HOST:2376

export DOCKER\_TLS\_VERIFY=1' &gt; /etc/profile.d/docker.sh"

  sudo chmod +x /etc/profile.d/docker.sh

  source /etc/profile.d/docker.sh

else

  echo " =&gt; WARNING: No /etc/profile.d directoy on your system."

  echo " =&gt;   You will need to set the following environment variables before running the docker client:"

  echo " =&gt;   DOCKER\_HOST=tcp://$DOCKER\_HOST:2376"

  echo " =&gt;   DOCKER\_TLS\_VERIFY=1"

fi



OPTIONS="--tlsverify --tlscacert=$HOME/.docker/ca.pem --tlscert=$HOME/.docker/server-cert.pem --tlskey=$HOME/.docker/server-key.pem -H=0.0.0.0:2376"

if \[ -f "/etc/sysconfig/docker" \]; then

  echo " =&gt; Configuring /etc/sysconfig/docker"

  BACKUP="/etc/sysconfig/docker.$\(date +"%s"\)"

  sudo mv /etc/sysconfig/docker $BACKUP

  sudo sh -c "echo '\# The following line was added by ./create-certs docker TLS configuration script

OPTIONS=\"$OPTIONS\"

\# A backup of the old file is at $BACKUP.' &gt;&gt; /etc/sysconfig/docker"

  echo " =&gt; Backup file location: $BACKUP"

else

  echo " =&gt; WARNING: No /etc/sysconfig/docker file found on your system."

  echo " =&gt;   You will need to configure your docker daemon with the following options:"

  echo " =&gt;   $OPTIONS"

fi



export DOCKER\_HOST=tcp://DOCKER\_HOST:2376

export DOCKER\_TLS\_VERIFY=1

echo " =&gt; Done! You just need to restart docker for the changes to take effect"



