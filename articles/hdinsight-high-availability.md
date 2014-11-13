<properties urlDisplayName="HDInsight High Availability" pageTitle="Disponibilit&eacute; des clusters Hadoop dans HDInsight | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight d&eacute;ploie des clusters hautement disponibles et fiables." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Disponibilit&eacute; des clusters Hadoop dans HDInsight" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Disponibilité et fiabilité des clusters Hadoop dans HDInsight

## Introduction

Un second nœud principal a été ajouté aux clusters Hadoop déployés par HDInsight pour améliorer la disponibilité et la fiabilité du service requis pour gérer les charges de travail. Les implémentations standard des clusters Hadoop ont normalement un seul nœud principal. Ces clusters sont conçus pour gérer en douceur la défaillance des nœuds de travail, mais toute interruption des services principaux exécutés sur le nœud principal provoquera l'arrêt du fonctionnement du cluster.

![][0]

HDInsight élimine ce point de défaillance unique avec l'ajout d'un nœud principal secondaire (Head Node1). Les nœuds [ZooKeeper][ZooKeeper] (ZK) ont été ajoutés et sont utilisés pour le choix de l'instance responsable des nœuds principaux et pour s'assurer que les nœuds de travail et les passerelles (GW) savent à quel moment doit se faire le basculement vers le nœud principal secondaire (Head Node1) lorsque le nœud principal actif (HeadNode0) devient inactif.

## Vérification du statut du service sur le nœud principal actif

Pour déterminer quel est le nœud principal actif et vérifier le statut des services en cours d'exécution sur ce nœud principal, vous devez vous connecter au cluster Hadoop avec le protocole RDP (Remote Desktop Protocol). La possibilité de se connecter à distance au cluster étant désactivée par défaut dans Azure, elle doit d'abord être activée. Pour des instructions sur la procédure à suivre dans le portail, consultez la page [Connexion à des clusters HDInsight à l'aide de RDP][Connexion à des clusters HDInsight à l'aide de RDP]
Une fois que vous vous êtes connecté à distance au cluster, double-cliquez sur l'icône **Statut disponible du service Hadoop** située sur le bureau pour obtenir le statut concernant le nœud principal sur lequel les services Namenode, Jobtracker, Templeton, Oozieservice, Metastore et Hiveserver2 s'exécutent, ou, pour HDI 3.0, les services Namenode, Resource Manager, History Server, Templeton, Oozieservice, Metastore and Hiveserver2.

![][1]

## Accès aux fichiers journaux sur le nœud principal secondaire

Pour accéder aux fichiers journaux sur le nœud principal secondaire dans le cas où il est devenu le nœud principal actif, la navigation dans l'interface utilisateur du service JobTracker de suivi des tâches fonctionne de la même manière que pour le nœud primaire (actif). Pour accéder au dispositif de suivi des tâches (Job Tracker), vous devez vous connecter au cluster Hadoop avec le protocole RDP (Remote Desktop Protocol), comme décrit dans la section précédente. Une fois que vous avez accédé à distance au cluster, double-cliquez sur l'icône **Nœud de nom Hadoop** située sur le bureau, puis cliquez sur **Journal du nœud de nom** pour accéder au répertoire des journaux sur le nœud principal secondaire.

![][2]

## Configuration de la taille du nœud principal

Par défaut, les nœuds principaux sont alloués en tant que machines virtuelles de taille importante. Cette taille est appropriée pour la gestion de la plupart des tâches Hadoop exécutées sur le cluster. Mais dans certains scénarios, des machines virtuelles de taille très importante peuvent être nécessaires pour les nœuds principaux. C'est le cas, par exemple, lorsque le cluster doit gérer un nombre important de petites tâches Oozie.

Les machines virtuelles de taille très importante peuvent être configurées soit avec des cmdlets Azure PowerShell soit avec le Kit de développement logiciel (SDK) HDInsight.

La création et l'approvisionnement d'un cluster avec PowerShell sont documentés sur la page [Administration de HDInsight à l'aide de PowerShell][Administration de HDInsight à l'aide de PowerShell]. La configuration d'un nœud principal de très grande taille nécessite l'ajout du paramètre `-HeadNodeVMSize ExtraLarge` à la cmdlet `New-AzureHDInsightcluster` utilisée dans ce code.

    # Create a new HDInsight cluster in Azure PowerShell
    # Configured with an ExtraLarge Headnode VM
    New-AzureHDInsightCluster -Name $clusterName -Location $location -HeadNodeVMSize ExtraLarge -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

Pour le Kit de développement logiciel (SDK), l'histoire est similaire. La création et l'approvisionnement d'un cluster avec le Kit de développement logiciel (SDK) sont documentés sur la page [Utilisation du Kit de développement logiciel (SDK) HDInsight .NET][Utilisation du Kit de développement logiciel (SDK) HDInsight .NET]. La configuration d'un nœud principal de très grande taille nécessite l'ajout du paramètre `HeadNodeSize = NodeVMSize.ExtraLarge` à la méthode `ClusterCreateParameters()` utilisée dans ce code.

    # Create a new HDInsight cluster with the HDInsight SDK
    # Configured with an ExtraLarge Headnode VM
    ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
    {
    Name = clustername,
    Location = location,
    HeadNodeSize = NodeVMSize.ExtraLarge,
    DefaultStorageAccountName = storageaccountname,
    DefaultStorageAccountKey = storageaccountkey,
    DefaultStorageContainer = containername,
    UserName = username,
    Password = password,
    ClusterSizeInNodes = clustersize
    };

**Références**

-   [ZooKeeper][ZooKeeper]
-   [Connexion à des clusters HDInsight à l'aide de RDP][Connexion à des clusters HDInsight à l'aide de RDP]
-   [Utilisation du Kit de développement logiciel (SDK) HDInsight .NET][Utilisation du Kit de développement logiciel (SDK) HDInsight .NET]

  [0]: http://i.imgur.com/jrUmrH4.png
  [ZooKeeper]: http://zookeeper.apache.org/
  [Connexion à des clusters HDInsight à l'aide de RDP]: ../hdinsight-administer-use-management-portal/#rdp
  [1]: http://i.imgur.com/MYTkCHW.png
  [2]: http://i.imgur.com/eL6jzgB.png
  [Administration de HDInsight à l'aide de PowerShell]: ../hdinsight-administer-use-powershell/
  [Utilisation du Kit de développement logiciel (SDK) HDInsight .NET]: ../hdinsight-provision-clusters/#sdk
