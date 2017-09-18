---
title: Activer FTP dans App Service sur Azure Stack | Microsoft Docs
description: "Étapes à effectuer pour activer FTP dans App Service sur Azure Stack"
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
ms.date: 4/6/2017
ms.author: anwestg
ms.translationtype: HT
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: bab69a9ff7e101f22713ba7f1ac2cbd0add7fdbd
ms.contentlocale: fr-fr
ms.lasthandoff: 09/15/2017

---
# <a name="enable-ftp-in-app-service-on-azure-stack"></a>Activer FTP dans App Service sur Azure Stack

Une fois que vous avez correctement déployé App Service sur Azure Stack, si vous souhaitez activer la publication FTP afin que vos clients puissent télécharger leurs fichiers d’application et leur contenu, vous devez effectuer quelques étapes supplémentaires.  Ces étapes seront automatisées dans les prochaines versions.

> [!NOTE]
> Ces étapes sont destinées aux administrateurs Service ou Entreprise qui souhaitent configurer un fournisseur de ressources App Service ou Azure Stack.

## <a name="enable-ftp"></a>Activer FTP

1.  Connectez-vous au portail Azure Stack en tant qu’administrateur du service.
2.  Accédez à **Interfaces réseau** et sélectionnez **FTP-NIC** dans le **Groupe de ressources** - **AppService-LOCAL**. ![Interfaces réseau Azure Stack][1]
3.  Notez **l’adresse IP publique** de **FTP-NIC**. 
![Détails de l’interface réseau Azure Stack][2]
4.  Accédez ensuite à **Machines virtuelles** et sélectionnez **FTP0-VM**. ![Machines virtuelles Azure Stack][3]
5.  Ouvrez une session Bureau à distance sur la machine virtuelle à l’aide du bouton **Se connecter** et connectez-vous à la session en utilisant les informations d’identification de l’administrateur définies lors du déploiement d’App Service.  
![Détails des machines virtuelles Azure Stack][4]
6.  Ouvrez le **Gestionnaire des services IIS** sur la machine virtuelle FTP (FTP0-VM).
7.  Sous **Sites**, sélectionnez **Hosting FTP Site** (Site d’hébergement FTP).
8.  Ouvrez **Prise en charge du pare-feu FTP**. ![Gestionnaire des services IIS sur App Service FTP0-VM][5]
9.  Entrez l’adresse IP publique de FTP-NIC et cliquez sur **Appliquer**. ![Prise en charge du pare-feu FTP dans le gestionnaire des services IIS][6].

## <a name="validate-the-enabling-of-ftp"></a>Valider l’activation de FTP

1.  Connectez-vous au portail Azure Stack en tant qu’administrateur du service ou en tant que client.
2.  Accédez à **App Services** et sélectionnez un application Web, mobile ou API que vous avez créée. ![Services d’application][7]
3.  Dans les détails de l’application, notez le **Nom d’hôte FTP** et le **Nom d’utilisateur FTP/Déploiement**. ![Détails de l’application App Service][8]
> [!NOTE]
> Si vous ne voyez pas d’entrée sous **Nom d’utilisateur FTP/Déploiement**, vous devez d’abord définir les informations d’identification de déploiement à l’aide du panneau **Informations d’identification du déploiement**.

4.  Ouvrez l’Explorateur Windows, entrez le nom d’hôte FTP dans la barre d’adresse du fichier, par exemple, ftp://ftp.appservice.azurestack.local.
5.  Lorsque vous y êtes invité, entrez les **Informations d’identification du déploiement** notées à l’étape 3. Si la fonctionnalité a été activée, vous verrez une liste de répertoires comportant les contenus de l’application App Service. ![Liste de fichiers FTP][9]
<!--Image references-->
[1]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interfaces.png
[2]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interface-details.png
[3]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines.png
[4]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines-FTP0-VM.png
[5]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager.png
[6]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager-FTP-Firewall-Support.png
[7]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-services.png
[8]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-service-app-detail.png
[9]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-ftp-file-listing.png

