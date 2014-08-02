<properties linkid="manage-linux-commontasks-install-software" urlDisplayName="Install software on VM" pageTitle="Install software on a Linux virtual machine - Azure" metaKeywords="" description="Learn how to install software on your Linux virtual machine in Azure by using CentOS/Red Hat or Ubuntu." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install software on your Linux virtual machine in Azure" authors="" solutions="" manager="" editor="" />

Installation de logiciels sur une machine virtuelle Linux dans Azure
====================================================================

Les distributions Linux tendent à livrer les logiciels sous forme de « packages » pour les installer. Ces packages sont en général gérés à l'aide d'un ensemble de commandes, telles que `apt` ou `yum`. Vous pouvez également installer des programmes sans package, avec, par exemple, un *tarball* du code source.

Nous allons présenter l'utilisation des gestionnaires de package de certaines distributions Linux courantes. Les étapes peuvent différer en fonction de la distribution Linux utilisée.

**Remarque :** en fonction de la configuration de votre environnement, ces commandes peuvent nécessiter d'être exécutées en utilisant des privilèges racine (via `sudo`).

CentOS/Red Hat
--------------

CentOS est livré avec `yum` pour la gestion des packages. Cet outil permet d'installer, de désinstaller, de mettre à jour, d'afficher la liste des packages installés, et bien plus. La syntaxe des commandes correspondantes est présentée ci-après.

### Installation

Cette commande installe un package, ainsi que les packages dont il dépend. En raison des dépendances, plusieurs packages peuvent être installés.

    yum install [nom du package]

### Désinstallation

Cette commande désinstalle un package de votre machine. Gardez à l'esprit qu'elle ne supprime aucune dépendance.

    yum remove [nom du package]

### Mise à jour

Cette commande met à jour un package à la dernière version. Le package doit être installé pour pouvoir être mis à jour.

    yum update [nom du package]

### Affichage de la liste des packages installés

Cette commande affiche une liste de tous les packages installés sur votre machine.

    yum list installed

Ubuntu
------

Ubuntu est livré avec `apt` (Advanced Packaging Tool ou gestionnaire de paquets) pour la gestion des packages. Cet outil permet d'installer, de désinstaller, de mettre à jour, d'afficher la liste des packages installés, et bien plus. La syntaxe des commandes correspondantes est présentée ci-après.

### Installation

Cette commande installe un package, ainsi que les packages dont il dépend. En raison des dépendances, plusieurs packages peuvent être installés.

    apt-get install [nom du package]

### Désinstallation

Cette commande désinstalle un package de votre machine. Gardez à l'esprit qu'elle ne supprime aucune dépendance.

    apt-get remove [nom du package]

### Mise à jour/Mise à niveau

Pour effectuer une mise à niveau vers une nouvelle version, vous devez utiliser deux commandes : l'une pour mettre à jour l'index de votre package et l'autre pour mettre à niveau les packages. Exécutez la commande suivante pour mettre à jour l'index de votre package :

    apt-get update

Une fois l'index à jour, exécutez la commande suivante pour mettre à niveau les packages :

    apt-get upgrade
