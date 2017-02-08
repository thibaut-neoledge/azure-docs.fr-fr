---
title: Sauvegarde de votre application dans Azure
description: "Apprenez à créer des sauvegardes de vos applications Web dans Azure App Service."
services: app-service
documentationcenter: 
author: cephalin
manager: wpickett
editor: jimbe
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: b00c320eeb1e8b30a0fad9634844ceeca60d1c29


---
# <a name="back-up-your-app-in-azure"></a>Sauvegarde de votre application dans Azure
La fonctionnalité de sauvegarde et de restauration d’ [Azure App Service](../app-service/app-service-value-prop-what-is.md) vous permet de créer facilement des sauvegardes d’applications manuelles et automatiques. Vous pouvez restaurer votre application à une étape précédente ou créer une application basée sur l’une des sauvegardes de votre application d’origine. 

Pour plus d’informations sur la restauration d’une application à partir d’une sauvegarde, consultez [Restauration d’une application dans Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Éléments sauvegardés
App Service peut sauvegarder les informations suivantes :

* la configuration d’une application ;
* le contenu d’un fichier ;
* toute base de données SQL Azure ou base de données MySQL Azure (ClearDB) connectée à votre application (vous pouvez choisir l’élément à inclure dans votre sauvegarde).

Ces informations sont sauvegardées dans le conteneur et le compte de stockage Azure que vous indiquez. 

> [!NOTE]
> Chaque sauvegarde représente une copie hors connexion complète de votre application et non une mise à jour incrémentielle.
> 
> 

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Exigences et restrictions
* La fonctionnalité de sauvegarde et de restauration implique que le plan App Service soit de type **Standard** ou à un niveau plus élevé. Pour plus d'informations sur la mise à l’échelle de votre plan App Service en vue d'utiliser un niveau plus élevé, consultez [Mise à l’échelle d’une application web dans Microsoft Azure App Service](web-sites-scale.md). Notez que le niveau **Premium** autorise un plus grand nombre de sauvegardes quotidiennes que le niveau **Standard**.
* Vous avez besoin d’un compte de stockage Azure et d’un conteneur dans le même abonnement que l’application que vous souhaitez sauvegarder. Pour plus d'informations sur les comptes de stockage Azure, consultez les [liens](#moreaboutstorage) situés en bas de cet article.
* Les sauvegardes peuvent contenir jusqu’à 10 Go de contenu d’applications et de bases de données. Une erreur se produit si la taille de la sauvegarde dépasse cette limite. 

<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Création d’une sauvegarde manuelle
1. Dans le [portail Azure](https://portal.azure.com), accédez au panneau de votre application, sélectionnez **Paramètres**, puis **Sauvegardes**. Le panneau **Sauvegardes** s’affiche.
   
    ![Page Sauvegardes][ChooseBackupsPage]
   
   > [!NOTE]
   > Si le message ci-dessous s’affiche, cliquez dessus pour mettre à niveau votre plan App Service avant de pouvoir poursuivre avec les sauvegardes.
   > Consultez la page [Mise à l’échelle d’une application web dans Microsoft Azure App Service](web-sites-scale.md) pour plus d’informations.  
   > ![Sélection d'un compte de stockage](./media/web-sites-backup/01UpgradePlan.png)
   > 
   > 
2. Dans le panneau **Sauvegardes**, cliquez sur **Stockage : Non configuré** pour configurer un compte de stockage.
   
    ![Sélection d'un compte de stockage][ChooseStorageAccount]
3. Choisissez la destination de sauvegarde en sélectionnant un **Compte de stockage** et un **Conteneur**. Ce compte de stockage doit relever du même abonnement que l’application que vous souhaitez sauvegarder. Si vous le souhaitez, vous pouvez créer un compte de stockage ou un conteneur dans les panneaux respectifs. Quand vous avez terminé, cliquez sur **Sélectionner**.
   
    ![Sélection d'un compte de stockage](./media/web-sites-backup/02ChooseStorageAccount1.png)
4. Dans le panneau **Configurer les paramètres de sauvegarde** toujours ouvert, cliquez sur **Paramètres de la base de données**, sélectionnez les bases de données que vous souhaitez inclure dans les sauvegardes (base de données SQL ou MySQL), puis cliquez sur **OK**.  
   
    ![Sélection d'un compte de stockage](./media/web-sites-backup/03ConfigureDatabase.png)
   
   > [!NOTE]
   > Pour qu’une base de données apparaisse dans cette liste, sa chaîne de connexion doit figurer dans la section **Chaînes de connexion** du panneau **Paramètres d’application** de votre application.
   > 
   > 
5. Dans le panneau **Configurer les paramètres de sauvegarde**, cliquez sur **Enregistrer**.    
6. Dans la barre de commandes du panneau **Sauvegardes**, cliquez sur **Sauvegarder**.
   
    ![Bouton Backup Now][BackUpNow]
   
    Un message de progression s’affiche au cours du processus de sauvegarde.

Après avoir configuré un compte de stockage et un conteneur pour les sauvegardes, vous pouvez effectuer une sauvegarde manuelle à tout moment.  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Configuration de sauvegardes automatisées
1. Dans le panneau **Sauvegardes**, cliquez sur **Planification : Non configurée**. 
   
    ![Sélection d'un compte de stockage](./media/web-sites-backup/05ScheduleBackup.png)
2. Dans le panneau **Paramètres de planification de la sauvegarde**, définissez **Sauvegarde planifiée** sur **Activé**, puis configurez la planification de sauvegarde comme vous le souhaitez et cliquez sur **OK**.
   
    ![Activation des sauvegardes automatisées][SetAutomatedBackupOn]
3. Dans le panneau **Configurer les paramètres de sauvegarde** toujours ouvert, cliquez sur **Paramètres de stockage**, puis choisissez la destination de sauvegarde en sélectionnant un **compte de stockage** et un **conteneur**. Ce compte de stockage doit relever du même abonnement que l’application que vous souhaitez sauvegarder. Si vous le souhaitez, vous pouvez créer un compte de stockage ou un conteneur dans les panneaux respectifs. Quand vous avez terminé, cliquez sur **Sélectionner**.
   
    ![Sélection d'un compte de stockage](./media/web-sites-backup/02ChooseStorageAccount1.png)
4. Dans le panneau **Configurer les paramètres de sauvegarde**, cliquez sur **Paramètres de la base de données**, sélectionnez les bases de données que vous souhaitez inclure dans les sauvegardes (base de données SQL ou MySQL), puis cliquez sur **OK**.  
   
    ![Sélection d'un compte de stockage](./media/web-sites-backup/03ConfigureDatabase.png)
   
   > [!NOTE]
   > Pour qu’une base de données apparaisse dans cette liste, sa chaîne de connexion doit figurer dans la section **Chaînes de connexion** du panneau **Paramètres d’application** de votre application.
   > 
   > 
5. Dans le panneau **Configurer les paramètres de sauvegarde**, cliquez sur **Enregistrer**.    

<a name="partialbackups"></a>

## <a name="backup-just-part-of-your-app"></a>Sauvegarde d’une partie de votre application uniquement
Parfois, vous ne souhaitez pas sauvegarder tout le contenu de votre application. Voici quelques exemples :

* Vous [configurez une sauvegarde hebdomadaire](web-sites-backup.md#configure-automated-backups) de votre application qui contient du contenu statique qui ne change jamais, comme des anciens billets de blog ou des images.
* Votre application a plus de 10 Go de contenu (qui est la quantité maximale que vous pouvez sauvegarder à la fois).
* Vous ne souhaitez pas sauvegarder les fichiers journaux.

Les sauvegardes partielles vous permettent de choisir exactement les fichiers à sauvegarder.

### <a name="exclude-files-from-your-backup"></a>Exclusion de fichiers de votre sauvegarde
Pour exclure des fichiers et dossiers de vos sauvegardes, créez un fichier `_backup.filter` dans le dossier D:\home\site\wwwroot de votre application et spécifiez la liste des fichiers et dossiers à exclure. Pour y accéder facilement, vous pouvez utiliser la [Console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console). 

Supposons que vous avez une application qui contient des fichiers journaux et des images statiques créés il y a plusieurs années et qui ne seront jamais modifiés. Vous disposez déjà d'une sauvegarde complète de l'application qui comprend les anciennes images. Vous voulez désormais effectuer une sauvegarde quotidienne de l'application, mais vous ne voulez pas payer pour le stockage des fichiers journaux ou des images statiques qui ne changent jamais.

![Dossier des journaux][LogsFolder]
![Dossier des images][ImagesFolder]

Les étapes ci-dessous vous montrent comment exclure ces fichiers de la sauvegarde.

1. Accédez à `http://{yourapp}.scm.azurewebsites.net/DebugConsole` et identifiez les dossiers que vous souhaitez exclure de vos sauvegardes. Dans cet exemple, vous voulez exclure les fichiers et dossiers suivants affichés dans cette interface utilisateur :
   
        D:\home\site\wwwroot\Logs
        D:\home\LogFiles
        D:\home\site\wwwroot\Images\2013
        D:\home\site\wwwroot\Images\2014
        D:\home\site\wwwroot\Images\brand.png
   
    [AZURE.NOTE] La dernière ligne montre que vous pouvez exclure des fichiers individuels, ainsi que des dossiers.
2. Créez un fichier sous le nom `_backup.filter` et placez la liste précédente dans le fichier, mais supprimez `D:\home`. Listez un répertoire ou fichier par ligne. Par conséquent, le contenu du fichier doit ressembler à ce qui suit :
   
    \site\wwwroot\Logs  \LogFiles  \site\wwwroot\Images\2013  \site\wwwroot\Images\2014  \site\wwwroot\Images\brand.png
3. Téléchargez ce fichier vers le répertoire `D:\home\site\wwwroot\` de votre site en utilisant [ftp](web-sites-deploy.md#ftp) ou toute autre méthode. Si vous le souhaitez, vous pouvez créer le fichier directement dans `http://{yourapp}.scm.azurewebsites.net/DebugConsole` et y insérer le contenu.
4. Exécutez des sauvegardes comme vous le faites normalement, [manuellement](#create-a-manual-backup) ou [automatiquement](#configure-automated-backups).

Dorénavant, tous les fichiers et dossiers spécifiés dans `_backup.filter` seront exclus de la sauvegarde. Dans cet exemple, les fichiers journaux et les fichiers image 2013 et 2014 ne seront plus sauvegardés, ainsi que brand.png.

> [!NOTE]
> Pour restaurer les sauvegardes partielles de votre site, procédez de la même façon que pour [restaurer une sauvegarde régulière](web-sites-restore.md). Le processus de restauration fait ce qu'il faut.
> 
> Lorsqu'une sauvegarde complète est restaurée, tout le contenu sur le site est remplacé par tout ce qui se trouve dans la sauvegarde. Si un fichier se trouve sur le site, mais pas dans la sauvegarde, il est supprimé. Mais lorsqu'une sauvegarde partielle est restaurée, tout contenu qui se trouve dans l'un des répertoires exclus, ou n'importe quel fichier exclu, est conservé tel quel.
> 
> 

<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Mode de stockage des sauvegardes
Dès lors que vous avez effectué une ou plusieurs sauvegardes de votre application, celles-ci apparaissent dans le panneau **Conteneurs** de votre compte de stockage, tout comme votre application. Dans le compte de stockage, chaque sauvegarde se compose d'un fichier .zip et d'un fichier .xml contenant respectivement les données sauvegardées et un manifeste du contenu du fichier .zip. Vous pouvez décompresser et parcourir ces fichiers si vous souhaitez accéder à vos sauvegardes sans réellement effectuer une restauration d'application.

La sauvegarde de base de données pour l'application est stockée dans la racine du fichier .zip. Pour une base de données SQL, il s'agit d'un fichier BACPAC (pas d'extension de fichier) qui peut être importé. Pour créer une nouvelle base de données SQL en fonction de l'exportation de BACPAC, consultez [Importer un fichier BACPAC pour créer une nouvelle base de données utilisateur](http://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> Toute modification apportée aux fichiers de votre conteneur **websitebackups** peut invalider la sauvegarde et la rendre impossible à restaurer.
> 
> 

<a name="nextsteps"></a>

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la restauration d’une application à partir d’une sauvegarde, consultez [Restauration d’une application dans Azure](web-sites-restore.md). Vous pouvez également sauvegarder et restaurer des applications App Service à l’aide de l’API REST. Pour cela, consultez [Utilisation de REST pour sauvegarder et restaurer des applications App Service](websites-csm-backup.md).

> [!NOTE]
> Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](https://azure.microsoft.com/try/app-service/), où vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
> 
> 

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




<!--HONumber=Jan17_HO3-->


