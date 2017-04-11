% Compte-Rendu du "20th birthday hackathon"

Ce hackathon avait pour but de relancer le développement de sympa sur la base d'une grande mise
à jour. les fonctionnalités de sympa et son fonctionnement restent les mêmes mais le coeur et l'interface graphique sont entièrement revues pour refleter l'état de l'art en terme de développement, déploiement et experience utilisateur.

# Hebergement des dépots

Nous avons créé
une [organisation sympa-community](https://github.com/sympa-community/)
sur [Github](https://github.com/). Toutefois, nous envisageons de retourner
sur [SourceSup](http://sourcesup.renater.fr/) (la forge de [RENATER](http://renater.fr/))
des que ce dernier proposera un service de qualité au moins équivalent par
rapport aux besoins du projet (runners macos pour l'intégration continue par
exemple).

# Nouvelle terminologie

L'intégralité des services qui sont proposés par sympa autour des listes de
diffusion font qu'il serait réducteur d'en parler encore comme un simple
service de listes de diffusion. Les utilisateurs eux-même envisagent ces listes
comme des "groupes de travail". De plus, personne ne comprend vraiment ce qu'est un robot
(l'ensemble des groupes associés à un domaine internet), nous préférons donc
parler de communautés (terme mieux compris par les utilisateurs).

# sympa-vue: Nouvelle interface web pour sympa

L'idée fondatrice de la relance du développement de sympa, c'est une nouvelle interface web
qui fusionne les fonctionnalités actuelles de "publications", "archives" et
"documents partagés" au sein d'une même page qui permettera de rendre les
listes de diffusions exploitables sous la forme d'un forum web.

Nous repartons de zero pour utiliser l'état de l'art du devéloppement d'interfaces web
en nous basant sur le framework [vue](https://vuejs.org/) et les
recommendations de google en terme de responsivness et de material design.

Au passage, nous en avons profité pour initier l'idée d'agrégation discutée de
longue date avec les utilisateurs de sympa: toutes les listes de tous les
serveurs seront disponibles via la même interface web (si l'utilisateur le
souhaite).

vous pouvez contribuer au [code](https://github.com/sympa-community/sympa-vue/)
ou à la [maquette](https://github.com/sympa-community/sympa-design)
(en utilisant le logiciel propriétaire sketch sous macos, malheureusement ...)
ou vous rendre compte de l'avancement du projet en visualisant le
[rendu en ligne](http://sympa-vue.surge.sh/).

# Modularisation

Pour clarifier et faciliter l'évolution de sympa, les idées suivantes ont été retenues

* les parties fonctionnellement indépendantes vivent dans des dépots séparés.
  Ainsi, il sera possible d'installer le gestionnaire de liste sans l'interface
  web ou le serveur soap. les dépots sont décrits dans la section suivante.

* les données métier relatives au gestionnaire de listes
  seront stockées dans la base de données
  qui fera référence. Le schema de la base de données sera décrit et exploité par l'ORM
  [DBIx::Class](https://metacpan.org/pod/DBIx::Class) et sera maintenu dans le dépot
  [sympa-schema](https://github.com/sympa-community/sympa-schema).

* sympa offre déjà la possibilité de mettre les données du schéma à jour
  via des sources externes paramètrables. Ce fonctionnement sera généralisé
  (les fichiers de configuration, par exemple, deviennent des sources externes).
  Les fournisseurs de données seront disponibles sous la forme de modules CPAN.

# Pratiques de développement

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

## Utilisation de [Moo](https://metacpan.org/pod/Moo) pour la déclaration des classes

## Renforcement du coding style



`Sympa.pm` will import some strictness and helpers into your code so

    use Sympa;


# intégration à YUNoHost

Grace à l'intégration de sympa dans YUNoHost, créer une instance de sympa pour
auto-heberger ses listes devrait se résumer à un simple click. Nous esperons beaucoup de la combinaison de cette fonctionnalité avec la capacité qu'aura sympa-vue à créer la listes de groupes d

l'intégration de sympa-vue.




`Sympa.pm` will import some strictness and helpers into your code so


    use feature  ':5.16';
    use strict   ();
    use warnings ();
    use Function::Parameters;
    use Sympa::Constants;



  Le schéma de la base sera maintenu avec un ORM
  (DBIx::Class en l'occurence)



toutes les informations relatives au mlm seront stockées en base de données
  maintenue




## modularisation

La distribution de sympa se fait sous la forme d'une seule archive proposant
le serveur de listes lui-même et divers composants comme une interface web,
un serveur soap, des outils d'administration.

Ces outils ne sont pas utilisés de manière homogène par la communauté et
la maintenance de la totalité de la collection logicielle nécessite 


manque de granularité est un frein à l'évolution, l'adoption, l'intégration
dans les systèmes d'information et finalement l'installation.

Un frein à l'évolution parceque les usages de sympa sont très différents et
et qu'une modification dans le moteur de sympa entraine possiblement des
modifications dans les composants. Notre idée est de maintenir

* un schema (système de stockage des informations dans un gestionnaire de bases de données)
  utilisant l'[ORM](https://en.wikipedia.org/wiki/Object-relational_mapping)
  [DBIx::Class](https://metacpan.org/pod/DBIx::Class) et des sources externes permettant
  d'y synchroniser de manière configurable des informations.

  de synchroniser des informations
  * qui contiendrait désormais l'intégralité des informations nécessaires à sympa
    serait défini et exploité via

  métier de sympa et sur lequel
  pouront se greffer des sources externes


* des connecteurs pour les sources de données externes comme par exemple
  * les fichiers plats de configuration, scenarii, ...
  * les sources d'abonnés


* un gestionnaire de listes de diffusion (envoi et réception et traitement
  des messages relatifs au publipostage et aux listes de diffusion)



Nous souhaitons découper sympa en plusieur composants logiciels qui interagiront entre eux

### schema

e


### sympa-mlm (mailing list manager)

intégrera les fonctions de diffusion de messages, de publipostage, de scenarii 


intègrera les






Pour les architectes des systèmes d'information, c'est la possibilité d'identifier




Si nous conservons cette unique archive comme moyen de distribution, nous
seront obligé d'attendre que tous ces composants soient prêts





### pour les administrateurs de site

Sympa repose sur de nombreux modules et son installation n'est pas aisée. Une par

Sympa est compliqué à installer et une grande partie de l'effort fourni le sera
pour



est le simple fruit de l'histoire de sympa qui a grandit
organiquement dans un seul dépot 


* il complique le travail des packq


## simplification de l'installation

Pour le moment, sympa est un logiciel monolithique fou

## économie autour de sympa
##

# La base de code et le boilerplate





