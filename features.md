`Note: this a work in progress`

# Sympa Functionalities

## Authentication

### native
email/password
password stored as MD5 hash

### LDAP

### SAML

### CAS

## Authorization
Scenarios
### actions

- access\_web\_archive
- add
- automatic\_list\_creation
- create\_list
- del
- d\_edit
- d\_read
- info
- invite
- remind
- global\_remind
- review
- send
- subscribe
- topics\_visibility
- tracking
- unsubscribe
- visibility

## Configuration

### sympa.conf
### edit\_list.conf
### auth.conf
### trusted\_applications.conf

## Customization

## Data sources

### types

#### file

#### remote file

#### SQL

- MySQL
- PostgreSQL
- Oracle
- SQLite

#### LDAP

- one level
- two levels

#### VOOT

### synchronization

- regularly
- on demand

TODO: freeze subscribers list

## Hierarchisation

### Configuration-wise

server level
domain
family
list

### users-wise

- listmaster
- vhost listmaster
- owner (privileged or normal)
- moderator
- subscriber

### industrialisation

#### list families

#### automatic lists

#### user friendly automatic lists

## Interfaces

### mail

help

#### subscription management

- subscribe
- signoff
- which
- reception preferences
- nomail
- digest
- summary
- txt
- html
- urlize
- not\_me
- mail

#### subscribers management

- add
- delete
- review
- invite
- list of lists

#### lists management

- info
- stats

#### archives

- get
- index
- last

### CLI

#### family management

- add list
- instantiate family
- close unknown
- close family
- modify list
- option: no STDOUT

#### lists management

- create list
- close list
- purge list
- rename list

#### subscribers management

- change email address
- import subscribers
- dump subscribers
- lowercase email addresses
- trigger inclusion synchronisation
- recreate aliases file
- recreate binary config cache
- upgrade Sympa version
- run in debug mode
- specify lang to be used while running
- log mail commands
- output Sympa version

### web

### REST

### SOAP

## mail

### bulk mailing

### RFC

#### DKIM

#### DMARC

#### S/MIME

- Signing
- Crypting

## message processing

### authorization

#### reject
#### moderation
#### authentication
#### direct transfer

### message modification

#### header
#### footer
#### merge
#### subject customization
#### Re: munging

## task manager

### bounce expiration

### digest sending

### bounce management

### subscription reminder

### user table purge

### database logs purge

### web sessions purge

## bounce management

### DSN analysis

- temporary errors
- permanent errors

### multiple MTA support

DSN parsing

### bounce note evaluation

### actions on bouncers
- warning
- unsubscription

## shared documents

web only

### access rights by scenario

- read
- delete
- write

## upgrade

- automatic database upgrade
- automatic files conversion
