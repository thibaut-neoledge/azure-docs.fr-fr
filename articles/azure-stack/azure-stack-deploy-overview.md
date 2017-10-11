---
title: "Démarrage rapide du déploiement du Kit de développement Azure Stack | Microsoft Docs"
description: "En savoir plus sur le déploiement du Kit de développement Azure Stack"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/10/2017
ms.author: erikje
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 3d3f31775ae9384faeae20f108b71cea5dcc2934
ms.contentlocale: fr-fr
ms.lasthandoff: 09/28/2017

---
# <a name="azure-stack-development-kit-deployment-quickstart"></a>Démarrage rapide du déploiement du Kit de développement Azure Stack

*S’applique à : Kit de développement Azure Stack*

Le [Kit de développement Azure Stack](azure-stack-poc.md) est un environnement de développement et de test que vous pouvez déployer pour évaluer et présenter les fonctionnalités et services Azure Stack. Pour l’installer et le rendre opérationnel, vous devez préparer l’environnement matériel nécessaire et exécuter plusieurs scripts (cette opération peut prendre plusieurs heures). Une fois que vous aurez effectué ces étapes préalables, vous pourrez vous connecter aux portails de l’administrateur et de l’utilisateur pour gérer Azure Stack et tester des offres. 

1. [**Planifiez votre matériel, vos logiciels et votre réseau**](azure-stack-deploy.md). Vérifiez que l’ordinateur prévu pour héberger le Kit de développement (l’hôte du Kit de développement) a la configuration matérielle, logicielle et réseau requise. Vous devez également déterminer si vous allez utiliser Azure Active Directory ou les services de fédération Active Directory (AD FS). Avant de démarrer votre déploiement, assurez-vous que tous ces prérequis sont remplis pour que le processus d’installation s’exécute correctement. 

2. [**Téléchargez et extrayez le package de déploiement**](azure-stack-run-powershell-script.md#download-and-extract-the-development-kit). Vous pouvez télécharger le package de déploiement sur l’hôte du Kit de développement ou sur un autre ordinateur. Une fois extraits, les fichiers de déploiement occupent jusqu’à 60 Go d’espace disque. L’utilisation d’un autre ordinateur peut vous permettre d’avoir une configuration matérielle moins exigeante pour l’hôte du Kit de développement.

3. [**Préparez l’hôte du Kit de développement**](azure-stack-run-powershell-script.md#prepare-the-development-kit-host) en utilisant le programme d’installation. Après cette étape, l’hôte du Kit de développement démarrera sur Cloudbuilder.vhdx (disque dur virtuel qui contient un système d’exploitation démarrable et les fichiers d’installation d’Azure Stack).

4. [**Déployez le Kit de développement**](azure-stack-run-powershell-script.md#deploy-the-development-kit) sur l’hôte du Kit de développement.

5. Si votre déploiement Azure Stack utilise Azure Active Directory, vous devez [inscrire Azure Stack auprès d’Azure](azure-stack-register.md) pour pouvoir ensuite [télécharger des éléments de la Place de marché Azure](azure-stack-download-azure-marketplace-item.md) dans Azure Stack.

Après toutes ces étapes, l’environnement pour le Kit de développement est prêt, avec les portails de l’administrateur et de l’utilisateur. Vous pouvez maintenant [vous connecter et ouvrir une session](azure-stack-connect-azure-stack.md) sur le portail. Vous pouvez ensuite commencer à déployer des fournisseurs de ressources, créer des [offres](azure-stack-key-features.md#regions-services-plans-offers-and-subscriptions)et ajouter des éléments sur la [Place de marché](azure-stack-marketplace.md) Azure Stack.

