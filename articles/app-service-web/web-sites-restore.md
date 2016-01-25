<properties 
	pageTitle="Restauration d'une application dans Azure App Service" 
	description="Découvrez comment restaurer votre application à partir d'une sauvegarde." 
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
	ms.date="01/07/2015" 
	ms.author="cephalin"/>

# Restauration d'une application dans Azure App Service

Cet article vous explique comment restaurer une application App Service que vous avez précédemment sauvegardée à l'aide de la fonctionnalité de sauvegarde d'[App Service](app-service-value-prop-what-is). Pour en savoir plus, consultez [Sauvegardes App Service](web-sites-backup.md).

La fonctionnalité de restauration App Service vous permet de restaurer votre application avec ses bases de données liées (base de données SQL ou MySQL) à la demande à un état antérieur ou de créer une application à partir de la sauvegarde de votre application d'origine. La création d'une application s'exécutant parallèlement à la dernière version peut se révéler utile à des fins de test A/B.

La fonctionnalité de restauration App Service, accessible dans le panneau **Sauvegardes** du [portail Azure](http://portal.azure.com), n'est disponible que dans les niveaux de tarification Standard et Premium. Pour plus d'informations sur la mise à l'échelle de votre application en utilisant le niveau Standard ou Premium, consultez la page [Mise à l'échelle d'une application dans Azure App Service](web-sites-scale.md). Notez que le niveau Premium autorise un plus grand nombre de sauvegardes quotidiennes que le niveau Standard.

<a name="PreviousBackup"></a>
## Pour restaurer une application à partir d'une sauvegarde précédente

1. Dans le panneau **Paramètres** de votre application dans le portail Azure, cliquez sur **Sauvegardes** pour afficher le panneau **Sauvegardes**. Puis cliquez sur **Restaurer maintenant** dans la barre de commandes. 
	
	![Sélectionner Restaurer maintenant][ChooseRestoreNow]

3. Dans le panneau **Restaurer**, sélectionnez tout d'abord la source de la sauvegarde.

	![](./media/web-sites-restore/021ChooseSource.png)
	
	L'option **Sauvegarde d'une application** affiche toutes les sauvegardes qui sont créés directement par l'application elle-même, puisque ce sont les seules dont les applications ont connaissance. Vous pouvez facilement en sélectionner une. L'option **Stockage** vous permet de sélectionner le fichier ZIP de la sauvegarde dans le compte de stockage et le conteneur configurés dans votre panneau **Sauvegardes**. S'il existe des fichiers de sauvegarde à partir d'autres applications dans le conteneur, vous pouvez choisir de les restaurer également.

4. Ensuite, spécifiez la destination de la restauration de l'application dans **Destination de restauration**.

	![](./media/web-sites-restore/022ChooseDestination.png)
	
	>[AZURE.WARNING]Si vous choisissez **Remplacer**, toutes les données relatives à votre application existante seront effacées. Avant de cliquer sur **OK**, vérifiez que c'est bien ce que vous voulez faire.
	
	Vous pouvez sélectionner **Application existante** pour restaurer la sauvegarde d'une application vers une autre application dans le même groupe de ressources. Avant d'utiliser cette option, vous devez avoir créé une autre application dans votre groupe de ressources avec mise en miroir de la configuration de la base de données sur celle définie dans la sauvegarde de l'application.
	
5. Cliquez sur **OK**.

<a name="StorageAccount"></a>
## Télécharger ou supprimer une sauvegarde à partir d’un compte de stockage
	
1. Dans le panneau **Parcourir** principal du portail Azure, sélectionnez **Comptes de stockage**.
	
	La liste de vos comptes de stockage existants s’affiche.
	
2. Sélectionnez le compte de stockage contenant la sauvegarde que vous souhaitez télécharger ou supprimer.
	
	Le panneau **STOCKAGE** s’affiche.

3. Sélectionnez la section **Conteneurs** du panneau **STOCKAGE** pour afficher le panneau **Conteneurs**.
	
	La liste des conteneurs s’affiche. Cette liste affiche également l’URL et la date de la dernière modification de ce conteneur.
	
	![Afficher les conteneurs][ViewContainers]

4. Sélectionnez le conteneur dans la liste pour visualiser le panneau qui répertorie des noms de fichier, ainsi que la taille de chaque fichier.
	
5. Lorsque vous sélectionnez un fichier, vous pouvez choisir l’option **Télécharger** ou **Supprimer**. Notez qu’il existe deux principaux types de fichier : les fichiers .zip et les fichiers .xml.

<a name="OperationLogs"></a>
## Afficher les journaux d’audit
	
1. Pour afficher les détails concernant la réussite ou l'échec de la restauration de l'application, sélectionnez la section **Journal d'audit** du panneau **Parcourir** principal. 
	
	Le panneau **Journal d’audit** répertorie toutes vos opérations, ainsi que le niveau, l’état, la ressource et l’heure qui leur correspondent.
	
2. Faites défiler le panneau pour rechercher les opérations associées à votre application.
3. Pour visualiser des détails supplémentaires concernant une opération spécifique, sélectionnez cette opération dans la liste.
	
Le panneau de détails affiche les informations disponibles relatives à l’opération.
	
>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Étapes suivantes

Vous pouvez également sauvegarder et restaurer des applications App Service à l’aide de l’API REST. Pour cela, consultez [Utiliser REST pour sauvegarder et restaurer des applications App Service](websites-csm-backup.md).

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).

<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
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
 

<!---HONumber=AcomDC_0114_2016-->