<properties 
   pageTitle="Tableau de bord du service StorSimple Manager | Microsoft Azure"
	description="Décrit le tableau de bord du service StorSimple Manager et explique comment l'utiliser pour surveiller l’état de votre solution StorSimple."
	services="storsimple"
	documentationCenter=""
	authors="SharS"
	manager="carolz"
	editor=""/>
<tags 
   ms.service="storsimple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="09/02/2015"
	ms.author="v-sharos"/>

# Utilisation du tableau de bord du service StorSimple Manager

## Vue d'ensemble

La page de tableau de bord du service StorSimple Manager fournit un résumé de tous les périphériques qui sont connectés au service StorSimple Manager, mettant en surbrillance celles qui requièrent l’attention particulière d'un administrateur système. Ce didacticiel présente la page du tableau de bord, explique le contenu du tableau de bord et ses fonctions, et décrit les tâches que vous pouvez effectuer à partir de cette page.

![Tableau de bord du service](./media/storsimple-service-dashboard/HCS_ServiceDashboard.png)

**Figure 1 : Tableau de bord du service StorSimple Manager**

Le tableau de bord du service StorSimple Manager affiche les informations suivantes :

- Dans la zone du **graphique**, vous pouvez voir les graphiques de mesures pour vos périphériques. Vous pouvez afficher le stockage principal utilisé sur tous les périphériques, ainsi que le stockage de cloud utilisé par les périphériques sur une période donnée. Utilisez les commandes dans le coin supérieur droit du graphique pour définir l’échelle de temps : 1 semaine, 1 mois, 3 mois ou 1 an.

- L’**aperçu de l'utilisation** montre le stockage principal alloué et utilisé par toutes les unités par rapport au stockage total disponible sur tous les périphériques. **Approvisionné** fait référence à la quantité de stockage préparée et allouée à l'utilisation, tandis qu’**Utilisé** fait référence à l'utilisation des volumes, tel qu'affiché par les initiateurs connectés aux périphériques.

- La zone d’**alertes** fournit un instantané de toutes les alertes actives sur tous les périphériques, groupées par gravité. Cliquez sur le niveau de gravité pour ouvrir la pages des **alertes**, dédiée à l’affichage de ces alertes. Sur la page **Alertes**, vous pouvez cliquer sur une alerte individuelle pour afficher des détails supplémentaires sur cette alerte, y compris les actions recommandées. Vous pouvez également effacer l'alerte si le problème a été résolu.

- La zone **Tâches** fournit un instantané des tâches récentes de tous les périphériques connectés à votre service. Il y a des liens que vous pouvez utiliser pour examiner les tâches actuellement en cours, celles qui ont échoué au cours des 24 dernières heures ou celles dont l’exécution est planifiée dans les 24 prochaines heures.

- La zone **Aperçu rapide** fournit des informations utiles telles que l'état du service, le nombre de périphériques connectés au service, l'emplacement du service et des détails sur l'abonnement associé au service. Il y a également un lien vers le journal des opérations. Cliquez sur le lien pour afficher la liste de toutes les opérations de service StorSimple Manager terminées.

Vous pouvez utiliser la page du tableau de bord du service StorSimple Manager pour effectuer les tâches suivantes :

- Afficher ou régénérer la clé d’inscription au service
- Modifier la clé de chiffrement des données du service
- Afficher les journaux des opérations

## Affichage ou régénération de la clé d’inscription

La clé d’inscription auprès du service permet d’enregistrer des périphériques Microsoft Azure StorSimple à l’aide du service StorSimple Manager, de manière à ce que le périphérique apparaisse sur le portail Microsoft Azure Management pour des actions de gestion futures. La clé est créée sur le premier périphérique et partagée avec vos autres périphériques.

Le fait de cliquer sur la **clé d’enregistrement** (au bas de la page) ouvre la boîte de dialogue **Clé d’enregistrement auprès du service**, à partir de laquelle vous pouvez soit copier la clé d’enregistrement dans le presse-papier, soit la régénérer.

La régénération de la clé n'affecte pas les périphériques déjà enregistrés : elle affecte uniquement les périphériques enregistrés auprès du service après la régénération.

Pour plus d'informations sur l'affichage et la génération de la clé d'enregistrement auprès du service, consultez la page [Obtenir la clé d'inscription](storsimple-manage-service.md#get-the-service-registration-key).

## Modification de la clé de chiffrement des données de service

Les clés de chiffrement des données de service permettent de chiffrer les données client confidentielles, telles que les identifiants du compte de stockage, qui sont envoyées à partir votre service StorSimple Manager sur le périphérique StorSimple. Vous devrez modifier ces clés périodiquement si votre service informatique applique une politique de rotation des clés sur les périphériques de stockage. Le processus de modification de la clé peut être légèrement différent selon qu'il y a un ou plusieurs périphériques gérés par le service StorSimple Manager.

La modification de la clé de chiffrement est un processus en 3 étapes :

1. Autoriser un appareil à modifier la clé de chiffrement des données du service dans le portail de gestion.
2. Utiliser Windows PowerShell pour StorSimple pour démarrer la modification de la clé de chiffrement des données du service.
3. Si vous avez plusieurs périphériques StorSimple, mettez à jour la clé de chiffrement des données sur les autres périphériques.

Les étapes suivantes décrivent le processus de substitution pour la clé de chiffrement des données de service.

[AZURE.INCLUDE [storsimple-change-data-encryption-key](../../includes/storsimple-change-data-encryption-key.md)]


## Affichage des journaux des opérations

Vous pouvez afficher les journaux des opérations en cliquant sur le lien des journaux disponible dans le volet **Aperçu rapide** du tableau de bord. Ceci vous dirigera vers la page des services de gestion, où vous pouvez filtrer et consulter les journaux propres à votre service StorSimple Manager.

## Étapes suivantes

[Découvrez la procédure de résolution des problèmes d’un périphérique StorSimple](storsimple-troubleshoot-operational-device.md).

<!---HONumber=September15_HO1-->