<properties linkid="web-sites-backup" urlDisplayName="Azure Websites Backups" pageTitle="Azure Websites Backups" metaKeywords="Azure Web Sites, Backups" description="Learn how to create backups of your Azure websites." metaCanonical="" services="web-sites" documentationCenter="" title="Azure Websites Backups" authors="cephalin" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Sauvegardes de Sites Web Azure

La fonctionnalité de sauvegarde et restauration de Sites Web Azure vous permet de créer facilement des sauvegardes de sites web, manuellement ou automatiquement. Vous pouvez restaurer votre site web à une étape précédente, ou créer un site web basé sur les sauvegardes de votre site d'origine.

Pour plus d'informations sur la restauration d'un site web Azure depuis une sauvegarde, consultez [Restauration d'un site web Azure][Restauration d'un site web Azure].

## Dans cet article

-   [Sauvegarde automatique et facile (vidéo)][Sauvegarde automatique et facile (vidéo)]
-   [Éléments sauvegardés][Éléments sauvegardés]
-   [Exigences et restrictions][Exigences et restrictions]
-   [Pour créer une sauvegarde manuelle][Pour créer une sauvegarde manuelle]
-   [Pour configurer des sauvegardes automatisées][Pour configurer des sauvegardes automatisées]
-   [Stockage des sauvegardes][Stockage des sauvegardes]
-   [Remarques][Remarques]
-   [Étapes suivantes][Étapes suivantes]
    -   [En savoir plus sur les comptes de stockage][En savoir plus sur les comptes de stockage]

<a name="video"></a>

## Sauvegarde automatique et facile (vidéo)

Dans cette vidéo, Eduardo Laureano et Scott Hanselman présentent les sauvegardes de Sites Web Azure. (Durée : 11:43)

> [AZURE.VIDEO azure-websites-automatic-and-easy-backup]

<a name="whatsbackedup"></a>

## Éléments sauvegardés

Sites Web Azure sauvegarde les informations suivantes :

-   Configuration du site web
-   Contenu du site web
-   Serveurs SQL ou bases de données MySQL connectés à votre site (vous pouvez choisir l'élément à inclure dans votre sauvegarde)

Ces informations sont sauvegardées dans le compte de stockage Azure que vous indiquez.

> [WACOM.NOTE] Chaque sauvegarde représente une copie hors connexion complète de votre site web, et non une mise à jour incrémentielle.

<a name="requirements"></a>

## Exigences et restrictions

-   La fonction de sauvegarde et de restauration implique que le site se situe au niveau Standard. Pour plus d'informations sur la mise à l'échelle de votre site web au niveau Standard, consultez [Mise à l'échelle de sites web][Mise à l'échelle de sites web].

-   La fonctionnalité de sauvegarde et restauration demande un compte Azure Storage appartenant au même abonnement que le site web que vous sauvegardez. À défaut de disposer d'un compte de stockage, vous pouvez en créer un en cliquant sur le bouton **Stockage** (icône grille) situé dans le volet de gauche du portail Azure, puis en sélectionnant **Nouveau** dans la barre de commandes du bas. Pour plus d'informations sur les comptes de stockage Azure, consultez les [liens][En savoir plus sur les comptes de stockage] situés en bas de cet article.

<a name="manualbackup"></a>

## Pour créer une sauvegarde manuelle

1.  Dans le portail Azure de votre site web, choisissez l'onglet **Sauvegardes**.

    ![Page Sauvegardes][Page Sauvegardes]

2.  Sélectionnez le compte de stockage dans lequel vous souhaitez sauvegarder votre site web. Ce compte de stockage doit relever du même abonnement que le site web que vous sauvegardez.

    ![Sélection d'un compte de stockage][Sélection d'un compte de stockage]

3.  Dans l'option **Bases de données incluses**, sélectionnez les bases de données connectées au site web (serveur SQL ou MySQL) que vous souhaitez sauvegarder.

    ![Sélection des bases de données à inclure][Sélection des bases de données à inclure]

    > [WACOM.NOTE] Pour permettre l'affichage d'une base de données dans cette liste, sa chaîne de connexion doit être présente dans la section **Chaînes de connexion** de l'onglet Configurer du portail.

4.  Dans la barre de commandes, cliquez sur **Backup Now**.

    ![Bouton Backup Now][Bouton Backup Now]

    Un message de progression s'affiche au cours du processus de sauvegarde :

    ![Message de progression de la sauvegarde][Message de progression de la sauvegarde]

Vous pouvez à tout moment effectuer une sauvegarde manuelle. Dans la version préliminaire, seules deux sauvegardes manuelles peuvent être effectuées par 24 heures (peut faire l'objet de modifications).

<a name="automatedbackups"></a>

## Pour configurer des sauvegardes automatisées

1.  Sur la page Sauvegardes, activez **Sauvegarde automatisée**.

    ![Activation des sauvegardes automatisées][Activation des sauvegardes automatisées]

2.  Sélectionnez le compte de stockage dans lequel vous souhaitez sauvegarder votre site web. Ce compte de stockage doit relever du même abonnement que le site web que vous sauvegardez.

    ![Sélection d'un compte de stockage][Sélection d'un compte de stockage]

3.  Dans la zone **Fréquence**, indiquez la fréquence de sauvegarde automatisée de votre choix. (Dans la version préliminaire, le nombre de jours correspond à la seule unité de temps disponible.)

    ![Sélection de la fréquence de sauvegarde][Sélection de la fréquence de sauvegarde]

    Le nombre de jours doit être situé entre 1 et 90 compris (d'une fois par jour à tous les 90 jours).

4.  Utilisez l'option **Date de début** pour indiquer la date et l'heure de début des sauvegardes automatisées souhaitées.

    ![Sélection d'une date de début][Sélection d'une date de début]

    Les heures disponibles correspondent à des incréments d'une demi-heure.

    ![Sélection d'une heure de début][Sélection d'une heure de début]

    > [WACOM.NOTE] Azure stocke les heures de sauvegarde au format UTC, mais les affiche selon l'heure de l'ordinateur que vous utilisez pour afficher le portail.

5.  Dans la section **Bases de données incluses**, sélectionnez les bases de données connectées au site web (serveur SQL ou MySQL) que vous souhaitez sauvegarder. Pour permettre l'affichage d'une base de données dans cette liste, sa chaîne de connexion doit être présente dans la section **Chaînes de connexion** de l'onglet Configurer du portail.

    ![Sélection des bases de données à inclure][Sélection des bases de données à inclure]

    > [WACOM.NOTE] Si vous avez sélectionné une ou plusieurs bases de données dans la sauvegarde et spécifié une fréquence inférieure à 7 jours, vous serez informé du fait que de fréquentes sauvegardes peuvent augmenter les coûts de vos bases de données.

6.  Dans la barre de commandes, cliquez sur le bouton **Enregistrer** pour enregistrer les modifications de votre configuration (ou sélectionnez **Ignorer** pour ne pas les enregistrer).

    ![Bouton enregistrer][Bouton enregistrer]

<a name="aboutbackups"></a>

## Stockage des sauvegardes

Les sauvegardes effectuées apparaissent dans Conteneurs de votre compte de stockage. Vos sauvegardes se trouvent dans un conteneur appelé **websitebackups**. Chaque sauvegarde se compose d'un fichier .zip et d'un fichier .xml contenant respectivement les données sauvegardées et un manifeste du contenu du fichier .zip.

Les noms des fichiers de sauvegarde .zip et .xml se composent du nom de votre site web suivi d'un trait de soulignement et de l'heure à laquelle la sauvegarde a eu lieu. L'horodatage comprend la date au format AAAAMMJJ (en chiffres, sans espace) ainsi que l'heure UTC au format 24 heures (par exemple, fabrikam\_201402152300.zip). Vous pouvez décompresser ces fichiers pour accéder à vos sauvegardes sans recourir à une restauration du site web.

Le fichier XML stocké avec le fichier zip indique le nom du fichier de base de données sous *description\_base\_données* \> *bases\_données* \> *description\_sauvegarde\_base\_données* \> *nom\_fichier*.

Le fichier de sauvegarde de base de données est stocké à la racine du fichier .zip. Pour une base de données SQL, il s'agit d'un fichier BACPAC (pas d'extension de fichier) qui peut être importé. Pour créer une base de données SQL basée sur l'exportation du fichier BACPAC, suivez les étapes indiquées dans l'article [Importer un fichier BACPAC pour créer une nouvelle base de données utilisateur][Importer un fichier BACPAC pour créer une nouvelle base de données utilisateur].

Pour plus d'informations sur la restauration d'un site web Azure (y compris les bases de données) via le portail de gestion Azure, consultez [Restauration d'un site web Microsoft Azure][Restauration d'un site web Microsoft Azure].

> [WACOM.NOTE] Toute modification apportée aux fichiers de votre conteneur **websitebackups** peut invalider la sauvegarde et la rendre impossible à restaurer.

<a name="notes"></a>

## Remarques

-   Veillez à correctement configurer les chaînes de connexion de chacune de vos bases de données sous l'onglet Configurer du site web pour permettre à la fonctionnalité de sauvegarde et de restauration de les inclure.
-   Dans la version préliminaire, il vous appartient de gérer le contenu sauvegardé enregistré dans votre compte de stockage. Si vous supprimez une sauvegarde de votre compte de stockage sans disposer d'une autre copie, vous ne serez pas en mesure de restaurer ultérieurement cette sauvegarde.
-   Bien qu'il vous soit possible de sauvegarder plusieurs sites web sur le même compte de stockage, pensez à créer un compte de stockage distinct pour chaque site web afin d'en faciliter la maintenance.
-   Dans la version préliminaire, les opérations de sauvegarde et de restauration sont uniquement disponibles via le portail de gestion Azure.

<a name="nextsteps"></a>

## Étapes suivantes

Pour plus d'informations sur la restauration d'un site web Azure depuis une sauvegarde, consultez [Restauration d'un site web Azure][Restauration d'un site web Azure].

Pour la prise en main d'Azure, consultez la page [Version d'évaluation gratuite de Microsoft Azure][Version d'évaluation gratuite de Microsoft Azure].

<a name="moreaboutstorage"></a>

### En savoir plus sur les comptes de stockage

[Présentation des comptes de stockage][Présentation des comptes de stockage]

[Création d’un compte de stockage][Création d’un compte de stockage]

[Surveillance d'un compte de stockage][Surveillance d'un compte de stockage]

[Présentation de la facturation du stockage Windows][Présentation de la facturation du stockage Windows]

<!-- IMAGES -->

  [Restauration d'un site web Azure]: http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-restore/
  [Sauvegarde automatique et facile (vidéo)]: #video
  [Éléments sauvegardés]: #whatsbackedup
  [Exigences et restrictions]: #requirements
  [Pour créer une sauvegarde manuelle]: #manualbackup
  [Pour configurer des sauvegardes automatisées]: #automatedbackups
  [Stockage des sauvegardes]: #aboutbackups
  [Remarques]: #notes
  [Étapes suivantes]: #nextsteps
  [En savoir plus sur les comptes de stockage]: #moreaboutstorage
  [Mise à l'échelle de sites web]: http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-scale/
  [Page Sauvegardes]: ./media/web-sites-backup/01ChooseBackupsPage.png
  [Sélection d'un compte de stockage]: ./media/web-sites-backup/02ChooseStorageAccount.png
  [Sélection des bases de données à inclure]: ./media/web-sites-backup/03IncludedDatabases.png
  [Bouton Backup Now]: ./media/web-sites-backup/04BackUpNow.png
  [Message de progression de la sauvegarde]: ./media/web-sites-backup/05BackupProgress.png
  [Activation des sauvegardes automatisées]: ./media/web-sites-backup/06SetAutomatedBackupOn.png
  [Sélection de la fréquence de sauvegarde]: ./media/web-sites-backup/07Frequency.png
  [Sélection d'une date de début]: ./media/web-sites-backup/08StartDate.png
  [Sélection d'une heure de début]: ./media/web-sites-backup/09StartTime.png
  [Bouton enregistrer]: ./media/web-sites-backup/10SaveIcon.png
  [Importer un fichier BACPAC pour créer une nouvelle base de données utilisateur]: http://technet.microsoft.com/fr-fr/library/hh710052.aspx
  [Restauration d'un site web Microsoft Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-restore/
  [Version d'évaluation gratuite de Microsoft Azure]: http://azure.microsoft.com/fr-fr/pricing/free-trial/
  [Présentation des comptes de stockage]: http://www.windowsazure.com/fr-fr/documentation/articles/storage-whatis-account/
  [Création d’un compte de stockage]: http://www.windowsazure.com/fr-fr/documentation/articles/storage-create-storage-account/
  [Surveillance d'un compte de stockage]: http://www.windowsazure.com/fr-fr/documentation/articles/storage-monitor-storage-account/
  [Présentation de la facturation du stockage Windows]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx
