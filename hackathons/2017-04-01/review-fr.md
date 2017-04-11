% Compte-Rendu du "20th birthday hackathon"

Ce hackathon avait pour but de relancer le développement de sympa sur la base
d'une grande mise à jour. les fonctionnalités de sympa et son fonctionnement
restent les mêmes mais le coeur et l'interface graphique sont entièrement
revues pour refleter l'état de l'art en terme de développement, déploiement et
experience utilisateur.

# Hebergement des dépots

Nous avons créé
une [organisation sympa-community](https://github.com/sympa-community/)
sur [Github](https://github.com/). Toutefois, nous envisageons de retourner
sur [SourceSup](http://sourcesup.renater.fr/) (la forge de [RENATER](http://renater.fr/))
des que ce dernier proposera un service de qualité au moins équivalent par
rapport aux besoins du projet (runners macos pour l'intégration continue par
exemple).

# Prochaines versions de sympa

Les prochaines versions de sympa 6.2 seront maintenant maintenues par Soji
Ikeda avec l'aide de la communauté. Elles ne feront l'objet que de corrections.

Pour les versions suivantes, la séparation des fonctionnalités de sympa en
produits permettra probablement de produire des mises à jour plus rapidement.
Ces modules utiliseront la stratégie de numérotation et de gestion de
fonctionnalités de Perl. A savoir:

    major.minor.patchlevel

* Les versions majeures impaires (7.x, 9.x, ...) seront des versions avec des
cycles de vie court qui permettront de pouvoir bénéficier des dernières experimentations.

* Les versions majeures paires (8.x, 10.x) seront des versions plus
  conservatives et donc adaptées aux sites ayant de larges bases d'utilisateur.

les versions mineures correspondent aux ajouts fonctionnelles et le patchlevel
est réservé aux corrections.

Soji souhaite voir une nouvelle version majeure tous les ans. tout dépendra du
rythme de développement.

# Nouvelle terminologie

L'intégralité des services qui sont proposés par sympa autour des listes de
diffusion font qu'il serait réducteur d'en parler encore comme un simple
service de listes de diffusion. Les utilisateurs eux-même envisagent ces listes.

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

# intégration à YUNoHost

Grace à l'intégration de sympa dans YUNoHost, créer une instance de sympa pour
auto-heberger ses listes devrait se résumer à un simple click. Nous esperons
beaucoup de la combinaison de cette fonctionnalité avec la capacité qu'aura
sympa-vue à créer la listes de groupes.

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

* sympa devrait être facilement scriptable en Perl grace à une API clarifiée,
  testée et documentée. Cette API sera possiblement exposée par un [serveur
  REST](https://github.com/sympa-community/sympa-service-rest) dont les routes
  seront décrites par une
  [specification OpenAPI](https://github.com/sympa-community/sympa-spec-openapi).

# Pratiques de développement

## Perl 5.16 est requis pour les versions superieures à 6.2.x

perl 5.20 est  excellent cru. toutefois nous ne souhaitons pas mettre les
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

Des batteries de tests unitaires seront mises en place dans toutes les parties de sympa. L'intégration continue sera activée sur tous les projets et ne sera déversé dans master que du code n'ayant produit aucune erreur. Pour le reste, l'assurance qualité est une responsabilité collective et la revue de code est encouragée.

# Environements virtuels d'experiementation

Avoir des environements virtuels avec des sympa prets a l'emploi pourrait simplifier
le travail des administrateurs et des développeurs. Des experimentations ont été menés avec KVM, docker et lxc.



