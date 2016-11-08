---
title: Utiliser Apache Phoenix et SQuirreL dans HDinsight | Microsoft Docs
description: Découvrez comment utiliser Apache Phoenix dans HDInsight et comment installer et configurer SQuirreL sur votre poste de travail pour vous connecter à un cluster HBase dans HDInsight.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: jgao

---
# Utilisation d’Apache Phoenix avec les clusters HBase basés sur Linux dans HDinsight
Découvrez comment utiliser [Apache Phoenix](http://phoenix.apache.org/) dans HDInsight et comment utiliser SQLLine. Pour plus d'informations sur Phoenix, consultez [Phoenix en 15 minutes ou moins](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Pour en savoir plus sur la grammaire Phoenix, consultez la page [Grammaire Phoenix](http://phoenix.apache.org/language/index.html).

> [!NOTE]
> Pour des informations sur la version de Phoenix dans HDInsight, consultez [Nouveautés des versions de cluster Hadoop fournies par HDInsight][hdinsight-versions].
> 
> 

## Utiliser SQLLine
[SQLLine](http://sqlline.sourceforge.net/) est un utilitaire de ligne de commande pour exécuter SQL.

### Composants requis
Avant de pouvoir utiliser SQLLine, vous devez disposer des éléments suivants :

* **Un cluster HBase dans HDInsight**. Pour plus d'informations sur l'approvisionnement d'un cluster HBase, consultez [Prise en main d'Apache HBase dans HDInsight][hdinsight-hbase-get-started].
* **Une connexion au cluster HBase à l'aide du protocole RDP (Remote Desktop Protocol)**. Pour obtenir des instructions, consultez [Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure Classic][hdinsight-manage-portal].

Quand vous vous connectez à un cluster HBase, vous devez vous connecter à l’un des Zookeepers. Chaque cluster HDInsight contient 3 Zookeepers :

**Pour connaître le nom d’hôte du Zookeeper**

1. Ouvrez Ambari en accédant à **https://<ClusterName>.azurehdinsight.net**.
2. Entrez le nom d’utilisateur HTTP (cluster) et le mot de passe de connexion.
3. Dans le menu de gauche, cliquez sur **ZooKeeper**. Vous devriez voir 3 **ZooKeeper Server** répertoriés.
4. Cliquez sur l’un des **ZooKeeper Server** répertoriés. Dans le volet Résumé, recherchez le **nom d’hôte**. Il ressemble à *zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Pour utiliser SQLLine**

1. Connectez-vous au cluster à l'aide de SSH. Pour obtenir des instructions, consultez [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix et OS X](hdinsight-hadoop-linux-use-ssh-unix.md) ou [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md) selon le système d’exploitation de votre ordinateur client.
2. Depuis SSH, exécutez les commandes suivantes pour exécuter SQLLine :
   
        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ClusterName>:2181:/hbase-unsecure
3. Exécutez les commandes suivantes pour créer une table HBase et insérer des données :
   
        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
   
        !tables
   
        UPSERT INTO Company VALUES(1, 'Microsoft');
   
        SELECT * FROM Company;
   
        !quit

Pour plus d’informations, consultez le [manuel SQLLine](http://sqlline.sourceforge.net/#manual) et la [grammaire Phoenix](http://phoenix.apache.org/language/index.html).

## Étapes suivantes
Dans cet article, vous avez appris comment utiliser Apache Phoenix dans HDInsight. Pour plus d'informations, consultez les rubriques suivantes :

* [Vue d'ensemble de HDInsight HBase][hdinsight-hbase-overview] : HBase est une base de données NoSQL open source Apache basée sur Hadoop qui fournit un accès aléatoire et une forte cohérence pour de grandes quantités de données non structurées et semi-structurées.
* [Approvisionnement de clusters HBase sur Azure Virtual Network][hdinsight-hbase-provision-vnet] :avec l'intégration du réseau virtuel, les clusters HBase peuvent être déployés sur le même réseau virtuel que vos applications pour permettre à celles-ci de communiquer directement avec HBase.
* [Configurer la réplication HBase dans HDInsight](hdinsight-hbase-geo-replication.md) : découvrez comment configurer la réplication HBase entre deux centres de données Azure.
* [Analyse de sentiments Twitter avec HBase dans HDInsight][hbase-twitter-sentiment] : découvrez comment effectuer une [analyse de sentiments](http://en.wikipedia.org/wiki/Sentiment_analysis) en temps réel des données volumineuses à l'aide de HBase dans un cluster Hadoop sous HDInsight.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png




<!---HONumber=AcomDC_0914_2016-->