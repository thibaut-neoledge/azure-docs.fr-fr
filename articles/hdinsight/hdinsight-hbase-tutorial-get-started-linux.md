---
title: "Prise en main de HBase sur Azure HDInsight | Microsoft Docs"
description: "Suivez ce didacticiel HBase pour apprendre à utiliser Apache HBase avec Hadoop dans HDInsight. Créez des tables à partir de l’interpréteur de commandes HBase et interrogez-les à l’aide de Hive."
keywords: apache hbase,hbase,shell hbase,didacticiel hbase
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 4d6a2658-6b19-4268-95ee-822890f5a33a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 21d8dff230e045607b70013f4eabf1bfe8ec3993
ms.lasthandoff: 03/25/2017


---
# <a name="hbase-tutorial-get-started-using-apache-hbase-in-hdinsight"></a>Didacticiel HBase : prise en main de HBase avec Hadoop dans HDInsight Linux

Découvrez comment créer un cluster HBase dans HDInsight, créer des tables HBase et les interroger à l’aide de Hive. Pour obtenir des informations générales sur HBase, voir [Vue d’ensemble de HDInsight HBase][hdinsight-hbase-overview].

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel sur HBase, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Secure Shell(SSH)](hdinsight-hadoop-linux-use-ssh-unix.md). 
* [curl](http://curl.haxx.se/download.html).

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>Créer un cluster HBase
La procédure suivante utilise un modèle Azure Resource Manager pour créer un cluster HBase basé sur Linux version 3.4 et le compte Stockage Azure dépendant par défaut. Pour comprendre les paramètres utilisés dans la procédure et d’autres méthodes de création de cluster, consultez [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Cliquez sur l’image suivante pour ouvrir le modèle dans le portail Azure. Le modèle est situé dans un conteneur d’objets blob public. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Dans le panneau **Déploiement personnalisé**, entrez les valeurs suivantes :
   
   * **Abonnement** : sélectionnez votre abonnement Azure à utiliser pour créer le cluster.
   * **Groupe de ressources** : créez un groupe Azure Resource Management ou utilisez un groupe existant.
   * **Emplacement** : spécifiez l’emplacement du groupe de ressources. 
   * **ClusterName**: entrez un nom pour le cluster HBase que vous allez créer.
   * **Nom d’utilisateur et mot de passe de cluster** : le nom de connexion par défaut est **admin**.
   * **Nom d’utilisateur SSH et mot de passe** : le nom d’utilisateur par défaut est **sshuser**.  Vous pouvez le renommer.
     
     Tous les autres paramètres sont facultatifs.  
     
     Chaque cluster possède une dépendance de compte Stockage Azure. Après avoir supprimé un cluster, les données sont conservées dans le compte de stockage. Le nom du compte de stockage par défaut du cluster est le nom du cluster suivi du suffixe « store ». Il est codé en dur dans la section des variables du modèle.
3. Sélectionnez **J’accepte les termes et conditions mentionnés ci-dessus**, puis cliquez sur **Acheter**. La création d’un cluster prend environ 20 minutes.

> [!NOTE]
> Après la suppression d’un cluster HBase, vous pouvez créer un autre cluster HBase à l’aide du même conteneur d’objets blob par défaut. Le nouveau cluster utilisera les tables HBase créées dans le cluster d'origine. Pour éviter toute incohérence, nous vous recommandons de désactiver les tables HBase avant de supprimer le cluster.
> 
> 

## <a name="create-tables-and-insert-data"></a>Créer des tables et insérer des données
Vous pouvez utiliser SSH pour vous connecter à des clusters HBase et utiliser ensuite le Shell HBase pour créer des tables HBase et insérer et interroger des données. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

Pour la plupart des utilisateurs, les données s’affichent sous la forme tabulaire :

![Données tabulaires HBase HDInsight][img-hbase-sample-data-tabular]

Dans HBase, qui est une implémentation de BigTable, certaines données ont l’aspect suivant :

![Données bigtable HBase HDInsight][img-hbase-sample-data-bigtable]

Cela deviendra plus clair une fois la procédure suivante terminée.  

**Pour utiliser l’interpréteur de commandes HBase**

1. Depuis SSH, exécutez la commande suivante :
   
        hbase shell
2. Créez une HBase contenant deux familles de colonne :
   
        create 'Contacts', 'Personal', 'Office'
        list
3. Insérez des données :
   
        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'
   
    ![Interpréteur de commandes HBase Hadoop HDInsight][img-hbase-shell]
4. Récupérez une seule ligne :
   
        get 'Contacts', '1000'
   
    Comme il n’y a qu’une seule ligne, les résultats sont les mêmes que lors de l’utilisation de la commande d’analyse.
   
    Pour en savoir plus sur le schéma de la table Hbase, voir [Introduction à la conception de schéma HBase][hbase-schema]. Pour obtenir plus de commandes HBase, consultez le [Guide de référence Apache HBase][hbase-quick-start].
5. Quittez l’interpréteur de commandes.
   
        exit

**Pour charger des données en bloc dans la table de contacts HBase**

HBase propose plusieurs méthodes pour charger des données dans des tables.  Pour en savoir plus, consultez la rubrique [Chargement en bloc](http://hbase.apache.org/book.html#arch.bulk.load).

Un exemple de fichier de données a été chargé dans un conteneur d’objets blob public, *wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*.  Le contenu du fichier de données est le suivant :

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

Vous pouvez créer un fichier texte et le télécharger sur votre propre compte de stockage si vous le souhaitez. Pour obtenir des instructions, consultez la rubrique [Téléchargement de données pour les tâches Hadoop dans HDInsight][hdinsight-upload-data].

> [!NOTE]
> Cette procédure utilise la table de contacts HBase créée dans la dernière procédure.
> 
> 

1. Depuis SSH, exécutez la commande suivante pour transformer le fichier de données en StoreFiles et le stocker sur un chemin d’accès relatif spécifié par Dimporttsv.bulk.output :  Si vous êtes dans le Shell HBase, utilisez la commande Quitter pour sortir.
   
        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
2. Exécutez la commande suivante pour charger les données à partir de /example/data/storeDataFileOutput vers la table HBase :
   
        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
3. Vous pouvez ouvrir l’interpréteur de commandes HBase et utiliser la commande d’analyse pour répertorier le contenu de la table.

## <a name="use-hive-to-query-hbase"></a>Utiliser Hive pour interroger HBase
Vous pouvez interroger les données des tables HBase à l’aide de Hive. Cette section crée une table Hive qui peut être mappée à la table HBase et l'utilise pour interroger les données de votre table HBase.

> [!NOTE]
> Si Hive et HBase se trouvent sur des clusters différents d’un même réseau virtuel, vous devez passer le quorum zookeeper lors de l’appel de l’interpréteur de commandes Hive :
>
>       hive --hiveconf hbase.zookeeper.quorum=zk0-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net,zk1-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net,zk2-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net --hiveconf zookeeper.znode.parent=/hbase-unsecure  
>
>

1. Ouvrez **PuTTY**et connectez-vous au cluster.  Consultez les instructions de la procédure précédente.
2. Ouvrez l’interpréteur de commandes Hive.
   
       hive
       
3. Exécutez le script HiveQL suivant pour créer une table Hive mappant à la table HBase. Avant d'exécuter cette instruction, vous devez avoir créé l'exemple de table référencé dans ce didacticiel en utilisant l’interpréteur de commandes HBase.
   
        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');
4. Exécutez le script HiveQL suivant pour interroger les données dans la table HBase :
   
         SELECT count(*) FROM hbasecontacts;

## <a name="use-hbase-rest-apis-using-curl"></a>Utilisation des API REST HBase à l’aide de Curl
> [!NOTE]
> Lorsque vous utilisez Curl ou toute autre communication REST avec WebHCat, vous devez authentifier les demandes en fournissant le nom d'utilisateur et le mot de passe de l'administrateur du cluster HDInsight. Vous devez également utiliser le nom du cluster dans l’URI (Uniform Resource Identifier) utilisé pour envoyer les demandes au serveur.
> 
> Pour les commandes de cette section, remplacez **USERNAME** par l’utilisateur à authentifier sur le cluster et **PASSWORD** par le mot de passe du compte d’utilisateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
> 
> L’API REST est sécurisée à l’aide de l’ [authentification de base](http://en.wikipedia.org/wiki/Basic_access_authentication). Vous devez toujours effectuer les demandes à l’aide du protocole Secure HTTP (HTTPS) pour aider à vous assurer que vos informations d’identification sont envoyées en toute sécurité sur le serveur.
> 
> 

1. À partir d’une ligne de commande, exécutez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight.
   
        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
   
    Vous devez recevoir une réponse ayant l'aspect suivant :
   
        {"status":"ok","version":"v1"}
   
    Les paramètres utilisés dans cette commande sont les suivants :
   
   * **-u** : le nom d’utilisateur et le mot de passe utilisés pour authentifier la demande.
   * **-G** : indique qu’il s’agit d’une demande GET.
2. Utilisez la commande suivante pour répertorier les tables HBase existantes :
   
        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/hbaserest/
3. Utilisez la commande suivante pour créer une table HBase avec deux familles de colonnes :
   
        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
        -v
   
    Le schéma est fourni au format JSon.
4. Utilisez la commande suivante pour insérer des données :
   
        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
        -v
   
    Vous devez coder en base64 les valeurs spécifiées dans le commutateur -d.  Dans l’exemple :
   
   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Personal:Name
   * Sm9obiBEb2xl: John Dole
     
     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) vous permet d’insérer plusieurs valeurs (par lot).
5. Utilisez la commande suivante pour obtenir une ligne :
   
        curl -u <UserName>:<Password> \
        -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
        -H "Accept: application/json" \
        -v

Pour plus d’informations sur HBase Rest, voir le [Guide de référence Apache HBase](https://hbase.apache.org/book.html#_rest).

>
> [!NOTE]
> Thrift n’est pas pris en charge par HBase dans HDInsight.
>

## <a name="check-cluster-status"></a>Vérification du statut du cluster
HBase dans HDInsight est livré avec une interface utilisateur web pour la surveillance des clusters. Elle vous permet de demander des statistiques ou des informations sur les régions.

**Pour accéder à l’interface utilisateur principale HBase**

1. Ouvrez l’interface utilisateur web d’Ambari à l’adresse https://&lt;Clustername>.azurehdinsight.net.
2. Dans le menu de gauche, cliquez sur **HBase**.
3. Cliquez sur **Liens rapides** en haut de la page, pointez vers le lien de nœud Zookeeper actif, puis cliquez sur **HBase Master UI (Interface utilisateur principale HBase)**.  L’interface utilisateur est ouverte dans un autre onglet de navigateur :

  ![Interface utilisateur principale HDInsight HBase](./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

  L’interface utilisateur principale HBase comporte les sections suivantes :

  - serveurs de région
  - serveurs de sauvegarde
  - tables
  - tâches
  - attributs logiciels

## <a name="delete-the-cluster"></a>Suppression du cluster
Pour éviter toute incohérence, nous vous recommandons de désactiver les tables HBase avant de supprimer le cluster.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel HBase pour HDInsight, vous avez appris à créer un cluster HBase, à créer des tables et à afficher les données de ces tables à partir de l’interpréteur de commandes HBase. Vous avez également appris à utiliser une requête Hive pour interroger les données des tables HBase et à utiliser les API REST C# HBase pour créer une table HBase et en extraire les données.

Pour plus d'informations, consultez les rubriques suivantes :

* [Vue d’ensemble de HDInsight HBase][hdinsight-hbase-overview] : HBase est une base de données NoSQL open source Apache basée sur Hadoop qui fournit un accès aléatoire et une forte cohérence pour de grandes quantités de données non structurées et semi-structurées.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png

