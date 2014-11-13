<properties urlDisplayName="Monitor Hadoop clusters  in HDInsight using the Ambari API" pageTitle="Surveillance des clusters Hadoop dans HDInsight &agrave; l'aide des API Ambari | Azure" metaKeywords="" description="Utilisez les API Apache Ambari pour l'approvisionnement, la gestion et la surveillance des clusters Hadoop. Les outils intuitifs pour op&eacute;rateurs et les API d'Ambari masquent la complexit&eacute; de Hadoop." services="hdinsight" documentationCenter="" title="Surveillance des clusters Hadoop dans HDInsight &agrave; l'aide des API Ambari" umbracoNaviHide="0" disqusComments="1" authors="jgao" editor="cgronlun" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Surveillance des clusters Hadoop dans HDInsight à l'aide des API Ambari

Découvrez comment surveiller les clusters HDInsight versions 3.1 et 2.1 à l'aide des API Ambari.

**Durée de réalisation estimée :** 15 minutes

## Dans cet article

-   [Présentation d'Ambari][Présentation d'Ambari]
-   [Configuration requise][Configuration requise]
-   [Démarrage rapide][Démarrage rapide]
-   [API de surveillance Ambari][API de surveillance Ambari]
-   [Étapes suivantes][Étapes suivantes]

## <span id="whatisambari"></span></a> Présentation d'Ambari

[Apache Ambari][Apache Ambari] est destiné à l'approvisionnement, la gestion et la surveillance des clusters Apache Hadoop. Il comprend une collection intuitive d'outils d'opérateurs et un solide jeu d'API qui masque la complexité de Hadoop, en simplifiant le fonctionnement des clusters. Pour plus d'informations sur les API, consultez la page [Référence des API Ambari][Référence des API Ambari].

HDInsight prend actuellement en charge la fonctionnalité de surveillance Ambari uniquement. La version 1.0 de l'API Ambari est prise en charge par le cluster HDInsight versions 2.1 et 3.0. Cet article présente l'accès aux API Ambari sur les versions de clusters HDInsight 3.1 et 2.1. La différence principale entre les deux est que certains des composants ont changé avec l'introduction des nouvelles fonctionnalités (telles que le serveur d'historique des tâches).

## <span id="prerequisites"></span></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

-   Une **station de travail** sur laquelle Azure PowerShell est installé et configuré. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][Installation et configuration d'Azure PowerShell]. Pour exécuter des scripts PowerShell, vous devez exécuter Azure PowerShell en tant qu'administrateur et définir la stratégie d'exécution sur *RemoteSigned*. Consultez la page [Exécution de scripts Windows PowerShell][Exécution de scripts Windows PowerShell].

    [Curl][Curl] est facultatif. Vous pouvez l'installer à partir d'[ici][ici].

    > [WACOM.NOTE] Lorsque vous utilisez la commande curl sur Windows, remplacez les guillemets doubles par des guillemets simples pour exprimer la valeur des options.

-   Un **cluster Azure HDInsight**. Pour obtenir des instructions sur l'approvisionnement des clusters, consultez les rubriques [Prise en main de HDInsight][Prise en main de HDInsight] ou [Approvisionnement de clusters HDInsight][Approvisionnement de clusters HDInsight]. Vous aurez besoin des données suivantes pour suivre ce didacticiel :

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Propriété du cluster</th>
    <th align="left">Nom de la variable PowerShell</th>
    <th align="left">Valeur</th>
    <th align="left">Description</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Nom du cluster HDInsight</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">Nom de votre cluster HDInsight</td>
    </tr>
    <tr class="even">
    <td align="left">Nom d'utilisateur du cluster</td>
    <td align="left">$clusterUsername</td>
    <td align="left"></td>
    <td align="left">Nom d'utilisateur du cluster spécifié lors de l'approvisionnement</td>
    </tr>
    <tr class="odd">
    <td align="left">Mot de passe du cluster</td>
    <td align="left">$clusterPassword</td>
    <td align="left"></td>
    <td align="left">Mot de passe utilisateur du cluster</td>
    </tr>
    </tbody>
    </table>

    > [WACOM.NOTE] Renseignez ces tableaux. Cela vous sera utile pour ce didacticiel.

## <span id="jumpstart"></span></a>Démarrage rapide

Il existe plusieurs façons d'utiliser Ambari pour surveiller les clusters HDInsight.

**Utilisation d'Azure PowerShell**

Voici un script PowerShell permettant d'obtenir les informations de suivi de tâche MapReduce *sur un cluster 3.1.* La différence principale réside dans le fait que nous allons à présent extraire ces détails à partir du service YARN (plutôt que Map Reduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 

    $response.metrics.'yarn.queueMetrics'

Voici un script PowerShell permettant d'obtenir les informations de suivi de tâche MapReduce *sur un cluster 2.1* :

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

![Sortie du jobtracker][Sortie du jobtracker]

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
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

Note pour la version du 08/10/2014 :
Lors de l'utilisation du point de terminaison Ambari, https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}, le champ *host\_name* renvoie à présent le nom de domaine complet du nœud au lieu du nom d'hôte. Avant la version du 08/10/2014, cet exemple renvoyait simplement **headnode0**. Après le 08/10/2014, vous obtenez le nom de domaine complet **headnode0.{ClusterDNS}.azurehdinsight.net** comme illustré dans l'exemple ci-dessus. Cette modification était nécessaire pour permettre les scénarios dans lesquels plusieurs types de cluster tels que HBase et Hadoop sont déployés dans un réseau virtuel (VNET). Cela se produit, par exemple, lors de l'utilisation de HBase en tant que plateforme principale pour Hadoop.

## <span id="monitor"></span></a>API de surveillance Ambari

Le tableau suivant répertorie certains des appels d'API de surveillance Ambari les plus connus. Pour plus d'informations sur les API, consultez la page [Référence des API Ambari][Référence des API Ambari].

<table border="1">
<tr>
<th>
Appel d'API de surveillance

</th>
<th>
URI

</th>
<th>
Description

</th>
</tr>
<tr>
<td>
Obtenir des clusters

</td>
<td>
`/api/v1/clusters`

</td>
<td>
</td>
</tr>
<tr>
<td>
Obtenir des infos sur les clusters

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net`

</td>
<td>
clusters, services, hôtes

</td>
</tr>
<tr>
<td>
Obtenir des services

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services`

</td>
<td>
Les services incluent : hdfs, mapreduce

</td>
</tr>
<tr>
<td>
Obtenir des infos sur les services

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>`

</td>
<td>
</td>
</tr>
<tr>
<td>
Obtenir des composants de service

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components`

</td>
<td>
HDFS : namenode, datanode
MapReduce : jobtracker ; tasktracker

</td>
</tr>
<tr>
<td>
Obtenir des infos sur les composants

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>`

</td>
<td>
ServiceComponentInfo, host-components, mesures

</td>
</tr>
<tr>
<td>
Obtenir des hôtes

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts`

</td>
<td>
headnode0, workernode0

</td>
</tr>
<tr>
<td>
Obtenir des infos sur les hôtes

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName> `

<td>
</td>
</tr>
<tr>
<td>
Obtenir des composants d'hôte

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components`

</td>
<td>
namenode, resourcemanager

</td>
</tr>
<tr>
<td>
Obtenir des informations sur les composants d'hôte

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>`

</td>
<td>
HostRoles, composant, hôte, mesure

</td>
</tr>
<tr>
<td>
Obtenir des configurations

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations `

</td>
<td>
Types de configuration : core-site, hdfs-site, mapred-site, hive-site

</td>
</tr>
<tr>
<td>
Obtenir des infos sur la configuration

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName> `

</td>
<td>
Types de configuration : core-site, hdfs-site, mapred-site, hive-site

</td>
</tr>
</table>
## <span id="nextsteps"></span></a>Étapes suivantes

Vous avez appris à utiliser les appels d'API de surveillance Ambari. Pour plus d'informations, consultez les rubriques suivantes :

-   [Administration de clusters HDInsight à l'aide du portail de gestion][Administration de clusters HDInsight à l'aide du portail de gestion]
-   [Administration de clusters HDInsight à l'aide d'Azure PowerShell][Administration de clusters HDInsight à l'aide d'Azure PowerShell]
-   [Administration de clusters HDInsight à l'aide de l'interface de ligne de commande][Administration de clusters HDInsight à l'aide de l'interface de ligne de commande]
-   [Documentation HDInsight][Documentation HDInsight]
-   [Prise en main de HDInsight][Prise en main de HDInsight]

  [Présentation d'Ambari]: #whatisambari
  [Configuration requise]: #prerequisites
  [Démarrage rapide]: #jumpstart
  [API de surveillance Ambari]: #monitor
  [Étapes suivantes]: #nextsteps
  [Apache Ambari]: http://ambari.apache.org/
  [Référence des API Ambari]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [Installation et configuration d'Azure PowerShell]: ../install-configure-powershell/
  [Exécution de scripts Windows PowerShell]: http://technet.microsoft.com/fr-fr/library/ee176949.aspx
  [Curl]: http://curl.haxx.se
  [ici]: http://curl.haxx.se/download.html
  [Prise en main de HDInsight]: ../hdinsight-get-started/
  [Approvisionnement de clusters HDInsight]: ../hdinsight-provision-clusters/
  [Sortie du jobtracker]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
  [Administration de clusters HDInsight à l'aide du portail de gestion]: ../hdinsight-administer-use-management-portal/
  [Administration de clusters HDInsight à l'aide d'Azure PowerShell]: ../hdinsight-administer-use-powershell/
  [Administration de clusters HDInsight à l'aide de l'interface de ligne de commande]: ../hdinsight-administer-use-command-line/
  [Documentation HDInsight]: /fr-fr/documentation/services/hdinsight/
