<properties
	pageTitle="Approvisionnement de clusters HBase sur un réseau virtuel | Microsoft Azure"
	description="Prise en main de HBase dans Azure HDInsight Découvrez comment créer des clusters HDInsight HBase sur Azure Virtual Network."
	keywords=""
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
   ms.date="11/18/2015"
   ms.author="jgao"/>

# Approvisionnement de clusters HBase sur Azure Virtual Network 

Découvrez comment créer des clusters Azure HDInsight sur [Azure Virtual Network][1].

[AZURE.INCLUDE [version préliminaire du portail hdinsight azure](../../includes/hdinsight-azure-preview-portal.md)]

* [Approvisionnement de clusters HBase sur Azure Virtual Network](hdinsight-hbase-provision-vnet-v1.md)

Avec l’intégration du réseau virtuel, les clusters HBase peuvent être déployés sur le même réseau virtuel que vos applications pour permettre à celles-ci de communiquer directement avec HBase. Voici les avantages :

- Connectivité directe de l’application web aux nœuds du cluster HBase, ce qui permet les communications au moyen d’API d’appel de procédure distante (RPC) Java HBase.
- Amélioration des performances en évitant à votre trafic de transiter par plusieurs passerelles et équilibrages de charge.
- Capacité de traitement des informations critiques de façon plus sécurisée sans exposer de points de terminaison publics.

##Configuration requise
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez [Installation et utilisation d'Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell](../install-configure-powershell.md). Pour exécuter des scripts Azure PowerShell, vous devez exécuter Azure PowerShell en tant qu’administrateur et définir la stratégie d’exécution sur *RemoteSigned*. Consultez la rubrique [Utilisation de l'applet de commande Set-ExecutionPolicy][2].

	Avant d’exécuter vos scripts Azure PowerShell, assurez-vous que vous êtes connecté à votre abonnement Azure à l’aide de la cmdlet suivante :

		Add-AzureAccount

	Si vous possédez plusieurs abonnements Azure, utilisez la cmdlet suivante pour définir l'abonnement en cours :

		Select-AzureSubscription <AzureSubscriptionName>


## Approvisionnement d’un cluster HBase dans un réseau virtuel

Les applications sont généralement constituées de nombreux composants. Ce didacticiel présente les éléments suivants :

- un réseau virtuel Azure
- un compte de stockage Azure
- un cluster HDInsight HBase Azure
- (facultatif) une machine virtuelle Azure agissant comme un serveur DNS

Azure Resource Manager vous permet de manipuler les ressources de votre application sous la forme d’un groupe. Vous pouvez déployer, mettre à jour ou supprimer toutes les ressources de votre application dans le cadre d’une opération unique et coordonnée. Vous utilisez un modèle de déploiement pouvant fonctionner avec différents environnements (environnements de test, intermédiaire et de production). Vous pouvez clarifier la facturation pour votre organisation en visualisant les coûts cumulés pour l’ensemble du groupe.

**Pour créer un groupe de ressources**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **NOUVEAU**, cliquez sur **Gestion**, puis sur **Groupe de ressources**.
3. Tapez ou sélectionnez les valeurs suivantes :

	- **Nom du groupe de ressources** : saisissez un nom pour le groupe de ressources.
	- **Abonnement** : sélectionnez l'abonnement Azure utilisé pour ce groupe de ressources.
	- **Emplacement de groupe de ressources** : sélectionnez un centre de données Azure. Cet emplacement ne doit pas obligatoirement correspondre à l'emplacement du cluster HDInsight.

4. Cliquez sur **Create**.

Avant la configuration d’un cluster HBase, vous devez disposer d’un réseau virtuel Azure.

**Pour créer un réseau virtuel au moyen du portail Azure**

1. Connectez-vous au [portail](https://portal.azure.com).
2. Cliquez sur **NOUVEAU**, sur **Réseau**, puis cliquez sur **Réseau virtuel**.
3. Dans **Sélectionner un modèle de déploiement**, sélectionnez **classique** si vous utilisez un cluster HDInsight basé sur Windows, sélectionnez **Gestionnaire de ressources** si vous utilisez un cluster HDInsight basé sur Linux. Enfin, cliquez sur **Créer**.

    > [AZURE.NOTE]Les clusters Windows nécessitent un réseau virtuel v1 (classique), tandis que les clusters basés sur Linux nécessitent un réseau virtuel v2 (Azure Resource Manager). Si le type de réseau est incorrect, il ne sera pas utilisable lorsque vous créerez le cluster.
    >
    > Si vous disposez de ressources sur un réseau virtuel qui n’est pas utilisable par le cluster que vous envisagez de créer, vous pouvez créer un nouveau réseau virtuel utilisable par le cluster et le connecter au réseau virtuel incompatible. Vous pouvez ensuite créer le cluster dans la version réseau dont il a besoin, et il sera en mesure d’accéder aux ressources de l’autre réseau puisque les deux seront reliés. Pour plus d'informations sur la connexion de réseaux classiques et nouveaux, consultez [Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels](../virtual-network/virtual-networks-arm-asm-s2s.md).
    
4. Tapez ou sélectionnez les valeurs suivantes :

	- **Nom** : nom de votre réseau virtuel.
	- **Espace d’adressage** : choisissez un espace d’adressage pour le réseau virtuel qui soit suffisamment grand pour fournir des adresses à tous les nœuds du cluster. Si ce n'est pas le cas, l'approvisionnement échoue. Pour tout au long de ce didacticiel, vous pouvez utiliser les valeurs par défaut. Cliquez sur **OK** pour enregistrer les modifications.
    
        > [AZURE.NOTE]Si vous utilisez ce réseau virtuel avec plusieurs clusters HDInsight, il est fortement recommandé de désigner un sous-réseau unique pour chaque cluster.
         
	- **Groupe de ressources** : sélectionnez le groupe de ressources que vous avez créé précédemment dans le didacticiel.
	- **Abonnement** : sélectionnez l'abonnement Azure que vous voulez utiliser pour ce réseau virtuel.
	- **Emplacement** : l’emplacement doit être le même que celui du cluster HBase que vous allez créer.
    
        > [AZURE.NOTE]> Azure HDInsight prend en charge uniquement les réseaux virtuels basés sur l’emplacement et ne fonctionne pas pour le moment avec des réseaux virtuels basés sur des groupes d’affinités.

5. Cliquez sur **Create**.

Par défaut, le réseau virtuel utilise un serveur de système de nom de domaine (DNS) interne fourni par Azure. Des configurations de réseau plus avancées avec des serveurs DNS personnalisés sont également prises en charge. Pour obtenir des instructions détaillées, consultez la page [Résolution de noms (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

**(Facultatif) Ajout d'une machine virtuelle de serveur DNS sur le réseau virtuel**

Un serveur DNS est facultatif, mais il est nécessaire dans certains cas. La procédure a été documentée dans [Configuration d’un serveur DNS entre deux réseaux virtuels Azure][hdinsight-hbase-replication-dns]. Vous devez effectuer les étapes suivantes :

1. ajouter une machine virtuelle au réseau virtuel
2. configurer une adresse IP statique pour la machine virtuelle
3. ajouter le rôle Serveur DNS à la machine virtuelle
4. Assignation des serveurs DNS au réseau virtuel

**Approvisionnement d’un cluster HBase en utilisant le portail Azure**

> [AZURE.NOTE]Pour plus d’informations sur l’approvisionnement d’un nouveau cluster HBase au moyen d’Azure PowerShell, consultez la rubrique [Approvisionnement d’un cluster HBase au moyen d’Azure PowerShell](#powershell).


**Création d'un cluster HDInsight**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **NOUVEAU**, sur **Analyse des données**, puis sur **HDInsight**.

    ![Création d’un nouveau cluster dans le portail Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Création d’un nouveau cluster dans le portail Azure")

3. Tapez ou sélectionnez les valeurs suivantes :

  - **Nom de cluster** : saisissez un nom pour le cluster. Une coche verte en face du nom de cluster s'affiche si le nom est disponible.
  - **Type de cluster**: sélectionnez **HBase**.
  - **Système d’exploitation de cluster** : sélectionnez **Windows Server 2012 R2 Datacenter**.
  - **Abonnement** : sélectionnez l'abonnement Azure qui sera utilisé pour l'approvisionnement de ce cluster.
  - **Groupe de ressources** : sélectionnez le groupe de ressources que vous avez créé précédemment dans le didacticiel.
  - **Informations d’identification** : configurez le nom d’utilisateur et le mot de passe de l’utilisateur Hadoop (HTTP). Si vous activez le bureau à distance du cluster, vous devrez configurer le nom d'utilisateur et le mot de passe du bureau distant, et une date d'expiration de compte. Cliquez sur **Sélectionner** au bas de l’écran pour enregistrer les modifications.
  - **Source de données** : sélectionnez un compte de stockage Azure existant ou créez un nouveau compte de stockage Azure à utiliser comme système de fichiers par défaut pour le cluster. Le nom du conteneur par défaut correspond au nom du cluster. L'emplacement du compte de stockage détermine également l'emplacement du cluster.
  - **Niveau de tarification du nœud** : à des fins de formation ou d'évaluation, sélectionnez 1 nœud de région pour minimiser le coût.

  	- **Méthode de sélection** : définissez cette propriété sur la valeur **De tous les abonnements** pour permettre l’exploration des comptes de stockage de tous vos abonnements. Affectez-lui la valeur **Clé d’accès** si vous souhaitez entrer le **nom de stockage** et la **clé d’accès** d’un compte de stockage existant.
  	- **Sélectionner le compte de stockage/Créer un compte** : cliquez sur **Sélectionner le compte de stockage** pour rechercher et sélectionner un compte de stockage existant à associer au cluster. Vous pouvez également cliquer sur **Créer un nouveau** pour créer un nouveau compte de stockage. Utilisez le champ qui s’affiche pour saisir le nom du compte de stockage. Une coche verte s’affiche si le nom est disponible.
    - **Choisir un conteneur par défaut** : utilisez cette option pour entrer le nom du conteneur par défaut à utiliser pour le cluster. Vous pouvez saisir n’importe quel nom, mais nous vous conseillons d’utiliser le même nom que le cluster pour pouvoir facilement reconnaître le conteneur utilisé pour ce cluster spécifique.
  	- **Emplacement** : zone géographique où se trouve le compte de stockage ou dans laquelle il sera créé. Cet emplacement détermine l'emplacement du cluster. Le cluster et le compte de stockage par défaut doit se situer dans le même datacenter Azure.

  - **Niveaux de tarification du nœud** : définissez le nombre de nœuds de travail dont vous avez besoin pour le cluster. Le coût estimé du cluster s'affiche dans le panneau.
	- **Configuration facultative** : pour ce didacticiel, vous devez uniquement configurer **réseau virtuel**. Sélectionnez le réseau virtuel que vous avez créé plus tôt dans ce didacticiel. Veillez à sélectionner également un sous-réseau.

4. Cliquez sur **Create**.


Pour commencer à utiliser votre nouveau cluster HBase, vous pouvez utiliser les procédures figurant dans la rubrique [Prise en main de HBase avec Hadoop dans HDInsight](../hdinsight-hbase-get-started.md).

##Connexion au cluster HBase approvisionné dans le réseau virtuel au moyen d’API RPC Java HBase

1.	Approvisionnez une machine virtuelle IaaS dans le même réseau virtuel Azure et le même sous-réseau. Ainsi, la machine virtuelle et le cluster HBase utilisent tous deux le même serveur DNS interne pour résoudre les noms d'hôte. Pour cela, vous devez choisir l’option **À partir de la galerie** et sélectionner le réseau virtuel au lieu d’un centre de données. Pour obtenir des instructions, consultez le didacticiel [Création d'une machine virtuelle exécutant Windows Server](../virtual-machines-windows-tutorial.md). Une image standard de Windows Server 2012 avec une petite taille de machine virtuelle est suffisante.

2.	Lorsque vous utilisez une application Java pour vous connecter à distance à HBase, vous devez utiliser le nom de domaine complet. Pour déterminer cela, vous devez obtenir le suffixe DNS propre à la connexion du cluster HBase. À cette fin, utilisez Curl pour interroger Ambari ou le Bureau à distance pour effectuer une connexion au cluster.

	* **Curl** : utilisez la commande suivante :

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		Dans les données JavaScript Object Notation (JSON) renvoyées, recherchez l’entrée « host\_name ». Elle contient le nom de domaine complet des nœuds du cluster. Par exemple :

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		La partie du nom de domaine commençant par le nom de cluster est le suffixe DNS. Par exemple, mon\_cluster.b1.cloudapp.net.

	* **Azure PowerShell** : utilisez le script Azure PowerShell suivant pour enregistrer la fonction **Get-ClusterDetail**, qui peut être utilisée pour renvoyer le suffixe DNS.

			function Get-ClusterDetail(
			    [String]
			    [Parameter( Position=0, Mandatory=$true )]
			    $ClusterDnsName,
				[String]
			    [Parameter( Position=1, Mandatory=$true )]
			    $Username,
				[String]
			    [Parameter( Position=2, Mandatory=$true )]
			    $Password,
			    [String]
			    [Parameter( Position=3, Mandatory=$true )]
			    $PropertyName
				)
			{
			<#
			    .SYNOPSIS
			     Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
				.Description
				 This command shows the following 4 properties of an HDInsight cluster:
				 1. ZookeeperQuorum (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.quorum".
				 2. ZookeeperClientPort (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.property.clientPort".
				 3. HBaseRestServers (supports only HBase type cluster)
					Shows a list of host FQDNs that run the HBase REST server.
				 4. FQDNSuffix (supports all cluster types)
					Shows the FQDN suffix of hosts in the cluster.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
			     This command shows the value of HBase property "hbase.zookeeper.quorum".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
			     This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
			     This command shows a list of host FQDNs that run the HBase REST server.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
			     This command shows the FQDN suffix of hosts in the cluster.
			#>

				$DnsSuffix = ".azurehdinsight.net"

				$ClusterFQDN = $ClusterDnsName + $DnsSuffix
				$webclient = new-object System.Net.WebClient
				$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

				if($PropertyName -eq "ZookeeperQuorum")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
				}
				if($PropertyName -eq "ZookeeperClientPort")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
				}
				if($PropertyName -eq "HBaseRestServers")
				{
					$Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
					$Response1 = $webclient.DownloadString($Url1)
					$JsonObject1 = $Response1 | ConvertFrom-Json
					$PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

					$Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
					$Response2 = $webclient.DownloadString($Url2)
					$JsonObject2 = $Response2 | ConvertFrom-Json
					foreach ($host_component in $JsonObject2.host_components)
					{
						$ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
						Write-host $ConnectionString
					}
				}
				if($PropertyName -eq "FQDNSuffix")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/components/resourcemanager"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					$FQDN = $JsonObject.host_components[0].HostRoles.host_name
					$pos = $FQDN.IndexOf(".")
					$Suffix = $FQDN.Substring($pos + 1)
					Write-host $Suffix
				}
			}

		Après avoir exécuté le script Azure PowerShell, utilisez la commande suivante pour renvoyer le suffixe DNS au moyen de la fonction **Get-ClusterDetail**. Spécifiez votre nom de cluster HDInsight HBase, ainsi que le nom et le mot de passe de l'administrateur lors de l'utilisation de cette commande.

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		Ceci renvoie le suffixe DNS. Par exemple, **votre\_nom\_cluster.b4.internal.cloudapp.net**.

	> [AZURE.NOTE]Vous pouvez également utiliser le Bureau à distance pour vous connecter au cluster HBase (vous serez connecté au nœud principal) et exécuter **ipconfig** à partir d’une invite de commandes pour obtenir le suffixe DNS. Pour des instructions sur l’activation du protocole RDP (Remote Desktop Protocol) et la façon de se connecter au moyen de ce dernier, consultez la page [Gestion des clusters Hadoop dans HDInsight au moyen du portail de gestion Azure][hdinsight-admin-portal].
	>
	> ![hdinsight.hbase.dns.suffix][img-dns-surffix]


<!--
3.	Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

	To make the configuration change:

	1. RDP into the virtual machine.
	2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
	3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
	- Set **Primary DNS Suffix** to the value obtained in step 2:

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. Click **OK**.
	5. Reboot the virtual machine.
-->

Pour vérifier que la machine virtuelle peut communiquer avec le cluster HBase, utilisez la commande `ping headnode0.<dns suffix>` à partir de la machine virtuelle. Par exemple, ping headnode0.mon\_cluster.b1.cloudapp.net.

Pour utiliser ces informations dans une application Java, vous pouvez suivre la procédure décrite dans la rubrique [Utilisation de Maven pour créer des applications Java utilisant HBase avec HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md) pour créer une application. Pour que l’application soit connectée à un serveur HBase distant, modifiez le fichier **hbase-site.xml** dans cet exemple afin d’utiliser le nom de domaine complet pour Zookeeper. Par exemple :

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE]Pour plus d’informations sur la résolution de noms sur des réseaux virtuels Azure, y compris la façon d’utiliser votre propre serveur DNS, consultez la rubrique [Résolution de noms (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

##Approvisionnement d’un cluster HBase au moyen d’Azure PowerShell

**Approvisionnement d’un cluster HBase au moyen d’Azure PowerShell**

1. Ouvrez l’environnement de script intégré (ISE) Azure PowerShell :
2. Copiez et collez ce qui suit dans le volet de script :

		$hbaseClusterName = "<HBaseClusterName>"
		$hadoopUserName = "<HBaseClusterUsername>"
		$hadoopUserPassword = "<HBaseClusterUserPassword>"
		$location = "<HBaseClusterLocation>"  #i.e. "West US"
		$clusterSize = <HBaseClusterSize>  
		$resourceGroup = "<AzureResourceGroupName>"
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"

		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password)

		New-AzureHDInsightCluster -ResourceGroupName $resourceGroup `
		                          -ClusterName $hbaseClusterName `
				                    	-ClusterType HBase `
				                    	-Location $location `
				                    	-ClusterSizeInNodes $clusterSize `
		                          -HttpCredential $creds `
				                    	-VirtualNetworkId $vnetID `
				                    	-SubnetName $subNetName `
				                    	-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
				                    	-DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainer $storageContainerName


3. Cliquez sur **Exécuter le script** ou appuyez sur **F5**.
4. Pour valider le cluster, vous pouvez vérifier le cluster à partir du portail Azure ou exécuter la cmdlet Azure PowerShell suivante à partir du volet inférieur :

	Get-AzureHDInsightCluster

##Étapes suivantes

Dans ce didacticiel, vous avez appris à approvisionner un cluster HBase. Pour plus d'informations, consultez les rubriques suivantes :

- [Prise en main de HDInsight](../hdinsight-get-started.md)
- [Configuration de la géo-réplication HBase dans HDInsigtht](hdinsight-hbase-geo-replication.md)
- [Approvisionnement de clusters dans HDInsight](hdinsight-provision-clusters.md)
- [Prise en main de HBase avec Hadoop dans HDInsight](../hdinsight-hbase-get-started.md)
- [Analyse de sentiments Twitter avec HBase dans HDInsight](../hdinsight-hbase-twitter-sentiment.md)
- [Présentation du réseau virtuel.][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: ../hdinsight-hbase-get-started.md
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines-windows-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: ../install-configure-powershell.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Détails de configuration pour le nouveau cluster HBase"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Utilisation de l’action de script pour personnaliser un cluster HBase"

[azure-preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_1203_2015-->