---
title: Restauration d&quot;une application dans Azure App Service
description: "Découvrez comment restaurer votre application à partir d&quot;une sauvegarde."
services: app-service
documentationcenter: 
author: cephalin
manager: wpickett
editor: jimbe
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8136df6507f1d79872a286abb5810e595c9425ff


---
# <a name="restore-an-app-in-azure"></a>Restauration d'une application dans Azure App Service
Cet article vous explique comment restaurer une application dans [Azure App Service](../app-service/app-service-value-prop-what-is.md) que vous avez précédemment sauvegardée (voir [Sauvegarde de votre application dans Azure](web-sites-backup.md)). Vous pouvez restaurer votre application avec ses bases de données liées (base de données SQL ou MySQL) à la demande à un état antérieur ou créer une application à partir de la sauvegarde de votre application d’origine. La création d'une application s'exécutant parallèlement à la dernière version peut se révéler utile à des fins de test A/B.

La restauration à partir de sauvegardes est disponible pour des applications exécutées dans les niveaux **Standard** et **Premium**. Pour en savoir plus sur la mise à l’échelle de votre application, consultez [Mise à l’échelle d’une application web dans Microsoft Azure App Service](web-sites-scale.md). Notez que le niveau **Premium** autorise un plus grand nombre de sauvegardes quotidiennes que le niveau **Standard**.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Restauration d’une application à partir d’une sauvegarde existante
1. Dans le panneau **Paramètres** de votre application dans le portail Azure, cliquez sur **Sauvegardes** pour afficher le panneau **Sauvegardes**. Puis cliquez sur **Restaurer maintenant** dans la barre de commandes.
   
    ![Sélectionner Restaurer maintenant][ChooseRestoreNow]
2. Dans le panneau **Restaurer** , sélectionnez tout d'abord la source de la sauvegarde.
   
    ![](./media/web-sites-restore/021ChooseSource.png)
   
    L’option de **sauvegarde d’une application** vous montre toutes les sauvegardes existantes de l’application actuelle, et vous pouvez facilement sélectionner une.
    L’option de **stockage** vous permet de sélectionner un fichier ZIP de sauvegarde quelconque à partir de n’importe quel compte de stockage Azure et conteneur existants dans votre abonnement.
    Si vous essayez de restaurer une sauvegarde d’une autre application, utilisez l’option **Stockage** .
3. Ensuite, spécifiez la destination de la restauration de l'application dans **Destination de restauration**.
   
    ![](./media/web-sites-restore/022ChooseDestination.png)
   
   > [!WARNING]
   > Si vous choisissez **Remplacer**, toutes les données existantes dans votre application actuelle seront effacées. Avant de cliquer sur **OK**, vérifiez que c'est bien ce que vous voulez faire.
   > 
   > 
   
    Vous pouvez sélectionner **Application existante** pour restaurer la sauvegarde d'une application vers une autre application dans le même groupe de ressources. Avant d'utiliser cette option, vous devez avoir créé une autre application dans votre groupe de ressources avec mise en miroir de la configuration de la base de données sur celle définie dans la sauvegarde de l'application.
4. Cliquez sur **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Télécharger ou supprimer une sauvegarde à partir d’un compte de stockage
1. Dans le panneau **Parcourir** principal du portail Azure, sélectionnez **Comptes de stockage**.
   
    La liste de vos comptes de stockage existants s’affiche.
2. Sélectionnez le compte de stockage contenant la sauvegarde que vous souhaitez télécharger ou supprimer.
   
    Le panneau du compte de stockage s’affiche.
3. Dans le panneau du compte de stockage, sélectionnez le conteneur que vous souhaitez.
   
    ![Afficher les conteneurs][ViewContainers]
4. Sélectionnez le fichier de sauvegarde à télécharger ou à supprimer.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Cliquez sur **Télécharger** ou **Supprimer** selon ce que vous souhaitez faire.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Surveillance d’une opération de restauration
1. Pour afficher les détails concernant la réussite ou l’échec de l’opération de restauration de l’application, naviguez jusqu’au panneau **Journal d’activité** dans le portail Azure.
   
    Le panneau **Journal d’activité** répertorie toutes vos opérations, ainsi que le niveau, l’état, la ressource et l’heure qui leur correspondent.
2. Faites défiler la liste pour trouver l’opération de restauration souhaitée et cliquez dessus.

Le panneau de détails affiche les informations disponibles relatives à l’opération de restauration.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez également sauvegarder et restaurer des applications App Service à l’aide de l’API REST. Pour cela, consultez [Utilisation de REST pour sauvegarder et restaurer des applications App Service](websites-csm-backup.md).

> [!NOTE]
> Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
> 
> 

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



<!--HONumber=Nov16_HO3-->


