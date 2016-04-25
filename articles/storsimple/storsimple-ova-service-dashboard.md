<properties 
   pageTitle="Tableau de bord du service StorSimple Manager - Virtual Array | Microsoft Azure"
   description="Décrit le tableau de bord du service StorSimple Manager et explique comment l’utiliser pour surveiller l’état de votre StorSimple Virtual Array."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/07/2016"
   ms.author="alkohli" />

# Utiliser le tableau de bord du service StorSimple Manager pour StorSimple Virtual Array

## Vue d'ensemble

La page de tableau de bord du service StorSimple Manager fournit un résumé des StorSimple Virtual Arrays (également appelés appareils virtuels ou appareils virtuels locaux StorSimple) qui sont connectés au service StorSimple Manager, mettant en surbrillance ceux qui requièrent l’attention particulière d’un administrateur système. Ce didacticiel présente la page du tableau de bord, explique le contenu du tableau de bord et ses fonctions, et décrit les tâches que vous pouvez effectuer à partir de cette page.

![Tableau de bord du service](./media/storsimple-ova-service-dashboard/dashboard1.png)

Le tableau de bord du service StorSimple Manager affiche les informations suivantes :

- La zone de **graphique** en haut de la page présente les métriques appropriées pour vos appareils virtuels. Vous pouvez afficher le stockage principal utilisé sur tous les appareils virtuels, ainsi que le stockage de cloud utilisé par les appareils virtuels sur une période donnée. Utilisez les commandes dans le coin supérieur droit du graphique pour définir une utilisation relative ou absolue et pour afficher une échelle de temps d’une semaine, d’un mois, de 3 mois ou d’un an. Utilisez le contrôle d’actualisation ![contrôle de l’actualisation](./media/storsimple-ova-service-dashboard/refresh-control.png) pour actualiser le graphique.

- L’**aperçu de l’utilisation** montre le stockage principal approvisionné et utilisé par tous les appareils virtuels par rapport au stockage total disponible sur tous les appareils virtuels. **Approvisionné** fait référence à la quantité de stockage préparée et allouée à l’utilisation, **Utilisé** fait référence à l’utilisation des partages et volumes, tel qu’affiché par les initiateurs connectés aux appareils virtuels, tandis que **Capacité max.** indique la capacité maximale de tous les appareils virtuels.

- La zone d’**alertes** fournit un instantané de toutes les alertes actives sur tous les appareils virtuels, groupées par gravité. Cliquez sur le niveau de gravité pour ouvrir la pages des **alertes**, dédiée à l’affichage de ces alertes. Sur la page **Alertes**, vous pouvez cliquer sur une alerte individuelle pour afficher des détails supplémentaires sur cette alerte, y compris les actions recommandées. Vous pouvez également effacer l'alerte si le problème a été résolu.

- La zone **Tâches** fournit un instantané des tâches récentes de tous les appareils connectés à votre service. Il y a des liens que vous pouvez utiliser pour examiner les tâches en cours et celles qui ont réussi ou échoué au cours des 24 dernières heures.

- La zone **Aperçu rapide** à droite de la page fournit des informations utiles telles que l’état du service, le nombre total d’appareils connectés au service, l’emplacement du service et des détails sur l’abonnement associé au service. Il y a également un lien vers le journal des opérations. Cliquez sur le lien pour afficher la liste de toutes les opérations de service StorSimple Manager terminées.

Vous pouvez utiliser la page du tableau de bord du service StorSimple Manager pour effectuer les tâches suivantes :

- obtention de la clé d’inscription.
- Afficher les journaux des opérations

## Obtenir la clé d’inscription du service

La clé d’inscription auprès du service permet d’enregistrer un appareil virtuel StorSimple à l’aide du service StorSimple Manager, de manière à ce que l’appareil apparaisse sur le portail Azure Classic pour des actions de gestion futures. La clé est créée sur le premier appareil virtuel et partagée avec les autres appareils virtuels.

Le fait de cliquer sur la **clé d’enregistrement** (au bas de la page) ouvre la boîte de dialogue **Clé d’enregistrement auprès du service**, à partir de laquelle vous pouvez soit copier la clé d’enregistrement dans le presse-papier, soit la régénérer.

![clé d’enregistrement](./media/storsimple-ova-service-dashboard/service-dashboard3.png)

La régénération de la clé n’affecte pas les appareils virtuels déjà enregistrés : elle affecte uniquement les appareils virtuels enregistrés auprès du service après la régénération.

Pour plus d’informations sur l’obtention de la clé d’enregistrement auprès du service, consultez la page [Obtenir la clé d’inscription](storsimple-ova-manage-service.md#get-the-service-registration-key).

## Affichage des journaux des opérations

Vous pouvez afficher les journaux des opérations en cliquant sur le lien des journaux disponible dans le volet **Aperçu rapide** du tableau de bord. Ceci vous dirigera vers la page des services de gestion, où vous pouvez filtrer et consulter les journaux propres à votre service StorSimple Manager.

![Journal des opérations](./media/storsimple-ova-service-dashboard/ops-log.png)

## Étapes suivantes

Découvrez comment [utiliser l’interface utilisateur web locale pour gérer votre StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0413_2016-->