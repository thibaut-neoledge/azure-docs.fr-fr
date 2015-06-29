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
   ms.date="04/21/2015"
   ms.author="jgao"/>

# Approvisionnement de clusters HBase sur Azure Virtual Network

Découvrez comment créer des clusters Azure HDInsight sur [Azure Virtual Network][1].

Avec l’intégration du réseau virtuel, les clusters HBase peuvent être déployés sur le même réseau virtuel que vos applications pour permettre à celles-ci de communiquer directement avec HBase. Voici les avantages :

- Connectivité directe de l’application web aux nœuds du cluster HBase, ce qui permet les communications au moyen d’API d’appel de procédure distante (RPC) Java HBase.
- Amélioration des performances en évitant à votre trafic de transiter par plusieurs passerelles et équilibrages de charge.
- Capacité de traitement des informations critiques de façon plus sécurisée sans exposer de points de terminaison publics.

##Configuration requise
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez [Obtenir une version d'évaluation gratuite d'Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez [Installation et utilisation d'Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell](../install-configure-powershell.md). Pour exécuter des scripts Azure PowerShell, vous devez exécuter Azure PowerShell en tant qu’administrateur et définir la stratégie d’exécution sur *RemoteSigned*. Consultez la rubrique [Utilisation de l'applet de commande Set-ExecutionPolicy][2].

	Avant d’exécuter vos scripts Azure PowerShell, assurez-vous que vous êtes connecté à votre abonnement Azure à l’aide de la cmdlet suivante :

		Add-AzureAccount

	Si vous possédez plusieurs abonnements Azure, utilisez la cmdlet suivante pour définir l'abonnement en cours :

		Select-AzureSubscription <AzureSubscriptionName>


##Approvisionnement d’un cluster HBase dans un réseau virtuel 

Avant la configuration d’un cluster HBase, vous devez disposer d’un réseau virtuel Azure.

**Pour créer un réseau virtuel au moyen du portail Azure**

1. Connectez-vous au [portail Azure][azure-portal].
2. Cliquez sur **NOUVEAU** dans le coin inférieur gauche, puis sur **SERVICES RÉSEAU**, **RÉSEAU VIRTUEL** et **CRÉATION RAPIDE**.
3. Tapez ou sélectionnez les valeurs suivantes :

	- **Nom** : le nom de votre réseau virtuel.
	- **Espace d’adressage** : choisissez un espace d’adressage pour le réseau virtuel qui est suffisant grand pour fournir des adresses pour tous les nœuds du cluster. Si ce n'est pas le cas, l'approvisionnement échoue. Pour parcourir ce didacticiel, vous pouvez choisir l'une des trois options suivantes. 
	- **Nombre maximal de machines virtuelles** : choisissez l’un des nombres maximaux de machines virtuelles (VM). Cette valeur détermine le nombre d'hôtes (machines virtuelles) qu'il est possible de créer sous l'espace d'adressage. Pour parcourir ce didacticiel, **4096 [CIDR: /20]** est suffisant. 
	- **Emplacement** : l’emplacement doit être le même que celui du cluster HBase que vous allez créer.
	- **Serveur DNS** : ce didacticiel utilise un serveur de système de nom de domaine (DNS) interne fourni par Azure, vous pouvez donc choisir **Aucun**. Des configurations de réseau plus avancées avec des serveurs DNS personnalisés sont également prises en charge. Pour obtenir des instructions détaillées, consultez la page [Résolution de noms (DNS)](http://msdn.microsoft.com/library/azure/jj156088.aspx).
4. Cliquez sur **CRÉER UN RÉSEAU VIRTUEL** dans le coin inférieur droit. Le nom du nouveau réseau virtuel apparaît dans la liste. Attendez que la valeur **Créé** apparaisse dans la colonne Statut.
5. Dans le volet principal, cliquez sur le réseau virtuel que vous venez de créer.
6. Cliquez sur **TABLEAU DE BORD** en haut de la page.
7. Sous **aperçu rapide**, prenez note de l’ID de réseau virtuel. Vous en aurez besoin lors de l’approvisionnement du cluster HBase.
8. Cliquez sur **CONFIGURER** en haut de la page.
9. En bas de la page, le nom du sous-réseau par défaut est **Sous-réseau-1**. Facultativement, vous pouvez renommer le sous-réseau ou ajouter un nouveau sous-réseau pour le cluster HBase. Prenez note du nom du sous-réseau, car vous en aurez besoin lors de l’approvisionnement du cluster.
10. Vérifiez **CIDR (NOMBRE D’ADRESSES)** pour le sous-réseau qui sera utilisé pour le cluster. Le nombre d’adresses doit être supérieur au nombre de nœuds de travail plus 7 (passerelle : 2, nœud principal : 2, Zookeeper : 3). Par exemple, si vous avez besoin d’un cluster HBase à 10 nœuds, le nombre d’adresses pour le sous-réseau doit être supérieur à 17 (10+7). Si ce n'est pas le cas, le déploiement échoue.
11. Cliquez sur **Enregistrer** en bas de la page, si vous avez mis à jour les valeurs de sous-réseau.


**Pour ajouter un ordinateur virtuel du serveur DNS sur le réseau virtuel**

Un serveur DNS est facultatif, mais il est nécessaire dans certains cas. La procédure a été documentée dans [Configuration d’un serveur DNS entre deux réseaux virtuels Azure][hdinsight-hbase-replication-dns]. Vous devez effectuer les étapes suivantes :

1. ajouter une machine virtuelle au réseau virtuel
2. configurer une adresse IP statique pour la machine virtuelle
3. ajouter le rôle Serveur DNS à la machine virtuelle
4. Assignation des serveurs DNS au réseau virtuel 


**Pour créer un compte de stockage Azure et un conteneur de stockage d’objets blob à utiliser par le cluster**

> [AZURE.NOTE]Les clusters HDInsight utilisent le stockage d'objets blob Azure pour stocker des données. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec Hadoop dans HDInsight](../hdinsight-use-blob-storage.md). Vous aurez besoin d'un compte de stockage et d'un conteneur de stockage d'objets blob. Le compte de stockage doit être situé au même emplacement que le réseau virtuel et le cluster.

À l’instar des autres clusters HDInsight, un cluster HBase requiert un compte de stockage Azure et un conteneur de stockage d’objets blob Azure comme système de fichiers par défaut. Le compte de stockage doit être situé au même emplacement que le réseau virtuel et le cluster. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec Hadoop dans HDInsight][hdinsight-storage]. Lorsque vous configurez un cluster HBase, vous pouvez en créer ou utiliser ceux qui existent déjà. Cette procédure montre comment créer un compte de stockage et un conteneur de stockage d’objets blob à l’aide du portail Azure.

1. Connectez-vous au [portail Azure][azure-portal].
2. Cliquez sur **NOUVEAU** dans le coin inférieur gauche, cliquez sur **SERVICES DE DONNÉES**, **STOCKAGE**, puis cliquez sur **CRÉATION RAPIDE**.

3. Tapez ou sélectionnez les valeurs suivantes :

	- **URL** : le nom du compte de stockage.
	- **EMPLACEMENT** : l’emplacement du compte de stockage. Vérifiez qu'il correspond à l'emplacement du réseau virtuel. Les groupes d'affinités ne sont pas pris en charge.
	- **RÉPLICATION** : à des fins de test, utilisez **Redondant en local** pour réduire le coût.

4. Cliquez sur **CREATE STORAGE ACCOUNT**. Le nouveau compte de stockage figure dès lors dans la liste de stockage.
5. Attendez que l’**ÉTAT** du nouveau compte de stockage passe à **En ligne**.
6. Cliquez sur le nouveau compte de stockage dans la liste pour le sélectionner.
7. Cliquez sur **GÉRER LES CLÉS D'ACCÈS** dans le bas de la page.
8. Notez le nom du compte de stockage et la clé d’accès primaire (ou la clé d’accès secondaire, les deux peuvent être utilisées). Vous en aurez besoin plus loin dans le didacticiel.
9. En haut de la page, cliquez sur **CONTENEUR**.
10. En bas de la page, cliquez sur **AJOUTER**.
11. Entrez le nom du conteneur. Ce conteneur sera utilisé comme conteneur par défaut pour le cluster HBase. Par défaut, le nom du conteneur par défaut correspond au nom du cluster. Pour le champ **ACCÈS**, gardez la valeur **Privé**.  
12. Cliquez sur la coche pour créer le conteneur.

**Approvisionnement d’un cluster HBase en utilisant le portail Azure**

> [AZURE.NOTE]Pour plus d’informations sur l’approvisionnement d’un nouveau cluster HBase au moyen d’Azure PowerShell, consultez la rubrique [Approvisionnement d’un cluster HBase au moyen d’Azure PowerShell](#powershell).

1. Connectez-vous au [portail Azure][azure-portal].

2. Cliquez sur **NOUVEAU** dans le coin inférieur gauche, pointez sur **SERVICES DE DONNÉES**, sur **HDINSIGHT**, puis cliquez sur **CRÉATION PERSONNALISÉE**.

3. Entrez un nom de cluster, sélectionnez HBase comme type de cluster, sélectionnez le système d’exploitation Windows Server 2012, sélectionnez la version de HDInsight, puis cliquez sur le bouton de droite.

	![Fournir des détails pour le cluster HBase][img-provision-cluster-page1]


	> [AZURE.NOTE]Pour un cluster HBase, Windows Server est le seul système d’exploitation disponible.

4. Sur la page **Configurer le cluster**, entrez ou sélectionnez les éléments suivants :

	![Fournir des détails pour le cluster HBase](./media/hdinsight-hbase-provision-vnet/hbasewizard2.png)

	<table border='1'>
	<tr><th>Propriété</th><th>Valeur</th></tr>
	<tr><td>Nœuds de données</td><td>Sélectionnez le nombre de nœuds de données que vous souhaitez déployer. À des fins de test, créez un cluster à nœud unique. <br />La limite de taille de cluster varie pour les abonnements Azure. Contactez le support de facturation Azure pour augmenter la limite.</td></tr>
	<tr><td>Région/Réseau virtuel</td><td><p>Sélectionnez une région ou un réseau virtuel Azure, si vous en avez déjà créé un. Pour ce didacticiel, sélectionnez le réseau que vous avez créé précédemment, puis un sous-réseau correspondant. Le nom par défaut est <b>Sous-réseau-1</b>.</p></td></tr>
	<tr><td>Taille du nœud principal</td><td><p>Sélectionnez une taille de machine virtuelle pour le nœud principal.</p></td></tr>
	<tr><td>Taille du nœud de données</td><td><p>Sélectionnez une taille de machine virtuelle pour le nœud de données.</p></td></tr>
	<tr><td>Taille du nœud Zookeeper</td><td><p>Sélectionnez une taille de machine virtuelle pour le nœud Zookeeper.</p></td></tr>
</table> >[AZURE.NOTE]Selon votre choix de machines virtuelles, les coûts peuvent varier. HDInsight utilise toutes les machines virtuelles de niveau standard pour les nœuds de cluster. Pour plus d’informations sur les répercussions de la taille des machines virtuelles sur les prix, consultez <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Tarification HDInsight</a>.

	Cliquez avec le bouton droit.

5. Entrez le nom d’utilisateur et le mot de passe Hadoop à utiliser pour ce cluster, puis cliquez avec le bouton droit.

	![Fournir un compte de stockage pour le cluster Hadoop HDInsight](./media/hdinsight-hbase-provision-vnet/hbasewizard3.png)

	<table border='1'>
	<tr><th>Propriété</th><th>Valeur</th></tr>
	<tr><td>Nom d’utilisateur HTTP</td>
		<td>Spécifiez le nom d'utilisateur de cluster HDInsight.</td></tr>
	<tr><td>Mot de passe HTTP/Confirmer le mot de passe</td>
		<td>Spécifiez le mot de passe de cluster HDInsight.</td></tr>
	<tr><td>Activation du Bureau à distance pour le cluster</td>
		<td>Cochez cette case pour spécifier une date d’expiration, un nom d’utilisateur et un mot de passe pour un utilisateur de bureau à distance pouvant se connecter aux nœuds de cluster à distance, après configuration. Vous pouvez également activer le Bureau à distance ultérieurement, une fois le cluster configuré. Pour la marche à suivre, consultez <a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">Connexion à des clusters HDInsight à l’aide de RDP</a>.</td></tr>
</table>

6. Sur la page **Compte de stockage**, entrez les valeurs suivantes :

    ![Fournir un compte de stockage pour le cluster Hadoop HDInsight](./media/hdinsight-hbase-provision-vnet/hbasewizard4.png)

	<table border='1'>
	<tr><th>Propriété</th><th>Valeur</th></tr>
	<tr><td>Compte de stockage</td>
		<td>Spécifiez le compte Azure Storage qui sera utilisé comme système de fichiers par défaut pour le cluster HDInsight. Vous pouvez choisir l'une des trois options suivantes&#160;:
		<ul>
			<li><strong>Utiliser le stockage existant</strong></li>
			<li><strong>Créer un nouveau stockage</strong></li>
			<li><strong>Utiliser le stockage associé à un autre abonnement</strong></li>
		</ul>
		</td></tr>
	<tr><td>Nom du compte</td>
		<td><ul>
			<li>Si vous choisissez d’utiliser un stockage existant, pour <strong>Nom du compte</strong>, sélectionnez un compte de stockage existant. La liste déroulante répertorie uniquement les comptes de stockage situés dans le centre de données sur lequel vous voulez approvisionner le cluster.</li>
			<li>Si vous choisissez l’option <strong>Créer un nouveau stockage</strong> ou <strong>Utiliser le stockage associé à un autre abonnement</strong>, vous devez fournir le nom du compte de stockage.</li>
		</ul></td></tr>
	<tr><td>Clé du compte</td>
		<td>Si vous choisissez l’option <strong>Utiliser le stockage associé à un autre abonnement</strong>, veuillez indiquer la clé de ce compte de stockage.</td></tr>
	<tr><td>Conteneur par défaut</td>
		<td><p>Spécifiez le conteneur par défaut du compte de stockage qui sera utilisé comme système de fichiers par défaut pour le cluster HDInsight. Si vous sélectionnez l’option <strong>Utiliser le stockage existant</strong> pour le champ <strong>Compte de stockage</strong> et qu’il n’y a aucun conteneur existant dans ce compte, le conteneur est créé par défaut avec le même nom que celui du cluster. Si un conteneur portant le nom du cluster existe déjà, un numéro de séquence est ajouté au nom de conteneur. Par exemple, mon_conteneur1, mon_conteneur2, et ainsi de suite. Cependant, si le compte de stockage existant dispose d’un conteneur dont le nom est différent de celui du cluster spécifié, vous pouvez également utiliser ce conteneur.</p>
        <p>Si vous avez choisi de créer un stockage ou d'utiliser le stockage d'un autre abonnement Azure, vous devez spécifier le nom du conteneur par défaut.</p>
    </td></tr>
	<tr><td>Comptes de stockage supplémentaires</td>
		<td>Si nécessaire, spécifiez les comptes de stockage supplémentaires pour le cluster. HDInsight prend en charge plusieurs comptes de stockage. Le nombre de comptes de stockage supplémentaires pouvant être utilisés par un cluster n’est pas limité. Toutefois, si vous créez un cluster au moyen du portail Azure, la limite est établie à sept en raison de contraintes liées à l’interface utilisateur. Chaque compte de stockage supplémentaire que vous spécifiez dans ce champ ajoute une page <strong>Compte de stockage</strong> supplémentaire vers l’Assistant vous permettant de spécifier les informations de compte. Par exemple, dans la capture d’écran suivante, 1&#160;compte de stockage supplémentaire est sélectionné, une page est donc ajoutée à l’assistant.</td></tr>
</table>Cliquez sur la flèche droite.

7. Sur la page **Actions de script**, activez la case à cocher dans le coin inférieur droit. Ne cliquez pas sur le bouton **Ajouter une action de script**, étant donné que ce didacticiel ne nécessite pas une configuration de cluster personnalisée.
	
	![Configuration de l’action de script pour personnaliser un cluster HDInsight HBase][img-provision-cluster-page5]

	> [AZURE.NOTE]Cette page permet de personnaliser le cluster lors de l’installation. Pour plus d’informations, consultez l’article [Personnaliser des clusters HDInsight à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster.md).
	
Pour commencer à utiliser votre nouveau cluster HBase, vous pouvez utiliser les procédures figurant dans la rubrique [Prise en main de HBase avec Hadoop dans HDInsight](../hdinsight-hbase-get-started.md).

##Connexion au cluster HBase approvisionné dans le réseau virtuel au moyen d’API RPC Java HBase

1.	Approvisionnez une machine virtuelle IaaS dans le même réseau virtuel Azure et le même sous-réseau. Ainsi, la machine virtuelle et le cluster HBase utilisent tous deux le même serveur DNS interne pour résoudre les noms d'hôte. Pour cela, vous devez choisir l’option **À partir de la galerie** et sélectionner le réseau virtuel au lieu d’un centre de données. Pour obtenir des instructions, consultez le didacticiel [Création d'une machine virtuelle exécutant Windows Server](../virtual-machines-windows-tutorial.md). Une image standard de Windows Server 2012 avec une petite taille de machine virtuelle est suffisante.
	
2.	Lorsque vous utilisez une application Java pour vous connecter à distance à HBase, vous devez utiliser le nom de domaine complet. Pour déterminer cela, vous devez obtenir le suffixe DNS propre à la connexion du cluster HBase. À cette fin, utilisez Curl pour interroger Ambari ou le Bureau à distance pour effectuer une connexion au cluster.

	* **Curl** : utilisez la commande suivante :

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		Dans les données JavaScript Object Notation (JSON) renvoyées, recherchez l’entrée « host_name ». Elle contient le nom de domaine complet des nœuds du cluster. Par exemple :

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		La partie du nom de domaine commençant par le nom de cluster est le suffixe DNS. Par exemple, mon_cluster.b1.cloudapp.net.

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

		Ceci renvoie le suffixe DNS. Par exemple, **votre_nom_cluster.b4.internal.cloudapp.net**.

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

Pour vérifier que la machine virtuelle peut communiquer avec le cluster HBase, utilisez la commande `ping headnode0.<dns suffix>` à partir de la machine virtuelle. Par exemple, ping headnode0.mon_cluster.b1.cloudapp.net.

Pour utiliser ces informations dans une application Java, vous pouvez suivre la procédure décrite dans la rubrique [Utilisation de Maven pour créer des applications Java utilisant HBase avec HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md) pour créer une application. Pour que l’application soit connectée à un serveur HBase distant, modifiez le fichier **hbase-site.xml** dans cet exemple afin d’utiliser le nom de domaine complet pour Zookeeper. Par exemple :

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE]Pour plus d’informations sur la résolution de noms sur des réseaux virtuels Azure, y compris la façon d’utiliser votre propre serveur DNS, consultez la rubrique [Résolution de noms (DNS)](http://msdn.microsoft.com/library/azure/jj156088.aspx).

##Approvisionnement d’un cluster HBase au moyen d’Azure PowerShell

**Approvisionnement d’un cluster HBase au moyen d’Azure PowerShell**

1. Ouvrez l’environnement de script intégré (ISE) Azure PowerShell :
2. Copiez et collez ce qui suit dans le volet de script :

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
4. Pour valider le cluster, vous pouvez vérifier le cluster à partir du portail Azure ou exécuter la cmdlet Azure PowerShell suivante à partir du volet inférieur :

	Get-AzureHDInsightCluster

##Étapes suivantes

Dans ce didacticiel, vous avez appris à approvisionner un cluster HBase. Pour plus d'informations, consultez les rubriques suivantes :

- [Prise en main de HDInsight](../hdinsight-get-started.md)
- [Configuration de la géo-réplication HBase dans HDInsigtht](hdinsight-hbase-geo-replication.md) 
- [Approvisionnement de clusters dans HDInsight](hdinsight-provision-clusters.md) 
- [Prise en main de HBase avec Hadoop dans HDInsight](../hdinsight-hbase-get-started.md)
- [Analyse de sentiments Twitter avec HBase dans HDInsight](../hdinsight-hbase-twitter-sentiment.md)
- [Présentation du réseau virtuel][vnet-overview].


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: ../hdinsight-hbase-get-started.md
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment.md
[vnet-overview]: http://msdn.microsoft.com/library/azure/jj156007.aspx
[vm-create]: ../virtual-machines-windows-tutorial.md

[azure-portal]: https://manage.windowsazure.com
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
 

<!---HONumber=58_postMigration-->