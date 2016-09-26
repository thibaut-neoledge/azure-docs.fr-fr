<properties
   pageTitle="Créer des clusters HDInsight avec Azure Data Lake Store à l'aide de PowerShell | Azure"
   description="Utiliser Azure PowerShell pour créer et utiliser les clusters HDInsight avec Azure Data Lake"
   services="data-lake-store,hdinsight" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/01/2016"
   ms.author="nitinme"/>

# Créer un cluster HDInsight avec Data Lake Store à l'aide d'Azure PowerShell

> [AZURE.SELECTOR]
- [Utilisation du portail](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Utiliser PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


Apprenez à utiliser Azure PowerShell pour configurer un cluster HDInsight (Hadoop, HBase ou Storm) avec accès à Azure Data Lake Store. Points importants à prendre en compte pour cette version :

* **Pour les clusters Spark (Linux) et Hadoop/Storm (Windows et Linux)**, le Data Lake Store n’est utilisable que comme compte de stockage supplémentaire. Le compte de stockage par défaut pour de tels clusters sera toujours Objets BLOB de stockage Azure (WASB).

* **Pour les clusters HBase (Windows et Linux)**, vous pouvez utiliser Data Lake Store comme stockage par défaut ou comme stockage supplémentaire.

> [AZURE.NOTE] Quelques points importants à prendre en compte.
> 
> * L’option permettant de créer des clusters HDInsight avec accès au Data Lake Store est disponible uniquement si vous utilisez HDInsight version 3.2 et 3.4 (pour des clusters Hadoop, HBase et Storm sur Windows et Linux). Pour les clusters Spark sur Linux, cette option est uniquement disponible sur des clusters HDInsight 3.4.
>
> * Comme mentionné ci-dessus, Data Lake Store est disponible en tant que stockage par défaut pour certains types de cluster (HBase) et en tant que stockage supplémentaire pour d’autres types de cluster (Hadoop, Spark, Storm). L’utilisation de Data Lake Store en tant que compte de stockage supplémentaire n’affecte pas les performances ni la capacité de lecture/écriture sur le stockage à partir du cluster. Dans un scénario où Data Lake Store est utilisé comme espace de stockage supplémentaire, les fichiers associés au cluster (par exemple, les journaux, etc.) sont écrits dans le stockage par défaut (objets Blob Azure), tandis que les données que vous souhaitez traiter peuvent être stockées dans un compte Data Lake Store.


Dans cet article, nous approvisionnons un cluster Hadoop avec Data Lake Store comme stockage supplémentaire.

Pour configurer HDInsight afin qu'il fonctionne avec Data Lake Store à l'aide de PowerShell, la procédure est la suivante :

* Créer un Azure Data Lake Store
* Configurer l'authentification pour définir un accès à Data Lake Store en fonction du rôle
* Créer un cluster HDInsight avec authentification à Data Lake Store
* Lancer une tâche de test sur le cluster

## Composants requis

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Activez votre abonnement Azure** pour la version d’évaluation publique de Data Lake Store. Consultez les [instructions](data-lake-store-get-started-portal.md#signup).
- **Kit de développement logiciel (SDK) Windows**. Vous pouvez l'installer [ici](https://dev.windows.com/fr-FR/downloads). Il vous permet de créer un certificat de sécurité.


##Installer Azure PowerShell 1.0 et versions ultérieures

Pour commencer, vous devez désinstaller les versions 0.9x d’Azure PowerShell. Pour vérifier la version PowerShell installée, exécutez la commande suivante dans une fenêtre PowerShell :

	Get-Module *azure*

Pour désinstaller l’ancienne version, exécutez **Programmes et fonctionnalités** dans le Panneau de configuration et supprimez la version installée si elle est antérieure à PowerShell 1.0.

Il existe deux options principales pour l’installation d’Azure PowerShell.

- [PowerShell Gallery](https://www.powershellgallery.com/). Exécutez les commandes suivantes à partir de PowerShell ISE avec élévation de privilèges ou de la console Windows PowerShell avec élévation de privilèges :

		# Install the Azure Resource Manager modules from PowerShell Gallery
		Install-Module AzureRM
		Install-AzureRM

		# Install the Azure Service Management module from PowerShell Gallery
		Install-Module Azure

		# Import AzureRM modules for the given version manifest in the AzureRM module
		Import-AzureRM

		# Import Azure Service Management module
		Import-Module Azure

	Pour plus d’informations, consultez [PowerShell Gallery](https://www.powershellgallery.com/).

- [Microsoft Web Platform Installer (WebPI)](http://aka.ms/webpi-azps). Si vous disposez d’Azure PowerShell 0.9.x, vous êtes invité à désinstaller cette version. Si vous avez installé des modules Azure PowerShell à partir de PowerShell Gallery, vous devez les supprimer avant l’installation pour garantir un environnement Azure PowerShell cohérent. Pour obtenir des instructions, consultez [Installer Azure PowerShell 1.0 via WebPI](https://azure.microsoft.com/blog/azps-1-0/).

WebPI reçoit des mises à jour mensuelles. PowerShell Gallery reçoit des mises à jour en continu. Si vous êtes familiarisé avec l’installation à partir de PowerShell Gallery, il s’agit du premier canal pour bénéficier des dernières nouveautés d’Azure PowerShell.


## Créer un Azure Data Lake Store

Pour créer un Data Lake Store, procédez comme suit.

1. Sur votre bureau, ouvrez une nouvelle fenêtre Azure PowerShell et entrez l'extrait de code suivant. Lorsque vous êtes invité à vous connecter, vérifiez que vous vous connectez en tant qu’administrateur/propriétaire de l’abonnement :

        # Log in to your Azure account
		Login-AzureRmAccount

		# List all the subscriptions associated to your account
		Get-AzureRmSubscription

		# Select a subscription
		Set-AzureRmContext -SubscriptionId <subscription ID>

		# Register for Data Lake Store
		Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

	>[AZURE.NOTE] Si vous recevez une erreur similaire à `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` pendant l’inscription du fournisseur de ressources Data Lake Store, il est possible que votre abonnement ne figure pas dans la liste approuvée pour Azure Data Lake Store. Veillez à activer votre abonnement Azure pour la version préliminaire publique de Data Lake Store en suivant ces [instructions](data-lake-store-get-started-portal.md#signup).

3. Un compte Azure Data Lake Store est associé à un groupe de ressources Azure. Commencez par créer un groupe de ressources Azure.

		$resourceGroupName = "<your new resource group name>"
    	New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

	![Créer un groupe de ressources Azure](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Créer un groupe de ressources Azure")

2. Créer un compte Azure Data Lake Store. Le nom de compte que vous spécifiez doit contenir uniquement des lettres minuscules et des chiffres.

		$dataLakeStoreName = "<your new Data Lake Store name>"
    	New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

	![Créer un compte Azure Data Lake](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Créer un compte Azure Data Lake")

3. Vérifiez que le compte a bien été créé.

		Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

	Le résultat doit être **True**.

4. Téléchargez des exemples de données sur Azure Data Lake. Nous les utiliserons plus loin dans cet article pour vérifier que les données sont accessibles à partir d'un cluster HDInsight. Si vous recherchez des exemples de données à charger, vous pouvez récupérer le dossier **Données Ambulance** dans le [Référentiel Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).


		$myrootdir = "/"
		Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## Configurer l'authentification pour définir un accès à Data Lake Store en fonction du rôle

Chaque abonnement Azure est associé à un Azure Active Directory. Les utilisateurs et services qui accèdent aux ressources de l’abonnement avec les API du portail Azure Classic ou d’Azure Resource Manager doivent au préalable s’authentifier avec cette application Azure Active Directory. L’accès est accordé aux abonnements et services Azure en leur affectant le rôle approprié sur une ressource Azure. Pour les services, un principal du service identifie le service dans Azure Active Directory (AAD). Cette section montre comment accorder l'accès à une ressource Azure (le compte Azure Data Lake Store créé précédemment) à un service d'application, comme HDInsight, en créant un principal du service pour l'application et en lui attribuant des rôles avec Azure PowerShell.

Pour configurer l'authentification Active Directory pour Azure Data Lake, vous devez effectuer les tâches suivantes.

* Créer un certificat auto-signé
* Créer une application dans Azure Active Directory et un principal du service

### Créer un certificat auto-signé

Assurez-vous que le [Kit de développement logiciel (SDK) Windows](https://dev.windows.com/fr-FR/downloads) est installé avant de suivre la procédure décrite dans cette section. Vous devez également avoir créé un répertoire, comme **C:\\mycertdir**, où sera créé le certificat.

1. Dans la fenêtre PowerShell, accédez à l’emplacement où vous avez installé le Kit de développement logiciel (SDK) Windows (en général, `C:\Program Files (x86)\Windows Kits\10\bin\x86`) et utilisez l’utilitaire [MakeCert][makecert] pour créer un certificat auto-signé et une clé privée. Utilisez les commandes suivantes.

		$certificateFileDir = "<my certificate directory>"
		cd $certificateFileDir
		$startDate = (Get-Date).ToString('MM/dd/yyyy')
		$endDate = (Get-Date).AddDays(365).ToString('MM/dd/yyyy')

		makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -b $startDate -e $endDate -r -len 2048

	Vous devrez entrer le mot de passe de la clé privée. Une fois la commande exécutée avec succès, **CertFile.cer** et **mykey.pvk** seront visibles dans le répertoire du certificat que vous avez spécifié.

4. Utilisez l’utilitaire [Pvk2Pfx][pvk2pfx] pour convertir en un fichier .pfx les fichiers .pvk et .cer créés par MakeCert. Exécutez la commande ci-dessous.

		pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

	Lorsque vous y êtes invité, entrez le mot de passe de la clé privée spécifié au préalable. La valeur que vous donnez au paramètre **-po** est le mot de passe associé au fichier .pfx. Une fois la commande exécutée avec succès, CertFile.pfx est visible dans le répertoire du certificat que vous avez spécifié.

###  Créer une application Azure Active Directory et un principal du service

Cette section décrit les étapes pour créer un principal du service pour une application Azure Active Directory, attribuer un rôle au principal du service et vous authentifier en tant que principal du service en fournissant un certificat. Exécutez les commandes suivantes pour créer une application dans Azure Active Directory.

1. Collez les applets de commande suivantes dans la fenêtre de la console PowerShell. Vérifiez que la valeur que vous donnez à la propriété **-DisplayName** est unique. En outre, les valeurs pour **-HomePage** et **-IdentiferUris** sont des valeurs d’espace réservé et ne sont pas vérifiées.

		$certificateFilePath = "$certificateFileDir\CertFile.pfx"

		$password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

		$certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

		$rawCertificateData = $certificatePFX.GetRawCertData()

		$credential = [System.Convert]::ToBase64String($rawCertificateData)

		$application = New-AzureRmADApplication `
					-DisplayName "HDIADL" `
					-HomePage "https://contoso.com" `
					-IdentifierUris "https://mycontoso.com" `
					-KeyValue $credential  `
					-KeyType "AsymmetricX509Cert"  `
					-KeyUsage "Verify"  `
					-StartDate $startDate  `
					-EndDate $endDate

		$applicationId = $application.ApplicationId

2. Créez un principal du service avec l'ID d'application.

		$servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

		$objectId = $servicePrincipal.Id

3. Accordez au principal du service l'accès au Data Lake Store que vous avez créé précédemment.

		Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All

	À l’invite, entrez **Y** pour confirmer.

## Créer un cluster HDInsight avec authentification à Data Lake Store

Dans cette section, nous créons un cluster HDInsight Hadoop. Pour cette version, le cluster HDInsight et le Data Lake Store doivent se situer au même emplacement (Est des États-Unis 2).

1. Commencez par récupérer l’ID du client de l’abonnement. Vous en aurez besoin ultérieurement.

		$tenantID = (Get-AzureRmContext).Tenant.TenantId

2. Pour cette version, pour un cluster Hadoop, Data Lake Store ne peut être utilisé que comme stockage supplémentaire pour le cluster. Le stockage par défaut sera toujours les objets blob Azure Storage (WASB). Par conséquent, nous allons tout d'abord créer le compte de stockage et les conteneurs de stockage requis par le cluster.

		# Create an Azure storage account
		$location = "East US 2"
		$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name

		New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

		# Create an Azure Blob Storage container
		$containerName = "<ContainerName>"              # Provide a container name
		$storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $containerName -Context $destContext

3. Créez le cluster HDInsight. Utilisez les applets de commande suivantes.

		# Set these variables
		$clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
		$clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
		$httpCredentials = Get-Credential
		$rdpCredentials = Get-Credential

		New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.2" -RdpCredential $rdpCredentials -RdpAccessExpiry (Get-Date).AddDays(14) -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

	Après exécution de l'applet de commande, le résultat se présente ainsi :

		Name                      : hdiadlcluster
		Id                        : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/hdiadlgroup/providers/Mi
		                            crosoft.HDInsight/clusters/hdiadlcluster
		Location                  : East US 2
		ClusterVersion            : 3.2.7.707
		OperatingSystemType       : Windows
		ClusterState              : Running
		ClusterType               : Hadoop
		CoresUsed                 : 16
		HttpEndpoint              : hdiadlcluster.azurehdinsight.net
		Error                     :
		DefaultStorageAccount     :
		DefaultStorageContainer   :
		ResourceGroup             : hdiadlgroup
		AdditionalStorageAccounts :

## Exécuter des tâches de test sur le cluster HDInsight pour utiliser Data Lake Store

Après avoir configuré un cluster HDInsight, vous pouvez exécuter des tâches de test sur le cluster pour vérifier que le cluster HDInsight peut accéder à Data Lake Store. Pour ce faire, nous allons exécuter un exemple de tâche Hive qui crée une table avec les exemples de données que vous avez téléchargés précédemment dans votre Data Lake Store.

### Pour un cluster Linux

Dans cette section, vous allez utiliser SSH dans le cluster et exécuter l’exemple de requête Hive. Windows ne fournit pas de client SSH intégré. Nous vous recommandons d’utiliser **PuTTY**, qui peut être téléchargé à partir de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Pour plus d’informations sur l’utilisation de PuTTY, consultez la rubrique [Utilisation de SSH avec Hadoop Linux dans HDInsight à partir de Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Une fois connecté, démarrez l'interface de ligne de commande Hive à l'aide de la commande suivante :

    	hive

2. À l’aide de l’interface CLI, entrez les instructions suivantes pour créer une table nommée **vehicles** en utilisant les exemples de données dans Data Lake Store :

		DROP TABLE vehicles;
		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
		SELECT * FROM vehicles LIMIT 10;

	Le résultat doit ressembler à ce qui suit :

		1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
		1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
		1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
		1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
		1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
		1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
		1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
		1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
		1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
		1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


### Pour un cluster Windows

Utilisez les applets de commande suivantes pour exécuter la requête Hive. Dans cette requête, nous créons une table à partir des données de Data Lake Store, puis nous exécutons une requête SELECT sur la table créée.

	$queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"

	$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

	$hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

	Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials

Cela aura le résultat suivant. La valeur 0 pour **ExitValue** dans le résultat suggère que la tâche a réussi.

	Cluster         : hdiadlcluster.
	HttpEndpoint    : hdiadlcluster.azurehdinsight.net
	State           : SUCCEEDED
	JobId           : job_1445386885331_0012
	ParentId        :
	PercentComplete :
	ExitValue       : 0
	User            : admin
	Callback        :
	Completed       : done

Récupérez le résultat de la tâche à l'aide de l'applet de commande suivante :

	Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials

Le résultat de la tâche se présente ainsi :

	1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
	1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
	1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
	1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
	1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
	1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
	1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
	1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
	1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
	1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


## Accéder à Data Lake Store avec les commandes HDFS

Une fois que vous avez configuré le cluster HDInsight pour qu'il utilise Data Lake Store, vous pouvez utiliser les commandes de l'interpréteur de commandes HDFS pour accéder au magasin.

### Pour un cluster Linux

Dans cette section, vous allez utiliser SSH dans le cluster et exécuter les commandes HDFS. Windows ne fournit pas de client SSH intégré. Nous vous recommandons d’utiliser **PuTTY**, qui peut être téléchargé à partir de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Pour plus d’informations sur l’utilisation de PuTTY, consultez la rubrique [Utilisation de SSH avec Hadoop Linux dans HDInsight à partir de Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Une fois connecté, utilisez la commande du système de fichiers HDFS suivante pour répertorier les fichiers dans le Data Lake Store.

	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Le fichier que vous avez téléchargé dans Data Lake Store doit y figurer.

	15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
	Found 1 items
	-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

Vous pouvez également utiliser la commande `hdfs dfs -put` pour charger des fichiers dans Data Lake Store, puis utiliser `hdfs dfs -ls` pour vérifier si les fichiers ont été chargés avec succès.


### Pour un cluster Windows

1. Inscrivez-vous au nouveau [portail Azure](https://portal.azure.com).

2. Cliquez sur **Parcourir**, puis sur **Clusters HDInsight** et enfin sur le cluster HDInsight que vous avez créé.

3. Dans le panneau du cluster, cliquez sur **Bureau à distance** puis, dans le panneau **Bureau à distance**, cliquez sur **Connexion**.

	![Accès à distance à un cluster HDI](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png "Créer un groupe de ressources Azure")

	Lorsque vous y êtes invité, entrez les informations d'identification que vous avez fournies à l'utilisateur du bureau à distance.

4. Dans la session à distance, lancez Windows PowerShell et utilisez les commandes du système de fichiers HDFS pour lister les fichiers dans Azure Data Lake Store.

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	Le fichier que vous avez téléchargé dans Data Lake Store doit y figurer.

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv

	Vous pouvez également utiliser la commande `hdfs dfs -put` pour charger des fichiers dans Data Lake Store, puis utiliser `hdfs dfs -ls` pour vérifier si les fichiers ont été chargés avec succès.

## Voir aussi

* [Portail : Créer un cluster HDInsight pour utiliser Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_0914_2016-->