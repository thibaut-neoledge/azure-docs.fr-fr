---
title: "Basculement d’appareil et récupération d’urgence pour StorSimple Virtual Array | Microsoft Docs"
description: En savoir plus sur le basculement de votre StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 94a8c3db-8e47-4e9a-917a-29b14a9263aa
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 78daa5a75b3414e2761333ea6ad91945596553c8
ms.openlocfilehash: 2017fcdc7a5c36c7c00d8bcef5be973ed60e3f82


---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array"></a>Basculement d'appareil et  récupération d'urgence pour votre StorSimple Virtual Array
## <a name="overview"></a>Vue d'ensemble
Cet article décrit la récupération d'urgence pour votre Microsoft Azure StorSimple Virtual Array (également appelé appareil virtuel local StorSimple), y compris les étapes détaillées permettant de basculer vers un autre appareil virtuel en cas de sinistre. Un basculement vous permet de migrer vos données à partir d'un appareil *source* dans le centre de données vers un autre appareil *cible* situé à un emplacement géographique identique ou différent. Le basculement de l'appareil s'applique à l'ensemble de l'appareil. Lors du basculement, les données de cloud pour l'appareil source deviennent la propriété de l'appareil cible.

Le basculement de l'appareil est orchestré via la fonctionnalité de récupération d'urgence et se lance à partir de la page **Appareils** . Cette page répertorie tous les appareils StorSimple connectés à votre service StorSimple Manager. Pour chaque appareil, le nom convivial, le statut, la capacité maximale et d'approvisionnement, le type et le modèle s'affichent.

![](./media/storsimple-ova-failover-dr/image15.png)

Cet article s'applique aux StorSimple Virtual Arrays uniquement. Pour le basculement d'un appareil de la gamme 8000, consultez [Basculement et récupération d'urgence pour votre appareil StorSimple](storsimple-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery"></a>Qu'est-ce que la récupération d'urgence ?
Dans un scénario de récupération d’urgence, l’appareil principal cesse de fonctionner. Dans ce cas, vous pouvez déplacer les données de cloud associées à l’appareil défaillant vers un autre appareil en utilisant l’appareil principal en tant que *source* et en spécifiant un autre appareil en tant que *cible*. Ce processus est appelé le *basculement*. Pendant le basculement, la propriété de tous les volumes ou les partages de l'appareil source change et ceux-ci sont transférés vers l'appareil cible. Aucun filtrage des données n'est autorisé.

La récupération d'urgence est modelée comme une restauration complète de l'appareil à l'aide du suivi et de la hiérarchisation basés sur la carte thermique. Une carte thermique est définie par l'affectation d'une valeur de chaleur aux données selon les modèles de lecture et d'écriture. Cette carte thermique transmet les segments de données à chaleur faible vers le cloud d'abord tout en conservant les segments de données à chaleur élevée (plus utilisés) au niveau local. Pendant une récupération d'urgence, la carte thermique sert à restaurer et à rafraîchir les données à partir du cloud. L'appareil extrait tous les volumes/partages dans la dernière sauvegarde récente (comme déterminé en interne) et effectue une restauration à partir de cette sauvegarde. L'ensemble du processus de récupération d'urgence est orchestré par l'appareil.

## <a name="prerequisites-for-device-failover"></a>Configuration requise pour le basculement d'appareil
### <a name="prerequisites"></a>Composants requis
Pour n'importe quel basculement d'appareil, les conditions préalables suivantes doivent être satisfaites :

* L'appareil source doit se trouver dans un état **Désactivé** .
* L'appareil cible doit s'afficher avec l'état **Active** dans le portail Azure Classic. Vous devrez configurer un appareil virtuel cible avec une capacité identique ou supérieure. Vous devrez ensuite utiliser l'interface utilisateur web locale pour configurer et inscrire correctement l'appareil virtuel.
  
  > [!IMPORTANT]
  > N’essayez pas de configurer l’appareil virtuel inscrit via le service en cliquant sur **Terminer l’installation de l’appareil**. Aucune configuration d'appareil ne doit être effectuée via le service.
  > 
  > 
* Les appareils source et cible doivent être du même type. Vous pouvez uniquement basculer un appareil virtuel configuré comme un serveur de fichiers vers un autre serveur de fichiers. Il en est de même pour un serveur iSCSI.
* Pour un récupération d'urgence de serveur de fichiers, nous vous recommandons de joindre l'appareil cible au même domaine que celui de la source afin que les autorisations de partage soient automatiquement résolues. Cette version prend en charge uniquement le basculement vers un appareil cible dans le même domaine.

### <a name="other-considerations"></a>Autres considérations
* Nous vous recommandons de mettre hors connexion tous les volumes ou partages sur l'appareil source.
* Dans le cas d'un basculement planifié, nous vous recommandons d'effectuer une sauvegarde de l'appareil, puis de continuer le basculement pour minimiser la perte de données. Dans le cas d'un basculement non planifié, la sauvegarde la plus récente sera utilisée pour restaurer l'appareil.
* Les appareils cibles disponibles pour la récupération d'urgence sont des appareils ayant une capacité égale ou supérieure par rapport à l'appareil source. Les appareils connectés à votre service mais ne répondant pas aux critères d’espace ne seront pas disponibles en tant qu’appareils cibles.

### <a name="dr-prechecks"></a>Vérifications préalables à la récupération d'urgence
Avant le début de la récupération d'urgence, des vérifications préalables sont effectuées sur l'appareil. Ces vérifications garantissent qu'aucune erreur ne se produira lorsque la récupération d'urgence aura commencé. Les vérifications préalables sont les suivantes :

* Validation du compte de stockage
* Vérification de la connectivité du cloud dans Azure
* Vérification de l'espace disponible sur l'appareil cible
* Vérifier si un appareil source du serveur iSCSI possède des noms valides ACR, un IQN (ne dépassant pas 220 caractères) et un mot de passe CHAP (12 et 16 caractères) correspondant aux volumes

Si l'une de ces vérifications préalables échoue, vous ne pourrez pas continuer la récupération d'urgence. Vous devez résoudre ces problèmes, puis réessayer d'exécuter la récupération d'urgence.

Lorsque la récupération d'urgence est terminée, la propriété des données cloud sur l'appareil source est transférée vers l'appareil cible. L'appareil source n'est alors plus disponible dans le portail. L'accès à tous les volumes/partages sur l'appareil source est bloqué et l'appareil cible devient actif.

> [!IMPORTANT]
> Même si l'appareil n'est plus disponible, la machine virtuelle que vous avez configurée sur le système hôte continue de consommer des ressources. Une fois que la récupération d’urgence est terminée, vous pouvez supprimer cette machine virtuelle de votre système hôte.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Basculement vers un tableau virtuel
Nous vous recommandons d’avoir un autre tableau StorSimple virtuel mis en service, configuré via l’interface utilisateur web locale et inscrit auprès du service StorSimple Manager avant d’exécuter cette procédure.

> [!IMPORTANT]
> * Vous n’êtes pas autorisé à basculer d’un appareil de la gamme StorSimple 8000 vers un appareil virtuel de la gamme 1200.
> * Vous pouvez basculer d’un appareil virtuel avec mode FIPS déployé dans le portail Azure Government vers un appareil virtuel déployé dans le portail Azure Classic. L’opération inverse est également possible.
> 
> 

Procédez comme suit pour restaurer votre appareil vers un appareil virtuel StorSimple cible.

1. Mettez les volumes/partages hors connexion sur l'ordinateur hôte. Reportez-vous aux instructions spécifiques au système d'exploitation de l'ordinateur hôte pour mettre les volumes/partages hors connexion. Si ce n’est pas déjà fait, vous devrez mettre tous les volumes ou partages hors connexion sur l’appareil en accédant à **Appareils > Partages** (ou **Appareil > Volumes**). Sélectionnez un partage ou un volume, puis cliquez sur **Mettre hors connexion** au bas de la page. Cliquez sur **Oui**lorsque vous êtes invité à confirmer l’opération. Répétez cette procédure pour tous les partages/volumes sur l'appareil.
2. Sur la page **Appareils**, sélectionnez l’appareil source pour le basculement, puis cliquez sur **Désactiver**. 
    ![](./media/storsimple-ova-failover-dr/image16.png)
3. Vous êtes invité à confirmer l’opération. La désactivation d'appareil est une opération définitive qui ne peut pas être annulée. Vous recevrez également un rappel pour mettre vos partages/volumes hors connexion sur l'ordinateur hôte.
   
    ![](./media/storsimple-ova-failover-dr/image18.png)
4. Après confirmation, la désactivation démarre. Une fois la désactivation terminée avec succès, vous serez averti.
   
    ![](./media/storsimple-ova-failover-dr/image19.png)
5. Dans la page **Appareils**, l’état passe à **Désactivé**.
   
    ![](./media/storsimple-ova-failover-dr/image20.png)
6. Sélectionnez l’appareil désactivé, puis, en bas de la page, cliquez sur **Basculement**.
7. Dans l'Assistant de confirmation du basculement qui s'affiche, procédez comme suit :
   
   1. Dans la liste déroulante des appareils disponibles, choisissez un **Appareil cible.** Seuls les appareils possédant la capacité suffisante sont affichés dans la liste déroulante.
   2. Passez en revue les détails associés à l'appareil source tels que le nom de l'appareil, la capacité totale et les noms des partages qui seront basculés.
      
       ![](./media/storsimple-ova-failover-dr/image21.png)
8. Cochez la case **Je comprends que le basculement est une opération définitive qui, une fois effectuée avec succès, supprime l’appareil source.**.
9. Cliquez sur l’icône en forme de coche ![](./media/storsimple-ova-failover-dr/image1.png).
10. Un travail de basculement sera lancé et vous serez averti. Cliquez sur **Afficher le travail** pour contrôler le basculement.
    
     ![](./media/storsimple-ova-failover-dr/image22.png)
11. Dans la page **Travaux** , vous verrez un travail de basculement créé pour l’appareil source. Ce travail effectue les vérifications préalables pour la récupération d'urgence.
    
    ![](./media/storsimple-ova-failover-dr/image23.png)
    
     Une fois les vérifications préalables pour la récupération d'urgence terminées avec succès, le travail de basculement génèrera des travaux de restauration pour chaque volume/partage qui existe sur l'appareil source.
    
    ![](./media/storsimple-ova-failover-dr/image24.png)
12. Une fois le basculement terminé, accédez à la page **Appareils** .
    
    a. Sélectionnez l’appareil virtuel StorSimple qui a été utilisé en tant qu’appareil cible pour le processus de basculement.
    
    b. Accédez à la page **Partages** (ou **Volumes** pour un serveur iSCSI). Tous les partages (volumes) de l'ancien appareil doivent maintenant être affichés.
    
    ![](./media/storsimple-ova-failover-dr/image25.png)

![](./media/storsimple-ova-failover-dr/video_icon.png) **Vidéo disponible**

Cette vidéo montre comment vous pouvez basculer un appareil virtuel local StorSimple vers un autre appareil virtuel.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Virtual-Array-Disaster-Recovery/player]
> 
> 

## <a name="business-continuity-disaster-recovery-bcdr"></a>Continuité d’activité et récupération d’urgence (Business Continuity Disaster Recovery - BCDR)
Un scénario de continuité d’activité et récupération d’urgence (BCDR) se produit lorsque l’ensemble du centre de données Azure cesse de fonctionner. Cela peut affecter votre service StorSimple Manager et les appareils StorSimple associés.

S'il existe des appareils StorSimple inscrits juste avant un incident, ces appareils StorSimple devront peut-être être réinitialisés. Après l'incident, vous pouvez recréer et configurer ces appareils.

## <a name="errors-during-dr"></a>Erreurs lors de la récupération d'urgence
**Panne de connectivité cloud pendant la récupération d'urgence**

Si la connectivité cloud est interrompue après le démarrage de la récupération d'urgence et avant la fin de la restauration de l'appareil, la récupération d'urgence échoue et vous serez averti. L’appareil cible qui a été utilisé pour la récupération d’urgence est alors marqué comme *inutilisable.* Le même appareil cible ne peut pas servir ensuite pour des récupérations d'urgence futures.

**Aucun appareil cible compatible**

Si les appareils cibles disponibles n'ont pas suffisamment d'espace, une erreur s'affiche indiquant qu'il n'y a aucun appareil cible compatible.

**Échecs des vérifications préalables**

Si l'une des vérifications préalables n'est pas satisfaite, des erreurs s'affichent.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la [gestion de StorSimple Virtual Array à l’aide de l’interface utilisateur web locale](storsimple-ova-web-ui-admin.md).




<!--HONumber=Jan17_HO5-->


