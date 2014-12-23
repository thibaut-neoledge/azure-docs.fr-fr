<properties title="Managing Elastic Scale Credentials" pageTitle="Gestion des informations d'identification de l'infrastructure élastique" description="How to set the right level of credentials, admin to read-only, for Elastic Scale apps." metaKeywords="Azure SQL Database, elastic scale, about user credentials in elastic scale" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Gestion des informations d'identification de l'infrastructure élastique  

Les [API clientes de l'infrastructure élastique](http://go.microsoft.com/?linkid=9862605) utilisent les informations d'identification pour différents types d'opération, en particulier la création ou la manipulation d'un [gestionnaire des cartes de partitions](http://go.microsoft.com/?linkid=9862595), en faisant référence à un gestionnaire des cartes de partitions existant pour obtenir des informations sur les partitions et se connecter à ces dernières. Les informations d'identification pour ces types d'opération sont décrites ci-dessous. 


* **Gestion des informations d'identification pour l'accès aux cartes de partitions** : les informations d'identification de gestion sont utilisées lors de l'instanciation d'un objet **ShardMapManager** pour les applications qui manipulent les cartes de partitions. L'utilisateur des API de l'infrastructure élastique doit créer les utilisateurs et les connexions SQL nécessaires et s'assurer qu'ils sont dotés des autorisations en lecture/écriture pour la base de données de cartes de partitions globale et pour toutes les bases de données de partitions. Ces informations d'identification sont utilisées pour mettre à jour la carte de partitions globale et les cartes de partitions locales lorsque des modifications sont apportées à la carte de partitions. Par exemple, utilisez les informations d'identification de gestion pour instancier l'objet de gestionnaire des cartes de partitions, comme le montre le code suivant : 

        // Obtain a shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmAdminConnectionString, 
                ShardMapManagerLoadPolicy.Lazy 
        ); 


     La variable **smmAdminConnectionString** est une chaîne de connexion qui comporte les informations d'identification de gestion. L'identifiant utilisateur et le mot de passe fournissent l'accès en lecture/écriture à la base de données de cartes de partitions et aux partitions individuelles. La chaîne de connexion de gestion inclut également le nom du serveur et le nom de la base de données afin d'identifier la base de données de cartes de partitions globale. Voici une chaîne de connexion classique à cet effet :

        "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" 

* **Informations d'identification de l'utilisateur pour l'accès au gestionnaire des cartes de partitions** :  lorsque vous instanciez le gestionnaire des cartes de partitions dans une application qui ne va pas gérer les cartes de partitions, utilisez les informations d'identification dotées des autorisations en lecture seule pour la carte de partitions globale. Les informations extraites de la carte de partitions globale avec ces informations d'identification sont utilisées pour le [routage dépendant des données](./sql-database-elastic-scale-data-dependent-routing.md) et pour remplir le cache de la carte de partitions sur le client. Les informations d'identification sont fournies via le même modèle d'appel à **GetSqlShardMapManager** que celui indiqué ci-dessus : 
 
        // Obtain shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmReadOnlyConnectionString, 
                ShardMapManagerLoadPolicy.Lazy
        );  

     Notez l'utilisation de **smmReadOnlyConnectionString** afin de refléter l'utilisation d'informations d'identification différentes pour l'accès des utilisateurs **non-administrateurs**. Ces informations d'identification ne doivent pas fournir d'autorisations d'écriture sur la carte de partitions globale. 

* **Informations d'identification de l'utilisateur pour l'accès aux données utilisateur** : des informations d'identification supplémentaires sont requises lorsque vous utilisez la méthode **OpenConnectionForKey** pour accéder à une partition associée à une clé. Ces informations d'identification doivent fournir des autorisations d'accès en lecture seule aux tables de la carte de partitions locale résidant sur la partition. Cela est nécessaire pour effectuer une validation de connexion pour le routage dépendant des données sur la partition. L'extrait de code suivant illustre l'utilisation des informations d'identification de l'utilisateur pour l'accès aux données utilisateur dans le contexte du routage dépendant des données : 
 
        using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
        targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

    Dans cet exemple, **smmUserConnectionString** comporte la chaîne de connexion correspondant aux informations d'identification de l'utilisateur. Pour la base de données SQL Azure, voici une chaîne de connexion classique pour les informations d'identification de l'utilisateur : 

        "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;"  

    Notez que la chaîne de connexion ne comporte pas de nom de serveur et de nom de base de données. En effet, l'appel de **OpenConnectionForKey** dirige automatiquement la connexion à la partition appropriée en fonction de la clé. Par conséquent, il n'est pas nécessaire de fournir les noms de la base de données et du serveur. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
