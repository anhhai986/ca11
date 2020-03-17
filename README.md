[![Commitizen friendly](https://img.shields.io/badge/commitizen-friendly-brightgreen.svg)](http://commitizen.github.io/cz-cli/)

# About CA11

CA11 is **open** & **free** communication software; a fully open-source WebRTC-based
communication stack without paid services or any business model. A CA11 instance
is running [here](https://ca11.app/) as a public service.

CA11 uses a highly optimized JavaScript webphone stack, applies existing
PBX technology (Asterisk) for optimal reach and comes with an additional
E2E encrypted signalling service for accountless, federated WebRTC P2P
communication.

# Install

## Requirements

* Chrom(e/ium) browser
* Node.js 13+ (Native ESM)
* Docker (or install manually using Docker as reference)

## Procedure

* Clone the project & install dependencies:

      git clone git@github.com:garage11/ca11.git
      cd ca11
      yarn
      cp .ca11rc.example .ca11rc
      yarn bootstrap
      ./cli.js watch

* Generate a developer certificate & CA to use SSL without warnings on a local domain:

      cd ca11/docker/nginx/ssl
      ./ca_cert.sh dev.ca11.app
      ./ca_cert.sh sip.dev.ca11.app
      ./ca_cert.sh sig11.dev.ca11.app

* (!) Manually import the development CA

      sudo ./ca_system.sh

  > This shell-script for importing the CA into the system only works on Archlinux at the moment.

* Add local hostname lookup for the default domains:

      sudo echo "127.0.0.1 dev.ca11.app" >> /etc/hosts
      sudo echo "127.0.0.1 sip.dev.ca11.app" >> /etc/hosts
      sudo echo "127.0.0.1 sig11.dev.ca11.app" >> /etc/hosts

* Setup Asterisk database

      cd ca11/docker
      docker-compose up
      docker exec -w /root/asterisk/contrib/ast-db-manage -it asterisk alembic -c config.ini upgrade head
      psql -U asterisk -h 127.0.0.1 asterisk < postgres/sig11_asterisk.sql  # default pw is 'ca11ftw'

* Start the signalling service

      node packages/sig11/service.js

* Restart the browser for the SSL certificate to be picked up
* Open https://dev.ca11.app to start your webphone

Call one of the SIP testnumbers in contacts to verify that the SIP stack works.
