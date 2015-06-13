<properties
	pageTitle="Sauvegarde Azure - gérer les ordinateurs virtuels"
	description="Apprenez à gérer une machine virtuelle Azure"
	services="backup"
	documentationCenter=""
	authors="jimpark"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/28/2015"
	ms.author="jimpark"/>

# Gérer des ordinateurs virtuels


## Gérer des machines virtuelles protégées

1. Pour afficher et gérer les paramètres de sauvegarde pour un ordinateur virtuel cliquez sur le **éléments protégés** onglet.

  - Cliquez sur le nom d'un élément protégé, voir la **sauvegarde détails** onglet qui affiche des informations sur la dernière sauvegarde.

        ![Virtual machine backup](./media/backup-azure-manage-vms/backup-vmdetails.png)

2. Pour afficher et gérer les paramètres de stratégie de sauvegarde pour un ordinateur virtuel cliquez sur le **stratégies** onglet.

  - Le **stratégies de sauvegarde** onglet vous montre la stratégie existante. Vous pouvez modifier en fonction des besoins. Si vous avez besoin créer une nouvelle stratégie, cliquez sur **créer** sur la **stratégies** page. Notez que si vous souhaitez supprimer une stratégie qu'il ne devrait pas avoir de tous les ordinateurs virtuels associés.

        ![Virtual machine policy](./media/backup-azure-manage-vms/backup-vmpolicy.png)

3. Vous pouvez obtenir plus d'informations sur l'état ou des actions pour un ordinateur virtuel sur le **travaux** page. Cliquez sur une tâche dans la liste pour obtenir plus de détails ou de filtrer les travaux pour un ordinateur virtuel spécifique.

    ![Travaux](./media/backup-azure-manage-vms/backup-job.png)

## Sauvegarde d'un ordinateur virtuel
Vous pouvez effectuer une demande de sauvegarde d'un ordinateur virtuel, une fois qu'il est configuré pour la protection. Si la sauvegarde initiale est en attente pour la machine virtuelle, sauvegarde créera une copie complète de la machine virtuelle dans le coffre de sauvegarde Azure. Si la première sauvegarde est terminée, sauvegarde envoyer seulement les modifications à partir d'une sauvegarde précédente de coffre de sauvegarde Azure.

Pour créer une demande une sauvegarde d'un ordinateur virtuel :

1. Accédez à **éléments protégés** page et sélectionnez **Machine virtuelle Azure** en tant que **Type** (le cas échéant), puis cliquez sur **Sélectionnez** bouton.

    ![Type de machine virtuelle](./media/backup-azure-manage-vms/vm-type.png)

2. Sélectionnez l'ordinateur virtuel sur lequel vous souhaitez effectuer une demande de sauvegarde, puis cliquez sur **sauvegarde maintenant** bouton au bas de la page.

    ![Sauvegarder maintenant](./media/backup-azure-manage-vms/backup-now.png)

    Cela créera un travail de sauvegarde sur l'ordinateur virtuel sélectionné. Rétention de point de récupération créé via ce travail sera identique à celle spécifiée dans la stratégie associée à la machine virtuelle.

    ![Création du travail de sauvegarde](./media/backup-azure-manage-vms/creating-job.png)

    >[AZURE.NOTE]Pour afficher la stratégie associée à un ordinateur virtuel, explorez machine virtuelle dans le **éléments protégés** page et accédez à l'onglet Stratégie de sauvegarde.

3. Une fois que le travail est créé, vous pouvez cliquer sur **Afficher le travail** dans la barre de toast pour voir la tâche correspondante dans la page tâches.

    ![Travail de sauvegarde créé](./media/backup-azure-manage-vms/created-job.png)

4. Après l'achèvement réussi de la tâche, un point de récupération est créé qui vous permet de restaurer l'ordinateur virtuel. Cela permet également d'incrémenter la valeur de colonne de point de récupération par 1 dans **éléments protégés** page.

## Arrêter la protection des machines virtuelles
Vous pouvez choisir d'arrêter les sauvegardes futures d'un ordinateur virtuel avec les options suivantes :

- Conserver des données liées à la machine virtuelle dans le coffre de sauvegarde Azure
- Supprimer les données de sauvegarde associées de machine virtuelle

Si vous avez sélectionné la première option, vous pouvez utiliser les données de sauvegarde pour restaurer l'ordinateur virtuel. Pour connaître les prix pour ces ordinateurs virtuels, cliquez sur [ici](http://azure.microsoft.com/pricing/details/backup/).

Arrêter la protection d'un ordinateur virtuel :

1. Accédez à **éléments protégés** page et sélectionnez **machine virtuelle Azure** comme type de filtre (le cas échéant) et cliquez sur **Sélectionnez** bouton.

    ![Type de machine virtuelle](./media/backup-azure-manage-vms/vm-type.png)

2. Sélectionnez l'ordinateur virtuel et cliquez sur **Arrêter la Protection** en bas de la page.

    ![Arrêter la protection](./media/backup-azure-manage-vms/stop-protection.png)

3. Par défaut, Azure Backup ne supprime pas les données de sauvegarde associées à la machine virtuelle.

    ![Confirmation d'arrêter la protection](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    Si vous souhaitez supprimer les données de sauvegarde, sélectionnez la case à cocher.

    ![Case à cocher](./media/backup-azure-manage-vms/checkbox.png)

    Sélectionnez le motif de l'arrêt de la sauvegarde. Cette opération est facultative, fournir une raison permettent de sauvegarde Azure pour travailler sur les commentaires et de hiérarchiser les scénarios de client.

4. Cliquez sur **envoyer** pour envoyer le **Arrêter la protection** travail. Cliquez sur **Afficher le travail** pour voir le travail dans correspondant **travaux** page.

    ![Arrêter la protection](./media/backup-azure-manage-vms/stop-protect-success.png)

    Si vous n'avez pas sélectionné **suppression des données de sauvegarde associées** option pendant **Arrêter la Protection** Assistant, puis valider travail terminé, modifications d'état de protection à **Protection arrêtée**.

    ![Protection arrêtée](./media/backup-azure-manage-vms/protection-stopped-status.png)

    Si vous avez sélectionné le **suppression des données de sauvegarde associées** option, ordinateur virtuel n'est pas une partie de **éléments protégés** page.

## Protégez à nouveau un ordinateur virtuel
Si vous n'avez pas sélectionné le **suppression des données de sauvegarde associées** option **Arrêter la Protection**, vous pouvez protéger de nouveau l'ordinateur virtuel en suivant les étapes similaires pour la sauvegarde des machines virtuelles enregistrées. Une fois protégé, cet ordinateur virtuel sera conservées avant d'arrêter la protection de données de sauvegarde et les points de récupération créées après protéger de nouveau.

Après protéger de nouveau, l'état de protection de l'ordinateur virtuel est remplacé par **protégé** s'il existe des points de récupération avant **Arrêter la Protection**.

  ![Machine virtuelle reprotected](./media/backup-azure-manage-vms/reprotected-status.png)

>[AZURE.NOTE]Lors de la ré-protection de l'ordinateur virtuel, vous pouvez choisir une autre stratégie que la stratégie avec laquelle ordinateur virtuel a été initialement protégé.

## Annuler l'inscription d'ordinateurs virtuels

Si vous souhaitez supprimer l'ordinateur virtuel du coffre de sauvegarde :

1. Cliquez sur le **UNREGISTER** bouton au bas de la page.

    ![Désactiver la protection](./media/backup-azure-manage-vms/unregister-button.png)

    Une notification toast s'affiche en bas de l'écran de demande de confirmation. Cliquez sur **Oui** pour continuer.

    ![Désactiver la protection](./media/backup-azure-manage-vms/confirm-unregister.png)

## Supprimer les données de sauvegarde
Vous pouvez supprimer les données de sauvegarde associées à une machine virtuelle, soit :

- Au cours de la tâche d'arrêt de Protection
- Arrêter la protection après la tâche terminée sur une machine virtuelle

Pour supprimer les données de sauvegarde sur un ordinateur virtuel, qui se trouve dans « Protection arrêté » état valider la réussite de **Arrêter sauvegarde** travail :

1. Accédez à **éléments protégés** page et sélectionnez **Machine virtuelle Azure** comme type, puis cliquez sur **Sélectionnez** bouton.

    ![Type de machine virtuelle](./media/backup-azure-manage-vms/vm-type.png)

2. Sélectionnez l'ordinateur virtuel. L'ordinateur virtuel sera dans **Protection arrêtée** état.

    ![Protection arrêtée](./media/backup-azure-manage-vms/protection-stopped-b.png)

3. Cliquez sur le **Supprimer** bouton au bas de la page.

    ![Suppression de la sauvegarde](./media/backup-azure-manage-vms/delete-backup.png)

4. Dans le **Supprimer les données de sauvegarde** Assistant, sélectionnez un motif pour la suppression des données de sauvegarde (hautement recommandées) et cliquez sur **Submit**.

    ![Supprimer les données de sauvegarde](./media/backup-azure-manage-vms/delete-backup-data.png)

5. Cela créera un travail pour supprimer les données de sauvegarde de l'ordinateur virtuel sélectionné. Cliquez sur **Afficher le travail** pour afficher la tâche correspondante dans la page tâches.

    ![Suppression de données a réussi](./media/backup-azure-manage-vms/delete-data-success.png)

    Une fois la tâche terminée, entrée correspondant à l'ordinateur virtuel sera supprimée de **éléments protégés** page.


###Tableau de bord

Sur le **tableau de bord** page, vous pouvez consulter des informations sur les machines virtuelles, de leur stockage et travaux associés dans les dernières 24 heures. Vous pouvez afficher le statut de la sauvegarde et les erreurs de sauvegarde associés.

  ![Tableau de bord](./media/backup-azure-manage-vms/dashboard-protectedvms.png)

<!---HONumber=GIT-SubDir--> 