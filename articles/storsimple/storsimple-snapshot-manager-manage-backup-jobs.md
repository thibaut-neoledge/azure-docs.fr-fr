<properties 
   pageTitle="Utiliser le Gestionnaire d’instantanés StorSimple pour afficher et gérer les tâches de sauvegarde | Microsoft Azure"
   description="Explique comment utiliser le composant logiciel enfichable MMC Gestionnaire d’instantanés StorSimple pour afficher et gérer les tâches de sauvegarde planifiées, en cours d’exécution et terminées."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2015"
   ms.author="v-sharos" />


# Utiliser le Gestionnaire d’instantanés StorSimple pour afficher et gérer les tâches de sauvegarde

## Vue d'ensemble

Le nœud **Tâches** dans le volet **Étendue** présente les tâches de sauvegarde **planifiées**, des **dernières 24 heures** et **en cours** que vous avez lancées de façon interactive ou à l’aide d’une stratégie configurée.

Ce didacticiel explique comment utiliser le nœud **Tâches** pour afficher des informations sur les tâches de sauvegarde planifiées, récentes et en cours d’exécution. (La liste des tâches et les informations correspondantes s’affichent dans le volet **Résultats**.) Vous pouvez également cliquer avec le bouton droit sur une tâche répertoriée et afficher un menu contextuel présentant les actions disponibles.

## Afficher les tâches planifiées

Pour afficher les tâches de sauvegarde planifiées, procédez comme suit.

#### Pour afficher les tâches planifiées

1. Cliquez sur l’icône du Bureau pour démarrer le Gestionnaire d’instantanés StorSimple. 

2. Dans le volet **Étendue**, développez le nœud **Tâches**, puis cliquez sur**Planifiées**. Les informations suivantes sont affichées dans le volet **Résultats** :

    - **Nom** : nom de l’instantané planifié

    - **Exécution suivante** : date et heure du prochain instantané planifié

    - **Dernière exécution** : date et heure de l’instantané planifié le plus récent

    >[AZURE.NOTE]Pour les instantanés ponctuels, les informations des colonnes **Exécution suivante** et **Dernière exécution** sont identiques.
 
    ![Tâches de sauvegarde planifiées](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png)
 
3. Pour effectuer des actions supplémentaires sur une tâche spécifique, cliquez avec le bouton droit sur le nom de la tâche dans le volet **Résultats** et sélectionnez les options de menu de votre choix.

## Afficher les tâches récentes

Pour afficher les tâches de sauvegarde et de restauration effectuées au cours des 24 dernières heures, procédez comme suit.

#### Pour afficher les tâches récentes

1. Cliquez sur l’icône du Bureau pour démarrer le Gestionnaire d’instantanés StorSimple.

2. Dans le volet **Étendue**, développez le nœud **Tâches**, puis cliquez sur **Dernières 24 heures**. Le volet **Résultats** affiche les tâches de sauvegarde des dernières 24 heures (avec un maximum de 64 tâches). Les informations suivantes apparaissent dans le volet **Résultats**, selon les options spécifiées pour **Affichage** :

    - **Nom** : nom de l’instantané planifié.
 
    - **Démarré** : date et heure de début de l’instantané.

    - **Arrêté** : date et l’heure de fin ou d’arrêt de l’instantané.

    - **Écoulé** : intervalle de temps entre le **démarrage** et l’**arrêt**.

    - **État** : état de la tâche terminée récemment. **Succès** indique que la sauvegarde a bien été créée. **Échec** indique que la tâche ne s’est pas exécutée correctement.

    - **Informations** : raison de l’échec.

    - **Octets traités (Mo)** : volume de données du groupe de volumes qui ont été traitées (en Mo).

    ![Tâches exécutées au cours des 24 dernières heures](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png)

3. Pour effectuer des actions supplémentaires sur une tâche spécifique, cliquez avec le bouton droit sur le nom de la tâche dans le volet **Résultats** et sélectionnez les options de menu de votre choix.

    ![Supprimer un travail](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)
     
## Afficher les tâches en cours d’exécution

Pour afficher les tâches en cours d’exécution, procédez comme suit.

#### Pour afficher les tâches en cours d’exécution

1. Cliquez sur l’icône du Bureau pour démarrer le Gestionnaire d’instantanés StorSimple.

2. Dans le volet **Étendue**, développez le nœud **Tâches**, puis cliquez sur**En cours d’exécution**. Selon les options spécifiées pour **Affichage**, les informations suivantes apparaissent dans le volet **Résultats** :

    - **Nom** : nom de l’instantané planifié.

    - **Démarré** : date et heure de début de l’instantané.

    - **Point de contrôle** : action actuelle de la sauvegarde.

    - **État** : pourcentage d’achèvement.
    
    - **Écoulé** : intervalle de temps écoulé depuis le début de la sauvegarde.

    - **Débit moyen (Mo)** : volume médian de données fournies, exprimé en mégaoctets (Mo).

    - **Octets traités (Mo)** : volume de données du groupe de volumes qui ont été traitées (en Mo).

    - **Octets écrits (Mo)** : volume de données qui ont été écrites dans la sauvegarde (en Mo).

    ![Tâches en cours d’exécution](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)

3. Pour effectuer des actions supplémentaires sur une tâche spécifique, cliquez avec le bouton droit sur le nom de la tâche dans le volet **Résultats** et sélectionnez les options de menu de votre choix.

## Étapes suivantes

[Utiliser le Gestionnaire d’instantanés StorSimple pour gérer le catalogue de sauvegarde](storsimple-snapshot-manager-manage-backup-catalog.md).















            


 

<!---HONumber=August15_HO8-->