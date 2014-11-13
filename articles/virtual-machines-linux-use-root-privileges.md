<properties urlDisplayName="Use root privileges" pageTitle="Utilisation des privil&egrave;ges root sur les machines virtuelles&nbsp;Linux dans Azure" metaKeywords="" description="Apprenez &agrave; utiliser les privil&egrave;ges root sur une machine virtuelle&nbsp;Linux dans&nbsp;Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Utilisation des privil&egrave;ges root sur les machines virtuelles Linux dans Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Utilisation des privilèges root sur les machines virtuelles Linux dans Azure

Les utilisateurs peuvent exécuter des commandes avec des privilèges élevés sur une machine virtuelle Linux à l'aide de la commande `sudo`. Toutefois, l'expérience peut varier en fonction du mode de déploiement du système.

1.  **Clé SSH et mot de passe ou mot de passe uniquement** : la machine virtuelle a été déployée soit avec un certificat (fichier `.CER`) et un mot passe, soit avec un nom d'utilisateur et un mot de passe uniquement. Dans ce cas, `sudo` demande le mot de passe de l'utilisateur avant d'exécuter la commande.

2.  **Clé SSH uniquement** : la machine virtuelle a été déployée avec un certificat (fichier `.cer` ou `.pem`), mais sans mot de passe. Dans ce cas, `sudo` **ne demande pas** le mot de passe de l'utilisateur avant d'exécuter la commande.

## Clé SSH et mot de passe ou mot de passe uniquement

Connectez-vous à la machine virtuelle Linux à l'aide de l'authentification par clé SSH ou par mot de passe, puis exécutez les commandes à l'aide de `sudo`, par exemple :

    # sudo <command>
    [sudo] password for azureuser:

Dans ce cas, l'utilisateur est invité à fournir un mot de passe. Une fois le mot de passe entré, `sudo` exécute la commande avec les privilèges `root`.

## Clé SSH uniquement

Connectez-vous à la machine virtuelle Linux à l'aide de l'authentification par clé SSH, puis exécutez les commandes à l'aide de `sudo`, par exemple :

    # sudo <command>

Dans ce cas, l'utilisateur **n'est pas** invité à fournir un mot de passe. Lorsque vous appuyez sur `<enter>`, `sudo` exécute la commande avec les privilèges `root`.

