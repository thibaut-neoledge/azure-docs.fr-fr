<properties 
	pageTitle="Accéder aux journaux des applications HDInsight par programmation| Azure" 
	description="Access HDInsight Application Logs Programmatically." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="bradsev"/>

# Accéder aux journaux des applications HDInsight par programmation

Cette rubrique explique comment énumérer par programmation les applications YARN terminées sur un cluster Hadoop dans HDInsight et comment accéder par programmation aux journaux des applications sans recourir à RDP dans vos clusters. Plus précisément, un nouveau composant et une nouvelle API sont ajoutés :

  1. Le serveur d'historique des applications génériques sur les clusters HDInsight est activé. Il s'agit d'un composant du serveur YARN Timeline qui gère le stockage et la récupération d'informations génériques sur les applications terminées.
  2. De nouvelles API sont ajoutées dans le Kit de développement logiciel (SDK) .Net Azure HDInsight pour vous permettre d'énumérer par programmation les applications exécutées sur vos clusters et de télécharger les journaux spécifiques à des applications ou à des conteneurs (au format texte brut) en vue du débogage de problèmes éventuels.


## Conditions préalables

Le SDK Azure HDInsight est requis pour utiliser le code présenté dans cette rubrique dans une application .NET. La dernière version publiée du Kit de développement logiciel (SDK) est disponible sur le site [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). 

Pour installer le SDK HDInsight à partir d'une application Visual Studio, accédez au menu **Outils**, cliquez sur **Gestionnaire de package NuGet**, puis sur **Console du gestionnaire de package**. Exécutez les commandes suivantes dans la console pour installer les packages.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

Cette commande ajoute les bibliothèques .NET pour HDInsight et les références à celles-ci au projet Visual Studio actuel.

## Dans cet article

- [Serveur YARN Timeline](#YARNTimelineServer)
- [Applications et journaux YARN](#YARNAppsAndLogs)
- [Énumération d'applications et téléchargement de journaux par programmation](#enumerate-and-download)


## <a name="YARNTimelineServer"></a>Serveur YARN Timeline

Le <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">serveur YARN Timeline</a> fournit des informations génériques sur les applications terminées, ainsi que des informations sur les applications spécifiques à l'infrastructure, via deux interfaces différentes. Plus précisément :

* Le stockage et la récupération d'informations génériques sur les applications sur les clusters HDInsight sont activés dans les versions 3.1.1.374 ou ultérieures. 
* Le composant fournissant des informations sur les applications spécifiques à l'infrastructure n'est pas actuellement disponible sur les clusters HDInsight.


Les informations génériques sur les applications comprennent les données suivantes : 

* l'identificateur unique de l'application (**ApplicationId**); 
* l'**utilisateur** qui a démarré l'application ; 
* des informations sur les **tentatives** effectuées afin de terminer l'application ; 
* et les **conteneurs** utilisés par toute tentative d'application donnée. 

Sur les clusters HDInsight, ces informations sont stockées par le gestionnaire de ressources dans un magasin d'historique dans le conteneur par défaut de votre compte de stockage par défaut. Vous pouvez récupérer ces données génériques sur les applications terminées via une API REST :

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps

Le SDK .Net HDInsight comprend de nouvelles API qui facilitent la récupération de ces données par programmation. Vous pouvez aussi récupérer les données génériques en exécutant les commandes CLI YARN directement sur vos nœuds de cluster (après connexion au cluster via RDP).

## <a name="YARNAppsAndLogs"></a>Applications et journaux YARN

YARN (Yet Another Resource Negotiator) prend en charge plusieurs modèles de programmation (dont MapReduce) en séparant la gestion des ressources de la planification et de l'analyse des applications. Cela s'effectue via un  *Resource Manager* (RM) global, des  *Node Managers* (NM) par nœud de travail et des  *Application Masters* (AM) par application. Le maître d'application, propre à chaque application, négocie les ressources nécessaires (processeur, mémoire, disque, réseau) pour exécuter l'application avec le gestionnaire de ressources. Le gestionnaire de ressources fonctionne avec les gestionnaires de nœuds pour octroyer ces ressources sous forme de *Containers*. Le maître d'application est chargé de suivre la progression des conteneurs qui lui sont assignés par le gestionnaire de ressources. Selon la nature de l'application, celle-ci peut nécessiter de nombreux conteneurs. 

Par ailleurs, chaque application peut se composer de plusieurs *Application Attempts* pour terminer l'application en cas de panne ou de perte de communication entre un maître d'application et un gestionnaire de ressources. Par conséquent, les conteneurs sont accordés à une tentative spécifique d'une application. D'une certaine façon, un conteneur fournit le contexte pour l'unité de base du travail effectué par une application YARN, et tout le travail effectué dans le contexte d'un conteneur est effectué sur le nœud de travail unique sur lequel le conteneur a été alloué. Pour plus d'informations, consultez la rubrique [Concepts relatifs à YARN][YARN-concepts].

Les journaux des applications (et les journaux des conteneurs associés) sont essentiels pour déboguer des applications Hadoop problématiques. La fonctionnalité [Agrégation de journaux][log-aggregation] de YARN fournit une infrastructure adaptée à la collecte, à l'agrégation et au stockage des journaux des applications. La fonctionnalité d'agrégation de journaux rend l'accès aux journaux des applications plus déterministe, car elle regroupe les journaux de tous les conteneurs sur un nœud de travail et les stocke dans un même fichier journal agrégé par nœud de travail sur le système de fichiers par défaut au terme de l'exécution d'une application. Votre application peut utiliser des centaines voire des milliers de conteneurs, mais les journaux de tous les conteneurs exécutés sur un nœud de travail unique sont toujours regroupés dans un fichier unique, ce qui se traduit par l'existence d'un fichier journal par nœud de travail utilisé par votre application. L'agrégation de journaux est activée par défaut sur les clusters HDInsight (versions 3.0 et ultérieures) et les journaux agrégés figurent dans le conteneur par défaut de votre cluster à l'emplacement suivant :

	wasb:///app-logs/<user>/logs/<applicationId>

où " *user* " est le nom de l'utilisateur ayant démarré l'application et " *applicationId* ", l'identificateur unique d'une application telle qu'elle a été assignée par le gestionnaire de ressources YARN.

Les journaux agrégés ne sont pas lisibles directement, car ils sont écrits dans un [TFile][T-file], un [format binaire][binary-format] indexé par conteneur. YARN fournit des outils CLI pour vider ces journaux au format texte brut pour les applications ou les conteneurs qui présentent un intérêt. Vous pouvez afficher ces journaux au format texte brut en exécutant l'une des commandes YARN suivantes directement sur les nœuds de cluster (après avoir établi une connexion via RDP) :

	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address> 

La section suivante examine comment accéder par programmation à des journaux spécifiques à des applications ou à des conteneurs, sans recourir à RDP pour accéder à vos clusters HDInsight.

## <a name="enumerate-and-download"></a>Énumération d'applications et téléchargement de journaux par programmation

Pour utiliser les exemples de code suivants, vous devez télécharger la dernière version du SDK .NET HDInsight pour répondre à la configuration requise indiquée ci-dessus. Consultez les instructions fournies.

Le code ci-dessous montre comment utiliser les nouvelles API pour énumérer les applications et télécharger les journaux des applications terminées.

> [AZURE.NOTE] Les API ci-dessous ne fonctionnent que sur les clusters Hadoop " en cours d'exécution " avec la version 3.1.1.374 ou une version ultérieure. Ajoutez les directives suivantes.

	using Microsoft.Hadoop.Client;
	using Microsoft.WindowsAzure.Management.HDInsight;

Celles-ci font référence aux API nouvellement définies dans le code ci-dessous. L'extrait de code ci-dessus crée un client de l'historique des applications sur votre cluster " en cours d'exécution " dans votre abonnement.

	string subscriptionId = "<your-subscription-id>";
	string clusterName = "<your-cluster-name>";
	string certName = "<your-subscription-management-cert-name>";
	
	// Create an HDInsight Client
	X509Store store = new X509Store(StoreName.My, StoreLocation.LocalMachine);
	store.Open(OpenFlags.ReadOnly);
	X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>()
	                            .Single(x => x.FriendlyName == certName);
	
	HDInsightCertificateCredential creds = 
				new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
	
	IHDInsightClient client = HDInsightClient.Connect(creds);
	
	// Get the cluster on which your applications were run
	// The cluster needs to be in "Running" state
	ClusterDetails cluster = client.GetCluster(clusterName);
	
	// Create an Application History client against your cluster
	IHDInsightApplicationHistoryClient appHistoryClient = 
				cluster.CreateHDInsightApplicationHistoryClient(TimeSpan.FromMinutes(5));


 Vous pouvez à présent utiliser le client de l'historique des applications pour répertorier les applications terminées, filtrer les applications basées sur vos critères et télécharger les journaux des applications appropriés. L'extrait de code suivant montre comment procéder par programmation.

	// Local download folder location where the logs will be placed
	string downloadLocation = "E:\\YarnApplicationLogs";
	
	// List completed applications on your cluster that were submitted in the last 24 hours but failed
	// Search for applications based on application name
	string appNamePrefix = "your-app-name-prefix";
	DateTime endTime = DateTime.UtcNow;
	DateTime startTime = endTime.AddHours(-24);
	IEnumerable<ApplicationDetails> applications = appHistoryClient
	                .ListCompletedApplications(startTime, endTime)
	                .Where(app => 
	                    app.GetApplicationFinalStatusAsEnum() == ApplicationFinalStatus.Failed 
	                    && app.Name.StartsWith(appNamePrefix));
	
	// Download logs for failed or killed applications
	// This will generate one log file for each application
	foreach (ApplicationDetails application in applications)
	{
	    appHistoryClient.DownloadApplicationLogs(application, downloadLocation);
	}

Le code ci-dessus répertorie/trouve les applications qui vous intéressent à l'aide du client de l'historique des applications, puis télécharge les journaux de ces applications dans un dossier local. 

L'extrait de code ci-dessous télécharge également les journaux d'une application dont l'identificateur unique (" ApplicationId ") est connu. ApplicationId est l'identificateur global unique d'une application qui est assigné par le gestionnaire de ressources. Il est construit à l'aide de l'heure de début du gestionnaire de ressources et d'un compteur croissant monotone pour les applications envoyées au gestionnaire de ressources. ApplicationId se présente sous la forme " application\_&lt;RM-start-time&gt;\_&lt;Counter&gt; ". Notez que les identificateurs ApplicationId et JobId sont distincts. JobId est un concept MapReduce spécifique à l'infrastructure, tandis qu'ApplicationId est un concept YARN indépendant de l'infrastructure. Dans YARN, JobId identifie une tâche MapReduce spécifique comme étant gérée par le maître d'application d'une application MapReduce envoyée au gestionnaire de ressources.

	// Download application logs for an application whose application Id is known
	string applicationId = "application_1416017767088_0028";
	ApplicationDetails someApplication = appHistoryClient.GetApplicationDetails(applicationId);
	appHistoryClient.DownloadApplicationLogs(someApplication, downloadLocation);

Si nécessaire, vous pouvez également télécharger des journaux pour chaque conteneur (ou pour tout conteneur spécifique) utilisé par une application, comme illustré ci-dessous.

	ApplicationDetails someApplication = appHistoryClient.GetApplicationDetails(applicationId);
	
	// Download logs separately for each container of application(s) of interest
	// This will generate one log file per container
	IEnumerable<ApplicationAttemptDetails> applicationAttempts =
				appHistoryClient.ListApplicationAttempts(someApplication);
	
	ApplicationAttemptDetails finalAttempt = applicationAttempts
	    		.Single(x => x.ApplicationAttemptId == someApplication.LatestApplicationAttemptId);
	
	IEnumerable<ApplicationContainerDetails> containers =
				appHistoryClient.ListApplicationContainers(finalAttempt);
	
	foreach (ApplicationContainerDetails container in containers)
	{
	    appHistoryClient.DownloadApplicationLogs(container, downloadLocation);
	}



[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/browse/HADOOP-3315
[binary-format]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf 
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/

<!--HONumber=42-->
