<properties title="HDInsight Release Notes" pageTitle="Notes de publication de HDInsight | Azure" description="Notes de publication de HDInsight." metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure, release notes" services="HDInsight" solutions="" documentationCenter="" editor="cgronlun" manager="paulettm"  authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/05/2014" ms.author="bradsev" />


#Notes de publication de Microsoft HDInsight

## Notes relatives à la version du 05/12/2014 de HDInsight ##

Les numéros de version complets des clusters HDInsight déployés avec cette version sont les suivants :

* HDInsight 	2.1.9.406.1221105	(HDP 1.3.9.0-01351 )
* HDInsight 	3.0.5.406.1221105	(HDP 2.0.9.0-2097)
* HDInsight 	3.1.1.406.1221105	(HDP 2.1.9.0-2196)
* Kit de développement logiciel (SDK) HDInsight N/A

Cette version contient les mises à jour de composant suivantes.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Composant</th>
<th>Type du cluster</th>
<th>JIRA (le cas échéant)</th>
</tr>

<tr>
<td>Résolution de bogue : Erreurs intermittentes lors de l'ajout d'un grand nombre de partitions à une table dans un fichier DDL Hive. </td>
<td><p>En cas d'erreur de connexion intermittente à la base de données de metastore Hive lors de l'ajout d'un grand nombre de partitions à une table Hive, le fichier DDL Hive peut échouer. L'instruction suivante est alors affichée dans le journal d'erreurs Hive : </p><p>" ERROR [main]: ql.Driver (SessionState.java:printError(547)) - FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. MetaException(message:java.lang.RuntimeException: commitTransaction was called but openTransactionCalls = 0. This probably indicates that there are unbalanced calls to openTransaction/commitTransaction) "</p></td>
<td>Hive</td>
<td>Hadoop, Hbase</td>
<td>HIVE-482 (système JIRA interne ne pouvant pas faire l'objet d'un citation externe. Indiqué ici pour référence.)</td>
</tr>

<tr>
<td>Résolution de bogue : Blocage occasionnel dans la console de requête HDInsight</td>
<td>Lorsque cela se produit, l'instruction suivante est affichée dans le journal WebHCat pour la tâche du lanceur WebHCat : <p>" org.apache.hive.hcatalog.templeton.CatchallExceptionMapper | org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.yarn.exceptions.YarnRuntimeException): Could not load history file {wasb url to the history file} "</p></td>
<td>WebHCat</td>
<td>Hadoop</td>
<td>HIVE-482 (système JIRA interne ne pouvant pas faire l'objet d'un citation externe. Indiqué ici pour référence.)</td>
</tr>

<tr>
<td>Résolution de bogue : Pic occasionnel dans la latence des requêtes Hbase</td>
<td>Si cela se produit, les utilisateurs constatent un pic occasionnel de 3 secondes dans la latence des requêtes Hbase. </td>
<td>Passerelle de cluster HDInsight</td>
<td>HBase</td>
<td>N/A</td>
</tr>

<tr>
<td>Changement du nom du fichier Jar HDP</td>
<td>Pour le cluster HDI version 3.0, quelques modifications ont été apportées aux fichiers jar internes installés par HDP. jetty-6.1.26.jar
 a été remplacé par jetty-6.1.26.hwx.jar. jetty-util-6.1.26.jar a été remplacé par jetty-util-6.1.26.hwx.jar. Ces modifications s'appliquent aux projets Hadoop, Mahout, WebHCat et Oozie.**</td>
<td>Hadoop, Mahout, WebHCat, Oozie</td>
<td>Hadoop, HBase</td>
<td>N/A</td>
</tr>

</table>
<br>


## Notes relatives à la version du 21/11/2014 de HDInsight ##

Les numéros de version complets des clusters HDInsight déployés avec cette version sont les suivants :

* HDInsight 2.1.9.382.1169709 (pas de changements par rapport à la version du 14/11/2014)
* HDInsight 3.0.5.382.1169709 (pas de changements par rapport à la version du 14/11/2014)
* HDInsight 3.1.1.382.1169709 (pas de changements par rapport à la version du 14/11/2014)
* Kit de développement logiciel (SDK) HDINsight 1.4.0

Cette version contient les mises à jour de composant suivantes.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Composant</th>
<th>Type du cluster</th>
<th>JIRA (le cas échéant)</th>
</tr>

<tr>
<td>Accès aux journaux des applications</td>
<td>Possibilité d'énumérer, par programme, les applications qui ont été exécutées sur vos clusters et de télécharger des journaux spécifiques d'un conteneur ou d'une application afin de faciliter le débogage d'applications problématiques.</td>
<td>Kit de développement logiciel (SDK)</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Possibilité de spécifier le nom de la région dans IHdInsightClient.DeleteCluster </td>
<td>Le Kit de développement logiciel (SDK) Azure HDInsight permet désormais d'indiquer un nom de région lors de l'utilisation de **DeleteCluster**. Cela permet de débloquer les clients qui avaient 2 ressources avec le même nom dans des régions différentes et étaient dans l'impossibilité de les supprimer.</td>
<td>Kit de développement logiciel (SDK)</td>
<td>Tout</td>
<td>N/A</td>
</tr>

<tr>
<td>ClusterDetails.DeploymentId</td>
<td>L'objet **ClusterDetails** renvoie désormais un champ **DeploymentID** qui représente un identificateur unique du cluster. Son unicité est garantie parmi les tentatives de création de cluster portant les mêmes noms.</td>
<td>Kit de développement logiciel (SDK)</td>
<td>Tout</td>
<td>N/A</td>
</tr>
</table>
<br>

## Notes relatives à la version du 14/11/2014 de HDInsight ##

Les numéros de version complets des clusters HDInsight déployés avec cette version sont les suivants :

* HDInsight 2.1.9.382.1169709
* HDInsight 3.0.5.382.1169709
* HDInsight 3.1.1.382.1169709

Cette version comprend les nouveautés suivantes (fonctions, mises à jour de composants et résolutions de bogues).

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Composant</th>
<th>Type du cluster</th>
<th>JIRA (le cas échéant)</th>
</tr>

<tr>
<td>Action de script (version préliminaire)</td>
<td>Version préliminaire de la fonctionnalité de personnalisation des clusters qui permet de modifier les clusters Hadoop de manière arbitraire à l'aide de scripts personnalisés. Cette nouvelle fonctionnalité offre aux utilisateurs la possibilité de tester et de déployer des projets disponibles dans l'écosystème Apache Hadoop vers les clusters Azure HDInsight. Elle est disponible sur tous les types de cluster HDInsight, dont Hadoop, HBase et Storm.</td>
<td>Nouvelle fonctionnalité</td>
<td>Tout</td>
<td>N/A</td>
</tr>

<tr>
<td>Tâches préconfigurées pour l'analyse des journaux Sites Web Azure et Azure Storage</td>
<td>La console de requête HDInsight s'accompagne d'une galerie de mise en route qui prend en charge les solutions qui fonctionnent sur vos données ou sur les exemples de données.
<p>Solutions opérationnelles sur vos données :<br>
Nous avons créé des tâches pour certains des scénarios d'analyse de données les plus courants afin de fournir un point de départ pour créer vos propres solutions. Vous pouvez utiliser vos propres données avec la tâche pour savoir comme elle fonctionnent. Lorsque vous êtes prêt, mettez en pratique ce que vous avez appris afin de créer votre propre solution sur la base de la tâche préconfigurée.</p>
<p>Solutions opérationnelles sur les exemples de données :<br>
Découvrez comment utiliser HDInsight en parcourant des scénarios de base, tels que l'analyse de journaux web et de données de capteur. Vous y apprendrez non seulement comment utiliser HDInsight pour analyser de telles données, mais aussi comment connecter d'autres applications et services à ces données. La visualisation de données en se connectant à Microsoft Excel illustre parfaitement la puissance de cette méthode.</p></td>
<td>Console de requête</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Résolution du problème de fuite de mémoire dans Templeton</td>
<td>Une fuite de mémoire dans Templeton a été corrigée. Ce problème concernait les clients qui possédaient un cluster de longue durée ou qui envoyaient des centaines de demandes de tâches à la seconde. Ce problème se manifestait sous la forme d'erreurs Templeton 5xx et la solution consistait à redémarrer service. Cette solution n'est plus nécessaire.</td>
<td>Templeton</td>
<td>Tout</td>
<td>https://issues.apache.org/jira/browse/HADOOP-11248</td>
</tr>
</table>
<br>


Remarque : Pour démontrer les nouvelles fonctionnalités rendues disponibles par la personnalisation de cluster, les procédures qui utilisent des actions de script pour installer les modules Spark et R sur un cluster ont été documentées. Pour plus d'informations, consultez les rubriques suivantes :

* [Installation et utilisation de Spark 1.0 sur des clusters HDInsight][hdinsight-install-spark]
* [Installation et utilisation de R sur des clusters HDInsight Hadoop][hdinsight-r-scripts]




## Notes relatives à la version du 07/11/2014 de HDInsight ##

Les numéros de version complets des clusters HDInsight déployés avec cette version sont les suivants :

* HDInsight 2.1	2.1.9.374.1153876
* HDInsight 3.0	3.0.5.374.1153876
* HDInsight 3.1	3.1.1.374.1153876

Cette version contient les mises à jour de composant suivantes.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Composant</th>
<th>Type du cluster</th>
<th>JIRA (le cas échéant)</th>
</tr>

<tr>
<td>HDP 2.1.7</td>
<td>Cette version est basée sur Hortonworks Data Platform (HDP) 2.1.7. Les notes de version relatives à HDP 2.1.7 sont disponibles sur le site web de Hortonworks à l'adresse suivante : http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html.</td>
<td>HDP</td>
<td>Tout</td>
<td>N/A</td>
</tr>

<tr>
<td>YARN Timeline Server</td>
<td>YARN Timeline Server (connu également sous le nom de Generic Application History Server) a été activé par défaut. Ce serveur fournit des informations génériques sur les applications terminées, telles que l'ID, le nom, l'état, l'heure d'envoi et l'heure de fin de l'application. <p>Ces informations peuvent être extraites du nœud principal, soit en accédant à l'URI http://headnodehost:8188, soit en exécutant la commande YARN " yarn application -list -appStates ALL ",</p> 
<p>ou à distance via l'API REST à l'adresse https://{ClusterDnsName}. azurehdinsight.net/ws/v1/applicationhistory/.</p> 
<p>Pour obtenir des informations plus détaillées sur ce serveur, rendez-vous sur http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html.</p></td>
<td>Service, YARN</td>
<td>Hadoop, HBase</td>
<td>N/A</td>
</tr>

<tr>
<td>ID de déploiement de cluster</td>
<td>Depuis la dernière version du Kit de développement logiciel (SDK) 1.3.3.1.5426.29232, les utilisateurs peuvent accéder à un identificateur unique émis par HDInsight pour chaque cluster. Cela permet aux clients de résoudre les instances uniques de clusters lorsqu'un nom DNS est réutilisé dans des scénarios de création/suppression.</td>
<td>Kit de développement logiciel (SDK)</td>
<td>Tout</td>
<td>N/A</td>
</tr>
</table>
<br>

* Notez que l'erreur qui empêchait la version complète # de s'afficher dans le portail ou d'être renvoyée par le Kit de développement logiciel (SDK) ou par PowerShell a été corrigée dans cette version. 

## Notes pour la version du 15/10/2014 ##

Cette version de correctif logiciel a résolu un problème de fuite de mémoire dans Templeton qui affectait les utilisateurs réguliers de Templeton. Dans certains cas, les utilisateurs réguliers de Templeton faisaient face à des erreurs ayant le code d'erreur 500, car leurs demandes ne disposaient pas d'assez de mémoire pour s'exécuter. La solution de contournement pour ce problème consistait à redémarrer le service Templeton. Ce problème est à présent résolu.


## Notes pour la version du 07/10/2014 ##

* Lors de l'utilisation du point de terminaison Ambari, " https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname} ", le champ *host_name* renvoie à présent le nom de domaine complet du nœud et pas simplement le nom d'hôte. Par exemple, au lieu de renvoyer " **headnode0** ", vous pouvez obtenir le nom de domaine complet " **headnode0.{ClusterDNS}.azurehdinsight.net** ". Cette modification était nécessaire pour permettre les scénarios dans lesquels plusieurs types de cluster tels que HBase et Hadoop sont déployés dans un réseau virtuel (VNET). Cela se produit, par exemple, lors de l'utilisation de HBase en tant que plateforme principale pour Hadoop.

* Nous avons fourni de nouveaux paramètres de mémoire pour le déploiement par défaut d'un cluster HDInsight. Les précédents paramètres de mémoire ne prenaient pas correctement en compte les conseils relatifs au nombre de cœurs de processeurs déployés. Ces nouveaux paramètres de mémoire doivent normalement fournir de meilleures valeurs par défaut, conformément aux recommandations de Hortonworks. Pour les modifier, veuillez consulter la documentation de référence du Kit de développement logiciel (SDK) relative à la modification de la configuration du cluster. Les nouveaux paramètres de mémoire utilisés par le cluster HDInsight quadricœur (8 conteneurs) par défaut sont répertoriés dans le tableau suivant. Les valeurs utilisées avant cette version sont également indiquées entre parenthèses. 
 
<table border="1">
<tr><th>Composant</th><th>Allocation de mémoire</th></tr>
<tr><td> yarn.scheduler.minimum-allocation</td><td>768 Mo (précédemment 512 Mo)</td></tr>
<tr><td> yarn.scheduler.maximum-allocation</td><td>6 144 Mo (non modifiée)</td></tr>
<tr><td>yarn.nodemanager.resource.memory</td><td>6 144 Mo (non modifiée)</td></tr>
<tr><td>mapreduce.map.memory</td><td>768 Mo (précédemment 512 Mo)</td></tr>
<tr><td>mapreduce.map.java.opts</td><td>opts=-Xmx512m (précédemment -Xmx410m)</td></tr>
<tr><td>mapreduce.reduce.memory</td><td>1 536 Mo (précédemment 1 024 Mo)</td></tr>
<tr><td>mapreduce.reduce.java.opts</td><td>opts=-Xmx1024m (précédemment -Xmx819m)</td></tr>
<tr><td>yarn.app.mapreduce.am.resource</td><td>768 Mo (précédemment 1 024 Mo)</td></tr>
<tr><td>yarn.app.mapreduce.am.command</td><td>opts=-Xmx512m (précédemment -Xmx819m)</td></tr>
<tr><td>mapreduce.task.io.sort</td><td>256 Mo (précédemment 200 Mo)</td></tr>
<tr><td>tez.am.resource.memory</td><td>1 536 Mo (non modifiée)</td></tr>

</table><br>

Pour plus d'informations sur les paramètres de configuration de mémoire utilisés par YARN et MapReduce sur la plateforme de données Hortonworks utilisée par HDInsight, consultez la rubrique [Déterminer les paramètres de configuration de la mémoire HDP](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html). Hortonworks a également fourni un outil permettant de calculer les paramètres de mémoire adéquats.

Erreur de Kit de développement logiciel (SDK)/HDInsight PowerShell : " *Le cluster n'est pas configuré pour l'accès aux services Http* " :

* Il s'agit d'un [problème de compatibilité](https://social.msdn.microsoft.com/Forums/azure/en-US/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight) connu pouvant survenir en raison d'une différence entre la version de Kit de développement logiciel (SDK)/PowerShell et la version du cluster. Les clusters créés le 15/08 ou ultérieurement prennent en charge la nouvelle capacité d'approvisionnement dans les réseaux virtuels. Mais cette capacité n'est pas interprétée correctement par les versions antérieures du Kit de développement logiciel/PowerShell. Il en résulte un échec dans certaines opérations de soumission de tâches. Si vous utilisez des API SDK ou des cmdlets PowerShell pour envoyer des tâches (**Use-AzureHDInsightCluster**, **Invoke-Hive**), ces opérations peuvent échouer avec le message d'erreur " *Le cluster <nom_cluster> n'est pas configuré pour l'accès aux services Http " ou, en fonction de l'opération, avec d'autres types de message d'erreur tels que “*Impossible de se connecter au cluster*”.

* Ces problèmes de compatibilité sont résolus dans les dernières versions du Kit de développement logiciel (SDK) HDInsight et Azure PowerShell. Nous vous recommandons de mettre à jour le Kit de développement logiciel (SDK) HDInsight à la version 1.3.1.6 ou ultérieure et les outils Azure PowerShell à la version 0.8.8 ou ultérieure. Vous pouvez obtenir l'accès au Kit de développement logiciel (SDK) HDInsight le plus récent depuis [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) et les outils  Azure PowerShell Tools à la rubrique [Installation et configuration d'Azure PowerShell](http://azure.microsoft.com/fr-fr/documentation/articles/install-configure-powershell/).

* Vous pouvez vous attendre à ce que le Kit de développement logiciel (SDK) et PowerShell continuent de fonctionner avec les nouvelles mises à jour des clusters tant que la version du cluster reste identique. Par exemple, la version 3.1 des clusters sera toujours compatible avec la version actuelle du Kit de développement logiciel (SDK)/PowerShell 1.3.1.6 et 0.8.8.


## Notes pour la version du 12/09/2014 de HDinsight 3.1##

* Cette version est basée sur Hortonworks Data Platform (HDP) 2.1.5. Pour obtenir la liste des bogues corrigés dans cette version, consultez la page [Bogues corrigés dans cette version](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html) sur le site web de Hortonworks.
* Dans le dossier de bibliothèques Pig, le fichier " avro-mapred-1.7.4.jar " a été remplacé par avro-mapred-1.7.4-hadoop2.jar. Ces fichiers contiennent une résolution de bogue mineur ne provoquant pas d'arrêt. Nous recommandons aux clients de ne pas prendre une dépendance directe sur le nom du fichier JAR en lui-même, pour éviter tout arrêt intempestif lorsque des fichiers sont renommés.


## Notes pour la version du 21/08/2014 ##

* Nous ajoutons la nouvelle configuration WebHCat suivante (HIVE-7155) qui fixe la limite de mémoire par défaut pour une tâche de contrôleur Templeton à 1 Go (l'ancienne valeur par défaut était de 512 Mo) :
	
	* templeton.mapper.memory.mb (=1024)
	* Cette modification résout l'erreur suivante que certaines requêtes Hive rencontraient en raison des limites de mémoire plus basses : “Container is running beyond physical memory limits” (Le conteneur a dépassé les limites de la mémoire physique).
	* Pour revenir aux anciens paramètres par défaut, vous pouvez définir cette valeur de configuration sur 512 via le Kit de développement logiciel (SDK) PowerShell au moment de la création du cluster, avec la commande suivante :
	
		Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}


* Le nom d'hôte du rôle zookeeper a été remplacé par zookeeper. Cela a une incidence sur la résolution de noms dans le cluster, mais pas sur les API REST externes. Si vous avez des composants qui utilisent le nom d'hôte du nœud zookeeper, vous devez les mettre à jour pour qu'ils utilisent le nouveau nom. Les nouveaux noms des trois nœuds zookeeper sont les suivants : 
	* zookeeper0 
	* zookeeper1 
	* zookeeper2 
* La matrice de support des versions de HBase est mise à jour. Seule la version HDInsight 3.1 (HBase version 0.98) est prise en charge pour les charges de travail HBase de production. La version 3.0 qui était disponible en tant que version préliminaire ne sera plus prise en charge à l'avenir. Pendant la période de transition, les clients pourront continuer à créer des clusters de la version 3.0. 

## Notes sur les clusters créés avant le 15/08/2014 ##

Vous pouvez rencontrer une erreur de Kit de développement logiciel (SDK)/HDInsight PowerShell dont le message est " Le cluster <nom_cluster> n'est pas configuré pour l'accès aux services HTTP " (ou, en fonction de l'opération, d'autres messages comme : " Impossible de se connecter au cluster ") en raison d'une différence de version entre le Kit de développement logiciel (SDK)/PowerShell et un cluster. Les clusters créés le 15/08 ou ultérieurement prennent en charge la nouvelle capacité d'approvisionnement dans les réseaux virtuels. Cette capacité n'est pas interprétée correctement par les versions antérieures du Kit de développement logiciel (SDK)/PowerShell, ce qui entraîne des défaillance d'opérations d'envoi de tâche. Si vous utilisez des API de Kit de développement logiciel (SDK) ou des cmdlets PowerShell pour envoyer des tâches, comme Use-AzureHDInsightCluster ou Invoke-AzureHDInsightHiveJob, il est possible que ces opérations échouent en affichant l'un des messages d'erreur décrits plus haut.

Ces problèmes de compatibilité sont résolus dans les dernières versions du Kit de développement logiciel (SDK) et Azure PowerShell. Nous vous recommandons de mettre à jour le Kit de développement logiciel (SDK) HDInsight à la version 1.3.1.6 ou ultérieure et les outils Azure PowerShell à la version 0.8.8 ou ultérieure. Vous pouvez obtenir l'accès au Kit de développement logiciel (SDK) HDInsight le plus récent depuis [NuGet](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/) et les outils Azure PowerShell Tools à l'aide de [Microsoft Web PI](http://go.microsoft.com/?linkid=9811175&clcid=0x409).

Vous pouvez vous attendre à ce que le Kit de développement logiciel (SDK) et PowerShell continuent de fonctionner avec les nouvelles mises à jour des clusters tant que la version du cluster reste identique. Par exemple, la version 3.1 des clusters sera toujours compatible avec la version actuelle du Kit de développement logiciel (SDK)/PowerShell 1.3.1.6 et 0.8.8.


## Notes pour la version du 28/07/2014 ##

* **HDInsight est disponible dans de nouvelles régions** : avec cette version, nous avons étendu la présence géographique de HDInsight à trois nouvelles régions. Les clients HDInsight peuvent désormais créer des clusters dans ces régions. 
	* Asie orientale 
	* Nord du centre des États-Unis et 
	* Sud du centre des États-Unis. 
* Avec cette version, HDInsight v1.6 (HDP1.1, Hadoop 1.0.3) et HDInsight v2.1 (HDP1.3, Hadoop 1.2) sont retirés du portail de gestion Azure. Vous pouvez continuer à créer des clusters Hadoop pour ces versions à l'aide de cmdlets HDInsight PowerShell ([New-AzureHDInsightCluster](http://msdn.microsoft.com/fr-fr/library/dn593744.aspx)) ou en utilisant le [Kit de développement logiciel (SDK) HDInsight](http://msdn.microsoft.com/fr-fr/library/azure/dn469975.aspx). Pour plus d'informations, consultez la page [Contrôle de version des composants HDInsight](http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-component-versioning/) .
* Changements concernant Hortonworks Data Platform (HDP) dans cette version : 

<table border="1">
<tr><th>HDP</th><th>Changements</th></tr>
<tr><td>HDP 1.3 / HDI 2.1</td><td>Pas de changements</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>Pas de changements</td></tr>
<tr><td>HDP 2.1 / HDI 3.1</td><td>zookeeper: ['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>


</table><br>

## Notes pour la version du 24/06/14 ##

Cette version inclut plusieurs nouvelles améliorations du service HDInsight : 

* **Disponibilité de HDP 2.1** : HDInsight 3.1, qui contient HDP 2.1, est désormais disponible pour le grand public et constitue la version par défaut pour les nouveaux clusters.
* **HBase - Améliorations du portail de gestion Azure** : nous faisons en sorte que les clusters HBase soient disponibles dans la version préliminaire. Vous pouvez désormais créer des clusters HBase à partir du portail en 3 clics.

![](http://i.imgur.com/cmOl5fM.png)

Avec HBase, vous pouvez créer différentes charges de travail en temps réel sur HDInsight, de sites web interactifs fonctionnant avec des jeux de données volumineux à des services stockant les données de capteur et de télémétrie provenant de millions de points de terminaison. L'étape suivante consisterait à analyser les données dans ces charges de travail avec des tâches Hadoop, ce qui est déjà possible dans HDInsight grâce, notamment, à PowerShell et au tableau de bord de cluster Hive.

### Apache Mahout désormais installé sur HDInsight 3.1 ###

 [Mahout](http://hortonworks.com/hadoop/mahout/) est préinstallé sur les clusters Hadoop HDInsight 3.1. Vous pouvez donc exécuter des tâches Mahout sans avoir besoin d'une configuration de cluster supplémentaire. Par exemple, vous pouvez vous connecter à distance à un cluster Hadoop avec le protocole RDP (Remote Desktop Protocol) et exécuter la commande Hello world Mahout sans étapes supplémentaires :

		mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

		mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Pour une explication plus complète de cette procédure, consultez la documentation de l'[Exemple Breiman(https://mahout.apache.org/users/classification/breiman-example.html) sur le site web Apache Mahout. 


### Les requêtes Hive peuvent utiliser Tez dans HDinsight 3.1 ###

Hive 0.13 est désormais disponible dans HDInsight 3.1 et capable d'exécuter des requêtes avec Tez. Des gains de performances considérables peuvent ainsi être obtenus. 
Tez n'est pas activé par défaut pour les requêtes Hive. Vous devez choisir de l'utiliser. Vous pouvez activer Tez en exécutant l'extrait de code suivant :

		set hive.execution.engine=tez;
		select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks a publié une ventilation détaillée des améliorations apportées aux requêtes Hive en termes de performances avec Tez, tel qu'il est fourni dans les benchmarks standard. Pour plus d'informations, consultez la page [Tests de performances d'Apache Hive 13 for Enterprise Hadoop](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/). 

Pour plus d'informations sur l'utilisation de Hive avec Tez, consultez la [page wiki Hive sur Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez).

###Disponibilité globale
Avec le lancement d'Azure HDInsight sur Hadoop 2.2, Microsoft a fait en sorte que HDInsight soit disponible dans les principales régions Azure. Plus précisément, l'Europe de l'Ouest et le Sud-Est asiatique ont été mis en ligne. Cela permet aux clients de rechercher des clusters dans un centre de données proche et, potentiellement, dans une zone avec des exigences de conformité similaires. 


###﻿À faire et ne pas faire entre les versions de clusters

**Les metastores Oozie utilisés avec un cluster HDInsight 3.1 n'ont plus de compatibilité descendante avec les clusters HDInsight 2.1 et ne peuvent pas être utilisés avec cette version précédente.**

Vous ne pouvez plus réutiliser une base de données de metastore Oozie personnalisée déployée avec un cluster HDInsight 3.1 avec un cluster HDInsight 2.1. Ceci est valable même si le metastore a été créé avec un cluster 2.1. Ce scénario n'est pas pris en charge, car le schéma du metastore est mis à niveau lorsqu'il est utilisé avec un cluster 3.1. Il n'est donc plus compatible avec le metastore requis par les clusters 2.1. Toute tentative de réutilisation d'un metastore Oozie utilisé avec un cluster HDInsight 3.1 rendra le cluster 2.1 inutilisable. 

**Impossible de partager des metastores Oozie entre des clusters**
D'un point de vue plus général et même orthogonal, les metastores Oozie sont joints à des clusters spécifiques. Vous ne pouvez donc pas les partager entre différents clusters.

###Dernières modifications

**Syntaxe du préfixe** :
seule la syntaxe " wasb:// " est prise en charge dans les clusters HDInsight 3.0 et 3.1. L'ancienne syntaxe " asv:// " est prise en charge dans les clusters HDInsight 2.1 et 1.6, mais pas dans les clusters HDInsight 3.0 ou les versions ultérieures. Cela signifie que toutes les tâches envoyées vers un cluster HDInsight 3.0 ou 3.1 utilisant explicitement la syntaxe " asv:// " échoueront. Vous devez plutôt utiliser la syntaxe wasb://. De même, les tâches créées avec un metastore existant contenant des références explicites aux ressources utilisant la syntaxe asv:// et envoyées vers un cluster HDInsight 3.0 ou 3.1 échoueront également. Vous devrez recréer ces metastores en utilisant la syntaxe wasb:// pour adresser les ressources. 


**Ports**: les ports utilisés par le service HDInsight ont changé. Les numéros de ports utilisés étaient inclus dans la plage de ports éphémères du système d'exploitation Windows. Les ports sont alloués automatiquement à partir d'une plage éphémère prédéfinie pour des communications à durée de vie limitée basées sur un protocole Internet. Le nouvel ensemble de numéros de ports du service HDP (Hortonworks Data Platform) autorisés est désormais à l'extérieur de cette plage pour éviter tout conflit avec les ports utilisés par les services exécutés sur le nœud principal. Les nouveaux numéros de ports ne devraient pas entraîner des modifications radicales. Les numéros utilisés sont les suivants :

 **HDInsight 1.6 (HDP 1.1)**
<table border="1">
<tr><th>Nom</th><th>Valeur</th></tr>
<tr><td>dfs.http.address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.secondary.http.address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.tracker.http.address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.task.tracker.http.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>mapreduce.history.server.http.address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

 **HDInsight 3.0 et 3.1 (HDP 2.0 et 2.1)**
<table border="1">
<tr><th>Nom</th><th>Valeur</th></tr>
<tr><td>dfs.namenode.http-address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.namenode.https-address</td><td>headnodehost:30470</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.namenode.secondary.http-address</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.nodemanager.webapp.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

###Dépendances 

Les dépendances suivantes ont été ajoutées à HDInsight 3.x (HDP2.x):

* guice-servlet
* optiq-core
* javax.inject
* activation
* jsr305
* geronimo-jaspic_1.0_spec
* jul-to-slf4j
* java-xmlbuilder
* ant
* commons-compiler
* jdo-api
* commons-math3
* paranamer
* jaxb-impl
* stringtemplate
* eigenbase-xom
* jersey-servlet
* commons-exec
* jaxb-api
* jetty-all-server
* janino
* xercesImpl
* optiq-avatica
* jta
* eigenbase-properties
* groovy-all
* hamcrest-core
* mail
* linq4j
* jpam
* jersey-client
* aopalliance
* geronimo-annotation_1.0_spec
* ant-launcher
* jersey-guice
* xml-apis
* stax-api
* asm-commons
* asm-tree
* wadl
* geronimo-jta_1.1_spec
* guice
* leveldbjni-all
* velocity
* jettison
* snappy-java
* jetty-all
* commons-dbcp

Les dépendances suivantes n'existent plus dans HDInsight 3.x (HDP2.x):

* jdeb
* kfs
* sqlline
* ivy
* aspectjrt
* json
* core
* jdo2-api
* avro-mapred
* datanucleus-enhancer
* jsp
* commons-logging-api
* commons-math
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* snappy

###Changements de version 

Les changements de version suivants ont eu lieu entre HDInsight 2.x (HDP1.x) et HDInsight 3.x (HDP2.x):

* metrics-core: ['2.1.2'] -> ['3.0.0']
* derbynet: ['10.4.2.0'] -> ['10.10.1.1']
* datanucleus: ['rdbms-3.0.8'] -> ['rdbms-3.2.9']
* jasper-compiler: ['5.5.12'] -> ['5.5.23']
* log4j: ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient: ['10.4.2.0'] -> ['10.10.1.1']
* httpcore: ['4.2.4'] -> ['4.2.5']
* hsqldb: ['1.8.0.10'] -> ['2.0.0']
* jets3t: ['0.6.1'] -> ['0.9.0']
* protobuf-java: ['2.4.1'] -> ['2.5.0']
* derby: ['10.4.2.0'] -> ['10.10.1.1']
* jasper: ['runtime-5.5.12'] -> ['runtime-5.5.23']
* commons-daemon: ['1.0.1'] -> ['1.0.13']
* datanucleus-core: ['3.0.9'] -> ['3.2.10']
* datanucleus-api-jdo: ['3.0.7'] -> ['3.2.6']
* zookeeper: ['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp: ['0.7.1.RELEASE'] -> ['0.8.0.RELEASE']


###Pilotes
Le pilote JDBC pour SQL Server est utilisé en interne par HDInsight et n'est pas employé pour les opérations externes. Si vous voulez vous connecter à HDInsight avec ODBC, utilisez le pilote ODBC Hive de Microsoft. Pour plus d'informations sur l'utilisation du pilote ODBC Hive, consultez la page [Connexion d'Excel à HDInsight à l'aide du pilote ODBC Microsoft Hive][connect-excel-with-hive-ODBC].


### Résolution des bogues ###

Dans cette version, nous avons actualisé les versions suivantes de HDInsight (Hortonworks Data Platform - HDP) avec la résolution de plusieurs bogues :

* HDInsight 2.1 (HDP 1.3)
* HDInsight 3.0 (HDP 2.0)
* HDInsight 3.1 (HDP 2.1)


## Notes de publication de Hortonworks ##

Les notes de publication des HDP utilisés par les versions de cluster HDInsight sont disponibles aux emplacements suivants.

* Le cluster HDInsight version 3.1 utilise une distribution de Hadoop basée sur [Hortonworks Data Platform 2.1.7][hdp-2-1-7].Il s'agit du cluster Hadoop par défaut créé lors de l'utilisation du portail Azure HDInsight après le 7/11/2014. Les clusters HDInsight 3.1 créés avant le 07/11/2014 étaient basés sur [Hortonworks Data Platform 2.1.1][hdp-2-1-1] 

* Le cluster HDInsight version 3.0 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 2.0][hdp-2-0-8].

* Le cluster HDInsight version 2.1 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 1.3][hdp-1-3-0]. 

* Le cluster HDInsight version 1.6 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 1.1][hdp-1-1-0]. 

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html


[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/




<!--HONumber=35.1-->
