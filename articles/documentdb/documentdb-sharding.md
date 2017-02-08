---

redirect_url: https://azure.microsoft.com/services/documentdb/
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: d59ebef3cda36ba048b844f0cd2326fff66b4aa5
ms.openlocfilehash: d0a616d27c653e8d3749d93f565f5e8616fdf909



---
# <a name="how-to-partition-data-using-client-side-support-in-documentdb"></a>Comment partitionner des données à l’aide de la prise en charge côté client dans DocumentDB
Azure DocumentDB prend en charge le [partitionnement automatique des collections](documentdb-partition-data.md). Toutefois, il existe des cas d'usage où il est intéressant de disposer d'un contrôle plus fin sur le comportement du partitionnement. Afin de réduire la quantité de code réutilisable requis pour les tâches de partitionnement, nous avons ajouté des fonctionnalités dans les Kits de développement logiciel (SDK) Java, .NET et Node.js, qui optimisent la génération d’applications mises à l’échelle entre plusieurs collections.

Dans cet article, nous allons examiner les classes et les interfaces du Kit de développement (SDK) .NET et voir comment les utiliser pour développer des applications partitionnées. D’autres kits de développement logiciel (SDK) tels que Java, Node.js et Python prennent en charge des méthodes et des interfaces similaires pour le partitionnement côté client.

## <a name="client-side-partitioning-with-the-documentdb-sdk"></a>Partitionnement côté client avec le Kit de développement logiciel (SDK) DocumentDB
Avant d’aller plus loin dans le partitionnement, nous allons revoir certains concepts DocumentDB fondamentaux liés au partitionnement. Un compte de base de données DocumentDB Azure est constitué d'un ensemble de bases de données. Chacune d'elles contient plusieurs collections, lesquelles comportent des procédures stockées, des déclencheurs, des UDF, des documents et des pièces jointes associées. Les collections peuvent être des partitions uniques ou elles peuvent être partitionnées elles-mêmes et avoir les propriétés suivantes :

* Les collections offrent un isolement des performances. Par conséquent, vous pouvez améliorer les performances en regroupant les documents similaires dans la même collection. Par exemple, pour les données chronologiques, vous pouvez placer les données du mois dernier, qui sont fréquemment demandées, dans une collection avec un débit approvisionné plus élevé, tandis que les anciennes données sont placées dans des collections avec un débit approvisionné faible.
* Les transactions ACID, c’est-à-dire les procédures stockées et les déclencheurs, ne peuvent pas couvrir une collection. Les transactions sont limitées à une seule valeur de clé de partition au sein d'une collection.
* Les collections n'appliquent pas de schéma, ce qui permet de les utiliser pour les documents JSON de même type ou d’autres types.

Depuis la version [1.5.x des Kits de développement logiciel (SDK) Azure DocumentDB](documentdb-sdk-dotnet.md), vous pouvez effectuer les opérations de document directement dans une base de données. En interne, [DocumentClient](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) utilise la classe PartitionResolver que vous avez spécifiée pour la base de données pour acheminer les demandes vers la collection appropriée.

> [!NOTE]
> Le [partitionnement côté serveur](documentdb-partition-data.md) introduit dans l’API REST 2015-12-16 et les Kits de développement logiciel (SDK) à partir de la version 1.6.0 déconseille l’approche de résolution de la partition côté client pour des cas d’utilisation plus simples. Toutefois, le partitionnement côté client est plus flexible et vous permet de contrôler l’isolation des performances entre les clés de partition, de contrôler le degré de parallélisme lors de la lecture des résultats de plusieurs partitions et d’utiliser des approches de partitionnement spatial/plage au lieu du hachage.
> 
> 

Par exemple, dans .NET, chaque classe PartitionResolver est une implémentation concrète d’une interface [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx) disposant de trois méthodes : [GetPartitionKey](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.getpartitionkey.aspx), [ResolveForCreate](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforcreate.aspx) et [ResolveForRead](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforread.aspx). Les requêtes LINQ et les itérateurs ReadFeed utilisent la méthode ResolveForRead en interne pour effectuer une itération sur toutes les collections qui correspondent à la clé de partition de la demande. De même, la création d’opérations nécessite la méthode ResolveForCreate pour acheminer les créations dans la partition appropriée. Aucune modification n’est requise pour les méthodes Replace, Delete et Read puisqu’elles utilisent des documents contenant déjà la référence à la collection correspondante.

Les Kits de développement logiciel (SDK) incluent également deux classes qui prennent en charge les deux techniques de partitionnement canoniques, les recherches de hachage et de plage, par le biais des méthodes [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) et [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx). L’utilisation de ces classes vous permet d'ajouter facilement la logique de partitionnement à votre application.  

## <a name="add-partitioning-logic-and-register-the-partitionresolver"></a>Ajout d’une logique de partitionnement et enregistrement de  la classe PartitionResolver
Voici un extrait de code montrant comment créer une méthode [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) et l’enregistrer avec le DocumentClient d’une base de données.

```cs
// Create some collections to partition data.
DocumentCollection collection1 = await client.CreateDocumentCollectionAsync(...);
DocumentCollection collection2 = await client.CreateDocumentCollectionAsync(...);

// Initialize a HashPartitionResolver using the "UserId" property and the two collection self-links.
HashPartitionResolver hashResolver = new HashPartitionResolver(
    u => ((UserProfile)u).UserId, 
    new string[] { collection1.SelfLink, collection2.SelfLink });

// Register the PartitionResolver with the database.
this.client.PartitionResolvers[database.SelfLink] = hashResolver;

```

## <a name="create-documents-in-a-partition"></a>Création de documents dans une partition
Une fois que la méthode PartitionResolver est enregistrée, vous pouvez effectuer des créations et des requêtes directement dans la base de données, comme illustré ci-dessous. Dans cet exemple, le Kit de développement logiciel (SDK) utilise la méthode PartitionResolver pour extraire et hacher l’UserId puis utiliser cette valeur pour acheminer l'opération de création dans la collection appropriée.

```cs
Document johnDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("J1", "@John", Region.UnitedStatesEast));
Document ryanDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("U4", "@Ryan", Region.AsiaPacific, UserStatus.AppearAway));
```

## <a name="create-queries-against-partitions"></a>Création de requêtes dans les partitions
Vous pouvez interroger à l'aide de la méthode [CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) en passant par la base de données et une clé de partition. La requête retourne un ensemble de résultats unique pour toutes les collections de la base de données qui mappe vers la clé de partition.  

```cs
// Query for John's document by ID - uses PartitionResolver to restrict the query to the partitions 
// containing @John. Again the query uses the database self link, and relies on the hash resolver 
// to route the appropriate collection.
var query = this.client.CreateDocumentQuery<UserProfile>(
    database.SelfLink, null, partitionResolver.GetPartitionKey(johnProfile))
    .Where(u => u.UserName == "@John");
johnProfile = query.AsEnumerable().FirstOrDefault();
```

## <a name="create-queries-against-all-collections-in-the-database"></a>Création de requêtes dans toutes les collections de la base de données
Vous pouvez également interroger toutes les collections de la base de données et énumérer les résultats comme indiqué ci-dessous, en ignorant l'argument de la clé de partition.

```cs
// Query for all "Available" users. Here since there is no partition key, the query is serially executed 
// across each partition/collection and returns a single result-set. 
query = this.client.CreateDocumentQuery<UserProfile>(database.SelfLink)
    .Where(u => u.Status == UserStatus.Available);
foreach (UserProfile activeUser in query)
{
    Console.WriteLine(activeUser);
}
```

## <a name="hash-partition-resolver"></a>Programme de résolution de partition par hachage
Avec le partitionnement par hachage, les partitions sont affectées en fonction de la valeur d'une fonction de hachage, ce qui vous permet de répartir uniformément les demandes et les données dans un certain nombre de partitions. Cette méthode est généralement utilisée pour partitionner des données produites ou consommées par un grand nombre de clients distincts. Elle est utile pour stocker des profils utilisateur, des éléments de catalogue et des données de télémétrie IoT (Internet des objets). Le partitionnement par hachage est également utilisé par le support de partitionnement côté serveur de DocumentDB dans une collection.

**Partitionnement par hachage :**
![ diagramme illustrant la répartition uniforme des demandes entre les partitions avec le partitionnement par hachage](media/documentdb-sharding/partition-hash.png)

Un modèle de partitionnement par hachage simple dans *N* collections consisterait à prendre n’importe quel document et à calculer *hash(d) mod N* pour déterminer dans quelle collection il se trouve. Mais le problème avec cette technique simple est qu'elle ne fonctionne pas correctement lorsque vous ajoutez de nouvelles collections ou que vous supprimez des collections car cela nécessiterait que presque toutes les données soient relues de façon aléatoire. [hachage cohérent](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.23.3738) est un algorithme connu qui répond à ce problème en implémentant un modèle de hachage qui limite le nombre de déplacements de données requis lors de l'ajout ou de la suppression de collections.

La classe [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) implémente une logique pour générer un anneau de hachage cohérent sur la fonction de hachage spécifiée dans l’interface [IHashGenerator](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.ihashgenerator.aspx). Par défaut, la classe HashPartitionResolver utilise une fonction de hachage MD5, mais vous pouvez remplacer celle-ci par votre propre implémentation de hachage. La classe HashPartitionResolver crée les 16 hachages ou « nœuds virtuels » en interne dans l'anneau de hachage pour chaque collection afin d'obtenir une distribution uniforme de documents entre les collections, mais vous pouvez modifier ce chiffre pour compenser la différence de données avec le calcul côté client.

**Hachage cohérent avec HashPartitionResolver :**
![ diagramme illustrant comment HashPartitionResolver crée un anneau de hachage](media/documentdb-sharding/HashPartitionResolver.JPG)

## <a name="range-partition-resolver"></a>Programme de résolution de la partition de plage
Dans le partitionnement par plage, l'affectation des partitions est fonction de la présence de la clé de partition dans une certaine plage. Cette méthode est couramment utilisée pour le partitionnement avec des propriétés d'horodatage (par exemple, eventTime entre le 1er avril 2015 et le 14 avril 2015). La classe [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx) vous permet d’assurer un mappage entre une classe Range\<T\> et le lien réflexif de la collection. 

[Range\<T\>](https://msdn.microsoft.com/library/azure/mt126048.aspx) est une classe simple gérant des plages de tout type qui implémente IComparable\<T\> et IEquatable\<T\> telles que des chaînes ou des nombres. Pour les lectures et les créations, vous pouvez passer à n'importe quelle plage arbitraire et le programme de résolution identifie toutes les collections candidates en identifiant les plages de partitions qui se recoupent avec la plage demandée. Cette fonctionnalité peut être utile lors de l'exécution des requêtes de plage dans les données de série chronologique.

**Partitionnement par plage :**  

![ Diagramme illustrant la répartition uniforme des demandes entre les partitions avec le partitionnement par plage](media/documentdb-sharding/partition-range.png)  

Un cas particulier de partitionnement par plage se présente lorsque la plage est simplement une valeur discrète unique, parfois appelée « partitionnement par recherche ». Cette méthode est couramment utilisée pour le partitionnement par région (par exemple, la partition pour la Scandinavie contient la Norvège, le Danemark et la Suède) ou pour le partitionnement par client dans une application avec plusieurs clients.

## <a name="samples"></a>Exemples
Consultez le [projet GitHub d’exemples de partitionnement DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning) contenant des extraits de code relatifs à l’utilisation de ces PartitionResolvers et étendez-les pour implémenter vos propres programmes de résolution en fonction des utilisations spécifiques, comme suit : 

* Comment indiquer une expression lambda arbitraire pour GetPartitionKey et l'utiliser pour implémenter des clés de partition composées ou pour partitionner autrement les différents types d'objets.
* Comment créer une simple classe [LookupPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/LookupPartitionResolver.cs) qui utilise une table de recherche manuelle pour effectuer le partitionnement. Ce modèle est généralement utilisé pour le partitionnement basé sur des valeurs discrètes telles que la région, l'ID de client ou le nom de l’application.
* Comment créer une [ManagedPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/ManagedHashPartitionResolver.cs) qui crée automatiquement des collections basées sur un modèle qui définit un modèle d’appellation, IndexingPolicy, et des procédures stockées qui doivent être enregistrées dans les nouvelles collections.
* Comment créer une classe [SpilloverPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/SpilloverPartitionResolver.cs) sans modèle qui crée simplement de nouvelles collections lorsque les anciennes collections sont pleines.
* Comment sérialiser et désérialiser votre état PartitionResolver au format JSON afin que vous puissiez partager entre des processus et des arrêts. Vous pouvez conserver ces éléments dans des fichiers de configuration ou même dans une collection DocumentDB.
* Une classe [DocumentClientHashPartitioningManager](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Util/DocumentClientHashPartitioningManager.cs) classe pour l’ajout ou la suppression dynamique de partitions d'une base de données partitionnée en fonction du hachage cohérent. Elle utilise une classe [TransitionHashPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/TransitionHashPartitionResolver.cs) en interne pour acheminer des lectures et des écritures au cours de la migration à l'aide d'un des quatre modes : lecture à partir de l’ancien modèle de partitionnement (ReadCurrent), du nouveau (ReadNext), fusion des résultats des deux (ReadBoth) ou indisponibilité pendant la migration (None).

Les exemples sont open source et nous vous invitons à envoyer des requêtes d'extraction avec des contributions qui permettront aux autres développeurs DocumentDB de les exploiter. Veuillez vous reporter aux [instructions de contribution](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) pour obtenir des conseils sur la façon de contribuer.  

> [!NOTE]
> DocumentDB impose une vitesse de création de collections limite. Par conséquent, certains exemples de méthodes montrés ici peuvent prendre quelques minutes avant de s’achever.
> 
> 

## <a name="faq"></a>Forum Aux Questions
**DocumentDB prend-il en charge le partitionnement côté serveur ?**

Oui, DocumentDB prend en charge le [partitionnement côté serveur](documentdb-partition-data.md). DocumentDB prend également en charge le partitionnement côté client via des programmes de résolution de partition côté client pour les cas d'usage plus complexes.

**Quand dois-je utiliser le partitionnement côté serveur ou côté client ?**
Dans la majorité des cas d’usage, nous recommandons l’utilisation du partitionnement côté serveur car il gère les tâches d’administration du partitionnement des données et du routage des requêtes. Toutefois, si vous avez besoin d'un partitionnement par plage ou êtes confronté à un cas d'usage spécialisé pour l'isolement des performances entre les différentes valeurs de clés de partition, le partitionnement côté client peut être préférable.

**Comment puis-je ajouter ou supprimer une collection à mon modèle de partitionnement ?**

Consultez l'implémentation de DocumentClientHashPartitioningManager dans les exemples de projet pour obtenir un exemple d’implémentation de repartitionnement.

**Comment puis-je conserver ou partager ma configuration de partitionnement avec d'autres clients ?**

Vous pouvez sérialiser l'état du partitionneur au format JSON et le stocker dans des fichiers de configuration ou même dans des collections DocumentDB. Consultez la méthode RunSerializeDeserializeSample dans les exemples de projet pour obtenir un exemple.

**Comment puis-je associer différentes techniques de partitionnement ?**

Vous pouvez associer PartitionResolvers en implémentant votre propre IPartitionResolver qui utilise en interne un ou plusieurs programmes de résolution en interne. Consultez TransitionHashPartitionResolver dans les exemples de projet pour obtenir un exemple.

## <a name="references"></a>Références
* [Partitionnement côté serveur dans DocumentDB](documentdb-partition-data.md)
* [Collection DocumentDB et niveaux de performance](documentdb-performance-levels.md)
* [Partitionnement des exemples de code sur Github](https://github.com/Azure/azure-documentdb-dotnet/tree/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning)
* [Documentation du Kit de développement logiciel (SDK) DocumentDB .NET sur MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [Exemples .NET DocumentDB](https://github.com/Azure/azure-documentdb-net)
* [Blog de DocumentDB avec des conseils relatifs aux performances](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)




<!--HONumber=Feb17_HO1-->


