<properties
	pageTitle="Surveillance des clusters Hadoop dans HDInsight à l’aide des API Ambari | Microsoft Azure"
	description="Utilisez les API Apache Ambari pour l'approvisionnement, la gestion et la surveillance des clusters Hadoop. Les outils intuitifs pour opérateurs et les API masquent la complexité de Hadoop."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	editor="cgronlun"
	manager="paulettm"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/08/2015"
	ms.author="jgao"/>

# Surveillance des clusters Hadoop dans HDInsight à l'aide des API Ambari

Découvrez comment surveiller les clusters HDInsight versions 3.1 et 2.1 à l’aide des API Ambari.

> [AZURE.NOTE]Les informations contenues dans cet article sont adaptées principalement aux clusters HDInsight basés sur Windows, qui fournissent une version en lecture seule de l’API REST Ambari. Pour les clusters basés sur Linux, consultez la page [Gestion des clusters HDInsight à l’aide d’Ambari](hdinsight-hadoop-manage-ambari.md).

## <a id="whatisambari"></a> Présentation d’Ambari

[Apache Ambari][ambari-home] est destiné à l’approvisionnement, à la gestion et à la surveillance des clusters Apache Hadoop. Il comprend une collection intuitive d'outils d'opérateurs et un solide jeu d'API qui masque la complexité de Hadoop, en simplifiant le fonctionnement des clusters. Pour plus d’informations sur les API, consultez la page [Référence des API Ambari][ambari-api-reference].


HDInsight prend actuellement en charge la fonctionnalité de surveillance Ambari uniquement. La version 1.0 de l’API Ambari est prise en charge par les clusters HDInsight versions 2.1 et 3.0. Cet article présente l’accès aux API Ambari sur les versions de clusters HDInsight 3.1 et 2.1. La différence principale entre les deux est que certains des composants ont changé avec l'introduction des nouvelles fonctionnalités (telles que le serveur d'historique des tâches).


##<a id="prerequisites"></a>Configuration requise

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez la page [Installation et utilisation d’Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).


- [cURL][curl] est facultatif. Pour l’installer, consultez [Versions et téléchargements de cURL][curl-download].

	>[AZURE.NOTE]Lorsque vous utilisez la commande cURL sous Windows, remplacez les guillemets simples par des guillemets doubles pour exprimer la valeur des options.

- Un **cluster Azure HDInsight**. Pour obtenir des instructions sur l’approvisionnement des clusters, consultez la rubrique [Prise en main de HDInsight][hdinsight-get-started] ou [Approvisionnement de clusters HDInsight][hdinsight-provision]. Vous aurez besoin des données suivantes pour suivre ce didacticiel :

	<table border="1">
<tr><th>Propriété du cluster</th><th>Nom de la variable Azure&#160;PowerShell</th><th>Valeur</th><th>Description</th></tr>
<tr><td>Nom du cluster HDInsight</td><td>$clusterName</td><td></td><td>Nom de votre cluster HDInsight</td></tr>
<tr><td>Nom d'utilisateur du cluster</td><td>$clusterUsername</td><td></td><td>Nom d’utilisateur du cluster spécifié lors de l’approvisionnement</td></tr>
<tr><td>Mot de passe du cluster</td><td>$clusterPassword</td><td></td><td>Mot de passe utilisateur du cluster</td></tr>
</table>> [AZURE.NOTE]Renseignez les valeurs dans le tableau. Cela vous sera utile pour ce didacticiel.



##<a id="jumpstart"></a>Démarrage rapide

Il existe plusieurs façons d'utiliser Ambari pour surveiller les clusters HDInsight.

**Utilisation d’Azure PowerShell**

Voici un script Azure PowerShell permettant d’obtenir des informations de suivi des tâches MapReduce *dans un cluster HDInsight 3.1.* La différence principale réside dans le fait que nous allons extraire ces détails à partir du service YARN (plutôt que Map Reduce).

	$clusterName = "<HDInsightClusterName>"
	$clusterUsername = "<HDInsightClusterUsername>"
	$clusterPassword = "<HDInsightClusterPassword>"

	$ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
	$uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"

	$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
	$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

	$response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

	$response.metrics.'yarn.queueMetrics'

Voici un script Azure PowerShell permettant d’obtenir les informations de suivi de tâche MapReduce *dans un cluster HDInsight 2.1.* :

	$clusterName = "<HDInsightClusterName>"
	$clusterUsername = "<HDInsightClusterUsername>"
	$clusterPassword = "<HDInsightClusterPassword>"

	$ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
	$uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

	$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
	$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

	$response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

	$response.metrics.'mapred.JobTracker'

La sortie est la suivante :

![Sortie du jobtracker][img-jobtracker-output]

**Utilisation de curl**

L’exemple suivant permet d’obtenir des informations de cluster à l’aide de cURL :

	curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

La sortie est la suivante :

	{"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
	 "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
	 "services"[
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
	    "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
	    "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
	 "hosts":[
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
	    "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
	             "host_name":"headnode0"}},
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
	    "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
	             "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**Pour la version du 8/10/2014** :

Lors de l’utilisation du point de terminaison Ambari, « https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname} », le champ *host_name* renvoie le nom de domaine complet du nœud au lieu du nom d’hôte. Avant la version du 8/10/2014, cet exemple renvoyait simplement « **headnode0** ». Après la version du 8/10/2014, vous obtenez le nom de domaine complet « **headnode0.{ClusterDNS}.azurehdinsight.net** », comme illustré dans l’exemple précédent. Cette modification était nécessaire pour permettre les scénarios dans lesquels plusieurs types de cluster tels que HBase et Hadoop sont déployés dans un réseau virtuel (VNET). Cela se produit, par exemple, lors de l'utilisation de HBase en tant que plateforme principale pour Hadoop.

##<a id="monitor"></a>API de surveillance Ambari

Le tableau suivant répertorie certains des appels d'API de surveillance Ambari les plus connus. Pour plus d’informations sur l’API, consultez la page [Référence des API Ambari][ambari-api-reference].

<table border="1">
<tr><th>Appel d'API de surveillance</th><th>URI</th><th>Description</th></tr>
<tr><td>Obtenir des clusters</td><td><tt>/api/v1/clusters</tt></td><td></td></tr>
<tr><td>Obtenir des infos sur les clusters</td><td><tt>/api/v1/clusters/&lt;Nom_cluster>.azurehdinsight.net</tt></td><td>clusters, services, hôtes</td></tr>
<tr><td>Obtenir des services</td><td><tt>/api/v1/clusters/&lt;Nom_cluster>.azurehdinsight.net/services</tt></td><td>Les services incluent&#160;: hdfs, mapreduce</td></tr>
<tr><td>Obtenir des infos sur les services</td><td><tt>/api/v1/clusters/&lt;Nom_cluster>.azurehdinsight.net/services/&lt;Nom_service></tt></td><td></td></tr>
<tr><td>Obtenir des composants de service</td><td><tt>/api/v1/clusters/&lt;Nom_cluster>.azurehdinsight.net/services/&lt;Nom_service>/components</tt></td><td>HDFS&#160;: namenode, datanode<br/>MapReduce&#160;: jobtracker&#160;; tasktracker</td></tr>
<tr><td>Obtenir des infos sur les composants</td><td><tt>/api/v1/clusters/&lt;Nom_cluster>.azurehdinsight.net/services/&lt;Nom_service>/components/&lt;Nom_composant></tt></td><td>ServiceComponentInfo, host-components, mesures</td></tr>
<tr><td>Obtenir des hôtes</td><td><tt>/api/v1/clusters/&lt;Nom_cluster>.azurehdinsight.net/hosts</tt></td><td>headnode0, workernode0</td></tr>
<tr><td>Obtenir des infos sur les hôtes</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/hosts/&lt;HostName>
</td><td></td></tr>
<tr><td>Obtenir des composants d'hôte</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/hosts/&lt;HostName>/host_components </tt></td><td>namenode, resourcemanager</td></tr>
<tr><td>Obtenir des informations sur les composants d'hôte</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/hosts/&lt;HostName>/host_components/&lt;ComponentName> </tt></td><td>HostRoles, composant, hôte, mesure</td></tr>
<tr><td>Obtenir des configurations</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/configurations </tt></td><td>Types de configurations&#160;: core-site, hdfs-site, mapred-site, hive-site</td></tr>
<tr><td>Obtenir des infos sur la configuration</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/configurations?type=&lt;ConfigType>&amp;tag=&lt;VersionName> </tt></td><td>Types de configurations&#160;: core-site, hdfs-site, mapred-site, hive-site</td></tr>
</table>


##<a id="nextsteps"></a>Étapes suivantes

Vous avez appris à utiliser les appels d'API de surveillance Ambari. Pour plus d'informations, consultez les rubriques suivantes :

- [Gestion des clusters HDInsight au moyen du portail de gestion Azure][hdinsight-admin-portal]
- [Gestion des clusters HDInsight à l’aide d’Azure PowerShell][hdinsight-admin-powershell]
- [Gestion de clusters HDInsight à l’aide de l’interface de ligne de commande][hdinsight-admin-cli]
- [Documentation HDInsight][hdinsight-documentation]
- [Prise en main de HDInsight][hdinsight-get-started]



[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: ../install-configure-powershell.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: /documentation/services/hdinsight/
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
 

<!---HONumber=62-->