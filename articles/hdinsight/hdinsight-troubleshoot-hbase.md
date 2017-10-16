---
title: "Résolution de problèmes HBase à l’aide d’Azure HDInsight | Microsoft Docs"
description: "Obtenez les réponses aux questions courantes sur l’utilisation de HBase et d’Azure HDInsight."
services: hdinsight
documentationcenter: 
author: nitinver
manager: ashitg
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 7/7/2017
ms.author: nitinver
ms.openlocfilehash: 15412c3853a2b8436c5e96034c9a92a2a1094662
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-hbase-by-using-azure-hdinsight"></a>Résolution de problèmes HBase à l’aide d’Azure HDInsight

Découvrez les principaux problèmes rencontrés lors de l’utilisation de charges utiles Apache HBase dans Apache Ambari, et leur résolution.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Comment exécuter des rapports de commande hbck avec plusieurs régions non attribuées

Un message d’erreur courant indiquant que plusieurs régions ne sont pas attribuées ou qu’il y a des trous dans la chaîne de régions peut s’afficher lorsque vous exécutez la commande `hbase hbck`.

Dans l’interface utilisateur HBase Master, vous pouvez voir le nombre de régions en état de déséquilibre sur tous les serveurs régionaux. Vous pouvez ensuite exécuter la commande `hbase hbck` pour afficher les trous dans la chaîne de régions.

Les trous peuvent être dus à des régions hors connexion, donc commencez par corriger les attributions. 

Pour rétablir les régions non attribuées à leur état normal, suivez les étapes ci-dessous :

1. Connectez-vous au cluster HDInsight HBase à l’aide de SSH.
2. Pour vous connecter avec l’interpréteur de commandes ZooKeeper, exécutez la commande `hbase zkcli`.
3. Exécutez la commande `rmr /hbase/regions-in-transition` ou la commande `rmr /hbase-unsecure/regions-in-transition`.
4. Pour quitter l’interpréteur de commandes `hbase zkcli`, utilisez la commande `exit`.
5. Ouvrez l’interface utilisateur d’Apache Ambari et redémarrez le service HBase Master actif.
6. Exécutez de nouveau la commande `hbase hbck` (sans aucune option). Vérifiez la sortie de cette commande et assurez-vous que toutes les régions sont assignées.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Comment corriger les problèmes de délai d’expiration lors de l’utilisation des commandes hbck pour l’affectation des régions ?

### <a name="issue"></a>Problème

Les problèmes de délai d’expiration rencontrés lors de l’utilisation de la commande `hbck` peuvent être dus à la présence de plusieurs régions en état de transition pendant un certain temps. Ces régions apparaissent comme étant hors connexion dans l’interface utilisateur HBase Master. En raison du grand nombre de régions en tentative de transition, HBase Master pourrait connaître un problème de délai d’expiration et ne pas parvenir remettre ces régions en ligne.

### <a name="resolution-steps"></a>Étapes de résolution

1. Connectez-vous au cluster HDInsight HBase à l’aide de SSH.
2. Pour vous connecter avec l’interpréteur de commandes ZooKeeper, exécutez la commande `hbase zkcli`.
3. Exécutez la commande `rmr /hbase/regions-in-transition` ou `rmr /hbase-unsecure/regions-in-transition`.
4. Pour quitter l’interpréteur de commandes `hbase zkcli`, utilisez la commande `exit`.
5. Ouvrez l’interface utilisateur d’Ambari et redémarrez le service HBase Master actif.
6. Exécutez de nouveau la commande `hbase hbck -fixAssignments`.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>Comment forcer la désactivation du mode sans échec du stockage HDFS dans un cluster ?

### <a name="issue"></a>Problème

Le stockage HDFS (Hadoop Distributed File System) est bloqué en mode sans échec sur le cluster HDInsight.

### <a name="detailed-description"></a>Description détaillée

Cette erreur peut résulter d’un échec de l’exécution de la commande HDFS suivante :

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

L’erreur qui peut s’afficher lorsque vous tentez d’exécuter la commande ressemble à ce qui suit :

```apache
hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.mkdirs(FSNamesystem.java:4010)
        at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.mkdirs(NameNodeRpcServer.java:1102)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.mkdirs(ClientNamenodeProtocolServerSideTranslatorPB.java:630)
        at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:640)
        at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:982)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2313)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2309)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:422)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1724)
        at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2307)
        at org.apache.hadoop.ipc.Client.getRpcResponse(Client.java:1552)
        at org.apache.hadoop.ipc.Client.call(Client.java:1496)
        at org.apache.hadoop.ipc.Client.call(Client.java:1396)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:233)
        at com.sun.proxy.$Proxy10.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.mkdirs(ClientNamenodeProtocolTranslatorPB.java:603)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:278)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:194)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:176)
        at com.sun.proxy.$Proxy11.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.DFSClient.primitiveMkdir(DFSClient.java:3061)
        at org.apache.hadoop.hdfs.DFSClient.mkdirs(DFSClient.java:3031)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1162)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1158)
        at org.apache.hadoop.fs.FileSystemLinkResolver.resolve(FileSystemLinkResolver.java:81)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirsInternal(DistributedFileSystem.java:1158)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirs(DistributedFileSystem.java:1150)
        at org.apache.hadoop.fs.FileSystem.mkdirs(FileSystem.java:1898)
        at org.apache.hadoop.fs.shell.Mkdir.processNonexistentPath(Mkdir.java:76)
        at org.apache.hadoop.fs.shell.Command.processArgument(Command.java:273)
        at org.apache.hadoop.fs.shell.Command.processArguments(Command.java:255)
        at org.apache.hadoop.fs.shell.FsCommand.processRawArguments(FsCommand.java:119)
        at org.apache.hadoop.fs.shell.Command.run(Command.java:165)
        at org.apache.hadoop.fs.FsShell.run(FsShell.java:297)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:90)
        at org.apache.hadoop.fs.FsShell.main(FsShell.java:350)
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

### <a name="probable-cause"></a>Cause probable

Le cluster HDInsight a été redimensionné et ne compte plus que quelques nœuds. Le nombre de nœuds est inférieur au facteur de réplication HDFS ou proche de ce dernier.

### <a name="resolution-steps"></a>Étapes de résolution 

1. Déterminez l’état du stockage HDFS du cluster HDInsight à l’aide des commandes suivantes :

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   ```

   ```apache
   hdiuser@hn0-spark2:~$ hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   Safe mode is ON
   Configured Capacity: 3372381241344 (3.07 TB)
   Present Capacity: 3138625077248 (2.85 TB)
   DFS Remaining: 3102710317056 (2.82 TB)
   DFS Used: 35914760192 (33.45 GB)
   DFS Used%: 1.14%
   Under replicated blocks: 0
   Blocks with corrupt replicas: 0
   Missing blocks: 0
   Missing blocks (with replication factor 1): 0

   -------------------------------------------------
   Live datanodes (8):

   Name: 10.0.0.17:30010 (10.0.0.17)
   Hostname: 10.0.0.17
   Decommission Status : Normal
   Configured Capacity: 421547655168 (392.60 GB)
   DFS Used: 5288128512 (4.92 GB)
   Non DFS Used: 29087272960 (27.09 GB)
   DFS Remaining: 387172253696 (360.58 GB)
   DFS Used%: 1.25%
   DFS Remaining%: 91.85%
   Configured Cache Capacity: 0 (0 B)
   Cache Used: 0 (0 B)
   Cache Remaining: 0 (0 B)
   Cache Used%: 100.00%
   Cache Remaining%: 0.00%
   Xceivers: 2
   Last contact: Wed Apr 05 16:22:00 UTC 2017
   ...

   ```
2. Vous pouvez également vérifier l’intégrité du stockage HDFS du cluster HDInsight à l’aide des commandes suivantes :

   ```apache
   hdiuser@hn0-spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
   ```

   ```apache
   Connecting to namenode via http://hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net:30070/fsck?ugi=hdiuser&path=%2F
   FSCK started by hdiuser (auth:SIMPLE) from /10.0.0.22 for path / at Wed Apr 05 16:40:28 UTC 2017
   ....................................................................................................

   ....................................................................................................
   ..................Status: HEALTHY
   Total size:    9330539472 B
   Total dirs:    37
   Total files:   2618
   Total symlinks:                0 (Files currently being written: 2)
   Total blocks (validated):      2535 (avg. block size 3680686 B)
   Minimally replicated blocks:   2535 (100.0 %)
   Over-replicated blocks:        0 (0.0 %)
   Under-replicated blocks:       0 (0.0 %)
   Mis-replicated blocks:         0 (0.0 %)
   Default replication factor:    3
   Average block replication:     3.0
   Corrupt blocks:                0
   Missing replicas:              0 (0.0 %)
   Number of data-nodes:          8
   Number of racks:               1
   FSCK ended at Wed Apr 05 16:40:28 UTC 2017 in 187 milliseconds

   The filesystem under path '/' is HEALTHY
   ```

3. Si aucun bloc n’est manquant, endommagé ou sous-répliqué ou si ces blocs peuvent être ignorés, exécutez la commande suivante pour désactiver le mode sans échec sur le nœud de nom :

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Comment résoudre les problèmes de connectivité JDBC ou SQLLine avec Apache Phoenix ?

### <a name="resolution-steps"></a>Étapes de résolution

Pour vous connecter avec Phoenix, vous devez fournir l’adresse IP d’un nœud Zookeeper actif. Assurez-vous que le service Zookeeper auquel l’utilitaire sqlline.py essaie de se connecter est en cours d’exécution.
1. Connectez-vous au cluster HDInsight à l’aide de SSH.
2. Entrez la commande suivante :
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > Vous pouvez obtenir l’adresse IP du nœud ZooKeeper actif à partir de l’interface utilisateur d’Ambari. Accédez à **HBase** > **Quick Links** > **ZK\* (Active)** > **Zookeeper Info** (HBase > Liens rapides > ZK (acitf) > Infos ZooKeeper). 

3. Si l’utilitaire sqlline.py se connecte à Phoenix et n’expire pas, exécutez la commande suivante pour valider la disponibilité et l’intégrité de Phoenix :

   ```apache
           !tables
           !quit
   ```      
4. Si la commande fonctionne, il n’existe aucun problème. L’adresse IP fournie par l’utilisateur peut être incorrecte. Toutefois, si la commande s’interrompt pendant une durée prolongée, puis affiche l’erreur suivante, passez à l’étape 5.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. Exécutez les commandes suivantes à partir du nœud principal (hn0) pour diagnostiquer l’état de la table Phoenix SYSTEM.CATALOG :

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   La commande doit renvoyer une erreur similaire à la suivante : 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. À partir de l’interface utilisateur Ambari, suivez les étapes ci-dessous pour redémarrer le service HMaster sur tous les nœuds ZooKeeper :

    1. Dans la section **Summary** (Résumé) de HBase, accédez à **HBase** > **Active HBase Master** (HBase > HBase Master actif). 
    2. Dans la section **Components** (Composants), redémarrez le service HBase Master.
    3. Répétez ces étapes pour les services **Standby HBase Master** (Master HBase de secours) restants. 

La stabilisation et la récupération complète du service HBase Master peuvent prendre jusqu’à cinq minutes. Après quelques minutes, répétez les commandes sqlline.py pour confirmer que la table SYSTEM.CATALOG est activée et qu’elle peut être interrogée. 

Une fois la table SYSTEM.CATALOG revenue à son fonctionnement normal, le problème de connectivité à Phoenix devrait se résoudre automatiquement.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>Pourquoi le démarrage d’un serveur maître échoue-t-il ?

### <a name="error"></a>Error 

Le renommage atomique échoue.

### <a name="detailed-description"></a>Description détaillée

Pendant le processus de démarrage, HMaster exécute de nombreuses étapes d’initialisation. Cela inclut le déplacement des données à partir du dossier de travail (.tmp) vers le dossier de données. HMaster examine également le dossier de journaux WAL (write-ahead log) pour voir s’il existe des serveurs régionaux ne répondant pas, etc. 

Lors du démarrage, HMaster exécute une commande `list` de base sur ces dossiers. S’il rencontre un fichier inattendu dans l’un de ces dossiers, il envoie une exception et ne démarre pas.  

### <a name="probable-cause"></a>Cause probable

Dans les journaux des serveurs régionaux, essayez d’identifier la chronologie de création des fichiers et de déterminer si un blocage de processus s’est produit autour de l’heure de création du fichier. (Contactez le support HBase si vous avez besoin d’aide.) Cela nous permet de vous fournir des mécanismes plus robustes afin de vous éviter ce bogue et de garantir un arrêt approprié des processus.

### <a name="resolution-steps"></a>Étapes de résolution

Vérifiez la pile des appels et essayez de déterminer quel dossier pourrait être responsable du problème (par exemple le dossier de journaux WAL ou le dossier .tmp). Ensuite, dans Cloud Explorer ou à l’aide de commandes HDFS, tentez de localiser le fichier posant problème. En règle générale, il s’agit d’un fichier \*-renamePending.json. (Le fichier \*-renamePending.json est un fichier journal utilisé pour implémenter l’opération de renommage atomique au niveau du pilote WASB. En raison des bogues de cette implémentation, ces fichiers peuvent rester après le blocage du processus, etc.) Forcez la suppression de ce fichier soit dans Cloud Explorer, soit à l’aide de commandes HDFS. 

Dans certains cas, cet emplacement contient également un fichier temporaire nommé *$$$. $$$*. Vous devez utiliser la commande HDFS `ls` pour voir ce fichier ; vous ne pouvez pas le voir dans Cloud Explorer. Pour supprimer ce fichier, utilisez la commande HDFS `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`.  

Après avoir exécuté ces commandes, HMaster devrait démarrer immédiatement. 

### <a name="error"></a>Error

Aucune adresse de serveur n’est répertoriée dans la table *hbase: meta* au niveau de la région xxx.

### <a name="detailed-description"></a>Description détaillée

Un message indiquant que la table *hbase: meta* n’est pas en ligne peut s’afficher sur votre cluster Linux. L’exécution de la commande `hbck` peut renvoyer l’erreur suivante : « hbase: meta table replicaId 0 is not found on any region » (replicaId 0 introuvable dans toutes les régions de la table hbase: meta). Le problème peut provenir de l’échec de l’initialisation de HMaster après le redémarrage de HBase. Dans les journaux HMaster, le message « No server address listed in hbase: meta for region hbase: backup \<region name\> » (Aucune adresse de serveur n’est répertoriée dans la table hbase: meta au niveau de la région hbase: backup xxx) s’affiche parfois.  

### <a name="resolution-steps"></a>Étapes de résolution

1. Entrez la commande suivante dans l’interpréteur de commandes HBase (modifiez les valeurs le cas échéant) :  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. Supprimez l’entrée *hbase: namespace*. Cette entrée peut être à l’origine de l’erreur signalée lorsque la table *hbase: namespace* est analysée.

3. Pour rétablir le fonctionnement de HBase, redémarrez le service HMaster actif à partir de l’interface utilisateur Ambari.  

4. Dans l’interpréteur de commandes HBase, exécutez la commande suivante pour afficher toutes les tables hors connexion :

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>Documentation supplémentaire

[Unable to process the HBase table](http://stackoverflow.com/questions/4794092/unable-to-access-hbase-table) (Traitement de la table HBase impossible, en anglais)


### <a name="error"></a>Error

HMaster arrive à expiration avec une exception irrécupérable du type « java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned » (Expiration du délai de 300000ms en attente de l’affectation de la table namespace).

### <a name="detailed-description"></a>Description détaillée

Vous pourriez rencontrer ce problème si plusieurs tables et régions n’ont pas été vidées lors du redémarrage de vos services HMaster. Le redémarrage risque d’échouer, et vous le message d’erreur précédent s’affiche.  

### <a name="probable-cause"></a>Cause probable

Il s’agit d’un problème connu avec le service HMaster. Les tâches générales de démarrage du cluster peuvent prendre beaucoup de temps. HMaster s’arrête, car la table namespace n’a pas encore été affectée. Cela se produit uniquement lorsqu’une grande quantité de données n’a pas été vidée, et qu’un délai d’expiration de cinq minutes est insuffisant.
  
### <a name="resolution-steps"></a>Étapes de résolution

1. Dans l’interface utilisateur d’Ambari, accédez à **HBase** > **Configs**. Dans le fichier personnalisé hbase-site.XML, ajoutez le paramètre suivant : 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. Redémarrez les services requis (HMaster et éventuellement d’autres services HBase).  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Pourquoi le redémarrage échoue-t-il sur un serveur régional ?

### <a name="issue"></a>Problème

L’échec du redémarrage d’un serveur régional peut être évité en respectant ces meilleures pratiques. Il est recommandé d’interrompre les activités impliquant de lourdes charges de travail lorsque vous prévoyez de redémarrer les serveurs régionaux HBase. Si une application continue de se connecter aux serveurs régionaux pendant l’arrêt, cela ralentit le redémarrage de ces derniers de plusieurs minutes. En outre, il est judicieux de commencer par vider toutes les tables. Pour en savoir plus sur le vidage des tables, consultez ce billet de blog (en anglais) : [HDInsight HBase: How to Improve HBase cluster restart time by Flushing tables](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/) (HDInsight HBase : comment améliorer le temps de redémarrage des clusters HBase en vidant les tables).

Si vous lancez l’opération de redémarrage sur des serveurs régionaux HBase à partir de l’interface utilisateur d’Ambari, vous voyez immédiatement que les serveurs régionaux s’arrêtent, mais qu’ils ne redémarrent pas immédiatement. 

Voici ce qui se passe en arrière-plan : 

1. Ambari Agent envoie une demande d’arrêt au serveur régional.
2. L’agent attend 30 secondes que le serveur s’arrête de manière appropriée. 
3. Si votre application continue à se connecter au serveur régional, ce dernier ne sera pas arrêté immédiatement. Le délai de 30 secondes expire avant l’arrêt. 
4. Après 30 secondes, Ambari Agent force l’arrêt du serveur régional en lui envoyant une commande force-kill (`kill -9`). Vous pouvez voir cet événement dans le journal ambari-agent (dans le répertoire /var/log/ du nœud de travail correspondant) :

   ```apache
           2017-03-21 13:22:09,171 - Execute['/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/current/hbase-regionserver/conf stop regionserver'] {'only_if': 'ambari-sudo.sh  -H -E t
           est -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1', 'on_timeout': '! ( ambari-sudo.sh  -H -E test -
           f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H 
           -E cat /var/run/hbase/hbase-hbase-regionserver.pid`', 'timeout': 30, 'user': 'hbase'}
           2017-03-21 13:22:40,268 - Executing '! ( ambari-sudo.sh  -H -E test -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >
           /dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid`'. Reason: Execution of 'ambari-sudo.sh su hbase -l -s /bin/bash -c 'export  
           PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/var/lib/ambari-agent ; /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/curre
           nt/hbase-regionserver/conf stop regionserver was killed due timeout after 30 seconds
           2017-03-21 13:22:40,285 - File['/var/run/hbase/hbase-hbase-regionserver.pid'] {'action': ['delete']}
           2017-03-21 13:22:40,285 - Deleting File['/var/run/hbase/hbase-hbase-regionserver.pid']
   ```
En raison de cet arrêt brutal, le port associé au processus ne peut pas être libéré même si le processus du serveur régional est arrêté. Cela peut entraîner l’exception AddressBindException au démarrage du serveur régional, comme indiqué dans les journaux suivants. Cet élément apparaît dans le journal region-server.log du répertoire /var/log/hbase des nœuds de travail où le démarrage des serveurs régionaux échoue. 

   ```apache

   2017-03-21 13:25:47,061 ERROR [main] regionserver.HRegionServerCommandLine: Region server exiting
   java.lang.RuntimeException: Failed construction of Regionserver: class org.apache.hadoop.hbase.regionserver.HRegionServer
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2636)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.start(HRegionServerCommandLine.java:64)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.run(HRegionServerCommandLine.java:87)
   at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
   at org.apache.hadoop.hbase.util.ServerCommandLine.doMain(ServerCommandLine.java:126)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.main(HRegionServer.java:2651)
        
   Caused by: java.lang.reflect.InvocationTargetException
   at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
   at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)
   at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
   at java.lang.reflect.Constructor.newInstance(Constructor.java:526)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2634)
   ... 5 more
        
   Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2497)
   at org.apache.hadoop.hbase.ipc.RpcServer$Listener.<init>(RpcServer.java:580)
   at org.apache.hadoop.hbase.ipc.RpcServer.<init>(RpcServer.java:1982)
   at org.apache.hadoop.hbase.regionserver.RSRpcServices.<init>(RSRpcServices.java:863)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.createRpcServices(HRegionServer.java:632)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.<init>(HRegionServer.java:532)
   ... 10 more
        
   Caused by: java.net.BindException: Address already in use
   at sun.nio.ch.Net.bind0(Native Method)
   at sun.nio.ch.Net.bind(Net.java:463)
   at sun.nio.ch.Net.bind(Net.java:455)
   at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
   at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2495)
   ... 15 more
   ```

### <a name="resolution-steps"></a>Étapes de résolution

1. Essayez de réduire la charge sur les serveurs régionaux HBase avant de lancer un redémarrage. 
2. Si l’étape 1 n’a aucun effet, vous pouvez également essayer de redémarrer manuellement les serveurs régionaux sur les nœuds de travail à l’aide des commandes suivantes :

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

