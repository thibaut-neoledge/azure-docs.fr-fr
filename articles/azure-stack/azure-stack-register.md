---
title: "Inscrire Azure Stack | Microsoft Docs"
description: "Inscrire Azure Stack auprès de votre abonnement Azure."
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
ms.topic: article
ms.date: 09/25/2017
ms.author: erikje
ms.translationtype: HT
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: 13ac0afb9ebfe568f1d8b1db2fedfd28cb05c3e7
ms.contentlocale: fr-fr
ms.lasthandoff: 09/27/2017

---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Inscrire Azure Stack auprès de votre abonnement Azure

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Pour les déploiements Azure Active Directory, vous pouvez inscrire [Azure Stack](azure-stack-poc.md) auprès d’Azure pour télécharger des éléments de la Place de Marché à partir d’Azure et configurer la génération de rapports de données commerciales envoyés à Microsoft. 

> [!NOTE]
>L’inscription est recommandée, car elle vous permet de tester des fonctionnalités Azure Stack importantes, telles que la syndication de la Place de Marché et les rapports d’utilisation. Après avoir inscrit Azure Stack, l’utilisation est signalée à Azure Commerce. Vous pouvez la consulter sous l’abonnement utilisé pour l’inscription. Toute utilisation que les utilisateurs du kit de développement Azure Stack signalent ne leur sera pas facturée.
>


## <a name="get-azure-subscription"></a>Prendre un abonnement Azure

Avant d’inscrire Azure Stack auprès d’Azure, vous devez disposer des éléments suivants :

- L’ID d’abonnement d’un abonnement Azure. Pour obtenir l’ID, connectez-vous à Azure, cliquez sur **Plus de services** > **Abonnements**, cliquez sur l’abonnement que vous voulez utiliser. Sous **Éléments principaux** vous trouverez alors l’**ID d’abonnement**. Les abonnements au cloud du gouvernement de Chine, d’Allemagne et des États-Unis ne sont pas pris en charge pour le moment.
- Le nom d’utilisateur et le mot de passe d’un compte qui est un propriétaire de l’abonnement (les comptes MSA/2FA sont pris en charge)
- L’annuaire Azure Active Directory de l’abonnement Azure. Pour trouver cet annuaire dans Azure, pointez sur votre avatar situé en haut à droite dans le portail Azure. 
- Le fournisseur de ressources Azure Stack inscrit (pour plus d’informations, consultez la section **Inscrire un fournisseur de ressources Azure Stack** ci-dessous)

Si vous n’avez pas d’abonnement Azure répondant à ces exigences, vous pouvez [créer un compte Azure gratuit ici](https://azure.microsoft.com/en-us/free/?b=17.06). L’inscription d’Azure Stack n’entraîne aucun frais sur votre abonnement Azure.



## <a name="register-azure-stack-resource-provider-in-azure"></a>Inscrire un fournisseur de ressources Azure Stack dans Azure
> [!NOTE] 
> Cette étape ne doit être effectuée qu’une seule fois dans un environnement Azure Stack.
>

1. Démarrez PowerShell ISE en tant qu’administrateur.
2. Connectez-vous au compte Azure qui est un propriétaire de l’abonnement Azure avec le paramètre -EnvironmentName défini sur « AzureCloud ».
3. Inscrivez le fournisseur de ressources Azure « Microsoft.AzureStack ».

Exemple : 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack -Force
```


## <a name="register-azure-stack-with-azure"></a>Inscrire Azure Stack auprès d’Azure

> [!NOTE]
>Toutes les étapes suivantes doivent être effectuées sur l’ordinateur hôte.
>

1. [Installez PowerShell pour Azure Stack](azure-stack-powershell-install.md). 
2. Copiez le [script RegisterWithAzure.ps1](https://go.microsoft.com/fwlink/?linkid=842959) dans un dossier (tel que C:\Temp).
3. Démarrez PowerShell ISE en tant qu’administrateur.    
4. Exécutez le script RegisterWithAzure.ps1, en remplaçant les espaces réservés suivants :
    - *YourAccountName* est le propriétaire de l’abonnement Azure
    - *YourID* est l’ID d’abonnement Azure que vous voulez utiliser pour inscrire Azure Stack
    - *YourDirectory* est le nom de votre locataire Azure Active Directory dont fait partie votre abonnement Azure.

    ```powershell
    RegisterWithAzure.ps1 -azureSubscriptionId YourID -azureDirectoryTenantName YourDirectory -azureAccountId YourAccountName
    ```
    
    Par exemple :
    
    ```powershell
    C:\temp\RegisterWithAzure.ps1 -azureSubscriptionId "5e0ae55d-0b7a-47a3-afbc-8b372650abd3" `
    -azureDirectoryTenantName "contoso.onmicrosoft.com" `
    -azureAccountId serviceadmin@contoso.onmicrosoft.com
    ```
    
5. Aux deux invites, appuyez sur Entrée.
6. Dans la fenêtre de connexion indépendante, entrez les informations d’identification de votre abonnement Azure.

## <a name="verify-the-registration"></a>Vérifier l’inscription

1. Connectez-vous au portail d’administration (https://adminportal.local.azurestack.external).
2. Cliquez sur **Plus de services** > **Gestion de la Place de Marché** > **Ajouter à partir d’Azure**.
3. Si une liste d’éléments disponibles dans Azure (tels que WordPress) s’affiche, l’activation a réussi.

## <a name="next-steps"></a>Étapes suivantes

[Se connecter à Azure Stack](azure-stack-connect-azure-stack.md)


