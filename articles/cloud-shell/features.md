---
title: "Fonctionnalités d’Azure Cloud Shell (préversion) | Microsoft Docs"
description: "Vue d’ensemble des fonctionnalités d’Azure Cloud Shell"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: e789283ab9b25f634c50b341ca882bbf9c70a225
ms.contentlocale: fr-fr
ms.lasthandoff: 05/17/2017

---

# <a name="features--tools-for-azure-cloud-shell"></a>Fonctionnalités et outils pour Azure Cloud Shell
Azure Cloud Shell est une expérience shell sur navigateur de gestion et de développement des ressources Azure.

Cloud Shell offre une expérience shell, préconfigurée et accessible par le biais d’un navigateur, de gestion des ressources Azure qui dispense de la surcharge associée à l’installation, au contrôle de version et à la maintenance d’un ordinateur.

Cloud Shell approvisionne les machines à la demande ; par conséquent, leur état n’est pas persistant d’une session à l’autre. Cloud Shell étant conçu pour les sessions interactives, les shells s’arrêtent automatiquement après 10 minutes d’inactivité.

## <a name="tools"></a>Outils
|Catégorie   |Nom   |
|---|---|
|Interpréteur de commandes Linux|Bash<br> sh               |
|Outils Azure            |Azure CLI 2.0 et 1.0     |
|Éditeurs de texte           |vim<br> nano<br> emacs       |
|Contrôle de code source         |git                    |
|Outils de génération            |make<br> maven<br> npm<br> pip         |
|Conteneurs             |Docker<br> Kubectl<br> DC/OS CLI         |
|Bases de données              |Client MySQL<br> Client PostgreSQL<br> Utilitaire sqlcmd      |
|Autres                  |Client iPython |

## <a name="language-support"></a>Support multilingue
|language   |Version   |
|---|---|
|.NET       |1.01       |
|Go         |1.7        |
|Node.js    |6.9.4      |
|Python     |2.7 et 3.5|

## <a name="secure-automatic-authentication"></a>Authentification automatique sécurisée
Cloud Shell authentifie automatiquement et en toute sécurité l’accès au compte pour Azure CLI 2.0.

## <a name="azure-files-persistence"></a>Persistance Azure Files
Cloud Shell étant alloué à la demande à l’aide d’un ordinateur temporaire, les fichiers locaux qui ne se trouvent pas dans $Home et l’état de l’ordinateur ne sont pas persistants d’une session à l’autre.
Pour conserver les fichiers entre les sessions, Cloud Shell vous guide à travers le processus d’association d’un partage de fichiers Azure au premier lancement.
Par la suite, Cloud Shell associera automatiquement votre espace de stockage pour toutes les sessions à venir.

[En savoir plus sur l’association de partages de fichiers Azure à Cloud Shell](persisting-shell-storage.md).

## <a name="next-steps"></a>Étapes suivantes
[Démarrage rapide de Cloud Shell](quickstart.md) 
[En savoir plus sur Azure CLI 2.0](https://docs.microsoft.com/cli/azure/)
