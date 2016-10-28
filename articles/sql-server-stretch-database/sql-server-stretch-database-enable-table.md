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
	ms.date="08/05/2016"
	ms.author="douglasl"/>

# Activer Stretch Database pour une table

Pour configurer une table pour Stretch Database, sélectionnez **Stretch | Activer** pour une table dans SQL Server Management Studio pour ouvrir l’Assistant **Activer une table pour Stretch**. Vous pouvez également utiliser Transact-SQL pour activer Stretch Database sur une table existante ou pour créer une nouvelle table avec Stretch Database activé.

-   Si vous stockez des données brutes dans une table distincte, vous pouvez migrer la table entière.

-   Si votre table contient à la fois des données brutes et des données à chaud, vous pouvez spécifier une fonction de filtre pour sélectionner les lignes à transférer.

**Conditions préalables**. Si vous sélectionnez **Stretch | Activer** pour une table et que vous n’avez pas encore activé Stretch Database pour la base de données, l’Assistant configure d’abord la base de données pour Stretch Database. Suivez les étapes fournies dans [Commencez par exécuter l’Assistant Activer la base de données pour Stretch](sql-server-stretch-database-wizard.md) au lieu des étapes décrites dans cette rubrique.

**Autorisations**. L’activation de Stretch Database sur une table ou une base de données nécessite des autorisations db\_owner. L’activation de Stretch Database sur une table nécessite également l’autorisation ALTER sur la table.

 >   [AZURE.NOTE] Plus tard, si vous désactivez Stretch Database, gardez à l’esprit que la désactivation pour une table ou une base de données ne supprime pas l’objet distant. Si vous souhaitez supprimer la table distante ou la base de données distante, vous devez le faire à l’aide du portail de gestion Azure. Les objets distants continuent d’entraîner des coûts Azure jusqu’à leur suppression manuelle.
 
## <a name="EnableWizardTable"></a>Utiliser l’Assistant pour activer Stretch Database sur une table
**Lancer l’Assistant**

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez la table sur laquelle vous souhaitez activer Stretch.

2.  Cliquez avec le bouton droit et sélectionnez **Stretch**, puis sélectionnez **Activer** pour lancer l’Assistant.

**Introduction**

Examinez l’objectif de l’Assistant et les conditions préalables.

**Sélectionnez des tables de base de données**

Vérifiez que la table que vous souhaitez activer est affichée et sélectionnée.

Vous pouvez migrer une table entière ou spécifier une fonction de filtre simple dans l’Assistant. Si vous souhaitez utiliser un type différent de fonction de filtre pour sélectionner les lignes à migrer, effectuez l’une des opérations suivantes.

-   Quittez l'assistant et exécutez l'instruction ALTER TABLE pour activer Stretch pour la table et spécifier une fonction de filtre.

-   Exécutez l'instruction ALTER TABLE pour spécifier une fonction de filtre une fois que vous avez quitté l'assistant. Pour connaître les étapes requises, reportez-vous à [Ajouter une fonction de filtre après avoir exécuté l’Assistant](sql-server-stretch-database-predicate-function.md#addafterwiz).

La syntaxe ALTER TABLE est décrite plus loin dans cette rubrique.

**Résumé**

Examinez les valeurs que vous avez entrées et les options que vous avez sélectionnées dans l’Assistant. Ensuite, sélectionnez **Terminer** pour activer Stretch.

**Résultats**

Passez en revue les résultats.

## <a name="EnableTSQLTable"></a>Utiliser Transact-SQL pour activer Stretch Database sur une table
Vous pouvez activer Stretch Database sur une table existante ou créez une nouvelle table avec Stretch Database activé à l’aide de Transact-SQL.

### Options
Utilisez les options suivantes lorsque vous exécutez CREATE TABLE ou ALTER TABLE pour activer Stretch Database sur une table.

-   Si vous le souhaitez, utilisez la clause `FILTER_PREDICATE = <function>` pour spécifier une fonction pour sélectionner les lignes à migrer si la table contient à la fois des données brutes et des données à chaud. Le prédicat doit appeler une fonction table inline. Pour plus d’informations, consultez [Sélection des lignes à migrer à l’aide d’une fonction de filtre](sql-server-stretch-database-predicate-function.md). Si vous ne spécifiez pas de fonction de filtre, la table entière est migrée.

    >   [AZURE.NOTE] Si vous fournissez une fonction de filtre qui fonctionne mal, la migration de données est elle aussi médiocre. Stretch Database applique la fonction de filtre à la table à l’aide de l’opérateur CROSS APPLY.

-   Spécifiez `MIGRATION_STATE = OUTBOUND` pour démarrer la migration des données immédiatement, ou `MIGRATION_STATE = PAUSED` pour reporter le début de la migration des données.

### Activer Stretch Database pour une table existante
Pour configurer une table existante pour Stretch Database, exécutez la commande ALTER TABLE.

Voici un exemple qui migre la table entière et commence immédiatement la migration des données.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <table name>  
    SET ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;  
GO
```
Voici un exemple qui migre uniquement les lignes identifiées par la fonction table inline `dbo.fn_stretchpredicate` et reporte la migration des données. Pour plus d’informations sur la fonction de filtre, consultez [Sélection des lignes à migrer à l’aide d’une fonction de filtre](sql-server-stretch-database-predicate-function.md).

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <table name>  
    SET ( REMOTE_DATA_ARCHIVE = ON (  
        FILTER_PREDICATE = dbo.fn_stretchpredicate(),  
        MIGRATION_STATE = PAUSED ) ) ;  
 GO
```

Pour plus d’informations, consultez [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).

### Créer une table avec Stretch Database activé
Pour créer une nouvelle table avec Stretch Database activé, exécutez la commande CREATE TABLE.

Voici un exemple qui migre la table entière et commence immédiatement la migration des données.

```tsql
USE <Stretch-enabled database name>;
GO
CREATE TABLE <table name>
    ( ... )  
    WITH ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;  
GO
```

Voici un exemple qui migre uniquement les lignes identifiées par la fonction table inline `dbo.fn_stretchpredicate` et reporte la migration des données. Pour plus d’informations sur la fonction de filtre, consultez [Sélection des lignes à migrer à l’aide d’une fonction de filtre](sql-server-stretch-database-predicate-function.md).

```tsql
USE <Stretch-enabled database name>;
GO
CREATE TABLE <table name>
    ( ... )  
    WITH ( REMOTE_DATA_ARCHIVE = ON (  
        FILTER_PREDICATE = dbo.fn_stretchpredicate(),  
        MIGRATION_STATE = PAUSED ) ) ;  
GO  
```

Pour plus d’informations, consultez [CREATE TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx).


## Voir aussi

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

[CREATE TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx)

<!---HONumber=AcomDC_0810_2016-->