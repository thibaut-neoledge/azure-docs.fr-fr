<properties 
	pageTitle="Sauvegarder une application web dans Azure App Service" 
	description="Apprenez à créer des sauvegardes de vos applications Web dans Azure App Service" 
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


La fonctionnalité de sauvegarde et de restauration d’[Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) vous permet de créer facilement des sauvegardes d’application Web manuelles et automatiques. Vous pouvez restaurer votre application web à une étape précédente ou créer une nouvelle application web basée sur l’une des sauvegardes de votre application d’origine.

Pour plus d’informations sur la restauration d’une application Web Azure à partir d’une sauvegarde, consultez [Restaurer une application Web](web-sites-restore.md).

<a name="whatsbackedup"></a>
## Éléments sauvegardés 
Web Apps peut sauvegarder les informations suivantes :

* configuration d’une application web ;
* contenu d’un fichier d’application web ;
* serveurs SQL ou bases de données MySQL connectés à votre application (vous pouvez choisir l’élément à inclure dans votre sauvegarde)

Ces informations sont sauvegardées dans le conteneur et le compte de stockage Azure que vous indiquez.

> [AZURE.NOTE]Chaque sauvegarde représente une copie hors connexion complète de votre application et non une mise à jour incrémentielle.

<a name="requirements"></a>
## Exigences et restrictions

* La fonction de sauvegarde et de restauration implique que le site soit en mode Standard. Pour plus d’informations sur l’évolution de votre application Web en vue d’utiliser le mode Standard, consultez [Faire évoluer une application Web dans Azure App Service](web-sites-scale.md). Notez que le mode Premium permet de réaliser un nombre supérieur de sauvegardes quotidiennes par rapport au mode Standard.

* La fonctionnalité de sauvegarde et de restauration requiert un conteneur et un compte de stockage Azure relevant du même abonnement que l’application web que vous sauvegardez. Si vous ne disposez pas encore d’un compte de stockage, vous pouvez en créer un. Pour cela, cliquez sur le **Compte de stockage**, dans le panneau **Sauvegardes** du [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715), puis, choisissez le **Compte de stockage** et le **Conteneur** dans le panneau **Destination**. Pour plus d'informations sur les comptes de stockage Azure, consultez les [liens](#moreaboutstorage) situés en bas de cet article.

* La fonctionnalité de sauvegarde et de restauration prend en charge jusqu’à 10 Go de contenu de site Web et de base de données. Une erreur est indiquée dans les journaux des opérations lorsqu’un dépassement de ce seuil empêche la sauvegarde.

<a name="manualbackup"></a>
## Création d’une sauvegarde manuelle

1. Dans le portail Azure, choisissez votre application web dans le panneau Web Apps. Les informations détaillées sur votre application web s’affichent dans un nouveau panneau.
2. Sélectionnez l’option **Paramètres**. Le panneau **Paramètres** s’affiche : il vous permet de modifier votre application Web.
	
	![Page Sauvegardes][ChooseBackupsPage]

3. Choisissez l’option **Sauvegardes** dans le panneau **Paramètres**. Le panneau **Sauvegardes** s’affiche.
	
4. Dans le panneau **Sauvegardes**, choisissez la destination de sauvegarde en sélectionnant un **Compte de stockage** et un **Conteneur**. Ce compte de stockage doit relever du même abonnement que l’application web que vous sauvegardez.
	
	![Sélection d'un compte de stockage][ChooseStorageAccount]
	
5. Sous l’option **Bases de données incluses** du panneau **Sauvegardes**, sélectionnez les bases de données connectées à l’application Web (SQL Server ou MySQL) que vous souhaitez sauvegarder.

	> [AZURE.NOTE]Pour qu’une base de données apparaisse dans cette liste, sa chaîne de connexion doit figurer dans la section **Chaînes de connexion** du panneau **Paramètres d’application web** du portail.
	
6. Dans le panneau **Sauvegardes**, sélectionnez la destination concernée sous **Destination de sauvegarde**. Vous devez choisir un conteneur et un compte de stockage existants.
7. Dans la barre de commandes, cliquez sur **Backup Now**.
	
	![Bouton Backup Now][BackUpNow]
	
	Un message de progression s’affiche au cours du processus de sauvegarde.
	

Vous pouvez à tout moment effectuer une sauvegarde manuelle.

<a name="automatedbackups"></a>
## Configuration de sauvegardes automatisées

1. Dans le panneau **Sauvegardes**, activez l’option **Sauvegarde planifiée**.
	
	![Activation des sauvegardes automatisées][SetAutomatedBackupOn]
	
2. Sélectionnez le compte de stockage dans lequel vous souhaitez sauvegarder votre application web. Ce compte de stockage doit relever du même abonnement que l’application web que vous sauvegardez.
	
	![Sélection d'un compte de stockage][ChooseStorageAccount]
	
3. Dans la zone **Fréquence**, indiquez la fréquence de sauvegarde automatisée de votre choix. Le nombre de jours doit être situé entre 1 et 90 compris (d'une fois par jour à tous les 90 jours).
	
4. Utilisez l’option **Début** pour indiquer la date et l’heure de début des sauvegardes automatisées souhaitées.
	
	> [AZURE.NOTE]Azure stocke les heures de sauvegarde au format UTC, mais les affiche selon l’heure de l’ordinateur que vous utilisez pour afficher le portail.
	
5. Dans la section **Bases de données incluses**, sélectionnez les bases de données connectées à l’application Web (serveur SQL ou MySQL) que vous souhaitez sauvegarder. Pour qu’une base de données apparaisse dans la liste, sa chaîne de connexion doit figurer dans la section **Chaînes de connexion** du panneau **Paramètres d’application web** du portail.
	
	> [AZURE.NOTE]Si vous avez sélectionné une ou plusieurs bases de données dans la sauvegarde et spécifié une fréquence inférieure à 7 jours, vous serez informé du fait que de fréquentes sauvegardes peuvent augmenter les coûts de vos bases de données.
	
6. D’autre part, définissez la valeur **Rétention (jours)** en fonction du nombre de jours de conservation de la sauvegarde.
7. Dans la barre de commandes, cliquez sur le bouton **Enregistrer** pour enregistrer les modifications de votre configuration (ou sélectionnez **Ignorer** pour ne pas les enregistrer).
	
	![Bouton enregistrer][SaveIcon]

<a name="aboutbackups"></a>
## Mode de stockage des sauvegardes

Dès lors que vous avez effectué une ou plusieurs sauvegardes, ces dernières apparaissent dans le panneau **Conteneurs** de votre **Compte de stockage**, ainsi que votre application Web. Dans le **Compte de stockage**, chaque sauvegarde se compose d’un fichier .zip et d’un fichier .xml contenant respectivement les données sauvegardées et un manifeste du contenu du fichier .zip.

Les noms des fichiers de sauvegarde .zip et .xml se composent du nom de votre application web suivi d’un trait de soulignement et de l’heure à laquelle la sauvegarde a eu lieu. L'horodatage comprend la date au format AAAAMMJJ (en chiffres, sans espace) ainsi que l'heure UTC au format 24 heures (par exemple, fabrikam_201402152300.zip). Vous pouvez décompresser ces fichiers pour accéder à vos sauvegardes sans recourir à une restauration de l’application web.

Le fichier XML stocké avec le fichier zip indique le nom du fichier de base de données sous *description_base_données* > *bases_données* > *description_sauvegarde_base_données* > *nom_fichier*.

Le fichier de sauvegarde de base de données est stocké à la racine du fichier .zip. Pour une base de données SQL, il s'agit d'un fichier BACPAC (pas d'extension de fichier) qui peut être importé. Pour créer une base de données SQL basée sur l'exportation du fichier BACPAC, suivez les étapes indiquées dans l'article [Importer un fichier BACPAC pour créer une nouvelle base de données utilisateur](http://technet.microsoft.com/library/hh710052.aspx).

Pour plus d’informations sur la restauration des applications Web (y compris les bases de données) par le biais du portail Azure, consultez [Restaurer une application web dans Azure App Service](web-sites-restore.md).

> [AZURE.NOTE]Toute modification apportée aux fichiers de votre conteneur **websitebackups** peut invalider la sauvegarde et la rendre impossible à restaurer.

<a name="notes"></a>
## Remarques

* Veillez à configurer correctement les chaînes de connexion pour chacune de vos bases de données dans le panneau **Paramètres d’application web** correspondant au panneau **Paramètres** de l’application Web, afin que la fonctionnalité de sauvegarde et de restauration inclue vos bases de données.
* Bien qu’il vous soit possible de sauvegarder plusieurs applications web sur le même compte de stockage, pensez à créer un compte de stockage distinct pour chaque application web afin d’en faciliter la maintenance.

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

<a name="nextsteps"></a>
## Étapes suivantes
Pour plus d’informations sur la restauration des applications Web Azure à partir d’une sauvegarde, consultez [Restaurer une application web dans Azure App Service](web-sites-restore.md).

Pour la prise en main d'Azure, consultez la page [Version d'évaluation gratuite de Microsoft Azure](/pricing/free-trial/).


<a name="moreaboutstorage"></a>
### En savoir plus sur les comptes de stockage

[Présentation des comptes de stockage](../storage-whatis-account.md)

[Création d’un compte de stockage](../storage-create-storage-account/)

[Surveillance d’un compte de stockage](../storage-monitor-storage-account.md)

[Présentation de la facturation du stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx) (en anglais)

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page : [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l'ancien et le nouveau portail, consultez : [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715)

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

<!--HONumber=54--> 