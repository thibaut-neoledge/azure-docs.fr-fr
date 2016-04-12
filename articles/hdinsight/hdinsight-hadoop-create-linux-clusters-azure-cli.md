<properties
   	pageTitle="Créer des clusters Hadoop, HBase ou Storm sur Linux dans HDInsight à l’aide de l’interface de ligne de commande Azure inter-plateformes | Microsoft Azure"
   	description="Apprenez à créer des clusters HDInsight sous Linux à l’aide de l’interface de ligne de commande Azure inter-plateformes, de modèles Azure Resource Manager et de l’API REST Azure. Vous pouvez spécifier le type de cluster (Hadoop, HBase ou Storm) ou utiliser des scripts pour installer des composants personnalisés."
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="03/08/2016"
   	ms.author="larryfr"/>

#Création de clusters basés sur Linux dans HDInsight à l’aide de l’interface CLI Azure

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-selector-create-clusters.md)]

L'interface CLI Azure est un utilitaire de ligne de commande interplateforme qui vous permet de gérer les services Azure. Il peut être utilisé avec les modèles de gestion des ressources Azure pour créer un cluster HDInsight, et avec les comptes de stockage et autres services associés.

Les modèles de gestion des ressources Azure sont des documents JSON qui décrivent un __groupe de ressources__ et toutes les ressources qu’il contient (par exemple, HDInsight). Cette approche basée sur un modèle vous permet de définir toutes les ressources dont vous avez besoin pour HDInsight dans un seul modèle et de gérer les modifications apportées au groupe dans son ensemble par le biais de __déploiements__ qui appliquent les modifications au groupe.

Les étapes de ce document décrivent le processus de création d’un cluster HDInsight à l’aide de la CLI Azure et d’un modèle :

> [AZURE.IMPORTANT] Les étapes de ce document utilisent le nombre par défaut de nœuds worker (4) pour un cluster HDInsight. Si vous envisagez d’utiliser plus de 32 nœuds worker lors de la création du cluster ou en faisant évoluer le cluster après sa création, vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM.
>
> Pour plus d’informations sur les tailles de nœud et les coûts associés, consultez [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##Composants requis

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- __Interface de ligne de commande Azure__. Pour plus d’informations sur l’installation de l’interface de ligne de commande, consultez [Installer l’interface de ligne de commande Azure](../xplat-cli-install.md).

##Vous connecter à votre abonnement Azure

Suivez les étapes décrites dans [Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure (Azure CLI)](../xplat-cli-connect.md) et connectez-vous à votre abonnement à l’aide de la méthode __login__.

##Créer un cluster

La procédure suivante doit être effectuée à partir d'une session d’invite de commande, shell ou terminal après l'installation et la configuration de l'interface CLI Azure.

1. Utilisez la commande suivante pour vous identifier auprès de votre abonnement Azure :

        azure login

    Vous serez invité à fournir votre nom et mot de passe. Si vous possédez plusieurs abonnements Azure, vous pouvez utiliser `azure account set <subscriptionname>` pour définir l’abonnement que les commandes CLI Azure utiliseront.

3. Passez en mode Gestionnaire de ressources Azure en exécutant la commande suivante :

        azure config mode arm

4. Créez un modèle pour votre cluster HDInsight. Voici certains exemples de modèles de base :

    * [Cluster basé sur Linux, utilisant une clé publique SSH](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-publickey)
    * [Cluster basé sur Linux, utilisant un mot de passe pour le compte SSH](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password)

    Ces deux modèles créent également le compte de stockage Azure par défaut utilisé par HDInsight.

    Vous aurez besoin des fichiers __azuredeploy.json__ et __azuredeploy.parameters.json__. Copiez ces fichiers localement avant de continuer.

5. Ouvrez le fichier __azuredeploy.parameters.json__ dans un éditeur et fournissez les valeurs des éléments de la section `parameters` :

    * __location__ : centre de données dans lequel les ressources seront créées. Vous pouvez afficher la section `location` du fichier __azuredeploy.json__ pour obtenir la liste des emplacements autorisés.
    * __clusterName__: nom du cluster HDInsight. Ce nom doit être unique, sinon le déploiement échouera.
    * __clusterStorageAccountName__ : nom du compte de stockage Azure qui sera créé pour le cluster HDInsight. Ce nom doit être unique, sinon le déploiement échouera.
    * __clusterLoginPassword__ : mot de passe de l’utilisateur admin du cluster. Il doit s’agir d’un mot de passe sécurisé car il permet d’accéder à des sites web et des services REST sur le cluster.
    * __sshUserName__ : nom du premier utilisateur SSH à créer pour ce cluster. SSH permet d’accéder à distance au cluster en utilisant ce compte.
    * __sshPublicKey__ : si vous utilisez le modèle qui nécessite une clé publique SSH, vous devez ajouter votre clé publique sur cette ligne. Pour plus d'informations sur la génération et l'utilisation des clés publiques, consultez les articles suivants :

        * [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * __sshPassword__ : si vous utilisez le modèle qui nécessite un mot de passe SSH, vous devez ajouter un mot de passe sur cette ligne.

    Une fois que vous avez terminé, enregistrez et fermez le fichier.

5. Procédez comme suit pour créer un groupe de ressources vide. Remplacez __RESOURCEGROUPNAME__ par le nom que vous souhaitez utiliser pour ce groupe. Remplacez __LOCATION__ par le centre de données dans lequel vous souhaitez créer votre cluster HDInsight :

        azure group create RESOURCEGROUPNAME LOCATION

    > [AZURE.NOTE] Si le nom de l'emplacement contient des espaces, entourez-le de guillemets. Par exemple, « Centre-Sud des États-Unis ».

6. Utilisez la commande suivante afin de créer le déploiement initial pour ce groupe de ressources. Remplacez __PATHTOTEMPLATE__ par le chemin du fichier de modèle __azuredeploy.json__. Remplacez __PATHTOPARAMETERSFILE__ par le chemin du fichier __azuredeploy.parameters.json__. Remplacez __RESOURCEGROUPNAME__ par le nom du groupe que vous avez créé à l’étape précédente :

        azure group deployment create -f PATHTOTEMPLATE -e PATHTOPARAMETERSFILE -g RESOURCEGROUPNAME -n InitialDeployment

    Une fois le déploiement accepté, un message semblable à `group deployment create command ok` doit s’afficher.

7. La finalisation du déploiement peut prendre du temps, environ 15 minutes. Vous pouvez afficher des informations sur le déploiement à l'aide de la commande suivante. Remplacez __RESOURCEGROUPNAME__ par le nom du groupe de ressources utilisé à l’étape précédente :

        azure group log show -l RESOURCEGROUPNAME

    Une fois le déploiement terminé, le champ __Status__ contiendra la valeur __Succeeded__. Si une erreur se produit au cours du déploiement, vous pouvez obtenir plus d'informations sur cette erreur à l'aide de la commande suivante

        azure group log show -l -v RESOURCEGROUPNAME

##Étapes suivantes

Vous avez créé un cluster HDInsight. Pour apprendre à l’utiliser, consultez les rubriques ci-dessous :

###Clusters Hadoop

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

###Clusters HBase

* [Prise en main de HBase sur HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Développement d’applications Java pour HBase sur HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###Clusters Storm

* [Développement de topologies Java pour Storm sur HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilisation de composants Python dans Storm sur HDInsight](hdinsight-storm-develop-python-topology.md)
* [Déploiement et analyse des topologies avec Storm sur HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---------HONumber=AcomDC_0309_2016-->