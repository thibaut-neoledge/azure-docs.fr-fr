<properties 
	pageTitle="Approvisionnement de clusters HBase sur Azure Virtual Network | Azure" 
	description="Découvrez comment créer des clusters HDInsight sur Azure Virtual Network." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="jgao"/>

# Approvisionnement de clusters HBase sur Azure Virtual Network

Découvrez comment créer des clusters HDInsight sur [Azure Virtual Network][1]. 

Avec l'intégration du réseau virtuel, les clusters HBase peuvent être déployés sur le même réseau virtuel que vos applications pour permettre à celles-ci de communiquer directement avec HBase. Voici les avantages :

- Connectivité directe de l'application web aux nœuds du cluster HBase, ce qui permet les communications au moyen d'API RPC Java HBase.
- Amélioration des performances en évitant à votre trafic de transiter par plusieurs passerelles et équilibrages de charge. 
- Traitement des informations critiques de façon plus sécurisée sans exposer de points de terminaison publics.


##Dans cet article

- [Conditions préalables](#prerequisites)
- [Approvisionnement de clusters HBase dans un réseau virtuel](#hbaseprovision)
- [Connexion au cluster HBase approvisionné dans le réseau virtuel au moyen d'API RPC Java HBase](#connect)
- [Approvisionnement d'un cluster HBase au moyen de PowerShell](#powershell)
- [Étapes suivantes](#nextsteps)

##<a id="prerequisites"></a>Conditions préalables
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Azure est une plateforme disponible par abonnement. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les pages traitant des  [formules d'abonnement][azure-purchase-options], des [offres spéciales membres][azure-member-offers] ou de la [version d'évaluation gratuite][azure-free-trial].

- **Un poste de travail sur lequel Azure PowerShell est installé et configuré**. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][powershell-install]. Pour exécuter des scripts PowerShell, vous devez exécuter Azure PowerShell en tant qu'administrateur et définir la stratégie d'exécution sur *RemoteSigned*. Consultez la rubrique [Utilisation de la cmdlet Set-ExecutionPolicy][2].

	Avant d'exécuter vos scripts PowerShell, assurez-vous que vous êtes connecté à votre abonnement Azure à l'aide de la cmdlet suivante :

		Add-AzureAccount

	Si vous possédez plusieurs abonnements Azure, utilisez la cmdlet suivante pour définir l'abonnement en cours :

		Select-AzureSubscription <AzureSubscriptionName>


##<a id="hbaseprovision"></a>Approvisionnement d'un cluster HBase dans un réseau virtuel 

**Création d'un réseau virtuel au moyen du portail de gestion** :

1. Connectez-vous au [Portail de gestion Azure][azure-portal].
2. Cliquez sur **NOUVEAU** dans le coin inférieur gauche, puis sur **SERVICES RÉSEAU**, **RÉSEAU VIRTUEL** et **CRÉATION RAPIDE**.
3. Tapez ou sélectionnez les valeurs suivantes :

	- **Nom** : nom de votre réseau virtuel.
	- **Espace d'adressage** : choisissez un espace d'adressage pour le réseau virtuel qui est suffisant grand pour fournir des adresses pour tous les nœuds du cluster. Si ce n'est pas le cas, l'approvisionnement échoue. Pour parcourir ce didacticiel, vous pouvez choisir l'une des trois options suivantes. 
	- **Nombre maximal de machines virtuelles** : choisissez l'un des nombres maximum de machines virtuelles. Cette valeur détermine le nombre d'hôtes (machines virtuelles) qu'il est possible de créer sous l'espace d'adressage. Pour parcourir ce didacticiel, **4096 [CIDR: /20]** s'avère suffisant. 
	- **Emplacement** : l'emplacement doit être le même que celui du cluster HBase que vous allez créer.
	- **Serveur DNS** : étant donné que cet article utilise le serveur DNS interne fourni par Azure, vous pouvez choisir **Aucun**. Des procédures de configuration de réseau plus avancées avec des serveurs DNS personnalisés sont également prises en charge. Pour obtenir des instructions détaillées, consultez la page [Résolution de noms (DNS)](http://msdn.microsoft.com/library/azure/jj156088.aspx).
4. Cliquez sur **CRÉER UN RÉSEAU VIRTUEL**. Le nom du nouveau réseau virtuel apparaît dans la liste. Attendez que la valeur **Créé** apparaisse dans la colonne État.
5. Dans le volet principal, cliquez sur le réseau virtuel que vous venez de créer.
6. Cliquez sur **TABLEAU DE BORD** en haut de la page.
7. Sous **aperçu rapide**, prenez note de l'**ID RÉSEAU VIRTUEL**. Vous en aurez besoin lors de l'approvisionnement du cluster HBase.
8. Cliquez sur **CONFIGURER** en haut de la page.
9. En bas de la page, le nom du sous-réseau par défaut est **Sous-réseau-1**. Facultativement, vous pouvez renommer le sous-réseau ou ajouter un nouveau sous-réseau pour le cluster HBase. Prenez note du nom du sous-réseau, car vous en aurez besoin lors de l'approvisionnement du cluster.
10. Vérifiez **CIDR (NOMBRE D'ADRESSES)** pour le sous-réseau qui sera utilisé pour le cluster. Le nombre d'adresses doit être supérieur au nombre de nœuds de travail plus 7 (Gateway : 2, Headnode : 2, Zookeeper : 3). Par exemple, si vous avez besoin d'un cluster HBase à 10 nœuds, le nombre d'adresses pour le sous-réseau doit être supérieur à 17 (10+7). Si ce n'est pas le cas, le déploiement échoue.

	> [AZURE.NOTE] Il est vivement recommandé de désigner un seul sous-réseau pour un cluster. 

11. Cliquez sur **Enregistrer** en bas de la page, si vous avez mis à jour les valeurs de sous-réseau.



> [AZURE.NOTE] Les clusters HDInsight utilisent le stockage d'objets blob Azure pour stocker des données. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec Hadoop dans HDInsight][hdinsight-storage]. Vous aurez besoin d'un compte de stockage et d'un conteneur de stockage d'objets blob. Le compte de stockage doit être situé au même emplacement que le réseau virtuel et le cluster.

**Création d'un compte de stockage Azure et d'un conteneur de stockage d'objets blob** :

1. Connectez-vous au [Portail de gestion Azure][azure-portal].
2. Cliquez sur **NOUVEAU** dans le coin inférieur gauche, pointez sur **SERVICES DE DONNÉES**, sur **STOCKAGE**, puis cliquez sur **CRÉATION RAPIDE**.

3. Tapez ou sélectionnez les valeurs suivantes :

	- **URL** : nom du compte de stockage.
	- **EMPLACEMENT** : emplacement du compte de stockage. Vérifiez qu'il correspond à l'emplacement du réseau virtuel. Les groupes d'affinités ne sont pas pris en charge.
	- **RÉPLICATION** : à des fins de test, utilisez **Redondant en local** pour réduire le coût.

4. Cliquez sur **CRÉER UN COMPTE DE STOCKAGE**.  Le nouveau compte de stockage figure dès lors dans la liste de stockage. 
5. Attendez que l'**ÉTAT** du nouveau compte de stockage passe sur **En ligne**.
6. Cliquez sur le nouveau compte de stockage dans la liste pour le sélectionner.
7. Cliquez sur **GÉRER LES CLÉS D'ACCÈS** au bas de la page.
8. Notez le **NOM DU COMPTE DE STOCKAGE** et la **CLÉ D'ACCÈS PRIMAIRE** (ou la **CLÉ D'ACCÈS SECONDAIRE**.  Les deux clés fonctionnent).  Vous en aurez besoin plus loin dans le didacticiel.
9. En haut de la page, cliquez sur **CONTENEUR**.
10. En bas de la page, cliquez sur **AJOUTER**.
11. Entrez le nom du conteneur.  Ce conteneur sera utilisé comme conteneur par défaut pour le cluster HBase. Par défaut, le nom du conteneur par défaut correspond au nom du cluster. Pour le champ **ACCÈS**, conservez la valeur **Privé**.  
12. Cliquez sur l'icône en forme de coche pour créer le conteneur.

**Approvisionnement d'un cluster HBase en utilisant le portail Azure** :

> [AZURE.NOTE] Pour plus d'informations sur l'approvisionnement d'un nouveau cluster HBase au moyen de PowerShell, consultez la rubrique [Approvisionnement d'un cluster HBase au moyen de PowerShell](#powershell).

1. Connectez-vous au [Portail de gestion Azure][azure-portal].

2. Cliquez sur **NOUVEAU** dans le coin inférieur gauche, pointez sur **SERVICES DE DONNÉES**, sur **HDINSIGHT**, puis cliquez sur **CRÉATION PERSONNALISÉE**.

3. Entrez un NOM DE CLUSTER, sélectionnez le TYPE DE CLUSTER HBase, puis sélectionnez la version de HDINSIGHT.

	![Provide details for the HBase cluster][img-provision-cluster-page1]

	Cliquez avec le bouton droit.

4. Entrez ou sélectionnez les valeurs suivantes :

	- **NŒUDS DE DONNÉES** : entrez le nombre de nœuds de données pour le cluster HBase. 
	- **RÉGION/RÉSEAU VIRTUEL** : sélectionnez le réseau virtuel Azure que vous avez créé.
	- **SOUS-RÉSEAUX DU RÉSEAU VIRTUEL** : sélectionnez un sous-réseau. Le nom par défaut est **Sous-réseau-1**.

	Cliquez avec le bouton droit.

5. Entrez le **NOM D'UTILISATEUR** et le **MOT DE PASSE** Hadoop à utiliser pour ce cluster, puis cliquez avec le bouton droit.
6. Indiquez si vous souhaitez utiliser un nouveau compte de stockage ou un compte existant comme compte de stockage par défaut pour le cluster, puis cliquez avec le bouton droit de la souris.

7. Sur la page **Actions de script**, cliquez sur **ajouter une action de script** pour fournir des informations sur le script personnalisé que vous souhaitez exécuter pour personnaliser un cluster, à mesure qu'il est créé. Vous pouvez, par exemple, utiliser une action de script pour personnaliser un cluster afin d'installer <a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a>. Pour plus d'informations, consultez la rubrique [Personnalisation de clusters HDInsight à l'aide d'une action de script][hdinsight-customize-cluster]. 
	
	![Configure Script Action to customize an HDInsight HBase cluster][img-provision-cluster-page5]

	<table border='1'>
		<tr><th>Propriété</th><th>Valeur</th></tr>
		<tr><td>Nom</td>
			<td>Indiquez un nom pour l'action de script.</td></tr>
		<tr><td>URI de script</td>
			<td>Indiquez l'URI vers le script qui est appelé pour personnaliser le cluster.</td></tr>
		<tr><td>Type de nœud</td>
			<td>Spécifie les nœuds sur lesquels le script de personnalisation est exécuté. Vous avez le choix entre <b>Tous les nœuds</b>, <b>Nœuds principaux uniquement</b> ou <b>Nœuds de travail</b> uniquement.
		<tr><td>Paramètres</td>
			<td>Indiquez les paramètres, s'ils sont requis par le script.</td></tr>
	</table>

	Vous pouvez ajouter plusieurs actions de script afin d'installer plusieurs composants sur le cluster. Après avoir ajouté les scripts, cliquez sur la coche pour démarrer l'approvisionnement du cluster.


Pour commencer à utiliser votre nouveau cluster HBase, vous pouvez utiliser les procédures figurant dans la rubrique [Prise en main de HBase avec Hadoop dans HDInsight][hbase-get-started].

##<a id="connect"></a>Connexion au cluster HBase approvisionné dans le réseau virtuel au moyen d'API RPC Java HBase

1.	Approvisionnez une machine virtuelle IaaS dans le même réseau virtuel Azure et le même sous-réseau. Ainsi, la machine virtuelle et le cluster HBase utilisent tous deux le même serveur DNS interne pour résoudre les noms d'hôte. Pour cela, vous devez choisir l'option À partir de la galerie et sélectionner le réseau virtuel au lieu d'un centre de données. Pour obtenir des instructions, consultez la rubrique [Création d'une machine virtuelle exécutant Windows Server][vm-create]. Une image standard de Windows Server 2012 avec une petite taille de machine virtuelle est suffisante.
	
2.	Lorsque vous utilisez une application Java pour vous connecter à distance à HBase, vous devez utiliser le nom de domaine complet. Pour déterminer cela, nous devons obtenir le suffixe DNS propre à la connexion du cluster HBase. À cette fin, utilisez Curl pour interroger Ambari ou le Bureau à distance pour effectuer une connexion au cluster.

	* **Curl** - Utilisez la commande suivante :

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		Dans les données JSON renvoyées, recherchez l'entrée " host_name ". Elle contient le nom de domaine complet des nœuds du cluster. Par exemple :

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		La partie du nom de domaine commençant par le nom de cluster est le suffixe DNS. Par exemple, mon_cluster.b1.cloudapp.net.

	* **PowerShell** - Utilisez le script PowerShell suivant pour enregistrer la fonction **Get-ClusterDetail**, qui peut être utilisée pour renvoyer le suffixe DNS.

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
			     Displays information to facilitate HDInsight cluster to cluster scinario within same virtual network.
				.Description
				 This command shows following 4 properties of an HDInsight cluster.
				 1. ZookeeperQuorum (only support HBase type cluster)
					Shows the value of hbase property "hbase.zookeeper.quorum".
				 2. ZookeeperClientPort (only support HBase type cluster)
					Shows the value of hbase property "hbase.zookeeper.property.clientPort".
				 3. HBaseRestServers (only support HBase type cluster)
					Shows a list of host FQDNs which run HBase rest server.
				 4. FQDNSuffix (support all type cluster)
					Shows FQDN suffix of hosts in the cluster.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
			     This command shows the value of hbase property "hbase.zookeeper.quorum".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
			     This command shows the value of hbase property "hbase.zookeeper.property.clientPort".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
			     This command shows a list of host FQDNs which run HBase rest server.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
			     This command shows FQDN suffix of hosts in the cluster.
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

		Après avoir exécuté le script PowerShell, utilisez la commande suivante pour renvoyer le suffixe DNS au moyen de la fonction Get-ClusterDetail. Spécifiez votre nom de cluster HDInsight HBase, ainsi que le nom et le mot de passe de l'administrateur lors de l'utilisation de cette commande.

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		Ceci renvoie le suffixe DNS. Par exemple : **yourclustername.b4.internal.cloudapp.net**.

	> [AZURE.NOTE] Vous pouvez également utiliser le Bureau à distance pour vous connecter au cluster HBase (vous serez connecté au nœud principal) et exécuter **ipconfig** à partir d'une invite de commandes pour obtenir le suffixe DNS. Pour plus d'informations sur l'activation du protocole RDP et la façon de se connecter au moyen de ce dernier, consultez la page [Gestion des clusters Hadoop dans HDInsight au moyen du portail de gestion Azure][hdinsight-admin-portal].
	>
	> ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!-- 
3.	Modifiez la configuration Suffixe DNS principal de la machine virtuelle. Cela permet à la machine virtuelle de résoudre automatiquement le nom d'hôte du cluster HBase sans spécifier de façon explicite le suffixe. Par exemple, le nom d'hôte  *workernode0* est résolu correctement sur le nœud de travail workernode0 du cluster HBase. 

	Pour apporter des modifications à la configuration :

	1. Ouvrez une connexion RDP à la machine virtuelle. 
	2. Ouvrez **Éditeur de stratégie de groupe locale**. L'exécutable est gpedit.msc.
	3. Développez **Configuration ordinateur**, **Modèles d'administration**, **Réseau**, puis cliquez sur **Client DNS**. 
	- Définissez **Suffixe DNS principal** sur la valeur obtenue à l'étape 2 : 

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. Cliquez sur **OK**. 
	5. Redémarrez la machine virtuelle.
-->

Pour vérifier que la machine virtuelle peut communiquer avec le cluster HBase, utilisez la commande suivante  `ping headnode0.<dns suffix>` à partir de la machine virtuelle. Par exemple, ping headnode0.mon_cluster.b1.cloudapp.net

Pour utiliser ces informations dans une application Java, vous pouvez suivre la procédure décrite dans la rubrique [Utilisation de Maven pour créer des applications Java utilisant HBase avec HDInsight (Hadoop)](azure.microsoft.com/fr-fr/documentation/articles/hdinsight-hbase-build-java-maven/) pour créer une application. Pour que l'application soit connectée à un serveur HBase distant, modifiez le fichier **hbase-site.xml** de cet exemple afin d'utiliser le nom de domaine complet pour ZooKeeper. Par exemple :

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE] Pour plus d'informations sur la résolution de noms sur des réseaux Azure Virtual Network, y compris la façon d'utiliser votre propre serveur DNS, consultez la rubrique [Résolution de noms (DNS)](http://msdn.microsoft.com/library/azure/jj156088.aspx).

##<a id="powershell"></a>Approvisionnement d'un cluster HBase au moyen d'Azure PowerShell

**Pour approvisionner un cluster HBase au moyen d'Azure PowerShell** :

1. Ouvrez PowerShell ISE.
2. Copiez et collez ce qui suit dans le volet de script.

		$hbaseClusterName = "<HBaseClusterName>"
		$hadoopUserName = "<HBaseClusterUsername>"
		$hadoopUserPassword = "<HBaseClusterUserPassword>"
		$location = "<HBaseClusterLocation>"  #i.e. "West US"
		$clusterSize = <HBaseClusterSize>  
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"
		
		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password) 
		
		New-AzureHDInsightCluster -Name $hbaseClusterName `
		                          -ClusterType HBase `
		                          -Version 3.1 `
		                          -Location $location `
		                          -ClusterSizeInNodes $clusterSize `
		                          -Credential $creds `
		                          -VirtualNetworkId $vnetID `
		                          -SubnetName $subNetName `
		                          -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		                          -DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainerName $storageContainerName


3. Cliquez sur **Exécuter le script** ou appuyez sur **F5**.
4. Pour valider le cluster, vous pouvez vérifier le cluster à partir du portail de gestion ou exécuter la cmdlet PowerShell suivante à partir du volet inférieur :

	Get-AzureHDInsightCluster 

##<a id="nextsteps"></a>Étapes suivantes

Dans ce didacticiel, nous avons appris à approvisionner un cluster HBase. Pour plus d'informations, consultez les rubriques suivantes :

- [Prise en main de HDInsight][hdinsight-get-started]
- [Approvisionnement de clusters Hadoop dans HDInsight][hdinsight-provision] 
- [Prise en main de HBase avec Hadoop dans HDInsight][hbase-get-started]
- [Analyse de sentiments Twitter avec HBase dans HDInsight][hbase-twitter-sentiment]
- [Présentation du réseau virtuel][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: ../hdinsight-hbase-get-started/
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment/
[vnet-overview]: http://msdn.microsoft.com/library/azure/jj156007.aspx
[vm-create]: ../virtual-machines-windows-tutorial/

[azure-portal]: https://manage.windowsazure.com
[azure-create-storageaccount]: ../storage-create-storage-account/ 
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/#rdp

[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: ../install-configure-powershell


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage/#powershell
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Provision details for the new HBase cluster"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Use Script Action to customize an HBase cluster.


<!--HONumber=42-->
