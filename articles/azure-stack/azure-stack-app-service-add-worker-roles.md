---
title: "Augmenter la taille des instances des rôles de travail dans App Services - Azure Stack  | Microsoft Docs"
description: "Instructions détaillées pour la mise à l’échelle d’App Services Azure Stack"
services: azure-stack
documentationcenter: 
author: kathm
manager: slinehan
editor: anwestg
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/6/2017
ms.author: kathm
ms.translationtype: HT
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: f844658c6ad2529fd385476be63095bdae7c88e5
ms.contentlocale: fr-fr
ms.lasthandoff: 09/15/2017

---
# <a name="app-service-on-azure-stack-adding-more-worker-roles"></a>App Service sur Azure Stack : ajouter davantage de rôles de travail 

Ce document fournit des instructions sur la mise à l’échelle d’App Service sur les rôles de travail Azure Stack. Il contient les étapes de création de rôles de travail supplémentaires pour prendre en charge des applications de n’importe quelle taille.

> [!NOTE]
> Si votre environnement POC Azure Stack n’a pas plus de 96 Go de RAM, l’ajout de capacité supplémentaire pourrait se révéler difficile.

App Service sur Azure Stack, par défaut, prend en charge les niveaux Worker gratuits et partagés. Pour ajouter d’autres niveaux Worker, vous devez ajouter davantage de rôles de travail.

Si vous n’êtes pas sûr de ce qui a été déployé avec l’installation App Service sur Azure Stack par défaut, vous pouvez consulter des informations supplémentaires dans la [vue d’ensemble d’App Service sur Azure Stack](azure-stack-app-service-overview.md).

Il existe deux façons d’ajouter de la capacité supplémentaire à App Service sur Azure Stack :
1.  [Ajouter des Workers supplémentaires directement à partir de l’administrateur du fournisseur de ressources App Service](#Add-additional-workers-directly-from-within-the-App-Service-Resource-Provider-Admin).
2.  [Créer manuellement des machines virtuelles supplémentaires et les ajouter au fournisseur de ressources App Service](#Create-additional-VMs-manually-and-add-them-to-the-App-Service-Resource-Provider).

## <a name="add-additional-workers-directly-within-the-app-service-resource-provider-admin"></a>Ajouter des Workers supplémentaires directement à partir de l’administrateur du fournisseur de ressources App Service.

1.  Connectez-vous au portail Azure Stack en tant qu’administrateur du service.
2.  Accédez à **Fournisseurs de ressources** et sélectionnez **App Service Resource Provider Admin** (Administrateur du fournisseur de ressources App Service). ![Fournisseurs de ressources Azure Stack][1]
3.  Cliquez sur **Rôles**.  La répartition de tous les rôles App Service déployés s’affiche ici.
4.  Cliquez sur l’option **Nouvelle instance de rôle** ![Ajouter une nouvelle instance de rôle][2]
5.  Dans le panneau **Nouvelle instance de rôle** :
    1. Choisissez le nombre **d’instances de rôle** supplémentaires à ajouter.  Dans la préversion, le maximum est de 10.
    2. Sélectionnez le **type de rôle**.  Dans cette préversion, cette option est limitée à Web Worker.
    3. Sélectionnez le **niveau Worker** dans lequel vous voulez déployer ce Worker. Les choix par défaut sont Petit, Moyen, Grand ou Partagé.  Si vous avez créé vos propres niveaux Worker, ceux-ci sont également disponibles pour la sélection.
    4. Cliquez sur **OK** pour déployer les Workers supplémentaires
6.  App Service sur Azure Stack ajoute ensuite les machines virtuelles supplémentaires, les configure, installe tous les logiciels requis et les marque comme Prêt lorsque ce processus est terminé.  Ce processus peut prendre environ 80 minutes.
7.  Vous pouvez surveiller la progression de la préparation des nouveaux Workers en les affichant dans le panneau des **rôles**.

>[!NOTE]
>  Dans cette préversion, le flux Nouvelle instance de rôle intégré est limité aux Rôles de travail et déploie des machines virtuelles de taille A1 uniquement.  Nous développerons cette fonctionnalité dans une version ultérieure.

## <a name="manually-adding-additional-capacity-to-app-service-on-azure-stack"></a>Ajouter manuellement de la capacité supplémentaire à App Service sur Azure Stack.

Les étapes suivantes sont nécessaires pour ajouter des rôles supplémentaires :

1. [Créer une machine virtuelle](#step-1-create-a-new-vm-to-support-the-new-instance-size)
2. [Configurer la machine virtuelle](#step-2-configure-the-virtual-machine)
3. [Configurer le rôle Web Worker dans le portail Azure Stack](#step-3-configure-the-web-worker-role-in-the-azure-stack-portal)
4. [Configurer des plans App Service](#step-4-configure-app-service-plans)

## <a name="step-1-create-a-new-vm-to-support-the-new-instance-size"></a>Étape 1 : Créer une machine virtuelle pour prendre en charge la nouvelle taille d’instance
Créez une machine virtuelle, comme décrit dans [cet article](azure-stack-provision-vm.md), en vous assurant que les sélections suivantes sont effectuées :

* Nom d’utilisateur et mot de passe : fournissez les mêmes nom d’utilisateur et mot de passe que ceux fournis lors de l’installation d’App Service sur Azure Stack.
* Abonnement : utilisez l’abonnement du fournisseur par défaut.
* Groupe de ressources : choisissez **AppService-LOCAL**.

> [!NOTE]
> Stockez les machines virtuelles pour les rôles de travail dans le même groupe de ressources que celui où App Service sur Azure Stack est déployé. (Cela est recommandé pour cette version.)
> 
> 

## <a name="step-2-configure-the-virtual-machine"></a>Étape 2 : Configurer la machine virtuelle
Une fois le déploiement terminé, la configuration suivante est obligatoire pour prendre en charge le rôle Web Worker :

1. Accédez au groupe de ressources **AppService-LOCAL** dans le portail et sélectionnez la nouvelle machine créée à l’Étape 1.
2. Cliquez sur Se connecter dans le panneau de la machine virtuelle pour télécharger le profil Bureau à distance.  Ouvrez le profil pour ouvrir une session Bureau à distance vers votre machine virtuelle.
3. Connectez-vous à la machine virtuelle à l’aide du nom d’utilisateur et du mot de passe spécifiés à l’Étape 1.
4. Ouvrez PowerShell en cliquant sur le bouton **Démarrer** et en tapant PowerShell. Cliquez avec le bouton droit sur **PowerShell.exe**, puis sélectionnez **Exécuter en tant qu’administrateur** pour ouvrir PowerShell en mode administrateur.
5. Copiez et collez chacune des commandes suivantes (une à la fois) dans la fenêtre PowerShell et appuyez sur Entrée :
   
   ```netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes```
   ```netsh advfirewall firewall set rule group="Windows Management Instrumentation (WMI)" new enable=yes```
   ```reg add HKLM\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\system /v LocalAccountTokenFilterPolicy /t REG\_DWORD /d 1 /f```
   
6. Fermez votre session Bureau à distance.
7. Redémarrez la machine virtuelle à partir du portail.

> [!NOTE]
> Il s’agit de la configuration minimale requise pour App Service sur Azure Stack. Il s’agit des paramètres par défaut de l’image Windows 2012 R2 incluse avec Azure Stack. Les instructions ont été fournies pour référence ultérieure et pour ceux qui utilisent une autre image.
> 
> 

## <a name="step-3-configure-the-worker-role-in-the-azure-stack-portal"></a>Étape 3 : Configurer le rôle de travail dans le portail Azure Stack
1. Ouvrez le portail en tant qu’administrateur du service sur **ClientVM**.
2. Accédez à **Fournisseurs de ressources** &gt; **Administrateur du fournisseur de ressources App Service**.![Administrateur du fournisseur de ressources App Service][3]
3. Dans le panneau des paramètres, cliquez sur **Rôles**.![Rôles du fournisseur de ressources App Service][4]
4. Cliquez sur **Ajouter une instance de rôle**.
5. Dans la zone de texte **Nom du serveur**, entrez **l’adresse IP** du serveur créé précédemment (dans la Section 1).
6. Sélectionnez le **Type de rôle** à ajouter : Contrôleur, Serveur d’administration, Frontal, Web Worker, Serveur de publication ou Serveur de fichiers.  Dans cette instance, sélectionnez Web Worker.
7. Cliquez sur le **Niveau** dans lequel déployer la nouvelle instance (Petit, Moyen, Grand ou Partagé).  Si vous avez créé vos propres niveaux Worker, ceux-ci sont également disponibles pour la sélection.
8. Cliquez sur **OK**.
9. Revenez à la vue **Rôles**
10. Cliquez sur la ligne correspondant à la combinaison Type de rôle et Niveau Worker à laquelle votre machine virtuelle a été affectée.
11. Recherchez le nom du serveur que vous venez d’ajouter. Passez en revue la colonne d’état et attendez que l’état affiche « Prêt » avant de passer à l’étape suivante. Ceci peut prendre environ 80 minutes. ![Rôle Fournisseur de ressources App Service prêt][5]

## <a name="step-4-configure-app-service-plans"></a>Étape 4 : Configurer des plans App Service

1. Connectez-vous au portail sur la machine virtuelle cliente.
2. Accédez à **Nouveau** &gt; **Web et Mobile**.
3. Sélectionnez le type d’application que vous souhaitez déployer.
4. Fournissez les informations sur l’application, puis sélectionnez **Plan App Service / Emplacement**.
    1. Cliquez sur **Create New**.
    2. Créez votre plan en sélectionnant le niveau tarifaire correspondant.

> [!NOTE]
> Dans ce panneau, vous pouvez créer plusieurs plans. Avant de déployer, toutefois, assurez-vous d’avoir sélectionné le plan approprié.
> 
> 

Voici un exemple des différents niveaux tarifaires disponibles par défaut.  Vous remarquerez que s’il n’existe aucun Worker disponible pour un niveau Worker spécifique, l’option permettant de choisir le niveau tarifaire correspondant est indisponible.![Niveaux tarifaires par défaut App Service sur Azure Stack][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-add-worker-roles/azure-stack-resource-providers.png
[2]: ./media/azure-stack-app-service-add-worker-roles/app-service-new-role-instance.png
[3]: ./media/azure-stack-app-service-add-worker-roles/app-service-resource-provider-admin.png
[4]: ./media/azure-stack-app-service-add-worker-roles/app-service-resource-provider-roles.png
[5]: ./media/azure-stack-app-service-add-worker-roles/app-service-resource-provider-role-ready.png
[6]: ./media/azure-stack-app-service-add-worker-roles/app-service-resource-provider-pricing-tier.png

