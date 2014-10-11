<properties linkid="manage-services-hdinsight-administer-hdinsight-hadoop-clusters-using-command-line" urlDisplayName="HDInsight Administration" pageTitle="Manage Hadoop clusters using Cross-Platform Command-Line | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure, hadoop, administration" description="Learn how to use the Cross-Platform Command-Line Interface to manage Hadoop clusters in HDIsight on any platform that supports Node.js, including Windows, Mac, and Linux." services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Administer Hadoop clusters using the Cross-platform Command-line Interface" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Gestion des clusters Hadoop dans HDInsight à l'aide de l'interface de ligne de commande interplateforme

Cet article explique comment utiliser l'interface de ligne de commande interplateforme pour gérer des clusters Hadoop dans HDInsight. L'outil en ligne de commande est implémenté dans Node.js. Vous pouvez l'utiliser sur toute plateforme prenant en charge Node.js, y compris Windows, Mac et Linux.

Il s'agit d'une technologie open source. Le code source est géré dans GitHub sur <https://github.com/WindowsAzure/azure-sdk-tools-xplat>.

Cet article traite uniquement de l'utilisation de l'interface de ligne de commande dans Windows. Pour une aide générale sur l'utilisation de l'interface de ligne de commande, consultez la page [Utilisation des outils en ligne de commande Azure pour Mac et Linux][]. Pour accéder à une documentation de référence exhaustive, consultez la rubrique [Documentation de l’outil en ligne de commande Azure pour Mac et Linux][].

**Configuration requise :**

Avant de commencer cet article, vous devez disposer des éléments suivants :

-   **Abonnement Azure**. Azure est une plateforme disponible par abonnement. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les [formules d'abonnement][], les [offres spéciales membres][] ou la [version d'évaluation gratuite][].

## Dans cet article

-   [Installation][]
-   [Téléchargement et importation de paramètres de publication de compte Azure][]
-   [Mise en service d'un cluster][]
-   [Approvisionnement d'un cluster en utilisant un fichier de configuration][]
-   [Énumération et affichage des clusters][]
-   [Suppression d'un cluster][]
-   [Étapes suivantes][]

## <span id="installation"></span></a> Installation

L'interface de ligne de commande peut être installée au moyen du *Gestionnaire de package Node.js (NPM)* ou de Windows Installer.

**Pour installer l'interface de ligne de commande au moyen de NPM**

1.  Accédez à **www.nodejs.org**.
2.  Cliquez sur **INSTALL** et suivez les instructions en conservant les paramètres par défaut.
3.  Ouvrez **Invite de commandes** (ou *Invite de commandes Azure* ou *Invite de commandes développeur pour VS2012*) à partir de votre poste de travail.
4.  Exécutez la commande suivante dans la fenêtre d'invite de commandes.

        npm install -g azure-cli

    > [WACOM.NOTE] Si un message d'erreur indique que la commande NPM est introuvable, vérifiez que les chemins d'accès suivants se trouvent dans la variable d'environnement PATH : *C:\\Program Files (x86)\\nodejs;C:\\Users[nom\_utilisateur]\\AppData\\Roaming\\npm* ou *C:\\Program Files\\nodejs;C:\\Users[nom\_utilisateur]\\AppData\\Roaming\\npm*

5.  Exécutez la commande suivante pour vérifier l'installation :

        azure hdinsight -h

    Vous pouvez utiliser le commutateur *-h* à différents niveaux pour afficher les informations d'aide. Par exemple :

        azure -h
        azure hdinsight -h
        azure hdinsight cluster -h
        azure hdinsight cluster create -h

**Pour installer l'interface de ligne de commande au moyen de Windows Installer**

1.  Accédez à **<http://azure.microsoft.com/en-us/downloads/>**.
2.  Faites défiler l'écran pour accéder à la section **Outils de ligne de commande**, puis cliquez sur **Interface de ligne de commande interplateforme** et suivez les étapes de l'Assistant Web Platform Installer.

## <span id="importsettings"></span></a> Téléchargement et importation de paramètres de publication de compte Azure

Avant d'utiliser l'interface de ligne de commandes, vous devez configurer la connectivité entre votre poste de travail et Azure. L'interface de ligne de commande se sert des informations sur votre abonnement Azure pour se connecter à votre compte. Ces informations peuvent être obtenues d'Azure dans un fichier de paramètres de publication. Ce dernier peut ensuite être importé en tant que paramètre de configuration local persistant dont l'interface de ligne de commande se servira pour les opérations ultérieures. Vous importez vos paramètres de publication une fois pour toutes.

> [WACOM.NOTE] Le fichier de paramètres de publication contient des informations critiques. Il est recommandé de supprimer le fichier ou de prendre des mesures supplémentaires pour chiffrer le dossier utilisateur contenant le fichier. Sous Windows, modifiez les propriétés de dossier ou utilisez BitLocker.

**Téléchargement et importation des paramètres de publication**

1.  Ouvrez une **Invite de commandes**.
2.  Exécutez la commande suivante pour télécharger le fichier de paramètres de publication.

        azure account download

    ![HDI.CLIAccountDownloadImport][]

    La commande affiche les instructions permettant de télécharger le fichier, ainsi qu'une URL.

3.  Ouvrez **Internet Explorer** et accédez à l'URL figurant dans la fenêtre d'invite de commandes.
4.  Cliquez sur **Enregistrer** pour enregistrer le fichier sur le poste de travail.
5.  À partir de la fenêtre d'invite de commandes, exécutez la commande suivante pour importer le fichier de paramètres de publication :

        azure account import <file>

    Dans la capture d'écran précédente, le fichier de paramètres de publication a été enregistré dans le dossier C:\\HDInsight sur le poste de travail.

## <span id="provision"></span></a>Approvisionnement d'un cluster HDInsight

HDInsight utilise un conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage est nécessaire avant de pouvoir créer un cluster HDInsight.

Après avoir importé le fichier de paramètres de publication, vous pouvez utiliser la commande suivante pour créer un compte de stockage :

    azure account storage create [options] <StorageAccountName>

> [WACOM.NOTE] Le compte de stockage doit se trouver dans le même centre de données. Pour l'heure, les clusters HDInsight ne peuvent être approvisionnés que dans les centres de données suivants :

> -   Asie du Sud-Est
> -   Europe du Nord
> -   Europe de l'Ouest
> -   Est des États-Unis
> -   Ouest des États-Unis

Pour plus d'informations sur la création d'un compte Azure Storage au moyen du portail de gestion Azure, consultez la rubrique [Création d'un compte de stockage][].

Si vous disposez déjà d'un compte de stockage mais que vous ne connaissez ni le nom ni la clé du compte, vous pouvez utiliser les commandes suivantes pour récupérer les informations :

    -- lists storage accounts
    azure account storage list
    -- Shows a storage account
    azure account storage show <StorageAccountName>
    -- Lists the keys for a storage account
    azure account storage keys list <StorageAccountName>

Pour des détails sur l'obtention d'informations avec le portail de gestion, consultez la section *Affichage, copie et régénération de clés d'accès de stockage* de la page [Gestion des comptes de stockage][].

La commande *azure hdinsight cluster create* crée le conteneur s'il n'existe pas. Si vous avez choisi de créer le conteneur au préalable, vous pouvez utiliser la commande suivante :

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
        

Une fois que le compte de stockage et le conteneur d'objets blob sont prêts, vous êtes prêt à créer un cluster :

    azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][]

## <span id="provisionconfigfile"></span></a> Approvisionnement d'un cluster HDInsight au moyen d'un fichier de configuration

Généralement, vous approvisionnez un cluster HDInsight, y exécutez des tâches, puis le supprimez, afin de réduire les coûts. L'interface de ligne de commande vous donne la possibilité d'enregistrer les configurations dans un fichier, de sorte que vous pouvez les réutiliser chaque fois que vous mettez en service un cluster.

    azure hdinsight cluster config create <file>
     
    azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"
     
    azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
           --storageAccountKey "<StorageAccountKey>"
     
    azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
           --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"
     
    azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
           --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"
     
    azure hdinsight cluster create --config <file>
         

![HDI.CLIClusterCreationConfig][]

## <span id="listshow"></span></a> Énumération et affichage des détails de cluster

Utilisez les commandes suivantes pour énumérer et afficher les détails de cluster :

    azure hdinsight cluster list
    azure hdinsight cluster show <ClusterName>

![HDI.CLIListCluster][]

## <span id="delete"></span></a> Suppression d'un cluster

Utilisez les commandes suivantes pour supprimer un cluster :

    azure hdinsight cluster delete <ClusterName>

## <span id="nextsteps"></span></a>Étapes suivantes

Dans cet article, vous avez appris comment effectuer différentes tâches d'administration sur un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

-   [Administration de HDInsight à l'aide du portail de gestion][]
-   [Administration de HDInsight à l'aide de PowerShell][]
-   [Prise en main d'Azure HDInsight][]
-   [Utilisation des outils en ligne de commande Azure pour Mac et Linux][]
-   [Outil en ligne de commande Azure pour Mac et Linux][Documentation de l’outil en ligne de commande Azure pour Mac et Linux]

  [Utilisation des outils en ligne de commande Azure pour Mac et Linux]: ../xplat-cli/
  [Documentation de l’outil en ligne de commande Azure pour Mac et Linux]: ../command-line-tools/
  [formules d'abonnement]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [offres spéciales membres]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [version d'évaluation gratuite]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Installation]: #installation
  [Téléchargement et importation de paramètres de publication de compte Azure]: #importsettings
  [Mise en service d'un cluster]: #provision
  [Approvisionnement d'un cluster en utilisant un fichier de configuration]: #provisionconfigfile
  [Énumération et affichage des clusters]: #listshow
  [Suppression d'un cluster]: #delete
  [Étapes suivantes]: #nextsteps
  [HDI.CLIAccountDownloadImport]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
  [Création d'un compte de stockage]: ../storage-create-storage-account/
  [Gestion des comptes de stockage]: ../storage-manage-storage-account/
  [HDI.CLIClusterCreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
  [HDI.CLIClusterCreationConfig]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
  [HDI.CLIListCluster]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Énumérer et afficher les clusters"
  [Administration de HDInsight à l'aide du portail de gestion]: ../hdinsight-administer-use-management-portal/
  [Administration de HDInsight à l'aide de PowerShell]: ../hdinsight-administer-use-powershell/
  [Prise en main d'Azure HDInsight]: ../hdinsight-get-started/
