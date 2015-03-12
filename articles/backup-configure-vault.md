<properties 
	pageTitle="Configuration d'Azure Recovery Services pour sauvegarder facilement et rapidement Windows Server" 
	description="Utilisez ce didacticiel pour découvrir comment utiliser le service Backup de l'offre cloud de Microsoft Azure pour sauvegarder Windows Server dans le cloud." 
	services="site-recovery" 
	documentationCenter="" 
	authors="markgalioto" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="markgal"/>



<h1><a id="configure-a-backup-vault-tutorial"></a>Configuration d'Azure Backup pour sauvegarder rapidement et facilement Windows Server</h1>
<div class="dev-callout"> 
<strong>Remarque</strong>
 
<p>Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Ce didacticiel vous guidera dans l'activation de la fonctionnalité Azure Backup. Auparavant, vous aviez besoin de créer ou de vous procurer un certificat X.509 v3 afin d'enregistrer votre serveur de sauvegarde. Les certificats sont toujours pris en charge, mais désormais, pour faciliter l'enregistrement d'un coffre Azure avec un serveur, vous pouvez générer un droit d'information d'identification de coffre à partir de la page de démarrage rapide. </p>
<ul> 
<li>Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez <a href="/fr-fr/pricing/free-trial/">Version d'évaluation gratuite Azure</a>.</li> 
 

</ul>
 

</div>
  

<p>Pour sauvegarder des fichiers et données de votre serveur Windows Server vers Azure, vous devez créer un coffre de sauvegarde dans la région géographique où vous souhaitez stocker les données. Ce didacticiel vous guidera lors de la création du coffre de stockage des sauvegardes, du téléchargement d'une information d'identification de coffre et de l'installation de l'agent de sauvegarde. Il vous présentera également les tâches de gestion de sauvegarde disponibles dans le portail de gestion.</p>



<h2><a id="create"></a>Création d'un coffre de sauvegarde</h2>

1. Connectez-vous au [Portail de gestion](https://manage.windowsazure.com).

2. Cliquez sur **Nouveau**, sur **Data Services**, sur **Recovery Services**, sur **Archivage de sauvegarde**, puis sur **Création rapide**.

3. Dans **Nom**, entrez un nom convivial permettant d'identifier le coffre de sauvegarde.

4. Dans **Région**, sélectionnez la région géographique du coffre de sauvegarde.  
![New backup vault](http://i.imgur.com/8ptgjuo.png)

5. Cliquez sur **Créer un archivage**.

	La création du coffre de sauvegarde peut prendre du temps. Pour vérifier l'état d'avancement de l'opération, vous pouvez contrôler les notifications au bas du portail. Une fois que le coffre de sauvegarde est créé, un message vous en informe. En outre, le coffre est répertorié dans les ressources de Recovery Services avec l'état **Actif**. 
![backup vault creation](http://i.imgur.com/grtLcKM.png)

3. Si vous disposez de plusieurs abonnements associés à votre compte professionnel, choisissez le compte correct à associer avec le coffre de sauvegarde.

<h2><a id="upload"></a>Téléchargement d'une information d'identification de coffre</h2>

Les informations d'identification de coffre remplacent les certificats pour l'inscription de votre service Azure auprès de votre serveur. Vous pouvez toujours utiliser des certificats. Toutefois, les informations d'identification de coffre sont plus faciles à utiliser, car vous utilisez le portail Azure pour les générer et les télécharger.  

1. Connectez-vous au [Portail de gestion](https://manage.windowsazure.com).

2. Cliquez sur **Recovery Services**, puis sélectionnez le coffre de sauvegarde à inscrire auprès d'un serveur.  La page de démarrage rapide pour ce coffre de sauvegarde apparaît.
	

3. Dans la page de démarrage rapide, cliquez sur **Télécharger les informations d'identification de l'archivage** pour indiquer au portail de générer et de télécharger les informations d'identification du coffre que vous utiliserez pour inscrire votre serveur auprès du coffre de sauvegarde.

4. Le portail générera une information d'identification de coffre en combinant le nom du coffre et la date actuelle. Cliquez sur **Enregistrer** pour télécharger les informations d'identification de l'archivage vers le dossier de téléchargements du compte local, ou sélectionnez **Enregistrer sous** dans le menu **Enregistrer** pour spécifier l'emplacement des informations d'identification de l'archivage. Vous ne pouvez pas modifier les informations d'identification. Il n'y a donc aucune raison de cliquer sur Ouvrir. Une fois que les informations d'identification auront été téléchargées, vous serez invité à Ouvrir le dossier. Cliquez sur **x** pour fermer ce menu.

<h2><a id="download"></a>Téléchargement et installation d'un agent de sauvegarde</h2>
1. Dans le [Portail de gestion](https://manage.windowsazure.com).

2. Cliquez sur **Recovery Services**, puis sélectionnez un coffre de sauvegarde pour afficher sa page de démarrage rapide.

3. Dans la page de démarrage rapide, sélectionnez le type d'agent que vous souhaitez télécharger. Vous pouvez choisir **Télécharger l'agent Azure Backup**, **Windows Server et System Center Data Protection Manager**, ou **Windows Server Essentials**.  Pour plus d'informations, consultez les pages suivantes :

	* [Installer l'agent Azure Backup pour Windows Server 2012 et System Center 2012 SP1 - Data Protection Manager](http://technet.microsoft.com/library/hh831761.aspx#BKMK_installagent)
	* [Installer l'agent Azure Backup pour Windows Server 2012 Essentials](http://technet.microsoft.com/library/jj884318.aspx)

Une fois l'agent installé, vous pouvez utiliser l'interface de gestion locale appropriée (telle que le composant logiciel enfichable Microsoft Management Console, la console System Center Data Protection Manager ou le tableau de bord Windows Server Essentials) pour configurer la stratégie de sauvegarde pour le serveur.
	
  

<h2><a id="manage"></a>Gestion des coffres et serveurs de sauvegarde</h2>
1. Connectez-vous au [Portail de gestion](https://manage.windowsazure.com).

2. Cliquez sur **Recovery Services**, puis sur le nom du coffre de sauvegarde pour afficher la page de démarrage rapide. 

3. Cliquez sur **Tableau de bord** pour afficher une vue d'ensemble de l'utilisation du serveur. En bas du tableau de bord, vous pouvez réaliser les tâches suivantes :
	* **Gérer le certificat**. Si un certificat a été utilisé pour enregistrer le serveur, utilisez cette tâche pour mettre à jour le certificat. Si vous utilisez des informations d'identification de coffre, n'utilisez pas **Gérer le certificat**.
	* **Supprimer**. Permet de supprimer le coffre de sauvegarde actuel. Si un coffre de sauvegarde n'est plus utilisé, vous pouvez le supprimer pour libérer de l'espace de stockage. **Supprimer** est disponible uniquement une fois que tous les serveurs inscrits ont été supprimés du coffre.
	* **Informations d'identification du coffre**. Utilisez cet élément de menu d'aperçu rapide pour configurer vos informations d'identification de coffre. 

3. Cliquez sur **Éléments protégés** pour afficher les éléments sauvegardés à partir des serveurs. Cette liste est fournie à titre d'information uniquement.  
![Protected Items][protected-itmes]

4. Cliquez sur **Serveurs** pour afficher le nom des serveurs inscrits auprès de ce coffre. Exécutez ensuite les tâches suivantes :
	* **Autoriser la réinscription**. Lorsque cette option est sélectionnée pour un serveur, vous pouvez utiliser l'Assistant Inscription dans l'agent pour inscrire à nouveau le serveur auprès du coffre de sauvegarde. Vous devrez peut-être effectuer cette nouvelle inscription en raison d'une erreur dans le certificat ou de la nécessité de régénérer un serveur. La réinscription est autorisée une seule fois par nom de serveur.
	* **Supprimer**. Permet de supprimer un serveur du coffre de sauvegarde. Toutes les données stockées associées au serveur sont immédiatement supprimées.

		![Deleted Server][deleted-server]

<h2><a id="next"></a>Étapes suivantes</h2>

- Pour en savoir plus sur Azure Backup, consultez la [vue d'ensemble d'Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=222425). 

- Visitez le [Forum Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933).

[new-backup-vault]: ./media/backup-configure-vault/RS_howtobackup1.png
[backup-vault-create]: ./media/backup-configure-vault/RS_howtobackup2.png
[manage-cert]: ./media/backup-configure-vault/RS_howtoupload1.png
[install-agent]: ./media/backup-configure-vault/RS_howtodownload1.png
[deleted-server]: ./media/backup-configure-vault/RS_deletedserver.png
[protected-itmes]: ./media/backup-configure-vault/RS_protecteditems.png

<!--HONumber=35.2-->

<!--HONumber=46--> 
