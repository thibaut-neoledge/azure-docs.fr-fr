---
title: "Gérer les enregistrements de contrôle d’accès pour le StorSimple Virtual Array | Microsoft Docs"
description: "Décrit comment gérer les enregistrements de contrôle d’accès pour déterminer les hôtes qui peuvent se connecter à un volume sur le StorSimple Virtual Array."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 11252938-5b97-4178-8c37-f58eaa3d00b1
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 037da7a5647f5b3d2b0cf1364fab050406be3b62


---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records-for-the-storsimple-virtual-array"></a>Utiliser le service StorSimple Manager pour gérer les enregistrements de contrôle d’accès pour le StorSimple Virtual Array
## <a name="overview"></a>Vue d'ensemble
Les enregistrements de contrôle d’accès vous permettent de spécifier les hôtes qui peuvent se connecter à un volume sur le StorSimple Virtual Array (également appelé appareil virtuel local StorSimple). Les enregistrements de contrôle d’accès sont définis pour un volume spécifique et contiennent les noms complets iSCSI (IQN) des ordinateurs hôtes. Quand un hôte essaie de se connecter à un volume, l’appareil vérifie l’enregistrement de contrôle d’accès associé à ce volume pour le nom IQN et, s’il existe une correspondance, la connexion est établie. La section des **enregistrements de contrôle d’accès** de la page **Configurer** affiche tous les enregistrements de contrôle d’accès avec les noms complets iSCSI (IQN) des hôtes correspondants.

Le didacticiel décrit les tâches courantes liées aux enregistrements de contrôle d’accès :

* Obtenir le nom IQN
* Ajouter un enregistrement de contrôle d’accès 
* Modifier un enregistrement de contrôle d’accès 
* Supprimer un enregistrement de contrôle d’accès 

> [!IMPORTANT]
> * Lorsque vous attribuez un enregistrement de contrôle d’accès à un volume, veillez à ce que plusieurs hôtes non cluster n’accèdent pas simultanément au volume, sans quoi celui-ci pourrait être endommagé. 
> * Lorsque vous supprimez un enregistrement de contrôle d’accès d’un volume, assurez-vous que l’hôte correspondant n’accède pas au volume, car la suppression pourrait entraîner une perturbation des opérations de lecture-écriture.
> 
> 

## <a name="get-the-iqn"></a>Obtenir le nom IQN
Procédez comme suit pour obtenir le nom IQN d’un hôte Windows exécutant Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Ajouter un enregistrement de contrôle d’accès
Utilisez la page **Configuration** du service StorSimple Manager pour ajouter des enregistrements de contrôle d’accès. En général, vous associez un enregistrement de contrôle d’accès à un volume.

Pour plus d’informations sur l’association d’un ACR à un volume, consultez la page [Ajouter un volume](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Lorsque vous attribuez un enregistrement de contrôle d’accès à un volume, veillez à ce que plusieurs hôtes non cluster n’accèdent pas simultanément au volume, sans quoi celui-ci pourrait être endommagé.
> 
> 

Effectuez les opérations suivantes pour ajouter un enregistrement de contrôle d’accès.

#### <a name="to-add-an-acr"></a>Pour ajouter un enregistrement de contrôle d’accès
1. Dans la page d’accueil du service, sélectionnez votre service, double-cliquez sur son nom, puis cliquez sur l’onglet **Configuration** .
   
    ![Onglet Configuration](./media/storsimple-ova-manage-acrs/acr1.png)
2. Dans le tableau sous **Enregistrements de contrôle d’accès**, attribuez un **Nom** à votre enregistrement de contrôle d’accès.
3. Sous **Nom d’initiateur iSCSI**, indiquez le nom IQN de votre hôte Windows. 
4. En bas de la page, cliquez sur **Enregistrer** pour enregistrer l’enregistrement de contrôle d’accès ainsi créé. Le message de confirmation suivant doit normalement s’afficher.
   
    ![Message de confirmation](./media/storsimple-ova-manage-acrs/acr2.png)
5. Cliquez sur l’icône en forme de coche  ![icône en forme de coche](./media/storsimple-ova-manage-acrs/check-icon.png). La liste de la table est mise à jour pour refléter les modifications.

## <a name="edit-an-acr"></a>Modifier un enregistrement de contrôle d’accès
Utilisez la page **Configuration** du portail Azure Classic pour modifier les enregistrements de contrôle d’accès. 

> [!NOTE]
> Vous devez modifier uniquement les enregistrements de contrôle d’accès qui ne sont pas en cours d’utilisation. Pour modifier un enregistrement de contrôle d’accès associé à un volume en cours d’utilisation, vous devez d’abord placer le volume hors connexion.
> 
> 

Effectuez les opérations suivantes pour modifier un enregistrement de contrôle d’accès.

#### <a name="to-edit-an-acr"></a>Pour modifier un enregistrement de contrôle d’accès
1. Dans la page d’accueil du service, sélectionnez votre service, double-cliquez sur son nom, puis cliquez sur l’onglet **Configuration** .
2. Dans la liste de la table des enregistrements de contrôle d’accès, pointez sur l’enregistrement que vous souhaitez modifier.
3. Attribuez un nouveau nom et/ou nom IQN à l’enregistrement de contrôle d’accès.
4. Pour enregistrer l’enregistrement de contrôle d’accès modifié, cliquez sur **Enregistrer** en bas de la page. Un message de confirmation s’affiche. 
5. Cliquez sur l’icône en forme de coche  ![icône en forme de coche](./media/storsimple-ova-manage-acrs/check-icon.png). La liste de la table est mise à jour pour refléter la modification.

## <a name="delete-an-access-control-record"></a>Supprimer un enregistrement de contrôle d’accès
Utilisez la page **Configuration** du portail Azure Classic pour supprimer des enregistrements de contrôle d’accès. 

> [!NOTE]
> * Vous devez uniquement supprimer les enregistrements de contrôle d’accès qui ne sont pas en cours d’utilisation. Pour supprimer un enregistrement de contrôle d’accès associé à un volume en cours d’utilisation, vous devez d’abord placer le volume hors connexion.
> * Lorsque vous supprimez un enregistrement de contrôle d’accès d’un volume, assurez-vous que l’hôte correspondant n’accède pas au volume, car la suppression pourrait entraîner une perturbation des opérations de lecture-écriture.
> 
> 

Pour supprimer un enregistrement de contrôle d’accès, procédez comme suit.

#### <a name="to-delete-an-access-control-record"></a>Pour supprimer un enregistrement de contrôle d’accès
1. Dans la page d’accueil du service, sélectionnez votre service, double-cliquez sur son nom, puis cliquez sur l’onglet **Configuration** .
2. Dans la liste de la table des enregistrements de contrôle d’accès, pointez sur l’enregistrement que vous souhaitez supprimer.
3. Une icône de suppression (**x**) apparaît dans la colonne la plus à droite, en regard de l’enregistrement de contrôle d’accès que vous sélectionnez. Cliquez sur l’icône **x** pour supprimer l’enregistrement de contrôle d’accès. Le message de confirmation suivant doit normalement s’afficher.
   
    ![Message de confirmation](./media/storsimple-ova-manage-acrs/acr3.png)
4. Cliquez sur l’icône en forme de coche  ![icône en forme de coche](./media/storsimple-ova-manage-acrs/check-icon.png). La liste de la table est mise à jour pour refléter la suppression.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [ajouter des volumes et configurer les enregistrements de contrôle d’accès](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).




<!--HONumber=Nov16_HO3-->


