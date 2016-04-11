<properties
	pageTitle="Activer Stretch Database pour une table | Microsoft Azure"
	description="Découvrez comment configurer une table pour Stretch Database."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Activer Stretch Database pour une table

Pour configurer une table pour Stretch Database, sélectionnez **Stretch | Activer** pour une table dans SQL Server Management Studio pour ouvrir l’Assistant **Activer une table pour Stretch**. Vous pouvez également utiliser Transact-SQL pour activer Stretch Database sur une table existante ou pour créer une nouvelle table avec Stretch Database activé.

-   Si vous stockez des données d’historique dans une table distincte, vous pouvez migrer la table entière.

-   Si votre table contient à la fois des données d’historique et des données actuelles, vous pouvez spécifier un prédicat de filtre pour sélectionner les lignes à migrer. Dans la version CTP 3.1 jusqu’à RC1, l’option permettant de spécifier un prédicat n’est pas disponible dans l’Assistant Activer la base de données pour Stretch. Vous devez utiliser l’instruction CREATE TABLE ou ALTER TABLE pour configurer une table pour Stretch Database avec cette option.

**Conditions préalables**. Si vous sélectionnez **Stretch | Activer** pour une table et que vous n’avez pas encore activé Stretch Database pour la base de données, l’Assistant configure d’abord la base de données pour Stretch Database. Suivez les étapes fournies dans [Assistant Activer la base de données pour Stretch](sql-server-stretch-database-wizard.md) au lieu des étapes décrites dans cette rubrique.

**Autorisations**. L’activation de Stretch Database sur une table ou une base de données nécessite des autorisations db\_owner. L’activation de Stretch Database sur une table nécessite également l’autorisation ALTER sur la table.

## <a name="EnableWizardTable"></a>Utiliser l’Assistant pour activer Stretch Database sur une table
**Lancer l’Assistant**

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez la table sur laquelle vous souhaitez activer Stretch.

2.  Cliquez avec le bouton droit et sélectionnez **Stretch**, puis sélectionnez **Activer** pour lancer l’Assistant.

**Introduction**

Examinez l’objectif de l’Assistant et les conditions préalables.

**Sélectionnez des tables de base de données**

Vérifiez que la table que vous souhaitez activer est affichée et sélectionnée.

Dans CTP 3.1 jusqu’à RC1, vous pouvez uniquement migrer une table entière à l’aide de l’Assistant. Si vous souhaitez spécifier un prédicat pour sélectionner des lignes à migrer à partir d’une table qui contient à la fois des données d’historique et des données actuelles, exécutez l’instruction ALTER TABLE pour spécifier un prédicat après avoir quitté l’Assistant, ou quittez l’Assistant et exécutez l’instruction ALTER TABLE, comme décrit plus loin dans cette rubrique.

**Résumé**

Examinez les valeurs que vous avez entrées et les options que vous avez sélectionnées dans l’Assistant. Ensuite, sélectionnez **Terminer** pour activer Stretch.

**Résultats**

Passez en revue les résultats.

## <a name="EnableTSQLTable"></a>Utiliser Transact-SQL pour activer Stretch Database sur une table
Vous pouvez activer Stretch Database sur une table existante ou créez une nouvelle table avec Stretch Database activé à l’aide de Transact-SQL.

### Options courantes
Utilisez les options suivantes lorsque vous exécutez CREATE TABLE ou ALTER TABLE pour activer Stretch Database sur une table.

-   Si vous le souhaitez, utilisez la clause `FILTER_PREDICATE = <predicate>` pour spécifier un prédicat pour sélectionner les lignes à migrer si la table contient à la fois des données d’historique et des données actuelles. Le prédicat doit appeler une fonction table inline. Pour plus d’informations, consultez [Write an Inline Table-Valued Function to Select Rows (Stretch Database)](sql-server-stretch-database-predicate-function.md). Si vous ne spécifiez pas de prédicat de filtre, la table entière est migrée.

    >   [AZURE.NOTE] Si vous fournissez un prédicat de filtre qui fonctionne mal, la migration de données est elle aussi médiocre. Stretch Database applique le prédicat de filtre à la table à l’aide de l’opérateur CROSS APPLY.

    Dans la version CTP 3.1 jusqu’à RC1, cette option n’est pas disponible dans l’Assistant Activer la base de données pour Stretch. Vous devez utiliser l’instruction CREATE TABLE ou ALTER TABLE pour configurer une table pour Stretch Database avec cette option. Pour plus d’informations, voir [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).

-   Spécifiez `MIGRATION_STATE = OUTBOUND` pour démarrer la migration des données immédiatement, ou `MIGRATION_STATE = PAUSED` pour reporter le début de la migration des données.

### Activer Stretch Database pour une table existante
Pour configurer une table existante pour Stretch Database, exécutez la commande ALTER TABLE.

Voici un exemple qui migre la table entière et commence immédiatement la migration des données.

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;
```
Voici un exemple qui migre uniquement les lignes identifiées par la Fonction table inline `dbo.fn_stretchpredicate` et reporte la migration des données. Pour plus d’informations sur le prédicat de filtre, voir [Écrire une Fonction table inline pour sélectionner des lignes (Stretch Database)](sql-server-stretch-database-predicate-function.md).

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = PAUSED ) );
```

Pour plus d’informations, voir [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).

### Créer une table avec Stretch Database activé
Pour créer une nouvelle table avec Stretch Database activé, exécutez la commande CREATE TABLE.

Voici un exemple qui migre la table entière et commence immédiatement la migration des données.

```tsql
CREATE TABLE <table name> ...
    WITH ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;
```
Voici un exemple qui migre uniquement les lignes identifiées par la Fonction table inline `dbo.fn_stretchpredicate` et reporte la migration des données. Pour plus d’informations sur le prédicat de filtre, voir [Écrire une Fonction table inline pour sélectionner des lignes (Stretch Database)](sql-server-stretch-database-predicate-function.md).

```tsql
CREATE TABLE <table name> ...
    WITH ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = PAUSED ) );
```

Pour plus d’informations, voir [CREATE TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx).


## Voir aussi

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

[CREATE TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx)

<!---HONumber=AcomDC_0330_2016-->