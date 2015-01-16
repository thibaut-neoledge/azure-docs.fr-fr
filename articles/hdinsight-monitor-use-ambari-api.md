<properties urlDisplayName="Monitor Hadoop clusters  in HDInsight using the Ambari API" pageTitle="Surveillance des clusters Hadoop dans HDInsight à l'aide des API Ambari | Azure" metaKeywords="" description="Utilisez les API Apache Ambari pour l'approvisionnement, la gestion et la surveillance des clusters Hadoop. Les outils intuitifs pour opérateurs et les API d'Ambari masquent la complexité de Hadoop." services="hdinsight" documentationCenter="" title="Monitor Hadoop clusters in HDInsight using the Ambari API" umbracoNaviHide="0" disqusComments="1" authors="jgao" editor="cgronlun" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="jgao" />

# Surveillance des clusters Hadoop dans HDInsight à l'aide des API Ambari
 
Découvrez comment surveiller les clusters HDInsight versions 3.1 et 2.1 à l'aide des API Ambari.

##Dans cet article

- [Présentation d'Ambari](#whatisambari)
- [Configuration requise](#prerequisites)
- [Démarrage rapide](#jumpstart)
- [API de surveillance Ambari](#monitor)
- [Étapes suivantes](#nextsteps)


## <a id="whatisambari"></a> Présentation d'Ambari

[Apache Ambari][ambari-home] est destiné à l'approvisionnement, à la gestion et à la surveillance des clusters Apache Hadoop. Il comprend une collection intuitive d'outils d'opérateurs et un solide jeu d'API qui masque la complexité de Hadoop, en simplifiant le fonctionnement des clusters. Pour plus d'informations sur les API, consultez la page [Référence des API Ambari][ambari-api-reference].


HDInsight prend actuellement en charge la fonctionnalité de surveillance Ambari uniquement. La version 1.0 de l'API Ambari est prise en charge par le cluster HDInsight versions 2.1 et 3.0.  Cet article présente l'accès aux API Ambari sur les versions de clusters HDInsight 3.1 et 2.1.  La différence principale entre les deux est que certains des composants ont changé avec l'introduction des nouvelles fonctionnalités (telles que le serveur d'historique des tâches).


##<a id="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un poste de travail sur lequel **Azure PowerShell est installé et configuré. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][powershell-install]. Pour exécuter des scripts PowerShell, vous devez exécuter Azure PowerShell en tant qu'administrateur et définir la stratégie d'exécution sur *RemoteSigned*. Consultez la page [Exécution de scripts Windows PowerShell][powershell-script].

	[Curl][curl] est facultatif. Vous pouvez l'installer à partir d'[ici][curl-download].

	>[WACOM.NOTE] Lorsque vous utilisez la commande curl sur Windows, remplacez les guillemets doubles par des guillemets simples pour exprimer la valeur des options.

- **Un cluster Azure HDInsight**. Pour obtenir des instructions sur l'approvisionnement des clusters, consultez la rubrique [Prise en main de HDInsight][hdinsight-get-started] ou [Approvisionnement de clusters HDInsight][hdinsight-provision]. Vous aurez besoin des données suivantes pour suivre ce didacticiel :

	<table border="1">
	<tr><th>Propriété du cluster</th><th>Nom de la variable PowerShell</th><th>Valeur</th><th>Description</th></tr>
	<tr><td>Nom du cluster HDInsight</td><td>$clusterName</td><td></td><td>Nom de votre cluster HDInsight</td></tr>
	<tr><td>Nom d'utilisateur du cluster</td><td>$clusterUsername</td><td></td><td>Nom d'utilisateur du cluster spécifié lors de l'approvisionnement</td></tr>
	<tr><td>Mot de passe du cluster</td><td>$clusterPassword</td><td></td><td>Mot de passe utilisateur du cluster</td></tr>
	</table>

	> [WACOM.NOTE] Renseignez ces tableaux.  Cela vous sera utile pour ce didacticiel.



##<a id="jumpstart"></a>Démarrage rapide

Il existe plusieurs façons d'utiliser Ambari pour surveiller les clusters HDInsight.

**Utilisation d'Azure PowerShell**

Voici un script PowerShell permettant d'obtenir les informations de suivi de tâche MapReduce *sur un cluster 3.1*.  La différence principale réside dans le fait que nous allons à présent extraire ces détails à partir du service YARN (plutôt que Map Reduce).

	$clusterName = "<HDInsightClusterName>"
	$clusterUsername = "<HDInsightClusterUsername>"
	$clusterPassword = "<HDInsightClusterPassword>"
	
	$ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
	$uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"
	
	$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
	$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)
	
	$response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 
	
	$response.metrics.'yarn.queueMetrics'

Voici un script PowerShell permettant d'obtenir les informations de suivi de tâche MapReduce *sur un cluster 2.1* :

	$clusterName = "<HDInsightClusterName>"
	$clusterUsername = "<HDInsightClusterUsername>"
	$clusterPassword = "<HDInsightClusterPassword>"
	
	$ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
	$uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"
	
	$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
	$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)
	
	$response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 
	
	$response.metrics.'mapred.JobTracker'

La sortie est la suivante :

![Jobtracker Output][img-jobtracker-output]

**Utilisation de curl**

L'exemple suivant permet d'obtenir des informations de cluster à l'aide de Curl :

	curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

La sortie est la suivante :
	
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

Note pour la version du 08/10/2014 :
Lors de l'utilisation du point de terminaison Ambari, " https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname} ", le champ *host_name* renvoie à présent le nom de domaine complet du nœud au lieu du nom d'hôte. Avant la version du 08/10/2014, cet exemple renvoyait simplement **headnode0**. Après le 08/10/2014, vous obtenez le nom de domaine complet **headnode0.{ClusterDNS}.azurehdinsight.net** comme illustré dans l'exemple ci-dessus. Cette modification était nécessaire pour permettre les scénarios dans lesquels plusieurs types de cluster tels que HBase et Hadoop sont déployés dans un réseau virtuel (VNET). Cela se produit, par exemple, lors de l'utilisation de HBase en tant que plateforme principale pour Hadoop.

##<a id="monitor"></a>API de surveillance Ambari

Le tableau suivant répertorie certains des appels d'API de surveillance Ambari les plus connus. Pour plus d'informations sur l'API, consultez la page [Référence des API Ambari][ambari-api-reference].

<table border="1">
<tr><th>Appel d'API de surveillance</th><th>URI</th><th>Description</th></tr>
<tr><td>Obtenir des clusters</td><td><tt>/api/v1/clusters</tt></td><td></td></tr>
<tr><td>Obtenir des infos sur les clusters</td><td><tt>/api/v1/clusters/&lt;Nom_cluster&gt;.azurehdinsight.net</tt></td><td>clusters, services, hôtes</td></tr>
<tr><td>Obtenir des services</td><td><tt>/api/v1/clusters/&lt;Nom_cluster&gt;.azurehdinsight.net/services</tt></td><td>Les services incluent : hdfs, mapreduce</td></tr>
<tr><td>Obtenir des infos sur les services</td><td><tt>/api/v1/clusters/&lt;Nom_cluster&gt;.azurehdinsight.net/services/&lt;Nom_service&gt;</tt></td><td></td></tr>
<tr><td>Obtenir des composants de service</td><td><tt>/api/v1/clusters/&lt;Nom_cluster&gt;.azurehdinsight.net/services/&lt;Nom_service&gt;/components</tt></td><td>HDFS : namenode, datanode<br/>MapReduce : jobtracker ; tasktracker</td></tr>
<tr><td>Obtenir des infos sur les composants</td><td><tt>/api/v1/clusters/&lt;Nom_cluster&gt;.azurehdinsight.net/services/&lt;Nom_service&gt;/components/&lt;Nom_composant&gt;</tt></td><td>ServiceComponentInfo, host-components, mesures</td></tr>
<tr><td>Obtenir des hôtes</td><td><tt>/api/v1/clusters/&lt;Nom_cluster&gt;.azurehdinsight.net/hosts</tt></td><td>headnode0, workernode0</td></tr>
<tr><td>Obtenir des infos sur les hôtes</td><td><tt>/api/v1/clusters/&lt;Nom_cluster&gt;.azurehdinsight.net/hosts/&lt;Nom_hôte&gt; 
</td><td></td></tr>
<tr><td>Obtenir des composants d'hôte</td><td><tt>/api/v1/clusters/&lt;Nom_cluster&gt;.azurehdinsight.net/hosts/&lt;Nom_hôte&gt;/host_components
</tt></td><td>namenode, resourcemanager</td></tr>
<tr><td>Obtenir des informations sur les composants d'hôte</td><td><tt>/api/v1/clusters/&lt;Nom_cluster&gt;.azurehdinsight.net/hosts/&lt;Nom_hôte&gt;/host_components/&lt;Nom_composant&gt;
</tt></td><td>HostRoles, composant, hôte, mesure</td></tr>
<tr><td>Obtenir des configurations</td><td><tt>/api/v1/clusters/&lt;Nom_cluster&gt;.azurehdinsight.net/configurations 
</tt></td><td>Types de configuration : core-site, hdfs-site, mapred-site, hive-site</td></tr>
<tr><td>Obtenir des infos sur la configuration</td><td><tt>/api/v1/clusters/&lt;Nom_cluster&gt;.azurehdinsight.net/configurations?type=&lt;Type_config&gt;&tag=&lt;Nom_version&gt; 
</tt></td><td>Types de configuration : core-site, hdfs-site, mapred-site, hive-site</td></tr>
</table>


##<a id="nextsteps"></a>Étapes suivantes 

Vous avez appris à utiliser les appels d'API de surveillance Ambari. Pour plus d'informations, consultez les rubriques suivantes :

- [Administration de clusters HDInsight à l'aide du portail de gestion][hdinsight-admin-portal]
- [Administration de clusters HDInsight à l'aide d'Azure PowerShell][hdinsight-admin-powershell]
- [Administration de clusters HDInsight à l'aide de l'interface de ligne de commande][hdinsight-admin-cli]
- [Documentation HDInsight][hdinsight-documentation]
- [Prise en main de HDInsight][hdinsight-get-started]



[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[Powershell-install]: ../install-configure-powershell/
[Powershell-script]: http://technet.microsoft.com/fr-fr/library/ee176949.aspx 

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hdinsight-admin-cli]: ../hdinsight-administer-use-command-line/
[hdinsight-documentation]: /fr-fr/documentation/services/hdinsight/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-provision]: ../hdinsight-provision-clusters/

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png


<!--HONumber=35.1-->
