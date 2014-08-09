<properties linkid="web-sites-backup" urlDisplayName="Azure Web Sites Backups" pageTitle="Azure Web Sites Backups" metaKeywords="Azure Web Sites, Backups" description="Learn how to create backups of your Azure web sites." metaCanonical="" services="web-sites" documentationCenter="" title="Azure Web Sites Backups" authors="timamm" solutions="" manager="paulettm" editor="mollybos" />

Sauvegarde des sites Web Azure
==============================

La fonction de sauvegarde et de restauration des sites Web Azure vous permet de facilement créer des sauvegardes de sites Web, manuellement ou automatiquement. Vous pouvez restaurer votre site Web à une étape précédente ou créer un nouveau site Web basé sur des sauvegardes de votre site d'origine.

Pour plus d'informations sur la restauration d'un site Web Azure depuis une sauvegarde, consultez la rubrique [Restauration d'un site Web Azure](http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-restore/).

Dans cet article
----------------

-   [Éléments sauvegardés](#whatsbackedup)
-   [Exigences et restrictions](#requirements)
-   [Pour créer une sauvegarde manuelle](#manualbackup)
-   [Pour configurer des sauvegardes automatisées](#automatedbackups)
-   [Stockage des sauvegardes](#aboutbackups)
-   [Remarques](#notes)
-   [Étapes suivantes](#nextsteps)
    -   [En savoir plus sur les comptes de stockage](#moreaboutstorage)

## Éléments sauvegardés - Les sites Web Azure sauvegardent ce qui suit

-   Configuration du site Web
-   Contenu du fichier du site Web
-   Serveurs SQL ou bases de données MySQL connectés à votre site (vous pouvez choisir l'élément à inclure dans votre sauvegarde)

Ces informations sont sauvegardées dans le compte de stockage Azure que vous indiquez.

> [WACOM.NOTE] Chaque sauvegarde correspond à une copie hors ligne complète de votre site Web et non à une mise à jour incrémentielle.

## Exigences et restrictions

-   La fonction de sauvegarde et de restauration implique que le site se situe au niveau Standard. Pour plus d'informations sur la mise à l'échelle de votre site Web au niveau Standard, consultez la rubrique [Mise à l'échelle de sites Web](http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-scale/).

-   La fonction de sauvegarde et de restauration requiert un compte de stockage Azure relevant du même abonnement que le site Web que vous sauvegardez. À défaut de disposer d'un compte de stockage, vous pouvez en créer un en cliquant sur le bouton **Stockage** (icône grille) situé dans le volet de gauche du portail Azure, puis en sélectionnant **Nouveau** dans la barre de commandes du bas. Pour plus d'informations sur les comptes de stockage Azure, consultez les [liens](#moreaboutstorage) situés en bas de cet article.

## Pour créer une sauvegarde manuelle

1.  Dans le portail Azure de votre site Web, sélectionnez l'onglet **Sauvegardes**.

    ![Page Sauvegardes](./media/web-sites-backup/01ChooseBackupsPage.png)

2.  Sélectionnez le compte de stockage dans lequel vous souhaitez sauvegarder votre site Web. Ce compte de stockage doit relever du même abonnement que le site Web que vous sauvegardez.

    ![Sélection d'un compte de stockage](./media/web-sites-backup/02ChooseStorageAccount.png)

3.  Dans l'option **Bases de données incluses**, sélectionnez les bases de données connectées au site Web (serveur SQL ou MySQL) que vous souhaitez sauvegarder.

    ![Sélection des bases de données à inclure](./media/web-sites-backup/03IncludedDatabases.png)

    > [WACOM.NOTE] Pour permettre l'affichage d'une base de données dans cette liste, sa chaîne de connexion doit être présente dans la section **Chaînes de connexion** de l'onglet Configurer du portail.

4.  Dans la barre de commandes, cliquez sur **Backup Now**.

    ![Bouton Backup Now](./media/web-sites-backup/04BackUpNow.png)

    Un message de progression s'affiche au cours du processus de sauvegarde :

    ![Message de progression de la sauvegarde](./media/web-sites-backup/05BackupProgress.png)

Vous pouvez à tout moment effectuer une sauvegarde manuelle. Dans la version préliminaire, seules deux sauvegardes manuelles peuvent être effectuées par 24 heures (peut faire l'objet de modifications).

## Pour configurer des sauvegardes automatisées

1.  Sur la page Sauvegardes, activez **Sauvegarde automatisée**.

    ![Activation des sauvegardes automatisées](./media/web-sites-backup/06SetAutomatedBackupOn.png)

2.  Sélectionnez le compte de stockage dans lequel vous souhaitez sauvegarder votre site Web. Ce compte de stockage doit relever du même abonnement que le site Web que vous sauvegardez.

    ![Sélection d'un compte de stockage](./media/web-sites-backup/02ChooseStorageAccount.png)

3.  Dans la zone **Fréquence**, indiquez la fréquence de sauvegarde automatisée de votre choix. (Dans la version préliminaire, le nombre de jours correspond à la seule unité de temps disponible.)

    ![Sélection de la fréquence de sauvegarde](./media/web-sites-backup/07Frequency.png)

    Le nombre de jours doit être situé entre 1 et 90 compris (d'une fois par jour à tous les 90 jours).

4.  Utilisez l'option **Date de début** pour indiquer la date et l'heure de début des sauvegardes automatisées souhaitées.

    ![Sélection d'une date de début](./media/web-sites-backup/08StartDate.png)

    Les heures disponibles correspondent à des incréments d'une demi-heure.

    ![Sélection d'une heure de début](./media/web-sites-backup/09StartTime.png)

    > [WACOM.NOTE] Azure stocke les heures de sauvegarde au format UTC, mais les affiche selon l'heure de l'ordinateur que vous utilisez pour afficher le portail.

5.  Dans la section **Bases de données incluses**, sélectionnez les bases de données connectées au site Web (serveur SQL ou MySQL) que vous souhaitez sauvegarder. Pour permettre l'affichage d'une base de données dans cette liste, sa chaîne de connexion doit être présente dans la section **Chaînes de connexion** de l'onglet Configurer du portail.

    ![Sélection des bases de données à inclure](./media/web-sites-backup/03IncludedDatabases.png)

    > [WACOM.NOTE] Si vous avez sélectionné une ou plusieurs bases de données dans la sauvegarde et spécifié une fréquence inférieure à 7 jours, vous serez informé du fait que de fréquentes sauvegardes peuvent augmenter les coûts de vos bases de données.

6.  Dans la barre de commandes, cliquez sur le bouton **Enregistrer** pour enregistrer les modifications de votre configuration (ou sélectionnez **Ignorer** pour ne pas les enregistrer).

    ![Bouton Enregistrer](./media/web-sites-backup/10SaveIcon.png)

## Stockage des sauvegardes

Les sauvegardes effectuées apparaissent dans Conteneurs de votre compte de stockage. Vos sauvegardes se trouvent dans un conteneur appelé **websitebackups**. Chaque sauvegarde se compose d'un fichier .zip et d'un fichier .xml contenant respectivement les données sauvegardées et un manifeste du contenu du fichier .zip.

Les noms des fichiers de sauvegarde .zip et .xml se composent du nom de votre site Web suivi d'un trait de soulignement et de l'heure à laquelle la sauvegarde a eu lieu. L'horodatage comprend la date au format AAAAMMJJ (en chiffres, sans espace) ainsi que l'heure UTC au format 24 heures (par exemple, fabrikam\_201402152300.zip). Vous pouvez décompresser ces fichiers pour accéder à vos sauvegardes sans recourir à une restauration du site Web.

> [WACOM.NOTE] Toute modification apportée aux fichiers de votre conteneur **websitebackups** peut invalider la sauvegarde et la rendre impossible à restaurer.

## Remarques

-   Veillez à correctement configurer les chaînes de connexion de chacune de vos bases de données dans l'onglet Configurer du site Web pour permettre à la fonction de sauvegarde et de restauration de les inclure.
-   Dans la version préliminaire, il vous appartient de gérer le contenu sauvegardé enregistré dans votre compte de stockage. Si vous supprimez une sauvegarde de votre compte de stockage sans disposer d'une autre copie, vous ne serez pas en mesure de restaurer ultérieurement cette sauvegarde.
-   Bien qu'il vous soit possible de sauvegarder plusieurs sites Web sur le même compte de stockage, pensez à créer un compte de stockage distinct pour chaque site Web afin d'en faciliter la maintenance.
-   Dans la version préliminaire, les opérations de sauvegarde et de restauration sont uniquement disponibles via le portail de gestion Azure.

## Étapes suivantes
Pour plus d'informations sur la restauration d'un site Web Azure depuis une sauvegarde, consultez la rubrique [Restauration d'un site Web Azure](http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-restore/).

### En savoir plus sur les comptes de stockage

[Présentation des comptes de stockage](http://www.windowsazure.com/fr-fr/documentation/articles/storage-whatis-account/)

[Personnalisation Création d'un compte de stockage](http://www.windowsazure.com/fr-fr/documentation/articles/storage-create-storage-account/)

[Surveillance d'un compte de stockage](http://www.windowsazure.com/fr-fr/documentation/articles/storage-monitor-storage-account/)

[Présentation de la facturation du stockage Windows](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

