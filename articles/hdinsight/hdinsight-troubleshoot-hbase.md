---
title: "Résolution de problèmes HBase - Azure HDInsight | Microsoft Docs"
description: "Résolution de la cause des trous dans la chaîne de régions."
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
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 29b1776d6b0c456515738aae3c5fd836c9a0295d
ms.contentlocale: fr-fr
ms.lasthandoff: 07/11/2017

---

# <a name="hbase-troubleshooting"></a>Résolution de problèmes HBASE

Cet article décrit les principaux problèmes rencontrés lors de l’utilisation de charges utiles HBASE dans Apache Ambari, et leur résolution.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Comment exécuter des rapports de commande hbck avec plusieurs régions non attribuées

Il est courant que plusieurs régions ne soient pas attribuées ou qu’il y ait des trous dans la chaîne de régions lorsqu’un utilisateur HBase exécute la commande 'hbase hbck'.

L’utilisateur constate un déséquilibre dans le nombre de régions non attribuées sur tous les serveurs régionaux dans l’interface utilisateur HBase Master. Il exécute alors la commande 'hbase hbck' et remarque des trous dans la chaîne de régions.

L’utilisateur doit tout d’abord résoudre les problèmes d’affectation. En effet, les trous peuvent être dus à des régions hors connexion. 

Suivez les étapes ci-dessous pour rétablir les régions non attribuées à leur état normal :

1. Connectez-vous au cluster HBase HDInsight à l’aide de SSH.
1. Exécutez la commande 'hbase zkcli' pour vous connecter à l’interpréteur de commandes Zookeeper.
1. Exécutez la commande 'rmr /hbase/regions-in-transition' ou 'rmr /hbase-unsecure/regions-in-transition'.
1. Quittez le shell 'hbase zkcli' à l’aide de la commande 'exit'.
1. Ouvrez l’interface utilisateur d’Ambari et redémarrez le service HBase Master actif à partir d’Ambari.
1. Exécutez de nouveau la commande 'hbase hbck' (sans autre option).

Vérifiez la sortie de commande de l’étape 6 et assurez-vous que toutes les régions sont assignées.

---

## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Comment corriger les problèmes de délai d’expiration avec les commandes hbck pour l’affectation des régions ?

### <a name="probable-cause"></a>Cause probable

Le problème vient potentiellement de la présence de plusieurs régions restées à l’état de « transition » pendant une période prolongée. Ces régions apparaissent hors connexion dans l’interface utilisateur HBase Master. En raison du grand nombre de régions en tentative de transition, HBase Master pourrait connaître un problème de délai d’expiration et ne pas parvenir remettre ces régions en ligne.

### <a name="resolution-steps"></a>Étapes de résolution

Voici les étapes permettant de résoudre le problème de délai d’expiration de la commande hbck :

1. Connectez-vous au cluster HBase HDInsight à l’aide de SSH.
1. Exécutez la commande 'hbase zkcli' pour vous connecter à l’interpréteur de commandes Zookeeper.
1. Exécutez la commande 'rmr /hbase/regions-in-transition' ou 'rmr /hbase-unsecure/regions-in-transition'.
1. Quittez le shell 'hbase zkcli' à l’aide de la commande 'exit'.
1. Ouvrez l’interface utilisateur d’Ambari et redémarrez le service HBase Master actif à partir d’Ambari.
1. Exécutez de nouveau la commande 'hbck hbase - fixAssignments' et le problème de délai d’expiration ne devrait plus se reproduire.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-an-cluster"></a>Comment forcer la désactivation du mode sans échec du stockage HDFS dans un cluster ?

### <a name="issue"></a>Problème :

Le stockage HDFS local est bloqué en mode sans échec sur le cluster HDInsight.   

### <a name="detailed-description"></a>Description détaillée :

Échec d’exécution d’une simple commande HDFS, comme suit :

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

L’erreur s’est produite lors de la tentative d’exécution de la commande ci-dessus, comme suit :

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

### <a name="probable-cause"></a>Cause probable :

Le cluster HDInsight a été redimensionné avec très peu de nœuds, avec un nombre en deçà ou proche du facteur de réplication HDFS.

### <a name="resolution-steps"></a>Étapes de résolution : 

- Créez un rapport sur l’état du stockage HDFS du cluster HDInsight à l’aide des commandes suivantes :

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
- Vous pouvez également vérifier l’intégrité du stockage HDFS du cluster HDInsight à l’aide des commandes suivantes :

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

- Si aucun bloc n’est manquant, endommagé ou sous-répliqué ou si ces blocs peuvent être ignorés, exécutez la commande suivante pour désactiver le mode sans échec sur le nœud de nom :

```apache
hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Comment résoudre les problèmes de connectivité JDBC ou sqlline avec Apache Phoenix

### <a name="resolution-steps"></a>Étapes de résolution :

Pour vous connecter avec Apache Phoenix, vous devez fournir l’adresse IP du nœud Zookeeper actif. Assurez-vous que le service Zookeeper auquel l’utilitaire sqlline .py essaie de se connecter est en cours d’exécution.
1. Effectuez la connexion SSH au cluster HDInsight.
1. Essayez la commande suivante :
        
```apache
        "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
```     
    Note: The IP of Active Zooker node can be identified from Ambari UI, by following the links to HBase -> "Quick Links" -> "ZK* (Active)" -> "Zookeeper Info". 

Si l’utilitaire sqlline.py se connecte à Apache Phoenix et n’expire pas, exécutez la commande suivante pour valider la disponibilité et l’intégrité d’Apache Phoenix :

```apache
        !tables
        !quit
```      
- Si les commandes ci-dessus fonctionnent, il n’existe aucun problème. L’adresse IP fournie par l’utilisateur est peut-être incorrecte.
   
    Toutefois, si la commande s’interrompt pendant trop longtemps et provoque l’erreur ci-dessous, continuez à suivre ce guide de dépannage :

```apache
        Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
```

- Exécutez les commandes suivantes à partir du nœud principal (hn0) pour diagnostiquer l’état de la table Phoenix SYSTEM.CATALOG :

```apache
        hbase shell
        
        count 'SYSTEM.CATALOG'
```        
- La commande doit renvoyer une erreur similaire à la suivante : 

```apache
        ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
```
- Redémarrez le service HMaster sur tous les nœuds Zookeeper à partir de l’interface utilisateur Ambari en suivant les étapes ci-dessous :

    a. Accédez au lien « HBase -> Active HBase Master » (HBase -> HBase Master actif) dans la section Summary (Résumé) de HBase. 
    a. Dans la section Components (Composants), redémarrez le service HBase Master.
    a. Répétez les étapes ci-dessus pour services « Standby HBase Master » (Master HBase de secours) restants. 

La stabilisation et la récupération complète du service HBase Master peuvent prendre jusqu’à cinq minutes. Après quelques minutes d’attente, répétez les commandes sqlline.py pour confirmer que la table de catalogue système est activée et peut être interrogée. 

Une fois la table SYSTEM.CATALOG revenue à son fonctionnement normal, le problème de connectivité à Apache Phoenix devrait se résoudre automatiquement.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>Pourquoi le démarrage d’un serveur maître échoue-t-il ?

### <a name="error"></a>Error: 

Échec de renommage atomique

### <a name="detailed-description"></a>Description détaillée :

Pendant le processus de démarrage, HMaster effectue de nombreuses étapes d’initialisation, y compris le déplacement des données du dossier de travail (.tmp) vers le dossier de données. Il analyse également le dossier des journaux WAL (Write Ahead Log) pour déterminer s’il existe des serveurs régionaux inactifs, etc. 

Pendant l’ensemble de ces opérations, il exécute une commande 'list' de base sur ces dossiers. S’il rencontre un fichier inattendu dans l’un de ces dossiers, il envoie une exception et le démarrage échoue.  

### <a name="probable-cause"></a>Cause probable :

Essayez d’identifier la chronologie de la création de fichiers et de déterminer si un processus s’est arrêté autour de ce moment-là dans les journaux des serveurs régionaux (contactez un membre de l’équipe Hbase pour obtenir de l’aide). Cela nous permet de fournir des mécanismes plus robustes pour éviter ce bogue et de garantir un arrêt approprié des processus.

### <a name="resolution-steps"></a>Étapes de résolution :

Si vous vous trouvez dans cette situation, essayez de vérifier la pile des appels et de déterminer quel dossier pourrait être responsable du problème (par exemple le dossier de journaux WAL ou le dossier .tmp). Essayez ensuite d’identifier le fichier posant problème via Cloud Explorer ou des commandes hdfs. Il s’agit généralement d’un fichier *-renamePending.json (un fichier journal utilisé pour implémenter l’opération de renommage atomique dans le pilote WASB ; en raison des bogues dans cette implémentation, de tels fichiers peuvent subsister en cas de blocage de processus, etc.). Forcez la suppression du fichier via Cloud Explorer. 

Il arrive parfois également qu’un fichier temporaire de type $$$. $$$ se trouve à cet emplacement, ce qui n’est pas détectable via Cloud Explorer, mais uniquement via la commande hdfs ls. Vous pouvez utiliser la commande hdfs « hdfs dfs -rm /<the path>/\$\$\$.\$\$\$ » pour supprimer ce fichier.  

Une fois cette opération effectuée, HMaster devrait démarrer immédiatement. 

### <a name="error-no-server-address-listed-in"></a>Erreur : aucune adresse de serveur répertoriée 

Aucune adresse de serveur n’est répertoriée dans la table hbase: meta au niveau de la région xxx

### <a name="detailed-description"></a>Description détaillée :

Le client a rencontré une erreur dans son cluster Linux lui indiquant que la table hbase: meta n’était pas en ligne. L’exécution de la commande hbck a renvoyé l’erreur suivante : « hbase: meta table replicaId 0 is not found on any region » (replicaId 0 introuvable dans toutes les régions de la table hbase: meta). Après le redémarrage de HBase, c’est le service hmaster qui n’a pas pu s’initialiser. Dans les journaux hmaster, il est indiqué « No server address listed in hbase: meta for region hbase: backup <region name> » (Aucune adresse de serveur n’est répertoriée dans la table hbase: meta au niveau de la région hbase: backup).  

### <a name="resolution-steps"></a>Étapes de résolution :

- Tapez la commande suivante dans le shell HBase (modifiez les valeurs le cas échéant).  

```apache
> scan 'hbase:meta'  
```

```apache
> delete 'hbase:meta','hbase:backup <region name>','<column name>'  
```

- Supprimez l’entrée hbase: namespace, car la même erreur pourrait se reproduire pendant l’analyse de la table hbase: namespace.

- Redémarrez le service HMaster actif à partir de l’interface utilisateur Ambari pour rétablir le fonctionnement de HBase.  

- Exécutez la commande suivante dans le shell HBase pour afficher toutes les tables hors connexion :

```apache 
hbase hbck -ignorePreCheckPermission -fixAssignments 
```

### <a name="further-reading"></a>Pour aller plus loin :

[Unable to access HBase table](http://stackoverflow.com/questions/4794092/unable-to-access-hbase-table) (Impossible d’accéder à la table HBase, en anglais)


### <a name="error"></a>Error:

HMaster arrive à expiration avec une exception irrécupérable du type java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned (Expiration du délai de 300000ms en attente de l’affectation de la table namespace)

### <a name="detailed-description"></a>Description détaillée :

Le client ayant rencontré ce problème avait apparemment beaucoup de tables et de régions et n’avait pas effectué de vidage au redémarrage de ses services HMaster. Le redémarrage échouait avec le message d’erreur ci-dessus. Aucune autre erreur détectée.  

### <a name="probable-cause"></a>Cause probable :

Il s’agit d’un « défaut » connu du service HMaster. Les tâches de démarrage de cluster générales peuvent prendre du temps et le service HMaster s’arrête parce que la table namespace n’est pas encore attribuée. Cette erreur se produit uniquement dans les scénarios impliquant un grand nombre de données non vidées, lorsqu’un délai d’expiration de cinq minutes n’est pas suffisant.
  
### <a name="resolution-steps"></a>Étapes de résolution :

- Dans l’interface utilisateur Ambari, accédez à HBase -> Configs et ajoutez le paramètre suivant au niveau de custom hbase-site.xml : 

```apache
Key: hbase.master.namespace.init.timeout Value: 2400000  
```

- Redémarrez les services requis (principalement HMaster et éventuellement d’autres services HBase).  



## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Pourquoi le redémarrage échoue-t-il sur un serveur régional ?

### <a name="probable-cause"></a>Cause probable :

Tout d’abord, une telle situation peut être évitée en suivant les meilleures pratiques. Il est recommandé d’interrompre les activités impliquant de lourdes charges de travail lorsque vous prévoyez de redémarrer les serveurs régionaux HBase. Si l’application continue de se connecter aux serveurs régionaux pendant l’arrêt, cela ralentit le redémarrage de ces derniers de plusieurs minutes. En outre, il est recommandé aux utilisateurs de vider toutes les tables en prenant ce billet de blog (en anglais) comme référence : [HDInsight HBase: How to Improve HBase cluster restart time by Flushing tables](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/) (HDInsight HBase : comment améliorer le temps de redémarrage des clusters HBase en vidant les tables).

Imaginons qu’un utilisateur lance l’opération de redémarrage sur les serveurs régionaux HBase à partir de l’interface utilisateur Ambari. Il verrait immédiatement que les serveurs régionaux sont arrêtés, mais qu’ils mettent trop de temps à revenir en ligne. 

Voici ce qui se passe en arrière-plan : 

1. Ambari Agent envoie une demande d’arrêt au serveur régional.
1. L’agent attend ensuite 30 secondes que le serveur s’arrête de manière appropriée. 
1. Si l’application du client continue à se connecter au serveur de la région, ce dernier ne s’arrêtera pas immédiatement et le délai d’expiration de 30 secondes sera dépassé. 
1. Après l’expiration des 30 secondes, Ambari Agent force l’arrêt du serveur régional (kill -9). Cette opération apparaît dans le journal ambari-agent (dans /var/log/répertoire du nœud de travail concerné), comme illustré ci-dessous :

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
En raison de cet arrêt brutal, le port associé au processus ne peut pas être libéré même si le processus du serveur régional est arrêté de force. Cela peut entraîner l’exception AddressBindException, comme indiqué dans les journaux de démarrage du serveur régional ci-dessous. Cet élément apparaît dans le journal region-server.log dans /var/log/répertoire hbase des nœuds de travail où le redémarrage des serveurs régionaux échoue. 

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

### <a name="resolution-steps"></a>Étapes de résolution :

Dans de tels cas, la solution de contournement ci-dessous peut fonctionner : 

- Essayez de réduire la charge sur les serveurs régionaux HBase avant de lancer un redémarrage. 

- Si l’étape ci-dessus n’a aucun effet, vous pouvez également essayer de redémarrer manuellement les serveurs régionaux sur les nœuds de travail à l’aide des commandes suivantes :

```apache
      sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
      sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
```



