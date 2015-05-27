<properties 
	pageTitle="Développement d’une action de script avec HDInsight | Microsoft Azure" 
	description="Découvrez comment personnaliser des clusters Hadoop avec une action de script." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="bradsev"/>

# Développement d'actions de script avec HDInsight 

Les actions de script fournissent une fonctionnalité Azure HDInsight permettant d'installer des logiciels supplémentaires s'exécutant sur un cluster Hadoop ou de modifier la configuration des applications installées sur un cluster. Les actions de script sont des scripts qui s'exécutent sur des nœuds de cluster lors du déploiement des clusters HDInsight. Elles sont exécutées une fois la configuration de HDInsight terminée dans le cluster. Une action de script est exécutée avec les privilèges d'un compte d'administrateur système qui fournissent des droits d'accès complets aux nœuds du cluster. Chaque cluster peut recevoir une liste d'actions de script qui sont exécutées dans l'ordre spécifié.

Une action de script peut être déployée à partir d'Azure PowerShell ou à l'aide du Kit de développement logiciel (SDK) .NET HDInsight. Pour plus d'informations, consultez la page [Personnaliser des clusters HDInsight à l'aide d'une d'action de script][hdinsight-cluster-customize].



## <a name="bestPracticeScripting"></a>Meilleures pratiques relatives au développement de scripts

Quand vous développez un script personnalisé pour un cluster HDInsight, tenez compte des meilleures pratiques suivantes :

* [Rechercher la version Hadoop](#bPS1)
* [Fournir des liens stables vers les ressources de script](#bPS2)
* [S’assurer que le script de personnalisation du cluster est idempotent](#bPS3)
* [Installer des composants personnalisés dans l'emplacement optimal](#bPS4)
* [Garantir la haute disponibilité de l’architecture du cluster](#bPS5)
* [Configurer les composants personnalisés pour utiliser le stockage d’objets blob Azure](#bPS6)

### <a name="bPS1"></a>Rechercher la version Hadoop
Seul HDInsight 3.1 (Hadoop 2.4) ou version ultérieure prend en charge l'utilisation d'une action de script pour installer des composants personnalisés sur un cluster. Dans votre script personnalisé, vous devez utiliser la méthode d'assistance **Get-HDIHadoopVersion** pour vérifier la version de Hadoop avant d'effectuer d'autres tâches dans le script.


### <a name="bPS2"></a>Fournir des liens stables vers les ressources de script 
Les utilisateurs doivent s'assurer que tous les scripts et autres artefacts utilisés dans la personnalisation d'un cluster sont disponibles tout au long de la durée de vie du cluster et que les versions de ces fichiers ne changent pas au cours de cette période. Ces ressources sont requises si le réimageage des nœuds dans le cluster est requis. La meilleure pratique consiste à tout télécharger et à tout archiver dans un compte de stockage contrôlé par l'utilisateur. Cela peut être le compte de stockage par défaut ou les comptes de stockage supplémentaires spécifiés au moment du déploiement d'un cluster personnalisé. Dans les exemples de clusters Spark et R personnalisés fournis dans la documentation, par exemple, nous avons créé une copie locale des ressources dans ce compte de stockage : https://hdiconfigactions.blob.core.windows.net/.


### <a name="bPS3"></a>Assurez-vous que le script de personnalisation de cluster est idempotent
Vous devez tenir compte du réimageage des nœuds d'un cluster HDInsight au cours de la durée de vie du cluster. Le script de personnalisation du cluster est exécuté chaque fois qu'un cluster est réimagé. Ce script doit être conçu pour être idempotent afin que lors du réimageage, le script s’assure que le cluster retrouve l’état personnalisé qui était le sien juste après la première exécution du script lors de la création initiale du cluster. Par exemple, si un script personnalisé a installé une application à l’emplacement D:\\AppLocation lors de sa première exécution, alors, à chaque exécution ultérieure, lors du réimageage, le script doit vérifier si l'application existe à l'emplacement D:\\AppLocation avant de passer aux autres étapes du script.


### <a name="bPS4"></a>Installer des composants personnalisés dans l'emplacement optimal 
Lorsque les nœuds de cluster sont réimagés, le lecteur des ressources C:\\ et le lecteur du système D:\\ peuvent être reformatés, ce qui entraîne la perte des données et des applications qui avaient été installées sur ces lecteurs. Cela peut également se produire si un nœud de machine virtuelle (VM) Azure qui fait partie du cluster tombe en panne et est remplacé par un nouveau nœud. Vous pouvez installer les composants sur le lecteur D:\\ ou dans l'emplacement C:\\apps sur le cluster. Tous les autres emplacements sur le lecteur C:\\ sont réservés. Indiquez l'emplacement d'installation des applications ou bibliothèques dans le script de personnalisation de cluster.


### <a name="bPS5"></a>Garantir la haute disponibilité de l'architecture de cluster

HDInsight possède une architecture actif/passif pour la haute disponibilité, dans laquelle un nœud principal est en mode actif (où les services HDInsight sont en cours d'exécution) et l'autre nœud principal est en mode veille (dans lequel les services HDInsight ne sont pas en cours d’exécution). Les nœuds basculent entre les modes actif et passif si les services HDInsight sont interrompus. Si une action de script est utilisée pour installer les services sur les deux nœuds principaux pour la haute disponibilité, notez que le mécanisme de basculement HDInsight n’est pas en mesure de basculer automatiquement ces services installés par l'utilisateur. Ainsi, les services installés par l'utilisateur sur les nœuds principaux HDInsight qui sont censés être hautement disponibles doivent soit avoir leur propre mécanisme de basculement en mode actif/passif soit être en mode actif-actif.

Une commande Action de script HDInsight s'exécute sur les deux nœuds principaux lorsque le rôle de nœud principal est spécifié comme valeur dans le paramètre *ClusterRoleCollection* (décrit ci-dessous dans la section [Comment exécuter une action de script](#runScriptAction)). Par conséquent, lorsque vous concevez des scripts personnalisés, vérifiez qu'il est au fait de cette configuration. Vous devez vous assurer que des services identiques ne sont pas installés et démarrés sur les deux nœuds principaux pour éviter les conflits. Sachez, par ailleurs, que les données seront perdues lors des réimageages ; les logiciels installés à l'aide d'actions de script ne doivent donc pas être affectés par de tels événements. Les applications doivent être conçues pour fonctionner avec des données hautement disponibles qui sont distribuées sur de nombreux nœuds. Notez que jusqu'à 20 % des nœuds d'un cluster peuvent être réimagés en même temps.


### <a name="bPS6"></a>Configurer les composants personnalisés pour utiliser le stockage d'objets Blob Azure
Les composants personnalisés que vous installez sur les nœuds de cluster peuvent être configurés par défaut pour utiliser le stockage HDFS (Hadoop Distributed File System). Vous devez modifier la configuration de façon à utiliser plutôt le stockage d'objets blob Azure. Sur un réimageage de cluster, le système de fichiers HDFS est formaté et vous perdrez alors toutes les données qui y sont stockées. L'utilisation du stockage d'objets blob Azure au lieu du stockage HDFS garantit que vos données seront conservées.

## <a name="helpermethods"></a>Méthodes d'assistance pour les scripts personnalisés 

L'action de script fournit les méthodes d'assistance suivantes que vous pouvez utiliser lors de l'écriture de scripts personnalisés.

Méthode d'assistance | Description
-------------- | -----------
**Save-HDIFile** | Télécharger un fichier à partir de l'URI spécifié vers un emplacement du disque local qui est associé au nœud de machine virtuelle Azure affecté au cluster.
**Expand-HDIZippedFile** | Décompresser un fichier zippé.
**Invoke-HDICmdScript** | Exécuter un script à partir de cmd.exe.
**HDILog d'écriture** | Écrire la sortie du script personnalisé utilisé pour une action de script.
**Get-Services** | Obtenir la liste des services s'exécutant sur l'ordinateur où s'exécute le script.
**Get-Service** | En utilisant le nom de service spécifique comme entrée, obtenir des informations détaillées pour un service spécifique (nom du service, ID de processus, état, etc.) sur l'ordinateur où s'exécute le script.
**Get-HDIServices** | Obtenir la liste des services HDInsight en cours d'exécution sur l'ordinateur où s'exécute le script.
**Get-HDIService** | En utilisant le nom de service HDInsight spécifique comme entrée, obtenir des informations détaillées pour un service spécifique (nom du service, l'identificateur de processus, état, etc.) sur l'ordinateur où s'exécute le script.
**Get-ServicesRunning** | Obtenir la liste des services en cours d'exécution sur l'ordinateur où s'exécute le script.
**Get-ServiceRunning** | Vérifier si un service spécifique (par nom) est en cours d'exécution sur l'ordinateur où s'exécute le script.
**Get-HDIServicesRunning** | Obtenir la liste des services HDInsight en cours d'exécution sur l'ordinateur où s'exécute le script.
**Get-HDIServiceRunning** | Vérifier si un service HDInsight spécifique (par nom) est en cours d'exécution sur l'ordinateur où s'exécute le script.
**Get-HDIHadoopVersion** | Obtenir la version de Hadoop installée sur l'ordinateur où s'exécute le script.
**Test-IsHDIHeadNode** | Vérifier si l'ordinateur où s'exécute le script est un nœud principal.
**Test-IsActiveHDIHeadNode** | Vérifier si l'ordinateur où s'exécute le script est un nœud principal actif.
**Test-IsHDIDataNode** | Vérifier si l'ordinateur où s'exécute le script est un nœud de données.
**Edit-HDIConfigFile** | Modifier les fichiers de configuration hive-site.xml, core-site.xml, hdfs-site.xml, mapred-site.xml ou yarn-site.xml.

## <a name="commonusage"></a>Modes d'utilisation courants

Cette section fournit des conseils sur l'implémentation de certains des modèles d'utilisation courants que vous pouvez rencontrer lors de l'écriture de votre propre script personnalisé.

### Définition des variables d'environnement

Dans le développement d'actions de script, vous aurez souvent besoin de définir des variables d'environnement. Par exemple, la situation suivante correspond à un scénario très probable : vous téléchargez un fichier binaire à partir d'un site externe, l’installez sur le cluster et ajoutez l'emplacement où il est installé à votre variable d'environnement « PATH ». L'extrait de code suivant montre comment définir des variables d'environnement dans le script personnalisé.

	Write-HDILog "Starting environment variable setting at: $(Get-Date)";
	[Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Cette instruction définit la variable d'environnement **MDS_RUNNER_CUSTOM_CLUSTER** sur la valeur « true » et définit également l'étendue de cette variable à l'échelle de l'ordinateur. Dans certains cas, il est important que les variables d'environnement soient définies avec l’étendue appropriée (machine ou utilisateur). Reportez-vous [ici](https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx « ici ») pour plus d'informations sur la définition des variables d'environnement.

### Accès aux emplacements où sont stockés les scripts personnalisés

Les scripts utilisés pour personnaliser un cluster doivent être soit dans le compte de stockage par défaut pour le cluster soit dans un conteneur public en lecture seule sur un autre compte de stockage. Si votre script accède à des ressources situées ailleurs, celles-ci doivent se trouver dans un conteneur accessible publiquement (au moins public en lecture seule). Par exemple, vous souhaitez accéder à un fichier et l’enregistrer à l'aide de la commande HDI SaveFile.

	Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

### Levée d'exception pour l’échec d’un déploiement de cluster

Si vous souhaitez être notifié précisément du fait que la personnalisation du cluster n’a pas réussi comme prévu, il est important qu’une exception soit levée et que l’approvisionnement du cluster échoue. Par exemple, vous souhaitez traiter un fichier s'il existe et gérer la situation d'erreur si le fichier n'existe pas. Cela garantira que le script s'arrête normalement et que l'état du cluster est correctement connu. L'extrait de code suivant fournit un exemple montrant comment y parvenir :

	If(Test-Path($SomePath)) {
		#Process file in some way
	} else {
		# File does not exist; handle error case
		# Print error message
	exit
	}

## <a name="deployScript"></a>Liste de vérification pour le déploiement d'une action de script
Voici les étapes à suivre avant de déployer des scripts :

1. Placez les fichiers qui contiennent les scripts personnalisés dans un emplacement accessible aux nœuds du cluster lors du déploiement. Il peut s'agir de tout compte de stockage par défaut ou supplémentaire spécifié lors du déploiement du cluster, ou de tout conteneur de stockage accessible au public.
2. Ajoutez des contrôles dans les scripts pour vous assurer qu'ils s'exécutent de manière idempotente, ce qui permet d'exécuter le script plusieurs fois sur le même nœud.
3. Utilisez l’applet de commande Azure PowerShell **Write-Output** pour imprimer dans STDOUT et dans STDERR. N'utilisez pas **Write-Host**.
4. Utilisez un dossier de fichiers temporaires, tel que $env:TEMP, pour conserver le fichier téléchargé utilisé par les scripts, puis nettoyez-le au terme de l'exécution des scripts.
5. 5.Installez des logiciels personnalisés uniquement aux emplacements suivants : D:\\ ou C:\\apps. N'utilisez pas d'autres emplacements sur le lecteur C:, car ceux-ci sont réservés. Notez que l'installation de fichiers sur le lecteur C: dans des emplacements autres que dans le dossier C:/apps peut entraîner des erreurs d'installation lors des opérations de réimageage du nœud.
6. En cas de modification des paramètres au niveau du système d'exploitation ou des fichiers de configuration du service Hadoop, vous pouvez redémarrer les services HDInsight pour qu'ils récupèrent des paramètres au niveau du système d'exploitation, tels que les variables d'environnement définies dans les scripts.


## <a name="runScriptAction"></a>Comment exécuter une action de script

Vous pouvez utiliser des actions de script pour personnaliser les clusters HDInsight avec le portail Azure, Azure PowerShell ou le Kit de développement logiciel (SDK) .NET HDInsight. Pour obtenir des instructions, consultez [Comment utiliser l'action de script](../hdinsight-hadoop-customize-cluster/#howto).


## <a name="sampleScripts"></a>Exemples de scripts personnalisés

Microsoft fournit des exemples de scripts pour installer des composants sur un cluster HDInsight. Vous trouverez des exemples de scripts et des instructions sur leur utilisation en cliquant sur les liens ci-dessous :

- [Installer et utiliser Spark sur les clusters HDInsight][hdinsight-install-spark]
- [Installer et utiliser R sur les clusters HDInsight Hadoop][hdinsight-r-scripts]
- [Installer et utiliser Solr sur les clusters HDInsight](../hdinsight-hadoop-solr-install)
- [Installer et utiliser Giraph sur les clusters HDInsight](../hdinsight-hadoop-giraph-install)  

> [AZURE.NOTE]Les exemples de scripts fonctionnent uniquement avec le cluster HDInsight version 3.1 ou version ultérieure. Pour plus d'informations sur les versions de cluster HDInsight, consultez [Versions des clusters HDInsight](../hdinsight-component-versioning/).

## <a name="testScript"></a>Comment tester votre script personnalisé avec l'émulateur HDInsight

Un moyen simple pour tester un script personnalisé avant de l'utiliser dans la commande Action de script HDInsight consiste à l’exécuter sur l'émulateur HDInsight. Vous pouvez installer l'émulateur HDInsight localement ou sur une machine virtuelle Azure IaaS Windows Server 2012 R2, ou sur un ordinateur local, et observer si le script se comporte correctement. Notez que la machine virtuelle Windows Server 2012 R2 est la même machine virtuelle que celle utilisée par HDInsight pour ses nœuds.

Cette section décrit la procédure d'utilisation de l'émulateur HDInsight en local à des fins de test. Cependant, la procédure d'utilisation d'une machine virtuelle est similaire.

**Installation de l'émulateur HDInsight** : l’exécution locale d’actions de script nécessite que l'émulateur HDInsight soit installé. Pour des instructions sur son installation, consultez la page [Prise en main de l'émulateur HDInsight](../hdinsight-get-started-emulator/).

**Définition de la stratégie d'exécution pour Azure PowerShell** : ouvrez Azure PowerShell et exécutez (en tant qu'administrateur) la commande suivante pour définir la stratégie d'exécution sur *LocalMachine* et *Unrestricted* :
 
	Set-ExecutionPolicy Unrestricted –Scope LocalMachine

Il est important que cette stratégie soit sans restriction, car les scripts ne sont pas signés.

**Téléchargez l'action de script** devant s'exécuter sur un emplacement local. Les exemples de scripts suivants sont disponibles en téléchargement à partir des emplacements suivants :

* **Spark**. https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1
* **R**. https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1
* **Solr**. https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1
* **Giraph**. https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1

**Exécution de l'action de script** : ouvrir une nouvelle fenêtre Azure PowerShell en mode administrateur et exécutez le script d'installation Spark ou R à partir de l'emplacement local où il a été enregistré.

**Exemples d'utilisation** : lorsque vous utilisez les clusters Spark et R, il se peut que les fichiers de données requis n'existent pas dans l'émulateur HDInsight. Vous pouvez alors avoir besoin de télécharger les fichiers .txt appropriés contenant des données vers un chemin d'accès dans HDFS, puis utiliser ce chemin pour accéder aux données. Par exemple :

	val file = sc.textFile("/example/data/gutenberg/davinci.txt")


il peut arriver qu'un script personnalisé dépende de composants HDInsight, par exemple pour détecter si certains services Hadoop sont opérationnels. Dans ce cas, vous devez tester vos scripts personnalisés en les déployant sur un cluster HDInsight réel.


## <a name="debugScript"></a>Comment déboguer un script personnalisé

Les journaux des erreurs de script et d'autres sorties sont stockés dans le compte de stockage par défaut que vous avez spécifié pour le cluster au moment de sa création. Les journaux sont stockés dans une table nommée *u<\\cluster-name-fragment><\\time-stamp>setuplog*. Il s'agit de journaux agrégés contenant des enregistrements de tous les nœuds (nœud principal et nœuds de travail) sur lesquels le script s'exécute dans le cluster.

Vous pouvez également accéder à distance aux nœuds de cluster pour voir les informations STDOUT et STDERR des scripts personnalisés. Les journaux de chaque nœud sont spécifiques à celui-ci et sont enregistrés dans **C:\\HDInsightLogs\\DeploymentAgent.log**. Ces fichiers journaux enregistrent tous les résultats du script personnalisé. Un extrait de journal pour une action de script Spark ressemble à ceci :

	Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.; 
	Version : 2.1.0.0; 
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692; 
	AzureVMName : HEADNODE0; 
	IsException : False; 
	ExceptionType : ; 
	ExceptionMessage : ; 
	InnerExceptionType : ; 
	InnerExceptionMessage : ; 
	Exception : ;
	...

	Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;
	
	Version : 2.1.0.0; 
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692; 
	AzureVMName : HEADNODE0; 
	IsException : False; 
	ExceptionType : ; 
	ExceptionMessage : ; 
	InnerExceptionType : ; 
	InnerExceptionMessage : ; 
	Exception : ;
	...
	
	Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; 
	Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.; 
	Version : 2.1.0.0; 
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692; 
	AzureVMName : HEADNODE0; 
	IsException : False; 
	ExceptionType : ; 
	ExceptionMessage : ; 
	InnerExceptionType : ; 
	InnerExceptionMessage : ; 
	Exception : ;

 
Dans ce journal, il apparaît clairement que l'action de script Spark a été exécutée sur la machine virtuelle nommée HEADNODE0 et qu'aucune exception n'a été générée pendant l'exécution.

En cas d'échec de l'exécution, la sortie décrivant cet échec est également contenue dans ce fichier journal. Les informations fournies dans ces journaux doivent être utiles lors du débogage des problèmes de script qui peuvent survenir.


## <a name="seeAlso"></a>Voir aussi

[Personnaliser des clusters HDInsight à l'aide d'une action de script][hdinsight-cluster-customize]


[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
[powershell-install-configure]: ../install-configure-powershell/

<!--HONumber=54-->