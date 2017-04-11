% Compte-Rendu du "20th birthday hackathon"

les décisions suivantes ont été prises avant et pendant le hackathon.

# Hebergement des dépots

Nous avons créé
une [organisation sympa-community](https://github.com/sympa-community/)
sur [Github](https://github.com/). Toutefois, nous envisageons de retourner
sur [SourceSup](http://sourcesup.renater.fr/) (la forge de [RENATER](http://renater.fr/))
des que ce dernier proposera un service de qualité au moins équivalent par
rapport aux besoins du projet (runners macos pour l'intégration continue par
exemple).

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

# Dépots créés




# Coding style

le préprocessing des fichiers perl pour inserer en dur les répertoires ou les
modules sont stockés est abandonné: c'est l'administrateur du système (ou le
mainteneur du package) qui s'assure désormais que les modules de sympa sont
présents dans @INC.

* les fichiers de configuration deviennent une source externes qui pourront
  ne pas être installé

  Des sources de données externes pourront être utilisées pour synchroniser des
  données 




* les fichiers de configuration seront gérés comme source de données




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





