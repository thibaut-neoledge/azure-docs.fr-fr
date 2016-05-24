<properties
   	pageTitle="Installer des applications Hadoop sur HDInsight | Microsoft Azure"
   	description="Découvrez comment installer des applications Hadoop sur des applications HDInsight."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="05/18/2016"
   	ms.author="jgao"/>

# Installer des applications HDInsight personnalisées

Une application HDInsight est une application que les utilisateurs peuvent installer sur un cluster HDInsight sous Linux. Ces applications peuvent être développées par Microsoft, par des éditeurs de logiciels indépendants (ISV) ou par vous-même. Dans cet article, vous découvrirez comment installer une application HDInsight n’ayant pas été publiée sur le portail Azure, dans HDInsight. Vous allez installer l’application [Hue](http://gethue.com/).

Autres articles associés :

- [Publish HDInsight applications](hdinsight-apps-publish-applications.md) (Publier des applications HDInsight) : découvrez comment publier vos applications HDInsight personnalisées sur Azure Marketplace.
- [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN : installer une application HDInsight) : découvrez comment définir des applications HDInsight.

 
## Composants requis

Pour installer des applications HDInsight sur un cluster HDInsight existant, vous devez disposer d’un cluster HDInsight. Pour en créer un, consultez [Créer des clusters](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). Vous pouvez également installer des applications HDInsight lorsque vous créez un cluster HDInsight.


## Installer une application HDInsight

Les applications HDInsight peuvent être installées sur un cluster HDInsight existant ou lorsque vous créez un cluster. Pour définir des modèles ARM, consultez [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN : installer une application HDInsight).

Fichiers nécessaires pour le déploiement de cette application (Hue) :

- [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): le modèle ARM pour installer l’application HDInsight. Consultez la page [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN : installer une application HDInsight) pour développer votre propre modèle ARM.
- [hue-install\_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh) : l’action de script appelée par le modèle ARM pour la configuration du nœud de périmètre. 
- [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz) : le fichier binaire hue appelé à partir de hui-install\_v0.sh. 
- [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz) : le fichier binaire hue appelé à partir de hui-install\_v0.sh. 
- [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz) : un exemple d’application web (Tomcat) appelée à partir de hui-install\_v0.sh.

**Pour installer Hue sur un cluster HDInsight existant**

1. Cliquez sur l’image suivante pour vous connecter à Azure et ouvrir le modèle ARM dans le portail Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/fr-FR/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Ce bouton ouvre un modèle ARM sur le portail Azure. Le modèle ARM est situé dans [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue). Pour savoir comment écrire ce modèle ARM, consultez [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN : installer une application HDInsight).
    
2. À partir du panneau **Paramètres**, saisissez les informations suivantes :

    - **ClusterName** : entrez le nom du cluster où vous souhaitez installer l’application. Ce cluster doit être un cluster existant.
    
3. Cliquez sur **OK** pour enregistrer les paramètres.
4. Dans le panneau **Déploiement personnalisé**, entrez **Groupe de ressources**. Le groupe de ressources est un conteneur qui regroupe le cluster, le compte de stockage dépendant et d’autres ressources. Vous devez utiliser le même groupe de ressources que le cluster.
5. Cliquez sur **Conditions juridiques**, puis cliquez sur **Créer**.
6. Vérifiez que la case **Épingler au tableau de bord** est cochée, puis cliquez sur **Créer**. Vous pouvez voir l’état de l’installation dans la vignette épinglée sur le tableau de bord du portail ainsi que la notification du portail (cliquez sur l’icône en forme de cloche en haut du portail). L’installation de l’application prend environ 10 minutes.

**Pour installer Hue lors de la création d’un cluster**

1. Cliquez sur l’image suivante pour vous connecter à Azure et ouvrir le modèle ARM dans le portail Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/fr-FR/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Ce bouton ouvre un modèle ARM sur le portail Azure. Le modèle ARM se trouve à l’adresse [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json.](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json) Pour savoir comment écrire ce modèle ARM, consultez [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN : installer une application HDInsight).

2. Suivez les instructions pour créer le cluster et installer Hue. Pour plus d’informations sur la création de clusters HDInsight, consultez [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## validation de l'installation

Vous pouvez vérifier l’état de l’application sur le portail Azure pour valider l’installation de l’application. Vous pouvez également valider la création appropriée de tous les points de terminaison HTTP et du site web, le cas échéant :

**Pour accéder au portail Hue**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Clusters HDInsight** dans le menu de gauche. Si vous ne le voyez pas, cliquez sur **Parcourir**, puis sur **Clusters HDInsight**.
3. Cliquez sur le cluster dans lequel vous avez installé l’application.
4. Dans le panneau **Paramètres**, cliquez sur **Applications** sous la catégorie **Général**. Vous devriez voir **hue** dans le panneau **Applications installées**.
5. Cliquez sur **hue** dans la liste pour en afficher les propriétés.  
6. Cliquez sur le lien de la page web pour valider le site web ; ouvrez le point de terminaison HTTP dans un navigateur pour valider l’interface utilisateur web de Hue, puis ouvrez le point de terminaison SSH à l’aide de [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md) ou d’autres [clients SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
 
## Dépanner l’installation

Consultez la section [Dépannage](hdinsight-apps-install-applications.md#troubleshoot) de l’article Install HDInsight Applications (Installer des applications HDInsight).


## Étapes suivantes

- [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN : installer une application HDInsight) : découvrez comment développer des applications ARM pour le déploiement d’applications HDInsight.
- [Publish HDInsight applications](hdinsight-apps-publish-applications.md) (Publier des applications HDInsight) : découvrez comment publier vos applications HDInsight personnalisées sur Azure Marketplace.
- [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md) : apprenez à utiliser l’action de script pour installer des applications supplémentaires.
- [Créer des clusters Hadoop sous Linux dans HDInsight à l’aide de modèles ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md) : apprenez à appeler des modèles ARM pour la création de clusters HDInsight.

<!---HONumber=AcomDC_0518_2016-->