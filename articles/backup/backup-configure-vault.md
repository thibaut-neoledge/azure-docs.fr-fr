<properties 
	pageTitle="Configuration d'Azure Backup Services pour sauvegarder facilement et rapidement Windows Server" 
	description="Utilisez ce didacticiel pour découvrir comment utiliser le service Backup de l'offre cloud de Microsoft Azure pour sauvegarder Windows Server dans le cloud." 
	services="backup" 
	documentationCenter="" 
	authors="markgalioto" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="backup" 
	ms.workload="storage-backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="hero-article" 
	ms.date="06/03/2015" 
	ms.author="markgal"/>



#Configurer Azure Backup pour sauvegarder rapidement et facilement Windows Server

Pour suivre ce didacticiel, vous avez besoin d’un compte Azure. Ce didacticiel vous guidera dans l'activation de la fonctionnalité Azure Backup.
>[AZURE.NOTE]Auparavant, vous aviez besoin de créer ou de vous procurer un certificat X.509 v3 afin d'enregistrer votre serveur de sauvegarde. Les certificats sont toujours pris en charge, mais désormais, pour faciliter l'enregistrement d'un coffre Azure avec un serveur, vous pouvez générer un droit d'information d'identification de coffre à partir de la page de démarrage rapide. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

Pour sauvegarder des fichiers et données de votre serveur Windows Server vers Azure, vous devez créer un coffre de sauvegarde dans la région géographique où vous souhaitez stocker les données. Ce didacticiel présente :

- la création de l’archivage que vous utiliserez pour stocker les sauvegardes ;
- le téléchargement des informations d’identification du coffre ;
- l’installation d’un agent de sauvegarde ;
- une vue d’ensemble des tâches de gestion des sauvegardes disponibles via le portail de gestion.

##Créer un coffre de sauvegarde

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com).
2. Cliquez sur **Nouveau** -> **Services de données** -> **Services de récupération** -> **Archivage de sauvegarde** > **Création rapide**.
3. Dans **Nom**, entrez un nom convivial permettant d'identifier le coffre de sauvegarde.
4. Dans **Region**, sélectionnez la région géographique du coffre de sauvegarde.

    ![Nouveau coffre de sauvegarde](./media/backup-configure-vault/RS_newbackupvault.png)

5. Cliquez sur **Create vault**.

    La création du coffre de sauvegarde peut prendre du temps. Pour vérifier l’état d’avancement de l’opération, vous pouvez contrôler les notifications au bas du portail. Une fois l’archivage de sauvegarde créé, vous en êtes informé par un message et l’archivage s’affiche dans les ressources de Recovery Services avec l’état **Actif**.

    ![Création du coffre de sauvegarde](./media/backup-configure-vault/RS_backupvaultcreation.png)

6. Si vous disposez de plusieurs abonnements associés à votre compte professionnel, choisissez le compte correct à associer avec le coffre de sauvegarde.

##Télécharger les informations d'identification du coffre

Les informations d'identification de coffre remplacent les certificats pour l'inscription de votre service Azure auprès de votre serveur. Vous pouvez toujours utiliser des certificats. Toutefois, les informations d'identification de coffre sont plus faciles à utiliser, car vous utilisez le portail Azure pour les générer et les télécharger.

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com).
2. Cliquez sur **Recovery Services**, puis sélectionnez le coffre de sauvegarde que vous souhaitez enregistrer avec un serveur. La page de démarrage rapide pour ce coffre de sauvegarde apparaît.
3. Dans la **page de démarrage rapide**, cliquez sur **Télécharger les informations d’identification de coffre** pour indiquer au portail de générer et de télécharger les informations d’identification du coffre que vous utiliserez pour inscrire votre serveur auprès de l’archivage de sauvegarde.

    Le portail générera une information d'identification de coffre en combinant le nom du coffre et la date actuelle.

4. Cliquez sur **Save** pour télécharger les informations d'identification de coffre vers les dossier de téléchargements du compte local, ou sélectionnez **Save As** à partir du menu **Save** pour spécifier un emplacement pour les informations d'identification de coffre. Vous ne pouvez pas modifier les informations d'identification. Il n'y a donc aucune raison de cliquer sur Ouvrir. Une fois que les informations d'identification auront été téléchargées, vous serez invité à Ouvrir le dossier. Cliquez sur **x** pour fermer ce menu.

##Télécharger et installer un agent de sauvegarde

1. Dans le [portail de gestion](https://manage.windowsazure.com).
2. Cliquez sur **Recovery Services**, puis sélectionnez un coffre de sauvegarde pour afficher sa page de démarrage rapide.
3. Dans la page de démarrage rapide, sélectionnez le type d'agent que vous souhaitez télécharger. Vous pouvez choisir **Download Azure Backup Agent**, **Windows Server and System Center Data Protection Manager**, ou **Windows Server Essentials**. Pour plus d’informations, consultez les pages suivantes :

	* [Installer l’agent Azure Backup pour Windows Server 2012 et System Center 2012 SP1 - Data Protection Manager](http://technet.microsoft.com/library/hh831761.aspx#BKMK_installagent)
	* [Installer l’agent Azure Backup pour Windows Server 2012 Essentials](http://technet.microsoft.com/library/jj884318.aspx)

Une fois l'agent installé, vous pouvez utiliser l'interface de gestion locale appropriée (telle que le composant logiciel enfichable Microsoft Management Console, la console System Center Data Protection Manager ou le tableau de bord Windows Server Essentials) pour configurer la stratégie de sauvegarde pour le serveur.

##Gérer les coffres et les serveurs de sauvegarde

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com).
2. Cliquez sur **Recovery Services**, puis sur le nom du coffre de sauvegarde pour afficher la page de démarrage rapide.
3. Cliquez sur **Dashboard** pour afficher l'aperçu de l'utilisation du serveur. En bas du tableau de bord, vous pouvez réaliser les tâches suivantes :

    - **Manage certificate**. Si un certificat a été utilisé pour enregistrer le serveur, utilisez cette tâche pour mettre à jour le certificat. Si vous utilisez des informations d'identification de coffre, n'utilisiez pas **Manage certificate**.
    - **Supprimer**. Permet de supprimer le coffre de sauvegarde actuel. Si un coffre de sauvegarde n'est plus utilisé, vous pouvez le supprimer pour libérer de l'espace de stockage. L'option **Supprimer** est disponible uniquement une fois que tous les serveurs inscrits ont été supprimés du coffre.
    - **Vault credentials**. Utilisez cet élément de menu d'aperçu rapide pour configurer vos informations d'identification de coffre.

4. Cliquez sur **Éléments protégés** pour afficher les éléments sauvegardés à partir des serveurs. Cette liste est fournie à titre d'information uniquement.

    ![Éléments protégés](./media/backup-configure-vault/RS_protecteditems.png)

5. Cliquez sur **Serveurs** pour afficher le nom des serveurs inscrits auprès de ce coffre. Exécutez ensuite les tâches suivantes :

    - **Allow Re-registration**. Lorsque cette option est sélectionnée pour un serveur, vous pouvez utiliser l'Assistant Inscription dans l'agent pour inscrire à nouveau le serveur auprès du coffre de sauvegarde. Vous devrez peut-être effectuer cette nouvelle inscription en raison d'une erreur dans le certificat ou de la nécessité de régénérer un serveur. La réinscription est autorisée une seule fois par nom de serveur.
    - **Supprimer**. Permet de supprimer un serveur du coffre de sauvegarde. Toutes les données stockées associées au serveur sont immédiatement supprimées.

        ![Serveur supprimé](./media/backup-configure-vault/RS_deletedserver.png)

##Étapes suivantes

- Pour en savoir plus sur Azure Backup, consultez la page [Présentation de la Sauvegarde Windows Azure](http://go.microsoft.com/fwlink/p/?LinkId=222425). 
- Consultez le [forum Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933).


 

<!---HONumber=August15_HO6-->