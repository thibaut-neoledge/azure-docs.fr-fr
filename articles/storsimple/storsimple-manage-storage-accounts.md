---
title: "Gérer votre compte de stockage StorSimple | Microsoft Docs"
description: "Explique comment utiliser la page Configurer de StorSimple Manager pour ajouter, modifier ou supprimer les clés de sécurité d’un compte de stockage ou effectuer une rotation de ces clés."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 93207c40-e0eb-489e-8724-59fb94907081
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 4bf8b397d81e12bc48fe3d0ce16d5fff705cedbc
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-your-storage-account"></a>Utiliser le service StorSimple Manager pour gérer votre compte de stockage
> [!NOTE]
> Le portail Azure Classic pour StorSimple est déconseillé. Vos instances de StorSimple Device Manager seront automatiquement déplacées vers le nouveau portail Azure à la date de désapprobation planifiée. Vous serez prévenu de ce déplacement par un e-mail et une notification du portail. Ce document sera également bientôt retiré. Pour afficher la version de cet article applicable au nouveau portail Azure, accédez à [Utiliser le service StorSimple Manager pour gérer votre compte de stockage](storsimple-8000-manage-storage-accounts.md). Si vous avez des questions concernant le déplacement, consultez [FAQ : Déplacement vers le portail Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Vue d'ensemble
La page **Configurer** présente tous les paramètres de service globaux qui peuvent être créés dans le service StorSimple Manager. Ces paramètres peuvent être appliqués à tous les appareils connectés au service et incluent les éléments suivants :

* Comptes de stockage 
* Modèles de bande passante 
* Enregistrements de contrôle d’accès 

Ce didacticiel explique comment utiliser la page **Configurer** pour ajouter, modifier ou supprimer des comptes de stockage ou pour effectuer une rotation des clés de sécurité d’un compte de stockage.

 ![Page Configurer](./media/storsimple-manage-storage-accounts/HCS_ConfigureService.png)  

Les comptes de stockage contiennent les informations d’identification que l’appareil utilise pour accéder au compte de stockage que vous procure votre fournisseur de services cloud. Pour les comptes de stockage Microsoft Azure, il s’agit d’informations d’identification telles que le nom du compte et la clé d’accès primaire. 

Dans la page **Configurer** , tous les comptes de stockage créés pour l’abonnement à la facturation sont affichés sous forme de tableau et contiennent les informations suivantes :

* **Nom** : nom unique attribué au compte lors de sa création.
* **SSL activé** : permet d’indiquer si le protocole SSL est activé et si la communication appareil-cloud s’effectue sur le canal sécurisé.
* **Utilisé par** : nombre de volumes utilisant le compte de stockage.

Les tâches les plus courantes que vous pouvez effectuer sur les comptes de stockage dans la page **Configurer** sont les suivantes :

* Ajout d’un compte de stockage 
* Modification d’un compte de stockage 
* Suppression d'un compte de stockage 
* Rotation des clés de comptes de stockage 

## <a name="types-of-storage-accounts"></a>Types de compte de stockage
Vous pouvez utiliser trois types de compte de stockage avec votre appareil StorSimple.

* **Comptes de stockage générés automatiquement** : comme son nom l’indique, ce type de compte de stockage est généré automatiquement à la création du service. Pour en savoir plus sur la création de ces comptes de stockage, consultez l’[Étape 1 : création d’un nouveau service](storsimple-deployment-walkthrough-u1.md#step-1-create-a-new-service) dans [Déploiement de l’appareil local StorSimple](storsimple-deployment-walkthrough.md). 
* **Comptes de stockage de l’abonnement au service** : il s’agit des comptes de stockage Azure associés à l’abonnement au service. Pour en savoir plus sur la création de ces comptes de stockage, consultez la rubrique [À propos des comptes de stockage Azure](../storage/common/storage-create-storage-account.md). 
* **Comptes de stockage hors abonnement au service** : il s’agit des comptes de stockage Azure qui ne sont pas associés à votre service et existaient probablement avant sa création.

## <a name="add-a-storage-account"></a>Ajout d’un compte de stockage
Vous pouvez ajouter un compte de stockage en fournissant un nom convivial unique et les informations d’accès liées au compte de stockage (du fournisseur de services cloud spécifié). Vous avez également la possibilité d’activer le mode SSL (Secure Sockets Layer) afin de créer un canal sécurisé pour la communication réseau entre l’appareil et le cloud.

Vous pouvez créer plusieurs comptes pour un fournisseur de services cloud donné. Veuillez noter toutefois qu’une fois qu’un compte de stockage a été créé, vous ne pouvez plus modifier le fournisseur de services cloud.

Pendant l’enregistrement du compte de stockage, le service tente de communiquer avec votre fournisseur de services cloud. Les informations d’identification et les données d’accès que vous avez fournies sont alors authentifiées. Un compte de stockage est créé uniquement si l’authentification réussit. Si l’authentification échoue, un message d’erreur s’affiche.

Les comptes de stockage Resource Manager créés dans le portail Azure sont également pris en charge dans StorSimple. Les comptes de stockage Resource Manager ne s’affichent pas dans la liste déroulante de sélection lorsque vous tentez de créer un conteneur de volumes. Seuls les comptes de stockage créés dans le portail Azure Classic sont affichés. Les comptes de stockage Resource Manager doivent être ajoutés à l’aide de la procédure d’ajout d’un compte de stockage décrite ci-dessous.

> [!NOTE]
> La procédure d’ajout d’un compte de stockage varie en fonction de la version du logiciel StorSimple que vous utilisez. Veillez à suivre la procédure correspondant à la version de StorSimple que vous utilisez.
> 
> 

[!INCLUDE [add-a-storage-account-update1](../../includes/storsimple-configure-new-storage-account-u1.md)]

[!INCLUDE [add-a-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>Modification d’un compte de stockage
Vous pouvez modifier un compte de stockage utilisé par un conteneur de volumes. Si vous modifiez un compte de stockage actuellement utilisé, le seul champ modifiable est celui de la clé d’accès du compte de stockage. Vous pouvez saisir la nouvelle clé d’accès de stockage et enregistrer les paramètres mis à jour.

#### <a name="to-edit-a-storage-account"></a>Pour modifier un compte de stockage
1. Dans la page d’accueil du service, sélectionnez votre service, double-cliquez sur son nom, puis cliquez sur **Configurer**.
2. Cliquez sur **Ajouter/modifier des comptes de stockage**.
3. Dans la boîte de dialogue **Ajouter/modifier des comptes de stockage** , procédez comme suit :
   
   1. Dans la liste déroulante **Comptes de stockage**, choisissez un compte existant à modifier. Cette liste peut également comprendre les comptes de stockage qui ont été générés automatiquement lors de la création du service.
   2. Si nécessaire, vous pouvez modifier le réglage de l’option **Activer le mode SSL** .
   3. Vous pouvez choisir d’effectuer une rotation des clés d’accès de votre compte de stockage. Consultez la rubrique [Rotation des clés de comptes de stockage](#key-rotation-of-storage-accounts) pour plus d’informations sur la rotation des clés.
   4. Cliquez sur l’icône en forme de coche ![icône en forme de coche](./media/storsimple-manage-storage-accounts/HCS_CheckIcon.png) pour enregistrer les paramètres. Les paramètres de la page **Configurer** sont mis à jour. Cliquez sur **Enregistrer** pour enregistrer les paramètres que vous venez de modifier.
      
      ![Modification d’un compte de stockage](./media/storsimple-manage-storage-accounts/HCs_AddEditStorageAccount.png)

## <a name="delete-a-storage-account"></a>Suppression d'un compte de stockage
> [!IMPORTANT]
> Vous pouvez supprimer un compte de stockage uniquement s’il n’est pas utilisé par un conteneur de volumes. Si un compte de stockage est utilisé par un conteneur de volumes, supprimez d’abord ce dernier, puis le compte de stockage associé.
> 
> 

#### <a name="to-delete-a-storage-account"></a>Pour supprimer un compte de stockage
1. Dans la page d’accueil du service StorSimple Manager, sélectionnez votre service, double-cliquez sur son nom, puis cliquez sur **Configurer**.
2. Dans le tableau de comptes de stockage, placez le pointeur sur le compte à supprimer.
3. Une icône de suppression (**x**) s’affiche dans la colonne de droite en regard de ce compte de stockage. Cliquez sur l’icône **x** pour supprimer les informations d’identification.
4. Lorsque vous êtes invité à confirmer la suppression, cliquez sur **Oui** . La liste du tableau est mise à jour pour refléter les modifications.

## <a name="key-rotation-of-storage-accounts"></a>Rotation des clés de comptes de stockage
Pour des raisons de sécurité, les centres de données exigent souvent une rotation des clés. 

> [!NOTE]
> Les informations suivantes sur la rotation des clés et la procédure de rotation s’appliquent uniquement aux comptes de stockage Microsoft Azure. Si vous utilisez un autre fournisseur de services cloud, vous pouvez gérer les clés de compte de stockage via le tableau de bord de ce fournisseur.
> 
> 

Chaque abonnement Microsoft Azure peut être associé à un ou plusieurs comptes de stockage. L’accès à ces comptes est contrôlé par l’abonnement et les clés d’accès de chaque compte de stockage. 

Lorsque vous créez un compte de stockage, Microsoft Azure génère deux clés d’accès de stockage de 512 bits, qui servent à l’authentification lors de l’accès au compte de stockage. Grâce aux deux clés d’accès de stockage, vous pouvez régénérer les clés sans interrompre votre service de stockage ni l’accès à ce service. La clé actuellement utilisée est la clé *primaire* et la clé de sauvegarde est appelée clé *secondaire*. Vous devez fournir une de ces deux clés lorsque votre appareil Microsoft Azure StorSimple accède à votre fournisseur de services de stockage cloud.

## <a name="what-is-key-rotation"></a>La rotation des clés, de quoi s’agit-il ?
En règle générale, les applications utilisent une seule clé pour accéder à vos données. Après un certain temps, vous pouvez faire en sorte que vos applications utilisent la deuxième clé. Une fois que vos applications utilisent la clé secondaire, vous pouvez mettre la première clé hors service et générer une nouvelle clé. En utilisant les deux clés de cette manière, vous permettez à vos applications d’accéder aux données sans provoquer de temps d’arrêt.

Les clés de compte de stockage sont toujours stockées dans le service sous forme chiffrée. Toutefois, elles peuvent être réinitialisées via le service StorSimple Manager. Le service peut obtenir la clé primaire et la clé secondaire de tous les comptes de stockage d’un abonnement, y compris les comptes créés dans le service de stockage, ainsi que les comptes de stockage par défaut générés lors de la création du service StorSimple Manager. Le service StorSimple Manager obtient toujours ces clés à partir du portail Azure Classic et les stocke sous forme chiffrée.

## <a name="rotation-workflow"></a>Workflow de rotation
Un administrateur Microsoft Azure peut régénérer ou modifier la clé primaire ou secondaire en accédant directement au compte de stockage (via le service Microsoft Azure Storage). Le service StorSimple Manager ne voit pas automatiquement ce changement.

Pour informer le service StorSimple Manager de la modification, vous devez accéder au service StorSimple Manager et au compte de stockage, puis synchroniser la clé primaire ou secondaire (selon celle qui a été modifiée). Le service obtient alors la clé la plus récente, chiffre les clés, puis envoie la clé chiffrée à l’appareil.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service-azure-only"></a>Pour synchroniser les clés des comptes de stockage associés au même abonnement que le service (Azure uniquement)
1. Dans la page **Services**, cliquez sur l’onglet **Configurer**.
2. Cliquez sur **Ajouter/modifier des comptes de stockage**.
3. Dans la boîte de dialogue qui s’affiche, procédez comme suit :
   
   1. Sélectionnez le compte de stockage associé à la clé que vous souhaitez synchroniser. Les clés de compte de stockage sont affichées sous forme chiffrée.
   2. Dans le service StorSimple Manager, vous devez mettre à jour la clé que vous avez précédemment modifiée dans le service Microsoft Azure Storage. Si vous avez modifié (régénéré) la clé primaire, cliquez sur **Synchroniser la clé primaire**. Si vous avez modifié la clé secondaire, cliquez sur **Synchroniser la clé secondaire**.
      
      ![synchroniser les clés](./media/storsimple-manage-storage-accounts/HCS_KeyRotationStorageAccountSameSubscriptionAsService.png)

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Pour synchroniser les clés des comptes de stockage hors abonnement au service
1. Dans la page **Services**, cliquez sur l’onglet **Configurer**.
2. Cliquez sur **Ajouter/modifier des comptes de stockage**.
3. Dans la boîte de dialogue qui s’affiche, procédez comme suit :
   
   1. Sélectionnez le compte de stockage associé à la clé que vous souhaitez mettre à jour.
   2. Vous devrez mettre la clé d’accès de stockage à jour dans le service StorSimple Manager. Dans ce cas, vous pouvez voir la clé d’accès de stockage. Entrez la nouvelle clé dans le champ **Clé d’accès au compte de stockage**. 
   3. Enregistrez vos modifications. La clé d’accès de votre compte de stockage doit maintenant être à jour.

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur la [sécurité StorSimple](storsimple-security.md).
* En savoir plus sur [l’utilisation du service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).

