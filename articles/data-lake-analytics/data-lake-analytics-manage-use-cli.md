<properties 
   pageTitle="Gestion d’Azure Data Lake Analytics à l’aide de l’interface de ligne de commande Azure | Azure" 
   description="Apprenez à gérer des comptes Data Lake Analytics, des sources de données, des utilisateurs et des travaux à l'aide de l’interface de ligne de commande Azure." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="11/03/2015"
   ms.author="jgao"/>

# Gestion de Azure Data Lake Analytics à l’aide de l’interface de ligne de commande Azure (CLI)

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Apprenez à gérer des comptes Azure Data Lake Analytics, des sources de données, des utilisateurs et des travaux à l'aide d'Azure. Pour afficher la rubrique de gestion à l'aide d'autres outils, cliquez sur l'onglet de sélection ci-dessus.

**Configuration requise**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/fr-FR/pricing/free-trial/).
- **Interface de ligne de commande Azure**. Consultez [Installation et configuration de l’interface de ligne de commande Azure](xplat-cli.md).
	- Téléchargez et installez les [outils d’interface de ligne de commande Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases) en **version préliminaire** pour effectuer cette démonstration.
- **Authentication**, en utilisant la commande  suivante :

		azure login
	Pour plus d'informations sur l'authentification à l'aide d'un compte professionnel ou scolaire, consultez la rubrique [Se connecter à un abonnement Azure à partir de l'interface de ligne de commande Azure](xplat-cli-connect.md).
- **Passez en mode Azure Resource Manager** en exécutant la commande suivante :

		azure config mode arm

**Pour répertorier les commandes Data Lake Store et Data Lake Analytics :**

	azure datalake store
	azure datalake analytics

<!-- ################################ -->
<!-- ################################ -->
## Gérer les comptes

Avant d'exécuter des travaux Data Lake Analytics, vous devez avoir un compte Data Lake Analytics. Contrairement à Azure HDInsight, vous ne payez pas pour un compte Analytics lorsque celui-ci n'exécute aucun travail. Vous payez uniquement lorsqu'il exécute un travail. Pour plus d'informations, consultez [Présentation d'Azure Data Lake Analytics](data-lake-analytics-overview.md).

###Création de comptes

  	azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"


###Mise à jour de comptes

La commande suivante met à jour les propriétés d'un compte Data Lake Analytics existant
  	
	azure datalake analytics account set "<Data Lake Analytics Account Name>"


###Énumérer les comptes

Énumérer les comptes Data Lake Analytics

	azure datalake analytics account list

Répertorier les comptes Data Lake Analytics dans un groupe de ressources spécifique

	azure datalake analytics account list -g "<Azure Resource Group Name>"

Obtenir des détails sur un compte Data Lake Analytics spécifique

	azure datalake analytics account show -g "<Azure Resource Group Name>" -n "<Data Lake Analytics Account Name>"

###Supprimer les comptes Data Lake Analytics

  	azure datalake analytics account delete "<Data Lake Analytics Account Name>"


<!-- ################################ -->
<!-- ################################ -->
## Gestion des sources de données du compte

Data Lake Analytics prend actuellement en charge les sources de données suivantes :

- [Azure Data Lake Storage](data-lake-storage-overview.md)
- [Azure Storage](storage-introduction.md)

Lorsque vous créez un compte Analytics, vous devez désigner un compte Azure Data Lake Storage comme compte de stockage par défaut. Le compte de stockage ADL par défaut est utilisé pour stocker les métadonnées du travail et les journaux d'audit du travail. Après la création d'un compte Analytics, vous pouvez ajouter des comptes Data Lake Storage et/ou des comptes Azure Storage supplémentaires.

### Recherchez le compte de stockage ADL par défaut

	azure datalake analytics account show "<Data Lake Analytics Account Name>"

La valeur est répertoriée sous properties:datalakeStoreAccount:name.

### Ajouter des comptes de stockage d'objets Blob Azure supplémentaires

  	azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -b "<Azure Blob Storage Account Short Name>" -k "<Azure Storage Account Key>"

>[AZURE.NOTE]Seuls les noms courts Blob Storage sont pris en charge. N'utilisez pas de nom de domaine complet, comme « myblob.blob.core.windows.net ».

### Ajouter des comptes Data Lake Store supplémentaires

  	azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -l "<Data Lake Store Account Name>" [-d]

[-d] est un commutateur facultatif pour indiquer si le lac de données ajouté est le compte Data Lake par défaut.

### Mettre à jour la source de données existante

Pour faire d’un compte existant de magasin Data Lake un compte par défaut :

  	azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -l "<Azure Data Lake Store Account Name>" -d
	  
Pour mettre à jour une clé de compte Blob Storage existante :

  	azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -b "<Blob Storage Account Name>" -k "<New Blob Storage Account Key>"

### Répertorier les sources de données :

	azure datalake analytics account show "<Data Lake Analytics Account Name>"
	
![Data Lake Analytics : énumérer les sources de données](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### Supprimer des sources de données :

Pour supprimer un compte de magasin Data Lake :

  	azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Azure Data Lake Store Account Name>"

Pour supprimer un compte de stockage Blob :

  	azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Blob Storage Account Name>"

## Gestion des travaux

Vous devez disposer d'un compte Data Lake Analytics avant de pouvoir créer un travail. Pour plus d'informations, consultez [Gestion des comptes Data Lake Analytics](#manage-accounts).

### Répertorier les travaux

  	azure datalake analytics job list -n "<Data Lake Analytics Account Name>"

![Data Lake Analytics : énumérer les sources de données](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### Obtenir les détails du travail

  	azure datalake analytics job show -n "<Data Lake Analytics Account Name>" -j "<Job ID>"
	
### Soumettre les travaux

> [AZURE.NOTE]La priorité par défaut d'un travail est 1 000 et le degré de parallélisme par défaut d'un travail est 1.

	azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"

### Annuler les travaux

Utilisez la commande list pour trouver l'id de travail, puis utilisez la fonction d’annulation pour annuler le travail.

  	azure datalake analytics job list -n "<Data Lake Analytics Account Name>"
  	azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job ID>"

## Gestion du catalogue

Le catalogue U-SQL est utilisé pour structurer les données et le code afin que les scripts U-SQL puissent les partager. Le catalogue permet les meilleures performances possibles avec les données comprises dans Azure Data Lake. Pour plus d'informations, consultez [Utilisation du catalogue U-SQL](data-lake-analytics-use-u-sql-catalog.md).
 
###Répertorier les éléments du catalogue

	#List databases
	azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t database

	#List tables
	azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t table
	
Les types incluent : database, schema, assembly, externaldatasource, table, tablevaluedfunction ou tablestatistics.

###Créer la clé secrète du catalogue

	azure datalake analytics catalog secret create -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

### Modifier la clé secrète du catalogue

  	azure datalake analytics catalog secret set -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

###Supprimer la clé secrète du catalogue

	azure datalake analytics catalog secrete delete -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

<!-- ################################ -->
<!-- ################################ -->
## Utilisez des groupes ARM

Les applications sont généralement constituées de nombreux composants, par exemple une application web, base de données, serveur de base de données, stockage et services tiers. Azure Resource Manager (ARM) vous permet de manipuler les ressources de votre application sous la forme d’un groupe, nommé groupe de ressources Azure. Vous pouvez déployer, mettre à jour, surveiller ou supprimer toutes les ressources de votre application dans le cadre d’une opération unique et coordonnée. Vous utilisez un modèle de déploiement pouvant fonctionner avec différents environnements (environnements de test, intermédiaire et de production). Vous pouvez clarifier la facturation pour votre organisation en visualisant les coûts cumulés pour l’ensemble du groupe. Pour plus d’informations, consultez la page [Présentation d’Azure Resource Manager](resource-group-overview.md).

Un service Data Lake Analytics peut inclure les composants suivants :

- Compte Azure Data Lake Analytics
- Compte Azure Data Lake Storage par défaut requis
- Comptes Azure Data Lake Storage supplémentaires
- Comptes Azure Storage supplémentaires

Vous pouvez créer tous ces composants dans un groupe ARM pour les rendre plus facile à gérer.

![compte et stockage Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Un compte Data Lake Analytics et les compte de stockage dépendants doivent se trouver dans le même centre de données Azure. Le groupe ARM peut cependant se trouver dans un autre centre de données.


##Voir aussi 

- [Vue d'ensemble de Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Prise en main de Data Lake Analytics à l'aide du portail Azure en version préliminaire](data-lake-analytics-get-started-portal.md)
- [Gestion d'Azure Data Lake Analytics à l'aide du portail Azure en version préliminaire](data-lake-analytics-use-portal.md)
- [Surveiller et résoudre les problèmes des tâches Azure Data Lake Analytics avec le portail Azure en version préliminaire](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=Nov15_HO4-->