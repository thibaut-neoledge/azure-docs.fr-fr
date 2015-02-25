<properties 
	pageTitle="Sauvegardes de Sites Web Azure" 
	description="Apprenez à créer des sauvegardes de vos sites Web Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="9/19/2014" 
	ms.author="cephalin"/>

#Sauvegardes de Sites Web Azure

La fonctionnalité de sauvegarde et restauration de Sites Web Azure vous permet de créer facilement des sauvegardes de sites Web, manuellement ou automatiquement. Vous pouvez restaurer votre site Web à une étape précédente, ou créer un site Web basé sur les sauvegardes de votre site d'origine. 


Pour plus d'informations sur la restauration d'un site Web Azure depuis une sauvegarde, consultez [Restauration d'un site Web Azure](http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-restore/).

##Dans cet article

- [Sauvegarde automatique et facile (vidéo)](#video)
- [Éléments sauvegardés](#whatsbackedup)
- [Exigences et restrictions](#requirements)
- [Création d'une sauvegarde manuelle](#manualbackup)
- [Configuration de sauvegardes automatisées](#automatedbackups)
- [Stockage des sauvegardes](#aboutbackups)
- [Remarques](#notes)
- [Étapes suivantes](#nextsteps)
	- [En savoir plus sur les comptes de stockage](#moreaboutstorage)

<a name="video"></a>
##Sauvegarde automatique et facile (vidéo)

Dans cette vidéo, Eduardo Laureano et Scott Hanselman présentent les sauvegardes de Sites Web Azure. (Durée : 11:43)  

> [AZURE.VIDEO azure-websites-automatic-and-easy-backup]

<a name="whatsbackedup"></a>
##Éléments sauvegardés 
Sites Web Azure sauvegarde les informations suivantes :

* Configuration du site Web
* Contenu du site Web
* Serveurs SQL ou bases de données MySQL connectés à votre site (vous pouvez choisir l'élément à inclure dans votre sauvegarde)

Ces informations sont sauvegardées dans le compte de stockage Azure que vous indiquez. 

> [AZURE.NOTE] Chaque sauvegarde représente une copie hors connexion complète de votre site Web, et non une mise à jour incrémentielle.

<a name="requirements"></a>
##Exigences et restrictions

* La fonction de sauvegarde et de restauration implique que le site se situe au niveau Standard. Pour plus d'informations sur la mise à l'échelle de votre site Web au niveau Standard, consultez [Mise à l'échelle de sites Web](http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-scale/). 

* La fonctionnalité de sauvegarde et restauration demande un compte Azure Storage appartenant au même abonnement que le site Web que vous sauvegardez. À défaut de disposer d'un compte de stockage, vous pouvez en créer un en cliquant sur le bouton **Stockage** (icône grille) situé dans le volet de gauche du portail Azure, puis en sélectionnant **Nouveau** dans la barre de commandes du bas. Pour plus d'informations sur les comptes de stockage Azure, consultez les [liens](#moreaboutstorage) à la fin de cet article.

<a name="manualbackup"></a>
## Pour créer une sauvegarde manuelle

1. Dans le portail Azure de votre site Web, choisissez l'onglet **Sauvegardes**.
	
	![Backups page][ChooseBackupsPage]
	
2. Sélectionnez le compte de stockage dans lequel vous souhaitez sauvegarder votre site Web. Ce compte de stockage doit relever du même abonnement que le site Web que vous sauvegardez.
	
	![Choose storage account][ChooseStorageAccount]
	
3. Dans l'option **Bases de données incluses**, sélectionnez les bases de données connectées au site Web (serveur SQL ou MySQL) que vous souhaitez sauvegarder. 
	
	![Choose databases to include][IncludedDatabases]

	> [AZURE.NOTE] 	Pour permettre l'affichage d'une base de données dans cette liste, sa chaîne de connexion doit être présente dans la section **Chaînes de connexion** de l'onglet Configurer du portail.
	
4. Dans la barre de commandes, cliquez sur **Backup Now**.
	
	![BackUpNow button][BackUpNow]
	
	Un message de progression s'affiche au cours du processus de sauvegarde :
	
	![Backup progress message][BackupProgress]
	
Vous pouvez à tout moment effectuer une sauvegarde manuelle. Dans la version préliminaire, seules deux sauvegardes manuelles peuvent être effectuées par 24 heures (peut faire l'objet de modifications).  

<a name="automatedbackups"></a>
## Pour configurer des sauvegardes automatisées

1. Sur la page Sauvegardes, activez **Sauvegarde automatisée**.
	
	![Enable automated backups][SetAutomatedBackupOn]
	
2. Sélectionnez le compte de stockage dans lequel vous souhaitez sauvegarder votre site Web. Ce compte de stockage doit relever du même abonnement que le site Web que vous sauvegardez.
	
	![Choose storage account][ChooseStorageAccount]
	
3. Dans la zone **Fréquence**, indiquez la fréquence de sauvegarde automatisée de votre choix. (Dans la version préliminaire, le nombre de jours correspond à la seule unité de temps disponible.)
	
	![Choose backup frequency][Frequency]
	
	Le nombre de jours doit être situé entre 1 et 90 compris (d'une fois par jour à tous les 90 jours).
	
4. Utilisez l'option **Date de début** pour indiquer la date et l'heure de début des sauvegardes automatisées souhaitées. 
	
	![Choose start date][StartDate]
	
	Les heures disponibles correspondent à des incréments d'une demi-heure.
	
	![Choose start time][StartTime]
	
	> [AZURE.NOTE] Azure stocke les heures de sauvegarde au format UTC, mais les affiche selon l'heure de l'ordinateur que vous utilisez pour afficher le portail.
	
5. Dans la section **Bases de données incluses**, sélectionnez les bases de données connectées au site Web (serveur SQL ou MySQL) que vous souhaitez sauvegarder. Pour permettre l'affichage d'une base de données dans cette liste, sa chaîne de connexion doit être présente dans la section **Chaînes de connexion** de l'onglet Configurer du portail.
	
	![Choose databases to include][IncludedDatabases]
	
	> [AZURE.NOTE] Si vous avez sélectionné une ou plusieurs bases de données dans la sauvegarde et spécifié une fréquence inférieure à 7 jours, vous serez informé du fait que de fréquentes sauvegardes peuvent augmenter les coûts de vos bases de données.
	
6. Dans la barre de commandes, cliquez sur le bouton **Enregistrer** pour enregistrer les modifications de votre configuration (ou sélectionnez **Ignorer** pour ne pas les enregistrer).
	
	![Save button][SaveIcon]

<a name="aboutbackups"></a>
## Stockage des sauvegardes

Les sauvegardes effectuées apparaissent dans Conteneurs de votre compte de stockage. Vos sauvegardes se trouvent dans un conteneur appelé **websitebackups**. Chaque sauvegarde se compose d'un fichier .zip et d'un fichier .xml contenant respectivement les données sauvegardées et un manifeste du contenu du fichier .zip. 

Les noms des fichiers de sauvegarde .zip et .xml se composent du nom de votre site Web suivi d'un trait de soulignement et de l'heure à laquelle la sauvegarde a eu lieu. L'horodatage comprend la date au format AAAAMMJJ (en chiffres, sans espace) ainsi que l'heure UTC au format 24 heures (par exemple, fabrikam_201402152300.zip). Vous pouvez décompresser ces fichiers pour accéder à vos sauvegardes sans recourir à une restauration du site Web.

Le fichier XML stocké avec le fichier zip indique le nom du fichier de base de données sous *backupdescription* > *databases* > *databasebackupdescription* > *filename*.

Le fichier de sauvegarde de base de données est stocké à la racine du fichier .zip. Pour une base de données SQL, il s'agit d'un fichier BACPAC (pas d'extension de fichier) qui peut être importé. Pour créer une base de données SQL basée sur l'exportation du fichier BACPAC, suivez les étapes indiquées dans l'article [Importer un fichier BACPAC pour créer une nouvelle base de données utilisateur](http://technet.microsoft.com/fr-fr/library/hh710052.aspx).

Pour plus d'informations sur la restauration d'un site Web Azure (y compris les bases de données) via le portail de gestion Azure, consultez [Restauration d'un site Web Microsoft Azure]( http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-restore/).

> [AZURE.NOTE] Toute modification apportée aux fichiers de votre conteneur **websitebackups** peut invalider la sauvegarde et la rendre impossible à restaurer.

<a name="notes"></a>
## Remarques

* Veillez à correctement configurer les chaînes de connexion de chacune de vos bases de données sous l'onglet Configurer du site Web pour permettre à la fonctionnalité de sauvegarde et de restauration de les inclure.
* Dans la version préliminaire, il vous appartient de gérer le contenu sauvegardé enregistré dans votre compte de stockage. Si vous supprimez une sauvegarde de votre compte de stockage sans disposer d'une autre copie, vous ne serez pas en mesure de restaurer ultérieurement cette sauvegarde. 
* Bien qu'il vous soit possible de sauvegarder plusieurs sites Web sur le même compte de stockage, pensez à créer un compte de stockage distinct pour chaque site Web afin d'en faciliter la maintenance.
* Dans la version préliminaire, les opérations de sauvegarde et de restauration sont uniquement disponibles via le portail de gestion Azure.

<a name="nextsteps"></a>
## Étapes suivantes
Pour plus d'informations sur la restauration d'un site Web Azure depuis une sauvegarde, consultez [Restauration d'un site Web Azure](http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-restore/).

Pour la prise en main d'Azure, consultez la page [Version d'évaluation gratuite de Microsoft Azure](http://azure.microsoft.com/fr-fr/pricing/free-trial/).


<a name="moreaboutstorage"></a>
### En savoir plus sur les comptes de stockage

[Présentation des comptes de stockage](http://www.windowsazure.com/fr-fr/documentation/articles/storage-whatis-account/)

[ Création d'un compte de stockage](http://www.windowsazure.com/fr-fr/documentation/articles/storage-create-storage-account/)

[Surveillance d'un compte de stockage](http://www.windowsazure.com/fr-fr/documentation/articles/storage-monitor-storage-account/)

[Présentation de la facturation du stockage Windows](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount.png
[IncludedDatabases]: ./media/web-sites-backup/03IncludedDatabases.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow.png
[BackupProgress]: ./media/web-sites-backup/05BackupProgress.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn.png
[Frequency]: ./media/web-sites-backup/07Frequency.png
[StartDate]: ./media/web-sites-backup/08StartDate.png
[StartTime]: ./media/web-sites-backup/09StartTime.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png


<!--HONumber=42-->
