---
title: Rapport SAE 2.03
author: LOUIS HUARD, MALORI ALVAREZ, ULYSSE PETILLON
toc: true
geometry: margin=2cm
---



# Rapport SAE 2.03 **[Installation de services réseaux](https://moodle.univ-lille.fr/course/view.php?id=30827&sectionid=266881)**

##  1^ère^ semaine
***
### *Création de la 1^ère^ VM*

Pour la première séance, nous avons commencé en créant une nouvelle VM dans **Oracle VM VirtualBox** que nous avons appelée "*SAE203auto*", sans aucun fichier iso, mais en sélectionnant le type (**Linux**) et la version (**Debian 12 Bookworm** en version 64-bit). Pour ce qui est de l'iso, il faut se rendre dans l'onglet `Storage`, puis dans "`Controller: IDE`" -> "`Empty`" pour rajouter dans "`IDE Primary Device 0`" le fichier "**S203-Debian12.viso**" afin que les fichiers de configuration automatique se lancent convenablement.

Le reste de la configuration de la VM s'est faite en laissant les paramètres **par défaut.** Cependant, l'étape de chargement du pre-seed était bloqué, suite aux recherches de Louis, nous avons pu résoudre ce problème en redémarrant la machine à cette étape. 

![Screen création VM](https://lh7-us.googleusercontent.com/HwGyxgCVJ4FucrncIYnrRtDJVPEdrYU__IEii__UMg29YSwengtuclEehw575ZxLd2NAXWbh1VpI0xNDq_o1kRc3V7HD2PmTYJKxIz14KGTN7T8BkUGBZ_M6d1aJU1k5DbnO3vo9U3_ELN5BtngKaZY)



###    *Configuration matérielle dans VirtualBox*

Que signifie "64-bit" dans "Debian 64-bit" ?

:   - **Malori** : C'est la largeur des registres soit la façon dont le processeur gère les informations qu'il doit effectuer, ce qui signifie que Debian peut faire tourner le processeur en 64-bit, et en 32-bit, ce qui n'est pas réciproque (le 32-bit ne fait pas tourner du 64). Pour exploiter ces processeurs à leur plein potentiel, il est nécessaire que les logiciels et l’O.S sur la machine soient compatibles, sinon le processeur se limiterait à tourner en mode 32 bits : c’est la **compatibilité descendante.**

***[Source](https://en.wikipedia.org/wiki/64-bit_computing)***

Quelle est la configuration réseau utilisée par défaut ?

:   - **Louis** :  La configuration réseau utilisée par défaut est la configuration
        en mode *NAT*. (Network Adress Translation) Celui-ci permet à la machine virtuelle de se connecter à Internet sans avoir à lui fournir une adresse I.P sur le réseau local, car elle utilise la connexion de l’hôte.

***[Source](https://www.virtualbox.org/manual/ch06.html#network_nat)***


Quel est le nom du fichier XML contenant la configuration de votre machine ?

:   -   **Ulysse** : Le nom du fichier XML contenant la configuration de votre machine est le fichier `sae203.vbox`, (``/usr/local/virtual_machine/infoetu/prenom.nom.etu/sae203``) comme le dit le manuel virtualbox en source :   
> "the XML settings file, with a `.vbox` file extension."

***[Source](https://www.virtualbox.org/manual/ch10.html#vboxconfigdata-XML-files)***


Comment vous modifieriez ce fichier de configuration pour mettre 2 processeurs à votre machine ?

 :   - **Louis** :   A partir de la commande VboxManage modifyvm, on peut utiliser « --cpus <cpucount> ».
Ainsi, pour mettre 2 processeurs, il suffit d’ajouter la ligne :
```VboxManage modifyvm --cpus 2```
[Source](https://docs.oracle.com/en/virtualization/virtualbox/6.0/user/vboxmanage-modifyvm.html)

###  Installation OS de base 

Qu'est-ce qu'un fichier iso bootable ?

:   -  **Louis** :  Un fichier iso bootable est un fichier qui s'exécute au
        démarrage de la VM et qui contient une instance d'un système
        d'exploitation. Il permet de créer la machine. 
        
:   - **Ulysse** : 
Un [fichier ISO bootable]{.underline} est un fichier qui contient une **image disque**, et cette image contient une **copie des données du disque** en question. On peut l'utiliser pour **stocker des données** sans support de stockage, ou dans notre cas **servir de CD-ROMs virtuel** pour l'[hyperviseur]{.underline} (ici VirtualBox).

***[Source](https://en.wikipedia.org/wiki/Disk_image)***

Qu'est-ce que MATE ? GNOME ?

:   -   **Malori** : *MATE* et *GNOME* sont des environnements de bureau (dit "DE")[^1] de Debian. On y retrouve le bureau, le gestionnaire de fenêtres, les différents menus…

:   - **Louis** : MATE est une dérivation de GNOME.  
GNOME est un environnement de bureau conçu pour les systèmes d’exploitation GNU/Linux et UNIX. MATE a repris le code source de GNOME 2 pour reprendre l’aspect « bureau », délaissé par la version GNOME 3.

[^1]: Un environnement de bureau est un logiciel qui regroupe différents outils constituant une interface graphique, qui permettent à l’utilisateur d’interagir graphiquement avec l’ordinateur.

***[Source](https://wiki.archlinux.org/title/Desktop_environment)***

Qu'est-ce qu'un serveur web ?

: - **Ulysse** :  [Un serveur WEB]{.underline} est un logiciel qui contrôle et permet de rendre accessible des **fichiers** ou des **pages WEB** (liste non-exhaustive) via HTTP ou HTTPS.

: - **Louis** : On peut distinguer deux catégories de serveurs Web :

>1. Le serveur logiciel, qui contrôle la façon dont l’utilisateur accède à un fichier hébergé sur le serveur matériel. On trouvera un serveur HTTP, qui utilise le protocole HTTP et permet de recevoir des requêtes HTTP de l’utilisateur et renvoyer des réponses HTTP contenant les fichiers demandés.
>
>2. Le serveur matériel, qui est un ordinateur stockant les fichiers composant le site Web et un serveur Web. Il est connecté à Internet et possède un nom de domaine.

***[Source](https://wiki.archlinux.org/title/List_of_applications/Internet#Web_servers)***

Qu'est-ce qu'un serveur ssh ?

:   -   **Ulysse** : SSH (pour "*Secure Shell*") est un protocole de communication sécurisé qui permet d'accéder à distance à un serveur, et donc un outil permettant de se connecter à distance à des serveurs Linux pour les administrer.
: - **Louis** : Il surpasse son prédécesseur Telnet notamment par **sa sécurité accrue des données échangées**. Le protocole, et la suite d’applications associées, permettent ainsi saisir des commandes sur un shell pour gérer en temps réel des serveurs Linux, mais aussi le transfert de fichiers entre des machines de façon sécurisée.

***[Source](https://wiki.archlinux.org/title/Secure_Shell)***


Qu'est-ce qu'un serveur mandataire ?

:   -   **Malori** : Un serveur mandataire (dit "*proxy*" plus communément) est un serveur intermédiaire qui vient se placer entre deux hôtes. 

: - **Ulysse** : Par exemple, une requête d’accès à une ressource normalement transmise du client vers un serveur, Web par exemple, passera plutôt par un serveur proxy, qui évalue la requête et la retransmet correctement au second hôte.

: - **Louis** : 
 Les serveurs mandataires ont différentes utilités :

> 1. Ils peuvent servir à filtrer l’accès certains contenus, ce qui est pratique en milieu académique ou professionnel.

> 2. Inversement, des serveurs proxy peuvent servir à passer outre ce genre de filtre. Ils sont par exemple utilisés pour éviter les restrictions gouvernementales et géographiques d’accès au contenu.

> 3. Ils servent aussi a anonymiser l’accès à un contenu, puisque le proxy est souvent situé du côté du client. L’origine de la requête peut dont être masquée aux yeux du serveur.

> 4. Les serveurs mandataires permettent aussi une accélération du traitement des requêtes en sauvegardant des fragments d’anciennes requêtes, ou encore la traduction de sites internet.

***[Source](https://wiki.archlinux.org/title/Proxy_server)***

###  Comment peux-ton savoir à quels groupes appartient l'utilisateur user ?

:   -  **Malori** :  On peut utiliser la commande : `\$ groups user` mais il y en a d'autres, comme `id -G -n user`

: - **Louis** : Combiner la commande `getent group`, qui affiche tous les groupes de la machine,  avec `grep -w user`, -w n’affiche que les lignes contenant **exactement** user,  de la manière suivante : `getent group | grep -w user`[^2].  

[^2]: On peut également regarder dans le fichier /etc/gshadow, il y a une ligne qui contient l'utilisateur et sonses groupe(s)

***[Source](https://www.geeksforgeeks.org/how-to-check-the-groups-a-user-belongs-to-in-linux/)***


### *Suppléments invités* 

Quelle est la version du noyau Linux utilisé par votre VM ? N'oubliez pas, comme pour toutes les questions, de justifier votre réponse.

:   -  **Malori** :  On l'obtient avec la commande `uname -r`, uname donne des
        informations sur le système qu'on utilise. Ici il s'agit de
        amd64.

***[Source](https://man.archlinux.org/man/uname.1.fr)***

À quoi servent les suppléments invités ? Donner 2 principales raisons de les installer.

:   -   **Ulysse** : 

Les [suppléments invités]{.underline} sont des **pilotes adapté à des systèmes invités**, c'est-à-dire les OSs des VMs.

| Avantage                     | Description                           |
|------------------------------|---------------------------------------|
| Meilleures performances vidéos | Accélération de l’affichage graphique |
|                              | Meilleure résolution d’écran          |
| Presse-papier partagé        | Partage entre l’hôte et la VM (après configuration) |
| Dossiers partagés            | Partage de dossiers entre l’hôte et la VM |
| Meilleure transition         | Fluidité améliorée entre l’hôte et la VM |


***[Source](https://doc.ubuntu-fr.org/virtualbox_additions_invite)***

À quoi sert la commande mount (dans notre cas de figure et dans le cas général) ?

:   -  **Malori** : Dans le cas général, cette commande permet de monter une partition sur le disque.
: - **Ulysse** : C'est-à-dire qu'elle **rattache** un système de fichier au système principal. Dans notre cas, l'objectif est de permettre au système d'**accéder au CDROM**.
:   -  **Louis** : Et elle permet donc au **système d’exploitation** d’utiliser **l’unité de stockage** montée. En effet, on va à la fois créer un chemin d’accès dans l’arborescence du système qui redirige vers l’unité de stockage (c’est l’ancien chemin du point de montage) et on va aussi formater la partition avec un système de fichier.

***[Source](https://man.archlinux.org/man/mount.8)***


###  *Quelques questions* 


Pendant combien de temps les mises à jour de sécurité seront-elles fournies ?

:   -   **Ulysse** : [Durée : **5 ans**](https://wiki.debian.org/LTS)
ELTS : jusqu’à **10 ans**[^4].

[^4]: Via paiement

Combien de version au minimum sont activement maintenues par Debian ? Donnez leur nom générique (= les types de distribution).

:   -  **Malori** :  Il y a 3 versions au minimum activement maintenues par Debian :
        *stable, testing* et *unstable*.

Chaque distribution majeur possède un nom de code différent. Par exemple la version majeur actuelle (Debian 12) se nomme bookworm. D'où viennent les noms de code données aux distributions ?

:   - **Malori** :  Les noms de code donnés aux distributions viennent de
        personnages de Toy Story. La décision d'utiliser des noms
        provenant de Toy Story a été prise par Bruce Perens qui était, à
        l'époque, responsable du projet Debian et travaillait chez
        Pixar, la société qui a produit les films.
        
### **Quelques questions**

 Qu’est-ce que le Projet Debian ? D’où vient le nom Debian ?

:   - **Louis** : Debian est une distribution de l’O.S Linux. Il est géré par les membres, volontaires, du projet Debian, qui se définissent comme des descendants directs du projet GNU, c’est-à-dire que leur objectif est de développer un système d’exploitation de qualité et qui le logiciel libre en son centre (liberté de distribution, de copie, de modification…). Le nom Debian est une contraction du nom du créateur, Ian Murdock, et sa femme, Debra.
[Source](https://www.debian.org/doc/manuals/debian-faq/basic-defs.fr.html#whatisdebian)  
  
Il existe 3 durées de prise en charge (support) de ces versions : la durée minimale la durée en support long terme (LTS) et la durée en support long terme étendue (ELTS). Quelles sont les durées de ces prises en charge ?

:   -   **Ulysse** : 

| Type       | Durée       | Source                                     |
|------------|-------------|---------------------------------------------|
| Minimum    | **3 ans**   | [Lien](https://wiki.debian.org/DebianReleases#Release_statistics) |
| LTS        | **5 ans**   | [Lien](https://wiki.debian.org/LTS)         |
| ELTS       | **10 ans**  | [Lien](https://wiki.debian.org/LTS/Extended) |


L’un des atouts de Debian fut le nombre d’architecture (≈ processeurs) officiellement prises en charge. Combien et lesquelles sont prises en charge par la version _Bullseye_ ?
:   -  **Louis** : 
Bullseye prend en charge 9 architectures :

-amd64

-i386

-ppc64el

-s390x

-armel

-armhf

-arm64

-mipsel

-mipsel64

**Sources :**
[WikiDebian](https://wiki.debian.org/DebianBullseye#Architectures)  
[WikiDebian](https://wiki.debian.org/LTS?highlight=%28%5CbCategoryLts%5Cb%29)

Quel a été le premier nom de code utilisé ?
    
 :   -  Le premier nom de code utilisé était « Buzz »
        [Source](https://wiki.debian.org/DebianReleases#Codenames)  
        
 Quand a-t-il été annoncé ?
    
  : -   La version Buzz a été annoncée le 17 juin 1996

    
Quelle était le numéro de version de cette distribution ?
    
:   -  Son numéro de version était 1.1
    Dernier nom de code attribué
    

Quel est le dernier nom de code annoncée à ce jour ?
    
:   -  Le dernier nom de code annoncé est « Trixie »
[Source](https://wiki.debian.org/DebianTrixie?highlight=%28CategoryRelease%29)  
    
Quand a-t-il été annoncé ?
    
:   -  Il a été annoncé le 8 Novembre 2020
[Source](https://wiki.debian.org/LTS?highlight=%28%5CbCategoryLts%5Cb%29)  
    
Quelle est la version de cette distribution ?
    
:   -  C’est le nom de code de Debian 13, qui est le « testing » actuel.

_Sources :_
[Doc Debian](https://www.debian.org/doc/manuals/debian-faq/basic-defs.fr.html#pronunciation)  

[WikiDebian](https://wiki.debian.org/fr/DebianOldStable)  



[Source](https://wiki.debian.org/fr/LTS/Extended)  

[Source](https://www.debian.org/security/faq#lifespan)  

[Source](https://www.debian.org/doc/manuals/project-history/releases.en.html)  




### *Ajustement de la pré-configuration*

Pour cette partie, il faut effectuer les modifications associées à preseed.cfg :

1. Modifier les groupe de « user » :

Pour ce faire, on a remplacé la ligne :

```d-i passwd/user-default-groups string audio cdrom video```

par la ligne :

```d-i passwd/user-default-groups string sudo audio cdrom video```

_Explication de la commande :_

-« d-i » est une abréviation pour « debian installer »

-passwd/user-default-groups est le fichier qui contient les groupe assignés par défaut à l’utilisateur par défaut, ici « user ». Cela a des conséquences uniquement sur cet utilisateur, et non ceux créés ultérieurement.

-les termes suivant « string » et séparés d’un espace sont les groupes auquel « user » sera automatiquement ajouté, c’est pour cela que l’on a rajouté « sudo ».

[https://www.debian.org/releases/stable/amd64/apbs04.fr.html#preseed-account](https://www.debian.org/releases/stable/amd64/apbs04.fr.html#preseed-account)  

2. Installation de MATE :

MATE est l’un des nombreux paquets que l’on peut sélectionner lors d’une installation graphique manuelle, ou avec la commande tasksel.

Pour automatiser cette sélection et installation, on va utiliser la ligne de commande suivante :

![](https://media.discordapp.net/attachments/861363773901635646/1345003034169577514/34be8daa4b5249e5.png?ex=67c2f730&is=67c1a5b0&hm=0c12dbf9965c91c7541f0f77f56ed1ae7e923a8f1c77a253e72513f0e3e3c831&=&format=webp&quality=lossless&width=1440&height=207)
(A noter qu’il n’y a pas besoin d’écrire « d-i » au début de la commande)

3. Installation des paquets supplémentaire :

Pour installer des paquets non présents dans le tasksel, on va utiliser la commande ``` d-i pkgsel/include``` et, à la suite de « string », on va renseigner les différents paquets à installer, soit :

![](https://media.discordapp.net/attachments/861363773901635646/1345003005614751786/8eb0211196a577b2.png?ex=67c2f729&is=67c1a5a9&hm=9422127e5508a4c2dfbec7cca352f639cd09cf661cb30f39d53e366a4f0d9f57&=&format=webp&quality=lossless&width=1440&height=99)

• sudo

• git

• sqllite3

• curl

• bash-completion

• neofetch


![Installation réussie](https://media.discordapp.net/attachments/861363773901635646/1344994403349172254/e186db6708bb1758.png?ex=67c2ef26&is=67c19da6&hm=14d1994d23ae87d8f5781bd4ebcc6081ad9d435cb242aaad0ac8bb65670b2f75&=&format=webp&quality=lossless&width=1131&height=604)

Alternativement, 

Dans le **vboxpostinstall**, l'on peut dans la partie "Run user
command" mettre les commandes pour installer les fonctionnalités attendues :

Soit au lancement de la machine grâce à la commande 

`log_command_in_target`

`log_command_in_target usermod -a -G sudo user`

`log_command_in_target apt-get install sudo`

`log_command_in_target sudo apt-get install git`

`log_command_in_target sudo apt-get install sqlite3`

`log_command_in_target sudo apt-get install curl`

`log_command_in_target sudo apt-get install bash-completion`

`log_command_in_target sudo apt-get install neofetch`

## 2^ème^-semaine  
***
**Malori** : Etant déjà initiée au **Markdown**, *riche d'une précédente SAE 2.03*, je me suis chargée de centraliser les rapports et questions en un seul et même rapport. Louis et Ulysse se sont initié au MarkDown via les sites [^3] qui nous ont été fournis pour s'exercer; Ils m'ont ensuite envoyé leurs rendus afin que je puisse regrouper nos réponses.

[^3]: [markdowntutorial.com](http://markdowntutorial.com) & [commonmark.org](http://commonmark.org)

Pour le choix de l'éditeur, je préfére travailler sur navigateur afin de m'épargner de devoir installer un logiciel sur chaque machines sur lesquelles je pourrais travailler. J'ai donc repris **StackEdit**, y étant familière.

En parralèle, Ulysse s'est penché sur **pandoc**, et a exporté ses réponses sous le format .md puis compilé en HTML avec la commande:

`pandoc.exe rapport-s203-markdown.md -o rapport-s203.html`

![Capture d'écran du rapport d'Ulysse](https://cdn.discordapp.com/attachments/1339313007641235487/1344963770484654191/Capture_du_2025-02-28_10-24-361.png?ex=67c2d29f&is=67c1811f&hm=d3bd37ace3b798ba34e9429abc5f3d6c0a048557bb8459c1a110ee731556ad90&)


## 3^ème^-semaine 
***
### *Introduction*
*Nous allons cette semaine traiter le cas de Git et des applications graphiques qui lui sont associées*

**Pour ce faire, nous allons :** 
1. Configurer git sur la machine virtuelle
2. Etudier les applications graphiques officielles gitk et git-gui
3. Installer une autre applications non-officielle : gitnuro

### I. Configuration de git sur la machine

### *Qu'est-ce que git ?*

Git est un logiciel de gestion de versions décentralisé (DVCS) qui se différencie des autres VCS
par sa vison des données comme une série de snapshots, au lieu de simplement y voir des fichiers et les changements qu'on leur applique.
Git sépare le travail en trois espaces : le dossier de travail, un index auquel on ajoute les changements et un dépôt .git, sur lequel on enverra les changements définitifs.

[Source](https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F)


### Comment le configurer sur la machine virtuelle ?

### II. Les applications graphiques officielles

J'ai utilisé `apt-cache search <name>` pour m'assurer d'utiliser les bon noms de paquets pour la machine


### *Installation sur la machine virtuelle*

Il existe plusieurs méthodes d'installer **gitnuro** sur une machine Linux proposées par leur site officiel.

Nous avons décidé d'utilise la ligne de commande :

`flatpak install --user com.jetpackduba.Gitnuro`

Mais pour ce faire, il fallait donc installer le paquet `flatpak` avec la commande `apt-get install flatpak`

Cependant, n'ayant aucune expérience, il était inévitable de tomber sur une erreur :

![Capture d'écran gitnuro](https://cdn.discordapp.com/attachments/861363773901635646/1345079726775074848/b5b6f1b1902b5679.png?ex=67c33e9d&is=67c1ed1d&hm=1d381ace0313cf0f346ff2cfd037af08d7ae17e06894d6411a1d8e88e8089944&)

Après avoir essayé, en vain, d'utiliser flathub, nous sommes tombés sur [ce post](https://superuser.com/questions/1755709/getting-this-error-when-trying-to-use-flatpak-no-remote-refs-found-similar-to)

Qui préconisait l'utilisation de la commande suivante :

`flatpak remote-add --user --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo`

Ainsi, nous n'avons plus eu qu'à réessayer la commande initiale et l'installation a eu lieu sans problème.

Pour lancer gitnuro, il suffit d'utiliser la commande :

`flatpak run com.jetpackduba.Gitnuro`

![Screen flatpak](https://cdn.discordapp.com/attachments/861363773901635646/1345079267356311593/718f9810582fd268.png?ex=67c33e2f&is=67c1ecaf&hm=893bd0046ebf42c972b49e0db82d6fee27eb8ac56d9d7d23956655163eea37a0&)

###    *Interfaces graphiques pour *git** 

Qu'est-ce que le logiciel gitk ? Comment se lance-t-il ?

:   - **Malori** : gitk est un navigateur de dépôt graphique qui permet de voir nos
        branches et nos commits, pulls, push, etc. sur git. Il se lance
        depuis le terminal depuis un dépôt. 
        
:   - **Ulysse** :  Il se concentre sur la visualisation et lanavigation dans l’historique du projet.
Pour le lancer, il faut taper la commande gitk, en étant dans un dépôt git (dans le terminal).
**Sources :**
***[Atlassian](https://www.atlassian.com/fr/git/tutorials/gitk)**
**[Man archlinux](https://man.archlinux.org/man/gitk.1.en)***


Qu'est-ce que le logiciel git-gui ? Comment se lance-t-il ?

:   -  **Malori** : C'est une Interface graphique de Git basée sur Tcl/Tk. Git gui permet aux
        utilisateurs d'apporter des modifications à leur dépôt en
        faisant de nouveaux commits, en modifiant les commits existants,
        en créant des branches, en effectuant des fusions locales, et en
        récupérant/poussant vers des dépôts distants.
Contrairement à gitk, git gui se concentre sur la génération de
        commit et l'annotation de fichiers uniques et n'affiche pas
        l'historique du projet. Il fournit cependant des actions de menu
        pour démarrer une session gitk à partir de git gui.
***[Source](https://git-scm.com/docs/git-gui/fr)***

Pourquoi avez-vous choisi ce logiciel ?

:   -   **Ulysse** : J’ai choisi premièrement d’installer le logiciel gitnuro, parce qu’il est disponible sur Linux, Mac et Windows, qu’il est gratuit, open-source et qu’il n’est pas propriétaire, mais sous la license GNU GPL, en outre de son apparence. 

Comment l'avez vous installé ?

:   -   **Louis** :  Comme le paquet git a déjà été installé sur la machine grâce à l'éxécution du preseed de 
la semaine 1, nous n'avons plus qu'à configurer le logiciel. Pour cela, il suffit d'utiliser
la commande :

		```bash 
		git config --global user.name "Prénom Nom" &&
		git config --global user.email "votre@email" &&
		git config --global init.defaultBranch "master"
		```
		 La commande `git config` sert, comme son nom l'indique, à configurer git sur la machine.
 
		L'option `--global`est un des trois niveau de configuration de git :

		- `--local`: limite la configuration au dépôt dans lequel la fonction est invoquée. 
C'est la configuration par défaut.

		- `--global`: permet d'étendre la configuration à l'utilisateur, à travers l'entiereté de l'O.S

		- `--system`: étend la configuration à tous les utilisateurs et tous les dépôts présent sur le système
 La 3e ligne, `int.defaultBranch "master"` évite un avertissement à la création d'une branche par défaut

			*Pour plus d'informations, utilisez `man git config` (`&&` permet d'enchaîner les 3 commandes en une seule ligne)*

[Sources](https://www.atlassian.com/git/tutorials/setting-up-a-repository/git-config)

Comparez-le aux outils inclus avec git (et installé précédemment) ainsi qu’avec ce qui serait fait en ligne de commande pure : fonctionnalités avantages, inconvénients…

:   -   **Malori** : L'interface est épuré et intuitive, avec des schémas clairs et un code couleur distinctif, la navigation est fluide et efficace. L’arborescence est facilement lisible, ce qui fait gagner du temps à l'utilisateur. Des fonctionnalités pratiques permettent d’apercevoir instantanément les modifications en survolant les éléments. On peut aussi consulter les statistiques détaillées de chaque utilisateur, et il est possible d’effectuer plusieurs actions en parallèle comme fetch, pull ou clone sur plusieurs repo à la fois.
		
| Fonctionnalités      | Avantage                     | Inconvénient                           |
|----------------------|------------------------------|---------------------------------------|
| Interface graphique  | Permet de comprendre plus efficacement les changements  | Plus long au démarrage, plus lourd que le version CLI |
| Plus intuitif puisque l'utilisation de commandes n'est pas obligatoire | Plus accès débutant | Perte de temps puisqu'il faut faire des aller-retour entre le terminal et l'application |
| Customisable | Avoir les fonctionnalités que l'on souhaite | Perte de temps puisque les fonctionnalités nécessaire sont présente par défaut |
| Peut gérer plusieurs dépôt simultanément | Boost de productivité | On peut vite s'y perdre |


## 4^ème^-semaine 
***
### Introduction
*Cette semaine, nous avons vu plus en profondeur l'outil Gitea ainsi que ses homologues, et nous sommes penché un peu sur ses fonctions et son fonctionnement*

## 1. Redirection de port

Pour cette partie, il suffit simplement de cliquer sur "Configuration" après avoir sélectionné la machine virtuelle sur VirtualBox.  
Ensuite, il faut cliquer sur "Advanced" pour faire dérouler un autre menu, et ensuite "Redirection des ports".  
Enfin, configurez comme ceci :

|Nom|Protocole|IP hôte|Port hôte|IP invité|Port invité|
|:------:|:------:|:------:|:------:|:------:|:------:|
|gitea|[TCP](https://fr.wikipedia.org/wiki/Transmission_Control_Protocol)||3000||3000|

## 2. Gitea

### *À propos de Gitea*

 Qu'est-ce que Gitea ?

 :   -   **Louis :**   Gitea est ce qu'on appelle une [**forge logicielle**](https://scienceouverte.univ-grenoble-alpes.fr/codes-et-logiciels/outils-bonnes-pratiques/forges-logicielles/), c'est-à-dire que c'est un outil de "*rédaction, partage et maintenance collaborative de texte*". Gitea inclue un système de gestions de versions, **Git** (dont nous avons parlé la semaine dernière), mais aussi bien d'autres fonctionnalités : le passage en revue de code, des outils de management de projet, une réponse très rapide couplée à une faible consommation de ressources.

**Sources**:
[Doc Gitea](https://docs.gitea.com/)  
[Wikipedia gitea](https://fr.wikipedia.org/wiki/Gitea)  


À quels logiciels bien connus dans ce domaine peut-on le comparer (en citer au moins 2) ?

   :   -   **Louis :** On pourrait comparer **Gitea** à [**GitLab**](https://about.gitlab.com/), [**GitHub**](https://github.com/) ou [**BitBucket**](https://bitbucket.org/). 
 :   -   **Malori :** Ils sont tous plus que décents, et répondent aux demandes de cette SAE
 
|Fonctionnalitées| Gitea  | BitBucket | GitLab |
|--|--|--|--|
| Open-source | ✓ | ✓ | ✓ |
| Gratuit | ✓ | ✓ | ✓ |
| Support Markdown | ✓ | ✓ | ✓ |
| Double authentification | ✓ | ✓ | ✓ |
|Télémétrie | ✘|✓|✓ | 

[Source](https://docs.gitea.com/installation/comparison)  

Qu’est-ce qu’un fork (dans le domaine du développement logiciel bien entendu) ?

 :   -   **Louis :** Un **fork** est un logiciel qui est créé à partir du code source d'un logiciel existant. Pour vraiment parler de fork, et non de branche, il faut que le nouveau logiciel parte d'une *vision différente* à celle de l'initiale, et c'est pour cela que l'on parle de schisme.

 :   -   **Malori :** On peut en citer parmis plus connus, tel que [WordPress](https://fr.wikipedia.org/wiki/WordPress "WordPress") fork de **b2**  et [MariaDB](https://fr.wikipedia.org/wiki/MariaDB "MariaDB") fork de **MySQL**. 
[*Source*](https://fr.wikipedia.org/wiki/Fork_(d%C3%A9veloppement_logiciel))  

 De quel logiciel Gitea est-il le fork ? Ce logiciel existe-t-il encore ?

 :   -   **Louis :** Gitea est un fork de [Gogs](https://dl.gogs.io/) (le fork eu lieu le 17 Octobre 2016). Gogs existe toujours et sa dernière mise à jour (version 0.13.2) a été publiée le 23 décembre 2024.

[*Source*](https://fr.wikipedia.org/wiki/Fork_(d%C3%A9veloppement_logiciel))  

### *Installation du binaire*
  • **Ulysse :** On utilise la commande 
``` bash
wget -O gitea https://dl.gitea.com/gitea/main-nightly gitea-main-nightly-linux-amd64
chmod +x gitea
```
Elle nous permet de récupérer les fichiers correspondant à un système Debian 64-bit et de l'exécuter.

*recopier commandes changer 1..giteaAsc ..gitea*  
wget -O giteaAsc...

Donnez la version que vous avez installé et la commande permettant d’obtenir cette information.
 :  -  Pour obtenir la version de gitea installée, `gitea -v`

[Source](https://docs.gitea.com/administration/command-line)

 Comment faire pour mettre à jour le binaire de votre service sans devoir tout reconfigurer ? Essayez en mettant à jour vers la version `1.24-dev`.
: -  
changement de mdp user git  
  

  

Choisir le fichier adéquat  
Comme nous sommes sur Linux x64, l'on doit choisir l'outil wget^(logiciel libre permettant le téléchargement d'un fichiermais aussi la recopie en local de tout ou partie d'un site qui sera par la suite consultable hors-ligne. https://doc.ubuntu-fr.org/wget) est installé par défaut dans les dernières versions d'Ubuntu, point fort, celui ci peut tourner en arrière-plan et ainsi nous permettre de lancer un téléchargement et de nous déconnecter du système  

  

  
wget -O gitea https://dl.gitea.com/gitea/main-nightly/gitea-main-nightly-linux-amd64 chmod +x gitea  
  
(l'option -O afin de préciser le nom du fichier de destination)

*recopier commandes changer 1..giteaAsc ..gitea*  

<!--stackedit_data:
eyJoaXN0b3J5IjpbODQ4NTI0ODM5LC0xNjQ3NTU3OTMxLDQ3OD
c0OTI5MSwtMTgyOTM0Njc0MywxNDU5MTI1Nzc0LDIwMzUwOTEy
NDQsNDMwMTk4MTI2LC0xODk4MzM3MTE4LDIwNDMzMzM0MDcsLT
YyMTc5NDU4OSw1NDYwNDc0NDMsODg1NDI5MTExLC0xODM1NjU3
MDY4LDI2ODI5NTg4OCw0OTgyMTA5MTUsMTA3Nzg0NzEzOSwtMT
g1Mjc2Mzk3OSwtODQ5NDA2MDc3LC0xNjk1MjI0MTExLC0xNzE0
NDg3MzkyXX0=
-->