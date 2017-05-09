---
title: Utilisation de Python avec Hive et Pig dans HDInsight | Microsoft Docs
description: "Découvrez comment utiliser des fonctions définies par l’utilisateur Python à partir de Hive et Pig dans HDInsight, la pile de technologies Hadoop sur Azure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c44d6606-28cd-429b-b535-235e8f34a664
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/27/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 985f14ce7b8249c8e80deb1851cfee8c89651786
ms.contentlocale: fr-fr
ms.lasthandoff: 04/27/2017

---
# <a name="use-python-user-defined-functions-udf-with-hive-and-pig-in-hdinsight"></a>Utiliser des fonctions définies par l’utilisateur (UDF) Python avec Hive et Pig dans HDInsight

Les composants Hive et Pig sont parfaits pour traiter des données dans HDInsight, mais vous avez parfois besoin d’un langage plus généraliste. Hive et Pig vous permettent tous deux de créer des fonctions définies par l’utilisateur (UDF) à l’aide de nombreux langages de programmation. Dans cet article, vous allez apprendre à utiliser une fonction définie par l’utilisateur Python à partir de Hive et de Pig.

## <a name="requirements"></a>Configuration requise

* Un cluster HDInsight

  > [!IMPORTANT]
  > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

* Un éditeur de texte

## <a name="python"></a>Python sur HDInsight

Python 2.7 est installé par défaut sur des clusters HDInsight 3.0 et versions ultérieures. Hive peut être utilisé avec cette version de Python pour le traitement par flux (les données sont transmises entre Hive et Python avec STDOUT/STDIN).

HDInsight inclut également Jython, une implémentation de Python écrite en Java. Vous avez intérêt à l'utiliser avec Pig, car ce dernier sait comment communiquer avec Jython sans devoir recourir à la diffusion en continu. Vous pouvez également utiliser la version normale de Python (C Python) avec Pig.

## <a name="hivepython"></a>Hive et Python

Python peut être utilisé en tant que fonction définie par l'utilisateur à partir de Hive via l'instruction HiveQL **TRANSFORM** . Par exemple, le code HiveQL suivant appelle un script Python stocké dans le fichier **streaming.py** .

**HDInsight Linux**

```hiveql
add file wasbs:///streaming.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python streaming.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**HDInsight Windows**

```hiveql
add file wasbs:///streaming.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe streaming.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]
> Dans les clusters HDInsight Windows, la clause **USING** doit spécifier le chemin d’accès complet à python.exe.

Cet exemple effectue ce qui suit :

1. L’instruction **add file** au début du fichier ajoute le fichier **streaming.py** au cache distribué ; il est donc accessible à tous les nœuds du cluster.
2. L'instruction **SELECT TRANSFORM ... USING** sélectionne les données de la table **hivesampletable**. Elle transmet également les valeurs clientid, devicemake et devicemodel au script **streaming.py**.
3. La clause **AS** décrit les champs renvoyés par **streaming.py**.

<a name="streamingpy"></a> Voici le fichier **streaming.py** utilisé par l’exemple HiveQL :

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

Le script effectue les opérations suivantes :

1. Une ligne de données de STDIN est lue.
2. Le caractère de saut de ligne de fin est supprimé à l’aide de `string.strip(line, "\n ")`.
3. Lors du traitement par flux, une seule ligne contient toutes les valeurs séparées par un caractère de tabulation. `string.split(line, "\t")` peut donc être utilisé pour fractionner l’entrée à chaque tabulation et retourner uniquement les champs.
4. Une fois le traitement terminé, la sortie doit être écrite dans STDOUT sur une seule ligne, chaque champ étant séparé par une tabulation. Cette opération est réalisée avec `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. La boucle `while` se répète jusqu’à ce que plus aucun élément `line` ne soit lu.

La sortie du script est une concaténation des valeurs d’entrée pour `devicemake` et `devicemodel`, ainsi qu’un code de hachage de la valeur concaténée.

Pour savoir comment exécuter cet exemple sur votre cluster HDInsight, consultez la rubrique [Exécution des exemples](#running) .

## <a name="pigpython"></a>Pig et Python

Un script Python peut être utilisé comme une fonction définie par l'utilisateur à partir de Pig via l'instruction **GENERATE** . Vous pouvez exécuter le script à l’aide de Jython ou de C Python.

La différence entre ces deux méthodes est que Jython s’exécute sur l’environnement Machine virtuelle Java (JVM) et peut être appelé en mode natif à partir de Pig. C Python est un processus externe. Par conséquent, les données de Pig sur JVM sont envoyées au script s’exécutant dans un processus Python. La sortie du script Python est ensuite renvoyée dans Pig.

Pour déterminer si Pig utilise Jython ou C Python pour exécuter le script, utilisez la commande **register** lorsque vous référencez le script Python depuis Pig Latin. Les exemples suivants permettent d’enregistrer des scripts avec Pig en tant que **myfuncs** :

* **Pour utiliser Jython** : `register '/path/to/pig_python.py' using jython as myfuncs;`
* **Pour utiliser C Python** : `register '/path/to/pig_python.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> Lorsque vous utilisez Jython, le chemin d’accès au fichier pig_jython peut être un chemin d’accès local ou un chemin d’accès WASB://. Toutefois, lorsque vous utilisez C Python, vous devez référencer un fichier sur le système de fichiers local du nœud que vous utilisez pour envoyer le travail Pig.

Une fois l’inscription effectuée, le langage Pig Latin pour cet exemple est le même pour les deux :

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Cet exemple effectue ce qui suit :

1. La première ligne charge l’exemple de fichier de données **sample.log** dans **LOGS**. Elle définit également chaque enregistrement sous la forme d’un tableau de caractères (**chararray**).
2. La ligne suivante élimine par filtrage les valeurs null et stocke le résultat de l'opération dans **LOG**.
3. La ligne suivante itère sur les enregistrements dans **LOG** et utilise **GENERATE** pour appeler la méthode **create_structure** contenue dans le script Python/Jython chargé en tant que **myfuncs**.  **LINE** est utilisé pour transmettre l'enregistrement actuel à la fonction.
4. Enfin, les sorties sont vidées dans STDOUT avec la commande **DUMP** . Cette opération affiche les résultats une fois l’opération terminée.

Les fichiers de script Python C Python et Jython sont similaires. La seule différence réside dans le fait qu’avec C Python, vous devez procéder à une importation à partir de **pig\_util**. Voici le script **pig\_python.py** :

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema

@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

> [!NOTE]
> Vous n’avez pas à vous soucier de l’installation de « pig_util » ; il est automatiquement disponible pour le script.

Rappelez-vous que nous avons précédemment défini l’entrée **LINE** comme un tableau de caractères, car il n’existait pas de schéma cohérent pour l’entrée. Le script Python transforme les données en un schéma cohérent pour la sortie.

1. L’instruction **@outputSchema** définit le format des données qui sont renvoyées à Pig. Dans le cas présent, il s'agit d'un **data bag**, qui est un type de données Pig. Ce conteneur contient les champs suivants, qui sont tous des tableaux de caractères (chaînes) :

   * date : date de création de l'entrée du journal
   * time : heure de création de l'entrée du journal
   * classname : nom de la classe pour laquelle l'entrée a été créée
   * level : niveau du journal
   * detail : détails pour l'entrée du journal

2. Ensuite, **def create_structure(input)** définit la fonction à laquelle Pig transmet les lignes.

3. L'exemple de données **sample.log**respecte pour l'essentiel le schéma basé sur les champs date, time, classname, level et detail que nous voulons renvoyer. Mais il contient également quelques lignes commençant par la chaîne*java.lang.Exception*qui doivent être modifiées pour correspondre au schéma. L'instruction **if** recherche ces dernières, puis manipule les données d'entrée pour déplacer la chaîne*java.lang.Exception*à la fin, harmonisant les données avec notre schéma de sortie prévu.

4. La commande **split** est ensuite utilisée pour fractionner les données aux quatre premiers caractères d'espacement. La sortie correspond à l’affectation de valeurs aux champs **date**, **time**, **classname**, **level** et **detail**.

5. Enfin, les valeurs sont renvoyées à Pig.

Lorsque les données sont renvoyées à Pig, elles utilisent un schéma cohérent comme défini dans l’instruction **@outputSchema**.

## <a name="running"></a>Exécution des exemples
Si vous utilisez un cluster HDInsight Linux, suivez les étapes **SSH**. Si vous utilisez un cluster HDInsight Windows et un client Windows, suivez les étapes **PowerShell** .

### <a name="ssh"></a>SSH

Pour plus d’informations sur l’utilisation de SSH, voir [Utilisation de SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

1. À l’aide des exemples Python [streaming.py](#streamingpy) et [pig_python.py](#jythonpy), créez des copies locales des fichiers sur votre ordinateur de développement.

2. Utilisez `scp` pour copier les fichiers sur votre cluster HDInsight. Par exemple, la commande ci-après copie les fichiers dans un cluster nommé **mycluster**.

        scp streaming.py pig_python.py myuser@mycluster-ssh.azurehdinsight.net:

3. Utilisez SSH pour la connexion au cluster. Par exemple, la commande suivante permet de se connecter à un cluster nommé **mycluster** en tant qu’utilisateur **myuser**.

        ssh myuser@mycluster-ssh.azurehdinsight.net
4. À partir de la session SSH, ajoutez les fichiers python téléchargés précédemment sur le stockage WASB pour le cluster.

        hdfs dfs -put streaming.py /streaming.py
        hdfs dfs -put pig_python.py /pig_python.py

Après avoir téléchargé les fichiers, procédez comme suit pour exécuter les tâches Hive et Pig.

#### <a name="hive"></a>Hive

1. Utilisez la commande `hive` pour lancer l’interpréteur de commandes Hive. Vous devez voir une invite `hive>` une fois l’interpréteur de commandes chargé.
2. À l’invite `hive>` , entrez les informations suivantes :

   ```hive
   add file wasbs:///streaming.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python streaming.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```
3. Après avoir entré la dernière ligne, la tâche doit démarrer. Une fois le travail terminé, il renvoie une sortie comparable à l’exemple de sortie suivant :

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig"></a>Pig

1. Utilisez la commande `pig` pour lancer l’interpréteur de commandes. Vous devez voir une invite `grunt>` une fois l’interpréteur de commandes chargé.

2. Entrez les instructions ci-après au niveau de l’invite `grunt>` :

   ```pig
   Register wasbs:///pig_python.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Après l’entrée de la ligne suivante, le travail doit démarrer. Une fois le travail terminé, il renvoie une sortie comparable à celle ci-dessous.

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Utilisez `quit` pour quitter l’interpréteur de commandes Grunt, puis utilisez les éléments suivants pour modifier le fichier pig_python.py sur le système de fichiers local :

    nano pig_python.py

5. Une fois dans l’éditeur, annulez les marques de commentaire dans la ligne suivante en supprimant le caractère `#` initial :

        #from pig_util import outputSchema

    Une fois que la modification a été apportée, utilisez Ctrl+X pour quitter l’éditeur. Sélectionnez Y, puis Entrée pour enregistrer les modifications.

6. Utilisez la commande `pig` pour relancer l’interpréteur de commandes. Une fois dans l’invite de commandes `grunt>` , utilisez les commandes suivantes pour exécuter le script Python à l’aide de l’interpréteur C Python.

   ```pig
   Register 'pig_python.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Une fois ce travail terminé, le résultat devrait être le même que lorsque vous avez exécuté le script à l’aide de Jython.

### <a name="powershell"></a>PowerShell

Ces étapes utilisent Azure PowerShell. Pour plus d’informations sur l’installation d’Azure PowerShell, consultez l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

1. À l’aide des exemples Python [streaming.py](#streamingpy) et [pig_python.py](#jythonpy), créez des copies locales des fichiers sur votre ordinateur de développement.
2. Utilisez le script PowerShell suivant pour télécharger les fichiers **streaming.py** et **pig\_python.py** sur le serveur. Remplacez le nom par celui de votre cluster Azure HDInsight et le chemin par celui de vos fichiers **streaming.py** et **pig\_python.py** sur les trois premières lignes du script.

   ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $pathToStreamingFile = "C:\path\to\streaming.py"
    $pathToJythonFile = "C:\path\to\pig_python.py"

    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
    -ResourceGroupName $resourceGroup)[0].Value

    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey

    Set-AzureStorageBlobContent `
        -File $pathToStreamingFile `
        -Blob "streaming.py" `
        -Container $container `
        -Context $context

    Set-AzureStorageBlobContent `
        -File $pathToJythonFile `
        -Blob "pig_python.py" `
        -Container $container `
        -Context $context
   ```

    Ce script récupère des informations concernant votre cluster HDInsight, puis extrait le compte et la clé pour le compte de stockage par défaut et télécharge les fichiers vers la racine du conteneur.

   > [!NOTE]
   > D’autres méthodes de téléchargement des scripts sont décrites dans le document [Téléchargement de données pour des tâches Hadoop dans HDInsight](hdinsight-upload-data.md) .

Après avoir téléchargé les fichiers, utilisez les scripts PowerShell suivant pour démarrer les tâches. Lorsque la tâche s'achève, le résultat est normalement écrit sur la console PowerShell.

#### <a name="hive"></a>Hive

Le script ci-après exécute le script **streaming.py**. Avant de s’exécuter, il vous invite à fournir les informations de compte HTTPs/Admin pour votre cluster HDInsight.

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

# If using a Windows-based HDInsight cluster, change the USING statement to:
# "USING 'D:\Python27\python.exe streaming.py' AS " +
$HiveQuery = "add file wasbs:///streaming.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python streaming.py' AS " +
                "(clientid string, phoneLabel string, phoneHash string) " +
                "FROM hivesampletable " +
                "ORDER BY clientid LIMIT 50;"

$jobDefinition = New-AzureRmHDInsightHiveJobDefinition `
    -Query $HiveQuery

$job = Start-AzureRmHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds
Write-Host "Wait for the Hive job to complete ..." -ForegroundColor Green
Wait-AzureRmHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds
# Uncomment the following to see stderr output
# Get-AzureRmHDInsightJobOutput `
#   -Clustername $clusterName `
#   -JobId $job.JobId `
#   -HttpCredential $creds `
#   -DisplayOutputType StandardError
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

La sortie du travail **Hive** doit ressembler à l’exemple de sortie suivant :

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>Pig (Jython)

Le script ci-après utilise le script **pig_python.py** à l’aide de l’interpréteur Jython. Avant de s’exécuter, il vous invite à fournir les informations HTTPs/Admin pour le cluster HDInsight.

> [!NOTE]
> Lors de l’envoi à distance d’un travail à l’aide de PowerShell, il n’est pas possible d’utiliser C Python en tant qu’interpréteur .

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

$PigQuery = "Register wasbs:///pig_python.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
            "LOG = FILTER LOGS by LINE is not null;" +
            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
            "DUMP DETAILS;"

$jobDefinition = New-AzureRmHDInsightPigJobDefinition -Query $PigQuery

$job = Start-AzureRmHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
Wait-AzureRmHDInsightJob `
    -Job $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds
# Uncomment the following to see stderr output
# Get-AzureRmHDInsightJobOutput `
#    -Clustername $clusterName `
#    -JobId $job.JobId `
#    -HttpCredential $creds `
#    -DisplayOutputType StandardError
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

Le résultat de la tâche **Pig** doit ressembler à ceci :

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="errors-when-running-jobs"></a>Erreurs lors de l'exécution des travaux

Lorsque vous exécutez la tâche hive, vous pouvez rencontrer une erreur similaire à ce qui suit :

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Ce problème peut être causé par les fins de ligne dans le fichier streaming.py. De nombreux éditeurs Windows utilisent par défaut CRLF comme fin de ligne, mais les applications Linux utilisent généralement LF.

Pour supprimer les caractères de retour chariot (CR) avant de charger le fichier dans HDInsight, vous pouvez utiliser les instructions PowerShell suivantes :

```powershell
$original_file ='c:\path\to\streaming.py'
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

### <a name="powershell-scripts"></a>Scripts PowerShell

Les deux exemples de script PowerShell utilisés pour l’exécution des exemples contiennent une ligne mise en commentaire qui affiche la sortie d’erreur pour le travail. Si la tâche ne donne pas le résultat prévu, supprimez les marques de commentaire sur la ligne suivante pour voir si les informations sur l'erreur signalent un problème.

```powershell
# Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Les informations sur l’erreur (STDERR) et le résultat du travail (STDOUT) sont également enregistrés dans votre stockage HDInsight.

| Pour cette tâche... | Examinez ces fichiers dans le conteneur d'objets blob |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>Étapes suivantes

Si vous devez charger des modules Python qui ne sont pas fournis par défaut, consultez l’article [How to deploy a Python module to Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) (Déploiement d’un module Python vers Azure HDInsight).

Pour connaître d’autres façons d’utiliser Pig et Hive et pour en savoir plus sur l’utilisation de MapReduce, consultez les documents suivants :

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

