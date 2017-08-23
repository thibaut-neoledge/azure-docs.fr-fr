---
title: Utiliser une action de script pour installer Solr sur un cluster Hadoop - Azure | Documents Microsoft
description: "Découvrez comment personnaliser un cluster HDInsight avec Solr à l’aide d’une action de script."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b1e6f338-8ac1-4b38-bbb5-2f7388b9de3b
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 6efb7ea26c3cdf7748fff4b02b5810c85cc41e1a
ms.contentlocale: fr-fr
ms.lasthandoff: 07/08/2017


---
# <a name="install-and-use-solr-on-windows-based-hdinsight-clusters"></a>Installer et utiliser Solr sur les clusters HDInsight Windows

Découvrez comment personnaliser un cluster HDInsight basé sur Windows avec Solr à l’aide d’une action de script, et comment utiliser Solr pour explorer des données.

> [!IMPORTANT]
> Les étapes décrites dans ce document fonctionnent uniquement avec les clusters HDInsight Windows. HDInsight est uniquement disponible sur Windows pour les versions antérieures à HDInsight 3.4. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Pour plus d’informations sur l’utilisation de Solr avec un cluster Linux, consultez [Installation et utilisation de Solr sur des clusters HDInsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md).


Vous pouvez installer Solr sur n’importe quel type de cluster (Hadoop, Storm, HBase, Spark) sur Azure HDInsight à l’aide d’une *action de script*. Pour obtenir un exemple de script pour installer Solr sur un cluster HDInsight, téléchargez l’objet blob de stockage Azure en lecture seule à l’adresse [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

L'exemple de script fonctionne uniquement avec un cluster HDInsight version 3.1. Pour plus d’informations sur les versions des clusters HDInsight, consultez la page [Versions des clusters HDInsight](hdinsight-component-versioning.md).

L’exemple de script utilisé dans cette rubrique crée un cluster Solr Windows avec une configuration spécifique. Si vous souhaitez configurer le cluster Solr avec d'autres collections, partitions, schémas, réplicas, etc., vous devez modifier le script et les fichiers binaires Solr en conséquence.

**Articles connexes**

* [Installer et utiliser Solr sur des clusters HDInsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [Créer des clusters Hadoop dans HDInsight](hdinsight-provision-clusters.md): informations générales sur la création de clusters HDInsight.
* [Personnaliser un cluster HDInsight à l’aide d’une action de script][hdinsight-cluster-customize] : informations générales sur la personnalisation de clusters HDInsight à l’aide d’actions de script.
* [Développer des scripts d’action de script pour HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-solr"></a>Présentation de Solr
<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> est une plateforme de recherche d’entreprise qui permet d’effectuer de puissantes opérations de recherche en texte intégral sur des données. Alors que Hadoop permet de stocker et de gérer de grandes quantités de données, Apache Solr fournit les fonctionnalités de recherche nécessaires pour les récupérer rapidement.

## <a name="install-solr-using-portal"></a>Installer Solr à l’aide du portail
1. Démarrez la création d’un cluster à l’aide de l’option **CRÉATION PERSONNALISÉE** , comme décrit dans [Création de clusters Hadoop dans HDInsight](hdinsight-provision-clusters.md).
2. Sur la page **Actions de script** de l’Assistant, cliquez sur **ajouter l’action de script** pour fournir des informations sur l’action de script, comme illustré ci-dessous :

    ![Utiliser Action de script pour personnaliser un cluster](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Utiliser Action de script pour personnaliser un cluster")

    <table border='1'>
        <tr><th>Propriété</th><th>Valeur</th></tr>
        <tr><td>Nom</td>
            <td>Indiquez un nom pour l'action de script. Par exemple, <b>Installation Solr</b>.</td></tr>
        <tr><td>URI du script</td>
            <td>Spécifiez l'URI (Uniform Resource Identifier) du script appelé pour personnaliser le cluster. Par exemple, <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
        <tr><td>Type de nœud</td>
            <td>Spécifiez les nœuds sur lesquels le script de personnalisation est exécuté. Vous avez le choix entre <b>Tous les nœuds</b>, <b>Nœuds principaux uniquement</b> et <b>Nœuds de travail uniquement</b>.
        <tr><td>parameters</td>
            <td>Spécifiez les paramètres, si le script le demande. Le script d’installation de Solr ne nécessite aucun paramètre. Vous pouvez donc laisser ce champ vide.</td></tr>
    </table>

    Vous pouvez ajouter plusieurs actions de script pour installer plusieurs composants sur le cluster. Après avoir ajouté les scripts, cliquez sur la coche pour démarrer la création du cluster.

## <a name="use-solr"></a>Utiliser Solr
Vous devez commencer par indexer Solr avec quelques fichiers de données. Vous pouvez ensuite utiliser Solr pour exécuter des requêtes de recherche sur les données indexées. Procédez comme suit pour utiliser Solr dans un cluster HDInsight :

1. **Utilisez le protocole RDP (Remote Desktop) pour vous connecter à distance au cluster HDInsight avec Solr installé**. À partir du portail Azure, activez le Bureau à distance pour le cluster que vous avez créé avec Solr installé, puis accédez à distance au cluster. Pour la marche à suivre, consultez [Connexion à des clusters HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. **Indexez Solr en téléchargeant des fichiers de données**. Lorsque vous indexez Solr, vous y placez des documents sur lesquels vous devrez peut-être effectuer des recherches. Pour indexer Solr, utilisez le protocole RDP pour vous connecter à distance au cluster, accédez au Bureau, ouvrez la ligne de commande Hadoop et accédez à **C:\apps\dist\solr-4.7.2\example\exampledocs**. Exécutez la commande suivante :

        java -jar post.jar solr.xml monitor.xml

    Le résultat suivant s’affiche sur la console :

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    L’utilitaire post.jar indexe Solr avec deux exemples de documents : **solr.xml** et **monitor.xml**. L'utilitaire post.jar et les exemples de documents sont disponibles avec l'installation Solr.
3. **Utilisez le tableau de bord Solr pour effectuer une recherche dans les documents indexés**. Dans la session RDP au cluster HDInsight, ouvrez Internet Explorer, puis lancez le tableau de bord Solr à l'adresse **http://headnodehost:8983/solr/#/**. Dans la liste déroulante **Sélecteur de base** située dans le volet gauche, sélectionnez **collection1** et cliquez ensuite sur **Requête**. Par exemple, pour sélectionner et renvoyer tous les documents dans Solr, renseignez les valeurs suivantes :

   * Dans la zone de texte **q**, entrez **\*:**\*. Tous les documents indexés dans Solr sont alors renvoyés. Si vous souhaitez rechercher une chaîne spécifique dans les documents, vous pouvez la saisir à cet emplacement.
   * Sélectionnez le format de sortie dans la zone de texte **wt** . La valeur par défaut est **json**. Cliquez sur **Exécuter la requête**.

     ![Utiliser Action de Script pour personnaliser un cluster](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Exécuter une requête sur un tableau de bord Solr")

     La sortie renvoie les deux documents que nous avions utilisés pour indexer Solr. La sortie se présente comme suit :

           "response": {
               "numFound": 2,
               "start": 0,
               "maxScore": 1,
               "docs": [
                 {
                   "id": "SOLR1000",
                   "name": "Solr, the Enterprise Search Server",
                   "manu": "Apache Software Foundation",
                   "cat": [
                     "software",
                     "search"
                   ],
                   "features": [
                     "Advanced Full-Text Search Capabilities using Lucene",
                     "Optimized for High Volume Web Traffic",
                     "Standards Based Open Interfaces - XML and HTTP",
                     "Comprehensive HTML Administration Interfaces",
                     "Scalability - Efficient Replication to other Solr Search Servers",
                     "Flexible and Adaptable with XML configuration and Schema",
                     "Good unicode support: héllo (hello with an accent over the e)"
                   ],
                   "price": 0,
                   "price_c": "0,USD",
                   "popularity": 10,
                   "inStock": true,
                   "incubationdate_dt": "2006-01-17T00:00:00Z",
                   "_version_": 1486960636996878300
                 },
                 {
                   "id": "3007WFP",
                   "name": "Dell Widescreen UltraSharp 3007WFP",
                   "manu": "Dell, Inc.",
                   "manu_id_s": "dell",
                   "cat": [
                     "electronics and computer1"
                   ],
                   "features": [
                     "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                   ],
                   "includes": "USB cable",
                   "weight": 401.6,
                   "price": 2199,
                   "price_c": "2199,USD",
                   "popularity": 6,
                   "inStock": true,
                   "store": "43.17614,-90.57341",
                   "_version_": 1486960637584081000
                 }
               ]
             }
4. **Recommandé : sauvegardez les données indexées à partir de Solr dans un objet WASB (Azure Storage Blob) associé au cluster HDInsight**. Il est conseillé de sauvegarder les données indexées à partir de nœuds de cluster Solr sur un stockage d’objets blob Azure. Pour ce faire, procédez comme suit :

   1. À partir de la session RDP, ouvrez Internet Explorer et accédez à l'URL suivante :

           http://localhost:8983/solr/replication?command=backup

       La réponse doit ressembler à ceci :

           <?xml version="1.0" encoding="UTF-8"?>
           <response>
             <lst name="responseHeader">
               <int name="status">0</int>
               <int name="QTime">9</int>
             </lst>
             <str name="status">OK</str>
           </response>
   2. Dans la session à distance, accédez à {SOLR_HOME}\{Collection}\data. Pour le cluster créé à l’aide de l’exemple de script, il doit s’agir de **C:\apps\dist\solr-4.7.2\example\solr\collection1\data**. Un dossier d’instantanés créé avec un nom semblable à **snapshot.*timestamp*** doit normalement figurer à cet emplacement.
   3. Compressez le dossier d’instantanés et téléchargez-le vers le stockage d’objets blob Azure. À partir de la ligne de commande Hadoop, accédez à l’emplacement du dossier d’instantanés à l’aide de la commande suivante :

             hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

       Cette commande copie l’instantané dans /example/data/ sous le conteneur, dans le compte de stockage par défaut associé au cluster.

## <a name="install-solr-using-aure-powershell"></a>Installer Solr à l’aide d’Azure PowerShell
Consultez [Personnalisation de clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  L’exemple montre comment installer Spark avec Azure PowerShell. Vous devez personnaliser le script pour utiliser [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="install-solr-using-net-sdk"></a>Installer Solr à l’aide du Kit de développement logiciel (SDK) .NET
Consultez [Personnalisation de clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). L’exemple montre comment installer Spark à l’aide du Kit de développement logiciel (SDK) .NET. Vous devez personnaliser le script pour utiliser [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="see-also"></a>Voir aussi
* [Installer et utiliser Solr sur des clusters HDInsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [Créer des clusters Hadoop dans HDInsight](hdinsight-provision-clusters.md): informations générales sur la création de clusters HDInsight.
* [Personnaliser un cluster HDInsight à l’aide d’une action de script][hdinsight-cluster-customize] : informations générales sur la personnalisation de clusters HDInsight à l’aide d’actions de script.
* [Développer des scripts d’action de script pour HDInsight](hdinsight-hadoop-script-actions.md).
* [Installer et utiliser Spark sur les clusters HDInsight][hdinsight-install-spark] : exemple d’action de script sur l’installation de Spark.
* [Installer R sur les clusters HDInsight][hdinsight-install-r] : exemple d’action de script sur l’installation de R.
* [Installer Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install.md): exemple d’action de script sur l’installation de Giraph.

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md

