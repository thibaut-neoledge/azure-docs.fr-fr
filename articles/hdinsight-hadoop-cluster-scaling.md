<properties
   pageTitle="Mise à l'échelle d'un cluster dans HDInsight | Azure"
   description="Modifiez le nombre de nœuds de données dans un cluster exécuté dans HDInsight sans avoir à supprimer puis recréer ce cluster."
   services="hdinsight"
   documentationCenter=""
   authors="bradsev"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="bradsev"/>

#Mise à l'échelle d'un cluster dans HDInsight

La fonctionnalité de mise à l'échelle d'un cluster vous permet de modifier le nombre de nœuds de données utilisés par un cluster exécuté dans HDInsight sans avoir à supprimer puis recréer ce cluster. Cette opération peut être effectuée à l'aide de PowerShell, du kit de développement logiciel (SDK) ou depuis le portail Azure.

> [WACOM.NOTE] Seuls les clusters de type Hadoop et Storm sont pris en charge dans la version actuelle. La prise en charge des clusters HBase sera bientôt possible. 

## Informations relatives à la fonctionnalité
Cette section décrit l'impact de la modification du nombre de nœuds de données pour chaque type de cluster pris en charge par HDInsight :

* Hadoop
* Storm
* HBase 

## Hadoop 

### Ajout de nœuds de données
Vous pouvez augmenter de façon continue le nombre de nœuds de données dans un cluster Hadoop exécuté sans affecter aucune tâche en attente ou en cours. De nouvelles tâches peuvent également être soumises lorsque l'opération est en cours. Les défaillances dans l'opération de mise à l'échelle sont correctement gérées de sorte que le cluster reste toujours fonctionnel.

### Suppression des nœuds de données
Lorsqu'un cluster Hadoop est diminué par la réduction du nombre de nœuds de données, certains services du cluster sont redémarrés. Pour cette raison, toutes les tâches en cours ou en attente échouent lors de la réalisation de l'opération de mise à l'échelle. Toutefois, vous pouvez soumettre à nouveau les tâches une fois l'opération terminée.

## Storm
Vous pouvez ajouter ou supprimer des nœuds de données en continu dans votre cluster Storm lorsque celui-ci s'exécute. Mais une fois l'opération de mise à l'échelle terminée avec succès, vous devrez rééquilibrer la topologie.

Vous pouvez la rééquilibrer de deux façons à l'aide de :

* l'interface utilisateur Web de Storm
* l'outil d'interface en ligne de commande 

Pour plus d'informations, consultez la [documentation Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

L'interface utilisateur Web de Storm est disponible dans le cluster HDInsight :

![image1](./media/hdinsight-hadoop-cluster-scaling/StormUI.png)

Voici un exemple relatif à l'utilisation de la commande de l'interface en ligne de commande pour rééquilibrer la topologie Storm :

	## Reconfigure the topology "mytopology" to use 5 worker processes,
	## the spout "blue-spout" to use 3 executors and
	## the bolt "yellow-bolt" to use 10 executors.

	$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

##HBase
L'opération de mise à l'échelle du cluster n'est pas prise en charge pour les clusters de type HBase pour le moment.

## Conditions préalables :

* Seuls les clusters avec une version HDInsight 3.1.3 ou version ultérieure sont pris en charge. Si vous n'êtes pas sûr de la version de votre cluster, vous pouvez la vérifier depuis le portail Azure en cliquant sur le nom du cluster HDInsight ou en exécutant la commande  `Get-AzureHDInsightCluster -name <clustername>` depuis Azure PowerShell.

* La version 0.8.14 d'Azure PowerShell ou une version ultérieure est requise pour effectuer l'opération à partir de PowerShell. Vous pouvez télécharger la dernière version de PowerShell depuis la section des outils en ligne de commande sur le site Web [Téléchargements Azure](http://azure.microsoft.com/downloads/). Vous pouvez vérifier sur le portail Azure la version PowerShell que vous avez installée à l'aide de la commande suivante, depuis une fenêtre PowerShell : `(get-module Azure).Version`

## Utilisation de la mise à l'échelle d'un cluster

### Portail Azure
La taille d'un cluster exécuté peut être modifiée depuis l'onglet **Échelle** du tableau de bord du cluster Azure HDInsight.

![](http://i.imgur.com/u5Mewwx.png)

### PowerShell
Pour modifier la taille du cluster Hadoop à l'aide de PowerShell, exécutez la commande suivante depuis une machine cliente :

	Set-AzureHDInsightClusterSize -ClusterSizeInNodes <NewSize> -name <clustername>	

> [WACOM.NOTE] La machine cliente doit disposer de la version 0.8.14 d'Azure PowerShell ou une version ultérieure pour utiliser cette commande.

### Kit SDK
Pour modifier la taille du cluster Hadoop à l'aide du kit de développement logiciel (SDK) HDInsight, appliquez l'une des méthodes suivantes : 

	ChangeClusterSize(string dnsName, string location, int newSize) 

ou 

	ChangeClusterSizeAsync(string dnsName, string location, int newSize) 


Les versions synchrones et asynchrones de cette méthode renvoient toutes les deux un objet [ClusterDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterdetails_properties.aspx).

Voici un exemple de code qui montre comment utiliser la version synchrone de cette méthode :

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Security.Cryptography.X509Certificates;
	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

	namespace HDInsightClusterScaling
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            // Friendly name for the certificate your created earlier  
	            string certfriendlyname = "<CertificateFriendlyName>";     
	            string subscriptionid = "<SubscriptionID>";
	            string clustername = "<ClusterDNSName>";
	     		string location = "<ClusterLocation>";
				int newSize = <NewClusterSize>;
	
	            // Get the certificate object from certificate store using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);
	
	            // Create an HDInsightClient object
	            HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
	            var client = HDInsightClient.Connect(creds);
	
	            Console.WriteLine("Rescaling HDInsight cluster ...");
	
	            // Change cluster size
	     		ClusterDetails cluster = client.ChangeClusterSize(clustername, location, newSize);
	            
	            Console.WriteLine("Cluster Rescaled: {0} \n New Cluster Size = {1}", cluster.ConnectionUrl, cluster.ClusterSizeInNodes);
	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadKey();
	        }
	    }
	}


Pour plus d'informations sur l'utilisation de HDInsight .NET SDK, consultez la rubrique [Approvisionnement des clusters Hadoop dans HDInsight à l'aide d'options personnalisées](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/).
<!--HONumber=47-->
