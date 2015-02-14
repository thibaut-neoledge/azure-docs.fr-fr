<properties 
	pageTitle="Collecter les dumps de tas pour le débogage et l'analyse| Azure" 
	description="Collecter les dumps de tas pour le débogage et l'analyse" 
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
	ms.date="12/08/2014" 
	ms.author="bradsev"/>

# Collecter les dumps de tas pour le débogage et l'analyse

Il est possible de collecter automatiquement les dumps de tas de services Hadoop et de les placer dans le compte de stockage d'objets blob d'un utilisateur sous HDInsightHeapDumps/.  Les fichiers dump d'un service avec des tas contiennent un instantané de la mémoire de l'application. Ils comprennent notamment les valeurs des variables au moment de la création du dump.

La collection des dumps de tas pour différents services doit être activée pour les services sur des clusters individuels. Par défaut, cette fonctionnalité est désactivée pour un cluster. Les dumps de tas pouvant être volumineux, il est recommandé de surveiller le compte de stockage d'objets blob dans lequel ils sont enregistrés une fois la collection activée.

## Dans cet article

- [Pour quels services les dumps de tas peuvent-ils être activés ?](#whichServices)
- [Éléments de configuration permettant d'activer les dumps de tas](#configuration)
- [Comment activer des dumps de tas avec Azure HDInsight PowerShell](#powershell)
- [Comment activer des dumps de tas avec le SDK .NET HDInsight](#sdk)


## <a name="whichServices"></a>Pour quels services les dumps de tas peuvent-ils être activés ?

Les services pour lesquels les dumps de tas peuvent être activés sur demande sont les suivants : 

*  **hcatalog** : tempelton
*  **hive** : hiveserver2, metastore, derbyserver 
*  **mapreduce** : jobhistoryserver 
*  **yarn** : resourcemanager, nodemanager, timelineserver 
*  **hdfs**: datanode, secondarynamenode, namenode

## <a name="configuration"></a>Éléments de configuration permettant d'activer les dumps de tas

Pour activer des dumps de tas sur un service, l'utilisateur doit définir les éléments de configuration appropriés dans la section de ce service, qui est spécifié par <service_name>.

	"javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
	"javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof" 

La valeur de <service_name> peut être l'un des services répertoriés ci-dessus : tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode ou namenode.

## <a name="powershell"></a>Comment activer des dumps de tas avec Azure HDInsight PowerShell

Par exemple, pour activer les dumps de tas à l'aide de PowerShell pour le service jobhistoryserver, l'utilisateur doit procéder comme suit :

À l'aide du SDK Powershell :

	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

	$MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="sdk"></a>Comment activer des dumps de tas avec le SDK Azure HDInsight .NET

Par exemple, pour activer les dumps de tas à l'aide du SDK .NET pour le service jobhistoryserver, l'utilisateur doit procéder comme suit :

À l'aide du SDK C# :

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));



<!--HONumber=42-->
