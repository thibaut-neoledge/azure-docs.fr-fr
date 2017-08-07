---
title: "Créer un cluster Hadoop à l’aide de comptes de stockage avec transfert sécurisé dans Azure HDInsight | Microsoft Docs"
description: "Découvrez comment créer des clusters HDInsight à l’aide de comptes de stockage Azure doté du transfert sécurisé."
keywords: "prise en main hadoop, hadoop linux, démarrage rapide hadoop, transfert sécurisé, compte de stockage azure"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/02/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: f89595c6721c00435e714368905ae48a542f8cb9
ms.contentlocale: fr-fr
ms.lasthandoff: 08/03/2017

---
# <a name="create-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Créer un cluster Hadoop à l’aide de comptes de stockage avec transfert sécurisé dans Azure HDInsight

La fonctionnalité [Transfert sécurisé requis](../storage/storage-require-secure-transfer.md) améliore la sécurité de votre compte de stockage Azure en appliquant toutes les demandes à votre compte via une connexion sécurisée. Cette fonctionnalité et le schéma wasbs sont uniquement pris en charge par les clusters HDInsight 3.6 ou version ultérieure. 

>[!NOTE] 
> La création de clusters avec un compte de stockage doté du transfert sécurisé via le kit de développement logiciel (SDK) .NET n’est pas prise en charge actuellement. La solution de contournement consiste à définir « wasbs » dans la propriété « fs.defaultFS » de la configuration du site principal comme élément de ClusterCreateParametersExtended.

## <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Abonnement Azure**: pour créer un compte d’essai gratuit d’une durée d’un mois, accédez à [azure.microsoft.com/free](https://azure.microsoft.com/free).
* **Un compte de stockage Azure doté du transfert sécurisé**. Pour obtenir des instructions, consultez [Créez un compte de stockage.](../storage/storage-create-storage-account.md#create-a-storage-account) et [Exiger un transfert sécurisé](../storage/storage-require-secure-transfer.md).
* **Un conteneur d’objets blob dans le compte de stockage**. 
## <a name="create-cluster"></a>Créer un cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Cette section vous permet de créer un cluster Hadoop dans HDInsight à l’aide d’un [modèle Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). Le modèle se trouve dans un [conteneur public](https://hditutorialdata.blob.core.windows.net/securetransfer/azuredeploy-new.json). Aucune expérience avec le modèle Resource Manager n’est requise pour ce didacticiel. Pour obtenir d’autres méthodes de création de cluster et comprendre les propriétés utilisées dans ce didacticiel, consultez [Création de clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Cliquez sur l’image suivante pour vous connecter à Azure et ouvrir le modèle Resource Manager dans le portail Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fsecuretransfer%2Fazuredeploy-new.json" target="_blank"><img src="./media/hdinsight-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Suivez les instructions pour créer le cluster avec les spécifications suivantes : 

    - Indiquez HDInsight version 3.6.  La version par défaut est 3.5. La version 3.6 ou une version ultérieure est requise.
    - Indiquez un compte de stockage doté du transfert sécurisé.
    - Utilisez un nom court pour le compte de stockage.
    - Le compte de stockage et le conteneur d’objets blob doivent être créés au préalable. 

    Pour obtenir des instructions, consultez [Créer un cluster](./hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). 

Si vous utilisez l’action de script pour fournir vos propres fichiers de configuration, vous devez utiliser wasbs dans les paramètres suivants :

- fs.defaultFS (site principal)
- spark.eventLog.dir 
- spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>Ajouter des comptes de stockage

Plusieurs options vous permettent d’ajouter des comptes de stockage dotés du transfert sécurisé :

- Modifiez le modèle Azure Resource Manager dans la dernière section.
- Créez un cluster à l’aide du [portail Azure](https://portal.azure.com) et indiquez le compte de stockage lié.
- Utilisez l’action de script pour ajouter des comptes de stockage doté du transfert sécurisé à un cluster HDInsight existant.  Pour plus d’informations, consultez [Ajouter des comptes de stockage supplémentaires à HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à créer un cluster HDInsight et à activer le transfert sécurisé pour les comptes de stockage.

Pour en savoir plus sur l’analyse des données avec HDInsight, consultez les articles suivants :

* Pour en savoir plus sur l’utilisation de Hive avec HDInsight, y compris comment exécuter des requêtes Hive à partir de Visual Studio, consultez la page [Utilisation de Hive avec HDInsight][hdinsight-use-hive].
* Pour en savoir plus sur Pig, un langage utilisé pour transformer les données, consultez la page [Utilisation de Pig avec HDInsight][hdinsight-use-pig].
* Pour en savoir plus sur MapReduce, un moyen d’écrire des programmes pour traiter les données sur Hadoop, consultez la page [Utilisation de MapReduce avec HDInsight][hdinsight-use-mapreduce].
* Pour en savoir plus sur l’utilisation des outils HDInsight pour Visual Studio pour analyser les données sur HDInsight, consultez la page [Prise en main des outils Hadoop de Visual Studio pour HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).

Pour plus d’informations sur la façon dont HDInsight stocke les données ou sur la façon d’obtenir des données dans HDInsight, consultez les articles suivants :

* Pour plus d’informations sur la façon dont HDInsight utilise le stockage Azure, consultez la page [Use Azure Storage with HDInsight](hdinsight-hadoop-use-blob-storage.md) (Utilisation du stockage Azure avec HDInsight).
* Pour plus d’informations sur le téléchargement de données dans HDInsight, consultez la page [Téléchargement de données dans HDInsight][hdinsight-upload-data].

Pour en savoir plus sur la création ou la gestion d’un cluster HDInsight, consultez les articles suivants :

* Pour en savoir plus sur la gestion de votre cluster HDInsight Linux, consultez la page [Gestion des clusters HDInsight à l’aide d’Ambari](hdinsight-hadoop-manage-ambari.md).
* Pour en savoir plus sur les options que vous pouvez sélectionner pendant la création d’un cluster HDInsight, consultez la page [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Si vous maîtrisez Linux et Hadoop, mais que vous souhaitez connaître les spécificités de Hadoop sur HDInsight, consultez la page [Utilisation de HDInsight sur Linux](hdinsight-hadoop-linux-information.md). Cet article vous fournit les informations suivantes :
  
  * les URL correspondant aux services hébergés sur le cluster, tels qu'Ambari et WebHCat
  * l'emplacement des fichiers Hadoop et des exemples sur le système de fichiers local
  * l'utilisation de stockage d'Azure Storage (WASB) au lieu de HDFS, en tant que stockage de données par défaut

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md



