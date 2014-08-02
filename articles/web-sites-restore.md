<properties linkid="web-sites-restore" urlDisplayName="Restore a Microsoft Azure web site" pageTitle="Restore a Microsoft Azure web site" metaKeywords="Azure Web Sites, Restore, restoring" description="Learn how to restore your Azure web sites from backup." metaCanonical="" services="web-sites" documentationCenter="" title="Restore a Microsoft Azure web site" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos" />

Restauration d'un site Web Microsoft Azure
==========================================

Cet article vous explique comment restaurer un site Web précédemment sauvegardé à l'aide de la fonction de sauvegarde des sites Web Azure. Pour plus d'informations, consultez la rubrique [Sauvegarde des sites Web Microsoft Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-backup/).

La fonction de restauration des sites Web Azure vous permet de restaurer votre site Web, à la demande, à une étape précédente ou de créer un nouveau site Web basé sur des sauvegardes de votre site d'origine. La création d'un site Web s'exécutant parallèlement à la dernière version peut être utile à des fins de test A/B.

La fonction Restaurer, accessible dans l'onglet Sauvegardes du portail de sites Web Azure, est uniquement disponible en mode Standard.

Dans cet article
----------------

-   [Pour restaurer un site Web Azure à partir d'une sauvegarde précédente](#PreviousBackup)
-   [Pour restaurer un site Web Azure directement depuis un compte de stockage](#StorageAccount)
-   [Sélection des paramètres de restauration de votre site Web et exécution de l'opération de restauration](#RestoreSettings)
-   [Affichage des journaux des opérations](#OperationLogs)

## Pour restaurer un site Web à partir d'une sauvegarde précédente

1.  Sur l'onglet **Sauvegardes**, cliquez sur **Restore Now** dans la barre de commandes située en bas de la page du portail. La boîte de dialogue **Restore Now** s'affiche.

    ![Sélection de la source de sauvegarde](./media/web-sites-restore/01ChooseBackupSource.png)

2.  Sous **Choisissez la source de la sauvegarde**, sélectionnez **Sauvegarde précédente pour ce site Web**.
3.  Sélectionnez la date de la sauvegarde que vous souhaitez restaurer, puis cliquez sur la flèche droite pour continuer.
4.  Suivez la procédure de la section [Sélection des paramètres de restauration de votre site Web](#RestoreSettings) située un peu plus loin dans cet article.

## Pour restaurer un site Web Azure directement depuis un compte de stockage

1.  Sur l'onglet **Sauvegardes**, cliquez sur **Restore Now** dans la barre de commandes située en bas de la page du portail. La boîte de dialogue **Restore Now** s'affiche.

    ![Sélection de la source de sauvegarde](./media/web-sites-restore/01ChooseBackupSource.png)

2.  Sous **Choisissez la source de la sauvegarde**, sélectionnez **Fichier de compte de stockage**. Vous pouvez directement y indiquer l'URL du fichier de compte de stockage ou cliquer sur l'icône du dossier pour accéder au compte de stockage d'objets blob et indiquer le fichier de sauvegarde. Cet exemple utilise l'icône du dossier.

    ![Fichier de compte de stockage](./media/web-sites-restore/02StorageAccountFile.png)

3.  Cliquez sur l'icône du dossier pour ouvrir la boîte de dialogue **Parcourir le stockage cloud**.

    ![Parcourir le stockage cloud](./media/web-sites-restore/03BrowseCloudStorage.png)

4.  Développez le nom du compte de stockage que vous souhaitez utiliser, puis sélectionnez **websitebackups**, qui contient vos sauvegardes.
5.  Sélectionnez le fichier .zip contenant la sauvegarde que vous souhaitez restaurer, puis cliquez sur **Ouvrir**.
6.  Le fichier de compte de stockage est sélectionné et affiche la zone correspondant au compte de stockage. Cliquez sur la flèche droite pour continuer.

    ![Fichier de compte de stockage sélectionné](./media/web-sites-restore/04StorageAccountFileSelected.png)

7.  Continuez avec la section suivante, [Sélection des paramètres de restauration de votre site Web et exécution de l'opération de restauration](#RestoreSettings).

## Sélection des paramètres de restauration de votre site Web et exécution de l'opération de restauration 1. Sous **Choisissez les paramètres de restauration de votre site Web**, **Restore To**, sélectionnez **Site Web actuel** ou **Nouvelle instance de site Web**.

    ![Choisissez les paramètres de restauration de votre site Web][ChooseRestoreSettings]

    Si vous sélectionnez **Site Web actuel**, le site Web existant est remplacé par la sauvegarde sélectionnée (restauration destructrice). Toutes les modifications apportées au site Web depuis la sélection de la sauvegarde sont définitivement supprimées et l'opération de restauration ne peut être annulée. Lors de l'opération de restauration, votre site Web actuel est momentanément indisponible et vous en êtes averti.

    Si vous sélectionnez **Nouvelle instance de site Web**, un nouveau site Web est créé au même emplacement avec le nom que vous indiquez. (Par défaut, le nouveau nom est **restored-***oldWebSiteName*.) 

    Le site que vous restaurez présente le même contenu et la même configuration que ceux situés dans le portail du site d'origine. Il comprend toutes les bases de données que vous avez choisies d'inclure à l'étape suivante.

1.  Si vous souhaitez restaurer une base de données en même temps que votre site Web, sous **Bases de données incluses**, sélectionnez le nom du serveur de base de données vers lequel vous souhaitez restaurer la base de données en utilisant le menu déroulant situé sous **Restore To**. Vous pouvez également choisir de créer un nouveau serveur de base de données ou sélectionner **Don't Restore** pour ne pas restaurer la base de données (option par défaut).

    Après avoir choisi le nom du serveur, indiquez le nom de la base de données cible à restaurer dans la zone **Nom de la base de données**.

    Si votre restauration comprend une ou plusieurs bases de données, vous pouvez sélectionner **Ajuster automatiquement les chaînes de connexion** pour mettre à jour les chaînes de connexion stockées dans la sauvegarde afin qu'elles pointent vers la nouvelle base de données ou le serveur de base de données, le cas échéant. Vérifiez le bon fonctionnement des bases de données une fois la restauration terminée.

    ![Sélection de l'hôte du serveur de base de données](./media/web-sites-restore/06ChooseDBServer.png)

    > [WACOM.NOTE] Vous ne pouvez pas restaurer une base de données SQL portant le même nom que le serveur SQL. Vous devez sélectionner un autre nom de base de données ou un autre hôte de serveur SQL vers lequel restaurer la base de données.

    > [WACOM.NOTE] Vous pouvez restaurer une base de données MySQL portant le même nom vers le même serveur, mais le contenu stocké dans la base de données MySQL sera effacé.

2.  Si vous choisissez de restaurer une base de données existante, vous devez indiquer un nom d'utilisateur et un mot de passe. Si vous choisissez de restaurer une nouvelle base de données, vous devez indiquer son nom :

    ![Restauration vers une nouvelle base de données SQL](./media/web-sites-restore/07RestoreToNewSQLDB.png)

    Cliquez sur la flèche droite pour continuer.

3.  Si vous choisissez de créer une nouvelle base de données, vous devez indiquer les informations d'identification et autres données de configuration initiales de la base de données dans la boîte de dialogue suivante. Le présent exemple illustre une nouvelle base de données SQL. (Les options d'une nouvelle base de données MySQL diffèrent légèrement.)

    ![Paramètres de la nouvelle base de données SQL](./media/web-sites-restore/08NewSQLDBConfig.png)

4.  Cliquez sur la coche pour démarrer l'opération de restauration. Une fois cette opération terminée, la nouvelle instance du site Web (s'il s'agit de l'option de restauration choisie) s'affiche dans la liste des sites Web du portail.

    ![Site Web Contoso restauré](./media/web-sites-restore/09RestoredContosoWebSite.png)

## Affichage des journaux des opérations

1.  Pour afficher les détails relatifs au déroulement de l'opération de restauration, accédez à l'onglet Tableau de bord du site Web. Dans la section **Aperçu rapide**, sous **Services de gestion**, cliquez sur **Journaux des opérations**.

    ![Tableau de bord - Lien Journaux des opérations](./media/web-sites-restore/10DashboardOperationLogsLink.png)

2.  Vous accédez à la page **Journaux des opérations** du portail des services de gestion où vous pouvez afficher le jour correspondant à votre opération de restauration dans la liste des journaux des opérations :

    ![Page Journaux des opérations des services de gestion](./media/web-sites-restore/11ManagementServicesOperationLogsList.png)

3.  Pour afficher les détails de l'opération, sélectionnez-la dans la liste, puis cliquez sur le bouton **Détails** de la barre de commandes.

    ![Bouton Détails](./media/web-sites-restore/12DetailsButton.png)

    La fenêtre **Détails de l'opération** s'ouvre et affiche le contenu copiable du fichier journal :

    ![Détails de l'opération](./media/web-sites-restore/13OperationDetails.png)


