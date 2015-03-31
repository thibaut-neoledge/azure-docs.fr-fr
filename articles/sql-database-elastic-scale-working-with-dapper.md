<properties 
	pageTitle="Utilisation de l'infrastructure élastique avec Dapper" 
	description="Utilisation de l'infrastructure élastique avec Dapper." 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="torsteng"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/01/2015" 
	ms.author="torsteng@microsoft.com"/>

# Utilisation de l'infrastructure élastique avec Dapper 

Ce document est destiné aux développeurs qui s'appuient sur Dapper pour créer des applications, mais veulent également utiliser l'[Infrastructure élastique de base de données SQL Azure](http://azure.microsoft.com/documentation/articles/sql-database-elastic-scale-introduction/) pour agrandir et réduire les capacités de manière élastique via le partitionnement et la mise à l'échelle de leurs applications. Ce document présente les modifications des applications axées sur Dapper requises pour intégrer les fonctionnalités de l'infrastructure élastique actuelles. Nous nous concentrerons sur la composition de la gestion de partition élastique et du routage dépendant des données avec Dapper. 

**Exemple de code** : [Infrastructure élastique avec Base de données SQL Azure - Intégration de Dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f)
 
L'intégration de **Dapper** et de **DapperExtensions** avec l'infrastructure élastique de base de données SQL Azure est simple. Les applications peuvent utiliser le routage dépendant des données à infrastructure élastique en modifiant la création et l'ouverture de nouveaux objets [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) pour utiliser l'appel [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) à partir de la [bibliothèque de l'infrastructure élastique](http://msdn.microsoft.com/library/azure/dn765902.aspx). Cela limite les modifications au sein de votre application à la création et l'ouverture de nouvelles connexions uniquement. 

## Vue d'ensemble de Dapper
**Dapper** est un mappeur relationnel objet. Il mappe les objets .NET de votre application à une base de données relationnelle (et inversement). La première partie de l'exemple de code illustre comment vous pouvez intégrer l'infrastructure élastique de base de données SQL Azure avec les applications Dapper. La deuxième partie de l'exemple de code illustre comment l'infrastructure élastique peut s'intégrer lors de l'utilisation de Dapper et de DapperExtensions. 

La fonctionnalité de mappeur de Dapper fournit des méthodes d'extension sur les connexions de base de données qui simplifient la soumission d'instructions T-SQL pour l'exécution ou l'interrogation de la base de données. Par exemple, Dapper facilite le mappage entre vos objets .NET et les paramètres des instructions SQL pour les appels **Execute**, ou pour utiliser les résultats de vos requêtes SQL dans des objets .NET à l'aide des appels **Query** de Dapper. 

Lorsque vous utilisez DapperExtensions, vous n'avez plus besoin de fournir les instructions SQL. Les méthodes d'extensions comme **GetList** ou **Insert** sur la connexion de base de données créent les instructions SQL en arrière-plan.
 
Un autre avantage de Dapper et de DapperExtensions est que l'application contrôle la création de la connexion de base de données. Cela permet d'interagir avec l'infrastructure élastique qui alloue les connexions de base de données en fonction du mappage de shardlets aux bases de données.

Pour obtenir les assemblys Dapper, consultez [Dapper point net](http://www.nuget.org/packages/Dapper/). Pour les extensions Dapper, consultez [DapperExtensions](http://www.nuget.org/packages/DapperExtensions).

## Aperçu rapide de l'infrastructure élastique

Avec l'infrastructure élastique de base de données SQL Azure, vous définissez les partitions de vos données d'application appelées *shardlets*, vous les mappez à des bases de données et les identifiez à l'aide de *sharding keys*. Vous pouvez avoir autant de bases de données que nécessaire et distribuer vos shardlets sur ces bases de données. Le mappage des valeurs de clé de partitionnement vers les bases de données est stocké par une carte de partitions fournie par les API de l'infrastructure élastique. Nous appelons cette fonctionnalité la **gestion de mappage de partition**. La carte de partitions sert également de service Broker de connexion de base de données pour les demandes transportant une clé de partitionnement. Nous appelons cette fonctionnalité le **routage dépendant des données**.

![Shard maps and data dependent routing][1]

Le gestionnaire des cartes de partitions de l'infrastructure élastique empêche tout affichage incohérent des données shardlet pouvant perturber les utilisateurs lors des opérations de gestion de shardlet simultanées dans les bases de données. Pour ce faire, la partition mappe dans le service Broker de l'infrastructure flexible les connexions de base de données pour une application d'infrastructure élastique. Cela permet à la fonctionnalité de mappage de partition d'arrêter automatiquement une connexion de base de données lorsque les opérations de gestion de partition peuvent affecter le shardlet. 

Plutôt que d'utiliser la méthode traditionnelle de création des connexions pour Dapper, nous devons utiliser la [méthode OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn824099.aspx). Cela garantit que toutes les validations ont lieu et que les connexions sont gérées correctement lors du déplacement de données entre les partitions.

### Configuration requise pour l'intégration Dapper

Lors de l'utilisation des API d'infrastructure élastique et Dapper, nous souhaitons conserver les propriétés suivantes :

* **Montée en puissance** : nous souhaitons ajouter ou supprimer des bases de données de la couche Données de l'application partitionnée si nécessaire pour les demandes de capacité de l'application. 
-    **Cohérence** : Étant donné que notre application est redimensionnée à l'aide du partitionnement, nous devons utiliser le routage dépendant des données. Pour ce faire, nous souhaitons utiliser les fonctionnalités de routage dépendant des données de l'infrastructure élastique. En particulier, nous souhaitons conserver les garanties de validation et de cohérence fournies par les connexions réparties via le Gestionnaire de carte de partition à infrastructure élastique afin d'éviter une corruption ou des résultats de requête incorrects. Cela garantit que les connexions à un shardlet donné sont rejetées ou arrêtées si (par exemple) le shardlet est en cours de déplacement vers une partition différente à l'aide des API de fractionnement et de fusion.
-    **Mappage d'objets** : Nous souhaitons conserver la commodité des mappages fournis par Dapper pour la traduction entre les classes dans l'application et les structures de base de données sous-jacentes. 

La section suivante fournit des instructions relatives aux exigences pour les applications basées sur **Dapper** et **DapperExtensions**.

## Conseils techniques
### Routage dépendant des données avec Dapper 

Avec Dapper, l'application est généralement responsable de la création et de l'ouverture des connexions à la base de données sous-jacente. Après avoir reçu un type T de l'application, Dapper renvoie les résultats de la requête sous forme de collections .NET de type T. Dapper effectue le mappage des lignes de résultat T-SQL aux objets de type T. De même, Dapper mappe les objets .NET en valeurs SQL ou en paramètres pour les instructions de langage de manipulation de données. Dapper offre cette fonctionnalité via les méthodes d'extension sur l'objet [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) ordinaire à partir des bibliothèques clientes ADO .NET SQL. La connexion SQL renvoyée par les API d'infrastructure élastique pour les DDR présente également des objets [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) ordinaires. Cela permet d'utiliser directement des extensions Dapper sur le type retourné par l'API DDR d'infrastructure élastique, étant donné que c'est également une connexion SQL Client simple.

Ces observations simplifient l'utilisation des connexions réparties par l'infrastructure élastique pour Dapper. 

Cet exemple de code (de l'exemple qui accompagne cet article) illustre l'approche où la clé de partitionnement est fournie par l'application à la bibliothèque d'infrastructure élastique pour attribuer la connexion à la partition appropriée.  

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1, 
                     connectionString: connStrBldr.ConnectionString, 
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }

L'appel à l'API [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) remplace la création et l'ouverture par défaut d'une connexion SQL Client. L'appel [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) accepte les arguments qui sont requis pour le routage dépendant des données via l'infrastructure élastique : 

-    la carte de partitions pour accéder aux interfaces de routage dépendant des données,
-    la clé de partition pour identifier le shardlet,
-    les informations d'identification (nom d'utilisateur et mot de passe) pour se connecter à la partition

L'objet de carte de partitions crée une connexion ouverte vers la partition hébergeant le shardlet pour la clé de partitionnement donnée. Les API de l'infrastructure élastique marquent également la connexion pour implémenter ses garanties de cohérence. Étant donné que l'appel à [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) retourne un objet de connexion SQL Client normal, l'appel suivant à la méthode d'extension **Execute** à partir de Dapper suit la pratique Dapper standard.

Les requêtes fonctionnent de la même façon : une première ouverture de la connexion a lieu à l'aide de [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) de l'API de l'infrastructure élastique. Vous utilisez ensuite les méthodes d'extension Dapper ordinaires pour mapper les résultats de votre requête SQL dans des objets .NET :

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate ))
    {    
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT * 
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }

Notez que le bloc **using** avec la connexion DDR de l'infrastructure élastique définit l'étendue de toutes les opérations de base de données dans le bloc à la seule partition où tenantId1 est conservé. La requête retourne uniquement les blogs stockés sur la partition actuelle et non ceux stockés sur les autres partitions. 

## Routage dépendant des données avec Dapper et DapperExtensions

Dapper est fourni avec un écosystème d'extensions supplémentaires qui peuvent fournir plus de commodité et d'abstraction à partir de la base de données lors du développement d'applications de base de données. DapperExtensions est un exemple. 

L'utilisation de DapperExtensions dans votre application ne change pas la gestion ni la création des connexions de base de données. Il incombe toujours à l'application d'ouvrir des connexions et les méthodes d'extension attendent des objets de connexion SQL Client ordinaires. Nous pouvons utiliser [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) comme indiqué ci-dessus. Les exemples de code suivants montrent que la seule différence est que nous n'avez plus à écrire les instructions T-SQL :

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

Voici l'exemple de code pour la requête : 

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }

### Gestion des erreurs temporaires

L'équipe Microsoft Patterns & Practices a publié le [bloc d'applications de gestion des erreurs temporaires](http://msdn.microsoft.com/library/hh680934.aspx) afin d'aider les développeurs d'applications à atténuer les erreurs transitoires courantes rencontrées lors de l'exécution dans le cloud. Pour plus d'informations, consultez [La persévérance, le secret de tous les succès : utilisation du bloc d'applications de gestion des erreurs temporaires](http://msdn.microsoft.com/library/dn440719.aspx).

L'exemple de code s'appuie sur la bibliothèque d'erreurs transitoires pour vous protéger contre les défaillances temporaires. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** du code ci-dessus est définie comme une **SqlDatabaseTransientErrorDetectionStrategy** avec un nombre de tentatives de 10 et un délai d'attente de 5 secondes entre chaque tentative. Si vous utilisez des transactions, assurez-vous que la portée de votre nouvelle tentative revient au début de la transaction en cas d'erreur transitoire.

## Limites

Les approches décrites dans ce document entraînent quelques limitations :

* L'exemple de code pour ce document ne montre pas comment gérer le schéma entre les partitions.
* Nous partons du principe que tous les traitements de base de données d'une demande donnée sont contenus dans une seule partition, identifiée par la clé de partitionnement fournie par la demande. Cependant, cette hypothèse n'est pas toujours valide, par exemple, lorsqu'il n'est pas possible de rendre une clé de partitionnement disponible. Pour résoudre ce problème, les bibliothèques d'infrastructure élastique fournissent la [classe MultiShardQuery](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). La classe implémente une abstraction de la connexion pour l'interrogation sur plusieurs partitions. Ce document ne permet pas d'apprendre à utiliser la MultiShardQuery conjointement à Dapper.

## Conclusion

Les applications utilisant **Dapper** et **DapperExtensions** peuvent facilement tirer parti de l'infrastructure élastique de base de données SQL Azure. À travers les étapes décrites dans ce document, ces applications peuvent utiliser la capacité d'infrastructure élastique pour le routage dépendant des données en modifiant la création et l'ouverture de nouveaux objets [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) pour utiliser l'appel [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) de la bibliothèque de l'infrastructure élastique. Cela limite les modifications au sein de votre application aux endroits où de nouvelles connexions sont créées et ouvertes. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png

<!--HONumber=47-->
