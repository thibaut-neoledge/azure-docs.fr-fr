
<properties
	pageTitle="Azure Backup - Gestion des machines virtuelles"
	description="Découvrez comment gérer une machine virtuelle Azure."
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

# Gérer des machines virtuelles


## Gérer des machines virtuelles protégées

1. Pour afficher et gérer les paramètres de sauvegarde pour une machine virtuelle, cliquez sur l’onglet **Éléments protégés**.

  - Cliquez sur le nom d’un élément protégé pour afficher l’onglet **Détails de la sauvegarde** qui vous présente des informations sur la dernière sauvegarde.

        ![Virtual machine backup](./media/backup-azure-manage-vms/backup-vmdetails.png)

2. Pour afficher et gérer les paramètres de stratégie de sauvegarde pour une machine virtuelle, cliquez sur l’onglet **Stratégies**.

  - L’onglet **Stratégies de sauvegarde** affiche la stratégie existante. Vous pouvez la modifier en fonction de vos besoins. Si vous devez créer une stratégie, cliquez sur **Créer** dans la page **Stratégies**. Notez que si vous voulez supprimer une stratégie, aucune machine virtuelle ne doit lui être associée.

        ![Virtual machine policy](./media/backup-azure-manage-vms/backup-vmpolicy.png)

3. Vous pouvez obtenir plus d’informations sur l’état ou les actions d’une machine virtuelle dans la page **Tâches**. Cliquez sur une tâche dans la liste pour obtenir plus de détails, ou filtrez les tâches pour une machine virtuelle spécifique.

    ![Travaux](./media/backup-azure-manage-vms/backup-job.png)

## Sauvegarde à la demande d’une machine virtuelle
Vous pouvez effectuer une sauvegarde à la demande d’une machine virtuelle une fois que celle-ci est configurée pour la protection. Si la sauvegarde initiale est en attente pour la machine virtuelle, la sauvegarde à la demande crée une copie complète de la machine virtuelle dans l’archivage de sauvegarde Azure. Si la première sauvegarde est terminée, la sauvegarde à la demande envoie seulement les modifications apportées lors de la sauvegarde précédente à l’archivage de sauvegarde Azure.

Pour créer une sauvegarde à la demande d’une machine virtuelle :

1. Accédez à la page **Éléments protégés** et sélectionnez **Machine virtuelle Azure** en tant que **Type** (si besoin), puis cliquez sur le bouton **Sélectionner**.

    ![Type de machine virtuelle](./media/backup-azure-manage-vms/vm-type.png)

2. Sélectionnez la machine virtuelle sur laquelle vous souhaitez effectuer la sauvegarde à la demande, puis cliquez sur le bouton **Sauvegarder maintenant** au bas de la page.

    ![Sauvegarder maintenant](./media/backup-azure-manage-vms/backup-now.png)

    Cette opération permet de créer un travail de sauvegarde sur la machine virtuelle sélectionnée. La plage de rétention de point de récupération créée via ce travail est identique à celle spécifiée dans la stratégie associée à la machine virtuelle.

    ![Création du travail de sauvegarde](./media/backup-azure-manage-vms/creating-job.png)

    >[AZURE.NOTE]Pour afficher la stratégie associée à une machine virtuelle, accédez à cette dernière dans la page **Éléments protégés**, puis à l’onglet Stratégie de sauvegarde.

3. Une fois que le travail est créé, vous pouvez cliquer sur le bouton **Afficher le travail** dans la barre de notification pour voir le travail correspondant dans la page Travaux.

    ![Travail de sauvegarde créé](./media/backup-azure-manage-vms/created-job.png)

4. Après l’achèvement réussi du travail, le point de récupération créé vous permet de restaurer la machine virtuelle. Cela permet également d’incrémenter la valeur de colonne de point de récupération de 1 dans la page **Éléments protégés**.

## Arrêt de la protection des machines virtuelles
Vous pouvez choisir d’arrêter les sauvegardes futures d’une machine virtuelle avec les options suivantes :

- Conserver les données de sauvegarde associées à la machine virtuelle dans l’archivage de sauvegarde Azure
- Supprimer les données de sauvegarde associées à la machine virtuelle

Si vous avez sélectionné la première option, vous pouvez utiliser les données de sauvegarde pour restaurer la machine virtuelle. Pour connaître les détails de la tarification de ces machines virtuelles, cliquez [ici](http://azure.microsoft.com/pricing/details/backup/).

Pour arrêter la protection d’une machine virtuelle :

1. Accédez à la page **Éléments protégés** et sélectionnez **Machine virtuelle Azure** en tant que type (si besoin), puis cliquez sur le bouton **Sélectionner**.

    ![Type de machine virtuelle](./media/backup-azure-manage-vms/vm-type.png)

2. Sélectionnez la machine virtuelle et cliquez sur **Arrêter la protection** en bas de la page.

    ![Arrêter la protection](./media/backup-azure-manage-vms/stop-protection.png)

3. Par défaut, Azure Backup ne supprime pas les données de sauvegarde associées à la machine virtuelle.

    ![Confirmation l’arrêt de la protection](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    Si vous souhaitez supprimer les données de sauvegarde, sélectionnez la case à cocher.

    ![Case à cocher](./media/backup-azure-manage-vms/checkbox.png)

    Sélectionnez le motif de l’arrêt de la sauvegarde. Bien que cela soit facultatif, le fait d’indiquer un motif permet à Azure Backup de l’utiliser pour hiérarchiser les scénarios clients.

4. Cliquez sur le bouton **Envoyer** pour envoyer le travail **Arrêter la protection**. Cliquez sur **Afficher le travail** pour voir le travail correspondant dans la page **Travaux**.

    ![Arrêter la protection](./media/backup-azure-manage-vms/stop-protect-success.png)

    Si vous n’avez pas sélectionné l’option **Supprimer les données de sauvegarde associées** dans l’Assistant **Arrêter la protection**, à la fin du travail, l’état de la protection devient **Protection arrêtée**. Les données restent dans Azure Backup jusqu'à ce qu'elles soient explicitement supprimées. Vous pouvez toujours supprimer les données en sélectionnant la machine virtuelle sur la page **Éléments protégés** et en cliquant sur **Supprimer**.

    ![Protection arrêtée](./media/backup-azure-manage-vms/protection-stopped-status.png)

    Si vous avez sélectionné l’option **Supprimer les données de sauvegarde associées**, la machine virtuelle n’est pas affichée dans la page **Éléments protégés**.

## Application d’une nouvelle protection à la machine virtuelle
Si vous n’avez pas sélectionné l’option **Supprimer les données de sauvegarde associées** dans l’Assistant **Arrêter la protection**, vous pouvez à nouveau protéger la machine virtuelle en suivant les étapes similaires à celles de la sauvegarde de machines virtuelles inscrites. Une fois la protection effectuée, les données de cette machine virtuelle sont conservées avant l’arrêt de la protection et des points de récupération sont créés après l’application de la nouvelle protection.

Après l’application d’une nouvelle protection, l’état de protection de la machine virtuelle devient **Protégé** s’il existe des points de récupération antérieurs à l’opération **Arrêter la protection**.

  ![Machine virtuelle à nouveau protégée](./media/backup-azure-manage-vms/reprotected-status.png)

>[AZURE.NOTE]Lors de l’application d’une nouvelle protection à la machine virtuelle, vous pouvez choisir une autre stratégie que la stratégie avec laquelle la machine virtuelle a été initialement protégée.

## Annulation de l’inscription des machines virtuelles

Si vous voulez supprimer la machine virtuelle de l’archivage de sauvegarde :

1. Cliquez sur le bouton **ANNULER L’INSCRIPTION** en bas de la page.

    ![Désactiver la protection](./media/backup-azure-manage-vms/unregister-button.png)

    Une notification toast s’affiche en bas de l’écran pour demander confirmation. Cliquez sur **OUI** pour continuer.

    ![Désactiver la protection](./media/backup-azure-manage-vms/confirm-unregister.png)

## Suppression des données de sauvegarde
Vous pouvez supprimer les données de sauvegarde associées à une machine virtuelle comme suit :

- Au cours du travail Arrêter la protection
- Après la fin du travail d’arrêt de la protection sur une machine virtuelle

Pour supprimer les données de sauvegarde d’une machine virtuelle ayant l’état « Protection arrêtée » après la réussite du travail **Arrêter la sauvegarde** :

1. Accédez à la page **Éléments protégés** et sélectionnez **Machine virtuelle Azure** comme type, puis cliquez sur le bouton **Sélectionner**.

    ![Type de machine virtuelle](./media/backup-azure-manage-vms/vm-type.png)

2. Sélectionnez la machine virtuelle. La machine virtuelle apparaît avec l’état **Protection arrêtée**.

    ![Protection arrêtée](./media/backup-azure-manage-vms/protection-stopped-b.png)

3. Cliquez sur le bouton **SUPPRIMER** en bas de la page.

    ![Supprimer la sauvegarde](./media/backup-azure-manage-vms/delete-backup.png)

4. Dans l’Assistant **Supprimer les données de sauvegarde**, sélectionnez un motif pour la suppression des données de sauvegarde (hautement recommandé) et cliquez sur **Soumettre**.

    ![Supprimer les données de sauvegarde](./media/backup-azure-manage-vms/delete-backup-data.png)

5. Cette opération crée un travail pour supprimer les données de sauvegarde de la machine virtuelle sélectionnée. Cliquez sur **Afficher le travail** pour afficher le travail correspondant dans la page Travaux.

    ![Suppression de données réussie](./media/backup-azure-manage-vms/delete-data-success.png)

    Une fois le travail terminé, l’entrée correspondant à la machine virtuelle est supprimée de la page **Éléments protégés**.


###Tableau de bord

Dans la page **Tableau de bord**, vous pouvez consulter les informations des machines virtuelles Azure, leur stockage et leurs tâches associées au cours des dernières 24 heures. Vous pouvez afficher l’état de la sauvegarde et les éventuelles erreurs de sauvegarde associées.

  ![Tableau de bord](./media/backup-azure-manage-vms/dashboard-protectedvms.png)
 

<!---HONumber=July15_HO3-->