# Sympa ideas

Sympa is much more than a mailing list manager: it comes with a very rich web
interface allowing non-technical people to create and customize working groups
of people and allow them to communicate, share documents, get complete archives,
create homepages, wikis and much more.

Sympa is also a very fast and robust system to send loads of mails with the state
of art of SMTP standards (DMARC, DKIM, ...).

However, the project is quite old now (22 years) and need some features to be
refactored.

According to my understanding of the sympa code and the Perl ecosystem, a
lot of homebrew code should be either

* replaced by external tools
* published as external tools

(mostly CPAN distributions)

This page is my personnal list of main goals to make Sympa modern again.

## A responsive HTML5 UI for sympa

### short description

Some very powerful features of sympa are hidden by the web interface wich is
unattractive and uneasy to use. We need to change this.

### long description

* sketch a new interface that should be more dynamic and easier to use.
  * It MUST be
    * at least isofunctional
    * a11y compliant
    * responsive
  * It MUST implement
    * merge the archives, send/resend, shared documents in a single page
      so users can contribute to their workgroups without using the mails
  * It COULD implement
    * simplified admin pages
    * complete drag'n'drop of shared documents and folders
    * a visual editor for simple publishing scenarii
      (instead of the homebrew DSL)

* implement it while using a modern web stack
  ([elm](http://elm-lang.org/) or [vue.js](https://vuejs.org/) ?)

### expected outcome

Modernize the UI is crucial to make sympa attractive for newcommers
and experimented users.

### required skills

UX, design principles, a11y and modern web stack technologies (html5, js, CSS)

### possible mentor

* Marc Chantreux

## Replace the homebrew HTTP handler with Plack

### short description

Use Plack instead of any homebrew code, delete the old code.

### long description

* identify all the code that can be replaced by a plack counterpart
* replace this code by the plack counterpart
* if relevant, introduce an higher level web framework (dancer2?)

### expected outcome

* remove the need of maintenance and documentation
* make all the plack ecosystem (modules, runners, ...) available for sympa
* make sympa easier to deploy, configure and maintain for a perl experimented administrator

### required skills

Perl (Plack and Moo), good understanding of HTTP, FCGI, CGI

### possible mentor

* Marc Chantreux

## Evaluation of the job queuing part

### short description

Does sympa job queuing compete (in terms of speed and functionnalities)
with the CPAN counterparts?
* if so  : publish them on CPAN
* if not : replace them by a CPAN counterpart

### expected outcome

* remove the need of maintenance and documentation
* make all the plack ecosystem (modules, runners, ...) available for sympa
* make sympa easier to deploy, configure and maintain for a perl experimented administrator

### required skills

Perl, Job queueing, documentation and packaging

### possible mentor

* Marc Chantreux

## Make the scenarii run everywhere and better

Scenaries are one of the key features that makes sympa a much superior mailing list manager,
it gives non-technical people a way to define exactly what to do when a message is
received, a document is shared and so on.

But...

* there are a lot of actions that can't take avantage of them
* they are not exendable in Perl
* they can be hard to test
* they cannot be chained

Refactor the whole Action/Scenarii mechanism to give them the power they deserve
(maybe inspired by the Request Tracker Scrip system).

### expected outcome

* give more power to experimented non-technical users
* make sympa even easier to tweak

### required skills

Perl, software design, documentation and packaging

### possible mentor

* Marc Chantreux

## Processing of bounces

Processing of bounces isn't only interesting for Sympa but also for all kind of web applications
sending out emails to users respective customers.

We can try to factor that part out of Sympa into a separate module and/or take a look at
[Sisimai](http://libsisimai.org/en/).

### suggested by

* Stefan Hornburg (Racke)
