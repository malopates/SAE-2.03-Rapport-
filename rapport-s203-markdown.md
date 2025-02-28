---
title: Rapport SAE 2.03
author: LOUIS HUARD, MALORI ALVAREZ, ULYSSE PETILLON
toc: true
geometry: margin=2cm
---



# Rapport SAE 2.03 **[Installation de services réseaux](https://moodle.univ-lille.fr/course/view.php?id=30827&sectionid=266881)**

##  1^ère^ semaine

### ![Logo PC](https://cdn-icons-png.flaticon.com/512/73/73575.png =50x50) Création de la 1^ère^ VM

Pour la première séance, nous avons commencé en créant une nouvelle VM dans **Oracle VM VirtualBox** que nous avons appelée "*SAE203auto*", sans aucun fichier iso, mais en sélectionnant le type (**Linux**) et la version (**Debian 12 Bookworm** en version 64-bit). Pour ce qui est de l'iso, il faut se rendre dans l'onglet `Storage`, puis dans "`Controller: IDE`" -> "`Empty`" pour rajouter dans "`IDE Primary Device 0`" le fichier "**S203-Debian12.viso**" afin que les fichiers de configuration automatique se lancent convenablement.

Le reste de la configuration de la VM s'est faite en laissant les paramètres **par défaut.** Cependant, l'étape de chargement du pre-seed était bloqué, suite aux recherches de Louis, nous avons pu résoudre ce problème en redémarrant la machine à cette étape. 



![Screen création VM](https://lh7-us.googleusercontent.com/HwGyxgCVJ4FucrncIYnrRtDJVPEdrYU__IEii__UMg29YSwengtuclEehw575ZxLd2NAXWbh1VpI0xNDq_o1kRc3V7HD2PmTYJKxIz14KGTN7T8BkUGBZ_M6d1aJU1k5DbnO3vo9U3_ELN5BtngKaZY)![Installation réussie](https://media.discordapp.net/attachments/861363773901635646/1344994403349172254/e186db6708bb1758.png?ex=67c2ef26&is=67c19da6&hm=14d1994d23ae87d8f5781bd4ebcc6081ad9d435cb242aaad0ac8bb65670b2f75&=&format=webp&quality=lossless&width=1131&height=604)
Avant de lancer la VM, nous avons modifié les fichiers
"`vboxpostinstall.sh`" et "`preseed-fr.cfg`" pour paramétrer notre VM,
ainsi que l'uuid dans le fichier viso.

Dans la **preseed**, on a modifié la ligne 83 afin d'avoir
l'installation de mate :

`tasksel tasksel/first multiselect standard ssh-server, mate-desktop`\

**![](https://lh7-us.googleusercontent.com/NXvSVVd-LuTqc8ObAwmz6XIFKAmHAJZ_OK8OafUy8bNNJKmjDhpUu9oPERd58B_ClqZOfV2UAREMnBZijqGrVuKCphyTQjMiojFAJzsT8ltba8KWeEGLdtXVldk-NC7NrDMONRHsnqa2n-hrjT5Un5s)**
Dans le **vboxpostinstall**, nous avons ajouté dans la partie "Run user
command" les commandes pour installer les fonctionnalités attendues :\

Soit au lancement de la machine grâce à la commande `log_command_in_target`

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
![](https://zimbra.univ-lille.fr/service/home/~/?auth=co&loc=fr_FR&id=4172&part=3)

### ![Question](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Question_mark_alternate.svg/788px-Question_mark_alternate.svg.png =20x30)   Configuration matérielle dans VirtualBox 

Que signifie "64-bit" dans "Debian 64-bit" ?

:   - **Malori** : C'est la ==largeur des registres== soit la façon dont le processeur gère les informations qu'il doit effectuer, ce qui signifie que Debian peut faire tourner le processeur en 64-bit, et en 32-bit, ce qui n'est pas réciproque (le 32-bit ne fait pas tourner du 64). Pour exploiter ces processeurs à leur plein potentiel, il est nécessaire que les logiciels et l’O.S sur la machine soient compatibles, sinon le processeur se limiterait à tourner en mode 32 bits : c’est la **compatibilité descendante.**

***[Source](https://en.wikipedia.org/wiki/64-bit_computing)***

Quelle est la configuration réseau utilisée par défaut ?

:   - **Louis** :  La configuration réseau utilisée par défaut est la configuration
        en mode *NAT*. (Network Adress Translation) Celui-ci permet à la machine virtuelle de se connecter à Internet ==sans avoir à lui fournir une adresse I.P== sur le réseau local, car elle utilise la connexion de l’hôte.

***[Source](https://www.virtualbox.org/manual/ch06.html#network_nat)***


Quel est le nom du fichier XML contenant la configuration de votre machine ?

:   -   **Ulysse** : Le nom du fichier XML contenant la configuration de votre machine est le fichier `sae203.vbox`, (``/usr/local/virtual_machine/infoetu/prenom.nom.etu/sae203``) comme le dit le manuel virtualbox en source :   
> "the XML settings file, with a `.vbox` file extension."

***[Source](https://www.virtualbox.org/manual/ch10.html#vboxconfigdata-XML-files)***


Comment vous modifieriez ce fichier de configuration pour mettre 2 processeurs à votre machine ?

:   -  **Malori  :** Pour mettre 2 processeurs à votre machine, je modifierais le
        fichier de configuration en ajoutant la ligne suivante :
        `<vcpu placement='static'>2</vcpu>.`
        [Source](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/6/html/virtualization_administration_guide/sect-cpu-allocation#sect-cpu-allocation)
        
        OU
 :   - **Louis** :   A partir de la commande VboxManage modifyvm, on peut utiliser « --cpus <cpucount> ».
Ainsi, pour mettre 2 processeurs, il suffit d’ajouter la ligne :
```VboxManage modifyvm --cpus 2```
[Source](https://docs.oracle.com/en/virtualization/virtualbox/6.0/user/vboxmanage-modifyvm.html)

### ![drawing](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Question_mark_alternate.svg/788px-Question_mark_alternate.svg.png =20x30) Installation OS de base 

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

: - **Ulysse** :  [Un serveur WEB]{.underline} est un logiciel qui ==contrôle et permet de rendre accessible des **fichiers** ou des **pages WEB**== (liste non-exhaustive) via HTTP ou HTTPS.

: - **Louis** : On peut distinguer deux catégories de serveurs Web :

>1. Le serveur logiciel, qui contrôle la façon dont l’utilisateur accède à un fichier hébergé sur le serveur matériel. On trouvera un serveur HTTP, qui utilise le protocole HTTP et permet de recevoir des requêtes HTTP de l’utilisateur et renvoyer des réponses HTTP contenant les fichiers demandés.
>
>2. Le serveur matériel, qui est un ordinateur stockant les fichiers composant le site Web et un serveur Web. Il est connecté à Internet et possède un nom de domaine.

***[Source](https://wiki.archlinux.org/title/List_of_applications/Internet#Web_servers)***

Qu'est-ce qu'un serveur ssh ?

:   -   **Ulysse** : SSH (pour "*Secure Shell*") est un protocole de communication sécurisé qui permet d'==accéder à distance à un serveur==, et donc un outil permettant de se connecter à distance à des serveurs Linux pour les administrer.
: - **Louis** : Il surpasse son prédécesseur Telnet notamment par **sa sécurité accrue des données échangées**. Le protocole, et la suite d’applications associées, permettent ainsi saisir des commandes sur un shell pour ==gérer en temps réel des serveurs Linux==, mais aussi ==le transfert de fichiers entre des machines== de façon sécurisée.

***[Source](https://wiki.archlinux.org/title/Secure_Shell)***


Qu'est-ce qu'un serveur mandataire ?

:   -   **Malori** : Un serveur mandataire (dit "*proxy*" plus communément) est un serveur intermédiaire qui vient ==se placer entre deux hôtes==. 

: - **Ulysse** : Par exemple, une requête d’accès à une ressource normalement transmise du client vers un serveur, Web par exemple, passera plutôt par un serveur proxy, qui ==évalue la requête et la retransmet correctement au second hôte==.

: - **Louis** : 
 Les serveurs mandataires ont ==différentes utilités== :

> 1. Ils peuvent servir à filtrer l’accès certains contenus, ce qui est pratique en milieu académique ou professionnel.

> 2. Inversement, des serveurs proxy peuvent servir à passer outre ce genre de filtre. Ils sont par exemple utilisés pour éviter les restrictions gouvernementales et géographiques d’accès au contenu.

> 3. Ils servent aussi a anonymiser l’accès à un contenu, puisque le proxy est souvent situé du côté du client. L’origine de la requête peut dont être masquée aux yeux du serveur.

> 4. Les serveurs mandataires permettent aussi une accélération du traitement des requêtes en sauvegardant des fragments d’anciennes requêtes, ou encore la traduction de sites internet.

***[Source](https://wiki.archlinux.org/title/Proxy_server)***

###  
![question](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Question_mark_alternate.svg/788px-Question_mark_alternate.svg.png =20x30) Comment peux-ton savoir à quels groupes appartient l'utilisateur user ?

:   -  **Malori** :  On peut utiliser la commande : `\$ groups user` mais il y en a d'autres, comme `id -G -n user`

: - **Louis** : Combiner la commande `getent group`, qui affiche tous les groupes de la machine,  avec `grep -w user`, -w n’affiche que les lignes contenant **exactement** user,  de la manière suivante : `getent group | grep -w user`[^2].  

[^2]: On peut également regarder dans le fichier /etc/gshadow, il y a une ligne qui contient l'utilisateur et son/ses groupe(s)

***[Source](https://www.geeksforgeeks.org/how-to-check-the-groups-a-user-belongs-to-in-linux/)***


### ![question](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Question_mark_alternate.svg/788px-Question_mark_alternate.svg.png =20x30)    Suppléments invités 

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
:   -  **Louis** : Et elle permet donc au **système d’exploitation** d’utiliser **l’unité de stockage** montée. En effet, on va à la fois ==créer un chemin d’accès dans l’arborescence du système qui redirige vers l’unité de stockage== (c’est l’ancien chemin du point de montage) et on va aussi ==formater la partition avec un système de fichier==.

***[Source](https://man.archlinux.org/man/mount.8)***


### ![question](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Question_mark_alternate.svg/788px-Question_mark_alternate.svg.png =20x30)  Quelques questions 

Il existe 3 durées de prise en charge (support) de ces versions : la durée minimale la durée en support long terme (LTS) et la durée en support long terme étendue (ELTS). Quelles sont les durées de ces prises en charge ?

:   -   **Ulysse** : 

| Type       | Durée       | Source                                     |
|------------|-------------|---------------------------------------------|
| Minimum    | **3 ans**   | [Lien](https://wiki.debian.org/DebianReleases#Release_statistics) |
| LTS        | **5 ans**   | [Lien](https://wiki.debian.org/LTS)         |
| ELTS       | **10 ans**  | [Lien](https://wiki.debian.org/LTS/Extended) |



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
Que signifie "64-bit" dans "Debian 64-bit" ?
L'un des atouts de Debian fut le nombre d'architecture (≈ processeurs) officiellement prises en charge. Combien et lesquelles sont prises en charge par la version Bullseye ?

:   -   Il y a 10 architectures officiellement prises en charge par la
        version Bullseye : amd64 (la notre) arm64 armel armhf i386 mips
        mips64el mipsel ppc64el s390x.

Quel était le numéro de version de cette distribution ?

:   -   Le numéro de version de cette distribution était 11.

Quel a été le premier nom de code utilisé ?

:   -   Le premier nom de code utilisé était Buzz.

## 2^ème^-semaine  

**Malori** : Etant déjà initiée au **Markdown**, *riche d'une précédente SAE 2.03*, je me suis chargée de centraliser les rapports et questions en un seul et même rapport. Louis et Ulysse se sont initié au MarkDown via les sites [^3] qui nous ont été fournis pour s'exercer; Ils m'ont ensuite envoyé leurs rendus afin que je puisse regrouper nos réponses.

[^3]: [markdowntutorial.com](http://markdowntutorial.com) & [commonmark.org](http://commonmark.org)

Pour le choix de l'éditeur, je préfére travailler sur navigateur afin de m'épargner de devoir installer un logiciel sur chaque machines sur lesquelles je pourrais travailler. J'ai donc repris **StackEdit**, y étant familière.

En parralèle, Ulysse s'est penché sur **pandoc**, et a exporté ses réponses sous le format .md puis compilé en HTML avec la commande:

`pandoc.exe rapport-s203-markdown.md -o rapport-s203.html`

![Capture d'écran du rapport d'Ulysse](https://cdn.discordapp.com/attachments/1339313007641235487/1344963770484654191/Capture_du_2025-02-28_10-24-361.png?ex=67c2d29f&is=67c1811f&hm=d3bd37ace3b798ba34e9429abc5f3d6c0a048557bb8459c1a110ee731556ad90&)


## 3^ème^-semaine 

### ![drawing](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Question_mark_alternate.svg/788px-Question_mark_alternate.svg.png =20x30)    Interfaces graphiques pour *git* 

Qu'est-ce que le logiciel gitk ? Comment se lance-t-il ?

:   - **Malori** : gitk est un navigateur de dépôt graphique qui permet de voir nos
        branches et nos commits, pulls, push, etc. sur git. Il se lance
        depuis le terminal depuis un dépôt. 

https://www.atlassian.com/fr/git/tutorials/gitk

Qu'est-ce que le logiciel git-gui ? Comment se lance-t-il ?

:   -  **Malori** : Interface graphique de Git basée sur Tcl/Tk. Git gui permet aux
        utilisateurs d'apporter des modifications à leur dépôt en
        faisant de nouveaux commits, en modifiant les commits existants,
        en créant des branches, en effectuant des fusions locales, et en
        récupérant/poussant vers des dépôts distants.

:   -   **Malori** : Contrairement à gitk, git gui se concentre sur la génération de
        commit et l'annotation de fichiers uniques et n'affiche pas
        l'historique du projet. Il fournit cependant des actions de menu
        pour démarrer une session gitk à partir de git gui.
[https://git-scm.com/docs/git-gui/fr]

Pourquoi avez-vous choisi ce logiciel ?

:   -   **Malori** : Nous avons choisi [Gittyup](https://murmele.github.io/Gittyup/) car c'est un logiciel open-source, son interface le rend facile d'utilisation, et sa license est MIT, c'est-à-dire qu'elle impose très peu de restrictions à la réutilisation et présente donc une compatibilité de licence élevée.

Comment l'avez vous installé ?

:   -   **Malori** Sur leur site avec la version demo Linux-Debian. L'essai gratuit
        couvre entièrement notre temps d'utilisation du logiciel pour la
        SAE.

Comparez-le aux outils inclus avec git (et installé précédemment) ainsi qu’avec ce qui serait fait en ligne de commande pure : fonctionnalités avantages, inconvénients…

:   -   **Ulysse** : L'interface est épuré et intuitive, avec des schémas clairs et un code couleur distinctif, la navigation est fluide et efficace. 
L’arborescence est facilement lisible, ce qui fait gagner du temps à l'utilisateur. Des fonctionnalités pratiques permettent d’apercevoir instantanément les modifications en survolant les éléments. On peut aussi consulter les statistiques détaillées de chaque utilisateur, et il est possible d’effectuer plusieurs actions en parallèle comme fetch, pull ou clone sur plusieurs repo à la fois.
		

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTc3MDE1MTc2OSwxOTU5OTgzODAxLC0xMD
Q0NjAwMjI0LDY0OTYzMjIxLC0xMjM5NTEzNzA3LC0xMDA1NTMz
MjUsMTkxODUzNDgxNiwtMzM5NzkxODE0LDEwOTA3MjkxMTUsND
gwNDQ0OTgsNTI3MDE0MDU4LC0xMjkxMTY2OTQsLTk0MDk4MzA4
MywxNTQ0MjU5MTE2LC02ODgzOTcyMDgsLTQxMzI3MTk4Miw4Nz
EyNzgyOCw3MDA0MjkxNTIsLTE2NDQyNTQ4MTMsLTIwMjkyNjM4
ODVdfQ==
-->