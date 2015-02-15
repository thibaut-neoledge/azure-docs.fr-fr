<properties 
	pageTitle="Développement d'une action de script avec HDInsight| Azure" 
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
	ms.date="12/19/2014" 
	ms.author="bradsev"/> 

# Développement d'action de script avec HDInsight 

Les actions de script fournissent une fonctionnalité Azure HDInsight permettant d'installer des logiciels supplémentaires s'exécutant sur un cluster Hadoop ou de modifier la configuration des applications installées sur un cluster. Les actions de script sont des scripts qui s'exécutent sur des nœuds de cluster lors du déploiement des clusters HDInsight. Elles sont exécutées une fois la configuration de HDInsight terminée dans le cluster. Une action de script est exécutée avec les privilèges d'un compte d'administrateur système qui fournissent des droits d'accès complets aux nœuds du cluster. Chaque cluster peut recevoir une liste d'actions de script qui sont exécutées dans l'ordre spécifié.

Une action de script peut être déployée à partir d'Azure PowerShell ou à l'aide du Kit de développement logiciel (SDK) .NET HDInsight.  Pour plus d'informations, consultez la rubrique [Personnalisation d'un cluster HDInsight à l'aide d'actions de script][hdinsight-cluster-customize].


## Dans cet article

- [Meilleures pratiques relatives au développement de scripts](#bestPracticeScripting)
- [Méthodes d'assistance pour les scripts personnalisés](#helpermethods)
- [Liste de contrôle en vue du déploiement d'une action de script](#deployScript)
- [Comment exécuter une action de script](#runScriptAction)
- [Exemples de scripts personnalisés](#sampleScripts) 
- [Comment tester votre script personnalisé avec l'émulateur HDInsight](#testScript)
- [Comment déboguer un script personnalisé](#debugScript)
- [Voir aussi](#seeAlso)


## <a name="bestPracticeScripting"></a>Meilleures pratiques relatives au développement de scripts

Quand vous développez un script personnalisé pour un cluster HDInsight, tenez compte des meilleures pratiques suivantes :

* [Rechercher la version Hadoop](#bPS1)
* [Fournir des liens stables vers les ressources de script](#bPS2)
* [Idempotence du script de personnalisation du cluster](#bPS3)
* [Emplacement d'installation des composants personnalisés](#bPS4)
* [Architecture de cluster : garantir une haute disponibilité](#bPS5)
* [Configurer les composants personnalisés pour utiliser WASB](#bPS6)

### <a name="bPS1"></a>Rechercher la version Hadoop
Seul HDInsight 3.1 (Hadoop 2.4) ou version ultérieure prend en charge l'utilisation d'une action de script pour installer des composants personnalisés sur un cluster. Dans votre script personnalisé, vous devez utiliser la méthode d'assistance **Get-HDIHadoopVersion** pour vérifier la version de Hadoop avant d'effectuer d'autres tâches dans le script.


### <a name="bPS2"></a>Fournir des liens stables vers les ressources de script 
Les utilisateurs doivent s'assurer que tous les scripts et autres artefacts utilisés dans la personnalisation d'un cluster sont disponibles tout au long de la durée de vie du cluster et que les versions de ces fichiers ne changent pas au cours de cette période. Ces ressources sont requises si le réimageage des nœuds dans le cluster est requis. La meilleure pratique consiste à tout télécharger et à tout archiver dans un compte de stockage contrôlé par l'utilisateur. Il peut s'agir du compte de stockage par défaut ou de l'un des comptes de stockage supplémentaires spécifiés lors du déploiement pour un cluster personnalisé.
Dans les exemples de clusters personnalisés Spark et R fournis dans la documentation, par exemple, une copie locale des ressources contenues dans ce compte de stockage est effectuée.: https://hdiconfigactions.blob.core.windows.net/.


### <a name="bPS3"></a>Idempotence du script de personnalisation du cluster
Vous devez tenir compte du réimageage des nœuds d'un cluster HDInsight au cours de la durée de vie du cluster. Le script de personnalisation du cluster est exécuté chaque fois qu'un cluster est réimagé. Ce script doit être conçu de manière idempotente pour que le script, après réimageage, puisse s'assurer que l'état personnalisé du cluster dans lequel ce dernier se trouvait lors de sa création au terme de la première exécution du script est rétabli. Par exemple, si un script personnalisé installe une application à l'emplacement D:\AppLocation lors de sa première exécution, il doit, à chaque exécution ultérieure au moment du réimageage, vérifier si l'application existe à l'emplacement D:\AppLocation avant de passer aux autres étapes dans le script.


### <a name="bPS4"></a>Emplacement d'installation des composants personnalisés 
Lorsque des nœuds de cluster sont réimagés, le lecteur de ressources C:\ et le lecteur système D:\ peuvent être reformatés, ce qui se traduit par la perte des données et applications qui y avaient été installées. Cela peut aussi arriver si un nœud de machine virtuelle Azure qui fait partie du cluster tombe en panne et est remplacé par un nouveau nœud. Vous pouvez installer des composants sur le lecteur D:/ ou à l'emplacement C:\apps sur le cluster. Tous les autres emplacements sur le lecteur C:\ sont réservés. Indiquez l'emplacement d'installation des applications ou bibliothèques dans le script de personnalisation de cluster. 


### <a name="bPS5"></a>Architecture de cluster : garantir une haute disponibilité

HDInsight possède une architecture active/passive pour la haute disponibilité, dans laquelle un nœud principal est en mode actif (dans lequel les services HDInsight s'exécutent) et l'autre en mode veille (dans lequel les services HDInsight ne s'exécutent pas). Les nœuds basculent entre les modes actif et passif si les services HDInsight sont interrompus. Si une action de script est utilisée pour installer les services sur les deux nœuds principaux pour la haute disponibilité, notez que le mécanisme de basculement HDInsight ne sera pas en mesure de faire basculer automatiquement les services installés par l'utilisateur. Par conséquent, les services installés par l'utilisateur sur les nœuds principaux HDInsight qui sont censés offrir une haute disponibilité doivent soit posséder leur propre mécanisme de basculement si le mode actif/passif est activé, soit être en mode actif/actif. 

Une commande Action de script HDInsight s'exécute sur les deux nœuds principaux lorsque le rôle de nœud principal est spécifié comme valeur dans le paramètre *ClusterRoleCollection* (décrit ci-dessous dans la section [Comment exécuter une action de script](#runScriptAction)). Par conséquent, lorsque vous concevez des scripts personnalisés, vérifiez qu'il est au fait de cette configuration. Vous devez vous assurer que des services identiques ne sont pas installés et démarrés sur les deux nœuds principaux pour éviter les conflits. Sachez, par ailleurs, que les données seront perdues lors des réimageages ; les logiciels installés à l'aide d'actions de script ne doivent donc pas être affectés par de tels événements. Les applications doivent être conçues pour fonctionner avec des données hautement disponibles qui sont distribuées sur de nombreux nœuds. Notez que jusqu'à 20 % des nœuds d'un cluster peuvent être réimagés en même temps.


### <a name="bPS6"></a>Configurer les composants personnalisés pour utiliser WASB
Les composants personnalisés que vous installez sur les nœuds du cluster peuvent être configurés, par défaut, pour utiliser le stockage HDFS. Vous devez modifier la configuration de manière à utiliser WASB (Azure Storage Blob). Sur un réimageage de cluster, le système de fichiers HDFS est formaté et vous perdrez alors toutes les données qui y sont stockées. L'utilisation de WASB garantit la conservation des données.

## <a name="helpermethods"></a>Méthodes d'assistance pour les scripts personnalisés 

L'action de script fournit les méthodes d'assistance suivantes que vous pouvez utiliser lors de l'écriture de scripts personnalisés.

Méthode d'assistance | Description
-------------- | -----------
**Save-HDIFile** | Télécharger un fichier à partir de l'URI spécifiée vers un emplacement du disque local qui est associé au nœud de machine virtuelle Azure affecté au cluster.
**Expand-HDIZippedFile** | Décompresser un fichier zippé.
**Invoke-HDICmdScript** | Exécuter un script à partir de cmd.exe.
**Write-HDILog** | Écrire la sortie du script personnalisé utilisé pour l'action de script.
**Get-Services** | Obtenir la liste des services en cours d'exécution sur la machine où s'exécute le script.
**Get-Service** | En utilisant le nom de service spécifique comme entrée, cette méthode renvoie des informations détaillées pour un service spécifique (nom du service, ID de processus, état, etc.) sur l'ordinateur où s'exécute le script.
**Get-HDIServices** | Obtenir la liste des services HDInsight en cours d'exécution sur l'ordinateur où s'exécute le script.
**Get-HDIService** | En utilisant le nom de service HDInsight spécifique comme entrée, cette méthode renvoie des informations détaillées pour un service spécifique (nom du service, ID de processus, état, etc.) sur l'ordinateur où s'exécute le script.
**Get-ServicesRunning** | Obtenir la liste des services en cours d'exécution sur l'ordinateur où s'exécute le script.
**Get-ServiceRunning** | Vérifier si un service spécifique (par nom) est en cours d'exécution sur l'ordinateur où s'exécute le script.
**Get-HDIServicesRunning** | Obtenir la liste des services HDInsight en cours d'exécution sur l'ordinateur où s'exécute le script.
**Get-HDIServiceRunning** | Vérifier si un service HDInsight spécifique (par nom) est en cours d'exécution sur l'ordinateur où s'exécute le script.
**Get-HDIHadoopVersion** | Obtenir la version de Hadoop installée sur l'ordinateur où s'exécute le script.
**Test-IsHDIHeadNode** | Vérifier si l'ordinateur où s'exécute le script est un nœud principal.
**Test-IsActiveHDIHeadNode** | Vérifier si l'ordinateur où s'exécute le script est un nœud principal actif.
**Test-IsHDIDataNode** | Vérifier si l'ordinateur où s'exécute le script est un nœud de données.
**Edit-HDIConfigFile** | Modifier les fichiers de configuration hive-site.xml, core-site.xml, hdfs-site.xml, mapred-site.xml ou yarn-site.xml.
 

## <a name="deployScript"></a>Liste de contrôle en vue du déploiement d'une action de script
Voici les étapes à suivre avant de déployer des scripts :

1. Placez les fichiers qui contiennent les scripts personnalisés dans un emplacement accessible aux nœuds du cluster lors du déploiement. Il peut s'agir de tout compte de stockage par défaut ou supplémentaire spécifié lors du déploiement du cluster, ou de tout conteneur de stockage accessible au public.
2. Ajoutez des contrôles dans les scripts pour vous assurer qu'ils s'exécutent de manière idempotente, ce qui permet d'exécuter le script plusieurs fois sur le même nœud.
3. Utilisez la cmdlet Powershell **Write-Output** pour imprimer dans STDOUT et dans STDERR. N'utilisez pas **Write-Host**.
4. Utilisez un dossier de fichiers temporaires, tel que $env:TEMP, pour conserver le fichier téléchargé utilisé par les scripts, puis nettoyez-le au terme de l'exécution des scripts.
5. Installez des logiciels personnalisés uniquement aux emplacements suivants : D:/ ou C:/apps. N'utilisez pas d'autres emplacements sur le lecteur C:, car ceux-ci sont réservés. Notez que l'installation de fichiers sur le lecteur C: dans des emplacements autres que dans le dossier C:/apps peut entraîner des erreurs d'installation lors des opérations de réimageage du nœud.
6. En cas de modification des paramètres au niveau du système d'exploitation ou des fichiers de configuration du service Hadoop, vous pouvez redémarrer les services HDInsight pour qu'ils récupèrent des paramètres au niveau du système d'exploitation, tels que les variables d'environnement définies dans les scripts.


## <a name="runScriptAction"></a>Comment exécuter une action de script

Vous pouvez utiliser des actions de script pour personnaliser des clusters HDInsight à l'aide du portail de gestion Azure, de PowerShell ou du Kit de développement (SDK) HDInsight .NET. Pour obtenir des instructions, consultez la rubrique traitant de [l'utilisation de l'action de script](./hdinsight-hadoop-customize-cluster/#howto). 


## <a name="sampleScripts"></a>Exemples de scripts personnalisés

Microsoft fournit des exemples de scripts pour installer des composants sur un cluster HDInsight. Vous trouverez des exemples de scripts et des instructions sur leur utilisation en cliquant sur les liens ci-dessous :

- [Installation et utilisation de Spark 1.0 sur des clusters HDInsight][hdinsight-install-spark]
- [Installation et utilisation de R sur des clusters HDInsight Hadoop][hdinsight-r-scripts]
- [Installation et utilisation de Solr sur des clusters HDInsight](../hdinsight-hadoop-solr-install)
- [Installation et utilisation de Giraph sur des clusters HDInsight](../hdinsight-hadoop-giraph-install)  

> [AZURE.NOTE] L'exemple de script fonctionne uniquement avec un cluster HDInsight version 3.1 ou supérieure. Pour plus d'informations sur les versions des clusters HDInsight, consultez la page [Versions des clusters HDInsight](http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-component-versioning/).

## <a name="testScript"></a>Comment tester votre script personnalisé avec l'émulateur HDInsight

Pour tester un script personnalisé avant de l'utiliser dans une commande Action de script HDInsight, vous pouvez simplement l'exécuter sur l'émulateur HDInsight. L'émulateur peut être installé en local ou sur une machine virtuelle Azure IaaS Windows Server 2012 R2. Examinez ensuite s'il se comporte correctement. Notez que Windows Server 2012 R2 est la même machine virtuelle que celle utilisée par HDInsight pour ses nœuds.

Cette section décrit la procédure d'utilisation de l'émulateur HDInsight en local à des fins de test. Cependant, la procédure d'utilisation d'une machine virtuelle est similaire.

**Installation de l'émulateur HDInsight** : Pour exécuter des actions de script en local, l'émulateur HDInsight doit être installé. Pour plus d'informations sur l'installation, consultez la rubrique [Prise en main de l'émulateur HDInsight](http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-get-started-emulator/).

**Définition de la stratégie d'exécution pour Azure PowerShell :** ouvrez Microsoft Azure PowerShell et exécutez (en tant qu'administrateur) la commande suivante pour définir la stratégie d'exécution sur la  *LocalMachine* et pour qu'elle soit  *Unrestricted*.
 
	Set-ExecutionPolicy Unrestricted -Scope LocalMachine

Il est important que cette stratégie soit sans restriction, car les scripts ne sont pas signés.

**Téléchargez l'action de script** qui doit s'exécuter sur un emplacement local. Les scripts Spark et R pouvant être exécutés en local sont disponibles, par exemple, aux emplacements suivants :

* https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1
* https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1

**Exécution des scripts d'action** : ouvrez une nouvelle session Azure PowerShell en mode administrateur et exécutez le script d'installation Spark ou R à partir de l'emplacement local où il a été enregistré.

**Exemples d'utilisation**
Lorsque vous utilisez les clusters Spark et R, il se peut que les fichiers de données requis n'existent pas dans l'émulateur HDInsight et que vous deviez, par conséquent, télécharger les fichiers .txt appropriés contenant des données vers un chemin d'accès dans HDFS, puis utiliser ce chemin pour accéder aux données. Par exemple :

	val file = sc.textFile("/example/data/gutenberg/davinci.txt")


il peut arriver qu'un script personnalisé dépende de composants HDInsight, par exemple pour détecter si certains services Hadoop sont opérationnels. Dans ce cas, vous devez tester vos scripts personnalisés en les déployant sur un cluster HDInsight réel.


## <a name="debugScript"></a>Comment déboguer un script personnalisé

Les journaux des erreurs de script et d'autres sorties sont stockés dans le compte de stockage par défaut que vous avez spécifié pour le cluster au moment de sa création. Les journaux sont stockés dans une table avec le nom *u<\cluster-name-fragment><\time-stamp>setuplog*. Il s'agit de journaux agrégés contenant des enregistrements de tous les nœuds (nœud principal et nœuds de travail) sur lesquels le script s'exécute dans le cluster.

Vous pouvez également accéder à distance aux nœuds de cluster pour voir les informations STDOUT et STDERR des scripts personnalisés. Les journaux de chaque nœud sont spécifiques à celui-ci et sont enregistrés dans **C:\HDInsightLogs\DeploymentAgent.log**. Ces fichiers journaux enregistrent tous les résultats du script personnalisé. Un extrait de journal pour une action de script Spark ressemble à ceci :

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

En cas d'échec de l'exécution, la sortie décrivant cet échec est également contenue dans ce fichier journal. Les informations fournies dans ces journaux sont utiles pour résoudre les problèmes de script qui peuvent se produire.


## <a name="seeAlso"></a>Voir aussi

[Personnaliser des clusters HDInsight à l'aide d'une action de script][hdinsight-cluster-customize] 


[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
[powershell-install-configure]: ../install-configure-powershell/
<!--HONumber=42-->
