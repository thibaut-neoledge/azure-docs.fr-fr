<properties
	pageTitle="Services de débogage et d’analyse Hadoop avec des dumps de tas | Microsoft Azure"
	description="Collectez automatiquement les dumps de tas de services Hadoop et placez-les dans le compte de stockage Azure Blob à des fins de débogage et d’analyse."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="jgao"/>


# Collecter les dumps de tas dans le stockage Blob pour les services de débogage et d'analyse Hadoop

[AZURE.INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Les dumps de tas contiennent un instantané de la mémoire de l’application, y compris des valeurs des variables au moment de la création du dump. Ils sont donc très utiles pour diagnostiquer les problèmes qui se produisent au moment de l’exécution. Il est possible de collecter automatiquement les dumps de tas de services Hadoop et de les placer dans le compte de stockage d’objets blob Azure d’un utilisateur sous HDInsightHeapDumps/.

La collection des dumps de tas pour différents services doit être activée pour les services sur des clusters individuels. Par défaut, cette fonctionnalité est désactivée pour un cluster. Les dumps de tas pouvant être volumineux, nous vous recommandons de surveiller le compte de stockage d’objets blob dans lequel ils sont enregistrés une fois la collection activée.

> [AZURE.NOTE] Les informations mentionnées dans cet article s’appliquent uniquement aux clusters HDInsight sur Windows. Pour plus d’informations sur HDInsight sur Linux, consultez la rubrique [Activation des dumps de tas pour les services Hadoop sur HDInsight sur Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)

## Services éligibles pour le vidage de tas

Vous pouvez activer des dumps de tas pour les services suivants :

*  **hcatalog** - tempelton
*  **hive** - hiveserver2, metastore, derbyserver
*  **mapreduce** - jobhistoryserver
*  **yarn** - resourcemanager, nodemanager, timelineserver
*  **hdfs** - datanode, secondarynamenode, namenode

## Éléments de configuration permettant d’activer le vidage de tas

Pour activer des dumps de tas sur un service, vous devez définir les éléments de configuration appropriés dans la section de ce service, qui est spécifié par **service\_name**.

	"javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
	"javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

La valeur de **service\_name** peut être l’un des services répertoriés ci-dessus : tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode ou namenode.

## Activer à l’aide d’Azure PowerShell

Par exemple, pour activer les dumps de tas à l’aide d’Azure PowerShell pour le service jobhistoryserver, procédez comme suit :

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

	$MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## Activer avec le kit de développement logiciel (SDK) .NET

Par exemple, pour activer les dumps de tas à l’aide du Kit de développement logiciel (SDK) Azure HDInsight pour le service jobhistoryserver, procédez comme suit :

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));

<!---HONumber=AcomDC_0914_2016-->