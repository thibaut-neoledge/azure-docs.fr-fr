---
title: Ajouter les bibliothèques Hive lors de la création de cluster HDInsight | Microsoft Docs
description: Découvrez comment ajouter des bibliothèques Hive (fichiers jar) à un cluster HDInsight pendant la création du cluster.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/20/2016
ms.author: larryfr

---
# Ajouter les bibliothèques Hive lors de la création de cluster HDInsight
Si vous avez des bibliothèques que vous utilisez fréquemment avec Hive sur HDInsight, ce document contient des informations sur l'utilisation d'une action de script pour précharger les bibliothèques lors de la création du cluster. Ainsi, les bibliothèques sont globalement disponibles dans Hive (inutile d'utiliser [ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) pour les charger).

## Fonctionnement
Lorsque vous créez un cluster, vous pouvez éventuellement spécifier une action de script qui exécute un script sur les nœuds de cluster lors de leur création. Le script dans ce document accepte un seul paramètre, qui est un emplacement WASB qui contient les bibliothèques (stockées en tant que fichiers jar) qui seront préchargées.

Lors de la création du cluster, le script énumère les fichiers, les copie sur le répertoire `/usr/lib/customhivelibs/` sur le nœud principal et le nœud de travail, puis les ajoute à la propriété `hive.aux.jars.path` dans le fichier `core-site.xml`. Pour les clusters basés sur Linux, il met également à jour le fichier `hive-env.sh` avec l'emplacement des fichiers.

> [!NOTE]
> L’utilisation de l’action de script dans cet article rend les bibliothèques disponibles dans les scénarios suivants :
> 
> * **HDInsight basé sur Linux** : lorsque vous utilisez la **ligne de commande Hive**, **WebHCat** (Templeton) et **HiveServer2**.
> * **HDInsight basé sur Windows** : lorsque vous utilisez la **ligne de commande Hive** et **WebHCat** (Templeton).
> 
> 

## Le script
**Emplacement du script**

Pour les **clusters basés sur Linux** : [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Pour les **clusters basés sur Windows** : [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

**Configuration requise**

* Les scripts doivent être appliqués à la fois aux **nœuds principaux** et aux **nœuds de travail**.
* Les fichiers jar à installer doivent être stockés dans le stockage d'objets Blob Azure dans un **seul conteneur**.
* Le compte de stockage contenant la bibliothèque de fichiers jar **doit** être lié au cluster HDInsight lors de la création. Cela peut se faire de deux façons :
  
  * En étant dans un conteneur sur le compte de stockage par défaut pour le cluster.
  * En étant dans un conteneur sur un conteneur de stockage lié. Par exemple, dans le portail vous pouvez utiliser **Configuration facultative**, **Comptes de stockage liés** pour ajouter un stockage supplémentaire.
* Le chemin WASB vers le conteneur doit être spécifié en tant que paramètre à l'action de script. Par exemple, si les fichiers jar sont stockés dans un conteneur nommé **libs** sur un compte de stockage nommé **mystorage**, le paramètre serait \_\_wasbs://libs@mystorage.blob.core.windows.net/__.
  
  > [!NOTE]
  > Ce document suppose que vous avez déjà créé un compte de stockage, un conteneur d'objets blob et que vous avez déjà téléchargé les fichiers vers ceux-ci.
  > 
  > Si vous n'avez pas créé de compte de stockage, vous pouvez le faire via le [portail Azure](https://portal.azure.com). Vous pouvez ensuite utiliser un utilitaire tel que [Azure Storage Explorer](http://storageexplorer.com/) pour créer un conteneur dans le compte et y télécharger des fichiers.
  > 
  > 

## Créer un cluster à l'aide du script
> [!NOTE]
> Les étapes suivantes créent un cluster HDInsight sous Linux. Pour créer un cluster basé sur Windows, sélectionnez **Windows** comme système d'exploitation du cluster lors de sa création et utilisez le script Windows (PowerShell) plutôt que le script Bash.
> 
> Vous pouvez également utiliser Azure PowerShell ou le Kit de développement logiciel (SDK) .NET HDInsight pour créer un cluster à l’aide de ce script. Pour plus d’informations sur ces méthodes, consultez [Personnaliser des clusters HDInsight à l’aide d’actions de script](hdinsight-hadoop-customize-cluster-linux.md).
> 
> 

1. Démarrez l’approvisionnement d’un cluster à l’aide de la procédure décrite dans [Approvisionner des clusters HDInsight sous Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), mais n’achevez pas l’approvisionnement.
2. Dans le panneau **Configuration facultative**, sélectionnez **Actions de script**, puis indiquez les informations comme spécifié ci-dessous :
   
   * **NAME** : saisissez un nom convivial pour l’action de script.
   * **SCRIPT URI** : https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh
   * **HEAD** : cochez cette option.
   * **WORKER** : cochez cette option.
   * **ZOOKEEPER** : laissez ce champ vide.
   * **PARAMETERS** : entrez l'adresse WASB du compte de stockage et du conteneur qui contiennent les fichiers jar. Par exemple, \_\_wasbs://libs@mystorage.blob.core.windows.net/__.
3. En bas de l’écran **Actions de script**, utilisez le bouton **Sélectionner** pour enregistrer la configuration.
4. Dans le panneau **Configuration facultative**, sélectionnez **Comptes de stockage liés** et sélectionnez le lien **Ajouter une clé de stockage**. Sélectionnez le compte de stockage qui contient les fichiers jar et utilisez les boutons de **sélection** pour enregistrer les paramètres et revenir au panneau **Configuration facultative**.
5. Utilisez le bouton **Sélectionner** au bas du panneau **Configuration facultative** pour enregistrer les informations de configuration facultatives.
6. Continuez l’approvisionnement du cluster, comme indiqué dans [Approvisionner des clusters HDInsight sous Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

Lorsque la création du cluster est terminée, vous pourrez utiliser les fichiers jar ajoutés via ce script à partir de Hive, sans avoir à utiliser l’instruction `ADD JAR`.

## Étapes suivantes
Dans ce document, vous avez appris à ajouter des bibliothèques Hive contenues dans les fichiers jar à un cluster HDInsight pendant la création du cluster. Pour plus d'informations sur l’utilisation de Hive, consultez la rubrique [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)

<!---HONumber=AcomDC_0921_2016-->