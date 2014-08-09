<properties linkid="manage-services-hdinsight-provision-hdinsight-clusters" urlDisplayName="HDInsight Administration" pageTitle="Provision HDInsight clusters | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to provision clusters for Azure HDInsight using the management portal, PowerShell, or the command line." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Provision HDInsight clusters" authors="jgao" />

Mise en service de clusters HDInsight
=====================================

Cet article va vous présenter différentes méthodes pour mettre en service un cluster HDInsight.

**Configuration requise :**

Avant de commencer la lecture de cet article, vous devez disposer des éléments suivants :

-   Un abonnement Azure. Azure est une plateforme disponible par abonnement. Les cmdlets HDInsight PowerShell permettent d'effectuer les tâches associées à votre abonnement. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les [formules d'abonnement](https://www.windowsazure.com/en-us/pricing/purchase-options/), les [offres spéciales membres](https://www.windowsazure.com/en-us/pricing/member-offers/) ou la [version d'évaluation gratuite](https://www.windowsazure.com/en-us/pricing/free-trial/).

Dans cet article
----------------

-   [Utilisation du portail de gestion Azure](#portal)
-   [Utilisation d'Azure PowerShell](#powershell)
-   [Utilisation de la ligne de commande interplateforme](#cli)
-   [Utilisation du Kit de développement logiciel (SDK) HDInsight .NET](#sdk)
-   [Étapes suivantes](#nextsteps)

Utilisation du portail de gestion Azure
---------------------------------------

Le cluster HDInsight utilise un conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage se trouvant dans le même centre de données est nécessaire avant de pouvoir créer un cluster HDInsight. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight](/en-us/manage/services/hdinsight/howto-blob-store/). Pour plus d'informations sur la création d'un compte Azure Storage, consultez la page [Création d'un compte de stockage](/en-us/manage/services/storage/how-to-create-a-storage-account/).

> [WACOM.NOTE] Actuellement, seules les régions Asie du Sud-Est, Europe du Nord, Europe de l'Ouest, Est des États-Unis et Ouest des États-Unis peuvent héberger des clusters HDInsight.

Cette session décrit la procédure de création d'un cluster HDInsight au moyen de l'option de création personnalisée. Pour des informations sur l'utilisation de l'option de création rapide, consultez la rubrique [Prise en main d'Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/).

**Pour créer un cluster HDInsight au moyen de l'option de création personnalisée**

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/).
2.  Dans la partie inférieure de la page, cliquez sur **+ NEW**, sur **DATA SERVICES**, sur **HDINSIGHT**, puis sur **CUSTOM CREATE**.
3.  Dans la page Cluster Details, tapez ou choisissez les valeurs suivantes :

    <table data-morhtml="true" border="1">
     <tr data-morhtml="true"><th data-morhtml="true">Propri&eacute;t&eacute;</th><th data-morhtml="true">Valeur</th></tr>
     <tr data-morhtml="true"><td data-morhtml="true">CLUSTER NAME</td>
         <td data-morhtml="true"><p data-morhtml="true">Nom du cluster. </p>
             <ul data-morhtml="true">
             <li data-morhtml="true">Le nom DNS doit commencer et finir par un caract&egrave;re alphanum&eacute;rique, et peut contenir des traits d'union.</li>
             <li data-morhtml="true">Le champ doit &ecirc;tre une cha&icirc;ne comportant entre 3 et 63&nbsp;caract&egrave;res.</li>
             </ul></td></tr>
     <tr data-morhtml="true"><td data-morhtml="true">DATA NODES</td>
         <td data-morhtml="true">Sp&eacute;cifiez le nombre de n&oelig;uds du cluster. La valeur par d&eacute;faut est 4.</td></tr>
     <tr data-morhtml="true"><td data-morhtml="true">HDINSIGHT VERSION</td>
         <td data-morhtml="true">Choisissez la version. La valeur par d&eacute;faut est 2.0 ex&eacute;cutant des clusters Hadoop&nbsp;1.2.  La version&nbsp;3.0 utilise des clusters Hadoop&nbsp;2.2. Pour plus d'informations, consultez la rubrique <a data-morhtml="true" href="http://www.windowsazure.com/fr-fr/manage/services/hdinsight/versioning-in-hdinsight/">Version de Hadoop dans Azure HDInsight</a>.</td></tr>
     <tr data-morhtml="true"><td data-morhtml="true">REGION</td>
         <td data-morhtml="true">Sp&eacute;cifiez le centre de donn&eacute;es o&ugrave; le cluster est install&eacute;. L'emplacement doit &ecirc;tre identique au stockage d'objets blob Azure qui sera utilis&eacute; en tant que syst&egrave;me de fichiers par d&eacute;faut. Actuellement, vous pouvez uniquement choisir *Asie du Sud-Est*, *Europe du Nord*, *Europe de l'Ouest*, *Est des &Eacute;tats-Unis* ou *Ouest des &Eacute;tats-Unis*.</td>
     </tr>
 	</table>

    ![HDI.CustomProvision.Page1](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png)

4.  Cliquez sur la flèche vers la droite dans le coin inférieur droit de la page.
5.  Sur la page Configure Cluster User, tapez ou choisissez la valeur suivante :

    <table data-morhtml="true" border="1">
     <tr data-morhtml="true"><th data-morhtml="true">Propri&eacute;t&eacute;</th><th data-morhtml="true">Valeur</th></tr>
     <tr data-morhtml="true"><td data-morhtml="true">USER NAME</td>
         <td data-morhtml="true">Sp&eacute;cifiez le nom d'utilisateur de cluster HDInsight.</td></tr>
     <tr data-morhtml="true"><td data-morhtml="true"><p data-morhtml="true">PASSWORD</p><p data-morhtml="true">CONFIRM PASSWORD</p></td>
         <td data-morhtml="true">Sp&eacute;cifiez le mot de passe de cluster HDInsight.</td></tr>
     <tr data-morhtml="true"><td data-morhtml="true">Enter Hive/Oozie Metastore</td>
         <td data-morhtml="true">Sp&eacute;cifiez une base de donn&eacute;es SQL dans le m&ecirc;me centre de donn&eacute;es devant &ecirc;tre utilis&eacute; comme metastore Hive/Oozie.</td></tr>
     <tr data-morhtml="true"><td data-morhtml="true">HIVE META/OOZIESTORE DATABASE</td>
         <td data-morhtml="true">Sp&eacute;cifiez la base de donn&eacute;es SQL Azure qui sera utilis&eacute;e comme metastore pour Hive/Oozie. Cette base de donn&eacute;es SQL doit se trouver dans le m&ecirc;me centre de donn&eacute;es que le cluster HDInsight. La zone de liste r&eacute;pertorie uniquement les bases de donn&eacute;es SQL situ&eacute;es dans le m&ecirc;me centre de donn&eacute;es que celui sp&eacute;cifi&eacute; dans la page Cluster Details.</td></tr>
     <tr data-morhtml="true"><td data-morhtml="true">DATABASE USER</td>
         <td data-morhtml="true">Sp&eacute;cifiez l'utilisateur de base de donn&eacute;es SQL qui sera utilis&eacute; pour se connecter &agrave; la base de donn&eacute;es.</td></tr>
     <tr data-morhtml="true"><td data-morhtml="true">DATABASE USER PASSWORD</td>
         <td data-morhtml="true">Sp&eacute;cifiez le mot de passe de la base de donn&eacute;es SQL.</td></tr>
 </table>

    ![HDI.CustomProvision.Page2](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png)

6.  Cliquez sur la flèche vers la droite dans le coin inférieur droit de la page.
7.  Dans la page Storage Account, tapez ou choisissez les valeurs suivantes :

    <table data-morhtml="true" border="1">
     <tr data-morhtml="true"><th data-morhtml="true">Propri&eacute;t&eacute;</th><th data-morhtml="true">Valeur</th></tr>
     <tr data-morhtml="true"><td data-morhtml="true">STORAGE ACCOUNT</td>
         <td data-morhtml="true">Sp&eacute;cifiez le compte Azure Storage qui sera utilis&eacute; comme syst&egrave;me de fichiers par d&eacute;faut pour le cluster HDInsight. Vous pouvez choisir l'une des trois options suivantes&nbsp;:
         <ul data-morhtml="true">
             <li data-morhtml="true">Use Existing Storage</li>
             <li data-morhtml="true">Create New Storage</li>
             <li data-morhtml="true">Use Storage From Another Subscription</li>
         </ul>
         </td></tr>
     <tr data-morhtml="true"><td data-morhtml="true">ACCOUNT NAME</td>
         <td data-morhtml="true">Lorsque l'option <b data-morhtml="true">Use Existing Storage</b> est choisie, la zone de liste affiche uniquement les comptes de stockage se trouvant dans le m&ecirc;me centre de donn&eacute;es.</td></tr>
     <tr data-morhtml="true"><td data-morhtml="true">ACCOUNT KEY</td>
         <td data-morhtml="true">Ce champ est uniquement disponible lorsque l'option <strong data-morhtml="true">Use Storage From Another Subscription</strong> est choisie dans le champ STORAGE ACCOUNT.</td></tr>	
     <tr data-morhtml="true"><td data-morhtml="true">DEFAULT CONTAINER</td>
         <td data-morhtml="true">Le conteneur par d&eacute;faut du compte de stockage sera utilis&eacute; comme syst&egrave;me de fichiers pour le cluster HDInsight. Lorsque l'option <strong data-morhtml="true">Use Existing Storage</strong> du champ STORAGE ACCOUNT et l'option <strong data-morhtml="true">Create default container</strong> du champ DEFAULT CONTAINER sont choisies, le nom du conteneur par d&eacute;faut est identique &agrave; celui du cluster. Si un conteneur portant le nom du cluster existe d&eacute;j&agrave;, un num&eacute;ro de s&eacute;quence est ajout&eacute; au nom de conteneur. Par exemple, mon_conteneur1, mon_conteneur2, et ainsi de suite.</td></tr>
     <tr data-morhtml="true"><td data-morhtml="true">ADDITIONAL STORAGE ACCOUNTS</td>
         <td data-morhtml="true">HDInsight prend en charge plusieurs comptes de stockage. Le nombre de comptes de stockage suppl&eacute;mentaires pouvant &ecirc;tre utilis&eacute;s par un cluster n'est pas limit&eacute;. Toutefois, si vous cr&eacute;ez un cluster au moyen du portail de gestion, la limite est &eacute;tablie &agrave; sept en raison de contraintes li&eacute;es &agrave; l'interface utilisateur. Chaque compte de stockage suppl&eacute;mentaire que vous sp&eacute;cifiez dans ce champ ajoute une page Storage Account suppl&eacute;mentaire vous permettant de sp&eacute;cifier les informations de compte. Par exemple, dans la capture d'&eacute;cran suivante, 1&nbsp;compte de stockage suppl&eacute;mentaire est s&eacute;lectionn&eacute;, la page&nbsp;4 est ajout&eacute;e &agrave; la bo&icirc;te de dialogue.</td></tr>		
 	</table>

    ![HDI.CustomProvision.Page3](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png)

8.  Cliquez sur la flèche vers la droite dans le coin inférieur droit de la page.
9.  Dans la page Storage Account, entrez les informations du compte de stockage supplémentaire :

    ![HDI.CustomProvision.Page4](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png)

10. Cliquez sur le bouton Complete (icône en forme de coche) dans le coin inférieur droit pour démarrer le processus de mise en service du cluster HDInsight.

La mise en service d'un cluster peut prendre plusieurs minutes. À l'issue du processus, la colonne d'état du cluster indique **Running**.

> [WACOM.NOTE] Une fois qu'un compte Azure Storage est choisi pour votre cluster HDInsight, vous ne pouvez ni supprimer le compte, ni le remplacer par un autre.

Utilisation d'Azure PowerShell
------------------------------

Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Pour plus d'informations sur la configuration d'un poste de travail pour exécuter des cmdlets HDInsight Powershell, consultez la rubrique [Installation et configuration d'Azure PowerShell](/fr-fr/documentation/articles/install-configure-powershell/). Pour plus d'informations sur l'utilisation de PowerShell avec HDInsight, consultez la rubrique [Administration de HDInsight avec PowerShell](/en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/). Pour la liste des cmdlets PowerShell pour HDInsight, consultez la rubrique [Référence des cmdlets HDInsight](http://msdn.microsoft.com/fr-fr/library/windowsazure/dn479228.aspx).

Les procédures suivantes sont nécessaires pour mettre en service un cluster HDInsight en utilisant PowerShell :

-   Création d'un compte Azure Storage
-   Création d'un conteneur d'objets blob Azure
-   Création d'un cluster HDInsight

HDInsight utilise un conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage et un conteneur de stockage sont nécessaires avant de pouvoir créer un cluster HDInsight. Ce compte de stockage doit se situer dans le même centre de données que le cluster HDInsight.

**Pour créer un compte Azure Storage**

-   Exécutez les commandes suivantes à partir d'une fenêtre de console Azure PowerShell :

		$storageAccountName = "<StorageAcccountName>"
		$location = "<MicrosoftDataCenter>"     # Par exemple, "Ouest des États-Unis"
            
		# Créez un compte Azure Storage
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

    Si vous disposez déjà d'un compte de stockage mais que vous ne connaissez ni le nom ni la clé du compte, vous pouvez utiliser les commandes PowerShell suivantes pour récupérer les informations :

		# Affichez la liste des comptes de stockage pour l'abonnement actif
          Get-AzureStorageAccount

		# Affichez la liste des clés d'un compte de stockage
          Get-AzureStorageKey "<StorageAccountName>"

**Pour créer un conteneur Azure Storage**

-   Exécutez les commandes suivantes à partir d'une fenêtre Azure PowerShell :

		$storageAccountName = "<StorageAccountName>"
		$storageAccountKey = "<StorageAccountKey>"
		$containerName="<ContainerName>"

		# Créez un objet de contexte de stockage
		$destContext = New-AzureStorageContext 	-StorageAccountName $storageAccountName 
                                                -StorageAccountKey $storageAccountKey  
             
		# Créez un conteneur de stockage d'objets blob
		New-AzureStorageContainer -Name $containerName -Context $destContext

Une fois que le compte de stockage et le conteneur d'objets blob sont prêts, vous êtes prêt à créer un cluster.

**Pour mettre en service un cluster HDInsight**

-   Exécutez les commandes suivantes à partir d'une fenêtre Azure PowerShell :

		$subscriptionName = "<SubscriptionName>"     	# Nom de l'abonnement Azure
		$storageAccountName = "<StorageAccountName>"    # Compte Azure Storage hébergeant le conteneur par défaut. Le conteneur par défaut sera utilisé comme système de fichiers par défaut.
		$containerName = "<ContainerName>"              # Conteneur de stockage d'objets blob Azure qui sera utilisé comme système de fichiers par défaut pour le cluster HDInsight

		$clusterName = "<HDInsightClusterName>"         # Nom que vous donnerez à votre cluster HDInsight.
		$location = "<MicrosoftDataCenter>"             # Emplacement du cluster HDInsight. Il doit s'agir du même centre de données que le compte de stockage.
		$clusterNodes = <ClusterSizeInNodes>          	# Nombre de nœuds du cluster HDInsight.

		# Obtenez la clé primaire de stockage sur la base du nom du compte
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

		# Créez un cluster HDInsight
		New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

    La mise en service du cluster peut prendre plusieurs minutes.

    ![HDI.CLI.Provision](./media/hdinsight-provision-clusters/HDI.ps.provision.png)

Vous pouvez également mettre en service le cluster et le configurer de façon à connecter plusieurs stockages d'objets blob Azure ou des metastores Hive et Oozie. Cette fonctionnalité avancée vous permet de séparer la durée de vie de vos données et métadonnées de celle du cluster.

**Pour mettre en service un cluster HDInsight au moyen de la configuration**

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
            
          # Obtenez les clés de compte de stockage
          Select-AzureSubscription $subscriptionName
          $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
          $storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }
            
          $oozieCreds = Get-Credential -Message "Oozie metastore"
          $hiveCreds = Get-Credential -Message "Hive metastore"
            
          # Créez un conteneur de stockage d'objets blob
          #$dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
          #New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context
            
          # Créez un cluster HDInsight
          $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
              Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
              Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
              Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
              Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
                  New-AzureHDInsightCluster -Name $clusterName -Location $location

**Pour afficher la liste des clusters HDInsight**

-   Exécutez les commandes suivantes à partir d'une fenêtre Azure PowerShell :

          Get-AzureHDInsightCluster -Name <ClusterName>

Utilisation de la ligne de commande interplateforme
---------------------------------------------------

Une autre possibilité pour la mise en service d'un cluster HDInsight est l'interface de ligne de commande interplateforme. L'outil en ligne de commande est mis en œuvre dans Node.js. Il peut être utilisé sur toute plateforme prenant en charge Node.js, y compris Windows, Mac et Linux. Il s'agit d'une technologie open source. Le code source est géré dans GitHub sur <https://github.com/WindowsAzure/azure-sdk-tools-xplat>. Pour une aide générale sur l'utilisation de l'interface de ligne de commande, consultez la page [Utilisation des outils en ligne de commande Azure pour Mac et Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/). Pour accéder à une documentation de référence exhaustive, consultez la rubrique [Documentation de l’outil en ligne de commande Azure pour Mac et Linux](/en-us/manage/linux/other-resources/command-line-tools/). Cet article traite uniquement de l'utilisation de l'interface de ligne de commande dans Windows.

Les procédures suivantes sont nécessaires pour mettre en service un cluster HDInsight en utilisant la ligne de commande interplateforme :

-   Installation de la ligne de commande interplateforme
-   Téléchargement et importation de paramètres de publication de compte Azure
-   Création d'un compte Azure Storage
-   Mise en service d'un cluster

L'interface de ligne de commande peut être installée au moyen du *Gestionnaire de package Node.js (NPM)* ou de Windows Installer.

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

1.  Accédez à **http://www.windowsazure.com/fr-fr/downloads/**.
2.  Faites défiler l'écran pour accéder à la section **Outils de ligne de commande**, puis cliquez sur **Interface de ligne de commande interplateforme** et suivez les étapes de l'Assistant Web Platform Installer.

Avant d'utiliser l'interface de ligne de commandes, vous devez configurer la connectivité entre votre poste de travail et Azure. L'interface de ligne de commande se sert des informations sur votre abonnement Azure pour se connecter à votre compte. Ces informations peuvent être obtenues d'Azure dans un fichier de paramètres de publication. Ce dernier peut ensuite être importé en tant que paramètre de configuration local persistant dont l'interface de ligne de commande se servira pour les opérations ultérieures. Vous importez vos paramètres de publication une fois pour toutes.

> [WACOM.NOTE] Le fichier de paramètres de publication contient des informations critiques. Il est recommandé de supprimer le fichier ou de prendre des mesures supplémentaires pour chiffrer le dossier utilisateur contenant le fichier. Sous Windows, modifiez les propriétés de dossier ou utilisez BitLocker.

**Pour télécharger et importer des paramètres de publication**

1.  Ouvrez une **Invite de commandes**.
2.  Exécutez la commande suivante pour télécharger le fichier de paramètres de publication.

        azure account download

    ![HDI.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png)

    La commande affiche les instructions permettant de télécharger le fichier, ainsi qu'une URL.

3.  Ouvrez **Internet Explorer** et accédez à l'URL figurant dans la fenêtre d'invite de commandes.
4.  Cliquez sur **Enregistrer** pour enregistrer le fichier sur le poste de travail.
5.  À partir de la fenêtre d'invite de commandes, exécutez la commande suivante pour importer le fichier de paramètres de publication :

        azure account import <file>

    Dans la capture d'écran précédente, le fichier de paramètres de publication a été enregistré dans le dossier C:\\HDInsight sur le poste de travail.

HDInsight utilise un conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage est nécessaire avant de pouvoir créer un cluster HDInsight. Il doit se trouver dans le même centre de données.

**Pour créer un compte Azure Storage**

-   À partir de la fenêtre d'invite de commandes, exécutez la commande suivante :

          azure account storage create [options] <StorageAccountName>

Pour plus d'informations sur la création d'un compte Azure Storage au moyen du portail de gestion Azure, consultez la rubrique [Création d'un compte de stockage](/en-us/manage/services/storage/how-to-create-a-storage-account/).

Si vous disposez déjà d'un compte de stockage mais que vous ne connaissez ni le nom ni la clé du compte, vous pouvez utiliser les commandes suivantes pour récupérer les informations :

    -- Énumère les comptes de stockage
    azure account storage list
    -- Affiche un compte de stockage
    azure account storage show <StorageAccountName>
    -- Énumère les clés pour un compte de stockage
    azure account storage keys list <StorageAccountName>

Pour plus d'informations sur l'obtention d'informations au moyen du portail de gestion, consultez la section *Affichage, copie et régénération des clés d'accès de stockage* de la rubrique [Gestion des comptes de stockage](/en-us/manage/services/storage/how-to-manage-a-storage-account/).

La commande *azure hdinsight cluster create* crée le conteneur s'il n'existe pas. Si vous avez choisi de créer le conteneur au préalable, vous pouvez utiliser la commande suivante :

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Une fois que le compte de stockage et le conteneur d'objets blob sont prêts, vous êtes prêt à créer un cluster.

**Pour créer un cluster HDInsight**

-   À partir de la fenêtre d'invite de commandes, exécutez la commande suivante :

          azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

    ![HDI.CLIClusterCreation](./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png)

Généralement, vous mettez en service un cluster HDInsight, vous exécutez ses tâches, puis vous supprimez le cluster pour réduire les coûts. L'interface de ligne de commande vous donne la possibilité d'enregistrer les configurations dans un fichier, de sorte que vous pouvez les réutiliser chaque fois que vous mettez en service un cluster.

**Pour mettre en service un cluster HDInsight au moyen d'un fichier de configuration**

-   À partir de la fenêtre d'invite de commandes, exécutez la commande suivante :

          azure hdinsight cluster config create <file>
             
          azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"
             
          azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
                 --storageAccountKey "<StorageAccountKey>"
             
          azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
                 --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"
             
          azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
                 --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"
             
          azure hdinsight cluster create --config <file>

    ![HDI.CLIClusterCreationConfig](./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png)

**Pour énumérer et afficher les détails de cluster**

-   Utilisez les commandes suivantes pour énumérer et afficher les détails de cluster :

          azure hdinsight cluster list
          azure hdinsight cluster show <ClusterName>

    ![HDI.CLIListCluster](./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "Énumérer et afficher les clusters")

**Pour supprimer un cluster**

-   Utilisez les commandes suivantes pour supprimer un cluster :

          azure hdinsight cluster delete <ClusterName>

Utilisation du Kit de développement logiciel (SDK) HDInsight .NET
-----------------------------------------------------------------

Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation de HDInsight à partir de .NET.

Les procédures suivantes sont nécessaires pour configurer un cluster HDInsight en utilisant le Kit de développement logiciel (SDK) :

-   Installation du Kit de développement logiciel (SDK) HDInsight .NET
-   Création d'une application console
-   Exécution de l'application

**Installation du Kit de développement logiciel (SDK) .NET HDInsight** : vous pouvez installer la dernière version du SDK publiée sur [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Vous pourrez consulter les instructions dans la procédure suivante.

**Création d'une application console Visual Studio**

1.  Ouvrez Visual Studio 2012.

2.  Dans le menu Fichier, cliquez sur **Nouveau**, puis sur **Projet**.

3.  Dans Nouveau projet, entrez ou sélectionnez les valeurs suivantes :

    <table data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
 <tr data-morhtml="true">
 <th data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propri&eacute;t&eacute;</th>
 <th data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valeur</th></tr>
 <tr data-morhtml="true">
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Cat&eacute;gorie</td>
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Mod&egrave;les/Visual C#/Windows</td></tr>
 <tr data-morhtml="true">
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Mod&egrave;le</td>
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Application console</td></tr>
 <tr data-morhtml="true">
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nom</td>
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
 </table>

4.  Cliquez sur **OK** pour créer le projet.

5.  Dans le menu **Outils**, cliquez sur **Library Package Manager**, puis sur **Console du gestionnaire de package**.

6.  Exécutez les commandes suivantes dans la console pour installer les packages.

         Install-Package Microsoft.WindowsAzure.Management.HDInsight

    Cette commande ajoute des bibliothèques .NET et leurs références nécessaires au projet Visual Studio en cours.

7.  Dans l'Explorateur de solutions, double-cliquez sur **Program.cs** pour l'ouvrir.

8.  Ajoutez les instructions using suivantes au début du fichier :

         using System.Security.Cryptography.X509Certificates;
         using Microsoft.WindowsAzure.Management.HDInsight;
         using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

9.  Dans la fonction Main(), copiez et collez le code suivant :

         string certfriendlyname = "<CertificateFriendlyName>";
         string subscriptionid = "<WindowsAzureSubscriptionID>";
         string clustername = "<HDInsightClusterName>";
         string location = "<MicrosoftDataCenter>";
         string storageaccountname = "<WindowsAzureStorageAccountName>";     // Full path must be used
         string storageaccountkey = "<WindowsAzureStorageAccountKey>";
         string containername = "<HDInsightDefaultContainerName>";
         string username = "<HDInsightUsername>";
         string password = "<HDInsightUserPassword>";
         int clustersize = <NumberOfNodesInTheCluster>;

         // Obtenez l'objet de certificat à partir du magasin de certificats en utilisant le nom convivial pour l'identifier.
         X509Store store = new X509Store();
         store.Open(OpenFlags.ReadOnly);
         X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

         // Si le compte de stockage n’existe pas, créez-le.

         // Si le conteneur n’existe pas, créez-le.

         // Créez un compte HDInsightClient.
         HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
         var client = HDInsightClient.Connect(creds);

         // Fournissez les informations de cluster.
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

         // Créez le cluster.
         Console.WriteLine("Création du cluster HDInsight ...");

         ClusterDetails cluster = client.CreateCluster(clusterInfo);

         Console.WriteLine("Cluster créé : {0}.", cluster.ConnectionUrl);
         Console.WriteLine("Appuyez sur ENTRÉE pour continuer.");
         Console.ReadKey();

10. Remplacez les variables au début de la fonction main().

**Exécution de l'application**

Lorsque l'application est ouverte dans Visual Studio, appuyez sur **F5** pour l'exécuter. Une fenêtre de console doit s'ouvrir et afficher l'état de l'application. La création d'un cluster HDInsight peut prendre plusieurs minutes.

Étapes suivantes
----------------

Cet article vous a présenté différentes méthodes pour mettre en service un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

-   [Prise en main d'Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Administration de HDInsight à l'aide de PowerShell](/en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/)
-   [Envoi de tâches Hadoop par programme](/en-us/manage/services/hdinsight/submit-hadoop-jobs-programmatically/)
-   [Documentation du Kit de développement logiciel (SDK) Azure HDInsight](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

