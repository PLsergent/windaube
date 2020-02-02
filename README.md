# X11 vs Wayland

## Introduction

Dans le cadre du cours de Unix, nous avons choisi de réaliser un article comparatif de X11 et Wayland. Tout deux sont des protocoles qui spécifient la communication entre le serveur d'affichage et le client/poste de travail.

Autrement dit, ces protocoles ont permis d'implémenter des **interfaces graphiques** (GUI: Graphical User Interface) facilitant grandement l'intéraction Homme-Machine, pour les systèmes Unix.

De nombreux systèmes d'affichage ont précédé les protocoles présentés ici. Des GUI étaient déjà présent sur des machines tels que: [Alto](https://www.wikiwand.com/en/Xerox_Alto), [Star](https://www.wikiwand.com/en/Xerox_Star) ou [Macintosh](https://www.wikiwand.com/en/Macintosh).


Macintosh             |  Xerox
:-------------------------:|:-------------------------:
![](./assets/macintosh.jpeg)  |  ![](./assets/xerox.png)

---

Nous allons donc étudier le protocole le plus utilisé de nos jours **X**, puis nous allons voir en quoi **Wayland** est une alternative intéressante. 

## X Window System

### Introduction

**X ou X Window System**, est un protocole ou framework, mettant en place une architecture particulière permettant d'implémenter des GUI sur les systèmes Unix. X11 est la onzième version de ce protocole.

#### Terminologies

- **X ou X Window System** : désigne le protocole ou framework de base qui permet de mettre en place une GUI.
- **X11** : 11ème version de X.
- **Xorg** : implémentation du serveur d'affichage (X server) utilisé dans le framework X.
- **Xlib & Xcb** : librairies côté client écrites en C permettant d'intéragir facilement avec le X server.

### Origine et objectifs

X est un idée qui a émergé au [MIT](https://www.wikiwand.com/en/MIT_Laboratory_for_Computer_Science) en 1984. Durant cette période le [MIT](https://www.wikiwand.com/en/MIT_Laboratory_for_Computer_Science), la [DEC](https://www.wikiwand.com/en/Digital_Equipment_Corporation) et [IBM](https://www.wikiwand.com/en/IBM) développait le **Projet Athena** à des fins éducatives. Cette plateforme permettrait aux étudiants d'avoir un accès partagé aux ressources informatiques. Elle avait donc besoin d'avoir une interface graphique indépendante pour fonctionner sur les différents systèmes (IBM vs DEC), tout en comportant un système de *fenêtres*.

Pour répondre à ce problème d'incompatibilité, les personnes en charges du projet ont créé un protocole qui permettait de lancer des applications en locales tout en appelant des ressources externes. De plus, à la même époque un protocole similaire existait déjà : le **W Window System**, qui était synchrone. *Bob Scheifler* du MIT s'est fortement inspiré de ce protocole, en le rendant asynchrone, X était né.

<div style="text-align: center">
    <img src="assets\X-Window-System.png"
        alt="wayland-schema" 
        width="600px" />
</div>

L'objectifs était donc de créer :
- Une **interface graphique** comportant un système de fenêtres
- fonctionnelle sur les différents systèmes et **indépendant du hardware**
- permettant une **intéraction avec les périphériques** (souris, clavier, écran tactile,...)

![Example interface graphique moderne](./assets/KDE_Plasma_5.16.png)

### Fonctionnement

### X server

### Libraries

### Limites

## Wayland

### Introduction

Wayland est un **protocole informatique** qui spécifie la communication entre un serveur d'affichage et ses clients, ainsi qu'une implémentation du protocole dans le langage de programmation C.

Le projet _Wayland_ développe également une interface de référence pour un compositeur Wayland qui se nomme Weston.

### Objectifs

L'objectif principal de Wayland est de remplacer le système X Window par un système de fenêtrage moderne, plus simple, sous Linux et d'autres systèmes d'exploitation de type Unix. Le code source du projet est publié selon les termes de la licence MIT, une licence de logiciel libre permissive.

### Fonctionnement

Le fonctionnement de _Wayland_ se fait entre 3 parties :

- **[KMS, evdev, Kernel]**
- **[Le compositeur de Wayland]**
- **[Les clients (programmes, applications)]**

<div style="text-align: center">
    <img src="assets\wayland-schema.png"
        alt="wayland-schema" />
</div>

Le noyau va obtenir des évènements provenant soit du _hardware_ soit des _inputs_ (clavier,etc...) et l'envoyer au compositeur. C'est le même fonctionnement qu'avec X Window, çela nous évite de redéfinir les pilotes d'entrée dans le noyau.

Le **compositeur** examine son **environnement** pour déterminer quelle fenêtre doit recevoir l'événement. L'environnement correspond à ce qui est à l'écran et le compositeur comprends les transformations qu'il peut appliquer aux éléments de l'environnement. Ainsi, le compositeur peut choisir la bonne fenêtre et transformer les coordonnées d'écran en coordonnées locales de fenêtre, en appliquant les transformations inverses. Les types de transformation qui peuvent être appliqués à une fenêtre sont uniquement limités à ce que le compositeur peut faire, tant qu'il peut calculer la transformation inverse pour les événements d'entrée.

Comme dans le cas X, lorsque le client reçoit l'événement, il met à jour l'interface utilisateur. Mais dans le cas de Wayland, le rendu se produit dans le client (**c.f : Rendu direct**), et le client envoie simplement une demande au compositeur pour indiquer la région qui a été mise à jour.
Le compositeur recueille les **demandes de dommages _(damage requests)_** de ses clients et recompose ensuite l'écran. Le compositeur peut alors directement émettre un **ioctl _(input-output control)_** pour planifier un saut de page avec **KMS**.

### Rendu direct

Avec le rendu direct, le client et le serveur partagent un tampon de mémoire vidéo. Le client se connecte à une bibliothèque de rendu comme **OpenGL** qui sait comment programmer le matériel et effectue le rendu directement dans la mémoire tampon. Le compositeur peut à son tour prendre la mémoire tampon et l'utiliser comme texture lorsqu'il compose le bureau. Après la configuration initiale, le client doit seulement indiquer au compositeur quel tampon utiliser et quand et où il a rendu le nouveau contenu dans celui-ci.

### Programmation

Il est possible de développer des compositeurs personalisés avec des lib tels que **Qt Wayland Compositor**.

Qt Wayland Compositor est un module qui fournit des **API QML et C++** pratiques et puissantes pour développer des compositeurs personnalisés basés sur le protocole Wayland. Rappelons que le **compositeur**, affiche le contenu des applications clientes qui supportent le protocole _Wayland_.

La philosophie de Wayland est de garder le protocole de base simple et minimal. Les développeurs peuvent ensuite développer ce protocole de base avec des extensions spécifiques à chaque cas d'utilisation. **Qt Wayland Compositor** prend en charge de nombreuses extensions courantes par défaut, et dispose également d'API permettant de créer de nouvelles extensions personnalisées.

Schéma plus détaillé pour mieux comprendre comment fonctionne le tout :

<img src="assets\wayland-schema-full.png"
     alt="wayland-schema"
     style="margin-left: 25%; width: 50%;" />

**Environnement de bureau supporté**

```
- GNOME 3.20+ (if specifically selected; X is used by default in Debian)
- KDE (Plasma 5.4)
- Enlightenment
- Hawaii
```

**Environnement de bureau non supporté**

```
- Cinnamon
- MATE
- XFCE
```

## Comparaison

Wayland permet une meilleure isolation entre les processus : une fenêtre ne peut pas accéder aux ressources d'une autre fenêtre, ni y injecter des frappes.

Wayland a également le potentiel d'être plus rapide, en réduisant la quantité de code entre les processus et le matériel, en déléguant beaucoup de choses aux processus eux-mêmes.

## Sources

- https://www.secjuice.com/wayland-vs-xorg/
- https://www.x.org/wiki/
- https://www.wikiwand.com/en/X.Org_Server
- https://www.wikiwand.com/en/X_Window_System
- https://wayland.freedesktop.org/