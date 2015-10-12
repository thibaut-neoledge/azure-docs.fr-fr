<properties 
	pageTitle="Restaurer une application web dans Azure App Service" 
	description="Découvrez comment restaurer votre application web à partir d’une sauvegarde." 
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
	ms.date="09/16/2015" 
	ms.author="cephalin"/>

# Restaurer une application web dans Azure App Service

Cet article vous explique comment restaurer une application web que vous avez précédemment sauvegardée à l’aide de la fonctionnalité de sauvegarde d’[App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Pour plus d’informations, consultez la section [Sauvegarder une application web dans Azure App Service](web-sites-backup.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

La fonctionnalité de restauration de Web Apps vous permet de restaurer à la demande un état précédent de votre application web, ou de créer une application web à partir de l’une des sauvegardes de votre application web d’origine. La création d’une application web s’exécutant parallèlement à la dernière version peut se révéler utile à des fins de test A/B.

La fonctionnalité de restauration de Web Apps, accessible à partir du panneau **Sauvegardes** du [portail Azure en version préliminaire](http://portal.azure.com), est uniquement disponible dans les modes Standard et Premium. Pour plus d’informations sur la mise à l’échelle de votre application en utilisant le mode Standard ou Premium, consultez la page [Mise à l’échelle d’une application web dans Azure App Service](web-sites-scale.md). Notez que le mode Premium autorise un plus grand nombre de sauvegardes quotidiennes que le mode Standard.

<a name="PreviousBackup"></a>
## Pour restaurer une application web à partir d’une sauvegarde précédente

1. Dans le panneau **Paramètres** de votre application web dans le portail Azure, cliquez sur l’option **Sauvegardes** pour afficher le panneau **Sauvegardes**. Faites défiler ce panneau et sélectionnez l’un des éléments de sauvegarde en fonction des valeurs **HEURE DE LA SAUVEGARDE** et **STATUT** dans la liste de sauvegardes.
	
	![Sélection de la source de sauvegarde][ChooseBackupSource]
	
2. Sélectionnez **Restaurer maintenant** en haut du panneau **Sauvegardes**.

	![Sélectionner Restaurer maintenant][ChooseRestoreNow]

3. Dans le panneau **Restaurer**, pour restaurer l’application web, vérifiez tous les détails affichés, puis cliquez sur **OK**.
	
Vous pouvez également restaurer votre application web sous la forme d’une nouvelle application web en sélectionnant la section **APPLICATION WEB** du panneau **Restaurer**, puis la section **Créer une application web**.
	
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
	
1. Pour afficher les détails concernant la réussite ou l’échec de la restauration de l’application web, sélectionnez la section **Journal d’audit** du panneau **Parcourir** principal. 
	
	Le panneau **Journal d’audit** répertorie toutes vos opérations, ainsi que le niveau, l’état, la ressource et l’heure qui leur correspondent.
	
2. Faites défiler le panneau pour rechercher les opérations associées à votre application web.
3. Pour visualiser des détails supplémentaires concernant une opération spécifique, sélectionnez cette opération dans la liste.
	
Le panneau de détails affiche les informations disponibles relatives à l’opération.
	
>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
	
## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

<!-- IMAGES -->
[ChooseBackupSource]: ./media/web-sites-restore/01ChooseBackupSource.png
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
 

<!---HONumber=Oct15_HO1-->