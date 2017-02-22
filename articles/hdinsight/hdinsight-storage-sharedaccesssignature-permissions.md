---
title: "Restreindre l’accès HDInsight aux données à l’aide des signatures d’accès partagé"
description: "Découvrez comment utiliser les signatures d’accès partagé pour limiter l’accès HDInsight aux données stockées dans des objets blob de stockage Azure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 7bcad2dd-edea-467c-9130-44cffc005ff3
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: ccd1dffda19718a434fc09bb74a536714799740a
ms.openlocfilehash: 6187106a9aa98107d89e65fe4c7a0e8a27befa87


---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-with-hdinsight"></a>Utiliser des signatures d’accès partagé Azure Storage pour restreindre l’accès aux données avec HDInsight
HDInsight utilise le stockage d’objets blob Azure pour stocker des données. Bien que HDInsight doive disposer d’un accès complet à l’objet blob utilisé comme espace de stockage par défaut pour le cluster, vous pouvez restreindre les autorisations aux données stockées dans d’autres objets blob utilisés par le cluster. Par exemple, vous souhaitez peut-être mettre des données en lecture seule. Pour ce faire, utilisez les signatures d’accès partagé.

Les signatures d’accès partagé (SAP) sont une fonctionnalité des comptes de stockage Azure qui vous permet de limiter l’accès aux données. Par exemple, en fournissant un accès en lecture seule aux données. Dans ce document, vous découvrirez comment utiliser les SAP pour activer un accès en lecture et en liste seules vers un conteneur d’objets blob à partir de HDInsight.

## <a name="requirements"></a>Configuration requise
* Un abonnement Azure
* C# ou Python. Un exemple de code C# est fourni en tant que solution Visual Studio.
  
  * La version de Visual Studio doit être 2013 ou 2015
  * La version de Python doit être 2.7 ou une version ultérieure.
* Cluster HDInsight basé sur Linux OU [Azure PowerShell][powershell] : si vous avez un cluster existant basé sur Linux, vous pouvez utiliser Ambari pour ajouter une signature d’accès partagé au cluster. Si ce n’est pas le cas, vous pouvez utiliser Azure PowerShell pour créer un cluster et ajouter une signature d’accès partagé lors de la création du cluster.

    > [!IMPORTANT]
    > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour en savoir plus, consultez le paragraphe [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* Fichiers d’exemple à l’adresse [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Ce référentiel comprend les éléments suivants :
  
  * Un projet Visual Studio permettant de créer un conteneur de stockage, une stratégie stockée et une SAP pour une utilisation avec HDInsight.
  * Un script Python permettant de créer un conteneur de stockage, une stratégie stockée et une SAP pour une utilisation avec HDInsight
  * Un script PowerShell permettant de créer un cluster HDInsight et de le configurer afin d’utiliser la SAP.

## <a name="shared-access-signatures"></a>Les signatures d’accès partagé
Il existe deux types de signatures d’accès partagé :

* Ad hoc : l’heure de début, l’heure d’expiration et les autorisations associées à la SAP sont spécifiées sur l’URI de signature d’accès partagé (ou implicites, dans le cas où l’heure de début est omise).
* Stratégie d’accès stockée : une stratégie d’accès stockée est définie sur un conteneur de ressources (conteneur d’objets blob, table, file d’attente ou partage de fichiers) et permet de gérer les contraintes d’une ou de plusieurs signatures d’accès partagé. Lorsque vous associez une signature d'accès partagé à une stratégie d'accès stockée, la signature hérite des contraintes (heure de début, heure d'expiration et autorisations) définies pour la stratégie.

La différence entre les deux formes est importante pour un scénario clé : la révocation. Une signature d'accès partagé est une URL. Par conséquent, toute personne qui obtient la signature peut s'en servir, quel que soit celui qui l'a demandée initialement. Si une SAP est publiée publiquement, elle peut être utilisée par n’importe qui. Une clé d'accès partagé qui est distribuée est valide jusqu'à ce que l'un des quatre événements suivants ait lieu :

1. L'heure d'expiration spécifiée sur la signature d'accès partagé est atteinte.
2. L'heure d'expiration spécifiée sur la stratégie d'accès stockée référencée par la signature d'accès partagé est atteinte (si une stratégie d'accès stockée est référencée et si elle spécifie une heure d'expiration). Cela peut arriver soit parce que l'intervalle s'est écoulé, soit parce que vous avez modifié la stratégie d'accès stockée pour définir une heure d'expiration dans le passé, ce qui est une manière de révoquer la signature d'accès partagé.
3. La stratégie d'accès stockée référencée par la signature d'accès partagé est supprimée, ce qui est une autre manière de révoquer la signature d'accès partagé. Notez que si vous recréez la stratégie d'accès stockée avec exactement le même nom, tous les jetons de signature d'accès partagé existants seront de nouveau valides en fonction des autorisations associées à cette stratégie d'accès stockée (en partant du principe que l'heure d'expiration sur la signature d'accès partagé n'est pas passée). Si vous avez l'intention de révoquer la signature d'accès partagé, veillez à utiliser un nom différent si vous recréez la stratégie d'accès avec une heure d'expiration située dans le futur.
4. La clé de compte qui a été utilisée pour créer la signature d'accès partagé est régénérée. Notez que cela provoquera un échec d'authentification pour tous les composants de l'application qui utilisent cette clé de compte jusqu'à ce qu'ils soient mis à jour afin d'utiliser soit l'autre clé de compte valide soit la clé de compte nouvellement régénérée.

> [!IMPORTANT]
> L’URI d’une signature d’accès partagé est associé à la clé du compte utilisée pour créer la signature et à la stratégie d’accès stockée correspondante (le cas échéant). Si aucune stratégie d’accès stockée n’est spécifiée, la seule façon de révoquer une signature d’accès partagé consiste à modifier la clé du compte. 
> 
> 

Il est recommandé de toujours utiliser les stratégies d’accès stockées, afin de pouvoir révoquer des signatures ou étendre la date d’expiration si nécessaire. Les étapes décrites dans ce document utilisent les stratégies d’accès stockées pour générer des SAP.

Pour plus d’informations sur les SAP, voir [Présentation du modèle SAP](../storage/storage-dotnet-shared-access-signature-part-1.md)

## <a name="create-a-stored-policy-and-generate-a-sas"></a>Créer une stratégie stockée et générer une SAP
Actuellement, vous devez créer une stratégie stockée par programme. Vous trouverez un exemple C# et Python de création d’une stratégie stockée et SAS à l’adresse [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature).

### <a name="create-a-stored-policy-and-sas-using-c"></a>Créer une stratégie stockée et une SAP à l’aide de C\
1. Ouvrez la solution dans Visual Studio.
2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **SASToken**, puis sélectionnez **Propriétés**.
3. Sélectionnez **Paramètres** et ajoutez des valeurs pour les entrées suivantes :
   
   * StorageConnectionString : chaîne de connexion pour le compte de stockage pour lequel vous souhaitez créer une stratégie stockée et une SAP. Le format doit être `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey`, où `myaccount` est le nom de votre compte de stockage et `mykey` est la clé pour le compte de stockage.
   * ContainerName : conteneur du compte de stockage auquel vous souhaitez restreindre l’accès.
   * SASPolicyName : nom à utiliser pour la stratégie stockée qui sera créée.
   * FileToUpload : chemin d’accès à un fichier qui sera téléchargé vers le conteneur.
4. Exécutez le projet. Une fenêtre de console apparaît et des informations semblables à celles ci-dessous s’affichent une fois la SAP générée :
   
        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
   
    Enregistrez le jeton de stratégie SAP, car vous en aurez besoin lorsque vous associerez le compte de stockage à votre cluster HDInsight. Vous aurez également besoin du nom du compte de stockage et du nom du conteneur.

### <a name="create-a-stored-policy-and-sas-using-python"></a>Créer une stratégie stockée et une SAP à l’aide de Python
1. Ouvrez le fichier SASToken.py et modifiez les valeurs suivantes :
   
   * policy\_name : nom à utiliser pour la stratégie stockée qui sera créée.
   * storage\_account\_name : le nom de votre compte de stockage.
   * storage\_account\_key : la clé du compte de stockage.
   * storage\_container\_name : conteneur du compte de stockage auquel vous souhaitez restreindre l’accès.
   * example\_file\_path : chemin d’accès à un fichier qui sera téléchargé vers le conteneur.
2. Exécutez le script. Un jeton SAPS semblable au jeton ci-dessous s’affiche lorsque le script est terminé :
   
        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
   
    Enregistrez le jeton de stratégie SAP, car vous en aurez besoin lorsque vous associerez le compte de stockage à votre cluster HDInsight. Vous aurez également besoin du nom du compte de stockage et du nom du conteneur.

## <a name="use-the-sas-with-hdinsight"></a>Utilisation de la SAP avec HDInsight
Lorsque vous créez un cluster HDInsight, vous devez spécifier un compte de stockage principal, et vous pouvez éventuellement indiquer des comptes de stockage supplémentaires. Ces deux méthodes d’ajout de stockage nécessitent un accès complet aux comptes de stockage et aux conteneurs qui sont utilisés.

Pour utiliser une signature d’accès partagé afin de limiter l’accès à un conteneur, vous devez ajouter une entrée personnalisée à la configuration **core-site** du cluster.

* Pour les clusters HDInsight **basés sur Windows** ou **basés sur Linux**, vous pouvez effectuer cette opération lors de la création du cluster à l’aide de PowerShell.
* Pour les clusters HDInsight **basés sur Linux** , vous pouvez modifier la configuration après la création du cluster à l’aide d’Ambari.

### <a name="create-a-new-cluster-that-uses-the-sas"></a>Créer un cluster qui utilise les SAP
Un exemple de création de cluster HDInsight utilisant les SAP est inclus dans le répertoire `CreateCluster` du référentiel. Pour l’utiliser, procédez comme suit :

1. Ouvrez le fichier `CreateCluster\HDInsightSAS.ps1` dans un éditeur de texte et modifiez les valeurs suivantes au début du document.
   
        # Replace 'mycluster' with the name of the cluster to be created
        $clusterName = 'mycluster'
        # Valid values are 'Linux' and 'Windows'
        $osType = 'Linux'
        # Replace 'myresourcegroup' with the name of the group to be created
        $resourceGroupName = 'myresourcegroup'
        # Replace with the Azure data center you want to the cluster to live in
        $location = 'North Europe'
        # Replace with the name of the default storage account to be created
        $defaultStorageAccountName = 'mystorageaccount'
        # Replace with the name of the SAS container created earlier
        $SASContainerName = 'sascontainer'
        # Replace with the name of the SAS storage account created earlier
        $SASStorageAccountName = 'sasaccount'
        # Replace with the SAS token generated earlier
        $SASToken = 'sastoken'
        # Set the number of worker nodes in the cluster
        $clusterSizeInNodes = 2
   
    Par exemple, remplacez `'mycluster'` avec le nom du cluster que vous souhaitez créer. Les valeurs SAP doivent correspondre aux valeurs des étapes précédentes lors de la création du compte de stockage et du jeton SAP.
   
    Une fois que vous avez modifié les valeurs, enregistrez le fichier.
2. Ouvrez une nouvelle invite Azure PowerShell. Si vous ne maîtrisez pas Azure PowerShell ou que vous ne l’avez pas installé, consultez la rubrique [Installer et configurer Azure PowerShell][powershell].
3. À partir de l’invite, utilisez la commande suivante pour vous identifier auprès de votre abonnement Azure :
   
        Login-AzureRmAccount
   
    Si vous y êtes invité, connectez-vous avec le compte associé à votre abonnement Azure.
   
    Si votre connexion est associée à plusieurs abonnements Azure, vous devrez peut-être utiliser `Select-AzureRmSubscription` pour sélectionner l’abonnement que vous souhaitez utiliser.
4. À partir de l’invite, remplacez les répertoires par le répertoire `CreateCluster` qui contient le fichier HDInsightSAS.ps1. Utilisez les éléments suivants pour exécuter le script.
   
        .\HDInsightSAS.ps1
   
    Lorsqu’il s’exécute, le script consigne la sortie dans l’invite PowerShell, et crée le groupe de ressources et les comptes de stockage. Il vous invite ensuite à entrer le nom d’utilisateur HTTP pour le cluster HDInsight. Il s’agit du compte d’utilisateur utilisé pour sécuriser l’accès HTTP/s pour le cluster.
   
    Si vous créez un cluster basé sur Linux, vous serez également invité à saisir un nom et un mot de passe de compte d’utilisateur SSH. Ce dernier est utilisé pour la connexion à distance au cluster.
   
   > [!IMPORTANT]
   > Lorsque vous êtes invité à saisir le nom d’utilisateur et le mot de passe HTTP/s ou SSH, vous devez fournir un mot de passe qui répond aux critères suivants :
   > 
   > * Il doit contenir au moins 10 caractères.
   > * Il doit contenir au moins un chiffre.
   > * Il doit contenir au moins un caractère non alphanumérique.
   > * Il doit contenir au moins une lettre minuscule et une lettre majuscule.
   > 
   > 

Le script peut prendre un certain temps pour s’exécuter, 15 minutes en général. Lorsque le script se termine sans erreur, le cluster a été créé.

### <a name="update-an-existing-cluster-to-use-the-sas"></a>Mise à jour d’un cluster existant pour utiliser la SAP
Si vous disposez d’un cluster existant basé sur Linux, vous pouvez ajouter la SAP pour la configuration **core-site** en procédant comme suit :

1. Ouvrez l’interface utilisateur web Ambari de votre cluster. L’adresse de cette page est https://VOTRE NOM DE CLISTER.azurehdinsight.net. À l’invite, authentifiez-vous auprès du cluster au moyen du nom et du mot de passe d’administrateur que vous avez utilisés lors de la création du cluster.
2. Dans la partie gauche de l’interface utilisateur web d’Ambari, sélectionnez **HDFS**, puis sélectionnez l’onglet **Configurations** au milieu de la page.
3. Sélectionnez l’onglet **Avancé** et faites défiler jusqu’à ce que vous trouviez la section **Configuration core-site personnalisée**.
4. Développez la section **Configuration core-site personnalisée**, puis faites défiler jusqu’à la fin et sélectionnez le lien **Ajouter une propriété...**. Utilisez les valeurs suivantes pour les champs **Clé** et **Valeur** :
   
   * **Clé**: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
   * **Valeur**: la SAP retournée par l’application C# ou Python exécutée précédemment.
     
     Remplacez **CONTAINERNAME** avec le nom du conteneur que vous avez utilisé avec l’application C# ou SAP. Remplacez **STORAGEACCOUNTNAME** avec le nom du compte de stockage que vous avez utilisé.
5. Cliquez sur le bouton **Ajouter** pour enregistrer cette clé et cette valeur, puis cliquez sur le bouton **Enregistrer** pour enregistrer les modifications de configuration. Lorsque vous y êtes invité, ajoutez une description de la modification (« Ajout d’accès de stockage SAP » par exemple), puis cliquez sur **Enregistrer**.
   
    Cliquez sur **OK** lorsque les modifications ont été effectuées.
   
   > [!IMPORTANT]
   > Cette opération enregistre les modifications de configuration, mais vous devrez redémarrer plusieurs services pour que la modification prenne effet.
   > 
   > 
6. Dans l’interface utilisateur web Ambari, sélectionnez **HDFS** dans la liste sur la gauche, puis sélectionnez **Redémarrer tout** dans la liste déroulante **Actions de service** située à droite. Lorsque vous y êtes invité, sélectionnez **Activer le mode de maintenance**, puis sélectionnez __Conform Restart All".
   
    Répétez ce processus pour les entrées MapReduce2 et YARN de la liste située à gauche de la page.
7. Une fois ces entrées redémarrées, sélectionnez chacune d’entre elles et désactivez le mode maintenance à partir de la liste déroulante **Actions de service** .

## <a name="test-restricted-access"></a>Tester l’accès restreint
Pour vérifier que vous disposez d’un accès restreint, procédez comme suit :

* Pour les clusters HDInsight **basés sur Windows**, utilisez le Bureau à distance pour vous connecter au cluster. Pour plus d’informations, voir. [Se connecter à HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
  
    Une fois la connexion établie, utilisez l’icône de **ligne de commande Hadoop** du Bureau pour ouvrir une invite de commande.
* Pour les clusters HDInsight **basés sur Linux** , utilisez SSH pour vous connecter au cluster. Pour plus d’informations sur l’utilisation de SSH avec les clusters basés sur Linux, consultez l’un des articles suivants :
  
  * [Utiliser SSH avec Hadoop Linux sur HDInsight à partir de Linux, OS X et Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
  * [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

Une fois connecté au cluster, procédez comme suit pour vérifier que vous pouvez uniquement lire et répertorier les éléments sur le compte de stockage SAP :

1. À partir de l’invite de commandes, tapez ce qui suit. Remplacez **SASCONTAINER** avec le nom du conteneur créé pour le compte de stockage SAP. Remplacez **SASACCOUNTNAME** avec le nom du compte de stockage utilisé pour la SAP :
   
        hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
   
    Cette opération permet de répertorier le contenu du conteneur, qui doit inclure le fichier téléchargé lorsque le conteneur et la SAP ont été créés.
2. Utilisez les éléments suivants pour vérifier que vous pouvez lire le contenu du fichier. Remplacez **SASCONTAINER** et **SASACCOUNTNAME** comme à l’étape précédente. Remplacez **FILENAME** avec le nom du fichier affiché dans la commande précédente :
   
        hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
   
    Cette opération permet de répertorier le contenu du fichier.
3. Pour télécharger le fichier sur le système de fichiers local, utilisez les éléments suivants :
   
        hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
   
    Cette opération permet de télécharger le fichier vers un fichier local nommé **testfile.txt**.
4. Utilisez la commande suivante pour télécharger le fichier local vers un nouveau fichier nommé **testupload.txt** sur le stockage SAP :
   
        hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
   
    Vous recevrez un message similaire à celui ci-dessous :
   
        put: java.io.IOException
   
    Cette erreur se produit, car l’emplacement de stockage est en lecture + liste uniquement. Pour placer les données sur le stockage par défaut pour le cluster, accessible en écriture, utilisez les éléments suivants :
   
        hdfs dfs -put testfile.txt wasbs:///testupload.txt
   
    Cette fois, l’opération doit se terminer normalement.

## <a name="troubleshooting"></a>Résolution des problèmes
### <a name="a-task-was-canceled"></a>Une tâche a été annulée
**Symptômes**: lorsque vous créez un cluster à l’aide du script PowerShell, le message d’erreur suivant peut s’afficher :

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

**Cause**: cette erreur peut se produire si vous utilisez un mot de passe pour l’utilisateur admin/HTTP pour le cluster, ou pour l’utilisateur SSH (pour les clusters basés sur Linux).

**Résolution**: utilisez un mot de passe qui répond aux critères suivants :

* Il doit contenir au moins 10 caractères.
* Il doit contenir au moins un chiffre.
* Il doit contenir au moins un caractère non alphanumérique.
* Il doit contenir au moins une lettre minuscule et une lettre majuscule.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris comment ajouter un stockage à accès limité à votre cluster HDInsight, découvrez d’autres façons de travailler avec des données sur votre cluster :

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

[powershell]: /powershell/azureps-cmdlets-docs



<!--HONumber=Jan17_HO3-->


