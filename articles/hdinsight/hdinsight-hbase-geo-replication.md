<properties 
   pageTitle="Configurer la réplication HBase entre deux centres de données | Microsoft Azure" 
   description="Découvrez comment configurer la réplication HBase entre deux centres de données, ainsi que des cas d’utilisation de la réplication en cluster." 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/02/2015"
   ms.author="jgao"/>

# Configurer la géo-réplication HBase dans HDInsigtht

> [AZURE.SELECTOR]
- [Configure VPN connectivity](../hdinsight-hbase-geo-replication-configure-VNETs.md)
- [Configure DNS](hdinsight-hbase-geo-replication-configure-DNS.md)
- [Configure HBase replication](hdinsight-hbase-geo-replication.md) 
 
Découvrez comment configurer la réplication HBase entre deux centres de données. Voici quelques cas d'utilisation pour la réplication en cluster :

- Sauvegarde et récupération d'urgence
- Agrégation de données
- Distribution géographique de données
- Réception de données en ligne associée à l'analyse des données hors connexion

La réplication en cluster utilise une méthodologie par émission de données source. Un cluster HBase peut être une source, une destination ou jouer les deux rôles à la fois. La réplication est asynchrone et l'objectif de la réplication est une cohérence éventuelle. Quand la source reçoit une modification apportée à une famille de colonnes dont la réplication est activée, cette modification est propagée à tous les clusters de destination. Quand les données sont répliquées d'un cluster à un autre, le cluster source et tous les clusters qui ont déjà utilisé les données font l'objet d'un suivi afin d'empêcher les boucles de réplication. Pour plus d'informations, vous allez configurer dans ce didacticiel une réplication source-destination. Pour d'autres topologies de cluster, consultez le [Guide de référence d'Apache HBase](http://hbase.apache.org/book.html#_cluster_replication).

Il s'agit de la troisième partie de la série :

- [Configurer une connexion VPN entre deux réseaux virtuels][hdinsight-hbase-replication-vnet]
- [Configurer des serveurs DNS pour les réseaux virtuels][hdinsight-hbase-replication-dns]
- Configurer la géo-réplication HBase (ce didacticiel)

Le diagramme suivant illustre les deux réseaux virtuels et la connexion réseau que vous avez créés dans les pages [Configurer une connexion VPN entre deux réseaux virtuels][hdinsight-hbase-geo-replication-vnet] et [Configurer des serveurs DNS pour les réseaux virtuels][hdinsight-hbase-replication-dns] :

![Diagramme du réseau virtuel de la réplication HDInsight HBase][img-vnet-diagram]

## <a id="prerequisites"></a>Configuration requise

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez la page [Installation et utilisation d’Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Pour exécuter des scripts PowerShell, vous devez exécuter Azure PowerShell en tant qu'administrateur et définir la stratégie d'exécution sur *RemoteSigned*. Consultez la rubrique Utilisation de la cmdlet Set-ExecutionPolicy.

- **Deux réseaux virtuels Azure avec une connexion VPN et des serveurs DNS configurés**. Pour des instructions, consultez [Configurer une connexion VPN entre deux réseaux virtuels Azure][hdinsight-hbase-replication-vnet] et [Configurer des serveurs DNS entre deux réseaux virtuels Azure][hdinsight-hbase-replication-dns].


	Avant d'exécuter vos scripts PowerShell, assurez-vous que vous êtes connecté à votre abonnement Azure à l'aide de la cmdlet suivante :

		Add-AzureAccount

	Si vous possédez plusieurs abonnements Azure, utilisez la cmdlet suivante pour définir l'abonnement en cours :

		Select-AzureSubscription <AzureSubscriptionName>



## Approvisionnement de clusters HBase dans HDInsight

Dans le didacticiel [Configurer une connexion VPN entre deux réseaux virtuels Azure][hdinsight-hbase-replication-vnet], vous avez créé un réseau virtuel dans un centre de données en Europe et un réseau virtuel dans un centre de données aux États-Unis. Les deux réseaux virtuels sont connectés via VPN. Dans cette session, vous allez approvisionner un cluster HBase dans chaque réseau virtuel. Plus loin dans ce didacticiel, vous configurerez l'un des clusters HBase pour qu'il réplique l'autre cluster HBase.

Le portail Azure Classic ne prend pas en charge l’approvisionnement de clusters HDInsight avec une configuration personnalisée. Par exemple, définissez *hbase.replication* sur *true*. Si vous définissez la valeur dans le fichier de configuration après l'approvisionnement d'un cluster, vous perdrez le paramètre une fois le cluster réinitialisé. Pour plus d'informations, consultez [Approvisionnement de clusters Hadoop dans HDInsight][hdinsight-provision]. L'une des options permettant d'approvisionner un cluster HDInsight avec des options personnalisées consiste à utiliser Azure PowerShell.


**Pour approvisionner un cluster HBase dans Contoso-VNet-EU**

1. Dans votre station de travail, ouvrez Windows PowerShell ISE.
2. Définissez les variables au début du script, puis exécutez-le.

		# create hbase cluster with replication enabled
		
		$azureSubscriptionName = "[AzureSubscriptionName]"
		
		$hbaseClusterName = "Contoso-HBase-EU" # This is the HBase cluster name to be used.
		$hbaseClusterSize = 1   # You must provision a cluster that contains at least 3 nodes for high availability of the HBase services.
		$hadoopUserLogin = "[HadoopUserName]"
		$hadoopUserpw = "[HadoopUserPassword]"
		
		$vNetName = "Contoso-VNet-EU"  # This name must match your Europe virtual network name.
		$subNetName = 'Subnet-1' # This name must match your Europe virtual network subnet name.  The default name is "Subnet-1".
		
		$storageAccountName = 'ContosoStoreEU'  # The script will create this storage account for you.  The storage account name doesn't support hyphens. 
		$storageAccountName = $storageAccountName.ToLower() #Storage account name must be in lower case.
		$blobContainerName = $hbaseClusterName.ToLower()  #Use the cluster name as the default container name.
		
		#connect to your Azure subscription
		Add-AzureAccount 
		Select-AzureSubscription $azureSubscriptionName
		
		# Create a storage account used by the HBase cluster
		$location = Get-AzureVNetSite -VNetName $vNetName | %{$_.Location} # use the virtual network location
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location
		
		# Create a blob container used by the HBase cluster
		$storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{$_.Primary}
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $blobContainerName -Context $storageContext
		
		# Create provision configuration object
		$hbaseConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHBaseConfiguration'
		    
		$hbaseConfigValues.Configuration = @{ "hbase.replication"="true" } #this modifies the hbase-site.xml file
		
		# retrieve vnet id based on vnetname
		$vNetID = Get-AzureVNetSite -VNetName $vNetName | %{$_.id}
		
		$config = New-AzureHDInsightClusterConfig `
		                -ClusterSizeInNodes $hbaseClusterSize `
		                -ClusterType HBase `
		                -VirtualNetworkId $vNetID `
		                -SubnetName $subNetName `
		            | Set-AzureHDInsightDefaultStorage `
		                -StorageAccountName $storageAccountName `
		                -StorageAccountKey $storageAccountKey `
		                -StorageContainerName $blobContainerName `
		            | Add-AzureHDInsightConfigValues `
		                -HBase $hbaseConfigValues
		
		# provision HDInsight cluster
		$hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
		$credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
		
		$config | New-AzureHDInsightCluster -Name $hbaseClusterName -Location $location -Credential $credential



**Pour approvisionner un cluster HBase dans Contoso-VNet-US**

- Utiliser le même script avec les valeurs suivantes :

		$hbaseClusterName = "Contoso-HBase-US" # This is the HBase cluster name to be used.
		$vNetName = "Contoso-VNet-US"  # This name must match your Europe virtual network name.
		$storageAccountName = 'ContosoStoreUS'	

	Étant donné que vous êtes déjà connecté à votre compte Azure, vous n'avez plus besoin d'exécuter les applets de commande suivantes :

		Add-AzureAccount 
		Select-AzureSubscription $azureSubscriptionName




## Configurer un redirecteur DNS conditionnel

Dans le didacticiel [Configurer des serveurs DNS pour les réseaux virtuels][hdinsight-hbase-replication-dns], vous avez configuré des serveurs DNS pour les deux réseaux. Les clusters HBase ont des suffixes de domaine différents. Vous devez donc configurer des redirecteurs DNS conditionnels supplémentaires.

Pour configurer un redirecteur conditionnel, vous devez connaître les suffixes de domaine des deux clusters HBase.

**Pour rechercher les suffixes de domaine des deux clusters HBase**

1. Ouvrez une session RDP sur **Contoso-HBase-EU**. Pour obtenir des instructions, consultez [Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure Classic][hdinsight-manage-portal]. Il s'agit en fait du headnode0 du cluster.
2. Ouvrez la console Windows PowerShell ou une invite de commandes.
3. Exécutez **ipconfig** et notez le **suffixe DNS propre à la connexion**.
4. Ne fermez pas la session RDP. Vous en aurez besoin plus tard pour tester la résolution de noms de domaine.
5. Répétez ces étapes pour déterminer le **suffixe DNS propre à la connexion** de **Contoso-HBase-US**.


**Pour configurer des redirecteurs DNS**
 
1.	Ouvrez une session RDP sur **Contoso-DNS-EU**. 
2.	Cliquez sur la touche Windows en bas à gauche.
2.	Cliquez sur **Outils d'administration**.
3.	Cliquez sur **DNS**.
4.	Dans le volet gauche, développez **DSN**, **Contoso-DNS-EU**.
5.	Cliquez avec le bouton droit sur **Redirecteurs conditionnels**, puis cliquez sur **Nouveau redirecteur conditionnel**. 
5.	Entrez les informations suivantes :
	- **Domaine DNS** : entrez le suffixe DNS de Contoso-HBase-US. Par exemple : Contoso-HBase-US.f5.internal.cloudapp.net.
	- **Adresses IP des serveurs maîtres** : entrez 10.2.0.4, qui est l'adresse IP de Contoso-DNS-US. Vérifiez l'adresse IP. Votre serveur DNS peut avoir une adresse IP différente.
6.	Appuyez sur **Entrée**, puis cliquez sur **OK**. Maintenant, vous pouvez résoudre l'adresse IP de Contoso-DNS-US à partir de Contoso-DNS-EU.
7.	Répétez les étapes pour ajouter un redirecteur DNS conditionnel au service DNS sur la machine virtuelle de Contoso-DNS-US avec les valeurs suivantes :
	- **Domaine DNS** : entrez le suffixe DNS de Contoso-HBase-EU. 
	- **Adresses IP des serveurs maîtres** : entrez 10.2.0.4, qui est l'adresse IP de Contoso-DNS-EU.

**Pour tester la résolution de noms de domaine**

1. Basculez sur la fenêtre de session RDP de Contoso-HBase-EU.
2. Ouvrez une invite de commandes.
3. Exécutez la commande ping :

		ping headnode0.[DNS suffix of Contoso-HBase-US]

	Le protocole ICM est activé sur les nœuds de traitement des clusters HBase

4. Ne fermez pas la session RDP. Vous en aurez encore besoin plus loin dans le didacticiel.
5. Répétez les mêmes étapes pour tester le headnode0 de Contoso-HBase-EU à partir de Contoso-HBase-US.

>[AZURE.IMPORTANT] Le serveur DNS doit fonctionner avant de procéder aux étapes suivantes.

## Activer la réplication entre les tables HBase

À présent, vous pouvez créer un exemple de table HBase, activer la réplication, puis la tester avec des données. L'exemple de table que vous allez utiliser contient deux familles de colonnes : Personal et Office.

Dans ce didacticiel, le cluster HBase d'Europe est le cluster source, et le cluster HBase des États-Unis est le cluster de destination.

Créez des tables HBase avec les mêmes noms et familles de colonnes sur les clusters source et de destination, de sorte que le cluster de destination sache où stocker les données qu'il reçoit. Pour plus d'informations sur l'utilisation du shell HBase, consultez [Prise en main d'Apache HBase dans HDInsight][hdinsight-hbase-get-started].

**Pour créer une table HBase sur Contoso-HBase-EU**

1. Basculez sur la fenêtre de session RDP de **Contoso-HBase-EU**.
2. Depuis le Bureau, cliquez sur **Ligne de commande Hadoop**.
2. Définissez le dossier sur le répertoire d'accueil HBase :

		cd %HBASE_HOME%\bin
3. Ouvrez le shell HBase :

		hbase shell
4. Créez une table HBase :

		create 'Contacts', 'Personal', 'Office'
5. Ne fermez pas la session RDP ni la fenêtre de ligne de commande Hadoop. Vous en aurez besoin plus loin dans le didacticiel.
	
**Pour créer une table HBase sur Contoso-HBase-US**

- Répétez les même étapes pour créer la même table sur Contoso-HBase-US.


**Pour ajouter Contoso-HBase-US comme homologue de réplication**

1. Basculez sur la fenêtre de session RDP de **Contoso-HBase-EU**.
2. Dans la fenêtre du shell HBase, ajoutez le cluster de destination (Contoso-HBase-US) en tant qu'homologue, par exemple :

		add_peer '1', 'zookeeper0.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper1.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper2.contoso-hbase-us.d4.internal.cloudapp.net:2181:/hbase'

	Dans l'exemple, le suffixe de domaine est *contoso-hbase-us.d4.internal.cloudapp.net*. Vous devez le mettre à jour pour qu'il corresponde au suffixe de domaine de votre cluster HBase aux États-Unis. Assurez-vous qu'il n'y a aucun espace entre les noms d'hôtes.

**Pour configurer chaque famille de colonnes afin d'être répliquée sur le cluster source**

1. Dans la fenêtre du shell HBase de la session RDP sur **Contso-HBase-EU**, configurez chaque famille de colonnes à répliquer :

		disable 'Contacts'
		alter 'Contacts', {NAME => 'Personal', REPLICATION_SCOPE => '1'}
		alter 'Contacts', {NAME => 'Office', REPLICATION_SCOPE => '1'}
		enable 'Contacts'

**Pour télécharger en bloc des données vers la table HBase**

Un exemple de fichier de données a été téléchargé dans un conteneur d'objets blob Azure public avec l'URL suivante :

		wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

Contenu du fichier :

		8396	Calvin Raji	230-555-0191	5415 San Gabriel Dr.
		16600	Karen Wu	646-555-0113	9265 La Paz
		4324	Karl Xie	508-555-0163	4912 La Vuelta
		16891	Jonathan Jackson	674-555-0110	40 Ellis St.
		3273	Miguel Miller	397-555-0155	6696 Anchor Drive
		3588	Osarumwense Agbonile	592-555-0152	1873 Lion Circle
		10272	Julia Lee	870-555-0110	3148 Rose Street
		4868	Jose Hayes	599-555-0171	793 Crawford Street
		4761	Caleb Alexander	670-555-0141	4775 Kentucky Dr.
		16443	Terry Chander	998-555-0171	771 Northridge Drive

Vous pouvez télécharger le même fichier de données dans votre cluster HBase et importer les données à partir de là.

1. Basculez sur la fenêtre de session RDP de **Contoso-HBase-EU**.
2. Depuis le Bureau, cliquez sur **Ligne de commande Hadoop**.
3. Définissez le dossier sur le répertoire d'accueil HBase :

		cd %HBASE_HOME%\bin

4. Téléchargez les données :

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts


## Vérifier que la réplication de données est en cours

Vous pouvez vérifier que la réplication est en cours en analysant les tables des deux clusters avec les commandes du shell HBase suivantes :

		Scan 'Contacts'


## Étapes suivantes

Dans ce didacticiel, vous avez vu comment configurer la réplication de HBase entre deux centres de données. Pour en savoir plus sur HDInsight et HBase, voir :

- [Page du service HDInsight](https://azure.microsoft.com/services/hdinsight/)
- [Documentation HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
- [Prise en main d'Apache HBase dans HDInsight][hdinsight-hbase-get-started]
- [Vue d'ensemble de HDInsight HBase][hdinsight-hbase-overview]
- [Approvisionnement de clusters HBase sur Azure Virtual Network][hdinsight-hbase-provision-vnet]
- [Analyse de sentiments Twitter en temps réel avec HBase][hdinsight-hbase-twitter-sentiment]
- [Analyse des données de capteur avec Storm et HBase dans HDInsight (Hadoop)][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: ../install-configure-powershell.md
[hdinsight-hbase-get-started]: ../hdinsight-hbase-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-get-started]: ../hdinsight-hbase-get-started.md

<!---HONumber=AcomDC_0128_2016-->