<properties 
	pageTitle="Procédure de partitionnement des données dans DocumentDB avec le Kit de développement logiciel (SDK) | Microsoft Azure" 
	description="Découvrez comment utiliser le Kit de développement logiciel (SDK) .NET Azure DocumentDB pour partitionner des données et acheminer des demandes dans plusieurs collections" 
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/05/2015" 
	ms.author="arramac"/>

# Partitionnement des données dans DocumentDB avec le Kit de développement logiciel (SDK) .NET

Azure DocumentDB est un service de base de données de document qui vous permet de mettre à l’échelle votre compte en toute transparence grâce à l’approvisionnement de collections à l'aide des [Kits de développement logiciel (SDK)](https://msdn.microsoft.com/library/azure/dn781482.aspx) et des [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) (également appelé **partitionnement**). Afin de rendre le développement d’applications partitionnées plus facile et de réduire la quantité de code standard requis pour les tâches de partitionnement, nous avons ajouté des fonctionnalités dans le Kit de développement logiciel (SDK) .NET qui facilitent la génération d'applications mises à l’échelle entre plusieurs partitions.

Dans cet article, nous allons examiner les classes et les interfaces du Kit de développement (SDK) .NET et voir comment les utiliser pour développer des applications partitionnées.

## Partitionnement avec le Kit de développement logiciel (SDK) DocumentDB

Avant d’aller plus loin dans le partitionnement, nous allons revoir certains concepts DocumentDB fondamentaux liés au partitionnement. Un compte de base de données DocumentDB Azure est constitué d'un ensemble de bases de données. Chacune d'elles contient plusieurs collections, lesquelles comportent des procédures stockées, des déclencheurs, des UDF, des documents et des pièces jointes associées. Les collections peuvent être traitées comme des partitions dans DocumentDB et ont les propriétés suivantes :

- Les collections ne sont pas simplement des conteneurs logiques. Ce sont des partitions physiques Par conséquent, l'interrogation ou le traitement de documents se trouvant dans la même collection offre une optimisation de performance.
- Les transactions ACID, c’est-à-dire les procédures stockées et les déclencheurs, sont limitées aux collections.
- Les collections n'appliquent pas de schéma, ce qui permet de les utiliser pour les documents JSON de même type ou d’autres types.

Depuis la version [1\.1.0 du Kit de développement logiciel (SDK) .NET Azure DocumentDB](http://www.nuget.org/packages/Microsoft.Azure.DocumentDB/), vous pouvez effectuer les opérations de document directement dans une base de données. En interne, [DocumentClient](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) utilise la classe PartitionResolver que vous avez spécifiée pour la base de données pour acheminer les demandes vers la collection appropriée.

Chaque classe PartitionResolver est une implémentation concrète d'une interface [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx) disposant de trois méthodes : [GetPartitionKey](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.getpartitionkey.aspx), [ResolveForCreate](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforcreate.aspx) et [ResolveForRead](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforread.aspx). Les requêtes LINQ et les itérateurs ReadFeed utilisent la méthode ResolveForRead en interne pour effectuer une itération sur toutes les collections qui correspondent à la clé de partition de la demande. De même, la création d’opérations nécessite la méthode ResolveForCreate pour acheminer les créations dans la partition appropriée. Aucune modification n’est requise pour les méthodes Replace, Delete et Read puisqu’elles utilisent des documents contenant déjà la référence à la collection correspondante.

Le Kit de développement logiciel (SDK) inclut également deux classes qui prennent en charge les deux techniques de partitionnement canoniques, les recherches de hachage et de plage, via les méthodes [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) et [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx). L’utilisation de ces classes vous permet d'ajouter facilement la logique de partitionnement à votre application.

## Ajout d’une logique de partitionnement et enregistrement de la classe PartitionResolver 

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

## Création de documents dans une partition  

Une fois que la méthode PartitionResolver est enregistrée, vous pouvez effectuer des créations et des requêtes directement dans la base de données, comme illustré ci-dessous. Dans cet exemple, le Kit de développement logiciel (SDK) utilise la méthode PartitionResolver pour extraire et hacher l’UserId puis utiliser cette valeur pour acheminer l'opération de création dans la collection appropriée.

```cs
Document johnDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("J1", "@John", Region.UnitedStatesEast));
Document ryanDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("U4", "@Ryan", Region.AsiaPacific, UserStatus.AppearAway));
```

## Création de requêtes dans les partitions  

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

## Création de requêtes dans toutes les collections de la base de données 

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

## Programme de résolution de partition par hachage
Avec le partitionnement par hachage, les partitions sont affectées en fonction de la valeur d'une fonction de hachage, ce qui vous permet de répartir uniformément les demandes et les données dans un certain nombre de partitions. Cette méthode est généralement utilisée pour partitionner des données produites ou consommées par un grand nombre de clients distincts. Elle est utile pour stocker des profils utilisateur, des éléments de catalogue et des données de télémétrie IoT (Internet des objets).

**Partitionnement par hachage :** ![Diagramme illustrant la répartition uniforme des demandes entre les partitions avec le partitionnement par hachage](media/documentdb-sharding/partition-hash.png "Partitionnement par hachage")

Un modèle de partitionnement par hachage simple dans *N* collections consisterait à prendre n'importe quel document et à calculer *hash(d) mod N* pour déterminer dans quelle collection il se trouve. Mais le problème avec cette technique simple est qu'elle ne fonctionne pas correctement lorsque vous ajoutez de nouvelles collections ou que vous supprimez des collections car cela nécessiterait que presque toutes les données soient relues de façon aléatoire. Le [hachage cohérent](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.23.3738) est un algorithme connu qui répond à ce problème en implémentant un modèle de hachage qui limite le nombre de déplacements de données requis lors de l'ajout ou de la suppression de collections.

La classe [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) implémente une logique pour générer un anneau de hachage cohérent sur la fonction de hachage spécifiée dans l’interface [IHashGenerator](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.ihashgenerator.aspx). Par défaut, la classe HashPartitionResolver utilise une fonction de hachage MD5, mais vous pouvez remplacer celle-ci par votre propre implémentation de hachage. La classe HashPartitionResolver crée les 16 hachages ou « nœuds virtuels » en interne dans l'anneau de hachage pour chaque collection afin d'obtenir une distribution uniforme de documents entre les collections, mais vous pouvez modifier ce chiffre pour compenser la différence de données avec le calcul côté client.

**Hachage cohérent avec HashPartitionResolver :** ![Diagramme illustrant comment HashPartitionResolver crée un anneau de hachage](media/documentdb-sharding/HashPartitionResolver.JPG "Hachage cohérent")

## Programme de résolution de la partition de plage

Dans le partitionnement par plage, l'affectation des partitions est fonction de la présence de la clé de partition dans une certaine plage. Cette méthode est couramment utilisée pour le partitionnement avec des propriétés d'horodatage (par exemple, eventTime entre le 1er avril 2015 et le 14 avril 2015). La classe [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx) vous permet d'assurer un mappage entre une classe Range<T> et le lien réflexif de la collection.

[Range<T>](https://msdn.microsoft.com/library/azure/mt126048.aspx) est une classe simple gérant des plages de tout type qui implémente IComparable<T> et IEquatable<T> telles que des chaînes ou des nombres. Pour les lectures et les créations, vous pouvez passer à n'importe quelle plage arbitraire et le programme de résolution identifie toutes les collections candidates en identifiant les plages de partitions qui se recoupent avec la plage demandée. Cette fonctionnalité peut être utile lors de l'exécution des requêtes de plage dans les données de série chronologique.

**Partitionnement par plage :**

![Diagramme illustrant la répartition uniforme des demandes entre les partitions avec le partitionnement par plage](media/documentdb-sharding/partition-range.png "Partitionnement par plage")

Un cas particulier de partitionnement par plage se présente lorsque la plage est simplement une valeur discrète unique, parfois appelée « partitionnement par recherche ». Cette méthode est couramment utilisée pour le partitionnement par région (par exemple, la partition pour la Scandinavie contient la Norvège, le Danemark et la Suède) ou pour le partitionnement par client dans une application avec plusieurs clients.

## Exemples 

Consultez le [projet Github d’exemples de partitionnement DocumentDB](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning) contenant des extraits de code relatif à l'utilisation de ces PartitionResolvers et étendez-les pour implémenter vos propres programmes de résolution en fonction des utilisations spécifiques, comme suit :

* Comment indiquer une expression lambda arbitraire pour GetPartitionKey et l'utiliser pour implémenter des clés de partition composées ou pour partitionner autrement les différents types d'objets.
* Comment créer une simple classe [LookupPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/LookupPartitionResolver.cs) qui utilise une table de recherche manuelle pour effectuer le partitionnement. Ce modèle est généralement utilisé pour le partitionnement basé sur des valeurs discrètes telles que la région, l'ID de client ou le nom de l’application.
* Comment créer une [ManagedPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/ManagedHashPartitionResolver.cs) qui crée automatiquement des collections basées sur un modèle qui définit un modèle d’appellation, IndexingPolicy, et des procédures stockées qui doivent être enregistrées dans les nouvelles collections.
* Comment créer une classe[SpilloverPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/SpilloverPartitionResolver.cs) sans modèle qui crée simplement de nouvelles collections lorsque les anciennes collections sont pleines.
* Comment sérialiser et désérialiser votre état PartitionResolver au format JSON afin que vous puissiez partager entre des processus et des arrêts. Vous pouvez conserver ces éléments dans des fichiers de configuration ou même dans une collection DocumentDB.
* Une classe [DocumentClientHashPartitioningManager](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Util/DocumentClientHashPartitioningManager.cs) classe pour l’ajout ou la suppression dynamique de partitions d'une base de données partitionnée en fonction du hachage cohérent. Elle utilise une classe[TransitionHashPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/TransitionHashPartitionResolver.cs) en interne pour acheminer des lectures et des écritures au cours de la migration à l'aide d'un des quatre modes : lecture à partir de l’ancien modèle de partitionnement (ReadCurrent), du nouveau (ReadNext), fusion des résultats des deux (ReadBoth) ou indisponibilité pendant la migration (None).

Les exemples sont open source et nous vous invitons à envoyer des requêtes d'extraction avec des contributions qui permettront aux autres développeurs DocumentDB de les exploiter. Veuillez vous reporter aux [instructions de contribution](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) pour obtenir des conseils sur la façon de contribuer.

>[AZURE.NOTE]DocumentDB impose une vitesse de création de collections limite. Par conséquent, certains exemples de méthodes montrés ici peuvent prendre quelques minutes avant de s’achever.

##Forum Aux Questions
**Pourquoi DocumentDB prend en charge le partitionnement côté client au lieu du partitionnement côté serveur ?**

DocumentDB prend en charge le partitionnement côté client pour plusieurs raisons :

- Il est très difficile de reprendre le concept d'une collection auprès des développeurs sans compromettre une des trois garanties d’indexation/d’interrogation cohérente, de haute disponibilité et de transaction ACID. 
- Les bases de données de document nécessitent souvent une flexibilité en termes de définition de stratégies de partitionnement, ce qu’une approche côté serveur n’est pas forcément en mesure de faire. 

**Pourquoi le partitionnement n’est pas pris en charge sur d'autres plateformes (Node.js, Java ou Python) ?**

Nous allons développer progressivement la prise en charge du partitionnement sur d'autres plateformes en fonction des commentaires des clients du Kit de développement logiciel (SDK) .NET.

**Comment puis-je ajouter ou supprimer une collection à mon modèle de partitionnement ?**

Consultez l'implémentation de DocumentClientHashPartitioningManager dans les exemples de projet pour obtenir un exemple d’implémentation de repartitionnement.

**Comment puis-je conserver ou partager ma configuration de partitionnement avec d'autres clients ?**

Vous pouvez sérialiser l'état du partitionneur au format JSON et le stocker dans des fichiers de configuration ou même dans des collections DocumentDB. Consultez la méthode RunSerializeDeserializeSample dans les exemples de projet pour obtenir un exemple.

**Comment puis-je associer différentes techniques de partitionnement ?**

Vous pouvez associer PartitionResolvers en implémentant votre propre IPartitionResolver qui utilise en interne un ou plusieurs programmes de résolution en interne. Consultez TransitionHashPartitionResolver dans les exemples de projet pour obtenir un exemple.

##Références
* [Partitionnement des exemples de code sur Github](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning)
* [Partitionnement des données avec les concepts DocumentDB](documentdb-partition-data.md)
* [Collection DocumentDB et niveaux de performance](documentdb-performance-levels.md)
* [Documentation du Kit de développement logiciel (SDK) DocumentDB .NET sur MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [Exemples .NET DocumentDB](https://github.com/Azure/azure-documentdb-net)
* [Limites de DocumentDB](documentdb-limits.md)
* [Blog de DocumentDB avec des conseils relatifs aux performances](http://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)
 

<!---HONumber=Oct15_HO2-->