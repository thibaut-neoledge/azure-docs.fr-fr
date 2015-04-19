<properties 
	pageTitle="Sauvegarder une application web dans Azure App Service" 
	description="Apprenez à créer des sauvegardes de vos applications web dans Azure App Service." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Sauvegarder une application web dans Azure App Service


La fonctionnalité de sauvegarde et de restauration d'[Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) vous permet de créer facilement des sauvegardes d'application web manuelles et automatiques. Vous pouvez restaurer votre application web à une étape précédente ou créer une nouvelle application web basée sur l'une des sauvegardes de votre application d'origine. 

Pour plus d'informations sur la restauration d'une application web Azure depuis une sauvegarde, consultez la rubrique [Restaurer une application web](web-sites-restore.md).

<a name="whatsbackedup"></a>
## Éléments sauvegardés 
Web Apps peut sauvegarder les informations suivantes :

* configuration d'une application web ;
* contenu d'un fichier d'application web ;
* serveurs SQL ou bases de données MySQL connectés à votre application (vous pouvez choisir l'élément à inclure dans votre sauvegarde)

Ces informations sont sauvegardées dans le conteneur et le compte de stockage Azure que vous indiquez. 

> [AZURE.NOTE] Chaque sauvegarde représente une copie hors connexion complète de votre application et non une mise à jour incrémentielle.

<a name="requirements"></a>
## Exigences et restrictions

* La fonction de sauvegarde et de restauration implique que le site soit en mode Standard. Pour plus d'informations sur la mise à l'échelle de votre application web pour utiliser le mode Standard, consultez la rubrique [Mettre à l'échelle une application web dans Azure App Service](web-sites-scale.md). Notez que le mode Premium permet de réaliser un nombre supérieur de sauvegardes quotidiennes par rapport au mode Standard.

* La fonctionnalité de sauvegarde et de restauration requiert un conteneur et un compte de stockage Azure relevant du même abonnement que l'application web que vous sauvegardez. Si vous ne disposez pas encore d'un compte de stockage, vous pouvez en créer un. Pour cela, cliquez sur le **Compte de stockage**, dans le panneau **Sauvegardes** du [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715), puis, dans le panneau **Destination**, choisissez le **Compte de stockage** et le **Conteneur**. Pour plus d'informations sur les comptes de stockage Azure, consultez les [liens](#moreaboutstorage) fournis à la fin de cet article.

<a name="manualbackup"></a>
## Création d'une sauvegarde manuelle

1. Dans le portail Azure, choisissez votre application web dans le panneau Web Apps. Les informations détaillées sur votre application web s'affichent dans un nouveau panneau.
2. Sélectionnez l'option **Paramètres**. Le panneau **Paramètres** s'affiche. Celui-ci vous permet de modifier votre application web.
	
	![Backups page][ChooseBackupsPage]

3. Choisissez l'option **Sauvegardes** dans le panneau **Paramètres**. Le panneau **Sauvegardes** s'affiche.
	
4. Dans le panneau **Sauvegardes**, choisissez la destination de sauvegarde en sélectionnant un **Compte de stockage** et un **Conteneur**. Ce compte de stockage doit relever du même abonnement que l'application web que vous sauvegardez.
	
	![Choose storage account][ChooseStorageAccount]
	
5. Sous l'option **Bases de données incluses** du panneau **Sauvegardes**, sélectionnez les bases de données connectées à l'application web (SQL Server ou MySQL) que vous souhaitez sauvegarder. 

	> [AZURE.NOTE] 	Pour qu'une base de données apparaisse dans cette liste, sa chaîne de connexion doit figurer dans la section **Chaînes de connexion** du panneau **Paramètres d'application web** du portail.
	
6. Dans le panneau **Sauvegardes**, sélectionnez la destination concernée sous **Destination de sauvegarde**. Vous devez choisir un conteneur et un compte de stockage existants.
7. Dans la barre de commandes, cliquez sur **Sauvegarder maintenant**.
	
	![BackUpNow button][BackUpNow]
	
	Un message de progression s'affiche au cours du processus de sauvegarde.
	

Vous pouvez à tout moment effectuer une sauvegarde manuelle.  

<a name="automatedbackups"></a>
## Configuration de sauvegardes automatisées

1. Dans le panneau **Sauvegardes**, activez l'option **Sauvegarde planifiée**.
	
	![Enable automated backups][SetAutomatedBackupOn]
	
2. Sélectionnez le compte de stockage dans lequel vous souhaitez sauvegarder votre application web. Ce compte de stockage doit relever du même abonnement que l'application web que vous sauvegardez.
	
	![Choose storage account][ChooseStorageAccount]
	
3. Dans la zone **Fréquence**, indiquez la fréquence souhaitée des sauvegardes automatisées. 
	Le nombre de jours doit être situé entre 1 et 90 compris (d'une fois par jour à tous les 90 jours).
	
4. Utilisez l'option **Début** pour indiquer la date et l'heure de début des sauvegardes automatisées souhaitées. 
	
	> [AZURE.NOTE] Azure stocke les heures de sauvegarde au format UTC, mais les affiche selon l'heure de l'ordinateur que vous utilisez pour afficher le portail.
	
5. Dans la section **Bases de données incluses**, sélectionnez les bases de données connectées à l'application web (serveur SQL ou MySQL) que vous souhaitez sauvegarder. Pour qu'une base de données apparaisse dans la liste, sa chaîne de connexion doit figurer dans la section **Chaînes de connexion** du panneau **Paramètres d'application web** du portail.
	
	> [AZURE.NOTE] Si vous avez sélectionné une ou plusieurs bases de données dans la sauvegarde et spécifié une fréquence inférieure à 7 jours, vous serez informé du fait que de fréquentes sauvegardes peuvent augmenter les coûts de vos bases de données.
	
6. D'autre part, définissez la valeur **Rétention (jours)** selon le nombre de jours pendant lesquels vous souhaitez conserver la sauvegarde.
7. Dans la barre de commandes, cliquez sur le bouton **Enregistrer** pour enregistrer les modifications de votre configuration (ou sélectionnez **Ignorer** pour ne pas les enregistrer).
	
	![Save button][SaveIcon]

<a name="aboutbackups"></a>
## Mode de stockage des sauvegardes

Dès lors que vous avez effectué une ou plusieurs sauvegardes, celles-ci apparaissent dans le panneau **Conteneurs** de votre **Compte de stockage**, ainsi que votre application web. Dans le **Compte de stockage**, chaque sauvegarde se compose d'un fichier .zip et d'un fichier .xml contenant respectivement les données sauvegardées et un manifeste du contenu du fichier .zip. 

Les noms des fichiers de sauvegarde .zip et .xml se composent du nom de votre application web suivi d'un trait de soulignement et de l'heure à laquelle la sauvegarde a eu lieu. L'horodatage comprend la date au format AAAAMMJJ (en chiffres, sans espace) ainsi que l'heure UTC au format 24 heures (par exemple, fabrikam_201402152300.zip). Vous pouvez décompresser ces fichiers pour accéder à vos sauvegardes sans recourir à une restauration de l'application web.

Le fichier XML stocké avec le fichier zip indique le nom du fichier de base de données sous *backupdescription* > *databases* > *databasebackupdescription* > *filename*.

Le fichier de sauvegarde de base de données est stocké à la racine du fichier .zip. Pour une base de données SQL, il s'agit d'un fichier BACPAC (pas d'extension de fichier) qui peut être importé. Pour créer une base de données SQL basée sur l'exportation du fichier BACPAC, suivez les étapes indiquées dans l'article [Importer un fichier BACPAC pour créer une nouvelle base de données utilisateur.](http://technet.microsoft.com/library/hh710052.aspx)

Pour plus d'informations sur la restauration d'une application web (y compris les bases de données) à l'aide du portail Azure, consultez la rubrique [Restaurer une application web dans Azure App Service](web-sites-restore.md).

> [AZURE.NOTE] Toute modification apportée aux fichiers de votre conteneur **websitebackups** peut invalider la sauvegarde et la rendre impossible à restaurer.

<a name="notes"></a>
## Remarques

* Veillez à configurer correctement les chaînes de connexion pour chacune de vos bases de données dans le panneau **Paramètres d'application Web** correspondant au panneau **Paramètres** de l'application web, afin que la fonctionnalité de sauvegarde et de restauration inclue vos bases de données.
* Bien qu'il vous soit possible de sauvegarder plusieurs applications web sur le même compte de stockage, pensez à créer un compte de stockage distinct pour chaque application web afin d'en faciliter la maintenance.

>[AZURE.NOTE] Si vous souhaitez commencer à utiliser Azure App Service avant d'ouvrir un compte Azure, accédez à la page d'[essai d'App Service](http://go.microsoft.com/fwlink/?LinkId=523751) (en anglais), qui vous permet de créer immédiatement une application web de départ de courte durée dans App Service. Aucune carte de crédit n'est requise, et vous ne prenez aucun engagement.

<a name="nextsteps"></a>
## Étapes suivantes
Pour plus d'informations sur la restauration d'une application web Azure à partir d'une sauvegarde, consultez la rubrique [Restaurer une application web dans Azure App Service](web-sites-restore.md).

Pour la prise en main d'Azure, consultez la page [Version d'évaluation gratuite de Microsoft Azure](/pricing/free-trial/).


<a name="moreaboutstorage"></a>
### En savoir plus sur les comptes de stockage

[Présentation des comptes de stockage](storage-whatis-account.md)

[How to: Create a storage account](storage-create-storage-account.md)

[Surveillance d'un compte de stockage](storage-monitor-storage-account.md)

[Présentation de la facturation du stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx) (en anglais)

## Modifications
* Pour plus d'informations sur les modifications entre Sites Web et App Service, consultez la page : [Azure App Service et son impact sur les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714) (en anglais)
* Pour plus d'informations sur les modifications entre l'ancien portail et le nouveau, consultez la page : [Référence pour la navigation sur la version préliminaire du portail](http://go.microsoft.com/fwlink/?LinkId=529715) (en anglais)

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

<!--HONumber=49-->