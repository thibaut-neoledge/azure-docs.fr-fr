<properties linkid="manage-services-hdinsight-provision-hadoop-clusters" urlDisplayName="HDInsight Administration" pageTitle="Provision Hadoop clusters in HDInsight | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to provision clusters for Azure HDInsight using the management portal, PowerShell, or the command line." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Provision Hadoop clusters in HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Approvisionnement de clusters Hadoop dans HDInsight avec des options personnalisées

Cet article vous présente différentes méthodes pour approvisionner et personnaliser un cluster Hadoop sur Azure HDInsight en utilisant le portail de gestion Azure, PowerShell, les outils en ligne de commande ou le Kit de développement logiciel (SDK) .NET HDInsight. Il traite de l'approvisionnement des clusters Hadoop. Pour des instructions sur l'approvisionnement d'un cluster HBase, consultez la page [Approvisionnement d'un cluster HBase dans HDInsight][]. Pour connaître les raisons qui peuvent vous pousser à choisir Hadoop ou HBase, consultez la page [Différences entre Hadoop et HBase][].

## Présentation des clusters HDInsight

Vous êtes-vous déjà demandé pourquoi nous évoquons les clusters chaque fois que nous parlons de Hadoop ou de données volumineuses (Big Data) ? C'est parce que Hadoop autorise le traitement distribué de grandes quantités de données réparties entre différents nœuds d'un cluster. Le cluster a une architecture maître/esclave avec un maître (appelé également nœud principal ou nœud de nom) et un nombre quelconque d'esclaves (également appelés nœuds de données). Pour plus d'informations, consultez la page [Apache Hadoop][].

Un cluster HDInsight vous libère de tous les détails de l'implémentation de Hadoop : vous n'avez plus à vous soucier des problèmes de communication avec les différents nœuds d'un cluster. Lorsque vous approvisionnez un cluster HDInsight, vous approvisionnez des ressources de calcul Azure qui contiennent Hadoop et les applications apparentées. Pour plus d'informations, consultez la page [Présentation de Hadoop dans HDInsight][].

Cet article fournit des instructions concernant les différentes façons d'approvisionner un cluster. Si vous recherchez une approche rapide pour approvisionner un cluster, consultez la page [Prise en main d'Azure HDInsight][].

**Configuration requise :**

Avant de commencer cet article, vous devez disposer des éléments suivants :

-   Un abonnement Azure. Azure est une plateforme disponible par abonnement. Les cmdlets HDInsight PowerShell permettent d'effectuer les tâches associées à votre abonnement. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les [formules d'abonnement][], les [offres spéciales membres][] ou la [version d'évaluation gratuite][].

## Dans cet article

-   [Utilisation du portail de gestion Azure][]
-   [Utilisation d'Azure PowerShell][]
-   [Utilisation de la ligne de commande interplateforme][]
-   [Utilisation du Kit de développement logiciel (SDK) HDInsight .NET][]
-   [Étapes suivantes][]

## <span id="portal"></span></a> Utilisation du portail de gestion Azure

Les clusters HDInsight utilisent un conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage se trouvant dans le même centre de données est nécessaire avant de pouvoir créer un cluster HDInsight. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][]. Pour plus d'informations sur la création d'un compte Azure Storage, consultez la page [Création d'un compte de stockage][].

> [WACOM.NOTE] Actuellement, seules les régions suivantes peuvent héberger des clusters HDInsight : **Est de l'Asie**, **Asie du Sud-Est**, **Europe du Nord**, **Europe de l'Ouest**, **Est des États-Unis**, **Ouest des États-Unis**, **Nord du centre des États-Unis** et **Sud du centre des États-Unis**.

**Pour créer un cluster HDInsight au moyen de l'option de création personnalisée**

1.  Connectez-vous au [portail de gestion Azure][].
2.  Dans la partie inférieure de la page, cliquez sur **+ NEW**, sur **DATA SERVICES**, sur **HDINSIGHT**, puis sur **CUSTOM CREATE**.
3.  Sur la page **Détails du cluster**, tapez ou choisissez les valeurs suivantes :

    ![HDI.CustomCreateCluster][]


    <table border='1'>
		<tr><th>Propriété</th><th>Valeur</th></tr>
		<tr><td>Nom du cluster</td>
			<td><p>Nom du cluster.</p>
				<ul>
				<li>Le nom DNS doit commencer et finir par un caractère alphanumérique, et peut contenir des traits d'union.</li>
				<li>Le champ doit être une chaîne comportant entre 3 et 63 caractères.</li>
				</ul></td></tr>
		<tr><td>Type de cluster</td>
			<td>Pour le type de cluster, sélectionnez <strong>Hadoop</strong>.</td></tr>
		<tr><td>Version de HDInsight</td>
			<td>Choisissez la version. Pour Hadoop, la version par défaut est HDInsight version 3.1, qui utilise Hadoop 2.4.</td></tr>
		</table>

    Entrez ou sélectionnez les valeurs comme indiqué dans le tableau, puis cliquez sur la flèche vers la droite.

4.  Sur la page **Configurer le cluster**, entrez ou sélectionnez les valeurs suivantes :

	<table border="1">
	<tr><th>Nom</th><th>Valeur</th></tr>
	<tr><td>Nœuds de données</td><td>Nombre de nœuds de données que vous souhaitez déployer. À des fins de test, créez un cluster à nœud unique. <br />La limite de taille de cluster varie pour les abonnements Azure. Contactez le support de facturation Azure pour augmenter la limite.</td></tr>
	<tr><td>Région/Réseau virtuel</td><td>Choisissez la même région que pour le compte de stockage que vous avez créé dans la dernière procédure. HDInsight requiert en effet que le compte de stockage soit situé dans la même région. Plus loin dans la configuration, vous ne pourrez choisir qu'un compte de stockage situé dans la région spécifiée ici. Les régions disponibles sont les suivantes : <strong>Est de l'Asie</strong>, <strong>Asie du Sud-Est</strong>, <strong>Europe du Nord</strong>, <strong>Europe de l'Ouest</strong>, <strong>Est des États-Unis</strong>, <strong>Ouest des États-Unis</strong>, <strong>Nord du centre des États-Unis</strong> et <strong>Sud du centre des États-Unis</strong>.</td></tr>
	</table>

5.  Sur la page **Configurer l'utilisateur du cluster**, fournissez les valeurs suivantes :

    ![HDI.CustomCreateCluster.ClusterUser][]
	
	<table border="1">
	<tr><th>Propriété                                     </th><th>Valeur</th></tr>
    <tr><td>Nom d'utilisateur                             </td><td>Spécifiez le nom d'utilisateur de cluster HDInsight.                                                                  </td></tr>
    <tr><td>Mot de passe/Confirmer le mot de passe        </td><td>Spécifiez le mot de passe de cluster HDInsight.                                                                       </td></tr>
    <tr><td>Enter Hive/Oozie Metastore                    </td><td>Activez cette case à cocher pour spécifier une base de données SQL dans le même centre de données que le cluster devant être utilisé comme metastore Hive/Oozie. Cela est utile si vous voulez conserver les métadonnées à propos des tâches Hive/Oozie même après la suppression d'un cluster.                                                          </td></tr>
    <tr><td>Base de données de metastore                  </td><td>Spécifiez la base de données SQL Azure qui sera utilisée comme metastore pour Hive/Oozie. Cette base de données SQL doit se trouver dans le même centre de données que le cluster HDInsight. La zone de liste répertorie uniquement les bases de données SQL situées dans le même centre de données que celui spécifié dans la page <strong>Détails du cluster</strong>. </td></tr>
    <tr><td>Utilisateur de la base de données             </td><td>Spécifiez l'utilisateur de base de données SQL qui sera utilisé pour se connecter à la base de données.               </td></tr>
    <tr><td>Mot de passe utilisateur de la base de données</td><td>Spécifiez le mot de passe de la base de données SQL.                                                                  </td></tr>
	</table>

    > [WACOM.NOTE] La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données SQL Azure, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est en cours d'exécution. Une fois que vous êtes dans la vue serveur, cliquez sur **Configurer**, puis, pour **Services Windows Azure**, cliquez sur **Oui**. Cliquez ensuite sur **Enregistrer**.

    Cliquez sur la flèche droite.

6.  Sur la page **Compte de stockage**, entrez la valeur suivante :

    ![HDI.CustomCreateCluster.StorageAccount][]

    <table>
    <tr class="header">
    <th align="left">Propriété</th>
    <th align="left">Valeur</th>
    </tr>
    <tr class="odd">
    <td align="left">Compte de stockage</td>
    <td align="left">Spécifiez le compte de stockage Azure qui sera utilisé comme système de fichiers par défaut pour le cluster HDInsight. Vous pouvez choisir l'une des trois options suivantes :
    <ul>
    <li>Use Existing Storage</li>
    <li>Create New Storage</li>
    <li>Use Storage From Another Subscription</li>
    </ul></td>
    </tr>
    <tr class="even">
    <td align="left">Nom du compte</td>
    <td align="left"><ul>
    <li>Si vous avez choisi d'utiliser un stockage existant, sélectionnez un compte un stockage existant pour <strong>Nom du compte</strong>. La liste déroulante répertorie uniquement les comptes de stockage situés dans le même centre de données que celui où vous avez choisi d'approvisionner le cluster.</li>
    <li>Si vous avez choisi l'option <strong>Créer un nouveau stockage</strong> ou <strong>Utiliser le stockage associé à un autre abonnement</strong>, vous devez fournir le nom du compte de stockage.</li>
    </ul></td>
    </tr>
    <tr class="odd">
    <td align="left">Clé du compte</td>
    <td align="left">Si vous avez choisi l'option <strong>Utiliser le stockage associé à un autre abonnement</strong>, spécifiez la clé de compte pour ce compte de stockage.</td>
    </tr>
    <tr class="even">
    <td align="left">Conteneur par défaut</td>
    <td align="left"><p>Spécifie le conteneur par défaut sur le compte de stockage qui est utilisé comme système de fichiers par défaut pour le cluster HDInsight. Si vous avez choisi <strong>Utiliser le stockage existant</strong> pour le champ <strong>Compte de stockage</strong> et qu'il n'existe pas de conteneurs dans ce compte, le conteneur est créé par défaut avec le même nom que le cluster. Si un conteneur portant le nom du cluster existe déjà, un numéro de séquence est ajouté au nom de conteneur. Par exemple, mon_conteneur1, mon_conteneur2, et ainsi de suite. Cependant, si le compte de stockage existant a un conteneur avec un nom différent de celui du cluster que vous avez spécifié, vous pouvez utiliser également ce conteneur.</p>
    <p>Si vous avez choisi de créer un nouveau stockage ou d'utiliser un stockage d'un autre abonnement Azure, vous devez spécifier le nom du conteneur par défaut.</p></td>
    </tr>
    <tr class="odd">
    <td align="left">Comptes de stockage supplémentaire</td>
    <td align="left">HDInsight prend en charge plusieurs comptes de stockage. Le nombre de comptes de stockage supplémentaires pouvant être utilisés par un cluster n'est pas limité. Toutefois, si vous créez un cluster au moyen du portail de gestion, la limite est établie à sept en raison de contraintes liées à l'interface utilisateur. Chaque compte de stockage supplémentaire que vous spécifiez ajoute une page Compte de stockage supplémentaire à l'Assistant, vous permettant de spécifier les informations de compte. Par exemple, dans la capture d'écran ci-dessus, un compte de stockage supplémentaire étant sélectionné, la page 5 est ajoutée à la boîte de dialogue.</td>
    </tr>
    </table>

    Cliquez sur la flèche droite.

7.  Sur la page **Compte de stockage**, entrez les informations du compte de stockage supplémentaire :

    ![HDI.CustomCreateCluster.AddOnStorage][]

    Vous avez ici aussi la possibilité de choisir un stockage existant, de créer un nouveau stockage ou d'utiliser un stockage d'un autre abonnement Azure. La procédure à suivre pour fournir les valeurs est similaire à celle décrite à l'étape précédente.

    Cliquez sur la coche pour démarrer l'approvisionnement du cluster. À l'issue du processus d'approvisionnement, la colonne de statut indique **En cours d'exécution**.

    > [WACOM.NOTE] Une fois qu'un compte Azure Storage est choisi pour votre cluster HDInsight, vous ne pouvez ni supprimer le compte, ni le remplacer par un autre.

## <span id="powershell"></span></a> Utilisation d'Azure PowerShell

Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Cette section fournit des instructions sur l'approvisionnement d'un cluster HDInsight. Pour des informations sur la configuration d'un poste de travail pour l'exécution de cmdlets HDInsight Powershell, consultez la page [Installation et configuration d'Azure PowerShell][]. Pour plus d'informations sur l'utilisation de PowerShell avec HDInsight, consultez [Administration de HDInsight avec PowerShell][]. Pour la liste des cmdlets PowerShell pour HDInsight, consultez la rubrique [Référence des cmdlets HDInsight][].

Les procédures suivantes sont nécessaires pour mettre en service un cluster HDInsight en utilisant PowerShell :

-   Création d’un compte de stockage Azure
-   Création d'un conteneur d'objets blob Azure
-   Création d'un cluster HDInsight

HDInsight utilise un conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage et un conteneur de stockage sont nécessaires avant de pouvoir créer un cluster HDInsight. Ce compte de stockage doit se situer dans le même centre de données que le cluster HDInsight.

**Pour créer un compte Azure Storage**

-   Exécutez les commandes suivantes à partir d'une fenêtre de console Azure PowerShell :

        $storageAccountName = "<StorageAcccountName>"   # Provide a storage account name 
        $location = "<MicrosoftDataCenter>"             # For example, "West US"

        # Create an Azure storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

    Si vous disposez déjà d'un compte de stockage mais que vous ne connaissez ni le nom ni la clé du compte, vous pouvez utiliser les commandes PowerShell suivantes pour récupérer les informations :

        # List storage accounts for the current subscription
        Get-AzureStorageAccount

        # List the keys for a storage account
        Get-AzureStorageKey "<StorageAccountName>"

**Pour créer un conteneur de stockage d'objets blob Azure**

-   Exécutez les commandes suivantes à partir d'une fenêtre de console Azure PowerShell :

        $storageAccountName = "<StorageAccountName>"    # Provide the storage account name 
        $storageAccountKey = "<StorageAccountKey>"      # Provide either primary or secondary key
        $containerName="<ContainerName>"                # Provide a container name

        # Create a storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
                                               -StorageAccountKey $storageAccountKey  

        # Create a Blob storage container
        New-AzureStorageContainer -Name $containerName -Context $destContext

Une fois que le compte de stockage et le conteneur d'objets blob sont prêts, vous êtes prêt à créer un cluster.

**Pour mettre en service un cluster HDInsight**

-   Exécutez les commandes suivantes à partir d'une fenêtre de console Azure PowerShell :

        $subscriptionName = "<SubscriptionName>"        # Name of the Azure subscription.
        $storageAccountName = "<StorageAccountName>"    # Azure storage account that hosts the default container. 
        $containerName = "<ContainerName>"              # Azure Blob container that is used as the default file system for the HDInsight cluster.

        $clusterName = "<HDInsightClusterName>"         # The name you will name your HDInsight cluster.
        $location = "<MicrosoftDataCenter>"             # The location of the HDInsight cluster. It must in the same data center as the storage account.
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster.

        # Get the storage primary key based on the account name
        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

        # Create a new HDInsight cluster
        New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

    Lorsque vous y êtes invité, entrez les informations d'identification du cluster. La mise en service du cluster peut prendre plusieurs minutes.

    ![HDI.CLI.Provision][]

**Pour approvisionner un cluster HDInsight avec des options de configuration personnalisées**

Pendant l'approvisionnement d'un cluster, vous pouvez utiliser les autres options de configuration, par exemple, la connexion à plusieurs stockages d'objets blob Azure ou l'utilisation d'une base de données SQL Azure pour des metastores Hive et Oozie. Cette fonctionnalité vous permet de séparer la durée de vie de vos données et métadonnées de celle du cluster.

-   Exécutez les commandes suivantes à partir d'une fenêtre PowerShell Windows :

        $subscriptionName = "<SubscriptionName>"
        $clusterName = "<ClusterName>"
        $location = "<MicrosoftDataCenter>"
        $clusterNodes = <ClusterSizeInNodes>

        $storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
        $containerName_Default = "<DefaultFileSystemContainerName>"

        $storageAccountName_Add1 = "<AdditionalStorageAccountName>"

        $hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
        $hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
        $oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
        $oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

        # Get the storage account keys
        Select-AzureSubscription $subscriptionName
        $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
        $storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

        $oozieCreds = Get-Credential -Message "Oozie metastore"
        $hiveCreds = Get-Credential -Message "Hive metastore"

        # Create a Blob storage container
        $dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
        New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

        # Create a new HDInsight cluster
        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
            Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
            Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
            Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
            Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
                New-AzureHDInsightCluster -Name $clusterName -Location $location

    > [WACOM.NOTE] La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données SQL Azure, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est en cours d'exécution. Une fois que vous êtes dans la vue serveur, cliquez sur **Configurer**, puis, pour **Services Windows Azure**, cliquez sur **Oui**. Cliquez ensuite sur **Enregistrer**.

**Pour afficher la liste des clusters HDInsight**

-   Exécutez les commandes suivantes depuis une fenêtre de console Azure PowerShell pour répertorier le cluster HDInsight et vérifier qu'il a été approvisionné correctement :

        Get-AzureHDInsightCluster -Name <ClusterName>

## <span id="cli"></span></a> Utilisation de la ligne de commande interplateforme

Une autre possibilité pour la mise en service d'un cluster HDInsight est l'interface de ligne de commande interplateforme. L'outil en ligne de commande est implémenté dans Node.js. Vous pouvez l'utiliser sur toute plateforme prenant en charge Node.js, y compris Windows, Mac et Linux. Il s'agit d'une technologie open source. Le code source est géré dans GitHub sur <https://github.com/Azure/azure-sdk-tools-xplat>. Pour une aide générale sur l'utilisation de l'interface de ligne de commande, consultez la page [Utilisation des outils en ligne de commande Azure pour Mac et Linux][]. Pour accéder à une documentation de référence exhaustive, consultez la rubrique [Documentation de l’outil en ligne de commande Azure pour Mac et Linux][]. Cet article traite uniquement de l'utilisation de l'interface de ligne de commande dans Windows.

Les procédures suivantes sont nécessaires pour mettre en service un cluster HDInsight en utilisant la ligne de commande interplateforme :

-   Installation de la ligne de commande interplateforme
-   Téléchargement et importation de paramètres de publication de compte Azure
-   Création d’un compte de stockage Azure
-   Mise en service d'un cluster

L'interface de ligne de commande peut être installée au moyen du *Gestionnaire de package Node.js (NPM)* ou de Windows Installer. Microsoft vous recommande d'effectuer l'installation avec une seule des deux options.

**Pour installer l'interface de ligne de commande au moyen de NPM**

1.  Accédez à **www.nodejs.org**.
2.  Cliquez sur **INSTALL** et suivez les instructions en conservant les paramètres par défaut.
3.  Ouvrez **Invite de commandes** (ou *Invite de commandes Azure* ou *Invite de commandes développeur pour VS2012*) à partir de votre poste de travail.
4.  Exécutez la commande suivante dans la fenêtre d'invite de commandes.

        npm install -g azure-cli

    > [WACOM.NOTE] Si un message d'erreur indique que la commande NPM est introuvable, vérifiez que les chemins d'accès suivants se trouvent dans la variable d'environnement : *C:\\Program Files (x86)\\nodejs;C:\\Users[nom\_utilisateur]\\AppData\\Roaming\\npm* ou *C:\\Program Files\\nodejs;C:\\Users[nom\_utilisateur]\\AppData\\Roaming\\npm*

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

**Pour télécharger et importer des paramètres de publication**

Avant d'utiliser l'interface de ligne de commandes, vous devez configurer la connectivité entre votre poste de travail et Azure. L'interface de ligne de commande se sert des informations sur votre abonnement Azure pour se connecter à votre compte. Ces informations peuvent être obtenues d'Azure dans un fichier de paramètres de publication. Ce dernier peut ensuite être importé en tant que paramètre de configuration local persistant dont l'interface de ligne de commande se servira pour les opérations ultérieures. Vous importez vos paramètres de publication une fois pour toutes.

> [WACOM.NOTE] Le fichier de paramètres de publication contient des informations critiques. Microsoft recommande de supprimer le fichier ou de prendre des mesures supplémentaires pour chiffrer le dossier utilisateur contenant le fichier. Sous Windows, modifiez les propriétés de dossier ou utilisez BitLocker.

1.  Ouvrez une **Invite de commandes**.
2.  Exécutez la commande suivante pour télécharger le fichier de paramètres de publication.

        azure account download

    ![HDI.CLIAccountDownloadImport][]

    La commande lance la page web à partir de laquelle vous pouvez télécharger le fichier des paramètres de publication.

3.  Lorsque vous êtes invité à enregistrer le fichier, cliquez sur **Enregistrer** et indiquez l'emplacement où le fichier doit être enregistré.
4.  À partir de la fenêtre d'invite de commandes, exécutez la commande suivante pour importer le fichier de paramètres de publication :

        azure account import <file>

    Dans la capture d'écran précédente, le fichier de paramètres de publication a été enregistré dans le dossier C:\\HDInsight sur le poste de travail.

**Pour créer un compte Azure Storage**

HDInsight utilise un conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage est nécessaire avant de pouvoir créer un cluster HDInsight. Il doit se trouver dans le même centre de données.

-   À partir de la fenêtre d'invite de commandes, exécutez le commande suivante pour créer un compte de stockage Azure :

        azure storage account create [options] <StorageAccountName>

    Lorsque vous êtes invité à indiquer un emplacement, sélectionnez un emplacement où un cluster HDINsight peut être approvisionné. Le stockage doit se trouver au même emplacement que le cluster HDInsight. Actuellement, seules les régions suivantes peuvent héberger des clusters HDInsight : **Est de l'Asie**, **Asie du Sud-Est**, **Europe du Nord**, **Europe de l'Ouest**, **Est des États-Unis**, **Ouest des États-Unis**, **Nord du centre des États-Unis** et **Sud du centre des États-Unis**.

Pour plus d'informations sur la création d'un compte Azure Storage au moyen du portail de gestion Azure, consultez la rubrique [Création d'un compte de stockage][].

Si vous disposez déjà d'un compte de stockage mais que vous ne connaissez ni le nom ni la clé du compte, vous pouvez utiliser les commandes suivantes pour récupérer les informations :

    -- lists storage accounts
    azure storage account list

    -- Shows information for a storage account
    azure storage account show <StorageAccountName>

    -- Lists the keys for a storage account
    azure storage account keys list <StorageAccountName>

Pour des détails sur l'obtention d'informations avec le portail de gestion, consultez la section *Affichage, copie et régénération de clés d'accès de stockage* de la page [Gestion des comptes de stockage][].

Un cluster HDInsight nécessite également un conteneur dans un compte de stockage. Si le compte de stockage que vous fournissez ne dispose pas déjà d'un conteneur, la commande *azure hdinsight cluster create* vous demande un nom de conteneur et le crée également. Si vous avez choisi de créer le conteneur au préalable, vous pouvez utiliser la commande suivante :

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
        

Une fois que le compte de stockage et le conteneur d'objets blob sont prêts, vous êtes prêt à créer un cluster.

**Pour mettre en service un cluster HDInsight**

-   À partir de la fenêtre d'invite de commandes, exécutez la commande suivante :

        azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <SorageContainerName> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

    ![HDI.CLIClusterCreation][]

**Pour mettre en service un cluster HDInsight au moyen d'un fichier de configuration**

Généralement, vous approvisionnez un cluster HDInsight, exécutez les tâches, puis supprimez le cluster pour réduire les coûts. L'interface de ligne de commande vous donne la possibilité d'enregistrer les configurations dans un fichier, de sorte que vous pouvez les réutiliser chaque fois que vous mettez en service un cluster.

-   À partir de la fenêtre d'invite de commandes, exécutez les commandes suivantes :

        #Create the config file
        azure hdinsight cluster config create <file> 

        #Add commands to create a basic cluster
        azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

        #If requred, include commands to use additional blob storage with the cluster
        azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
               --storageAccountKey "<StorageAccountKey>"

        #If required, include commands to use a SQL database as a Hive metastore
        azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
               --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

        #If required, include commands to use a SQL database as an Oozie metastore 
        azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
               --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

        #Run this command to create a cluster using the config file     
        azure hdinsight cluster create --config <file>

    > [WACOM.NOTE] La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données SQL Azure, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est en cours d'exécution. Une fois que vous êtes dans la vue serveur, cliquez sur **Configurer**, puis, pour **Services Windows Azure**, cliquez sur **Oui**. Cliquez ensuite sur **Enregistrer**.

    ![HDI.CLIClusterCreationConfig][]

**Pour énumérer et afficher les détails de cluster**

-   Utilisez les commandes suivantes pour énumérer et afficher les détails de cluster :

        azure hdinsight cluster list
        azure hdinsight cluster show <ClusterName>

    ![HDI.CLIListCluster][]

**Pour supprimer un cluster**

-   Utilisez les commandes suivantes pour supprimer un cluster :

        azure hdinsight cluster delete <ClusterName>

## <span id="sdk"></span></a> Utilisation du Kit de développement logiciel (SDK) HDInsight .NET

Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation de HDInsight à partir d'une application .NET.

Les procédures suivantes sont nécessaires pour approvisionner un cluster HDInsight à l'aide du Kit de développement logiciel (SDK) :

-   Installation du Kit de développement logiciel (SDK) HDInsight .NET
-   Création d'un certificat auto-signé
-   Création d'une application console
-   Exécution de l'application

**Pour installer le Kit de développement logiciel (SDK) HDInsight .NET**

Vous pouvez installer la dernière version du Kit de développement logiciel (SDK) sur [NuGet][]. Vous pourrez consulter les instructions dans la procédure suivante.

**Pour créer un certificat auto-signé**

1.  Créez un certificat auto-signé utilisé pour authentifier les demandes. Vous pouvez utiliser IIS ou [makecert][] pour créer le certificat.

2.  Recherchez l'emplacement du certificat, cliquez avec le bouton droit sur le certificat, cliquez sur **Installer le certificat** et installez le certificat dans le magasin personnel de l'ordinateur. Modifiez les propriétés du certificat de manière à lui attribuer un nom convivial.

3.  Importez le certificat dans le portail de gestion Azure. Dans le portail, cliquez sur **Paramètres** en bas à gauche de la page, puis cliquez sur **Certificats de gestion**. Au bas de la page, cliquez sur **Télécharger** et suivez les instructions pour télécharger le fichier .cer que vous avez créé à l'étape précédente.

    ![HDI.ClusterCreate.UploadCert][]

**Pour créer une application console Visual Studio**

1.  Ouvrez Visual Studio 2013.

2.  Dans le menu Fichier, cliquez sur **Nouveau**, puis sur **Projet**.

3.  Dans Nouveau projet, entrez ou sélectionnez les valeurs suivantes :

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propriété</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valeur</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Catégorie</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Modèles/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Modèle</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Application console</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nom</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
	</table>

4.  Cliquez sur **OK** pour créer le projet.

5.  Dans le menu **Outils**, cliquez sur **Gestionnaire de package Nuget**, puis sur **Console du Gestionnaire de package**.

6.  Exécutez les commandes suivantes dans la console pour installer les packages.

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

    Cette commande ajoute des bibliothèques .NET et leurs références nécessaires au projet Visual Studio en cours.

7.  Dans l'Explorateur de solutions, double-cliquez sur **Program.cs** pour l'ouvrir.

8.  Ajoutez les instructions using suivantes au début du fichier :

        using System.Security.Cryptography.X509Certificates;
        using Microsoft.WindowsAzure.Management.HDInsight;
        using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

9.  Dans la fonction Main(), copiez et collez le code suivant :

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate your created earlier  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the storage account if it doesn't exist.

        // Create the container if it doesn't exist.

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

        // Supply th cluster information
        ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
        };

        // Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. Remplacez les variables au début de la fonction main().

**Pour exécuter l'application**

Lorsque l'application est ouverte dans Visual Studio, appuyez sur **F5** pour l'exécuter. Une fenêtre de console doit s'ouvrir et afficher l'état de l'application. La création d'un cluster HDInsight peut prendre plusieurs minutes.

## <span id="nextsteps"></span></a>Étapes suivantes

Cet article vous a présenté différentes méthodes pour configurer un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

-   [Prise en main d'Azure HDInsight][]
-   [Administration de HDInsight à l'aide de PowerShell][Administration de HDInsight avec PowerShell]
-   [Envoi de tâches Hadoop par programme][]
-   [Documentation du Kit de développement logiciel (SDK) Azure HDInsight][]

  [Approvisionnement d'un cluster HBase dans HDInsight]: http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/
  [Différences entre Hadoop et HBase]: http://go.microsoft.com/fwlink/?LinkId=510237
  [Apache Hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
  [Présentation de Hadoop dans HDInsight]: ../hdinsight-introduction/
  [Prise en main d'Azure HDInsight]: ../hdinsight-get-started/
  [formules d'abonnement]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [offres spéciales membres]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [version d'évaluation gratuite]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Utilisation du portail de gestion Azure]: #portal
  [Utilisation d'Azure PowerShell]: #powershell
  [Utilisation de la ligne de commande interplateforme]: #cli
  [Utilisation du Kit de développement logiciel (SDK) HDInsight .NET]: #sdk
  [Étapes suivantes]: #nextsteps
  [Utilisation du stockage d'objets blob Azure avec HDInsight]: ../hdinsight-use-blob-storage/
  [Création d'un compte de stockage]: ../storage-create-storage-account/
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [HDI.CustomCreateCluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [HDI.CustomCreateCluster.ClusterUser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
  [HDI.CustomCreateCluster.StorageAccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
  [HDI.CustomCreateCluster.AddOnStorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png
  [Installation et configuration d'Azure PowerShell]: ../install-configure-powershell/
  [Administration de HDInsight avec PowerShell]: ../hdinsight-administer-use-powershell/
  [Référence des cmdlets HDInsight]: http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx
  [HDI.CLI.Provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png
  [Utilisation des outils en ligne de commande Azure pour Mac et Linux]: ../xplat-cli/
  [Documentation de l’outil en ligne de commande Azure pour Mac et Linux]: ../command-line-tools/
  [HDI.CLIAccountDownloadImport]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
  [Gestion des comptes de stockage]: ../storage-manage-storage-account/
  [HDI.CLIClusterCreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
  [HDI.CLIClusterCreationConfig]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
  [HDI.CLIListCluster]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "Énumérer et afficher les clusters"
  [NuGet]: http://nuget.codeplex.com/wikipage?title=Getting%20Started
  [makecert]: http://msdn.microsoft.com/en-us/library/bfsktky3(v=vs.110).aspx
  [HDI.ClusterCreate.UploadCert]: ./media/hdinsight-get-started/HDI.ClusterCreate.UploadCert.png
  [Envoi de tâches Hadoop par programme]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Documentation du Kit de développement logiciel (SDK) Azure HDInsight]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx
