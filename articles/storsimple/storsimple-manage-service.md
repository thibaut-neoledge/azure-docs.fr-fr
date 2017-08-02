---
title: "Déployer le service StorSimple Manager | Microsoft Docs"
description: "Explique comment créer et supprimer le service StorSimple Manager dans le portail Azure Classic et décrit comment gérer la clé d’inscription du service."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: bc1d5650-275c-42ed-bc77-cdb596f85943
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/14/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: ba3637a3a8b15b45c16bf5a00c1f4225bcfc5af8
ms.contentlocale: fr-fr
ms.lasthandoff: 07/14/2017

---
# <a name="deploy-the-storsimple-manager-service-in-the-azure-classic-portal"></a>Déploiement du service StorSimple Manager dans le portail Azure Classic

## <a name="overview"></a>Vue d’ensemble
Le service StorSimple Manager s’exécute dans Microsoft Azure et se connecte à plusieurs appareils StorSimple. Après avoir créé le service, vous pouvez l’utiliser pour gérer les appareils à partir du portail Microsoft Azure Classic s’exécutant dans un navigateur. Vous pouvez ainsi surveiller tous les appareils qui sont connectés au service StorSimple Manager à partir d’un emplacement central et unique, ce qui réduit la charge administrative.

La page d’accueil StorSimple Manager répertorie tous les services StorSimple Manager que vous pouvez utiliser pour gérer vos dispositifs de stockage StorSimple. Pour chaque service StorSimple Manager, les informations suivantes s’affichent sur la page StorSimple Manager :

* **Nom** : le nom affecté à votre service StorSimple Manager lors de sa création. **Impossible de modifier le nom du service une fois que le service a été créé. Cela vaut également pour les autres entités telles que les appareils, les volumes, les conteneurs de volumes et les stratégies de sauvegarde qui ne peut pas être renommés dans le portail Azure.**
* **État** : l’état du service, qui peut être **Actif**, **Création en cours** ou **En ligne**.
* **Emplacement** : l’emplacement géographique sur lequel l’appareil StorSimple sera déployé.
* **Abonnement** : l’abonnement de facturation associé à votre service.

Les tâches courantes qui peuvent être effectuées via la page StorSimple Manager sont les suivantes :

* Créer un service
* Supprimer un service
* Obtenir la clé d’inscription du service
* Régénérer la clé d’inscription du service

Le didacticiel explique comment effectuer chacune de ces tâches.

## <a name="create-a-service"></a>Créer un service
Utilisez l’option **Création rapide** pour créer un service StorSimple Manager si vous souhaitez déployer votre appareil StorSimple. Pour créer un service, vous avez besoin des éléments suivants :

* Un abonnement avec un contrat Entreprise
* Un compte de stockage Microsoft Azure actif
* Les informations de facturation utilisées pour la gestion des accès

Vous pouvez également choisir de générer un compte de stockage par défaut lorsque vous créez le service.

Un seul service peut gérer plusieurs appareils. Cependant, un appareil ne peut pas couvrir plusieurs services. Une grande entreprise peut avoir plusieurs instances de service pour utiliser différents abonnements, organisations ou même emplacements de déploiement. Veuillez noter que vous devez créer des instances distinctes du service StorSimple Manager pour gérer les appareils de la gamme StorSimple 8000 et les tableaux virtuels StorSimple.

> [!IMPORTANT] 
> Si vous disposez d’un service non utilisé (aucune opération d’appareil n’a été réalisée sur cette ressource) créé avant août 2016, celui-ci ne peut pas être géré via le portail Azure ou le portail Azure Classic. Nous vous recommandons de créer un nouveau service dans le portail Azure.

Procédez comme suit pour créer un service.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

## <a name="delete-a-service"></a>Supprimer un service
Avant de supprimer un service, assurez-vous qu’aucun appareil connecté ne l’utilise. Si le service est en cours d’utilisation, désactivez les appareils connectés. L’opération de désactivation rompt la connexion entre l’appareil et le service, mais conserve les données de l’appareil dans le cloud.

> [!IMPORTANT] 
> Après qu’un service a été supprimé, l’opération ne peut pas être annulée. Un appareil qui utilisait le service doit être réinitialisé aux paramètres d’usine avant de pouvoir être utilisé avec un autre service. Dans ce scénario, les données locales de l’appareil, ainsi que la configuration, seront perdues.

Pour supprimer un service, procédez comme suit.

### <a name="to-delete-a-service"></a>Pour supprimer un service
1. Dans la page **Service StorSimple Manager** , sélectionnez le service que vous souhaitez supprimer.
2. Cliquez sur **Supprimer** en bas de la page.
3. Cliquez sur **Oui** dans la notification de confirmation. La suppression du service peut nécessiter quelques minutes.

## <a name="get-the-service-registration-key"></a>Obtenir la clé d’inscription du service
Une fois que vous avez créé un service, vous devez inscrire votre appareil StorSimple auprès du service. Pour inscrire votre premier appareil StorSimple, vous avez besoin de la clé d’inscription du service. Pour inscrire des appareils supplémentaires avec un service StorSimple existant, vous avez besoin de la clé d’inscription et de la clé de chiffrement des données du service (générée sur le premier appareil lors de l’inscription). Pour plus d’informations sur la clé de chiffrement des données du service, consultez la rubrique [Sécurité StorSimple](storsimple-security.md). Vous pouvez obtenir la clé d’inscription en accédant à **Clé d’inscription** on the **Services** .

Procédez comme suit pour obtenir la clé d’inscription du service.

[!INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]

Conservez la clé d’inscription du service dans un emplacement sûr. Vous aurez besoin de cette clé, ainsi que de la clé de chiffrement des données du service, pour enregistrer des appareils supplémentaires auprès du service. Après avoir obtenu la clé d’inscription du service, vous devez configurer votre appareil via l’interface Windows PowerShell pour StorSimple.

Pour plus d’informations sur l’utilisation de la clé d’inscription, consultez [Étape 3 : configure et inscrire l’appareil via Windows PowerShell pour StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Régénérer la clé d’inscription du service
Vous devez régénérer une clé d’inscription du service si vous êtes amené à effectuer une rotation des clés ou si la liste des administrateurs du service a changé. Lorsque vous régénérez la clé, la nouvelle clé est utilisée uniquement pour l’enregistrement des appareils suivants. Les appareils déjà enregistrés ne sont pas affectés par ce processus.

Procédez comme suit pour régénérer une clé d’inscription du service.

### <a name="to-regenerate-the-service-registration-key"></a>Pour régénérer la clé d’inscription du service
1. Dans la page **Service StorSimple Manager**, cliquez sur **Clé d’inscription**.
2. Dans la boîte de dialogue **Clé d’inscription du service**, cliquez sur **Régénérer**.
3. Un message de confirmation s’affiche. Cliquez sur **OK** pour poursuivre la régénération.
4. Une nouvelle clé d’inscription du service s’affiche.
5. Copiez cette clé et sauvegardez-la pour enregistrer tout nouvel appareil auprès de ce service.
6. Cliquez sur l’icône de coche  ![Icône en forme de coche](./media/storsimple-manage-service/HCS_CheckIcon.png) pour fermer cette boîte de dialogue.

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur le [processus de déploiement StorSimple](storsimple-deployment-walkthrough-u2.md).
* En savoir plus sur la [gestion de votre compte de stockage StorSimple](storsimple-manage-storage-accounts.md).
* En savoir plus sur [l’utilisation du service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).

