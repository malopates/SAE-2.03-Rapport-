Malori ALVAREZ, Ulysse PETILLON, Louis HUARD  

# Rapport SAE 2.03

##  1^ère^-semaine 

### ![Logo PC](https://cdn-icons-png.flaticon.com/512/73/73575.png =50x50) Création de la 1^ère^ VM

Pour la première séance, nous avons commencé en créant une nouvelle VM dans **Oracle VM VirtualBox** que nous avons appelée "*SAE203auto*", sans aucun fichier iso, mais en sélectionnant le type (**Linux**) et la version (**Debian 12 Bookworm** en version 64-bit). Pour ce qui est de l'iso, il faut se rendre dans l'onglet `Storage`, puis dans "`Controller: IDE`" -> "`Empty`" pour rajouter dans "`IDE Primary Device 0`" le fichier "**S203-Debian12.viso**" afin que les fichiers de configuration automatique se lancent convenablement.

Le reste de la configuration de la VM s'est faite en laissant les paramètres par défaut. Cependant, l'étape de chargement du pre-seed était bloqué, suite aux recherches de Louis, nous avons pu résoudre ce problème en redémarrant la machine à cette étape. 

![Screen création VM](https://lh7-us.googleusercontent.com/HwGyxgCVJ4FucrncIYnrRtDJVPEdrYU__IEii__UMg29YSwengtuclEehw575ZxLd2NAXWbh1VpI0xNDq_o1kRc3V7HD2PmTYJKxIz14KGTN7T8BkUGBZ_M6d1aJU1k5DbnO3vo9U3_ELN5BtngKaZY)
Avant de lancer la VM, nous avons modifié les fichiers
"`vboxpostinstall.sh`" et "`preseed-fr.cfg`" pour paramétrer notre VM,
ainsi que l'uuid dans le fichier viso.

Dans la **preseed**, on a modifié la ligne 83 afin d'avoir
l'installation de mate :

`tasksel tasksel/first multiselect standard ssh-server, mate-desktop`\

**![](https://lh7-us.googleusercontent.com/NXvSVVd-LuTqc8ObAwmz6XIFKAmHAJZ_OK8OafUy8bNNJKmjDhpUu9oPERd58B_ClqZOfV2UAREMnBZijqGrVuKCphyTQjMiojFAJzsT8ltba8KWeEGLdtXVldk-NC7NrDMONRHsnqa2n-hrjT5Un5s)**
Dans le **vboxpostinstall**, nous avons ajouté dans la partie "Run user
command" les commandes pour installer les fonctionnalités attendues :\
**![](https://lh7-us.googleusercontent.com/UNWV9n-fmQzs8ckQ6X1Jv6B-sqRiQcOuRqqGqFoyHddPlooD2WQCCO5Nn3KV80svOt0gnpqwU0Pc20U3ihF7d2KU3x-EEJvqRnc7PBaEbutOic7IBDlwmiBTQYdecS9YvDb325QQ2BqxfhV5BRyTX68)**\
`log_command_in_target usermod -a -G sudo user`

`log_command_in_target apt-get install sudo`

`log_command_in_target sudo apt-get install git`

`log_command_in_target sudo apt-get install sqlite3`

`log_command_in_target sudo apt-get install curl`

`log_command_in_target sudo apt-get install bash-completion`

`log_command_in_target sudo apt-get install neofetch`

Nous avons rencontré quelques soucis à l'installation à cause de ces commandes, mais en commentant les 5 dernières lignes *( sudo apt-get...)* l'ensemble refonctionnait de nouveau, mais sans les installations.
Nous avons plus tard réalisé que le problème venait du fait que la commande `sudo` attend une entrée utilisateur, ce qui bloquait complètement l'exécution du script. De plus, ce script s'exécute déjà en tant que root, alors il nous a suffit de retirer `sudo` de toutes les commandes pour que le script
fonctionne.

### ![Question](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Question_mark_alternate.svg/788px-Question_mark_alternate.svg.png =20x30)   Configuration matérielle dans VirtualBox 

Que signifie "64-bit" dans "Debian 64-bit" ?

:   -   C'est la largeur des registres soit la façon dont le processeur gère les informations qu'il doit effectuer, ce qui signifie que Debian peut faire tourner le processeur en 64 bit, et en 32-bit, ce qui n'est pas réciproque (le 32-bit ne fait pas tourner du 64).

Quelle est la configuration réseau utilisée par défaut ?

:   -   La configuration réseau utilisée par défaut est la configuration
        en mode *NAT*.


Quel est le nom du fichier XML contenant la configuration de votre machine ?

:   -   Le nom du fichier XML contenant la configuration de votre
        machine est le fichier `debian.xml`

Comment vous modifieriez ce fichier de configuration pour mettre 2 processeurs à votre machine ?

:   -   Pour mettre 2 processeurs à votre machine, je modifierais le
        fichier de configuration en ajoutant la ligne suivante :
        `<vcpu placement='static'>2</vcpu>.`

### ![drawing](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Question_mark_alternate.svg/788px-Question_mark_alternate.svg.png =20x30) Installation OS de base 

Qu'est-ce qu'un fichier iso bootable ?

:   -   Un fichier iso bootable est un fichier qui s'exécute au
        démarrage de la VM et qui contient une instance d'un système
        d'exploitation. Il permet de créer la machine.

Qu'est-ce que MATE ? GNOME ?

:   -   *Mate* et *Gnome* sont des environnements graphiques de debian

Qu'est-ce qu'un serveur web ?

:   -   Un serveur web est un logiciel qui permet de stocker et de
        diffuser des pages web.

Qu'est-ce qu'un serveur ssh ?

:   -   SSH est un protocole de communication sécurisé qui permet
        d'accéder à distance à un serveur.


Qu'est-ce qu'un serveur mandataire ?

:   -   Un serveur mandataire est un serveur qui permet de filtrer les
        requêtes des clients.


![question](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Question_mark_alternate.svg/788px-Question_mark_alternate.svg.png =20x30)sudo 

Comment peux-ton savoir à quels groupes appartient l'utilisateur user ?

:   -   On utilise la commande : \$ groups user

### ![question](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Question_mark_alternate.svg/788px-Question_mark_alternate.svg.png =20x30) Suppléments invités 

Quelle est la version du noyau Linux utilisé par votre VM ? N'oubliez pas, comme pour toutes les questions, de justifier votre réponse.

:   -   On l'obtient avec la commande uname -r, uname donne des
        informations sur le système qu'on utilise. Ici il s'agit de
        amd64.

À quoi servent les suppléments invités ? Donner 2 principales raisons de les installer.

:   -   Les suppléments invités servent à :\
        -\> améliorer les performances de la machine virtuelle\
        -\> faciliter le partage de fichiers entre la machine virtuelle
        et l'hôte.

À quoi sert la commande mount (dans notre cas de figure et dans le cas général) ?

:   -   Elle permet de monter une partition sur le disque dans le cas
        général.
        

### ![question](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Question_mark_alternate.svg/788px-Question_mark_alternate.svg.png =20x30)  Quelques questions 

Il existe 3 durées de prise en charge (support) de ces versions : la durée minimale la durée en support long terme (LTS) et la durée en support long terme étendue (ELTS). Quelles sont les durées de ces prises en charge ?

:   -   La durée minimale, de support long terme (LTS) et celle en
        support long terme étendue (ELTS) sont de 5 ans

Pendant combien de temps les mises à jour de sécurité seront-elles fournies ?

:   -   Les mises à jour de sécurité seront fournies pendant 5 ans.

Combien de version au minimum sont activement maintenues par Debian ? Donnez leur nom générique (= les types de distribution).

:   -   Il y a 3 versions au minimum activement maintenues par Debian :
        *stable, testing* et *unstable*.

Chaque distribution majeur possède un nom de code différent. Par exemple la version majeur actuelle (Debian 12) se nomme bookworm. D'où viennent les noms de code données aux distributions ?

:   -   Les noms de code donnés aux distributions viennent de
        personnages de Toy Story. La décision d'utiliser des noms
        provenant de Toy Story a été prise par Bruce Perens qui était, à
        l'époque, responsable du projet Debian et travaillait chez
        Pixar, la société qui a produit les films.
ue signifie "64-bit" dans "Debian 64-bit" ?
L'un des atouts de Debian fut le nombre d'architecture (≈ processeurs) officiellement prises en charge. Combien et lesquelles sont prises en charge par la version Bullseye ?

:   -   Il y a 10 architectures officiellement prises en charge par la
        version Bullseye : amd64 (la notre) arm64 armel armhf i386 mips
        mips64el mipsel ppc64el s390x.

Quel était le numéro de version de cette distribution ?

:   -   Le numéro de version de cette distribution était 11.

Quel a été le premier nom de code utilisé ?

:   -   Le premier nom de code utilisé était Buzz.

## 2^ème^-semaine  

Nous nous sommes initié au **Markdown** via les sites qui nous ont été
fournis pour s'exercer
([markdowntutorial.com](http://markdowntutorial.com) &
[commonmark.org](http://commonmark.org)) mais certain.*es* d'entre nous
savaient déjà l'utiliser par le passé.

Pour le choix de l'éditeur, nous préférons sur navigateur afin de
s'épargner de devoir installer un logiciel sur chaque machines où nous
travaillons. Nous avons donc choisi **StackEdit** car après avoir essayé
d'autres options telles que **NextCloud** ou **github/lab**, celui-ci
nous paraissait optimal bien qu'on ne puisse pas rédiger le rapport en
temps réel ensemble comme sur **NextCloud**.

Nous avons exporté notre rapport sous le format .md et nous l'avons
compilé en HTML avec la commande:

`pandoc.exe --standalone --template template-compilation-s203.html rapport-s203-markdown.md -o rapport-s203.html`

L'utilisation de l'option -\-template permet, par exemple, de donner un titre à la page et d'utiliser une feuille de style CSS externe.

En revanche, nous nous sommes heurté.e.s à des obstacles pour la compilation au format PDF avec pdflatex.

## 3^ème^-semaine 

### ![drawing](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Question_mark_alternate.svg/788px-Question_mark_alternate.svg.png =20x30): Interfaces graphiques pour *git* 

Qu'est-ce que le logiciel gitk ? Comment se lance-t-il ?

:   -   gitk est un navigateur de dépôt graphique qui permet de voir nos
        branches et nos commits, pulls, push, etc. sur git. Il se lance
        depuis le terminal depuis un dépôt.

Qu'est-ce que le logiciel git-gui ? Comment se lance-t-il ?

:   -   Interface graphique de Git basée sur Tcl/Tk. Git gui permet aux
        utilisateurs d'apporter des modifications à leur dépôt en
        faisant de nouveaux commits, en modifiant les commits existants,
        en créant des branches, en effectuant des fusions locales, et en
        récupérant/poussant vers des dépôts distants.

:   -   Contrairement à gitk, git gui se concentre sur la génération de
        commit et l'annotation de fichiers uniques et n'affiche pas
        l'historique du projet. Il fournit cependant des actions de menu
        pour démarrer une session gitk à partir de git gui.

Pourquoi avez-vous choisi ce logiciel ?

:   -   Nous avons choisi [Gittyup](https://murmele.github.io/Gittyup/) car
        son interface semble plus logique, intuitive et esthéthiquement
        plaisante.

Comment l'avez vous installé ?

:   -   Sur leur site avec la version demo Linux-Debian. L'essai gratuit
        couvre entièrement notre temps d'utilisation du logiciel pour la
        SAE.

Comparer-le aux outils inclus avec git (et installé précédemment) ainsi qu'avec ce qui serait fait 2/3 \| 1.1. Configuration globale de git en ligne de commande pure : fonctionnalités avantages, inconvénients...

:   -   L'interface est logique (schéma clairs, différentes couleurs,
        interface simpliste.) et nous permet de visualiser simplement
        l'arborescence, nous faisant gagner énormément de temps. Des
        outils nous permettent de visualiser les modifications
        rapidement en passant le curseur dessus, on dispose également de
        stats par utilisateurs et d'actions effectuables simultanément
        sur plusieurs repository sélectionnés (fetch, pull, clone ect).
		

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTE4MDc0ODQ0MywtNDYxMDg5MzYsMTc3NT
c1NjgxOSwtOTQ2NDAwODMsLTg5MTAyNjQ0MSwtMTkyNzAyMTU5
MV19
-->