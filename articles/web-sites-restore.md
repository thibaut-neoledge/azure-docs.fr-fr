<properties 
	pageTitle="Restauration d'un site Web Microsoft Azure" 
	description="Découvrez comment restaurer vos sites Web Azure depuis une sauvegarde." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

#Restauration d'un site Web Microsoft Azure

Cet article vous explique comment restaurer un site Web précédemment sauvegardé à l'aide de la fonction de sauvegarde de Sites Web Azure. Pour plus d'informations, consultez la rubrique [Sauvegarde des sites Web Microsoft Azure](http://azure.microsoft.com/documentation/articles/web-sites-backup/). 

La fonction de restauration de Sites Web Azure vous permet de restaurer votre site Web à la demande, à une étape précédente ou de créer un site Web basé sur des sauvegardes de votre site d'origine. La création d'un site Web s'exécutant parallèlement à la dernière version peut être utile à des fins de test A/B.

La fonction Restaurer, accessible sous l'onglet Sauvegardes du portail Sites Web Azure, est uniquement disponible en mode Standard.

##Dans cet article
- [Pour restaurer un site Web Azure à partir d'une sauvegarde précédente](#PreviousBackup)
- [Pour restaurer un site Web Azure directement depuis un compte de stockage](#StorageAccount)
- [Sélection des paramètres de restauration de votre site Web et exécution de l'opération de restauration](#RestoreSettings)
- [Affichage des journaux des opérations](#OperationLogs)


<a name="PreviousBackup"></a>
##Pour restaurer un site Web Azure à partir d'une sauvegarde précédente

1. Sur l'onglet **Sauvegardes**, cliquez sur **Restore now** dans la barre de commandes située en bas de la page du portail. La boîte de dialogue **Restore now** s'affiche.
	
	![Choose backup source][ChooseBackupSource]
	
2. Sous **Choisissez la source de la sauvegarde**, sélectionnez **Sauvegarde précédente pour ce site Web**.
3. Sélectionnez la date de la sauvegarde que vous souhaitez restaurer, puis cliquez sur la flèche droite pour continuer.
4. Suivez la procédure de la section [Sélection des paramètres de restauration de votre site Web](#RestoreSettings) de cet article.

<a name="StorageAccount"></a>
##Pour restaurer un site Web Azure directement depuis un compte de stockage

1. Sur l'onglet **Sauvegardes**, cliquez sur **Restore now** dans la barre de commandes située en bas de la page du portail. La boîte de dialogue **Restore now** s'affiche.
	
	![Choose backup source][ChooseBackupSource]
	
2. Sous **Choisissez la source de la sauvegarde**, sélectionnez **Fichier de compte de stockage**. Vous pouvez directement y indiquer l'URL du fichier de compte de stockage ou cliquer sur l'icône du dossier pour accéder au compte de stockage d'objets blob et indiquer le fichier de sauvegarde. Cet exemple utilise l'icône du dossier.
	
	![Storage Account File][StorageAccountFile]
	
3. Cliquez sur l'icône du dossier pour ouvrir la boîte de dialogue **Parcourir le stockage cloud**.
	
	![Browse Cloud Storage][BrowseCloudStorage]
	

4. Développez le nom du compte de stockage que vous souhaitez utiliser, puis sélectionnez **websitebackups**, qui contient vos sauvegardes.
5. Sélectionnez le fichier .zip contenant la sauvegarde que vous souhaitez restaurer, puis cliquez sur **Ouvrir**.
6. Le fichier de compte de stockage est sélectionné et affiche la zone correspondant au compte de stockage. Cliquez sur la flèche droite pour continuer.
	
	![Storage Account File Selected][StorageAccountFileSelected]
	
7. Continuez avec la section suivante, [Sélection des paramètres de restauration de votre site Web et exécution de l'opération de restauration](#RestoreSettings).

<a name="RestoreSettings"></a>
##Sélection des paramètres de restauration de votre site Web et exécution de l'opération de restauration
1. Sous **Choisissez les paramètres de restauration de votre site Web**, **Restaurer à**, sélectionnez **Site Web actuel** ou **Nouvelle instance de site Web**.
	
	![Choose your web site restore settings][ChooseRestoreSettings]
	
	Si vous sélectionnez **Site Web actuel**, votre site Web existant est remplacé par la sauvegarde que vous avez sélectionnée (restauration destructrice). Toutes les modifications apportées au site Web depuis la sélection de la sauvegarde sont définitivement supprimées et l'opération de restauration ne peut pas être annulée. Lors de l'opération de restauration, votre site Web actuel est momentanément indisponible et vous en êtes averti.
	
	Si vous sélectionnez **Nouvelle instance de site Web**, un nouveau site Web est créé au même emplacement avec le nom que vous indiquez. (Par défaut, le nouveau nom est **restored-***nom_ancien_site_web*.) 
	
	Le site que vous restaurez présente le même contenu et la même configuration que ceux situés dans le portail du site d'origine. Il comprend toutes les bases de données que vous avez choisies d'inclure à l'étape suivante.
2. Si vous souhaitez restaurer une base de données en même temps que votre site Web, sous **Bases de données incluses**, sélectionnez le nom du serveur de base de données vers lequel vous souhaitez restaurer la base de données en utilisant le menu déroulant situé sous **Restaurer à**. Vous pouvez également choisir de créer un nouveau serveur de base de données ou sélectionner **Don't Restore** pour ne pas restaurer la base de données (option par défaut). 
	
	Après avoir choisi le nom du serveur, indiquez le nom de la base de données cible à restaurer dans la zone **Nom de la base de données**.
	
	Si votre restauration comprend une ou plusieurs bases de données, vous pouvez sélectionner **Ajuster automatiquement les chaînes de connexion** pour mettre à jour les chaînes de connexion stockées dans la sauvegarde afin qu'elles pointent vers la nouvelle base de données ou le serveur de base de données, le cas échéant. Vérifiez le bon fonctionnement des bases de données une fois la restauration terminée.
	
	![Choose database server host][ChooseDBServer]
	
	> [AZURE.NOTE] Vous ne pouvez pas restaurer une base de données SQL portant le même nom que le serveur SQL. Vous devez sélectionner un autre nom de base de données ou un autre hôte de serveur SQL vers lequel restaurer la base de données. 
	
	> [AZURE.NOTE] Vous pouvez restaurer une base de données MySQL portant le même nom vers le même serveur, mais le contenu stocké dans la base de données MySQL sera effacé.	
	
3. Si vous choisissez de restaurer une base de données existante, vous devez indiquer un nom d'utilisateur et un mot de passe. Si vous choisissez de restaurer une nouvelle base de données, vous devez indiquer son nom :
	
	![Restore to a new SQL database][RestoreToNewSQLDB]
	
	Cliquez sur la flèche droite pour continuer.	
4. Si vous choisissez de créer une nouvelle base de données, vous devez indiquer les informations d'identification et autres données de configuration initiales de la base de données dans la boîte de dialogue suivante. Le présent exemple illustre une nouvelle base de données SQL. (Les options d'une nouvelle base de données MySQL diffèrent légèrement.)
	
	![New SQL database settings][NewSQLDBConfig]
	
5. Cliquez sur la coche pour démarrer l'opération de restauration. Une fois cette opération terminée, la nouvelle instance du site Web (s'il s'agit de l'option de restauration choisie) s'affiche dans la liste des sites Web du portail.
	
	![Restored Contoso web site][RestoredContosoWebSite]

<a name="OperationLogs"></a>
##Affichage des journaux des opérations
	
1. Pour afficher les détails relatifs au déroulement de l'opération de restauration, accédez à l'onglet Tableau de bord du site Web. Dans la section **Aperçu rapide**, sous **Services de gestion**, cliquez sur **Journaux des opérations**.
	
	![Dashboard - Operation Logs Link][DashboardOperationLogsLink]
	
2. Vous accédez à la page **Journaux des opérations** du portail des services de gestion où vous pouvez afficher le jour correspondant à votre opération de restauration dans la liste des journaux des opérations :
	
	![Management Services Operation Logs page][ManagementServicesOperationLogsList]
	
3. Pour afficher les détails de l'opération, sélectionnez-la dans la liste, puis cliquez sur le bouton **Détails** de la barre de commandes.
	
	![Details Button][DetailsButton]
	
	La fenêtre **Détails de l'opération** s'ouvre et affiche le contenu copiable du fichier journal :
	
	![Operation Details][OperationDetails]
	

<!-- IMAGES -->
[ChooseBackupSource]: ./media/web-sites-restore/01ChooseBackupSource.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png


<!--HONumber=42-->
