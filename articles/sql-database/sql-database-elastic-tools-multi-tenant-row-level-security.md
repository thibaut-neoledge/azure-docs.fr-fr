---
title: "Applications multi-locataires avec des outils de base de données élastique et la sécurité au niveau des lignes"
description: "Utilisez les outils de base de données élastique avec une sécurité au niveau des lignes pour générer une application avec une couche Données hautement évolutive."
metakeywords: azure sql database elastic tools multi tenant row level security rls
services: sql-database
documentationcenter: 
manager: jhubbard
author: tmullaney
ms.assetid: e72d3cfe-e9be-4326-b776-9c6d96c0a18e
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2016
ms.author: thmullan;torsteng
ms.openlocfilehash: 4b4ec29bb53bdce413dadf7cb0751433b9ca686c
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2017
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Applications multi-locataires avec des outils de base de données élastique et la sécurité au niveau des lignes
Les [outils de base de données élastique](sql-database-elastic-scale-get-started.md) et la fonction de [sécurité au niveau des lignes (RLS)](https://msdn.microsoft.com/library/dn765131) offrent un ensemble de puissants outils, qui permettent d’étendre la couche Données d’une application multi-locataires de manière souple et efficace au moyen d’Azure SQL Database. Pour plus d’informations, consultez [Modèles de conception pour les applications SaaS mutualisées avec Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Cet article explique comment utiliser ces technologies conjointement, afin de créer une application proposant une couche Données hautement évolutive, capable de prendre en charge des partitions multi-locataires, en utilisant **SqlClient ADO.NET** et/ou **Entity Framework**.  

* Les **outils de base de données élastique** permettent aux développeurs de monter en charge la couche Données d’une application via des pratiques de partitionnement normalisées, reposant sur un ensemble de bibliothèques .NET et des modèles de service Microsoft Azure. En gérant les partitions via la bibliothèque cliente de base de données élastique, vous rationalisez et automatisez nombre des tâches de l’infrastructure portant généralement sur le partitionnement. 
* **sécurité au niveau des lignes** permet aux développeurs de stocker les données de plusieurs locataires dans la même base de données, à l’aide de stratégies de sécurité permettant de filtrer les lignes qui n’appartiennent pas au locataire exécutant une requête. Grâce à la centralisation de la logique d’accès avec RLS dans la base de données plutôt que dans l’application, vous simplifiez la maintenance et réduisez le risque d’erreurs lorsque la codebase d’une application s’agrandit. 

Grâce à l’utilisation conjointe de ces fonctionnalités, une application peut bénéficier d’une réduction des coûts et d’une optimisation de l’efficacité, via le stockage des données de plusieurs locataires au sein de la base de données d’une seule et même partition. Parallèlement à cela, elle a toujours la possibilité de proposer des partitions isolées, incluant un seul locataire, aux locataires « premium » qui doivent respecter des exigences plus élevées en termes de performances. En effet, les partitions multi-locataires ne garantissent pas la distribution équitable des ressources entre les locataires.  

En bref, la bibliothèque cliente de la base de données élastique offre des API de [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) , qui connectent automatiquement les locataires à la base de données de partition qui contient leur clé de partitionnement (il s’agit généralement d’un« ID de locataire »). Une fois la connexion établie, une stratégie de sécurité RLS appliquée au sein de la base de données s’assure que les locataires peuvent uniquement accéder aux lignes qui contiennent leur ID de locataire. Le système part du principe que l’ensemble des tables contient une colonne « ID de locataire », qui indique à quel locataire appartiennent les lignes. 

![Architecture d’application de création de blogs][1]

## <a name="download-the-sample-project"></a>Téléchargement de l’exemple de projet
### <a name="prerequisites"></a>Composants requis
* Exécuter Visual Studio version 2012 ou plus 
* Créer trois bases de données SQL Microsoft Azure 
* Télécharger un exemple de projet : [Outils de base de données pour base de données SQL Microsoft Azure - Partitions multi-locataires](http://go.microsoft.com/?linkid=9888163)
  * Saisissez les informations sur vos bases de données au début du fichier **Program.cs** 

Ce projet étend celui que décrit la section [Outils de base de données pour base de données SQL Microsoft Azure - Intégration d’Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) , en ajoutant la prise en charge des bases de données de partition multi-locataires. Il permet de créer une application console simple afin de créer des blogs et des publications, avec quatre locataires et deux bases de données de partition multi-locataires (comme illustré dans le diagramme précédent). 

Générez et exécutez l’application. Cette opération démarre le gestionnaire de mappage de la partition dédiée aux outils de base de données élastique. Exécutez les tests suivants : 

1. À l’aide d’Entity Framework et de LINQ, créez un blog et affichez tous les blogs pour chaque client.
2. À l’aide de la fonction SqlClient ADO.NET, affichez tous les blogs d’un locataire.
3. Essayez d’insérer un blog associé à un locataire incorrect, afin de vérifier qu’une erreur est déclenchée.  

Comme la fonction RLS n’a pas encore été activée sur les bases de données de la partition, vous pouvez voir que chacun de ces tests met en lumière un problème : les locataires peuvent afficher des blogs qui ne leur appartiennent pas et l’application est autorisée à insérer un blog associé à un locataire incorrect. Le reste de cet article explique comment résoudre ces problèmes en appliquant l’isolation des locataires avec la fonction RLS. La procédure à suivre implique deux étapes : 

1. **Couche Application** : modifiez le code de l’application en définissant toujours l’élément SESSION_CONTEXT sur l’ID de locataire (TenantId)actuel après l’ouverture d’une connexion. Cet exemple de projet a déjà effectué cette opération. 
2. **Couche Données** : créez une stratégie de sécurité RLS dans chaque base de données de partition, afin de filtrer les lignes selon l’ID de locataire (TenantId) stocké dans l’élément SESSION_CONTEXT. Vous devez procéder ainsi pour chaque base de données de partition. Dans le cas contraire, les lignes de partitions multi-locataires ne sont pas filtrées. 

## <a name="step-1-application-tier-set-tenantid-in-the-sessioncontext"></a>Étape 1) Couche application : définissez l’identifiant de locataire dans l’élément SESSION_CONTEXT
Une fois la connexion à la base de données de partition établie, via les API de routage dépendant des données de la bibliothèque de base de données élastique, vous devez faire en sorte que l’application indique à la base de données quel ID de locataire utilise cette connexion, afin que la stratégie de sécurité RLS puisse filtrer les lignes appartenant aux autres locataires. Pour transmettre ces informations, la méthode recommandée consiste à stocker l’ID de locataire (TenantId) en cours pour cette connexion dans l’élément [SESSION_CONTEXT](https://msdn.microsoft.com/library/mt590806.aspx). (Remarque : vous pouvez également utiliser [CONTEXT_INFO](https://msdn.microsoft.com/library/ms180125.aspx), mais SESSION_CONTEXT offre une meilleure option, car cet élément, plus facile à utiliser, renvoie la valeur NULL par défaut et prend en charge les paires clé-valeur.)

### <a name="entity-framework"></a>Entity Framework
Pour les applications utilisant Entity Framework, l’approche la plus simple consiste à définir l’élément SESSION_CONTEXT dans la substitution ElasticScaleContext décrite dans la section [Routage dépendant des données à l’aide de DbContext EF](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Avant de retourner la connexion répartie via le routage dépendant des données, créez et exécutez une commande SqlCommand qui définit l’élément SESSION_CONTEXT sur la valeur shardingKey spécifiée pour cette connexion. De cette façon, il vous suffit d’écrire le code une seule fois pour définir l’élément SESSION_CONTEXT. 

```
// ElasticScaleContext.cs 
// ... 
// C'tor for data-dependent routing. This call opens a validated connection routed to the proper 
// shard by the shard map manager. Note that the base class c'tor call fails for an open connection 
// if migrations need to be done and SQL credentials are used. This is the reason for the  
// separation of c'tors into the DDR case (this c'tor) and the internal c'tor for new shards. 
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(OpenDDRConnection(shardMap, shardingKey, connectionStr), true /* contextOwnsConnection */)
{
}

public static SqlConnection OpenDDRConnection(ShardMap shardMap, T shardingKey, string connectionStr)
{
    // No initialization
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(shardingKey, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
} 
// ... 
```

Désormais, l’élément SESSION_CONTEXT est automatiquement défini avec l’ID de locataire spécifié chaque fois que le paramètre ElasticScaleContext est appelé : 

```
// Program.cs 
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))   
    {     
        var query = from b in db.Blogs
                    orderby b.Name
                    select b;

        Console.WriteLine("All blogs for TenantId {0}:", tenantId);     
        foreach (var item in query)     
        {       
            Console.WriteLine(item.Name);     
        }   
    } 
}); 
```

### <a name="adonet-sqlclient"></a>SqlClient ADO.NET
Pour les applications utilisant SqlClient ADO.NET, il est recommandé d’opter pour la création d’une fonction wrapper autour de ShardMap.OpenConnectionForKey() qui définit automatiquement SESSION_CONTEXT sur l’ID de locataire correct avant de renvoyer une connexion. Pour garantir que SESSION_CONTEXT est toujours défini, vous ne devez ouvrir des connexions qu’à l’aide de cette fonction wrapper.

```
// Program.cs
// ...

// Wrapper function for ShardMap.OpenConnectionForKey() that automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection. As a best practice, you should only open connections using this 
// method to ensure that SESSION_CONTEXT is always set before executing a query.
public static SqlConnection OpenConnectionForTenant(ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key
        conn = shardMap.OpenConnectionForKey(tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient
// If row-level security is enabled, only Tenant 4's blogs are listed
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine("--\nAll blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);
        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="step-2-data-tier-create-row-level-security-policy"></a>Étape 2) Couche données : création d’une stratégie de sécurité au niveau des lignes
### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Créez une stratégie de sécurité pour filtrer les lignes accessibles à chaque client.
Comme l’application définit désormais l’élément SESSION_CONTEXT avec l’ID de locataire en cours avant d’envoyer des requêtes, une stratégie de sécurité RLS peut filtrer les requêtes et exclure les lignes associées à un ID de locataire différent.  

La fonction RLS est implémentée dans T-SQL : une fonction définie par l’utilisateur détermine la logique d’accès, et une stratégie de sécurité lie cette fonction à un nombre de tables quelconque. Pour les besoins de ce projet, la fonction vérifie que l’application (plutôt qu’un autre utilisateur SQL) est connectée à la base de données, et que l’ID de locataire stocké dans l’élément SESSION_CONTEXT correspond à l’ID de locataire d’une ligne donnée. Un prédicat de filtrage permet de filtrer les lignes satisfaisant à ces conditions pour les requêtes SELECT, UPDATE et DELETE ; un prédicat de blocage empêche l’insertion ou la mise à jour des lignes qui violent ces conditions. Si l’élément SESSION_CONTEXT n’a pas été défini, il retourne la valeur NULL et aucune ligne n’est visible ou ne peut être insérée. 

Pour activer la fonction RLS, exécutez la commande T-SQL suivante sur toutes les partitions, à l’aide de Visual Studio (SSDT), de SSMS ou du script PowerShell inclus dans le projet (le cas échéant, si vous avez recours aux [tâches de la base de données élastique](sql-database-elastic-jobs-overview.md), vous pouvez les utiliser pour automatiser l’exécution de cette commande T-SQL sur toutes les partitions) : 

```
CREATE SCHEMA rls -- separate schema to organize RLS objects 
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)     
    RETURNS TABLE     
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult          
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- the user in your application’s connection string (dbo is only for demo purposes!)         
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts
GO 
```

> [!TIP]
> Pour les projets plus complexes qui nécessitent l’ajout du prédicat à des centaines de tables, vous pouvez utiliser une procédure stockée d’assistance, qui génère automatiquement une stratégie de sécurité en ajoutant un prédicat sur toutes les tables dans un schéma. Pour plus d’informations, consultez le [blog Apply Row-Level Security to all tables - helper script (Appliquer la sécurité au niveau des lignes à toutes les tables - Script d’assistance)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script).  
> 
> 

À présent, si vous exécutez l’exemple d’application une nouvelle fois, les locataires ne sont en mesure de voir que les lignes qui leur appartiennent. Par ailleurs, l’application ne peut pas insérer des lignes qui appartiennent à un locataire autre que celui qui est actuellement connecté à la base de données de partition, de même qu’elle ne peut pas mettre à jour les lignes visibles pour leur affecter un autre ID de locataire. Si elle tente d’effectuer l’une ou l’autre de ces opérations, une exception DbUpdateException est déclenchée.

Si vous ajoutez une nouvelle table par la suite, effectuez une opération ALTER sur la stratégie de sécurité et ajoutez des prédicats de filtrage et de blocage sur la nouvelle table : 

```
ALTER SECURITY POLICY rls.tenantAccessPolicy     
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable
GO 
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Ajouter des contraintes par défaut afin d’indiquer automatiquement les ID de locataire pour les opérations INSERT
Vous pouvez placer une contrainte par défaut sur chaque table pour renseigner automatiquement l’ID de locataire avec la valeur actuellement stockée dans l’élément SESSION_CONTEXT lors de l’insertion de lignes. Par exemple : 

```
-- Create default constraints to auto-populate TenantId with the value of SESSION_CONTEXT for inserts 
ALTER TABLE Blogs     
    ADD CONSTRAINT df_TenantId_Blogs      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO

ALTER TABLE Posts     
    ADD CONSTRAINT df_TenantId_Posts      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO 
```

À présent, l’application n’a pas besoin de spécifier un ID de locataire lors de l’insertion de lignes : 

```
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        var blog = new Blog { Name = name }; // default constraint sets TenantId automatically     
        db.Blogs.Add(blog);     
        db.SaveChanges();   
    } 
}); 
```

> [!NOTE]
> Si vous utilisez des contraintes par défaut pour un projet Entity Framework, il est recommandé de ne PAS inclure la colonne « ID de locataire » dans votre modèle de données Entity Framework. En effet, les requêtes Entity Framework fournissent automatiquement des valeurs par défaut, qui remplacent les contraintes par défaut créées dans T-SQL et qui utilisent l’élément SESSION_CONTEXT. Pour utiliser les contraintes par défaut dans l’exemple de projet, par exemple, vous devez supprimer l’ID de locataire dans le fichier DataClasses.cs (et exécuter l’élément Add-Migration dans la Console du gestionnaire de package), puis utiliser T-SQL pour vérifier que le champ existe uniquement dans les tables de base de données. De cette façon, Entity Framework fournit automatiquement des valeurs par défaut incorrectes lors de l’insertion de données. 
> 
> 

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Facultatif) Activer un « superutilisateur » pour accéder à toutes les lignes
Certaines applications peuvent nécessiter la création d’un « superutilisateur » pouvant accéder à toutes les lignes. Cela permet par exemple d’activer la création de rapports pour tous les locataires de toutes les partitions, ou d’exécuter des opérations de fractionnement et de fusion sur des partitions impliquant le déplacement de lignes de locataires entre les bases de données. Pour ce faire, vous devez créer un nouvel utilisateur SQL (un « superutilisateur » dans cet exemple) dans chaque base de données de partition. Vous devez ensuite modifier la stratégie de sécurité en ajoutant une nouvelle fonction de prédicat permettant à cet utilisateur d’accéder à toutes les lignes :

```
-- New predicate function that adds superuser logic
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult 
        WHERE 
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- note, should not be dbo!
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        ) 
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        )
GO

-- Atomically swap in the new predicate function on each table
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts
GO
```


### <a name="maintenance"></a>Maintenance 
* **Ajout de nouvelles partitions** : exécutez le script T-SQL pour activer la sécurité au niveau des lignes sur les nouvelles partitions. Dans le cas contraire, les requêtes portant sur ces partitions ne sont pas filtrées.
* **Ajout de nouvelles tables** : ajoutez un prédicat de filtrage et de blocage à la stratégie de sécurité sur toutes les partitions chaque fois qu’une table est créée. Dans le cas contraire, les requêtes portant sur la nouvelle table ne sont pas filtrées. Vous pouvez automatiser ce processus par le biais d’un déclencheur DDL, comme décrit dans l’article [Apply Row-Level Security automatically to newly created tables (Appliquer automatiquement la sécurité au niveau des lignes aux nouvelles tables) (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Résumé
Les outils de base de données élastique et la fonction de sécurité au niveau des lignes (RLS) peuvent être utilisés ensemble pour faire monter en charge la couche Données d’une application prenant en charge les partitions multi-locataires ou à un seul locataire. Les partitions multi-locataires peuvent être utilisées pour stocker des données de manière plus efficace (notamment dans les cas où un grand nombre de locataires présente quelques lignes de données seulement). Les partitions à un seul locataire peuvent quant à elles servir à prendre en charge les locataires « premium » qui doivent respecter des exigences plus élevées en termes de performances et d’isolation.  Pour plus d’informations, consultez [Sécurité au niveau des lignes](https://msdn.microsoft.com/library/dn765131). 

## <a name="additional-resources"></a>Ressources supplémentaires
* [Qu’est-ce qu’un pool élastique Azure ?](sql-database-elastic-pool.md)
* [Montée en charge avec Base de données SQL Azure](sql-database-elastic-scale-introduction.md)
* [Modèles de conception pour les applications SaaS mutualisées avec Base de données SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Authentification sur les applications mutualisées, avec Azure AD et OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
* [Application Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Questions et demandes de fonctionnalités
Pour toute question, contactez-nous sur le [forum SQL Database](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) et formulez vos demandes de fonctionnalités éventuelles sur le [forum de commentaires SQL Database](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->


