<properties
	pageTitle="Gérer les serveurs et les coffres Azure Backup | Microsoft Azure"
	description="Ce didacticiel vous apprend à gérer les serveurs et les coffres Azure Backup."
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="jimpark; aashishr; giridham"/>


# Gérer les serveurs et les coffres Azure Backup
Cet article offre une vue d’ensemble des tâches de gestion des sauvegardes disponibles via le portail de gestion.

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com).
2. Cliquez sur **Recovery Services**, puis sur le nom du coffre de sauvegarde pour afficher la page de démarrage rapide.

    Sélectionnez les options situées en haut de la page de démarrage rapide pour afficher les tâches de gestion disponibles.

    ![Éléments protégés](./media/backup-azure-manage-windows-server/RS_tabs.png)

## Tableau de bord
Sélectionnez **Tableau de bord** pour afficher l’aperçu de l’utilisation du serveur. En bas du tableau de bord, vous pouvez réaliser les tâches suivantes :

- **Manage certificate**. Si un certificat a été utilisé pour enregistrer le serveur, utilisez cette tâche pour mettre à jour le certificat. Si vous utilisez des informations d'identification de coffre, n'utilisiez pas **Manage certificate**.
- **Supprimer**. Permet de supprimer le coffre de sauvegarde actuel. Si un coffre de sauvegarde n'est plus utilisé, vous pouvez le supprimer pour libérer de l'espace de stockage. L'option **Supprimer** est disponible uniquement une fois que tous les serveurs inscrits ont été supprimés du coffre.
- **Vault credentials**. Utilisez cet élément de menu d'aperçu rapide pour configurer vos informations d'identification de coffre.

## Éléments protégés
Sélectionnez **Éléments protégés** pour afficher les éléments sauvegardés à partir des serveurs. Cette liste est fournie à titre d'information uniquement.

![Éléments protégés](./media/backup-azure-manage-windows-server/RS_protecteditems.png)

## Éléments inscrits
Sélectionnez **Éléments inscrits** pour afficher le nom des serveurs inscrits auprès de ce coffre.

![Serveur supprimé](./media/backup-azure-manage-windows-server/RS_deletedserver.png)

Exécutez ensuite les tâches suivantes :

- **Autoriser la réinscription** : quand cette option est sélectionnée pour un serveur, vous pouvez utiliser l’**Assistant Inscription** dans l’agent pour inscrire de nouveau le serveur auprès du coffre de sauvegarde. Vous devrez peut-être effectuer cette nouvelle inscription en raison d'une erreur dans le certificat ou de la nécessité de régénérer un serveur.
- **Supprimer ** : permet de supprimer un serveur du coffre de sauvegarde. Toutes les données stockées associées au serveur sont immédiatement supprimées.

## Étapes suivantes
- [Restaurer un serveur Windows Server ou un client Windows à partir d’Azure](backup-azure-restore-windows-server.md)
- Pour en savoir plus sur Azure Backup, consultez la [vue d’ensemble d’Azure Backup](backup-introduction-to-azure-backup.md).
- Consultez le [forum Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_1217_2015-->