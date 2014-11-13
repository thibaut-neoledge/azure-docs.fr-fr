<properties urlDisplayName="Install software on VM" pageTitle="Installation de logiciels sur une machine virtuelle&nbsp;Linux - Azure" metaKeywords="" description="Apprenez &agrave; installer des logiciels sur votre machine virtuelle&nbsp;Linux dans&nbsp;Azure en utilisant&nbsp;CentOS/Red Hat ou&nbsp;Ubuntu." metaCanonical="" services="virtual-machines" documentationCenter="" title="Installation de logiciels sur une machine virtuelle Linux dans Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Installation de logiciels sur une machine virtuelle Linux dans Azure

Les distributions Linux tendent à livrer les logiciels sous forme de « packages » pour les installer. Ces packages sont en général gérés à l'aide d'un ensemble de commandes, telles que `apt` ou `yum`. Vous pouvez également installer des programmes sans package, avec, par exemple, un *tarball* du code source.

Nous allons présenter l'utilisation des gestionnaires de package de certaines distributions Linux courantes. Les étapes peuvent différer en fonction de la distribution Linux utilisée.

**Remarque :** en fonction de la configuration de votre environnement, ces commandes peuvent nécessiter d'être exécutées en utilisant des privilèges racine (via `sudo`).

## CentOS/Red Hat

CentOS est livré avec `yum` pour la gestion des packages. Cet outil permet d'installer, de désinstaller, de mettre à jour, d'afficher la liste des packages installés, et bien plus. La syntaxe des commandes correspondantes est présentée ci-après.

### Installation

Cette commande installe un package, ainsi que les packages dont il dépend. En raison des dépendances, plusieurs packages peuvent être installés.

    yum install [package name]

### Désinstallation

Cette commande désinstalle un package de votre machine. Gardez à l'esprit qu'elle ne supprime aucune dépendance.

    yum remove [package name]

### Mise à jour

Cette commande met à jour un package à la dernière version. Le package doit être installé pour pouvoir être mis à jour.

    yum update [package name]

### Affichage de la liste des packages installés

Cette commande affiche une liste de tous les packages installés sur votre machine.

    yum list installed

## Ubuntu

Ubuntu est livré avec `apt` (Advanced Packaging Tool ou gestionnaire de paquets) pour la gestion des packages. Cet outil permet d'installer, de désinstaller, de mettre à jour, d'afficher la liste des packages installés, et bien plus. La syntaxe des commandes correspondantes est présentée ci-après.

### Installation

Cette commande installe un package, ainsi que les packages dont il dépend. En raison des dépendances, plusieurs packages peuvent être installés.

    apt-get install [package name]

### Désinstallation

Cette commande désinstalle un package de votre machine. Gardez à l'esprit qu'elle ne supprime aucune dépendance.

    apt-get remove [package name]

### Mise à jour/Mise à niveau

Pour effectuer une mise à niveau vers une nouvelle version, vous devez utiliser deux commandes : l'une pour mettre à jour l'index de votre package et l'autre pour mettre à niveau les packages. Exécutez la commande suivante pour mettre à jour l'index de votre package :

    apt-get update

Une fois l'index à jour, exécutez la commande suivante pour mettre à niveau les packages :

    apt-get upgrade
