---
title: Effectuer la migration de HDInsight Windows vers HDInsight Linux | Microsoft Docs
description: "Apprenez à effectuer la migration d’un cluster HDInsight Windows vers un cluster HDInsight Linux."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: ff35be59-bae3-42fd-9edc-77f0041bab93
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d5b68d26d708a28edee13ff3d9a57588ce83e12
ms.openlocfilehash: 856d75c58cd911c641ec74b78f5c6133e605b2ec


---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Effectuer la migration d’un cluster HDInsight Windows vers un cluster Linux
Même si HDInsight Windows représente un moyen facile d’utiliser Hadoop dans le cloud, vous découvrirez peut-être que vous avez besoin d’un cluster Linux pour tirer parti des outils et technologies nécessaires pour votre solution. De nombreux éléments de l’écosystème Hadoop sont développés sur des systèmes Linux et certains peuvent ne pas être utilisables avec HDInsight Windows. En outre, un grand nombre de livres, de vidéos et d’autres documents de formation supposent que vous employez un système Linux quand vous utilisez Hadoop.

Ce document fournit des détails sur les différences entre HDInsight sur Windows et Linux ainsi que des conseils sur la migration de charges de travail existantes vers un cluster Linux.

> [!NOTE]
> Les clusters HDInsight utilisent le support à long terme (long term support, LTS) Ubuntu du système d’exploitation pour les nœuds du cluster. Pour plus d’informations sur la version d’Ubuntu disponible avec HDInsight, ainsi que d’autres informations sur le contrôle de version des composants, consultez la page [Versions des composants HDInsight](hdinsight-component-versioning.md).

## <a name="migration-tasks"></a>Tâches de migration
Le workflow général pour la migration se présente comme suit.

![Diagramme du workflow de migration](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. Lisez chaque section de ce document pour comprendre les modifications qui peuvent être nécessaires lors de la migration du workflow existant, des tâches, etc., vers un cluster Linux.
2. Créez un cluster Linux comme environnement de test ou d’assurance qualité. Pour plus d’informations sur la création d’un cluster Linux, consultez [Création de clusters Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
3. Copiez les tâches, sources de données et récepteurs existants dans le nouvel environnement. Pour plus d’informations, consultez la section Copier des données dans l’environnement de test.
4. Effectuez des tests de validation pour vérifier que vos tâches fonctionnent comme prévu sur le nouveau cluster.

Une fois que vous avez vérifié que tout fonctionne comme prévu, planifiez un temps d’arrêt pour la migration. Pendant ce temps d’arrêt, effectuez les actions suivantes.

1. Sauvegardez toutes les données temporaires stockées localement sur les nœuds du cluster, par exemple si vous avez des données stockées directement sur un nœud principal.
2. Supprimez le cluster Windows.
3. Créez un cluster Linux à l’aide du même magasin de données par défaut que celui utilisé par le cluster Windows. Cela permet au nouveau cluster de continuer à travailler sur vos données de production existantes.
4. Importez toutes les données temporaires que vous avez sauvegardées.
5. Démarrez des tâches ou poursuivez le traitement avec le nouveau cluster.

### <a name="copy-data-to-the-test-environment"></a>Copier des données dans l’environnement de test
Il existe de nombreuses méthodes pour copier les données et les tâches, mais les deux présentées dans cette section représentent les méthodes les plus simples pour déplacer directement des fichiers vers un cluster de test.

#### <a name="hdfs-dfs-copy"></a>Copie avec HDFS DFS
Vous pouvez utiliser la commande Hadoop HDFS pour copier directement des données depuis le stockage de votre cluster de production existant vers le stockage d’un nouveau cluster de test en procédant comme suit.

1. Recherchez les informations sur le compte de stockage et le conteneur par défaut pour votre cluster existant. Pour ce faire, utilisez le script Azure PowerShell suivant.

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. Suivez les étapes du document Création de clusters Hadoop basés sur Linux dans HDInsight pour créer un environnement de test. Arrêtez-vous avant de créer le cluster et sélectionnez à la place **Configuration facultative**.
3. Dans le panneau Configuration facultative, sélectionnez **Comptes de stockage liés**.
4. Sélectionnez **Ajouter une clé de stockage**puis sélectionnez le compte de stockage renvoyé par le script PowerShell à l’étape 1, quand vous y êtes invité. Cliquez sur **Sélectionner** dans chaque panneau pour les fermer. Enfin, créez le cluster.
5. Une fois le cluster créé, connectez-vous à celui-ci à l’aide de **SSH**. Si l’utilisation de SSH avec HDInsight ne vous est pas familière, consultez l’un des articles suivants.

   * [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
   * [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
6. Dans la session SSH, utilisez la commande suivante pour copier les fichiers à partir du compte de stockage lié vers le nouveau compte de stockage par défaut. Remplacez CONTAINER et ACCOUNT par les informations sur le conteneur et le compte retournées par le script PowerShell à l’étape 1. Remplacez le chemin d’accès aux données par le chemin d’accès à un fichier de données.

        hdfs dfs -cp wasbs://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location

    > [!NOTE]
    > Si la structure de répertoire qui contient les données n’existe pas dans l’environnement de test, vous pouvez la créer à l’aide de la commande suivante.

        hdfs dfs -mkdir -p /new/path/to/create

    Le commutateur `-p` permet la création de tous les répertoires dans le chemin.

#### <a name="direct-copy-between-azure-storage-blobs"></a>Copie directe entre les objets blob Azure Storage
Vous pouvez également utiliser l’applet de commande `Start-AzureStorageBlobCopy` Azure PowerShell pour copier des blobs entre des comptes de stockage externes à HDInsight. Pour plus d’informations, consultez la section Gestion des objets blob Azure dans Utilisation d’Azure PowerShell avec Azure Storage.

## <a name="client-side-technologies"></a>Technologies côté client
En général, les technologies côté client telles que les [applets de commande Azure PowerShell](/powershell/azureps-cmdlets-docs), [l’interface de ligne de commande Azure](../xplat-cli-install.md) ou le [Kit de développement logiciel (SDK) .NET pour Hadoop](https://hadoopsdk.codeplex.com/) continuent de fonctionner de la même façon avec les clusters Linux, car elles s’appuient sur des API REST qui sont identiques sur les deux types de systèmes d’exploitation de clusters.

## <a name="server-side-technologies"></a>Technologies côté serveur
Le tableau suivant fournit des conseils sur la migration des composants côté serveur qui sont spécifiques à Windows.

| Si vous utilisez cette technologie... | Procédez comme suit... |
| --- | --- |
| **PowerShell** (scripts côté serveur, notamment les actions de script utilisées lors de la création du cluster) |Réécrivez-les en tant que scripts Bash. En ce qui concerne les actions de script, consultez [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md) et [Développement d’actions de script avec HDInsight](hdinsight-hadoop-script-actions-linux.md). |
| **Interface de ligne de commande Azure** (scripts côté serveur) |Même si l’interface de ligne de commande Azure est disponible sous Linux, elle n’est pas préinstallée sur les nœuds principaux du cluster HDInsight. Si vous en avez besoin pour les scripts côté serveur, consultez [Installer l’interface de ligne de commande Azure](../xplat-cli-install.md) pour plus d’informations sur l’installation sur les plateformes Linux. |
| **Composants .NET** |.NET n’est pas intégralement pris en charge sur tous les types de clusters HDInsight Linux. Les clusters Storm sur HDInsight basés sur Linux créés après le 28/10/2016 prennent en charge les topologies Storm C# à l’aide de l’infrastructure SCP.NET. Une prise en charge supplémentaire pour .NET sera ajouté dans les mises à jour ultérieures. |
| **Composants Win32 ou autre technologie propre à Windows** |Les conseils varient selon le composant ou la technologie ; vous pouvez trouver une version compatible avec Linux ou être amené à trouver une autre solution ou réécrire ce composant. |

## <a name="cluster-creation"></a>Création du cluster
Cette section fournit des informations sur les différences dans la création du cluster.

### <a name="ssh-user"></a>Utilisateur SSH
Les clusters HDInsight Linux utilisent le protocole **SSH (Secure Shell)** pour permettre l’accès distant aux nœuds du cluster. Contrairement au Bureau à distance pour les clusters Windows, la plupart des clients SSH ne fournissent pas d’expérience utilisateur graphique, mais plutôt une ligne de commande qui permet d’exécuter des commandes sur le cluster. Certains clients (tels que [MobaXterm](http://mobaxterm.mobatek.net/)) fournissent un navigateur de système de fichiers graphiques en plus d’une ligne de commande distante.

Lors de la création du cluster, vous devez fournir un utilisateur SSH et un **mot de passe** ou un **certificat de clé publique** pour l’authentification.

Nous recommandons d’utiliser un certificat de clé publique, qui est plus sécurisé qu’un mot de passe. L’authentification par certificat fonctionne en générant une paire de clés publique/privée signée, puis en fournissant la clé publique lors de la création du cluster. Lors de la connexion au serveur à l’aide de SSH, la clé privée sur le client fournit l’authentification pour la connexion.

Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez :

* [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
* [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

### <a name="cluster-customization"></a>Personnalisation des clusters
Les **actions de script** utilisées avec les clusters Linux doivent être écrites dans un script Bash. Les actions de script peuvent être utilisées lors de la création du cluster. Pour les clusters Linux, elles peuvent également servir à effectuer la personnalisation une fois qu’un cluster est opérationnel. Pour plus d’informations, consultez [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md) et [Développement d’actions de script avec HDInsight](hdinsight-hadoop-script-actions-linux.md).

**Bootstrap**est une autre fonctionnalité de personnalisation. Pour les clusters Windows, Bootstrap vous permet de spécifier l’emplacement de bibliothèques supplémentaires à utiliser avec Hive. Après la création du cluster, ces bibliothèques sont automatiquement disponibles pour une utilisation avec des requêtes Hive sans la nécessité d’utiliser `ADD JAR`.

Bootstrap pour les clusters Linux ne fournit pas cette fonctionnalité. Utilisez à la place l’action de script décrite dans [Ajouter les bibliothèques Hive lors de la création de cluster HDInsight](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Virtual Network
Les clusters HDInsight Windows fonctionnent uniquement avec les réseaux virtuels classiques tandis que les clusters HDInsight Linux nécessitent des réseaux virtuels Resource Manager. Si vous disposez de ressources dans un réseau virtuel classique auquel le cluster HDInsight Linux doit se connecter, consultez [Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Pour plus d’informations sur la configuration requise pour utiliser des réseaux virtuels Azure avec HDInsight, consultez [Extension des capacités de HDInsight à l’aide d’Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).

## <a name="management-and-monitoring"></a>Gestion et surveillance
La plupart des interfaces utilisateur web que vous avez peut-être utilisées avec HDInsight Windows, telles que l’historique de la tâche ou l’interface utilisateur Yarn, sont disponibles via Ambari. En outre, l’affichage Ambari Hive fournit un moyen d’exécuter des requêtes Hive à l’aide de votre navigateur web. L’interface utilisateur Web d’Ambari est disponible sur les clusters Linux à l’adresse https://CLUSTERNAME.azurehdinsight.net.

Pour plus d’informations sur l’utilisation d’Ambari, consultez les documents suivants :

* [Interface utilisateur web Ambari](hdinsight-hadoop-manage-ambari.md)
* [API Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Alertes Ambari
Ambari dispose d’un système d’alerte pouvant vous indiquer des problèmes potentiels avec le cluster. Les alertes s’affichent en tant qu’entrées de couleur rouge ou jaune dans l’interface utilisateur web Ambari, mais vous pouvez également les récupérer via l’API REST.

> [!IMPORTANT]
> Les alertes Ambari indiquent qu’un problème se pose *peut-être* et non pas qu’un problème se pose *vraiment*. Par exemple, vous pouvez recevoir une alerte indiquant que HiveServer2 n’est pas accessible, même si vous pouvez y accéder normalement.
>
> De nombreuses alertes sont implémentées comme des requêtes basées sur un intervalle pour un service et attendent une réponse dans un intervalle de temps spécifique. Par conséquent, l’alerte ne signifie pas nécessairement que le service est arrêté, simplement qu’il n’a pas retourné de résultats dans l’intervalle de temps attendu.

En général, vous devez évaluer si une alerte se produit pendant une période prolongée, ou si elle reflète des problèmes d’utilisateur qui ont été signalés précédemment avec le cluster avant que des mesures ne soient prises.

## <a name="file-system-locations"></a>Emplacements du système de fichiers
Le système de fichiers des clusters Linux se présente différemment des clusters HDInsight Windows. Utilisez le tableau suivant pour trouver des fichiers couramment utilisés.

| Je dois trouver... | Il se trouve dans... |
| --- | --- |
| Configuration |`/etc`. Par exemple, `/etc/hadoop/conf/core-site.xml` |
| Fichiers journaux |`/var/logs` |
| Hortonworks Data Platform (HDP) |`/usr/hdp`. Deux répertoires figurent ici, l’un qui représente la version HDP actuelle (par exemple, `2.2.9.1-1`) et `current`. Le répertoire `current` contient des liens symboliques vers des fichiers et répertoires situés dans le répertoire des numéros de version, et est fourni comme un moyen pratique d’accéder aux fichiers HDP puisque le numéro de version change dès que la version HDP est mise à jour. |
| hadoop-streaming.jar |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

En général, si vous connaissez le nom du fichier, vous pouvez utiliser la commande suivante à partir d’une session SSH pour rechercher le chemin du fichier :

    find / -name FILENAME 2>/dev/null

Vous pouvez également utiliser des caractères génériques avec le nom de fichier. Par exemple, `find / -name *streaming*.jar 2>/dev/null` retourne le chemin d’accès à tous les fichiers jar contenant le mot « streaming » dans le nom de fichier.

## <a name="hive-pig-and-mapreduce"></a>Hive, Pig et MapReduce
Les charges de travail Pig et MapReduce sont très similaires sur les clusters Linux, la principale différence étant que, si vous utilisez Bureau à distance pour vous connecter à un cluster Windows et exécuter des tâches, vous allez utiliser SSH avec les clusters Linux.

* [Utiliser Pig avec SSH](hdinsight-hadoop-use-pig-ssh.md)
* [Utiliser MapReduce avec SSH](hdinsight-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive
Le tableau suivant fournit des conseils sur la migration de vos charges de travail Hive.

| Sur Windows, j’utilise... | Sur Linux, j’utilise... |
| --- | --- |
| **Éditeur Hive** |[Affichage Hive dans Ambari](hdinsight-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` pour activer Tez |Tez étant le moteur d’exécution par défaut pour les clusters Linux, l’instruction set n’est plus nécessaire. |
| Scripts ou fichiers CMD sur le serveur appelés dans le cadre d’une tâche Hive |Scripts Bash |
| `hive` à partir du Bureau à distance |Utilisez [Beeline](hdinsight-hadoop-use-hive-beeline.md) ou [Hive à partir d’une session SSH](hdinsight-hadoop-use-hive-ssh.md) |

## <a name="storm"></a>Storm
| Sur Windows, j’utilise... | Sur Linux, j’utilise... |
| --- | --- |
| Tableau de bord Storm |Le tableau de bord Storm n’est pas disponible. Pour connaître des façons de soumettre des topologies, consultez [Déploiement et gestion des topologies Apache Storm sur HDInsight Linux](hdinsight-storm-deploy-monitor-topology-linux.md) . |
| Interface utilisateur de Storm |L’interface utilisateur Storm est disponible à l’adresse https://CLUSTERNAME.azurehdinsight.net/stormui |
| Visual Studio pour créer, déployer et gérer des topologies C# ou hybrides |Visual Studio peut être utilisé pour créer, déployer et gérer les topologies C# (SCP.NET) ou hybrides sur des clusters Storm sur HDInsight basés sur Linux créés après le 28/10/2017. |

## <a name="hbase"></a>HBase
Sur les clusters Linux, le parent ZNode pour HBase est `/hbase-unsecure`. Vous devez définir cela dans la configuration de toutes les applications clientes Java qui utilisent une API Java HBase native.

Pour obtenir un exemple de client qui définit cette valeur, consultez [Utilisation de Maven pour créer des applications Java utilisant HBase avec HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md) .

## <a name="spark"></a>Spark
Les clusters Spark étaient disponibles sur les clusters Windows en version préliminaire. Toutefois, pour la version finale, Spark est uniquement disponible avec les clusters Linux. Il n’existe aucun chemin de migration d’un cluster Spark Windows en version préliminaire vers un cluster Spark Linux en version finale.

## <a name="known-issues"></a>Problèmes connus
### <a name="azure-data-factory-custom-net-activities"></a>Activités .NET personnalisées Azure Data Factory
Les activités .NET personnalisées Azure Data Factory ne sont actuellement pas prises en charge sur les clusters HDInsight Linux. Au lieu de cela, vous devez utiliser l’une des méthodes suivantes pour implémenter les activités personnalisées dans le cadre de votre pipeline ADF.

* Exécutez les activités .NET sur le pool Azure Batch. Consultez la section Utilisation du service lié Azure Batch dans [Utilisation des activités personnalisées dans un pipeline Azure Data Factory](../data-factory/data-factory-use-custom-activities.md)
* Implémentez l’activité comme une activité MapReduce. Pour plus d’informations, consultez [Appeler des programmes MapReduce à partir de Data Factory](../data-factory/data-factory-map-reduce.md) .

### <a name="line-endings"></a>Fins de ligne
En règle générale, les fins de ligne sur les systèmes Windows utilisent CRLF, alors que les systèmes Linux utilisent LF. Si vous produisez ou attendez des données avec des fins de ligne CRLF, vous devrez peut-être modifier les producteurs ou les consommateurs pour utiliser la fin de ligne LF.

Par exemple, l’utilisation d’Azure PowerShell pour interroger HDInsight sur un cluster Windows retourne des données avec CRLF. La même requête avec un cluster Linux retournera LF. Dans de nombreux cas, cela importe peu au consommateur de données, mais la question doit être traitée avant la migration vers un cluster Linux.

Si vous avez des scripts qui seront exécutés directement sur les nœuds de cluster Linux (par exemple, un script Python utilisé avec Hive ou une tâche MapReduce), vous devez toujours utiliser LF en tant que fin de ligne. Si vous utilisez CRLF, des erreurs peuvent apparaître lors de l’exécution des scripts sur un cluster Linux.

Si vous savez que les scripts ne contiennent pas de chaînes avec des caractères CR incorporés, vous pouvez modifier en bloc les fins de ligne à l’aide de l’une des méthodes suivantes :

* **Si vous avez des scripts que vous prévoyez de télécharger sur le cluster**, utilisez les instructions PowerShell suivantes pour remplacer les fins de ligne CRLF par LF avant de charger le script sur le cluster.

      $original_file ='c:\path\to\script.py'
      $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
      [IO.File]::WriteAllText($original_file, $text)
* **Si vous avez des scripts qui sont déjà dans le stockage utilisé par le cluster**, vous pouvez utiliser la commande suivante à partir d’une session SSH sur le cluster Linux pour modifier le script.

      hdfs dfs -get wasbs:///path/to/script.py oldscript.py
      tr -d '\r' < oldscript.py > script.py
      hdfs dfs -put -f script.py wasbs:///path/to/script.py

## <a name="next-steps"></a>Étapes suivantes
* [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
* [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gérer des clusters HDInsight à l’aide de l’interface utilisateur Web d’Ambari](hdinsight-hadoop-manage-ambari.md)



<!--HONumber=Jan17_HO3-->


