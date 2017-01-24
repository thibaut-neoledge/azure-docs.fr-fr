---
title: Abonnements Azure Government | Microsoft Docs
description: Informations sur la gestion de votre abonnement dans Azure Government
services: Azure-Government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
editor: 
ms.assetid: d3375e84-a37d-4e44-9040-70dbe08eabfc
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/21/2016
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 398abc29328adc179b860ab2cde5e6122f81779d


---
# <a name="managing-and-connecting-to-your-subscription-in-azure-government"></a>Connexion à votre abonnement dans Azure Government et gestion de celui-ci
Azure Government offre des URL et points de terminaison uniques pour la gestion de votre environnement. Il est important d’utiliser les connexions appropriées pour gérer votre environnement via le portail ou PowerShell. Une fois que vous êtes connecté à l’environnement Azure Government, les opérations normales pour la gestion d’un service fonctionnent si le composant a été déployé.

## <a name="connecting-via-the-portal"></a>Connexion via le portail
Le portail est le principal vecteur via lequel la plupart des gens se connectent à Azure Government.  Pour vous connecter, accédez au portail via la page [https://portal.azure.us](https://portal.azure.us).  L’ancienne version du portail Azure est accessible via la page [https://manage.windowsazure.us](https://manage.windowsazure.us).

Vous pouvez créer des abonnements pour votre compte en vous connectant à [https://account.windowsazure.us](https://account.windowsazure.us).

## <a name="connecting-via-powershell"></a>Connexion via PowerShell
Que vous utilisiez Azure PowerShell pour gérer un abonnement volumineux à l’aide de scripts, ou accédiez à des fonctionnalités actuellement indisponibles sur le portail Azure, vous devez vous connecter à Azure Government plutôt qu’à Public Azure.  Si vous avez utilisé PowerShell dans Public Azure, tout est très similaire.  Les éléments qui diffèrent dans Azure Government sont les suivants :

* Connexion à votre compte
* Noms de régions

> [!NOTE]
> Si vous n’avez pas encore utilisé PowerShell, voir la [Présentation d’Azure PowerShell](/powershell/azureps-cmdlets-docs).
> 
> 

Lorsque vous démarrez PowerShell, vous devez indiquer à Azure PowerShell de se connecter à Azure Government en spécifiant un paramètre d’environnement.  Ce paramètre garantit que PowerShell se connecte aux points de terminaison corrects.  La collection de points de terminaison est déterminée lorsque vous vous connectez à votre compte.  Différentes API requièrent différentes versions du commutateur Environnement :

| Type de connexion | Commande |
| --- | --- |
| Commandes de [Gestion des services](https://msdn.microsoft.com/library/dn708504.aspx) |`Add-AzureAccount -Environment AzureUSGovernment` |
| Commandes de [Gestion des ressources](https://msdn.microsoft.com/library/mt125356.aspx) |`Login-AzureRmAccount -EnvironmentName AzureUSGovernment` |
| Commandes d’[Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) |`Connect-MsolService -AzureEnvironment UsGovernment` |
| [Commande Azure Active Directory v2](https://msdn.microsoft.com/library/azure/mt757189.aspx) |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` |
| [Ligne de commande d’interface de ligne de commande Azure](../xplat-cli-install.md) |`azure login –environment "AzureUSGovernment"` |

Vous pouvez également utiliser le commutateur Environnement lors de la connexion à un compte de stockage à l’aide de l’applet de commande New-AzureStorageContext et spécifier AzureUSGovernment.

### <a name="determining-region"></a>Détermination de la région
Une fois connecté, il existe une différence supplémentaire : les régions utilisées pour cibler un service.  Chaque cloud Azure a des régions différentes.  Celles-ci sont répertoriées sur la page relative à la disponibilité du service.  Vous utilisez normalement la région correspondant au paramètre Emplacement pour une commande.

Il y a un piège.  Le format des régions dans Azure Government diffère de leurs noms courants :

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

## <a name="connecting-via-visual-studio"></a>Connexion via Visual Studio
Visual Studio est utilisé par les développeurs pour gérer facilement leurs abonnements Azure lors de la création de solutions.  Visual Studio ne vous permet actuellement pas de configurer une connexion à Azure Government dans l’interface utilisateur.  

### <a name="updating-visual-studio-for-azure-government"></a>Mise à jour de Visual Studio pour Azure Government
Pour permettre à Visual Studio de se connecter à Azure Government, vous devez mettre à jour le registre.

1. Fermez Visual Studio
2. Créez un fichier texte nommé **VisualStudioForAzureGov.reg**
3. Copiez et collez le texte suivant dans **VisualStudioForAzureGov.reg** :
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"="https://login-us.microsoftonline.com/"
        "adaluri"="https://management.core.usgovcloudapi.net"
        "AzureRMEndpoint"="https://management.usgovcloudapi.net"
        "AzureRMAudienceEndpoint"="https://management.core.usgovcloudapi.net"
        "EnableAzureRMIdentity"="true"
        "GraphUrl"="graph.windows.net"
4. Enregistrez le fichier, puis exécutez-le en double-cliquant dessus.  Vous êtes invité à fusionner le fichier dans votre registre.
5. Démarrez Visual Studio et commencez à utiliser [Cloud Explorer](../vs-azure-tools-resources-managing-with-cloud-explorer.md)

> [!NOTE]
> Une fois cette clé de registre définie, seuls les abonnements Azure Government sont accessibles.  Les abonnements que vous avez configurés précédemment sont toujours visibles mais ils ne fonctionnent pas, car Visual Studio est maintenant connecté à Azure Government au lieu d’Azure Public.  Consultez la section suivante pour connaître les étapes permettant d’annuler les modifications.
> 
> 

### <a name="reverting-visual-studio-connection-to-azure-government"></a>Suppression de la connexion de Visual Studio à Azure Government
Pour permettre à Visual Studio de se connecter à Azure Public, vous devez supprimer les paramètres du registre qui permettent la connexion à Azure Government.

1. Fermez Visual Studio
2. Créez un fichier texte nommé **VisualStudioForAzureGov_Remove.reg**
3. Copiez et collez le texte suivant dans **VisualStudioForAzureGov_Remove.reg** :
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"=-
        "adaluri"=-
        "AzureRMEndpoint"=-
        "AzureRMAudienceEndpoint"=-
        "EnableAzureRMIdentity"=-
        "GraphUrl"=-
4. Enregistrez le fichier, puis exécutez-le en double-cliquant dessus.  Vous êtes invité à fusionner le fichier dans votre registre.
5. Démarrez Visual Studio

> [!NOTE]
> Une fois cette clé de registre modifiée, vos abonnements Azure Government s’affichent mais ne sont pas accessibles.  Ils peuvent être supprimés en toute sécurité.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, voir :

* [Documents PowerShell sur GitHub](https://github.com/Azure/azure-powershell)
* [Instructions pas à pas sur la connexion à Gestion des ressources](https://blogs.msdn.microsoft.com/azuregov/2015/10/08/configuring-arm-on-azure-gc/)
* [Documents Azure PowerShell sur MSDN](https://msdn.microsoft.com/library/mt619274.aspx)

Pour obtenir des informations supplémentaires et des mises à jour, inscrivez-vous au [blog Microsoft Azure Government](https://blogs.msdn.microsoft.com/azuregov/)




<!--HONumber=Dec16_HO2-->


