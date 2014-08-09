<properties linkid="manage-services-hdinsight-use-Ambari" urlDisplayName="Monitor HDInsight clusters using the Ambari API" pageTitle="Monitor HDInsight clusters using the Ambari API | Azure" metaKeywords="" description="Use the Apache Ambari APIs for provisioning, managing, and monitoring Hadoop clusters. Ambari's intuitive operator tools and APIs hide the complexity of Hadoop." services="hdinsight" documentationCenter="" title="Monitor HDInsight clusters using the Ambari API" umbracoNaviHide="0" disqusComments="1" authors="jgao" editor="cgronlun" manager="paulettm" />

Surveillance des clusters HDInsight à l'aide des API Ambari
===========================================================

Découvrez comment surveiller les clusters HDInsight version 2.1 à l'aide des API Ambari.

**Durée de réalisation estimée :** 15 minutes

Dans cet article
----------------

-   [Présentation d'Ambari](#whatisambari)
-   [Conditions préalables](#prerequisites)
-   [Démarrage rapide](#jumpstart)
-   [API de surveillance Ambari](#monitor)
-   [Étapes suivantes](#nextsteps)

Présentation d'Ambari
---------------------

[Apache Ambari](http://ambari.apache.org/) est destiné à l'approvisionnement, la gestion et la surveillance des clusters Apache Hadoop. Il comprend une collection intuitive d'outils d'opérateurs et un solide jeu d'API qui masque la complexité de Hadoop, en simplifiant le fonctionnement des clusters. Pour plus d'informations sur les API, consultez la page [Référence des API Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

HDInsight prend actuellement en charge la fonctionnalité de surveillance Ambari uniquement. La version 1.0 de l'API Ambari est prise en charge par le cluster HDInsight versions 2.1 et 3.0. Cet article aborde uniquement l'exécution des API Ambari sur le cluster HDInsight version 2.1.

Conditions préalables
---------------------

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

-   Un **poste de travail** sur lequel Azure PowerShell est installé et configuré. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell](../install-configure-powershell/). Pour exécuter des scripts PowerShell, vous devez exécuter Azure PowerShell en tant qu'administrateur et définir la stratégie d'exécution sur *RemoteSigned*. Consultez la page [Exécution de scripts Windows PowerShell](http://technet.microsoft.com/en-us/library/ee176949.aspx).

    [Curl](http://curl.haxx.se) est facultatif. Vous pouvez l'installer à partir d'[ici](http://curl.haxx.se/download.html).

    > [WACOM.NOTE] Lorsque vous utilisez la commande curl sur Windows, remplacez les guillemets doubles par des guillemets simples pour exprimer la valeur des options.

-   Un **cluster Azure HDInsight**. Pour obtenir des instructions sur l'approvisionnement des clusters, consultez les rubriques [Prise en main de HDInsight](../hdinsight-get-started/) ou [Approvisionnement de clusters HDInsight](../hdinsight-provision-clusters/). Vous aurez besoin des données suivantes pour suivre ce didacticiel :

    <table data-morhtml="true" border="1">
  <tr data-morhtml="true"><th data-morhtml="true">Propri&eacute;t&eacute; du cluster</th><th data-morhtml="true">Nom de la variable PowerShell</th><th data-morhtml="true">Valeur</th><th data-morhtml="true">Description</th></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nom du cluster HDInsight</td><td data-morhtml="true">$clusterName</td><td data-morhtml="true"></td><td data-morhtml="true">Nom de votre cluster HDInsight</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nom d'utilisateur du cluster</td><td data-morhtml="true">$clusterUsername</td><td data-morhtml="true"></td><td data-morhtml="true">Nom d'utilisateur du cluster sp&eacute;cifi&eacute; lors de l'approvisionnement</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Mot de passe du cluster</td><td data-morhtml="true">$clusterPassword</td><td data-morhtml="true"></td><td data-morhtml="true">Mot de passe utilisateur du cluster</td></tr>
  </table>

    > [WACOM.NOTE] Renseignez ces tableaux. Cela vous sera utile pour ce didacticiel.

Démarrage rapide
----------------

Il existe plusieurs façons d'utiliser Ambari pour surveiller les clusters HDInsight.

**Utilisation d'Azure PowerShell**

Voici un script PowerShell permettant d'obtenir les informations de suivi de tâche MapReduce :

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

![Sortie du jobtracker](./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png)

**Utilisation de curl**

L'exemple suivant permet d'obtenir des informations de cluster à l'aide de Curl :

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
                 "host_name":"workernode0"}}]}

API de surveillance Ambari
--------------------------

Le tableau suivant répertorie certains des appels d'API de surveillance Ambari les plus connus. Pour plus d'informations sur les API, consultez la page [Référence des API Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">Appel d'API de surveillance</th><th data-morhtml="true">URI</th><th data-morhtml="true">Description</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">Obtenir des clusters</td><td data-morhtml="true">/api/v1/clusters</td><td data-morhtml="true"></td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Obtenir des infos sur les clusters</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net</td><td data-morhtml="true">clusters, services, h&ocirc;tes</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Obtenir des services</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services</td><td data-morhtml="true">Les services incluent&nbsp;: hdfs, mapreduce</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Obtenir des infos sur les services</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;</td><td data-morhtml="true"></td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Obtenir des composants de service</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components</td><td data-morhtml="true">HDFS&nbsp;: namenode, datanode<br data-morhtml="true" />MapReduce&nbsp;: jobtracker&nbsp;; tasktracker</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Obtenir des infos sur les composants</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components/&lt;ComponentName&gt;</td><td data-morhtml="true">ServiceComponentInfo, host-components, mesures</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Obtenir des h&ocirc;tes</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts</td><td data-morhtml="true">headnode0, workernode0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Obtenir des infos sur les h&ocirc;tes</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt; 
</td><td data-morhtml="true"></td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Obtenir des composants d'h&ocirc;te</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components
</td><td data-morhtml="true">namenode, resourcemanager</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Obtenir des informations sur les composants d'h&ocirc;te</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components/&lt;ComponentName&gt;
</td><td data-morhtml="true">HostRoles, composant, h&ocirc;te, mesure</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Obtenir des configurations</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations 
</td><td data-morhtml="true">Types de configuration&nbsp;: core-site, hdfs-site, mapred-site, hive-site</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Obtenir des infos sur la configuration</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations?type=&lt;ConfigType&gt;&amp;tag=&lt;VersionName&gt; 
</td><td data-morhtml="true">Types de configuration&nbsp;: core-site, hdfs-site, mapred-site, hive-site</td></tr>
</table>
Étapes suivantes
----------------

Vous avez appris à utiliser les appels d'API de surveillance Ambari. Pour plus d'informations, consultez les rubriques suivantes :

-   [Administration de clusters HDInsight à l'aide du portail de gestion](../hdinsight-administer-use-management-portal/)
-   [Administration de clusters HDInsight à l'aide d'Azure PowerShell](../hdinsight-administer-use-powershell/)
-   [Administration de clusters HDInsight à l'aide de l'interface de ligne de commande](../hdinsight-administer-use-command-line/)
-   [Documentation HDInsight](/fr-fr/documentation/services/hdinsight/)
-   [Prise en main de HDInsight](../hdinsight-get-started/)

