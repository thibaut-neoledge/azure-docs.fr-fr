<properties 
	pageTitle="Routage dépendant des données" 
	description="Utilisation de ShardMapManager pour le routage selon les données, une fonctionnalité de base de données élastique pour la base de données SQL Azure" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="sidneyh"/>

#Routage dépendant des données

La classe **ShardMapManager** permet aux applications ADO.NET de diriger facilement les requêtes et les commandes de base de données vers la base de données physique appropriée dans un environnement partitionné. Il s'agit du **routage dépendant des données** qui est un modèle fondamental lorsque vous travaillez avec des bases de données partitionnées. Chaque requête ou transaction spécifique d'une application utilisant le routage dépendant des données est limitée à l'accès à une seule base de données par demande.

À l'aide du routage dépendant des données, il est inutile que l'application effectue le suivi des diverses chaînes de connexion ou emplacements de base de données associés à différents segments de données dans l'environnement partitionné. À la place, le [Gestionnaire des cartes de partitions](sql-database-elastic-scale-shard-map-management.md) assume la responsabilité de la distribution des connexions ouvertes à la base de données appropriée, le cas échéant, en fonction des données de la carte de partitions et de la valeur de la clé de partitionnement qui est la cible de la demande de l'application. (Cette clé est généralement l'identificateur *customer_id*, *tenant_id* ou *date_key* ou tout autre identificateur spécifique qui est un paramètre fondamental de la demande de la base de données).

## Utilisation d’une classe ShardMapManager dans une application de routage dépendant des données 

Pour les applications utilisant le routage dépendant des données, une classe **ShardMapManager** doit être instanciée une fois par domaine d'application, lors de l'initialisation, à l'aide du répartiteur **GetSQLShardMapManager**.

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, 
                      ShardMapManagerLoadPolicy.Lazy);
    RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 

Dans cet exemple, à la fois une classe **ShardMapManager** et un objet **ShardMap** spécifique qu'elle contient sont initialisés.

Pour une application qui ne manipule pas la carte de partitions proprement dite, les informations d'identification utilisées dans la méthode de fabrique pour obtenir la classe **ShardMapManager** (dans l'exemple ci-dessus, *smmConnectionString*) doivent être des informations d'identification disposant des autorisations en lecture seule dans la base de données **Carte de partitions globale** référencée par la chaîne de connexion. Ces informations d'identification sont généralement différentes des informations d'identification utilisées pour ouvrir des connexions dans le gestionnaire des cartes de partitions. Voir également [Gestion des informations d’identification dans les bibliothèques clientes de la base de données élastique](sql-database-elastic-scale-manage-credentials.md).

## Appel du routage dépendant des données 

La méthode **ShardMap.OpenConnectionForKey(key, connectionString, connectionOptions)** retourne une connexion ADO.Net prête à émettre des commandes à la base de données appropriée en fonction de la valeur du paramètre **key**. Les informations de partitions sont mises en cache dans l'application par la classe **ShardMapManager**. De ce fait, ces demandes n'impliquent généralement pas une recherche de base de données par rapport à la base de données **Carte de partitions globale**.

* Le paramètre **key** est utilisé comme clé de recherche dans la carte de partitions afin de déterminer la base de données appropriée pour la demande. 

* L'instruction **connectionString** est utilisée pour transmettre uniquement les informations d'identification de l'utilisateur correspondant à la connexion de votre choix. Aucun nom de base de données ou de serveur n'est inclus dans l'instruction *connectionString* puisque la méthode détermine la base de données et le serveur à l'aide de l'objet **ShardMap**.

* L'instruction **connectionOptions** est utilisée pour indiquer si la validation a lieu ou non lors de la livraison de la connexion ouverte. L'instruction **ConnectionOptions.Validate** est recommandée. Dans un environnement où les cartes de partitions peuvent changer et les lignes peuvent se déplacer vers d'autres bases de données suite à des opérations de fractionnement ou de fusion, la validation garantit que la recherche mise en cache de la base de données selon une valeur de clé est toujours correcte. La validation implique une brève requête vers la carte de partitions locale sur la base de données cible (pas dans la carte de partitions globale) pour que la connexion soit accordée à l'application.

Si la validation par rapport à la carte de partitions locale échoue (indiquant que le cache est incorrect), le gestionnaire des cartes de partitions interroge la carte de partitions globale afin d'obtenir la nouvelle valeur correcte pour la recherche, de mettre à jour le cache et d'obtenir et retourner la connexion à la base de données appropriée.

La seule fois où l'instruction **ConnectionOptions.None** (ne pas valider) est acceptable se produit lorsque des modifications de la carte de partitions ne sont pas prévues alors qu'une application est en ligne. Dans ce cas, les valeurs en cache peuvent être considérées comme systématiquement correctes, et l'appel de validation aller-retour supplémentaire à la base de données cible peut être ignoré en toute sécurité. Cela peut réduire la latence de transaction et le trafic de la base de données. L'instruction **connectionOptions** peut également être définie par une valeur d'un fichier de configuration afin d'indiquer si des modifications de partitionnement sont prévues ou non pendant une période donnée.

Il s'agit d'un exemple de code qui utilise le gestionnaire des cartes de partitions pour effectuer le routage dépendant des données basé sur la valeur de clé de type entier **CustomerID**, à l'aide d'un objet **ShardMap** nommé **customerShardMap**.

## Exemple : routage dépendant des données 

    int customerId = 12345; 
    int newPersonId = 4321; 

    // Connection to the shard for that customer ID
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, 
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command 
        SqlCommand cmd = conn.CreateCommand(); 
        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 
    }  

Notez qu'au lieu d'utiliser un constructeur pour un objet **SqlConnection**, suivi d'un appel **Open()** à l'objet de connexion, la méthode **OpenConnectionForKey** est utilisée et elle offre une nouvelle connexion déjà ouverte à la base de données. Les connexions utilisées de cette manière tirent pleinement parti du regroupement de connexions ADO.Net. Tant que les transactions et les demandes peuvent être satisfaites une seule partition à la fois, il s'agit de la seule modification nécessaire dans une application utilisant déjà ADO.Net.

La méthode **OpenConnectionForKeyAsync** est également disponible si votre application utilise la programmation asynchrone avec ADO.Net. Son comportement est l’équivalent du routage dépendant des données de la méthode ADO.NET **Connection.OpenAsync**.

## Intégration de la gestion des erreurs temporaires 

Une meilleure pratique dans le développement d'applications d'accès aux données dans le cloud consiste à garantir que les erreurs temporaires de connexion ou d'interrogation de la base de données sont interceptées par l'application, et que les opérations sont retentées plusieurs fois avant de générer un message d'erreur. La gestion des erreurs temporaires pour les applications de cloud est traitée à la page [Gestion des erreurs temporaires](http://msdn.microsoft.com/library/dn440719(v=pandp.60).aspx).
 
La gestion des erreurs temporaires peut coexister naturellement avec le modèle de routage dépendant des données. La condition clé consiste à réessayer la demande d'accès aux données, notamment le bloc **using** qui a obtenu la connexion de routage dépendant des données. L'exemple précédent pourrait être réécrit comme suit (remarquez la modification mise en surbrillance).

### Exemple : routage dépendant des données avec gestion des erreurs temporaires 

<pre><code>int customerId = 12345 ; 
int newPersonId = 4321 ; 

<span style="background-color:  #FFFF00">Configuration.SqlRetryPolicy.ExecuteAction(() = ></span> 
<span style="background-color:  #FFFF00">    { </span>
        // Connexion à la partition pour cet ID de client 
        à l'aide de (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId,  
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
        { 
            // Exécuter une commande simple 
            SqlCommand cmd = conn.CreateCommand(); 

            cmd.CommandText = @"UPDATE Sales.Customer 
                            DÉFINIR PersonID = @newPersonID 
                            OÙ CustomerID = @customerID "; 

            cmd.Parameters.AddWithValue("@customerID", customerId); 
            cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
            cmd.ExecuteNonQuery(); 

            Console.WriteLine("Mise à jour teminée"); 
        } 
<span style="background-color:  #FFFF00">    }); </span> 
</code></pre>


Les packages nécessaires pour implémenter la gestion des erreurs temporaires sont automatiquement téléchargés lorsque vous générez l’exemple d’application de base de données élastique. Les packages sont également disponibles séparément dans [Enterprise Library - Bloc des applications de gestion des erreurs temporaires](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/). Utilisez la version 6.0 ou ultérieure.

## Cohérence transactionnelle 

Les propriétés transactionnelles sont garanties pour toutes les opérations locales sur une partition. Par exemple, les transactions soumises via le routage dépendant des données s'exécutent dans l'étendue de la partition cible pour la connexion. À ce stade, il n'existe aucune fonctionnalité fournie pour l'inscription de plusieurs connexions dans une transaction, et par conséquent, il n'existe aucune garantie transactionnelle lors des opérations effectuées sur les partitions.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=July15_HO2-->