---
title: "Fonctionnalités d’Azure Cloud Shell (préversion) | Microsoft Docs"
description: "Vue d’ensemble des fonctionnalités d’Azure Cloud Shell"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 3414957a67d67603fdb597b3715a902e8cc3c5bd
ms.contentlocale: fr-fr
ms.lasthandoff: 07/18/2017

---

# <a name="features-and-tools-for-azure-cloud-shell"></a>Fonctionnalités et outils pour Azure Cloud Shell
Azure Cloud Shell est une expérience shell sur navigateur de gestion et de développement des ressources Azure.

Cloud Shell offre une expérience shell, préconfigurée et accessible par le biais d’un navigateur, de gestion des ressources Azure qui dispense de la surcharge associée à l’installation, au contrôle de version et à la maintenance d’un ordinateur.

Cloud Shell approvisionne les machines à la demande ; par conséquent, leur état n’est pas persistant d’une session à l’autre. Cloud Shell étant conçu pour les sessions interactives, les shells s’arrêtent automatiquement après 20 minutes d’inactivité.

## <a name="bash-in-cloud-shell"></a>Bash dans Cloud Shell
### <a name="tools"></a>Outils
|Catégorie   |Nom   |
|---|---|
|Interpréteur de commandes Linux|Bash<br> sh               |
|Outils Azure            |[Azure CLI 2.0](https://github.com/Azure/azure-cli) et [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AZCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Lot chantier](https://github.com/Azure/batch-shipyard)     |
|Éditeurs de texte           |vim<br> nano<br> emacs       |
|Contrôle de code source         |git                    |
|Outils de génération            |make<br> maven<br> npm<br> pip         |
|Conteneurs             |[Docker CLI](https://github.com/docker/cli)/[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Draft](https://github.com/Azure/draft)<br> [CLI DC/OS](https://github.com/dcos/dcos-cli)         |
|Bases de données              |Client MySQL<br> Client PostgreSQL<br> [Utilitaire sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)      |
|Autres                  |Client iPython<br> [CLI Cloud Foundry](https://github.com/cloudfoundry/cli)<br> |

### <a name="language-support"></a>Support multilingue
|language   |Version   |
|---|---|
|.NET       |1.01       |
|Go         |1.7        |
|Java       |1.8        |
|Node.js    |6.9.4      |
|Python     |2.7 et 3.5 (par défaut)|

## <a name="secure-automatic-authentication"></a>Authentification automatique sécurisée
Cloud Shell authentifie automatiquement et en toute sécurité l’accès au compte pour Azure CLI 2.0.

## <a name="azure-files-persistence"></a>Persistance Azure Files
Cloud Shell étant alloué à la demande à l’aide d’un ordinateur temporaire, les fichiers qui ne se trouvent pas dans $Home et l’état de l’ordinateur ne sont pas persistants d’une session à l’autre.
Pour conserver les fichiers entre les sessions, Cloud Shell vous guide à travers le processus d’association d’un partage de fichiers Azure au premier lancement.
Par la suite, Cloud Shell associera automatiquement votre espace de stockage pour toutes les sessions à venir.

[En savoir plus sur l’association de partages de fichiers Azure à Cloud Shell.](persisting-shell-storage.md)

## <a name="next-steps"></a>Étapes suivantes
[Démarrage rapide de Cloud Shell](quickstart.md) <br>
[En savoir plus sur Azure CLI 2.0](https://docs.microsoft.com/cli/azure/) <br>
