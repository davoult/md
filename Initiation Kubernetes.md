# Initiation à Kubernetes

    Auteur: Sébastien DAVOULT
    Courriel: d@vou.lt
    Licence: CC-BY-4.0
    Version: 1.0

<!--- Forked from  --->

<a rel="license" href="http://creativecommons.org/licenses/by/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by/4.0/88x31.png" /></a><br />Ce support de formation est distribué sous la license <a rel="license" href="http://creativecommons.org/licenses/by/4.0/">Creative Commons Attribution 4.0 International License</a>. 

## Qu’est-ce que Kubernetes ? 

### Introduction

Kubernetes est une solution Open source permettant l'orchestration de ressources conteneurisées en environnement de production. Cette solution initialement développée par *Google* a été offerte à *Linux Foundation* en 2014 et a permis la création de la *Cloud Native Computing Foundation (**CNCF**)* en 2015.

Description de Kubernetes par la *CNCF* :

> Kubernetes est une plateforme open source extensible et portable pour la 
> gestion de charges de travail (workloads) et de services 
> conteneurisés.

### Y a t'il des alternatives à Kubernetes ?

En 2014, la bataille était rude, nous retrouvions plusieurs orchestrateurs comme **Mesos**, **Docker Swarm** et bien d'autres...

Mais c'est là où Google a su jouer un coup de maitre, c'est en offrant son produit à la communauté. Ce don à permis de fédérer une communauté très importante d'utilisateurs et de contributeurs autour du produit et d'en faire aujourd'hui **la solution d'orchestration de conteneurs**. 
Bien que Kubernetes soit devenu le **standard**, d'autres alternatives existent en 2021, nous retrouvons notamment:

- **Titus**, solution utilisé par Netflix [Qu'est ce que Titus ?](https://netflix.github.io/titus/)
- **Nomad**, solution en vogue propulsée par Hashicorp [Qu'est ce que Nomad ?](https://www.nomadproject.io/)
- **Apache MESOS**, solution propulsé par Apache [Qu'est ce que MESOS](https://mesos.apache.org/)

Mais qu'en est-il de **Docker Swarm** ? Est-ce une solution toujours viable aujourd'hui ?

L'orchestrateur de conteneurs propulsé par *Docker inc.* à l'avantage d'être extrêmement simple ! Quelques étapes suffisent à avoir un environnement pleinement fonctionnel:

1. Provisionner 3 noeuds (1 Master et 2 Workers)
2. Installer Docker engine sur les environnements
3. Créer un *service* définissant le prérequis de votre application
4. Et le tour est joué !

Mais bien que très facile d'utilisation, *Docker Swarm* manque de souplesse. À l'instar de Kubernetes, Swarm n'est pas *extensible*, nous avons certes quelques greffons de stockage, mais cela s'arrête là.

### Fonctionnement

#### Architecture

![Architecture simplifié](images/Architecture%20Kubernetes.png)

#### Quelques notions de vocabulaire

*Kubernetes* peut sembler difficile à aborder à cause principalement du nombre important de nouveaux termes que la solution apporte. Une fois ces nouveaux mots appréhendés, la technologie devient bien plus abordable.

| Terme                     | Signification                                          |
|:------------------------- |:------------------------------------------------------ |
| container                 | Ressource exécutant une application                    |
| pod                       | Groupement d'un ou plusieurs conteneurs                |
| deployment                | Orchestre les *pods* et *replicaSets*                  |
| deamonset                 | S'assure d'avoir un pod sur chaque noeud               |
| statefullset              | [legacy] S'assure de n'avoir qu'un seul pod            |
| service                   | Expose votre application sur le réseau du cluster      |
| ingress                   | Permet d'exposer un service en externe (#ReverseProxy) |
| persistent volumes        | Défini un accès au stockage                            |
| persistent volumes claims | Demande d'allocation de stockage                       |
| configMaps                | stockage clé-valeur non confidentiel                   |
| secrets                   | stockage clé-valeur confidentiel                       |

![Vocabulaire Kubernetes](images/Kubernetes%20Objects.png)

## Qu’est-ce qu’une application Cloud Native et quels sont les enjeux pour les OPS?

> Pour préserver leur compétitivité sur des marchés basés sur les logiciels qui évoluent rapidement, les entreprises doivent repenser la manière dont elles conçoivent, développent et utilisent leurs applications. L'approche de développement d'applications cloud-native s'appuie sur des techniques reconnues et sur des technologies de cloud computing. Elle facilite le développement, l'exécution et l'amélioration des applications. 
> 
> -- <cite>Red Hat</cite>

Une application *Cloud Native* doit être décomposée en plusieurs *services*, indépendants, faiblement couplés et idéalement *sans état*. Cela permet un gain fort en agilité et ainsi de pouvoir mettre à l'échelle et d'assurer une meilleure tolérance à la panne.

Une application *Cloud Native* est **spécialement** conçue pour être exécutée dans environnement de développement et de gestion automatisé qu'il soit un *Cloud Privé*, *public* ou *hybride*. Le cycle de vie d'une application *Cloud Native* est plus dynamique et éphémère qu'une application traditionnelle.

> Accélérer les étapes entre l'idée et la publication d'une nouvelle 
> application, c'est un des enjeux du **DevOps** et pour y arriver, 
> les applications devraient être **Cloud Native**.

### Les 12 facteurs du Cloud Native

Les équipes de chez [Heroku](https://blog.heroku.com/twelve-factor-apps) ont proposé, en 2012, une méthodologie de qualification pour les applications *Cloud Native* nommées: **12-Factor app**. Cette méthodologie de qualification est aujourd'hui utilisée comme référentiel. Elle propose, en 12 points clés de définir les directives et voies à suivre pour concevoir une application *Cloud Native*.

1. Codebase
2. Managing Dependencies
3. Application Configuration
4. Backing services
5. CI/CD Build, Release, Run
6. Running processes
7. Port Binding
8. Scaling with processes
9. Disposability
10. Dev/prod parity
11. Use your logs
12. Admin process

#### Codebase

Les sources de votre application devront être hébergées sur un gestionnaire de révision tels que Git, Mercurial, Subversion, Bazard.

#### Managing Dependencies

Une application *Cloud Native* doit définir de façon explicite ses dépendances.

#### Application Configuration

Les configurations applicatives doivent être séparées de l'application.

#### Backing services

Une application *Cloud Native* est décomposée en plusieurs services interconnectés.

#### CI/CD Build, Release, Run

Une application *Cloud Native* doit s'inscrire dans un chainon de construction, de publication et d'exécution automatisée.

#### Running processes

L'application doit composer un plusieurs de ses composants **sans état**. Pour pouvoir mettre à l'échelle facilement une application et ainsi passer d’une instance à 300 en 5 secondes, nous ne devons pas conserver de contenu localement sur une instance non prévu à cet effet.

#### Port binding

L'application doit pouvoir être exposée facilement et est mise à l'échelle. Ainsi il est nécessaire de pouvoir exposer son service sur des ports de communications éphémères non réservés par le système.

#### Concurrency (Scaling with processes)

Diviser pour mieux mettre à l'échelle. Vos applications doivent être décomposées pour permettre une mise à l'échelle différente en fonction des composants les plus utilisés.

#### Disposability

Le temps de démarrage de votre application est crucial pour pouvoir répondre aux variabilités de charge.

#### Dev/Prod parity

Vos environnements de Dev et de Prod doivent être similaires.

#### Use your Logs

Vos journaux applicatifs doivent être traités comme un flux et envoyé vers une sortie standardisée, généralement `/dev/stdout`.

#### Admin process

Des taches d'administration peuvent être nécessaire dans vos applications, mais ce n'est pas une raison pour exécuter des actions en interactif dans vos conteneurs.

Sources:

- [12factor.net](https://12factor.net/)

### Les nouvelles prérogatives pour les OPS

Le métier des *SysAdmins* évolue, les modes de livraisons changent, les interactions avec les équipes métiers ou avec les clients se renforcent. 
Pour répondre à ces nouveaux besoins, les OPS doivent comprendre le besoin des Devs et mettre à disposition des plateformes et mécanismes permettant l'enrichissement de la chaine de valeur de l'entreprise et ainsi accélérer le *Go To Market*.

Un *Ops* pourra très bien recevoir une adresse d'un registre d'images Docker de la part d'un éditeur de logiciel et devra définir le meilleur moyen pour y intégrer ce nouveau mode de livraison en production au sein de son entreprise.

> "*Comment garantir une étanchéité entre mes environnements conteneurisés 
> ?*"
> 
> "*Comment permettre à mes utilisateurs d'être autonomes sans pour autant 
> les restreindre dans leur récupération de ressources publiques ?*"
> 
> "*Comment faire cohabiter mon patrimoine applicatif avec mes nouvelles 
> applications Cloud Native ?*"

Sont toutes des questions qu'un *Ingénieur système* doit pouvoir traiter aujourd'hui.

## La conteneurisation prendra-t-elle le dessus sur la virtualisation « lourde » ?

La conteneurisation est un nouveau changement de paradigme dans le monde de l'exécution de ressources informatique. Nous avons vécu un changement similaire en 2000 lorsque la virtualisation des serveurs a été démocratisée.

La conteneurisation n'est pas un effet de mode, c'est la concrétisation d'années de constats sur l'exécution de ressources virtualisés.

Rappelez-vous comment vous faisiez pour développer une petite application Web...

> *"Je commence par me déployer une petite machine virtuelle, j'installe mon 
> système d'exploitation, next, next, next, et voilà déjà 1 heure d'écoulée.... Je 
> commence enfin à installer mon LAMP, je configure ma base MariaDB, mes 
> autorisations... Enfin j'arrive sur la configuration de mon Apache, il faut se 
> créer un VirtualHost.... Et booom !! Voilà encore 30 minutes de 
> perdues..."*

Il était donc *urgent* d'industrialiser les phases de développement en réduisant tous ces postes de dépenses de temps.

## [Labs] Déploiement d’une infrastructure Kubernetes

### Environnement

**Système d'exploitation:** Debian

**Processeur:** 2vCPU

**Mémoire:** 2 Go

**Stockage:** 20Go

**Instances:** 3 instances Kubernetes + 1 instance NFS Server

## [Labs] Création manuelle de ressources

## En savoir davantage

[[CNCF] Qu'est ce que Kubernetes](https://kubernetes.io/fr/docs/concepts/overview/what-is-kubernetes/)
