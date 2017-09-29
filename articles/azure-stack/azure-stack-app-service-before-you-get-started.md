---
title: "Avant de déployer App Service sur Azure Stack | Microsoft Docs"
description: "Étapes à effectuer avant de déployer App Service sur Azure Stack"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/3/2017
ms.author: anwestg
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 3cba11acc6279f24d0a47af8978610180724c0a2
ms.contentlocale: fr-fr
ms.lasthandoff: 09/15/2017

---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Avant de commencer avec App Service sur Azure Stack

Vous avez besoin de quelques éléments pour installer Azure App Service sur Azure Stack :

- Un déploiement terminé du [Kit de développement Azure Stack](azure-stack-run-powershell-script.md).
- Suffisamment d’espace dans votre système Azure Stack pour un petit déploiement d’App Service sur Azure Stack.  L’espace requis est d’environ 20 Go d’espace disque.
- Une image de machine virtuelle Windows Server à utiliser lorsque vous créez des machines virtuelles pour App Service sur Azure Stack.
- [Un serveur qui exécute SQL Server](#SQL-Server).

>[!NOTE] 
> Les étapes suivantes sont *toutes* exécutées sur l’ordinateur hôte Azure Stack.

Pour déployer un fournisseur de ressources, vous devez exécuter l’ISE PowerShell en tant qu’administrateur. Pour cette raison, vous devez autoriser les cookies et JavaScript dans le profil Internet Explorer que vous utilisez pour vous connecter à Azure Active Directory.

## <a name="turn-off-internet-explorer-enhanced-security"></a>Désactiver la sécurité renforcée d’Internet Explorer

1.  Connectez-vous sur la machine du Kit de développement Azure Stack en tant que **AzureStack/administrator**, puis ouvrez le **Gestionnaire de serveur**.

2.  Désactivez la **Configuration de sécurité renforcée d’Internet Explorer** pour les administrateurs et les utilisateurs.

3.  Connectez-vous sur la machine du Kit de développement Azure Stack en tant qu’administrateur, puis ouvrez le **Gestionnaire de serveur**.

4.  Désactivez la **Configuration de sécurité renforcée d’Internet Explorer** pour les administrateurs et les utilisateurs.

## <a name="enable-cookies"></a>Activer les cookies

1.  Sélectionnez **Démarrer** > **Toutes les applications** > **Accessoires Windows**. Cliquez avec le bouton droit sur **Internet Explorer** > **Plus** > **Exécuter en tant qu’administrateur**.

2.  Si vous y êtes invité, sélectionnez **Utiliser la sécurité recommandée**, puis sélectionnez **OK**.

3.  Dans Internet Explorer, sélectionnez **Outils** (icône d’engrenage) > **Options Internet** > **Confidentialité** > **Avancé**.

4.  Sélectionnez **Advanced (Avancé)**. Vérifiez que les deux cases **Accepter** sont cochées. Sélectionnez **OK** deux fois.

5.  Fermez Internet Explorer et redémarrez l’ISE PowerShell en tant qu’administrateur.

## <a name="install-powershell-for-azure-stack"></a>Installer PowerShell pour Azure Stack

Pour installer PowerShell pour Azure Stack, suivez les étapes sous [Installer PowerShell](azure-stack-powershell-install.md).

## <a name="use-visual-studio-with-azure-stack"></a>Utiliser Visual Studio avec Azure Stack

Pour utiliser Visual Studio avec Azure Stack, suivez les étapes sous [Installer Visual Studio](azure-stack-install-visual-studio.md).

## <a name="add-a-windows-server-2016-vm-image-to-azure-stack"></a>Ajouter une image de machine virtuelle Windows Server 2016 à Azure Stack

Étant donné qu’App Service déploie plusieurs machines virtuelles, il requiert une image de machine virtuelle Windows Server 2016 dans Azure Stack. Pour installer une image de machine virtuelle, suivez les étapes sous [Ajouter une image de machine virtuelle par défaut](azure-stack-add-default-image.md).

## <a name="SQL-Server"></a>SQL Server

App Service sur Azure Stack nécessite l’accès à une instance SQL Server pour créer et héberger deux bases de données afin d’exécuter le fournisseur de ressources App Service.  Si vous décidez de déployer une machine virtuelle SQL Server sur Azure Stack, son niveau de connectivité SQL doit être défini sur **Public**.  Vous pouvez choisir l’instance SQL Server à utiliser lorsque vous choisissez les options dans le programme d’installation App Service sur Azure Stack.

## <a name="next-steps"></a>Étapes suivantes

- [Installer le fournisseur de ressources App Service](azure-stack-app-service-deploy.md).

<!--Image references-->
[1]: ./media/azure-stack-app-service-before-you-get-started/PSGallery.png
[2]: ./media/azure-stack-app-service-before-you-get-started/WebPI_InstalledProducts.png

