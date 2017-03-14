---
title: Utilisation de Hadoop Oozie dans HDInsight | Microsoft Docs
description: "Utilisation de Hadoop Oozie dans HDInsight, un service pour les données volumineuses. Découvrez comment définir un workflow Oozie et envoyer une tâche Oozie."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 870098f0-f416-4491-9719-78994bf4a369
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 0587dfcd6079fc8df91bad5a5f902391d3657a6b
ms.openlocfilehash: e6749bdf73acc9c05e71c85410bb3d95c57a0a9f
ms.lasthandoff: 12/08/2016


---
# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-in-hdinsight"></a>Utilisation d'Oozie avec Hadoop pour définir et exécuter un workflow dans HDInsight
[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Apprenez à utiliser Apache Oozie pour définir un workflow et l'exécuter sur HDInsight. Pour apprendre à utiliser le coordinateur Oozie, consultez la page [Utilisation du coordinateur Hadoop Oozie basé sur le temps avec HDInsight][hdinsight-oozie-coordinator-time]. Pour en savoir plus sur Azure Data Factory, consultez la rubrique [Utilisation de Pig et Hive avec Data Factory][azure-data-factory-pig-hive].

Apache Oozie est un système de workflow/coordination qui gère les tâches Hadoop. Il est intégré à la pile Hadoop et prend en charge les tâches Hadoop pour Apache MapReduce, Apache Pig, Apache Hive et Apache Sqoop. Il peut également être utilisé pour planifier des tâches propres à un système comme des programmes Java ou des scripts shell.

Le workflow que vous allez implémenter en suivant les instructions de ce didacticiel contient deux actions :

![Diagramme du workflow][img-workflow-diagram]

1. Une action Hive exécute un script HiveQL pour compter les occurrences de chaque type de niveau de journalisation dans un fichier log4j. Chaque fichier log4j est constitué d’une ligne de champs qui contient un champ [LOG LEVEL] pour indiquer le type et la gravité, par exemple :
   
        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...
   
    La sortie du script Hive doit ressembler à ceci :
   
        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4
   
    Pour plus d’informations sur Hive, consultez l’article [Utilisation de Hive avec HDInsight][hdinsight-use-hive].
2. Une action Sqoop exporte la sortie de HiveQL vers une table dans la base de données SQL Azure. Pour plus d’informations sur Sqoop, consultez la rubrique [Utilisation de Hadoop Sqoop avec HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Pour obtenir la liste des versions Oozie prises en charge sur les clusters HDInsight, consultez la rubrique [Nouveautés des versions de cluster Hadoop fournies par HDInsight][hdinsight-versions].
> 
> 

### <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un poste de travail sur lequel est installé Azure PowerShell**. 
  

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
  

## <a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Définition du workflow Oozie et du script HiveQL lié
Les définitions des workflows Oozie sont écrites en hPDL (un langage de définition du processus XML). Le nom du fichier de workflow par défaut est *workflow.xml*. Dans ce didacticiel, vous allez utiliser le fichier de flux de travail suivant.

    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>

        <action name="RunHiveScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.job.queue.name</name>
                        <value>${queueName}</value>
                    </property>
                </configuration>
                <script>${hiveScript}</script>
                <param>hiveTableName=${hiveTableName}</param>
                <param>hiveDataFolder=${hiveDataFolder}</param>
                <param>hiveOutputFolder=${hiveOutputFolder}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>

        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.compress.map.output</name>
                        <value>true</value>
                    </property>
                </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveOutputFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\001"</arg>
            </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>

        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>

        <end name="end"/>
    </workflow-app>

Voici les deux actions définies dans le workflow : l'action de démarrage est *RunHiveScript*. Si cette action fonctionne correctement, l’action suivante est *RunSqoopExport*.

RunHiveScript a plusieurs variables. Vous transmettez ces valeurs lors de l'envoi de la tâche Oozie à partir de votre poste de travail en utilisant Azure PowerShell.

<table border = "1">
<tr><th>Variable de workflow</th><th>Description</th></tr>
<tr><td>${jobTracker}</td><td>Spécifie l'URL du suivi des tâches Hadoop. Utilisez <strong>jobtrackerhost:9010</strong> dans les versions 3.0 et 2.1 de HDInsight.</td></tr>
<tr><td>${nameNode}</td><td>Spécifie l'URL du nœud de nom Hadoop. Utilisez l’adresse du système de fichiers par défaut, par exemple, <i>wasbs://&lt;containerName&gt;@&lt;storageAccountName&gt;.blob.core.windows.net</i>.</td></tr>
<tr><td>${queueName}</td><td>Spécifie le nom de la file d’attente auquel est envoyée la tâche. Utilisez la <strong>valeur par défaut</strong>.</td></tr>
</table>

<table border = "1">
<tr><th>Variable d'action Hive</th><th>Description</th></tr>
<tr><td>${hiveDataFolder}</td><td>Spécifie le répertoire source pour la commande Hive de création d'une table.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Spécifie le dossier de sortie pour l'instruction INSERT OVERWRITE.</td></tr>
<tr><td>${hiveTableName}</td><td>Spécifie le nom de la table Hive référençant les fichiers de données log4j.</td></tr>
</table>

<table border = "1">
<tr><th>Variable d'action Sqoop</th><th>Description</th></tr>
<tr><td>${sqlDatabaseConnectionString}</td><td>Spécifie la chaîne de connexion à la base de données SQL</td></tr>
<tr><td>${sqlDatabaseTableName}</td><td>Spécifie la table de la base de données SQL Azure vers laquelle les données sont exportées.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Spécifie le dossier de sortie pour l'instruction INSERT OVERWRITE de Hive. Il s'agit du même dossier pour Sqoop Export (export-dir).</td></tr>
</table>

Pour plus d’informations sur le workflow Oozie et l’utilisation des actions de workflow, consultez la rubrique [Documentation sur Apache Oozie 4.0][apache-oozie-400] (pour la version 3.0 de HDInsight) ou [Documentation sur Apache Oozie 3.3.2][apache-oozie-332] (pour la version 2.1 de HDInsight).

L'action Hive dans le workflow appelle un fichier de script HiveQL. Le fichier de script contient trois instructions HiveQL :

    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

1. **L'instruction DROP TABLE** supprime la table Hive log4j si elle existe.
2. **L'instruction CREATE TABLE** crée une table externe Hive log4j pointant vers l'emplacement du fichier journal log4j. Le séparateur de champ est « , ». Le séparateur de ligne par défaut est « \n ». Une table externe Hive est utilisée pour éviter que le fichier de données soit supprimé de son emplacement d'origine si vous souhaitez exécuter à plusieurs reprises le workflow Oozie.
3. **L'instruction INSERT OVERWRITE** compte les occurrences de chaque type de niveau de journalisation à partir de la table Hive log4j et enregistre la sortie vers un objet blob dans Azure Storage.

Voici les trois variables utilisées dans le script :

* ${hiveTableName}
* ${hiveDataFolder}
* ${hiveOutputFolder}

Le fichier de définition du workflow (workflow.xml dans ce didacticiel) transmet ces valeurs à ce script HiveQL au moment de l'exécution.

Le fichier de flux de travail et le fichier HiveQL sont stockés dans un conteneur d’objets blob.  Le script PowerShell que vous utiliserez plus loin dans ce didacticiel copie les deux fichiers dans le compte de stockage par défaut. 

## <a name="submit-oozie-jobs-using-powershell"></a>Soumettre des tâches Oozie avec PowerShell
Azure PowerShell ne fournit actuellement aucune cmdlet pour la définition de tâches Oozie. Vous pouvez utiliser la cmdlet **Invoke-RestMethod** pour appeler les services web Oozie. L'API des services web Oozie est une API JSON REST HTTP. Pour plus d’informations sur l’API des services web Oozie, consultez la page [Documentation sur Apache Oozie 4.0][apache-oozie-400] (pour la version 3.0 de HDInsight) ou [Documentation sur Apache Oozie 3.3.2][apache-oozie-332] (pour la version 2.1 de HDInsight).

Le script PowerShell de cette section effectue les étapes suivantes :

1. Connexion à Azure.
2. Création d’un groupe de ressources Azure. Pour plus d’informations, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).
3. Création d’un serveur Base de données SQL Azure, d’une base de données SQL Azure et de deux tables. Ceux-ci sont utilisés par l’action Sqoop dans le flux de travail.
   
    Le nom de la table est *log4jLogCount*.
4. Créez un cluster HDInsight pour exécuter des tâches Oozie.
   
    Pour examiner le cluster, vous pouvez utiliser le portail Azure ou Azure PowerShell.
5. Copiez le fichier de flux de travail oozie et le fichier de script HiveQL dans le système de fichiers par défaut.
   
    Les deux fichiers sont stockés dans un conteneur d’objets blob public.
   
   * Copie du script HiveQL (useoozie.hql) dans le stockage Azure (wasbs:///tutorials/useoozie/useoozie.hql).
   * Copie de workflow.xml dans wasbs:///tutorials/useoozie/workflow.xml.
   * Copie du fichier de données (/example/data/sample.log) dans wasbs:///tutorials/useoozie/data/sample.log.
6. Soumettez une tâche Oozie.
   
    Pour examiner les résultats de la tâche OOzie, utilisez Visual Studio ou d’autres outils pour vous connecter à la base de données SQL Azure.

Voici le script.  Vous pouvez exécuter le script à partir de Windows PowerShell ISE. Vous devez uniquement configurer les 7 premières variables.

    #region - provide the following values

    $subscriptionID = "<Enter your Azure subscription ID>"

    # SQL Database server login credentials used for creating and connecting
    $sqlDatabaseLogin = "<Enter SQL Database Login Name>"
    $sqlDatabasePassword = "<Enter SQL Database Login Password>"

    # HDInsight cluster HTTP user credential used for creating and connectin
    $httpUserName = "admin"  # The default name is "admin"
    $httpPassword = "<Enter HDInsight Cluster HTTP User Password>"

    # Used for creating Azure service names
    $nameToken = "<Enter an Alias>"
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion

    #region - variables

    # Resource group variables
    $resourceGroupName = $namePrefix + "rg"
    $location = "East US 2" # used by all Azure services defined in this tutorial

    # SQL database varialbes
    $sqlDatabaseServerName = $namePrefix + "sqldbserver"
    $sqlDatabaseName = $namePrefix + "sqldb"
    $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $sqlDatabaseMaxSizeGB = 10

    # Used for retrieving external IP address and creating firewall rules
    $ipAddressRestService = "http://bot.whatismyipaddress.com"
    $fireWallRuleName = "UseSqoop"

    # HDInsight variables
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{
        Login-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionId $subscriptionID
    }
    #endregion

    #region - Create Azure resouce group
    Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
    try{
        Get-AzureRmResourceGroup -Name $resourceGroupName
    }
    catch{
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    }
    #endregion

    #region - Create Azure SQL database server
    Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
    try{
        Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
    catch{
        Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

        $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
        $sqlLoginCredentials = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

        $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                    -ResourceGroupName $resourceGroupName `
                                    -ServerName $sqlDatabaseServerName `
                                    -SqlAdministratorCredentials $sqlLoginCredentials `
                                    -Location $location).ServerName
        Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan

        Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
        $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-workstation" `
            -StartIpAddress $workstationIPAddress `
            -EndIpAddress $workstationIPAddress

        #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
        #Note that this allows Azure traffic from any Azure subscription to access the server.
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-Azureservices" `
            -StartIpAddress "0.0.0.0" `
            -EndIpAddress "0.0.0.0"
    }
    #endregion

    #region - Create and validate Azure SQL database
    Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green

    try {
        Get-AzureRmSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName
    }
    catch {
        New-AzureRMSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName `
            -Edition "Standard" `
            -RequestedServiceObjectiveName "S1"
    }
    #endregion

    #region - Create SQL database tables
    Write-Host "Creating the log4jlogs table  ..." -ForegroundColor Green

    $sqlDatabaseTableName = "log4jLogsCount"
    $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
            [Level] [nvarchar](10) NOT NULL,
            [Total] float,
        CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
        (
        [Level] ASC
        )
        )"

    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = $sqlDatabaseConnectionString
    $conn.Open()

    # Create the log4jlogs table and index
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.Connection = $conn
    $cmd.CommandText = $cmdCreateLog4jCountTable
    $cmd.ExecuteNonQuery()

    $conn.close()
    #endregion

    #region - Create HDInsight cluster

    Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green

    # Create the default storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS

    # Create the default Blob container
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                        -StorageAccountName $defaultStorageAccountName `
                                        -StorageAccountKey $defaultStorageAccountKey 
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext 

    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -Location $location `
        -ClusterType Hadoop `
        -OSType Windows `
        -ClusterSizeInNodes 2 `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultBlobContainerName 

    # Validate the cluster
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
    #endregion

    #region - copy Oozie workflow and HiveQL files

    Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green

    # Both files are stored in a public Blob
    $publicBlobContext = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous

    # WASB folder for storing the Oozie tutorial files.
    $destFolder = "tutorials/useoozie"  # Do NOT use the long path here

    Start-CopyAzureStorageBlob `
        -Context $publicBlobContext `
        -SrcContainer "useoozie" `
        -SrcBlob "useooziewf.hql"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -DestBlob "$destFolder/useooziewf.hql" `
        -Force

    Start-CopyAzureStorageBlob `
        -Context $publicBlobContext `
        -SrcContainer "useoozie" `
        -SrcBlob "workflow.xml"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -DestBlob "$destFolder/workflow.xml" `
        -Force

    #validate the copy
    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/workflow.xml

    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/useooziewf.hql

    #endregion

    #region - copy the sample.log file

    Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green

    Start-CopyAzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -SrcContainer $defaultBlobContainerName `
        -SrcBlob "example/data/sample.log"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -destBlob "$destFolder/data/sample.log" 

    #validate the copy
    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/data/sample.log

    #endregion

    #region - submit Oozie job

    $storageUri="wasbs://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net"

    $oozieJobName = $namePrefix + "OozieJob"

    #Oozie WF variables
    $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
    $waitTimeBetweenOozieJobStatusCheck=10

    #Hive action variables
    $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
    $hiveTableName = "log4jlogs"
    $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
    $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

    #Sqoop action variables
    $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

    $OoziePayload =  @"
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

    <property>
        <name>nameNode</name>
        <value>$storageUrI</value>
    </property>

    <property>
        <name>jobTracker</name>
        <value>jobtrackerhost:9010</value>
    </property>

    <property>
        <name>queueName</name>
        <value>default</value>
    </property>

    <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
    </property>

    <property>
        <name>hiveScript</name>
        <value>$hiveScript</value>
    </property>

    <property>
        <name>hiveTableName</name>
        <value>$hiveTableName</value>
    </property>

    <property>
        <name>hiveDataFolder</name>
        <value>$hiveDataFolder</value>
    </property>

    <property>
        <name>hiveOutputFolder</name>
        <value>$hiveOutputFolder</value>
    </property>

    <property>
        <name>sqlDatabaseConnectionString</name>
        <value>&quot;$sqlDatabaseConnectionString&quot;</value>
    </property>

    <property>
        <name>sqlDatabaseTableName</name>
        <value>$SQLDatabaseTableName</value>
    </property>

    <property>
        <name>user.name</name>
        <value>$httpUserName</value>
    </property>

    <property>
        <name>oozie.wf.application.path</name>
        <value>$oozieWFPath</value>
    </property>

    </configuration>
    "@

    Write-Host "Checking Oozie server status..." -ForegroundColor Green
    $clusterUriStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/admin/status"
    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $httpCredential -OutVariable $OozieServerStatus

    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $oozieServerSatus = $jsonResponse[0].("systemMode")
    Write-Host "Oozie server status is $oozieServerSatus."

    # create Oozie job
    Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
    Write-Host "`n--------`n$OoziePayload`n--------"
    $clusterUriCreateJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/jobs"
    $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $httpCredential -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug

    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $oozieJobId = $jsonResponse[0].("id")
    Write-Host "Oozie job id is $oozieJobId..."

    # start Oozie job
    Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
    $clusterUriStartJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
    $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $httpCredential | Format-Table -HideTableHeaders #-debug

    # get job status
    Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
    Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

    Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
    $clusterUriGetJobStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
    $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $JobStatus = $jsonResponse[0].("status")

    while($JobStatus -notmatch "SUCCEEDED|KILLED")
    {
        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $JobStatus = $jsonResponse[0].("status")
        $jobStatus
    }

    Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green

    #endregion


**Réexécution du didacticiel**

Pour réexécuter le workflow, vous devez supprimer les éléments suivants :

* Le fichier de sortie du script Hive
* Les données dans la table log4jLogsCount

Voici un exemple d'un script PowerShell que vous pouvez utiliser :

    $resourceGroupName = "<AzureResourceGroupName>"

    $defaultStorageAccountName = "<AzureStorageAccountName>"
    $defaultBlobContainerName = "<ContainerName>"

    #SQL database variables
    $sqlDatabaseServerName = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabasePassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $defaultBlobContainerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à définir un flux de travail Oozie et à exécuter une tâche Oozie en utilisant PowerShell. Pour en savoir plus, consultez les articles suivants :

* [Utilisation du coordinateur Oozie basé sur le temps avec HDInsight][hdinsight-oozie-coordinator-time]
* [Prise en main de Hadoop avec Hive dans HDInsight pour analyser l’utilisation des téléphones mobiles][hdinsight-get-started]
* [Utilisation de Stockage Blob Azure avec HDInsight][hdinsight-storage]
* [Administration de HDInsight à l’aide de PowerShell][hdinsight-admin-powershell]
* [Importation de données pour les tâches Hadoop dans HDInsight][hdinsight-upload-data]
* [Utilisation de Sqoop avec Hadoop dans HDInsight][hdinsight-use-sqoop]
* [Utilisation de Hive avec Hadoop sur HDInsight][hdinsight-use-hive]
* [Utilisation de Pig avec Hadoop sur HDInsight][hdinsight-use-pig]
* [Développement de programmes MapReduce en Java pour HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/data-factory-data-transformation-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: ../sql-database-create-configure.md
[sqldatabase-get-started]: ../sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/en-us/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

