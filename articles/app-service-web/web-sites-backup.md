<properties 
	pageTitle="Sauvegarder une application web dans Azure App Service" 
	description="Apprenez à créer des sauvegardes de vos applications Web dans Azure App Service" 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/10/2015" 
	ms.author="cephalin"/>

# Sauvegarder une application web dans Azure App Service


La fonctionnalité de sauvegarde et de restauration d’[Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) vous permet de créer facilement des sauvegardes d’application Web manuelles et automatiques. Vous pouvez restaurer votre application web à une étape précédente ou créer une nouvelle application web basée sur l’une des sauvegardes de votre application d’origine.

Pour plus d’informations sur la restauration d’une application Web Azure à partir d’une sauvegarde, consultez [Restaurer une application Web](web-sites-restore.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

<a name="whatsbackedup"></a>
## Éléments sauvegardés 
Web Apps peut sauvegarder les informations suivantes :

* configuration d’une application web ;
* contenu d’un fichier d’application web ;
* bases de données SQL Azure ou bases de données MySQL connectées à votre application (vous pouvez choisir l'élément à inclure dans votre sauvegarde)

Ces informations sont sauvegardées dans le conteneur et le compte de stockage Azure que vous indiquez.

> [AZURE.NOTE]Chaque sauvegarde représente une copie hors connexion complète de votre application et non une mise à jour incrémentielle.

<a name="requirements"></a>
## Exigences et restrictions

* La fonction de sauvegarde et de restauration implique que le plan App Service se situe au niveau Standard ou un niveau plus élevé. Pour plus d'informations sur l'évolution de votre plan App Service en vue d'utiliser un niveau plus élevé, consultez [Faire évoluer une application Web dans Azure App Service](web-sites-scale.md). Notez que le niveau Premium autorise un plus grand nombre de sauvegardes quotidiennes que le niveau Standard.

* La fonctionnalité de sauvegarde et de restauration requiert un conteneur et un compte de stockage Azure relevant du même abonnement que l’application web que vous sauvegardez. Si vous ne disposez pas encore d’un compte de stockage, vous pouvez en créer un. Pour cela, cliquez sur le **Compte de stockage**, dans le panneau **Sauvegardes** du [portail Azure](http://portal.azure.com), puis, choisissez le **Compte de stockage** et le **Conteneur** dans le panneau **Destination**. Pour plus d'informations sur les comptes de stockage Azure, consultez les [liens](#moreaboutstorage) situés en bas de cet article.

* La fonctionnalité de sauvegarde et de restauration prend en charge jusqu’à 10 Go de contenu de site Web et de base de données. Une erreur est indiquée lorsqu'un dépassement de ce seuil empêche la sauvegarde.

<a name="manualbackup"></a>
## Création d’une sauvegarde manuelle

1. Dans le portail Azure, choisissez votre application web dans le panneau Web Apps. Les informations détaillées sur votre application web s’affichent dans un nouveau panneau.
2. Dans le panneau de votre application, sélectionnez **Paramètres**, puis **Sauvegardes**. Le panneau **Sauvegardes** s’affiche.
	
	![Page Sauvegardes][ChooseBackupsPage]

3. Dans le panneau **Sauvegardes**, cliquez sur **Stockage : Non configuré** pour configurer un compte de stockage.

	![Sélection d'un compte de stockage][ChooseStorageAccount]
	
4. Choisissez la destination de sauvegarde en sélectionnant un **Compte de stockage** et un **Conteneur**. Ce compte de stockage doit relever du même abonnement que l’application web que vous sauvegardez. Si vous le souhaitez, vous pouvez créer un compte de stockage ou un conteneur dans les panneaux respectifs. Quand vous avez terminé, cliquez sur **Sélectionner**.
	
	![Sélection d'un compte de stockage](./media/web-sites-backup/02ChooseStorageAccount1.png)
	
5. Dans le panneau **Configurer les paramètres de sauvegarde** toujours ouvert, cliquez sur **Paramètres de la base de données**, puis sélectionnez les bases de données que vous souhaitez inclure dans les sauvegardes (base de données SQL ou MySQL), puis cliquez sur **OK**.

	![Sélection d'un compte de stockage](./media/web-sites-backup/03ConfigureDatabase.png)

	> [AZURE.NOTE]Pour qu'une base de données apparaisse dans cette liste, sa chaîne de connexion doit figurer dans la section **Chaînes de connexion** du panneau **Paramètres d'application web** du portail.

6. Dans le panneau **Configurer les paramètres de sauvegarde**, cliquez sur **Enregistrer**.
6. Dans le panneau **Sauvegardes**, sélectionnez la destination concernée sous **Destination de sauvegarde**. Vous devez choisir un conteneur et un compte de stockage existants.
7. Dans la barre de commandes du panneau **Sauvegardes**, cliquez sur **Sauvegarder**.
	
	![Bouton Backup Now][BackUpNow]
	
	Un message de progression s’affiche au cours du processus de sauvegarde.
	

Vous pouvez à tout moment effectuer une sauvegarde manuelle.

<a name="automatedbackups"></a>
## Configuration de sauvegardes automatisées

1. Dans le panneau **Sauvegardes**, cliquez sur **Planification : Non configurée**. 

	![Sélection d'un compte de stockage](./media/web-sites-backup/05ScheduleBackup.png)
	
1. Dans le panneau **Paramètres de planification de sauvegarde**, définissez **Sauvegarde planifiée** sur **Activé**, puis configurez la planification de sauvegarde comme vous le souhaitez et cliquez sur **OK**.
	
	![Activation des sauvegardes automatisées][SetAutomatedBackupOn]
	
4. Dans le panneau **Configurer les paramètres de sauvegarde** toujours ouvert, cliquez sur **Paramètres de stockage**, puis choisissez la destination de sauvegarde en sélectionnant un **compte de stockage** et un **conteneur**. Ce compte de stockage doit relever du même abonnement que l’application web que vous sauvegardez. Si vous le souhaitez, vous pouvez créer un compte de stockage ou un conteneur dans les panneaux respectifs. Quand vous avez terminé, cliquez sur **Sélectionner**.
	
	![Sélection d'un compte de stockage](./media/web-sites-backup/02ChooseStorageAccount1.png)
	
5. Dans le panneau **Configurer les paramètres de sauvegarde**, cliquez sur **Paramètres de la base de données**, puis sélectionnez les bases de données que vous souhaitez inclure dans les sauvegardes (base de données SQL ou MySQL), puis cliquez sur **OK**.

	![Sélection d'un compte de stockage](./media/web-sites-backup/03ConfigureDatabase.png)

	> [AZURE.NOTE]Pour qu'une base de données apparaisse dans cette liste, sa chaîne de connexion doit figurer dans la section **Chaînes de connexion** du panneau **Paramètres d'application web** du portail.

6. Dans le panneau **Configurer les paramètres de sauvegarde**, cliquez sur **Enregistrer**.

<a name="notes"></a>
## Remarques

* Veillez à configurer correctement les chaînes de connexion pour chacune de vos bases de données dans le panneau **Paramètres d’application web** correspondant au panneau **Paramètres** de l’application Web, afin que la fonctionnalité de sauvegarde et de restauration inclue vos bases de données.

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

<a name="partialbackups"></a>
## Sauvegarde d'une partie de votre application web uniquement

Parfois, vous ne souhaitez pas sauvegarder tout le contenu de votre application web. Voici quelques exemples :

-	Vous [configurez une sauvegarde hebdomadaire](web-sites-backup.md#configure-automated-backups) de votre application web qui contient du contenu statique qui ne change jamais, comme des anciens billets de blog ou des images.
-	Votre application web a plus de 10 Go de contenu (qui est la quantité maximale que vous pouvez sauvegarder à la fois).
-	Vous ne souhaitez pas sauvegarder les fichiers journaux.

Les sauvegardes partielles vous permettent de choisir exactement les fichiers à sauvegarder.

### Exclusion de fichiers de votre sauvegarde

Pour exclure des fichiers et dossiers de vos sauvegardes, créez un fichier `_backup.filter` dans le dossier wwwroot de votre application web et spécifiez la liste des fichiers et dossiers à exclure. Pour y accéder facilement, vous pouvez utiliser la [Console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console).

Supposons que vous avez une application web qui contient des fichiers journaux et des images statiques provenant d'années précédentes et qui ne vont jamais changer. Vous disposez déjà d'une sauvegarde complète de l'application web qui inclut les anciennes images. Vous voulez désormais effectuer une sauvegarde quotidienne de l'application web, mais vous ne voulez pas payer pour le stockage des fichiers journaux ou des images statiques qui ne changent jamais.

![Dossier des journaux][LogsFolder]
![Dossier images][ImagesFolder]
	
Les étapes ci-dessous vous montrent comment exclure ces fichiers de la sauvegarde.

1. Accédez à `http://{yourapp}.scm.azurewebsites.net/DebugConsole` et identifiez les dossiers que vous souhaitez exclure de vos sauvegardes. Dans cet exemple, vous voulez exclure les fichiers et dossiers suivants affichés dans cette interface utilisateur :

		D:\home\site\wwwroot\Logs
		D:\home\LogFiles
		D:\home\site\wwwroot\Images\2013
		D:\home\site\wwwroot\Images\2014
		D:\home\site\wwwroot\Images\brand.png

	[AZURE.NOTE]La dernière ligne montre que vous pouvez exclure des fichiers individuels, ainsi que des dossiers.

2. Créez un fichier sous le nom `_backup.filter` et placez la liste précédente dans le fichier, mais supprimez `D:\home`. Listez un répertoire ou fichier par ligne. Par conséquent, le contenu du fichier doit ressembler à ce qui suit :

    \\site\\wwwroot\\Logs \\LogFiles \\site\\wwwroot\\Images\\2013 \\site\\wwwroot\\Images\\2014 \\site\\wwwroot\\Images\\brand.png

3. Téléchargez ce fichier vers le répertoire `D:\home\site\wwwroot` de votre site en utilisant [ftp](web-sites-deploy.md#ftp) ou toute autre méthode. Si vous le souhaitez, vous pouvez créer le fichier directement dans `http://{yourapp}.scm.azurewebsites.net/DebugConsole` et y insérer le contenu.

4. Exécutez des sauvegardes comme vous le faites normalement, [manuellement](#create-a-manual-backup) ou [automatiquement](#configure-automated-backups).

Dorénavant, tous les fichiers et dossiers spécifiés dans `_backup.filter` seront exclus de la sauvegarde. Dans cet exemple, les fichiers journaux et les fichiers image 2013 et 2014 ne seront plus sauvegardés, ainsi que brand.png.

>[AZURE.NOTE]Pour restaurer les sauvegardes partielles de votre site, procédez de la même façon que pour [restaurer une sauvegarde régulière](web-sites-restore.md). Le processus de restauration fait ce qu'il faut.
>
>Lorsqu'une sauvegarde complète est restaurée, tout le contenu sur le site est remplacé par tout ce qui se trouve dans la sauvegarde. Si un fichier se trouve sur le site, mais pas dans la sauvegarde, il est supprimé. Mais lorsqu'une sauvegarde partielle est restaurée, tout contenu qui se trouve dans l'un des répertoires exclus, ou n'importe quel fichier exclu, est conservé tel quel.

<a name="aboutbackups"></a>

## Mode de stockage des sauvegardes

Dès lors que vous avez effectué une ou plusieurs sauvegardes de votre application web, celles-ci apparaissent dans le panneau **Conteneurs** de votre compte de stockage, tout comme votre application web. Dans le compte de stockage, chaque sauvegarde se compose d'un fichier .zip et d'un fichier .xml contenant respectivement les données sauvegardées et un manifeste du contenu du fichier .zip. Vous pouvez décompresser et parcourir ces fichiers si vous souhaitez accéder à vos sauvegardes sans réellement effectuer une restauration d'application web.

La sauvegarde de base de données pour l'application web est stockée dans la racine du fichier .zip. Pour une base de données SQL, il s'agit d'un fichier BACPAC (pas d'extension de fichier) qui peut être importé. Pour créer une base de données SQL à partir de l'exportation BACPAC, consultez [Importer un fichier BACPAC pour créer une nouvelle base de données utilisateur](http://technet.microsoft.com/library/hh710052.aspx).

> [AZURE.WARNING]Toute modification apportée aux fichiers de votre conteneur **websitebackups** peut invalider la sauvegarde et la rendre impossible à restaurer.

<a name="nextsteps"></a>
## Étapes suivantes
Pour plus d'informations sur la restauration d'une application web à partir d'une sauvegarde, consultez [Restaurer une application web dans Azure App Service](web-sites-restore.md).

Pour la prise en main d'Azure, consultez la page [Version d'évaluation gratuite de Microsoft Azure](/pricing/free-trial/).

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).

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
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
 

<!---HONumber=AcomDC_1217_2015-->