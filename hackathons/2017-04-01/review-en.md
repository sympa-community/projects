% "20th birthday hackathon" review

The goal of this hackathon was to setup a big refresh of sympa itself and its
community. The way sympa works will not change in future versions but the engine
will be rewritten from scratch to take avantage to the state of art in terms of
developpement process, packaging, deployment and user experience.

# repository hosting

We created a [sympa-community organisation](https://github.com/sympa-community/) on
[Github](https://github.com/). However, going back to the [RENATER](http://renater.fr/)
solution ([SourceSup](http://sourcesup.renater.fr/)) is something to consider
as soon as they will provide all the services we need (including macos runners
for continuous integration).

# Next versions of sympa

6.2.17 will be release on june 25th by Soji Ikeda who's now the supervisor of
the stable sympa branch (6.2). The next versions of this branch will be only
for maintainance purpose.

For the other version to come, sympa will be split into dependant components
that can be released separately so they can be easier to release. The
versionning system will be inspired by perl:

    major.minor.patchlevel

where

* the "master" branch will have odd major versions (7.x, 9.x, ...). we will release
  versions of it as soon as a new feature works.

* the "stable" branch will have odd major versions (7.x, 9.x, ...). this would be
  basically a freeze of the master branch more stable features and longer support
  (Soji would like a new stable release once a year, we will see how fast the
  codebase is moving)

* minor versions are for new features

* patchlevel are for bugfixing only

# New terminology

Sympa does more than just exchanging mails, all the features around the mailing
lists are tools for workgroups and the users organize themselves this way in most cases
(the others are basically newsletter and contact adresses). so you will no
longer create a "mailing list" but a "group" which exchanges via a mailing
list.

Also, no one knows what a "robot" is (basically: this is all the groups for one
internet domain name). It's easier for users to think about communities so
robot will become community both in documentation and source code.

# sympa-vue: a new web UI for sympa

The idea behind this whole story was the start from scratch of a new web UI for sympa
to merge the post page, archives pages and shared documents together so we could have
a complete appealing web user experience of sympa (think about a group as a web forum)
when experimented mail users will prefer the customizability of their MUA to
communicate with a workgroup. It's all about choice.

Starting from scratch wasn't for nothing: we will embrase very last best practices in
term of frontend developpement using the [vue](https://vuejs.org/) framework to deliver
a responsive UI compliant to the material design recommendations. We're also experimenting
community aggregation so you will have one single UI to share with all your workgroups
if the user wants to.

You can contribute to the [code](https://github.com/sympa-community/sympa-vue/)
or to the [wireframe](https://github.com/sympa-community/sympa-design)
(using the proprietary software "sketch" on macos ... sorry about that but
"sketchers" will understand why) or take a look of the very last state of the developpement
in the online [preview](http://sympa-vue.surge.sh/).

# YUNoHost integration

An auto-hosted sympa community would be installed in a few clicks for YUNoHost
users. Exciting when you think about sympa-vue as a single app to manage all your groups, isn't it?

# Modularization

To make sympa easier to manage and understand, 

Pour clarifier et faciliter l'évolution de sympa, les idées suivantes ont été retenues

* we split sympa into multiple repositories so you can now package or install the list manager
  without the code and dependencies of the SOAP server or the web UI.

* when a database storage is relevant (everything but shared documents, spoolers and archives),
  data will be put in it as reference. The [DBIx::Class](https://metacpan.org/pod/DBIx::Class) ORM
  will be used to describe the schema.

* external datasources will still be used to synchronize external data in a customizable way.


  they will be separated modules available on CPAN.


* sympa offre déjà la possibilité de mettre les données du schéma à jour
  via des sources externes paramètrables. Ce fonctionnement sera généralisé
  (les fichiers de configuration, par exemple, deviennent des sources externes).
  Les fournisseurs de données seront disponibles sous la forme de modules CPAN.

* sympa devrait être facilement scriptable en Perl grace à une API clarifiée,
  testée et documentée. Cette API sera possiblement exposée par un [serveur
  REST](https://github.com/sympa-community/sympa-service-rest) dont les routes
  seront décrites par une
  [specification OpenAPI](https://github.com/sympa-community/sympa-spec-openapi).

# Pratiques de développement

## Perl 5.16 est requis pour les versions superieures à 6.2.x

perl 5.20 est excellent cru. Toutefois nous ne souhaitons pas mettre les
administrateurs de sites de sympa dans l'embarras. Au vue des versions présentes
dans la RHEL current et dans la prochaine LTS de debian, 5.16 est un compromis
acceptable pour tous.

Si vous êtes responsable d'un service informatique et que cette nouvelle vous
fait froid dans le dos, n'hésitez pas à nous contacter.


## disparition d'autoconf dans les bibliothèques perl

La règle (qui pourra trouver des exceptions si besoin) est désormais de faire
confiance aux administrateurs systèmes et mainteneurs de paquets pour indiquer
les chemins des bibliothèques perl (via `PERL5LIB` et `-I`). Cette pratique

* est répandue de longue date dans les communautés des langages dynamiques
  (cf [local::lib](https://metacpan.org/pod/local::lib) en perl, `virtualenv` en python, ...)
* simplifie le processus de développement
* rend l'installation de sympa plus souple

En pratique, l'utilisation de autoconf et de `Find::Bin` est donc à proscrire
dans les modules.

## renforcement des pratiques de developpement communes

Outre l'utilisation du framework [Moo](https://metacpan.org/pod/Moo)
pour la déclaration des classes d'objet, les strictures seront imposées
et l'utilisation de
[Function::Parameters](https://metacpan.org/pod/Function::Parameters) recommandée.

Afin d'éviter un alourdissement du "boilerplate", ces imports seront effectués
automatiquement par `Sympa.pm`.

    use Sympa;

serait donc l'équivalent de

    use feature  ':5.16';
    use strict   ();
    use warnings ();
    use Function::Parameters;

TODO: i don't know about

    use Sympa::Constants;

TODO: i think Sympa.pm is the good name but it could be a bad idea for
technical reasons ? how about Sympatic.pm ? Sympatic should be pushed as it on
cpan (not only for sympa purposes ?)

## meilleure participation à la vie du CPAN

Réutiliser CPAN au maximum, c'est s'éviter de la maintenance de code
(correction, optimisation, amélioration, ...) mais aussi de la documentation,
des tests, des ports, du packaging. Sympa doit reposer au maximum sur des
modules externes et les contributeurs de sympa sont invités à faire évoluer
leurs modules directement sur le CPAN.

## Assurance qualité

Des batteries de tests unitaires seront mises en place dans toutes les parties
de sympa. L'intégration continue sera activée sur tous les projets et ne sera
déversé dans master que du code n'ayant produit aucune erreur. Pour le reste,
l'assurance qualité est une responsabilité collective et la revue de code est
encouragée.

# Environements virtuels d'experiementation

Avoir des environements virtuels avec des sympa prets a l'emploi pourrait
simplifier le travail des administrateurs et des développeurs. Des
experimentations ont été menés avec KVM, docker et lxc.
