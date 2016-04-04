<properties
	pageTitle="Approvisionnement de clusters HBase sur un réseau virtuel | Microsoft Azure"
	description="Prise en main de HBase dans Azure HDInsight Découvrez comment créer des clusters HDInsight HBase sur Azure Virtual Network."
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
   ms.date="02/17/2016"
   ms.author="jgao"/>

# Création de clusters HBase sur Azure Virtual Network 

Découvrez comment créer des clusters Azure HDInsight sur [Azure Virtual Network][1].

Avec l’intégration du réseau virtuel, les clusters HBase peuvent être déployés sur le même réseau virtuel que vos applications pour permettre à celles-ci de communiquer directement avec HBase. Voici les avantages :

- Connectivité directe de l’application web aux nœuds du cluster HBase, ce qui permet les communications au moyen d’API d’appel de procédure distante (RPC) Java HBase.
- Amélioration des performances en évitant à votre trafic de transiter par plusieurs passerelles et équilibrages de charge.
- Capacité de traitement des informations critiques de façon plus sécurisée sans exposer de points de terminaison publics.

###Configuration requise
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez la page [Installation et utilisation d’Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## Création de clusters HBase sur un réseau virtuel

Dans cette section, vous allez créer un cluster HBase sous Linux dans HDInsight avec le [modèle ARM Azure](../resource-group-template-deploy.md). Aucune expérience avec le modèle ARM Azure n'est requise pour ce didacticiel. Pour d'autres méthodes de création de cluster et comprendre les paramètres, consultez [Création de clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Pour plus d'informations sur l'utilisation du modèle ARM pour créer des clusters Hadoop dans HDInsight, consultez [Création de clusters Hadoop dans HDInsight à l'aide de modèles ARM](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

1. Cliquez sur l’image suivante pour ouvrir un modèle ARM dans le portail Azure. Le modèle ARM est situé dans un conteneur blob public. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/fr-FR/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. À partir du panneau **Paramètres**, saisissez les informations suivantes :

    - **ClusterName** : entrez un nom pour le cluster Hadoop que vous allez créer.
    - **Nom d’utilisateur et mot de passe de cluster** : le nom de connexion par défaut est **admin**.
    - **Nom d’utilisateur SSH et mot de passe** : le nom d’utilisateur par défaut est **sshuser**. Vous pouvez le renommer. 

    Un grand nombre de propriétés ont été codées en dur dans le modèle. Par exemple :
    
    - Emplacement : Est des États-Unis
    - Nombre de nœuds de travail du cluster : 4
    - Compte de stockage par défaut : <Cluster Name>store
    - Nom du réseau virtuel : <Cluster Name>-vnet
    - Espace d’adressage du réseau virtuel : 10.0.0.0/16
    - Nom du sous-réseau : par défaut
    - Plage d'adresses du sous-réseau : 10.0.0.0/24

3. Cliquez sur **OK** pour enregistrer les paramètres.
4. Dans le panneau **Déploiement personnalisé**, cliquez sur la zone de liste déroulante **Groupe de ressources**, puis cliquez sur **Nouveau** pour créer un nouveau groupe de ressources. Le groupe de ressources est un conteneur qui regroupe le cluster, le compte de stockage dépendant et une autre ressource liée.
5. Cliquez sur **Conditions juridiques**, puis cliquez sur **Créer**.
6. Cliquez sur **Create**. Vous verrez une nouvelle vignette intitulée **Envoi de déploiement pour le déploiement du modèle**. La création d’un cluster prend environ 20 minutes. Une fois le cluster créé, vous pouvez cliquer sur le panneau du cluster dans le portail pour l’ouvrir.

Après avoir terminé ce didacticiel, vous souhaiterez peut-être supprimer le cluster. Avec HDInsight, vos données sont stockées Azure Storage, pour que vous puissiez supprimer un cluster en toute sécurité s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés. Pour obtenir des instructions sur la suppression d’un cluster, consultez la page [Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure](hdinsight-administer-use-management-portal.md#delete-clusters).

Pour commencer à utiliser votre nouveau cluster HBase, vous pouvez utiliser les procédures figurant dans la rubrique [Prise en main de HBase avec Hadoop dans HDInsight](hdinsight-hbase-tutorial-get-started.md).

##Connexion au cluster HBase avec les API RPC Java HBase

1.	Créez une machine virtuelle IaaS dans le même réseau virtuel Azure et le même sous-réseau. Ainsi, la machine virtuelle et le cluster HBase utilisent tous deux le même serveur DNS interne pour résoudre les noms d'hôte. Pour cela, vous devez choisir l’option **À partir de la galerie** et sélectionner le réseau virtuel au lieu d’un centre de données. Pour obtenir des instructions, consultez le didacticiel [Création d'une machine virtuelle exécutant Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md). Une image standard de Windows Server 2012 avec une petite taille de machine virtuelle est suffisante.

2.	Lorsque vous utilisez une application Java pour vous connecter à distance à HBase, vous devez utiliser le nom de domaine complet. Pour déterminer cela, vous devez obtenir le suffixe DNS propre à la connexion du cluster HBase. À cette fin, utilisez Curl pour interroger Ambari ou le Bureau à distance pour effectuer une connexion au cluster.

	* **Curl** : utilisez la commande suivante :

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		Dans les données JavaScript Object Notation (JSON) renvoyées, recherchez l’entrée « host\_name ». Elle contient le nom de domaine complet des nœuds du cluster. Par exemple :

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		La partie du nom de domaine commençant par le nom de cluster est le suffixe DNS. Par exemple, mon\_cluster.b1.cloudapp.net.

	* **Azure PowerShell** : utilisez le script Azure PowerShell suivant pour enregistrer la fonction **Get-ClusterDetail**, qui peut être utilisée pour renvoyer le suffixe DNS.

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

	> [AZURE.NOTE] Vous pouvez également utiliser le Bureau à distance pour vous connecter au cluster HBase (vous serez connecté au nœud principal) et exécuter **ipconfig** à partir d’une invite de commandes pour obtenir le suffixe DNS. Pour des instructions sur l’activation du protocole RDP (Remote Desktop Protocol) et la façon de se connecter au moyen de ce dernier, consultez la page [Gestion des clusters Hadoop dans HDInsight au moyen du portail de gestion Azure][hdinsight-admin-portal].
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

Pour utiliser ces informations dans une application Java, vous pouvez suivre la procédure décrite dans la rubrique [Utilisation de Maven pour créer des applications Java utilisant HBase avec HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md) pour créer une application. Pour que l’application soit connectée à un serveur HBase distant, modifiez le fichier **hbase-site.xml** dans cet exemple afin d’utiliser le nom de domaine complet pour Zookeeper. Par exemple :

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE] Pour plus d’informations sur la résolution de noms sur des réseaux virtuels Azure, y compris la façon d’utiliser votre propre serveur DNS, consultez la rubrique [Résolution de noms (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

##Étapes suivantes

Dans ce didacticiel, vous avez appris à créer un cluster HBase. Pour plus d'informations, consultez les rubriques suivantes :

- [Prise en main de HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Configuration de la géo-réplication HBase dans HDInsigtht](hdinsight-hbase-geo-replication.md)
- [Créer des clusters Hadoop dans HDInsight](hdinsight-provision-clusters.md)
- [Prise en main de HBase avec Hadoop dans HDInsight](hdinsight-hbase-tutorial-get-started.md)
- [Analyse de sentiments Twitter avec HBase dans HDInsight](hdinsight-hbase-analyze-twitter-sentiment.md)
- [Présentation du réseau virtuel.][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines/virtual-machines-windows-hero-tutorial.md

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


[powershell-install]: powershell-install-configure.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Détails de configuration pour le nouveau cluster HBase"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Utilisation de l’action de script pour personnaliser un cluster HBase"

[azure-preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_0323_2016-->