<properties
   pageTitle="Copier des données d’objets blob Azure Storage vers Data Lake Store | Microsoft Azure"
   description="Utiliser l’outil AdlCopy pour copier les données d’objets blob Azure Storage vers Data Lake Store"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/05/2016"
   ms.author="nitinme"/>

# Copier des données d’objets blob Azure Storage vers Data Lake Store

Azure Data Lake Store fournit un outil en ligne de commande nommé [AdlCopy](http://aka.ms/downloadadlcopy) qui permet de copier les données **d’objets blob Azure Storage dans Data Lake Store**. Vous ne pouvez pas utiliser AdlCopy pour copier des données de Data Lake Store vers des objets blob Azure Storage.

Vous pouvez utiliser l’outil AdlCopy de deux manières :

* **Autonome** : l’outil utilise des ressources Data Lake Store pour effectuer la tâche.
* **À l’aide d’un compte Data Lake Analytics** : les unités affectées à votre compte Data Lake Analytics permettent d’effectuer l’opération de copie. Vous pouvez utiliser cette option lorsque vous souhaitez que les tâches de copie s’effectuent de manière prévisible.

##Composants requis

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Activez votre abonnement Azure** pour la version d'évaluation publique de Data Lake Store. Consultez les [instructions](data-lake-store-get-started-portal.md#signup).
- Conteneur d’**objets blob Azure Storage** avec quelques données.
- **Compte Azure Data Lake Analytics (facultatif)** - Consultez [Prise en main d’Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) pour obtenir des instructions sur la création d’un compte Data Lake Store.
- **Outil AdlCopy**. Installez l’outil AdlCopy à partir de [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy).

## Syntaxe de l’outil AdlCopy

L’outil AdlCopy utilise la syntaxe suivante.

	AdlCopy /Source <Blob source> /Dest <ADLS destination> /SourceKey <Key for Blob account> /Account <ADLA account> /Unit <Number of Analytics units>

Les paramètres de la syntaxe sont décrits ci-après :

| Option | Description |
|-----------|------------|
| Source | Spécifie l’emplacement de la source de données dans l’objet blob Azure Storage. La source peut être un conteneur d’objets blob ou un objet blob. |
| Dest | Spécifie la destination Data Lake Store vers laquelle effectuer la copie. |
| SourceKey | Spécifie la clé d’accès de stockage pour la source d’objet blob Azure Storage. |
| Compte | **Facultatif**. Utilisez ce paramètre si vous souhaitez utiliser un compte Azure Data Lake Analytics pour exécuter la tâche de copie. Si vous utilisez l’option /Account dans la syntaxe, mais sans spécifier de compte Data Lake Analytics, AdlCopy utilise un compte par défaut pour exécuter la tâche. En outre, si vous utilisez cette option, vous devez ajouter la source (objet blob Azure Storage) et la destination (Azure Data Lake Store) comme sources de données de votre compte Data Lake Analytics. |
| Units | Spécifie le nombre d’unités Data Lake Analytics utilisées pour la tâche de copie. Cette option est obligatoire si vous utilisez l’option **/Account** pour spécifier le compte Data Lake Analytics.                                                                                                                                                                                                                                                                                                                                               



## Utiliser l’outil AdlCopy autonome

1. Ouvrez une invite de commandes et accédez au répertoire où vous avez installé AdlCopy, généralement `%HOMEPATH%\Documents\adlcopy`.

2. Exécutez la commande suivante pour copier un objet blob spécifique du conteneur source vers Data Lake Store :

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

	Par exemple :

		AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

	Vous êtes invité à entrer les informations d’identification de l’abonnement Azure sous lequel vous disposez d’un compte Data Lake Store. Vous devez voir une sortie similaire à ce qui suit :

		Initializing Copy.
		Copy Started.
		...............
		0.00% data copied.
		. . .
		. . .
		100% data copied.
		Finishing copy.
		....
		Copy Completed.

1. Vous pouvez également copier tous les objets blob d’un conteneur donné vers le compte Data Lake Store à l’aide de la commande suivante :

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>		

	Par exemple :

		AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==



## Utiliser AdlCopy avec un compte Data Lake Analytics

Vous pouvez également utiliser votre compte Data Lake Analytics pour exécuter la tâche AdlCopy afin de copier les données d’objets blob Azure Storage vers Data Lake Store. Vous utilisez généralement cette option lorsque les données à déplacer sont situées dans une plage de plusieurs gigaoctets et téraoctets et que vous souhaitez que le débit des performances soit amélioré et prévisible.

Pour utiliser votre compte Data Lake Analytics avec AdlCopy, vous devez ajouter la source (objet blob Azure Storage) et la destination (Azure Data Lake Store) comme sources de données de votre compte Data Lake Analytics. Pour obtenir des instructions sur l’ajout de sources de données à votre compte Data Lake Analytics, consultez [Gérer les sources de donnés d’un compte Data Lake Analytics](..//data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

Exécutez la commande suivante :

	AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

Par exemple :

	AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeaccount /Units 2

## Facturation

* Si vous utilisez l’outil AdlCopy de manière autonome, vous êtes facturé en fonction des coûts de sortie correspondant au déplacement des données, si le compte Azure Storage source n’est pas situé dans la même région que Data Lake Store.

* Si vous utilisez l’outil AdlCopy avec votre compte Data Lake Analytics, les [tarifs de facturation Data Lake Analytics standard](https://azure.microsoft.com/pricing/details/data-lake-analytics/) s’appliquent.

## Considérations sur l’utilisation d’AdlCopy

* AdlCopy ne prend pas en charge la copie de données à partir de sources contenant collectivement plus de 1 000 fichiers et dossiers. Une autre approche consisterait à répartir les fichiers/dossiers dans différents sous-dossiers et à utiliser comme source le chemin d'accès à ces sous-dossiers.

## Étapes suivantes

- [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)
- [Utiliser Azure Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utiliser Azure HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0413_2016-->