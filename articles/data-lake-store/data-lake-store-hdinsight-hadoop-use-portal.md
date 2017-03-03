---
title: "Utiliser un navigateur web pour créer Azure HDInsight et Data Lake Store | Microsoft Docs"
description: "Utiliser le portail Azure pour créer et utiliser les clusters HDInsight avec Azure Data Lake Store"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/16/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9e480c13f48e93da32ff5a3c8d3064e98fed0265
ms.openlocfilehash: 0ec19832d395547e8ebd3eee0d44dcf466a2ace7
ms.lasthandoff: 02/17/2017


---
# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-portal"></a>Créer un cluster HDInsight avec Data Lake Store à l'aide du portail Azure
> [!div class="op_single_selector"]
> * [Utilisation du portail](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Utilisation de PowerShell (pour le stockage par défaut)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Utilisation de PowerShell (pour le stockage supplémentaire)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Utilisation du gestionnaire des ressources](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Apprenez à utiliser le portail Azure pour créer un cluster HDInsight avec accès à Azure Data Lake Store. Pour les types de clusters pris en charge, Data Lake Store est utilisé comme compte de stockage par défaut ou supplémentaire. Lorsque Data Lake Store est utilisé comme espace de stockage supplémentaire, le compte de stockage par défaut pour les clusters est toujours Azure Storage Blob (WABS), et les fichiers associés au cluster (par exemple, les journaux, etc.) sont écrits dans le stockage par défaut, tandis que les données que vous souhaitez traiter peuvent être stockées dans un compte Data Lake Store. L’utilisation de Data Lake Store en tant que compte de stockage supplémentaire n’affecte pas les performances ni la capacité de lecture/écriture sur le stockage à partir du cluster.

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>Utilisation de Data Lake Store pour le stockage de cluster HDInsight

Voici quelques considérations importantes pour l’utilisation de HDInsight avec Data Lake Store :

* L’option permettant de créer des clusters HDInsight avec accès au Data Lake Store comme stockage par défaut est disponible si vous utilisez HDInsight version 3.5.

* L’option permettant de créer des clusters HDInsight avec accès au Data Lake Store comme stockage supplémentaire est disponible si vous utilisez HDInsight versions 3.2, 3.4 et 3.5.

* Pour les clusters HBase (Windows et Linux), Data Lake Store n'est **pas pris en charge** comme option de stockage par défaut ou supplémentaire.

* Pour les clusters Storm (Windows et Linux), Data Lake Store peut être utilisé pour écrire des données à partir d’une topologie Storm. Le Data Lake Store peut également servir à stocker des données de référence qui peuvent ensuite être lues par une topologie Storm. Pour obtenir plus d’informations, consultez [Utiliser Data Lake Store dans une topologie Storm](#use-data-lake-store-in-a-storm-topology).


## <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Compte Azure Data Lake Store**. Suivez les instructions de [Prise en main d'Azure Data Lake Store avec le portail Azure](data-lake-store-get-started-portal.md).

* **Principal du service Azure Active Directory**. Les étapes de ce didacticiel indiquent comment créer un principal du service dans Azure AD. Toutefois, vous devez être administrateur Azure AD pour pouvoir créer un principal du service. Si vous êtes administrateur Azure AD, vous pouvez ignorer ce prérequis et poursuivre le didacticiel.

    **Si vous n’êtes pas administrateur Azure AD**, vous ne pouvez pas effectuer les étapes nécessaires à la création d’un principal du service. Dans ce cas, votre administrateur Azure AD doit d’abord créer un principal du service. Vous pourrez ensuite créer un cluster HDInsight avec Data Lake Store. En outre, le principal du service doit être créé à l’aide d’un certificat, comme décrit dans [Create a service principal with certificate](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate) (Créer un principal du service avec certificat).

## <a name="create-an-hdinsight-cluster-with-access-to-azure-data-lake-store"></a>Créer un cluster HDInsight ayant accès au Data Lake Store.
Dans cette section, vous créez un cluster HDInsight Hadoop qui utilise le Data Lake Store comme stockage supplémentaire. Dans cette version, pour un cluster Hadoop, Data Lake Store ne peut être utilisé que comme stockage supplémentaire pour le cluster. Le stockage par défaut sera toujours les objets blob Azure Storage (WASB). Par conséquent, nous allons tout d'abord créer le compte de stockage et les conteneurs de stockage requis par le cluster.

1. Inscrivez-vous au nouveau [portail Azure](https://portal.azure.com).

2. Suivez les étapes de la [Création de clusters Hadoop dans HDInsight](../hdinsight/hdinsight-provision-clusters.md) pour lancer l'approvisionnement d'un cluster HDInsight.

3. Dans le panneau **Stockage**, spécifiez si vous souhaitez utiliser Azure Storage (WASB) ou Data Lake Store comme stockage par défaut. Si vous voulez utiliser Azure Data Lake Store comme stockage par défaut, passez à l’étape suivante.

    Si vous souhaitez utiliser Azure Storage Blobs comme stockage par défaut, pour **Type de stockage principal**, cliquez sur **Azure Storage**. Ensuite, pour **Méthode de sélection**, vous pouvez choisir **Mes abonnements** si vous souhaitez spécifier un compte de stockage qui fait partie de votre abonnement Azure, puis sélectionner le compte de stockage. Sinon, cliquez sur **Clé d’accès** et fournissez les informations du compte de stockage que vous souhaitez choisir en dehors de votre abonnement Azure. Pour **Conteneur par défaut**, vous pouvez choisir d’utiliser le nom de conteneur par défaut suggéré par le portail ou spécifier le vôtre. 

    Lorsque vous utilisez les objets blob de stockage Azure comme stockage par défaut, vous pouvez toujours utiliser Azure Data Lake Store comme stockage par défaut pour le cluster. Pour ce faire, cliquez sur **Accès Data Lake Store**, puis passez à l’étape 5.

    ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Ajouter un principal du service à un cluster HDInsight")


4. Si vous souhaitez utiliser Azure Data Lake Store comme stockage par défaut, pour **Type de stockage principal**, cliquez sur **Data Lake Storage**. Sélectionnez un compte Data Lake Store existant, indiquez le chemin du dossier racine où les fichiers spécifiques au cluster seront stockés, choisissez **Est des États-Unis 2** comme **Emplacement**, puis cliquez sur **Accès Data Lake Store**. Vous pouvez utiliser cette option uniquement avec les clusters HDInsight 3.5 (édition Standard). Dans les clusters HDInsight 3.5, cette option n’est pas disponible pour le type de cluster HBase.

    Dans la capture d’écran ci-dessous, le chemin du dossier racine est /clusters/myhdiadlcluster, où **myhdiadlcluster** est le nom du cluster en cours de création. Dans ce cas, assurez-vous que le dossier **/clusters** existe déjà dans le compte Data Lake Store. Le dossier **myhdiadlcluster** sera créé pendant la création du cluster. De même, si le chemin racine a ét définie sur /hdinsight/clusters/data/myhdiadlcluter, vous devez vous assurer que **/hdinsight/clusters/data/** existe déjà dans le compte Data Lake Store.

    ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Ajouter un principal du service à un cluster HDInsight")

5. Dans le panneau **Accès Data Lake Store**, vous pouvez sélectionner un principal du service existant ou en créer un. Si vous souhaitez utiliser un principal du service existant, passez à l’étape suivante.

    Si vous souhaitez créer un principal du service, dans le panneau **Accès Data Lake Store**, cliquez sur **Nouveau**. Cliquez sur **Principal du service** puis, dans le panneau **Créer un principal du service**, fournissez les valeurs pour créer un principal du service. Dans ce cadre, un certificat et une application Azure Active Directory sont également créés. Cliquez sur **Create**.

    ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Ajouter un principal du service à un cluster HDInsight")

    Vous pouvez également cliquer sur **Télécharger le certificat** pour télécharger le certificat associé au principal du service que vous avez créé. Cela est utile si vous souhaitez utiliser le même principal du service à l’avenir, tout en créant des clusters HDInsight supplémentaires. Cliquez sur **Sélectionner**.

6. Si vous souhaitez utiliser un principal du service existant, dans le panneau **Accès Data Lake Store**, cliquez sur **Existant**, puis sur **Principal du service**. Dans le panneau **Sélectionner un principal du service**, recherchez un principal du service existant. Cliquez sur un nom de principal du service, puis sur **Sélectionner**.

    ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Ajouter un principal du service à un cluster HDInsight")

    Dans le panneau **Accès Data Lake Store**, chargez le certificat (.pfx) associé au principal du service que vous avez sélectionné, puis indiquez le mot de passe du certificat.

6. Dans le panneau **Accès Data Lake Store**, cliquez sur **Accès**. Dans le panneau suivant, l'option **Sélectionner les autorisations de fichier** est déjà activée par défaut et répertorie tous les comptes Data Lake Store de votre abonnement. Cliquez sur le compte Data Lake Store que vous souhaitez associer au cluster afin de répertorier les fichiers et dossiers de ce compte. Vous pouvez ensuite affecter des autorisations au niveau du fichier ou du dossier. Si vous souhaitez associer les autorisations à la racine du compte, sélectionnez la case à cocher en regard du nom de compte.

    ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Ajouter un principal du service à un cluster HDInsight")

    > [!NOTE]
    > Si vous utilisez le compte Data Lake Store comme stockage par défaut pour un cluster, vous **devez** affecter les autorisations au principal du service à la racine du compte Data Lake Store.

7. Si vous souhaitez affecter des autorisations pour fichiers ou des dossiers au sein d’un compte, sélectionnez le compte Data Lake Store afin d'afficher les fichiers ou dossiers dans le volet suivant. Sélectionnez les fichiers ou dossiers, les autorisations (LECTURE/ÉCRITURE/EXÉCUTION) que vous souhaitez attribuer à ces derniers, spécifiez si les autorisations s’appliquent de manière récursive aux éléments enfants, puis cliquez sur **Sélectionner**.

    ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Ajouter un principal du service à un cluster HDInsight")

8. Dans l’écran suivant, cliquez sur **Exécuter** pour attribuer les autorisations du principal du service Azure Active Directory au compte, fichier ou dossier sélectionné.

    ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-2.png "Ajouter un principal du service à un cluster HDInsight")

9. Une fois l’autorisation correctement attribuée, cliquez sur **Terminé** sur tous les panneaux jusqu’à revenir au panneau **Accès Data Lake Store**.

4. Cliquez sur **Sélectionner** sur les panneaux **Accès Data Lake Store** et poursuivez la création du cluster comme décrit dans [Création de clusters Hadoop dans HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

10. Une fois que le cluster est configuré, vous pouvez vérifier que le stockage associé pour le cluster est le compte Data Lake Store que vous avez spécifié. Vous pouvez le vérifier en cliquant sur l’onglet **Comptes de stockage** dans le panneau du cluster. 

    ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Ajouter un principal du service à un cluster HDInsight")

    Vous pouvez également vérifier que le principal du service est associé au cluster HDInsight. Pour ce faire, cliquez sur **Accès Data Lake Store** pour voir le principal du service associé.

    ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Ajouter un principal du service à un cluster HDInsight")

## <a name="show-me-some-examples"></a>Voici quelques exemples :

Après avoir configuré le cluster avec Data Lake Store comme stockage, voici quelques exemples d’utilisation du cluster HDInsight pour analyser les données stockées dans Data Lake Store.

### <a name="run-a-hive-query-against-data-stored-in-data-lake-store-as-primary-storage"></a>Exécuter une requête Hive sur des données stockées dans Data Lake Store (en tant que stockage primaire)

Pour exécuter une requête Hive, vous pouvez utiliser l’interface des vues Hive du portail Ambari. Pour obtenir des instructions sur l'utilisation des vues Hive Ambari, consultez [Utiliser la vue Hive avec Hadoop dans HDInsight](../hdinsight/hdinsight-hadoop-use-hive-ambari-view.md). Il existe plusieurs choses que vous devrez modifier lorsque vous travaillez avec des données dans Data Lake Store.

* Si vous considérez l’exemple du cluster créé avec Data Lake Store en tant que stockage primaire, le chemin d’accès aux données sera le suivant : `adl://<data_lake_store_account_name>/azuredatalakestore.net/path/to/file`. Une requête Hive permettant de créer une table à partir des exemples de données stockées dans le compte Data Lake Store ressemblera à ceci :

        CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Dans la requête ci-dessus,

* `adl://hdiadlstorage.azuredatalakestore.net/` est la racine du compte Data Lake Store.
* `/clusters/myhdiadlcluster` est la racine des données du cluster que vous avez spécifiée lors de la création du cluster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` est l’emplacement de l'exemple de fichier que vous utilisez dans la requête

### <a name="run-a-hive-query-against-data-stored-in-data-lake-store-as-additional-storage"></a>Exécuter une requête Hive sur des données stockées dans Data Lake Store (en tant que stockage supplémentaire)

Si le cluster créé utilise le stockage Azure en tant que stockage par défaut, les exemples de données ne se trouveront pas dans le compte Azure Data Lake Store utilisé en tant que stockage supplémentaire. Dans de tels cas, vous devez préalablement transférer les données du stockage Azure vers Azure Data Lake Store, puis exécuter les requêtes selon la procédure décrite ci-dessus.

Pour plus d’informations sur la copie de données du stockage Azure vers Azure Data Lake Store, consultez les rubriques suivantes :

* [Utiliser Distcp pour copier des données entre des objets blob Stockage Azure et Data Lake Store](data-lake-store-copy-data-wasb-distcp.md)
* [Copier des données d’objets blob Stockage Azure vers Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md) 

### <a name="use-data-lake-store-with-spark-cluster"></a>Utilisation de Data Lake Store avec Spark cluster
Vous pouvez utiliser un cluster Spark pour exécuter des tâches Spark sur des données stockées dans Data Lake Store. Pour obtenir des instructions à ce sujet, consultez [Utiliser le cluster HDInsight Spark pour analyser les données dans Data Lake Store](../hdinsight/hdinsight-apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-store-in-a-storm-topology"></a>Utiliser Data Lake Store dans une topologie Storm
Vous pouvez utiliser le Data Lake Store pour écrire des données à partir d’une topologie Storm. Pour obtenir des instructions sur la réalisation de ce scénario, consultez [Utilisation d’Azure Data Lake Store avec Apache Storm sur HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md).

## <a name="see-also"></a>Voir aussi
* [PowerShell : Créer un cluster HDInsight pour utiliser Data Lake Store](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

