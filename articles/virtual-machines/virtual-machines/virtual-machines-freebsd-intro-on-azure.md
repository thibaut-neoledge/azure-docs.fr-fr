---
title: "Présentation FreeBSD dans Azure | Microsoft Docs"
description: "Apprenez à utiliser des machines virtuelles FreeBSD sur Azure"
services: virtual-machines-linux
documentationcenter: 
author: KylieLiang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 32b87a5f-d024-4da0-8bf0-77e233d1422b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/27/2016
ms.author: kyliel
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 0b6516003d93368896ddb7ecbf97cdd01cd75430


---
# <a name="introduction-to-freebsd-on-azure"></a>Présentation de FreeBSD sur Azure
Cette rubrique offre une vue d’ensemble de l’exécution d’une machine virtuelle FreeBSD dans Azure.

## <a name="overview"></a>Vue d'ensemble
FreeBSD pour Microsoft Azure est un système d’exploitation informatique avancé utilisé sur les serveurs, ordinateurs de bureau et plateformes intégrées modernes. L’image de FreeBSD 10.3 est fournie par Microsoft Corporation et disponible dans Azure. Elle est basée sur la version FreeBSD 10.3 et l’agent invité de machine virtuelle Azure [2.1.4](https://github.com/Azure/WALinuxAgent/releases/tag/v2.1.4) est installé. L’agent est responsable de la communication entre la machine virtuelle FreeBSD et Azure Fabric pour les opérations telles que l’approvisionnement de la machine virtuelle à la première utilisation (nom d’utilisateur, mot de passe, nom d’hôte, etc.) et l’activation de fonctionnalités pour certaines extensions de machines virtuelles.
Comme pour les versions futures de FreeBSD, la stratégie consiste à rester à jour et à rendre disponibles les dernières versions dès qu’elles sont publiées par l’équipe d’ingénieurs de lancement de FreeBSD. La prochaine version est [FreeBSD 11](https://www.freebsd.org/releases/11.0R/schedule.html).

## <a name="deploying-a-freebsd-virtual-machine"></a>Déploiement d’une machine virtuelle FreeBSD
Le déploiement d’une machine virtuelle FreeBSD est un processus simple qui fait appel à une image provenant [d’Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/).

## <a name="vm-extensions-for-freebsd"></a>Extension de machine virtuelle pour FreeBSD
Voici les extensions de machines virtuelles prises en charge par FreeBSD.

### <a name="vmaccess"></a>VMAccess
L’extension [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) peut :

* Réinitialiser le mot de passe de l’utilisateur sudo d’origine.
* Créer un nouvel utilisateur sudo avec le mot de passe spécifié.
* Définir une clé hôte publique donnée.
* Réinitialiser la clé hôte publique fournie pendant l’approvisionnement de la machine virtuelle si la clé hôte n’est pas fournie.
* Ouvrir le port SSH (22) et restaurer sshd_config si reset_ssh a la valeur True.
* Supprimer l’utilisateur existant.
* Vérifier les disques.
* Réparer un disque ajouté.

### <a name="customscript"></a>CustomScript
L’extension [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) peut :

* Télécharger les scripts personnalisés dans le stockage Azure ou un stockage public externe (par exemple, GitHub) s’ils sont fournis.
* Exécuter le script de point d’entrée.
* Prendre en charge les commandes inline.
* Convertir automatiquement le saut de ligne Windows dans des scripts Shell et Python.
* Supprimer automatiquement les nomenclatures dans des scripts Shell et Python.
* Protéger les données sensibles dans CommandToExecute.

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Authentification : noms d’utilisateurs, mots de passe et clés SSH
Lorsque vous créez une machine virtuelle FreeBSD avec le Portail Azure, vous devez fournir un nom d’utilisateur, un mot de passe ou une clé publique SSH.
Le nom d’utilisateur pour le déploiement d’une machine virtuelle FreeBSD sur Azure ne doit pas correspondre au nom des comptes système (UID < 100) déjà présents sur la machine virtuelle (« root », par exemple).
Actuellement, seule la règle RSA SSH est prise en charge. La clé SSH multiligne doit commencer par « ---- BEGIN SSH2 PUBLIC KEY ---- » et finir par « ---- END SSH2 PUBLIC KEY ---- ».

## <a name="obtaining-superuser-privileges"></a>Obtention de privilèges de superutilisateur
Le compte utilisateur qui est spécifié pendant le déploiement de l'instance de machine virtuelle dans Azure est un compte privilégié. Le package de « sudo » a été installé dans l’image FreeBSD publiée.
Une fois connecté avec ce compte utilisateur, vous pouvez exécuter les commandes en tant que root avec la syntaxe de commande.

    # sudo <COMMAND>

Vous pouvez éventuellement obtenir un interpréteur de commandes root avec sudo -s.

## <a name="next-steps"></a>Étapes suivantes
* Accédez à [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/) pour créer une machine virtuelle FreeBSD.
* Si vous souhaitez utiliser votre propre FreeBSD dans Azure, consultez [Créer et charger un VHD FreeBSD dans Azure](../virtual-machines-linux-classic-freebsd-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).




<!--HONumber=Nov16_HO3-->


