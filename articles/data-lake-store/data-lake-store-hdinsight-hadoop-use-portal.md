---
title: "Utiliser le portail Azure pour créer des clusters Azure HDInsight avec Data Lake Store | Microsoft Docs"
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
ms.date: 03/02/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: d1d780eb2c635f60409396804c0b8b706e59127b
ms.lasthandoff: 03/07/2017


---
# <a name="create-hdinsight-clusters-with-data-lake-store-by-using-the-azure-portal"></a>Créer des clusters HDInsight avec Data Lake Store à l'aide du portail Azure
> [!div class="op_single_selector"]
> * [Utiliser le portail Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Utiliser PowerShell (pour le stockage par défaut)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Utiliser PowerShell (pour le stockage supplémentaire)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Utiliser Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Cet article montre comment utiliser le portail Azure pour créer des clusters HDInsight avec accès à Azure Data Lake Store. Pour les types de clusters pris en charge, vous pouvez utiliser Data Lake Store comme compte de stockage par défaut ou supplémentaire. 

Lorsque vous utilisez Data Lake Store comme espace de stockage supplémentaire, le compte de stockage par défaut pour les clusters est toujours Stockage Blob Azure et les fichiers associés au cluster (par exemple les journaux) sont écrits dans le stockage par défaut. Toutefois, les données que vous souhaitez traiter peuvent être stockées dans un compte Data Lake Store. L’utilisation de Data Lake Store en tant que compte de stockage supplémentaire n’affecte pas les performances ni la capacité de lecture ou d’écriture sur le stockage à partir du cluster.

Voici quelques considérations importantes pour l’utilisation de HDInsight avec Data Lake Store :

* L’option permettant de créer des clusters HDInsight avec accès à Data Lake Store comme stockage par défaut est disponible si vous utilisez HDInsight version 3.5.

* L’option permettant de créer des clusters HDInsight avec accès à Data Lake Store comme stockage par défaut n’est *pas disponible* pour les clusters HDInsight Premium.

* L’option permettant de créer des clusters HDInsight avec accès à Data Lake Store comme stockage supplémentaire est disponible si vous utilisez HDInsight versions 3.2, 3.4 et 3.5.

* Pour les clusters HBase (Windows et Linux), Data Lake Store n’est *pas pris en charge* comme option de stockage par défaut ou supplémentaire.

* Pour les clusters Storm (Windows et Linux), vous pouvez utiliser Data Lake Store pour écrire des données à partir d’une topologie Storm. Vous pouvez également vous en servir pour stocker des données de référence qui peuvent ensuite être lues par une topologie Storm. Pour obtenir plus d’informations, consultez [Utiliser Data Lake Store dans une topologie Storm](#use-data-lake-store-in-a-storm-topology).


## <a name="prerequisites"></a>Composants requis
Avant de commencer le didacticiel, veillez à ce que vos mots de passe répondent aux exigences suivantes :

* **Un abonnement Azure**. Consultez [Créez votre compte Azure gratuit](https://azure.microsoft.com/pricing/free-trial/).

* **Un compte Azure Data Lake Store**. Suivez les instructions de [Prise en main d’Azure Data Lake Store avec le portail Azure](data-lake-store-get-started-portal.md).

* **Un principal de service Azure Active Directory**. Ce didacticiel montre comment créer un principal de service dans Azure Active Directory (Azure AD). Toutefois, vous devez être administrateur Azure AD pour pouvoir créer un principal du service. Si vous êtes administrateur, vous pouvez ignorer ce prérequis et poursuivre le didacticiel.

 >[!NOTE]
 >Vous pouvez créer un principal de service uniquement si vous être administrateur Azure AD. Votre administrateur Azure AD doit créer un principal de service. Vous pouvez ensuite créer un cluster HDInsight avec Data Lake Store. En outre, le principal de service doit être créé à l’aide d’un certificat, comme décrit dans [Créer un principal du service avec un certificat](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).

## <a name="create-an-hdinsight-cluster-with-access-to-a-data-lake-store"></a>Créer un cluster HDInsight ayant accès à Data Lake Store
Dans cette section, vous allez créer un cluster HDInsight Hadoop qui utilise Data Lake Store comme stockage supplémentaire. Dans cette version, pour un cluster Hadoop, vous ne pouvez utiliser Data Lake Store que comme stockage supplémentaire pour le cluster. Le stockage par défaut est toujours Stockage Blob Azure. Par conséquent, créez tout d’abord le compte de stockage et les conteneurs de stockage requis par le cluster.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Pour démarrer l’approvisionnement d’un cluster HDInsight, suivez les instructions à la page [Création de clusters Hadoop dans HDInsight](../hdinsight/hdinsight-provision-clusters.md).

3. Dans le panneau **Stockage**, spécifiez si vous souhaitez utiliser Stockage Blob ou Data Lake Store comme stockage par défaut, puis effectuez l’une des opérations suivantes :

 * Pour utiliser Data Lake Store comme stockage par défaut, passez à l’étape 4.

 * Pour utiliser Stockage Blob comme stockage par défaut :

    a. Sous **Type de stockage principal**, sélectionnez **Stockage Azure**.

    b. Sous **Méthode de sélection**, procédez de l’une des façons suivantes :      * Pour spécifier un compte de stockage qui fait partie de votre abonnement Azure, sélectionnez **Mon compte de stockage**, puis le compte de stockage.      * Pour spécifier un compte de stockage qui est en dehors de votre abonnement Azure, sélectionnez **Clé d’accès**, puis fournissez les informations du compte de stockage externe.

    c. Sous **Conteneur par défaut**, entrez le nom de conteneur par défaut suggéré par le portail ou spécifiez le vôtre.

 Lorsque vous utilisez Stockage Blob comme stockage par défaut, vous pouvez toujours utiliser Data Lake Store comme stockage supplémentaire pour le cluster. Pour ce faire, cliquez sur **Accès à Data Lake Store**, puis passez à l’étape 5.

 ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Ajouter un principal du service à un cluster HDInsight")

4. Pour utiliser Data Lake Store comme stockage par défaut :

 a. Sous **Type de stockage principal**, cliquez sur **Data Lake Store**.

 b. Sélectionnez un compte Data Lake Store existant, entrez le chemin du dossier racine où les fichiers spécifiques au cluster sont stockés, spécifiez **États-Unis de l’Est 2** comme **Emplacement**, puis cliquez sur **Accès à Data Lake Store**.

 >[!NOTE]
 >Vous pouvez utiliser cette option uniquement avec les clusters HDInsight 3.5 (édition Standard). Dans les clusters HDInsight 3.5, cette option n’est pas disponible pour le type de cluster HBase.

 Dans la capture d’écran ci-dessous, le chemin du dossier racine est /clusters/myhdiadlcluster, où *myhdiadlcluster* est le nom du cluster en cours de création. Dans ce cas, vérifiez que le dossier */clusters* existe déjà dans le compte Data Lake Store. Le dossier *myhdiadlcluster* est créé pendant la création du cluster. De même, si le chemin racine a été défini sur */hdinsight/clusters/data/myhdiadlcluter*, vérifiez que */hdinsight/clusters/data/* existe dans le compte Data Lake Store.

 ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Ajouter un principal du service à un cluster HDInsight")

5. Dans le panneau **Accès à Data Lake Store**, procédez de l’une des façons suivantes :

 * Pour utiliser un principal de service existant, passez à l’étape 6.
 * Pour créer un principal de service :

    a. Dans le panneau **Accès à Data Lake Store**, cliquez sur **Créer**, puis sur **Principal de service**.

    b. Dans le panneau **Create a Service Principal** (Créer un principal du service), entrez les valeurs requises.  

    c. Cliquez sur **Create**.  
    Un certificat et une application Azure AD sont créés automatiquement.

    ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Ajouter un principal du service à un cluster HDInsight")

    Vous pouvez également cliquer sur **Télécharger le certificat** pour télécharger le certificat associé au principal du service que vous avez créé. Le téléchargement du certificat est utile si vous souhaitez utiliser le même principal de service lorsque vous allez créer des clusters HDInsight supplémentaires. Cliquez sur **Sélectionner**.

6. Pour utiliser un principal de service existant :

 a. Dans le panneau **Accès à Data Lake Store**, cliquez sur **Utiliser existant**, puis sur **Principal de service**.

 b. Dans le panneau **Sélectionner un principal de service**, recherchez un principal du service existant. Cliquez sur le principal de service à utiliser, puis sur **Sélectionner**.

 c. Dans le panneau **Accès à Data Lake Store**, chargez le certificat (fichier .pfx) associé au principal de service sélectionné, puis indiquez le mot de passe du certificat.

 ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Ajouter un principal du service à un cluster HDInsight")

7. Dans le panneau **Accès Data Lake Store**, cliquez sur **Accès**.  
Le panneau **Sélectionner des autorisations de fichiers** s’ouvre par défaut. Il répertorie les comptes Data Lake Store dans votre abonnement.

8. Sélectionnez le compte Data Lake Store que vous souhaitez associer au cluster.
 Tous les fichiers et dossiers de ce compte sont répertoriés. Vous pouvez ensuite affecter des autorisations au niveau du fichier ou du dossier. Pour assigner des autorisations à la racine du compte, sélectionnez la case à cocher en regard du nom de compte.

 ![Ajouter un principal du service à un cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Ajouter un principal du service à un cluster HDInsight")

 > [!NOTE]
 > Si vous utilisez le compte Data Lake Store comme stockage par défaut pour un cluster, affectez les autorisations au principal de service à la racine du compte Data Lake Store.

9. Pour affecter des autorisations pour fichiers ou des dossiers au sein d’un compte, dans la fenêtre **Sélectionner des autorisations de fichiers**, sélectionnez le compte Data Lake Store.

10. Dans le volet de droite, sélectionnez les fichiers ou dossiers auxquels vous souhaitez affecter des autorisations, sélectionnez les autorisations (lecture, écriture ou exécution), spécifiez si les autorisations s’appliquent de manière récursive aux éléments enfants, puis cliquez sur **Sélectionner**.

    ![Affecter des autorisations de principal de service au cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Affecter des autorisations de principal de service au cluster HDInsight")

11. Dans la fenêtre **Affecter les autorisations sélectionnées**, pour affecter des autorisations pour le principal de service Azure Active Directory au compte, fichier ou dossier sélectionné, cliquez sur **Exécuter**.

    ![Affecter des autorisations de principal de service au cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-2.png "Affecter des autorisations de principal de service au cluster HDInsight")

12. Une fois les autorisations correctement attribuées, cliquez sur **Terminé** dans chaque panneau jusqu’à revenir au panneau **Accès à Data Lake Store**.

13. Cliquez sur **Sélectionner** dans les panneaux **Accès à Data Lake Store** et poursuivez la création du cluster comme décrit dans [Création de clusters Hadoop dans HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

14. Une fois que l’installation du cluster est terminée, dans le panneau du cluster, vérifiez vos résultats en effectuant une des deux opérations suivantes :

 * Pour vérifier que le stockage associé au cluster est le compte Data Lake Store que vous avez spécifié, cliquez sur **Comptes de stockage** dans le panneau gauche.

        ![Add service principal to HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Add service principal to HDInsight cluster")

 * Pour vérifier que le principal de service est correctement associé au cluster HDInsight, cliquez sur **Accès à Data Lake Store** dans le volet gauche.

       ![Add service principal to HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Add service principal to HDInsight cluster")

## <a name="examples"></a>Exemples

Une fois que vous avez configuré le cluster avec Data Lake Store comme stockage, voici quelques exemples de l’utilisation du cluster HDInsight pour analyser les données stockées dans Data Lake Store.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-primary-storage"></a>Exécuter une requête Hive sur des données stockées dans Data Lake Store (comme stockage principal)

Pour exécuter une requête Hive, utilisez l’interface des vues Hive du portail Ambari. Pour obtenir des instructions sur l'utilisation des vues Hive Ambari, consultez [Utiliser la vue Hive avec Hadoop dans HDInsight](../hdinsight/hdinsight-hadoop-use-hive-ambari-view.md).

Lorsque vous utilisez les données dans Data Lake Store, quelques modifications doivent être apportées.

Si vous utilisez, par exemple, le cluster que vous avez créé avec Data Lake Store comme stockage principal, le chemin d’accès aux données est le suivant : *adl://<nom_compte_Data_Lake_Store>/azuredatalakestore.net/path/to/file*. Une requête Hive permettant de créer une table à partir des exemples de données stockés dans le compte Data Lake Store ressemble à ceci :

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Descriptions :
* `adl://hdiadlstorage.azuredatalakestore.net/` est la racine du compte Data Lake Store.
* `/clusters/myhdiadlcluster` est la racine des données du cluster que vous avez spécifiée lors de la création du cluster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` est l’emplacement de l’exemple de fichier que vous utilisez dans la requête.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-additional-storage"></a>Exécuter une requête Hive sur des données dans Data Lake Store (comme stockage supplémentaire)

Si le cluster que vous avez créé utilise Stockage Blob comme stockage par défaut, les exemples de données ne se trouvent pas dans le compte Azure Data Lake Store utilisé en tant que stockage supplémentaire. Dans ce cas, commencez par transférer les données de Stockage Blob vers Data Lake Store, puis exécutez les requêtes selon la procédure décrite ci-dessus.

Pour plus d’informations sur la copie de données de Stockage Blob vers Data Lake Store, consultez les articles suivants :

* [Utiliser Distcp pour copier des données entre Stockage Blob Azure et Data Lake Store](data-lake-store-copy-data-wasb-distcp.md)
* [Copier des données de Stockage Blob Azure vers Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-store-with-a-spark-cluster"></a>Utiliser Data Lake Store avec un cluster Spark
Vous pouvez utiliser un cluster Spark pour exécuter des tâches Spark sur des données stockées dans Data Lake Store. Pour plus d’informations, consultez [Utiliser le cluster HDInsight Spark pour analyser les données dans Data Lake Store](../hdinsight/hdinsight-apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-store-in-a-storm-topology"></a>Utiliser Data Lake Store dans une topologie Storm
Vous pouvez utiliser le Data Lake Store pour écrire des données à partir d’une topologie Storm. Pour obtenir des instructions sur la réalisation de ce scénario, consultez [Utilisation d’Azure Data Lake Store avec Apache Storm sur HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md).

## <a name="see-also"></a>Voir aussi
* [PowerShell : Créer un cluster HDInsight pour utiliser Data Lake Store](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

