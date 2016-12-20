---
title: "Modifier le mot de passe administrateur de l’appareil virtuel StorSimple | Microsoft Docs"
description: Explique comment utiliser le portail Azure Classic ou l&quot;interface utilisateur web StorSimple Virtual Array pour modifier le mot de passe administrateur.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 52acde45-ae29-4edb-9377-46918ab7eef8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/17/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 85fe4dae6abffebd9d68f3b4110dd45338f35bef


---
# <a name="change-the-storsimple-virtual-array-device-administrator-password"></a>Modifier le mot de passe administrateur de l’appareil StorSimple Virtual Array
## <a name="overview"></a>Vue d'ensemble
Quand vous utilisez l’interface Windows PowerShell pour accéder à l’appareil virtuel StorSimple, vous devez entrer un mot de passe d’administrateur de l’appareil. Lorsque l'appareil StorSimple est d'abord configuré puis démarré, le mot de passe par défaut est *Password1*. Pour garantir la sécurité de vos données, le mot de passe par défaut expire dès votre première connexion et vous devez donc le modifier.

Vous pouvez également utiliser l'interface utilisateur web locale ou le portail Azure Classic pour modifier à tout moment le mot de passe administrateur une fois l'appareil déployé dans votre environnement de production. Chacune de ces procédures est décrite dans cet article.

## <a name="use-the-azure-classic-portal-to-change-the-password"></a>Utilisez le portail Azure Classic pour modifier le mot de passe.
Procédez comme suit pour modifier le mot de passe d’administrateur de l’appareil via le portail Azure Classic.

#### <a name="to-change-the-device-administrator-password-via-the-azure-classic-portal"></a>Pour modifier le mot de passe administrateur de l’appareil via le portail Azure Classic
1. Dans le portail, cliquez sur **Appareils** > **Configuration** pour votre appareil.
2. Faites défiler l’écran vers le bas jusqu’à la section **Mot de passe de l’administrateur de l’appareil** . Indiquez un mot de passe Administrateur contenant entre 8 et 15 caractères. Le mot de passe doit contenir une combinaison de caractères en majuscules, en minuscules, numériques et spéciaux.
3. Confirmez le mot de passe.
4. Cliquez sur **Enregistrer** au bas de la page.

Le mot de passe Administrateur d’appareil doit maintenant être à jour. Vous pouvez utiliser ce mot de passe modifié pour accéder à l’appareil localement.

## <a name="use-the-storsimple-virtual-array-web-ui-to-change-the-password"></a>Utilisez l'interface utilisateur web de StorSimple Virtual Array pour modifier le mot de passe
Procédez comme suit pour modifier le mot de passe d’administrateur de l’appareil via l’interface utilisateur web locale.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Pour modifier le mot de passe administrateur de l’appareil via l’interface utilisateur web locale
1. Dans l'interface utilisateur web locale, cliquez sur **Maintenance** > **Modifier le mot de passe** pour votre appareil.
   
    ![change password1](./media/storsimple-ova-change-device-admin-password/image40.png)
2. Entrez le **mot de passe actuel**.
3. Fournissez un **nouveau mot de passe**. Le mot de passe doit comporter au moins 8 caractères. Il doit contenir 3 caractères sur 4 en majuscules, minuscules, chiffres et caractères spéciaux.
   
    Notez que votre mot de passe ne peut pas être identique aux 24 derniers mots de passe.
4. Entrez de nouveau le mot de passe pour le confirmer.
   
    ![change password2](./media/storsimple-ova-change-device-admin-password/image41.png)
5. En bas de cette page, cliquez sur **Appliquer**. Le nouveau mot de passe est alors appliqué. Si la modification du mot de passe n'a pas réussie, vous verrez l'erreur suivante.
   
    ![password error](./media/storsimple-ova-change-device-admin-password/image42.png)
   
    Vous serez averti une fois le mot de passe correctement mis à jour. Vous pouvez ensuite utiliser ce mot de passe modifié pour accéder à l’appareil localement.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la [gestion de votre StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).




<!--HONumber=Nov16_HO3-->


