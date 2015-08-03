<properties
   pageTitle="Mettre à l’échelle les clusters Hadoop, HBase ou Apache Storm dans HDInsight | Microsoft Azure"
   description="Modifiez le nombre de nœuds de données dans un cluster Hadoop, Apache Storm ou HBase exécuté dans HDInsight sans avoir à supprimer puis recréer ce cluster."
   services="hdinsight"
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
   ms.date="06/30/2015"
   ms.author="jgao"/>

#Mettez à l’échelle les clusters Hadoop, HBase ou Apache Storm afin de modifier leur nombre de nœuds de données dans HDInsight 

La fonctionnalité de mise à l’échelle d’un cluster vous permet de modifier le nombre de nœuds de données utilisés par un cluster exécuté dans Azure HDInsight sans avoir à supprimer puis recréer ce cluster. Cette opération peut être effectuée à l’aide d’Azure PowerShell, du Kit de développement logiciel (SDK) ou depuis le portail Azure.

## Informations relatives à la fonctionnalité
Cette section décrit l'impact de la modification du nombre de nœuds de données pour chaque type de cluster pris en charge par HDInsight :

* Hadoop
* Apache Storm
* HBase 

## Hadoop 

### Ajout de nœuds de données
Vous pouvez augmenter de façon continue le nombre de nœuds de données dans un cluster Hadoop exécuté sans affecter aucune tâche en attente ou en cours. De nouvelles tâches peuvent également être soumises lorsque l'opération est en cours. Les défaillances dans l'opération de mise à l'échelle sont correctement gérées de sorte que le cluster reste toujours fonctionnel.

### Suppression des nœuds de données
Lorsqu’un cluster Hadoop est diminué par la réduction du nombre de nœuds de données, certains services du cluster sont redémarrés. Pour cette raison, toutes les tâches en cours ou en attente échouent lors de la réalisation de l'opération de mise à l'échelle. Toutefois, vous pouvez soumettre à nouveau les tâches une fois l'opération terminée.

## Storm
Vous pouvez ajouter ou supprimer des nœuds de données en continu dans votre cluster Storm lorsque celui-ci s'exécute. Mais une fois l’opération de mise à l’échelle terminée avec succès, vous devrez rééquilibrer la topologie.

Cela peut se faire de deux façons à l’aide de :

* l'interface utilisateur Web de Storm
* l’outil d’interface de ligne de commande (CLI) 

Pour plus d’informations, consultez la documentation [Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

L’interface utilisateur web de Storm est disponible dans le cluster HDInsight :

![image1](./media/hdinsight-hadoop-cluster-scaling/StormUI.png)

Voici un exemple relatif à l'utilisation de la commande de l'interface en ligne de commande pour rééquilibrer la topologie Storm :

	## Reconfigure the topology "mytopology" to use 5 worker processes,
	## the spout "blue-spout" to use 3 executors, and
	## the bolt "yellow-bolt" to use 10 executors

	$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

##HBase
Vous pouvez ajouter ou supprimer des nœuds en continu dans votre cluster HBase lorsque celui-ci s’exécute. Les serveurs régionaux sont équilibrés automatiquement quelques minutes après la fin de l’opération de mise à l’échelle. Cependant, vous pouvez équilibrer manuellement des serveurs régionaux en vous connectant au nœud principal du cluster et en exécutant les commandes suivantes à partir d’une fenêtre d’invite de commandes :

	>pushd %HBASE_HOME%\bin
	>hbase shell
	>balancer

## Configuration requise

* Seuls les clusters ayant la version 3.1.3 de HDInsight ou une version ultérieure sont pris en charge. Si vous n’êtes pas sûr de la version de votre cluster, vous pouvez la vérifier depuis le portail Azure en cliquant sur le nom du cluster HDInsight ou en exécutant la commande `Get-AzureHDInsightCluster –name <clustername>` depuis Azure PowerShell.

* La version 0.8.14 d’Azure PowerShell ou une version ultérieure est requise pour effectuer l’opération à partir d’Azure PowerShell. Vous pouvez télécharger la dernière version d’Azure PowerShell depuis la section **Outils en ligne de commande** du site web [Téléchargements Azure](http://azure.microsoft.com/downloads/). Vous pouvez vérifier votre version d’Azure PowerShell sur le portail Azure à l’aide de la commande suivante, depuis une fenêtre Azure PowerShell : `(get-module Azure).Version`

## Utilisation de la mise à l’échelle d’un cluster

### Portail Azure
La taille d’un cluster exécuté peut être modifiée depuis l’onglet **METTRE À L’ÉCHELLE** du tableau de bord du cluster Azure HDInsight.

![](http://i.imgur.com/u5Mewwx.png)

### Azure PowerShell
Pour modifier la taille du cluster Hadoop à l’aide d’Azure PowerShell, exécutez la commande suivante depuis un ordinateur client :

	Set-AzureHDInsightClusterSize -ClusterSizeInNodes <NewSize> -name <clustername>	

> [AZURE.NOTE]La machine cliente doit disposer de la version 0.8.14 d'Azure PowerShell ou une version ultérieure pour utiliser cette commande.

### Foundation
Pour modifier la taille du cluster Hadoop à l’aide du Kit de développement logiciel (SDK) HDInsight, appliquez l’une des méthodes suivantes :

	ChangeClusterSize(string dnsName, string location, int newSize) 

ou

	ChangeClusterSizeAsync(string dnsName, string location, int newSize) 


Les versions synchrones et asynchrones de cette méthode renvoient toutes les deux un objet [ClusterDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterdetails_properties.aspx).

Voici un exemple de code qui montre comment utiliser la version synchrone de cette méthode :

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
	     		string location = "<ClusterLocation>”";
				int newSize = <NewClusterSize>;
	
	            // Get the certificate object from certificate store by using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);
	
	            // Create an HDInsightClient object
	            HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
	            var client = HDInsightClient.Connect(creds);
	
	            Console.WriteLine("Rescaling HDInsight cluster ...");
	
	            // Change the cluster size
	     		ClusterDetails cluster = client.ChangeClusterSize(clustername, location, newSize);
	            
	            Console.WriteLine("Cluster Rescaled: {0} \n New Cluster Size = {1}", cluster.ConnectionUrl, cluster.ClusterSizeInNodes);
	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadKey();
	        }
	    }
	}


Pour plus d’informations sur l’utilisation du Kit de développement logiciel (SDK) HDInsight .NET, consultez la rubrique [Approvisionnement de clusters Hadoop dans HDInsight à l’aide d’options personnalisées](hdinsight-provision-clusters.md).
 

<!---HONumber=July15_HO4-->