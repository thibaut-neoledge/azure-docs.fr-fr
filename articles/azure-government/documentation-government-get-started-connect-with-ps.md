---
title: "Azure Government Connect avec PowerShell | Microsoft Docs"
description: Informations sur la connexion de votre abonnement dans Azure Government avec PowerShell
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: 47e5e535-baa0-457e-8c41-f9fd65478b38
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: 3a0594fa639a2ac34c53b2263dbf7dd2410cf16a
ms.lasthandoff: 02/21/2017


---



# <a name="connect-to-azure-government-with-powershell"></a>Connexion à Azure Government avec PowerShell
Pour utiliser l’interface de ligne de commande Azure, vous devez vous connecter à Azure Government au lieu de la version publique d’Azure. L’interface de ligne de commande Azure peut servir à gérer un grand abonnement via un script ou d’accéder à des fonctionnalités qui ne sont pas actuellement disponibles dans le portail Azure. Si vous avez utilisé PowerShell dans Public Azure, tout est très similaire.  Les éléments qui diffèrent dans Azure Government sont les suivants :

* Connexion à votre compte
* Noms de régions

> [!NOTE]
> Si vous n’avez pas encore utilisé PowerShell, voir la [Présentation d’Azure PowerShell](/powershell/azureps-cmdlets-docs).
> 
> 

Lorsque vous démarrez PowerShell, vous devez indiquer à Azure PowerShell de se connecter à Azure Government en spécifiant un paramètre d’environnement.  Ce paramètre garantit que PowerShell se connecte aux points de terminaison corrects.  La collection de points de terminaison est déterminée lorsque vous vous connectez à votre compte.  Différentes API requièrent différentes versions du commutateur Environnement :

| Type de connexion | Commande |
| --- | --- |
| Commandes [Azure Active Directory (modèle de déploiement classique)](https://msdn.microsoft.com/library/dn708504.aspx) |`Add-AzureAccount -Environment AzureUSGovernment` |
| Commandes de [Gestion des ressources](https://msdn.microsoft.com/library/mt125356.aspx) |`Login-AzureRmAccount -EnvironmentName AzureUSGovernment` |
| Commandes d’[Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) |`Connect-MsolService -AzureEnvironment UsGovernment` |
| Commandes [Resource Manager (modèle de déploiement classique)](https://msdn.microsoft.com/library/azure/mt757189.aspx) |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` |
| [Ligne de commande d’interface de ligne de commande Azure](../xplat-cli-install.md) |`azure login –environment "AzureUSGovernment"` |

Vous pouvez également utiliser le commutateur Environnement lors de la connexion à un compte de stockage à l’aide de l’applet de commande New-AzureStorageContext et spécifier AzureUSGovernment.

### <a name="determining-region"></a>Détermination de la région
Une fois connecté, il existe une différence supplémentaire : les régions utilisées pour cibler un service.  Chaque cloud Azure a des régions différentes.  Celles-ci sont répertoriées sur la page relative à la disponibilité du service.  Vous utilisez normalement la région correspondant au paramètre Emplacement pour une commande.

Il y a un piège.  Les régions Azure Government ont un format différent de leurs noms courants :

| Nom courant | Commande |
| --- | --- |
| US Gov Virginia |USGov Virginia |
| US Gov Iowa |USGov Iowa |

> [!NOTE]
> Lors de l’utilisation du paramètre Location, Il ne peut pas y avoir d’espace entre US et Gov.
> 
> 

Si vous voulez valider les régions disponibles dans Azure Government, vous pouvez exécuter les commandes suivantes et imprimer la liste actuelle :

    Get-AzureLocation

Si vous êtes curieux de connaître les environnements disponibles dans Azure, vous pouvez exécuter la commande suivante :

    Get-AzureEnvironment

