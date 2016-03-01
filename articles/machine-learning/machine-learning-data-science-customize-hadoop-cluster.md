<properties 
	pageTitle="Personnaliser des clusters Hadoop pour le processus d’analyse Cortana | Microsoft Azure" 
	description="Modules de Python populaires disponibles dans les clusters Hadoop Azure HDInsight personnalisés."
	services="machine-learning" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="paulettm" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/08/2016" 
	ms.author="hangzh;bradsev" />

# Personnaliser des clusters Hadoop Azure HDInsight pour le processus d’analyse Cortana 

## Introduction

Cet article explique comment personnaliser un cluster Hadoop HDInsight en installant Anaconda 64 bits (Python 2.7) sur chaque nœud lorsque le cluster est en cours d’approvisionnement dans le service HDInsight. Cette personnalisation prépare le cluster à une utilisation avec le processus d’analyse Cortana. Il indique également comment accéder au nœud principal pour soumettre des tâches personnalisées au cluster.

Cette personnalisation permet de mettre à disposition de nombreux modules Python populaires, inclus dans Anaconda, à des fins d’utilisation dans les fonctions définies par l’utilisateur (UDF) qui sont conçues pour traiter les enregistrements de ruche dans le cluster. Pour obtenir des instructions sur les procédures utilisées dans ce scénario, consultez la section [Envoyer des requêtes Hive à des clusters Hadoop HDInsight dans le processus d’analyse avancée](machine-learning-data-science-hive-queries.md).

Le menu ci-dessous pointe vers des rubriques qui décrivent comment configurer les différents environnements de science de données utilisés par le processus d'analyse Cortana (CAP).

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]


## <a name="customize"></a>Personnaliser le cluster Hadoop Azure HDInsight

Pour créer un cluster Hadoop HDInsight personnalisé, les utilisateurs doivent se connecter au [**portail Azure Classic**](https://manage.windowsazure.com/), cliquer sur l’option **Nouveau** dans l’angle inférieur gauche de la page et sélectionner SERVICES DE DONNÉES -> HDINSIGHT -> **CRÉATION PERSONNALISÉE** pour faire apparaître la fenêtre **Détails du cluster**.

![Create workspace][1]

Indiquez le nom du cluster à créer sur la première page de configuration et acceptez les valeurs par défaut affichées dans les autres champs. Cliquez sur la flèche pour passer à la page de configuration suivante.

![Create workspace][2]

Sur la page de configuration 2, saisissez une valeur dans le champ **NŒUDS DE DONNÉES**, sélectionnez une valeur pour l’option **RÉGION/RÉSEAU VIRTUEL** et choisissez la taille des paramètres **NŒUD PRINCIPAL** et **NŒUD DE DONNÉES**. Cliquez sur la flèche pour passer à la page suivante de la configuration.

>[AZURE.NOTE] La valeur du paramètre **RÉGION/RÉSEAU VIRTUEL** doit correspondre à la région du compte de stockage qui doit être utilisé pour le cluster Hadoop HDInsight. Dans le cas contraire, le compte de stockage que les utilisateurs souhaitent utiliser n’apparaîtra pas dans la liste déroulante du paramètre **NOM DE COMPTE**, dans la quatrième page de configuration.

![Create workspace][3]

Sur la troisième page de configuration, saisissez un nom d’utilisateur et un mot de passe pour le cluster Hadoop HDInsight. **Ne sélectionnez pas** l’option _Sélection du metastore Hive/Oozie_. Ensuite, cliquez sur la flèche pour passer à la page de configuration suivante.

![Create workspace][4]

Sur la quatrième page, indiquez le nom du compte de stockage et celui du conteneur par défaut du cluster Hadoop HDInsight. Si l’utilisateur sélectionne l’option _Créer un conteneur par défaut_ dans la liste déroulante **CONTENEUR PAR DÉFAUT**, un conteneur portant le même nom que le cluster est créé. Cliquez sur la flèche pour accéder à la dernière page de configuration.

![Create workspace][5]

Sur la dernière page de configuration, **Actions de script**, cliquez sur le bouton **Ajouter une action de script** et remplissez les champs de texte en saisissant les valeurs suivantes.
 
* **NOM** : vous pouvez indiquer n’importe quelle chaîne en tant que nom de l’action de script. 
* **TYPE DE NŒUD** : sélectionnez **Tous les nœuds**. 
* **SCRIPT URI** : *http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1*
	* *publicscripts* est un conteneur public dans le compte de stockage 
	* *getgoing* permet de partager les fichiers de scripts PowerShell afin d’aider les utilisateurs à travailler dans Azure. 
* **PARAMÈTRES** : (laisser cette zone vide)

Enfin, cliquez sur la coche pour démarrer la création du cluster Hadoop HDInsight personnalisé.

![Create workspace][6]

## <a name="headnode"></a> Accéder au nœud principal du cluster Hadoop

Les utilisateurs doivent activer l’accès à distance au cluster Hadoop dans Microsoft Azure pour pouvoir accéder au nœud principal du cluster Hadoop, via RDP.

1. Connectez-vous au [**portail Azure Classic**](https://manage.windowsazure.com/), sélectionnez la valeur **HDInsight** sur la gauche, choisissez le cluster Hadoop dans la liste des clusters, cliquez sur l’onglet **CONFIGURATION** puis sur l’icône **ACTIVER DISTANT**, dans la partie inférieure de la page.
	
	![Create workspace][7]

2. Dans la fenêtre **Configurer le Bureau à distance**, renseignez les champs NOM D’UTILISATEUR et MOT DE PASSE, puis sélectionnez la date d’expiration de l’accès à distance. Ensuite, cliquez sur la case à cocher pour activer l’accès à distance au nœud principal du cluster Hadoop.
	
	>[AZURE.NOTE] 
	>
	>1. Le nom d’utilisateur et le mot de passe associés à l’accès à distance sont différents de ceux que vous avez utilisés lors de la création du cluster Hadoop. Il s’agit d’un ensemble d’informations d’identification distinct.
	>
	>2. La date d’expiration de l’accès à distance doit être située dans une période de 7 jours maximum, à compter de la date actuelle.

	![Create workspace][8]

3. Une fois l’accès à distance activé, cliquez sur l’option **CONNEXION** située en bas de la page pour permettre l’accès à distance au nœud principal. Vous vous connectez au nœud principal du cluster Hadoop en saisissant les informations d’identification de l’utilisateur autorisé que vous avez spécifiées précédemment.

	 ![Create workspace][9]

Les étapes suivantes du processus d’analyse avancée dans Azure sont présentées dans le [Guide d’apprentissage : traitement des données avancé dans Microsoft Azure](machine-learning-data-science-advanced-data-processing.md) et peuvent inclure des étapes de déplacement, de traitement et d’échantillonnage des données dans HDInsight en vue d’en extraire de l’information pertinente avec Azure Machine Learning.

Consultez la section [Envoyer des requêtes Hive à des clusters Hadoop HDInsight dans le processus d’analyse avancée](machine-learning-data-science-process-hive-tables.md) pour savoir comment accéder aux modules Python inclus dans Anaconda à partir du nœud principal du cluster, dans les fonctions définies par l’utilisateur (FDU) qui permettent de traiter les enregistrements Hive stockés dans le cluster.

[1]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png
[2]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img2.png
[3]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img3.png
[4]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img4.png
[5]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img5.png
[6]: ./media/machine-learning-data-science-customize-hadoop-cluster/script-actions.png
[7]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-1.png
[8]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-2.png
[9]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-3.png

 

<!----HONumber=AcomDC_0211_2016-->