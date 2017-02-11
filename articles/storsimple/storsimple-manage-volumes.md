---
title: "Gérer vos volumes StorSimple | Microsoft Docs"
description: "Explique comment ajouter, modifier, analyser et supprimer des volumes StorSimple et comment les mettre hors connexion si nécessaire."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: ccabd859-590c-4568-a81d-6ff38f125be3
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/11/2016
ms.author: v-sharos
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2b79492914bb52e970343a2e829622122f03642b


---
# <a name="use-the-storsimple-manager-service-to-manage-volumes"></a>Utiliser le service StorSimple Manager pour gérer les volumes
[!INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## <a name="overview"></a>Vue d’ensemble
Ce didacticiel explique comment utiliser le service StorSimple Manager pour créer et gérer des volumes sur l’appareil StorSimple et l’appareil virtuel StorSimple.

Le service StorSimple Manager est une extension du portail Azure Classic qui vous permet de gérer votre solution StorSimple à partir d’une seule interface Web. Outre la gestion des volumes, vous pouvez utiliser le service StorSimple Manager pour créer et gérer les services StorSimple, afficher et gérer les appareils, afficher les alertes, ainsi que pour afficher et gérer les stratégies de sauvegarde et le catalogue de sauvegarde.

> [!NOTE]
> Azure StorSimple peut créer uniquement des volumes alloués dynamiquement. Vous ne pouvez pas créer de volumes entièrement ou partiellement alloués sur un système Azure StorSimple.
> 
> L’allocation dynamique est une technologie de virtualisation dans laquelle le stockage disponible semble dépasser les ressources physiques. Plutôt que de réserver un espace de stockage suffisant à l’avance, Azure StorSimple utilise l’allocation dynamique afin d’allouer juste assez d’espace pour répondre aux besoins actuels. La nature élastique du stockage cloud simplifie cette approche, car Azure StorSimple peut augmenter ou diminuer le stockage cloud pour répondre aux demandes changeantes.
> 
> 

## <a name="the-volumes-page"></a>Page Volumes
La page **Volumes** vous permet de gérer les volumes de stockage alloués sur l’appareil Microsoft Azure StorSimple pour vos initiateurs (serveurs). Elle comprend la liste des volumes de votre appareil StorSimple.

 ![Page volumes](./media/storsimple-manage-volumes/HCS_VolumesPage.png)

Un volume est constitué d’une série d’attributs :

* **Nom** : nom descriptif qui doit être unique et vous aide à identifier le volume. Ce nom est également utilisé dans les rapports d’analyse lorsque vous filtrez sur un volume particulier.
* **État** : peut être en ligne ou hors connexion. Si un volume est hors connexion, il n’est pas visible pour les initiateurs (serveurs) qui sont autorisés à l’utiliser.
* **Capacité** : indique la taille du volume, telle qu’elle est perçue par l’initiateur (serveur). La capacité correspond au volume total de données qui peut être stocké par l’initiateur (serveur). Les volumes sont alloués dynamiquement et les données dédupliquées. Cela suppose que l’appareil ne préalloue pas de capacité de stockage physique localement ou sur le cloud en fonction de la capacité de volume configurée. La capacité du volume est allouée et utilisée à la demande.
* **Type** : indique le type de volume pouvant être hiérarchisé ou archivé (un sous-type du volume hiérarchisé)
* **Accès** : indique les initiateurs (serveurs) autorisés à accéder à ce volume. Les initiateurs qui ne sont pas membres de l’enregistrement de contrôle d’accès (ACR) associé au volume ne voient pas le volume.
* **Analyse** : indique si un volume est ou non en cours d’analyse. Par défaut, l’analyse est activée au moment de la création du volume. Toutefois, elle est désactivée pour un volume cloné. Pour activer l’analyse pour un volume, suivez les instructions données dans Analyse d’un volume.

Les tâches les plus courantes associées à un volume sont les suivantes :

* Ajout d’un volume
* Modification d’un volume
* Suppression d’un volume
* Mise hors connexion d’un volume
* Analyse d’un volume

## <a name="add-a-volume"></a>Ajout d’un volume
Vous [avez créé un volume](storsimple-deployment-walkthrough-u1.md#step-6-create-a-volume) lors du déploiement de votre solution StorSimple. La procédure d’ajout d’un volume est similaire.

### <a name="to-add-a-volume"></a>Pour ajouter un volume
1. Dans la page **Appareils**, sélectionnez l’appareil, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs de volumes**.
2. Sélectionnez un conteneur de volumes et cliquez sur la flèche de la ligne correspondante pour accéder aux volumes associés au conteneur.
3. Cliquez sur **Ajouter** en bas de la page. L’Assistant Ajouter un volume démarre.
   
     ![Assistant Ajout de volume, paramètres de base](./media/storsimple-manage-volumes/AddVolume1.png)
4. Dans l’Assistant Ajouter un volume, sous **Paramètres de base**, procédez comme suit :
   
   1. Saisissez un **nom** pour le volume.
   2. Indiquez la **capacité allouée** au volume en Go ou To. La capacité doit être comprise entre 1 Go et 64 To pour un appareil physique. La capacité maximale qui peut être allouée pour un volume d’un appareil virtuel StorSimple est 30 To.
   3. Sélectionnez le **Type d’utilisation** pour le volume. Si vous utilisez le volume hiérarchisé pour les données d’archivage, l’activation de la case à cocher **Utiliser ce volume pour les données d’archivage auxquelles vous accédez moins souvent** définit la taille de segment de déduplication pour votre volume sur 512 Ko. Si cette option n’est pas activée, le volume à plusieurs niveaux correspondant utilise une taille de segment de 64 Ko. Une grande taille de segment de déduplication permet à l’appareil d’accélérer le transfert des données d’archivage volumineuses vers le cloud (Les volumes à plusieurs niveaux étaient appelés volumes principaux).
   4. Cliquez sur l’icône en forme de flèche ![Icône en forme de flèche](./media/storsimple-manage-volumes/HCS_ArrowIcon.png)pour accéder à la page **Paramètres supplémentaires** .
      
        ![Assistant Ajout de volume, paramètres avancés](./media/storsimple-manage-volumes/AddVolume2.png)
5. Sous **Paramètres supplémentaires**, ajoutez un enregistrement de contrôle d’accès (ACR) :
   
   1. Dans la liste déroulante, sélectionnez un enregistrement de contrôle d’accès (ACR). Vous pouvez également ajouter un nouvel enregistrement de contrôle d’accès. Les enregistrements de contrôle d’accès déterminent quels hôtes peuvent accéder à vos volumes en faisant correspondre le nom qualifié de l’hôte à celui répertorié dans l’enregistrement.
   2. Nous vous recommandons d’activer la sauvegarde par défaut en cochant la case **Activer une sauvegarde par défaut pour ce volume** .
   3. Cliquez sur l’icône en forme de coche  ![Icône en forme de coche](./media/storsimple-manage-volumes/HCS_CheckIcon.png)  pour créer le volume avec les paramètres spécifiés.

Le nouveau volume est maintenant prêt à l’emploi.

## <a name="modify-a-volume"></a>Modification d’un volume
Modifiez un volume lorsque vous avez besoin d’en augmenter la taille ou de modifier les hôtes qui peuvent y accéder.

> [!IMPORTANT]
> * Si vous modifiez la taille du volume sur l’appareil, vous devez la modifier sur l’hôte également.
> * Les étapes côté hôte décrites ici concernent Windows Server 2012 (2012R2). Les procédures pour Linux ou d’autres systèmes d’exploitation sont différentes. Consultez les instructions du système d’exploitation de l’hôte pour modifier le volume sur un hôte exécutant un autre système d’exploitation.
> 
> 

### <a name="to-modify-a-volume"></a>Pour modifier un volume
1. Dans la page **Appareils**, sélectionnez l’appareil, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs de volumes**. Cette page répertorie dans un tableau tous les conteneurs de volumes associés à l’appareil.
2. Sélectionnez un conteneur de volumes et cliquez dessus pour afficher la liste de tous les volumes qu’il contient.
3. Dans la page **Volumes**, sélectionnez un volume, puis cliquez sur **Modifier**.
4. Dans l’Assistant Modifier un volume, sous **Paramètres de base**, vous pouvez effectuer les opérations suivantes :
   
   * Modifiez le **Nom** et le **Type** si vous souhaitez modifier un volume d’archivage hiérarchisé en sélectionnant la case à cocher **Utiliser ce volume pour les données d’archivage auxquelles vous accédez moins souvent** définit la taille de segment de déduplication pour votre volume sur 512 ko.
   * Augmenter la **capacité allouée**. La **capacité allouée** peut uniquement être augmentée. Vous ne pouvez pas réduire la taille d’un volume après sa création.
     
     > [!NOTE]
     > Vous ne pouvez pas modifier le conteneur de volumes après qu’il a été affecté à un volume.
     > 
     > 
5. Sous **Paramètres supplémentaires**, vous pouvez effectuer les opérations suivantes :
   
   * Modifier les enregistrements de contrôle d’accès, à condition que le volume soit hors connexion. Si le volume est en ligne, vous devez d’abord le mettre hors connexion. Reportez-vous à la procédure [Mise hors connexion d’un volume](#take-a-volume-offline) avant de modifier l’enregistrement de contrôle d’accès.
   * Modifiez la liste des enregistrements de contrôle d’accès une fois que le volume est hors connexion.
     
     > [!NOTE]
     > Vous ne pouvez pas modifier l’option **Activer une sauvegarde par défaut pour ce volume** pour le volume en question.
     > 
     > 
6. Enregistrez vos modifications en cliquant sur l’icône en forme de coche  ![icône-coche](./media/storsimple-manage-volumes/HCS_CheckIcon.png). Le portail Azure Classic affiche un message de mise à jour de volume. Il affiche un message de réussite quand le volume a été mis à jour avec succès.
7. Si vous développez un volume, procédez comme suit sur votre ordinateur hôte Windows :
   
   1. Accédez à **Gestion de l’ordinateur** ->** Gestion des disques**.
   2. Cliquez avec le bouton droit sur **Gestion des disques**, puis sélectionnez **Analyser les disques de nouveau**.
   3. Dans la liste des disques, sélectionnez le volume que vous avez mis à jour, cliquez avec le bouton droit, puis sélectionnez **Étendre le volume**. L’Assistant Étendre le volume démarre. Cliquez sur **Next**.
   4. Parcourez les étapes de l’Assistant en acceptant les valeurs par défaut. Une fois l’Assistant terminé, le volume doit afficher la taille augmentée.

![Vidéo disponible](./media/storsimple-manage-volumes/Video_icon.png) **Vidéo disponible**

Pour visionner une vidéo expliquant comment étendre un volume, cliquez [ici](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## <a name="take-a-volume-offline"></a>Mise hors connexion d’un volume
Vous devrez peut-être mettre un volume hors connexion si vous envisagez de le modifier ou de le supprimer. Lorsqu’un volume est hors connexion, il n’est pas disponible pour l’accès en lecture-écriture. Vous devrez mettre le volume hors connexion à la fois sur l’ordinateur hôte et sur l’appareil. Procédez comme suit pour mettre un volume hors connexion.

### <a name="to-take-a-volume-offline"></a>Pour mettre un volume hors connexion
1. Assurez-vous que le volume en question n’est pas utilisé avant de le mettre hors connexion.
2. Mettez d’abord le volume hors connexion sur l’ordinateur hôte. Cela élimine tout risque d’endommagement des données sur le volume. Pour les instructions spécifiques, reportez-vous aux instructions du système d’exploitation de l’ordinateur hôte.
3. Une fois que l’ordinateur hôte est hors connexion, mettez le volume hors connexion sur l’appareil en procédant comme suit :
   
   1. Dans la page **Appareils**, sélectionnez l’appareil, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs de volumes**. L’onglet **Conteneurs de volumes** répertorie dans un tableau tous les conteneurs de volumes associés à l’appareil.
   2. Sélectionnez un conteneur de volumes et cliquez dessus pour afficher la liste de tous les volumes qu’il contient.
   3. Sélectionnez un volume, cliquez sur **Mettre hors connexion**.
   4. Cliquez sur **Oui**lorsque vous êtes invité à confirmer l’opération. Le volume doit maintenant être hors connexion.
      
      Une fois qu’un volume est hors connexion, l’option **Mettre en ligne** devient disponible.

> [!NOTE]
> La commande **Mettre hors connexion** permet de demander à l’appareil de mettre le volume hors connexion. Si les ordinateurs hôtes utilisent toujours le volume, les connexions correspondantes sont rompues et la mise hors connexion du volume n’échoue pas.
> 
> 

## <a name="delete-a-volume"></a>Suppression d’un volume
> [!IMPORTANT]
> Vous pouvez supprimer un volume uniquement s’il est hors connexion.
> 
> 

Pour supprimer un volume, procédez comme indiqué ci-dessous.

### <a name="to-delete-a-volume"></a>Pour supprimer un volume
1. Dans la page **Appareils**, sélectionnez l’appareil, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs de volumes**.
2. Sélectionnez le conteneur de volumes qui contient le volume à supprimer. Cliquez sur le conteneur de volumes pour accéder à la page **Volumes** .
3. Tous les volumes associés à ce conteneur sont affichés sous forme de tableau. Vérifiez l’état du volume à supprimer. Si le volume que vous souhaitez supprimer n’est pas hors connexion, mettez-le d’abord hors connexion, en suivant la procédure [Mise hors connexion d’un volume](#take-a-volume-offline).
4. Une fois le volume hors connexion, cliquez sur **Supprimer** en bas de la page.
5. Cliquez sur **Oui**lorsque vous êtes invité à confirmer l’opération. Le volume est ensuite supprimé et la liste des volumes du conteneur affichée dans la page **Volumes** est mise à jour.

## <a name="monitor-a-volume"></a>Analyse d’un volume
L’analyse de volume vous permet de collecter des statistiques sur les E/S d’un volume. L’analyse est activée par défaut pour les 32 premiers volumes que vous créez. L’analyse des volumes supplémentaires est désactivée par défaut. L’analyse des volumes clonés est également désactivée par défaut.

Suivez la procédure ci-dessous pour activer ou désactiver l’analyse d’un volume.

### <a name="to-enable-or-disable-volume-monitoring"></a>Pour activer ou désactiver l’analyse de volume
1. Dans la page **Appareils**, sélectionnez l’appareil, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs de volumes**.
2. Sélectionnez le conteneur dans lequel réside le volume, puis cliquez sur le conteneur de volumes pour accéder à la page **Volumes** .
3. Tous les volumes associés à ce conteneur sont affichés sous forme de tableau. Cliquez sur le volume ou le volume cloné pour le sélectionner.
4. En bas de la page, cliquez sur **Modifier**.
5. Dans l’Assistant Modifier un volume, sous **Paramètres de base**, sélectionnez **Activer** ou **Désactiver** dans la liste déroulante **Analyse**.
   
    ![Modifier un volume, paramètres de base](./media/storsimple-manage-volumes/HCS_MonitorVolumeM.png)

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [cloner un volume StorSimple](storsimple-clone-volume.md).
* Découvrez comment [utiliser le service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).




<!--HONumber=Nov16_HO3-->


