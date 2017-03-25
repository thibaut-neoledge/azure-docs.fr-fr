---
title: "Unités de requête et estimation du débit - Azure DocumentDB | Microsoft Docs"
description: "Découvrez les besoins en unités de requête dans DocumentDB, et comment les spécifier et les estimer."
services: documentdb
author: syamkmsft
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: d0a3c310-eb63-4e45-8122-b7724095c32f
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: syamk
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b098e3087cb08528c5fbdc2d0d768ce40e7ffe0d
ms.lasthandoff: 03/15/2017


---
# <a name="request-units-in-documentdb"></a>Unités de requête dans DocumentDB
Désormais disponible : DocumentDB [calculatrice d’unités de demande](https://www.documentdb.com/capacityplanner). Pour en savoir plus, consultez [Estimation des besoins de débit](documentdb-request-units.md#estimating-throughput-needs).

![Calculatrice de débit][5]

## <a name="introduction"></a>Introduction
[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) est un service de base de données NoSQL entièrement géré et évolutif pour les documents JSON. Avec DocumentDB, il n’est pas nécessaire de louer des machines virtuelles, de déployer des logiciels ou de surveiller les bases de données. DocumentDB est surveillé en continu par les ingénieurs Microsoft afin d'offrir une disponibilité, des performances et une protection des données optimales. Dans DocumentDB, les données sont stockées au sein de collections, qui sont des conteneurs flexibles hautement disponibles. Plutôt que de vous soucier de la gestion des ressources matérielles comme le processeur, la mémoire et les E/S par seconde d’une collection, vous pouvez réserver un débit en termes de requêtes par seconde. DocumentDB gère automatiquement l’approvisionnement, le partitionnement transparent et la mise à l’échelle de votre collection pour traiter le nombre de requêtes approvisionnées. 

DocumentDB prend en charge un certain nombre d’API pour les opérations de lecture et d’écriture, les requêtes et les exécutions de procédures stockées. Toutes les requêtes n’étant pas égales, la quantité normalisée **d’unités de requête** qui leur est affectée est fonction de la quantité de calcul requise pour traiter chaque requête. Le nombre d’unités de requête d’une opération est déterministe. Dans DocumentDB, vous pouvez suivre le nombre d’unités de requête consommées par une opération via un en-tête de réponse.

Dans DocumentDB, chaque collection peut être réservée avec un débit, également exprimé en termes d’unités de requête. Il s’agit de blocs de 100 unités de demande (RU) par seconde, allant de quelques centaines à plusieurs millions d’unités de requête par seconde. Le débit prévu peut être ajusté tout au long de la durée de vie d’une collection pour s’adapter aux besoins de traitement et aux modèles d’accès changeants de votre application. 

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :  

* Que sont les unités de requête et les frais de requête ?
* Comment spécifier la capacité d’unités de requête pour une collection ?
* Comment estimer les besoins en unités de requête de mon application ?
* Que se passe-t-il si je dépasse la capacité d’unités de requête pour une collection ?

## <a name="request-units-and-request-charges"></a>Unités de requête et frais de requête
DocumentDB et l’API pour MongoDB offrent des performances élevées et prévisibles en *réservant* des ressources pour répondre aux besoins de débit de votre application.  Étant donné que les schémas d’accès et de charge des applications changent au fil du temps, DocumentDB vous permet de facilement augmenter ou diminuer la quantité de débit réservé disponible pour votre application.

Avec DocumentDB, un débit réservé est spécifié en termes de traitement d’unités de requête par seconde.  On peut considérer les unités de requête un peu comme une devise de débit : vous *réservez* une quantité d’unités de requête garantie accessible par seconde à votre application.  Chaque opération dans DocumentDB (écriture d’un document, exécution d’une requête, mise à jour d’un document) consomme des ressources de processeur, de mémoire et d’E/S.  Autrement dit, chaque opération entraîne des *frais de requête*, exprimés en *unités de requête*.  Comprendre les facteurs qui ont un impact sur les frais d’unités de requête et cerner les demandes de débit de votre application vous permettent d’exécuter votre application de la manière la plus rentable possible. 

Nous vous recommandons de commencer par visionner la vidéo suivante, dans laquelle Aravind Ramachandran explique les unités de requête et les performances prévisibles avec DocumentDB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Predictable-Performance-with-DocumentDB/player]
> 
> 

## <a name="specifying-request-unit-capacity-in-documentdb"></a>Spécification de la capacité d’unités de requête dans DocumentDB
Quand vous créez une collection DocumentDB, vous spécifiez le nombre d’unités de requête (RU par seconde) à réserver pour la collection. En fonction du débit configuré, DocumentDB alloue des partitions physiques pour héberger votre collection et répartit/rééquilibre les données sur les partitions à mesure qu’elles se développent.

DocumentDB nécessite la spécification d’une clé de partition lorsqu’une collection est configurée avec 10 000 unités de requête ou plus. Une clé de partition est également requise ultérieurement pour mettre à l’échelle le débit de votre collection au-delà de 10 000 unités de requête. Il est donc fortement recommandé de configurer une [clé de partition](documentdb-partition-data.md) lors de la création d’une collection, quel que soit le débit initial. Dans la mesure où les données doivent parfois être réparties sur plusieurs partitions, vous devez choisir une clé de partition ayant une cardinalité élevée (de plusieurs centaines à plusieurs millions de valeurs distinctes) de manière à ce que DocumentDB puisse mettre à l’échelle votre collection et vos requêtes de manière uniforme. 

> [!NOTE]
> Une clé de partition est une limite logique et non physique. Il n’est donc pas nécessaire de limiter le nombre de valeurs de clé de partition distinctes. En fait, il vaut mieux avoir trop de valeurs de clé de partition distinctes que pas assez, car DocumentDB offre plus d’options d’équilibrage de charge.

L’extrait de code suivant permet de créer une collection avec 3 000 unités de requête par seconde à l’aide du SDK .NET :

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

DocumentDB fonctionne sur un modèle de réservation du débit. Autrement dit, vous êtes facturé pour la quantité de débit *réservée* pour la collection, quelle que soit la quantité activement *utilisée*. À mesure que les modèles d’utilisation, de données et de charge de votre application évoluent, vous pouvez facilement augmenter ou réduire la quantité d’unités réservées par le biais des Kits de développement logiciel (SDK) DocumentDB ou à l’aide du [Portail Azure](https://portal.azure.com).

Chaque collection est mappée sur une ressource `Offer` dans DocumentDB, qui contient des métadonnées sur le débit configuré pour la collection. Vous pouvez modifier le débit alloué pour une collection en recherchant la ressource correspondante, et en mettant à jour la valeur de débit. L’extrait de code suivant permet de changer le débit d’une collection en 5 000 unités de requête par seconde à l’aide du SDK .NET :

```csharp
// Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

La modification du débit n’a aucun impact sur la disponibilité de votre collection. Le nouveau débit réservé est généralement effectif quelques secondes après son application.

## <a name="specifying-request-unit-capacity-in-api-for-mongodb"></a>Spécification de la capacité d’unités de requête dans l’API pour MongoDB
L’API pour MongoDB vous permet de spécifier le nombre d’unités de requête (RU par seconde) à réserver pour la collection.

L’API pour MongoDB fonctionne sur le même modèle de réservation basé sur le débit que DocumentDB. Autrement dit, vous êtes facturé pour la quantité de débit *réservée* pour la collection, quelle que soit la quantité activement *utilisée*. À mesure que les modèles d’utilisation, de données et de charge de votre application évoluent, vous pouvez facilement augmenter ou réduire la quantité d’unités de requête réservées à l’aide du [portail Azure](https://portal.azure.com).

La modification du débit n’a aucun impact sur la disponibilité de votre collection. Le nouveau débit réservé est généralement effectif quelques secondes après son application.

## <a name="request-unit-considerations"></a>Considérations relatives aux unités de requête
Quand vous évaluez le nombre d’unités de requête à réserver pour votre collection DocumentDB, vous devez impérativement tenir compte des variables suivantes :

* **Taille des documents**. Plus la taille du document est grande, plus le nombre d'unités consommées pour lire ou écrire des données augmente.
* **Nombre de propriétés de documents**. En supposant que toutes les propriétés sont indexées par défaut, le nombre d'unités consommées pour écrire un document augmente parallèlement au nombre de propriétés.
* **Cohérence des données**. Quand vous utilisez les niveaux de cohérence des données Strong (Fort) ou Bounded Staleness (En fonction de l'obsolescence), des unités supplémentaires sont consommées pour lire les documents.
* **Propriétés indexées**. Une stratégie d'indexation sur chaque collection détermine quelles propriétés sont indexées par défaut. Vous pouvez réduire la consommation d’unités de requête en limitant le nombre de propriétés indexées ou en activant l’indexation différée.
* **Indexation des documents**. Par défaut, chaque document est automatiquement indexé. Vous consommerez moins d'unités de requête en choisissant de ne pas indexer certains documents.
* **Modèles de requête**. La complexité d’une requête a un impact sur le nombre d’unités de requête consommées pour une opération. Le nombre de prédicats, la nature des prédicats, les projections, le nombre de fonctions définies par l’utilisateur et la taille du jeu de données source ont tous une influence sur le coût des opérations de requête.
* **Utilisation des scripts**.  Comme avec les requêtes, les procédures stockées et les déclencheurs consomment plus ou moins d’unités de requête en fonction de la complexité des opérations effectuées. Pendant le développement de votre application, inspectez l'en-tête des frais de requêtes pour mieux comprendre de quelle façon chaque opération consomme la capacité des unités de requête.

## <a name="estimating-throughput-needs"></a>Estimation des besoins de débit
Une unité de requête est une mesure normalisée du coût de traitement de la requête. Une unité de requête représente la capacité de traitement nécessaire pour lire (par le biais d’un élément self link ou id) un seul document JSON de 1 Ko composé de 10 valeurs de propriété uniques (à l’exclusion des propriétés système). Une demande de création (insertion), de remplacement ou de suppression du même document nécessite un plus grand traitement de la part du service et consomme donc plus d’unités de requête.   

> [!NOTE]
> La ligne de base d’une unité de requête pour un document de 1 Ko correspond à une opération GET simple par l’élément self link ou id du document.
> 
> 

Par exemple, voici un tableau qui indique le nombre d’unités de demande à fournir pour trois tailles de documents (1 Ko, 4 Ko et 64 Ko) et à deux niveaux de performances (500 lectures par seconde + 100 écritures par seconde et 500 lectures par seconde + 500 écritures par seconde). La cohérence des données a été configurée au niveau de la session et la stratégie d’indexation a été définie sur None.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Taille des documents</strong></p></td>
            <td valign="top"><p><strong>Lectures par seconde</strong></p></td>
            <td valign="top"><p><strong>Écritures par seconde</strong></p></td>
            <td valign="top"><p><strong>Unités de demande</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 Ko</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *1) + (100* 5) = 1 000 UR/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 Ko</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *5) + (100* 5) = 3 000 UR/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 Ko</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *1,3) + (100* 7) = 1 350 UR/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 Ko</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *1,3) + (500* 7) = 4 150 UR/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 Ko</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *10) + (100* 48) = 9 800 UR/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 Ko</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *10) + (500* 48) = 29 000 UR/s</p></td>
        </tr>
    </tbody>
</table>

### <a name="use-the-request-unit-calculator"></a>Utiliser la calculatrice d’unités de demande
Pour aider les clients à affiner leurs estimations de débit, il existe une [calculatrice d’unités de demande](https://www.documentdb.com/capacityplanner) sur le web qui permet d’estimer les besoins d’unité de demande pour les opérations courantes, notamment :

* Créations (écritures) de documents
* Lectures de documents
* Suppressions de documents
* Mises à jour de documents

L’outil inclut également la prise en charge de l’estimation des besoins de stockage de données en fonction des exemples de documents que vous fournissez.

L’utilisation de l’outil est simple :

1. Chargez un ou plusieurs documents JSON représentatifs.
   
    ![Charger les documents vers la calculatrice d’unités de demande][2]
2. Pour estimer les besoins de stockage, entrez le nombre total de documents que vous voulez stocker.
3. Entrez le nombre d’opérations de création, lecture, mise à jour et suppression de documents dont vous avez besoin (par seconde). Pour estimer les frais d’unités de requête des opérations de mise à jour de documents, chargez une copie du document de l’étape 1 comprenant des mises à jour de champs types.  Par exemple, si les mises à jour de document comprennent généralement la modification de deux propriétés nommées lastLogi et userVisits, copiez le document, mettez à jour les valeurs de ces deux propriétés, puis chargez le document copié.
   
    ![Entrez les exigences de débit dans la calculatrice d’unités de demande][3]
4. Cliquez sur Calculer et examinez les résultats.
   
    ![Résultats de la calculatrice d’unités de demande][4]

> [!NOTE]
> Si vous avez des types de documents qui varient considérablement en termes de taille et de nombre de propriétés indexées, chargez un exemple de chaque *type* de document standard dans l’outil, puis calculez les résultats.
> 
> 

### <a name="use-the-documentdb-request-charge-response-header"></a>Utiliser l’en-tête de réponse de frais de requête DocumentDB
Chaque réponse du service DocumentDB inclut un en-tête personnalisé (`x-ms-request-charge`) qui contient le nombre d’unités de requête consommées pour la requête. Cet en-tête est également accessible par le biais des Kits de développement logiciel (SDK) DocumentDB. Dans le Kit de développement logiciel (SDK) .NET, RequestCharge est une propriété de l’objet ResourceResponse.  Pour les requêtes, l’Explorateur de requête DocumentDB dans le portail Azure fournit des informations sur les frais de requête pour les requêtes exécutées.

![Examen des frais d’unités de requête dans l’Explorateur de requête][1]

Ainsi, une méthode permettant d’estimer la quantité de débit réservé requis par votre application consiste à enregistrer les frais d’unité de requête associés à l’exécution des opérations courantes sur un document représentatif utilisé par votre application, puis à évaluer le nombre d’opérations que vous prévoyez d’effectuer chaque seconde.  Veillez à mesurer et à inclure également les requêtes courantes et l’utilisation des scripts DocumentDB.

> [!NOTE]
> Si vous avez des types de documents qui varient considérablement en termes de taille et de nombre de propriétés indexées, enregistrez les frais d’unités de requête d’opérations applicables associés à chaque *type* de document par défaut.
> 
> 

Par exemple :

1. Enregistrer les frais d’unités de requête de création (insertion) d’un document par défaut. 
2. Enregistrer les frais d’unités de requête de lecture d’un document par défaut.
3. Enregistrer les frais d’unités de requête de mise à jour d’un document par défaut.
4. Enregistrer les frais d’unités de requête des requêtes de documents standard courantes.
5. Enregistrer les frais d’unités de requête des scripts personnalisés (procédures stockées, déclencheurs, fonctions définies par l’utilisateur) utilisés par l’application.
6. Calculer les unités de requête nécessaires étant donné l’estimation du nombre d’exécutions d’opérations prévues chaque seconde.

### <a id="GetLastRequestStatistics"></a>Utiliser la commande GetLastRequestStatistics de l’API pour MongoDB
L’API pour MongoDB prend en charge une commande personnalisée, *getLastRequestStatistics*, pour récupérer les frais de requête des opérations spécifiées.

Par exemple, dans l’interpréteur de commandes Mongo, exécutez l’opération dont vous souhaitez vérifier les frais de demande.
```
> db.sample.find()
```

Ensuite, exécutez la commande *getLastRequestStatistics*.
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

Ainsi, une méthode permettant d’estimer la quantité de débit réservé requis par votre application consiste à enregistrer les frais d’unité de requête associés à l’exécution des opérations courantes sur un document représentatif utilisé par votre application, puis à évaluer le nombre d’opérations que vous prévoyez d’effectuer chaque seconde.

> [!NOTE]
> Si vous avez des types de documents qui varient considérablement en termes de taille et de nombre de propriétés indexées, enregistrez les frais d’unités de requête d’opérations applicables associés à chaque *type* de document par défaut.
> 
> 

## <a name="use-api-for-mongodbs-portal-metrics"></a>Utiliser les mesures du portail de l’API pour MongoDB
La méthode la plus simple pour obtenir une estimation correcte des frais d’unité de requête pour votre base de données API pour MongoDB consiste à utiliser les mesures du [portail Azure](https://portal.azure.com). Grâce aux graphiques *Nombre de demandes* et *Frais de demande*, vous pouvez obtenir une estimation du nombre d’unités de requête consommées par chaque opération et par les opérations les unes par rapport aux autres.

![Mesures du portail de l’API pour MongoDB][6]

## <a name="a-request-unit-estimation-example"></a>Exemple d’estimation d’unités de requête
Considérez le document suivant d’environ&1; Ko :

```json
{
 "id": "08259",
  "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  "tags": [
    {
      "name": "cereals ready-to-eat"
    },
    {
      "name": "kellogg"
    },
    {
      "name": "kellogg's crispix"
    }
  ],
  "version": 1,
  "commonName": "Includes USDA Commodity B855",
  "manufacturerName": "Kellogg, Co.",
  "isFromSurvey": false,
  "foodGroup": "Breakfast Cereals",
  "nutrients": [
    {
      "id": "262",
      "description": "Caffeine",
      "nutritionValue": 0,
      "units": "mg"
    },
    {
      "id": "307",
      "description": "Sodium, Na",
      "nutritionValue": 611,
      "units": "mg"
    },
    {
      "id": "309",
      "description": "Zinc, Zn",
      "nutritionValue": 5.2,
      "units": "mg"
    }
  ],
  "servings": [
    {
      "amount": 1,
      "description": "cup (1 NLEA serving)",
      "weightInGrams": 29
    }
  ]
}
```

> [!NOTE]
> Les documents étant minimisés dans DocumentDB, la taille du document ci-dessus calculée par le système est légèrement inférieure à 1 Ko.
> 
> 

Le tableau suivant montre les frais d’unités de requête approximatifs pour les opérations courantes sur ce document (les frais d’unités de requête approximatifs partent du principe que le niveau de cohérence du compte a comme valeur « Session » et que tous les documents sont indexés automatiquement) :

| Opération | Frais d’unités de requête |
| --- | --- |
| Créer le document |~15 unités de requête |
| Lire le document |~1 unité de requête |
| Interroger le document par id |~2,5 unités de requête |

En outre, ce tableau montre les frais d’unités de requête approximatifs pour les requêtes courantes utilisées dans l’application :

| Requête | Frais d’unités de requête | # de documents retournés |
| --- | --- | --- |
| Sélectionner un aliment par id |~2,5 unités de requête |1 |
| Sélectionner un aliment par fabricant |~7 unités de requête |7 |
| Sélectionner par groupe d’aliments et trier par poids |~70 unités de requête |100 |
| Sélectionner les 10 premiers aliments dans un groupe d’aliments |~10 unités de requête |10 |

> [!NOTE]
> Les frais d’unités de requête varient en fonction du nombre de documents retournés.
> 
> 

Avec ces informations, nous pouvons estimer les besoins en unités de requête pour cette application étant donné le nombre d’opérations et de requêtes attendues par seconde :

| Opération/Requête | Nombre estimé par seconde | Unités de requête nécessaires |
| --- | --- | --- |
| Créer le document |10 |150 |
| Lire le document |100 |100 |
| Sélectionner un aliment par fabricant |25 |175 |
| Sélectionner par groupe d’aliments |10 |700 |
| Sélectionner les 10 premiers |15 |150 Total |

Dans ce cas, nous estimons l’exigence de débit moyen à 1275 unités de requête par seconde.  Arrondi à la centaine la plus proche, nous devons approvisionner 1 300 unités de requête par seconde pour la collection de cette application.

## <a id="RequestRateTooLarge"></a> Dépassement des limites de débit réservé dans DocumentDB
Souvenez-vous que la consommation d’unités de requête est évaluée en fonction d’un taux par seconde. Pour les applications qui dépassent le taux d’unités de requête configuré pour une collection, les requêtes pour cette collection sont limitées jusqu’à ce que le taux tombe sous le niveau réservé. En cas de limitation, le serveur met fin à la demande de manière préventive avec RequestRateTooLargeException (code d’état HTTP 429) et il retourne l’en-tête x-ms-retry-after-ms indiquant la durée, en millisecondes, pendant laquelle l’utilisateur doit attendre avant de réessayer.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Si vous utilisez des requêtes LINQ et le SDK .NET Client, la plupart du temps vous ne devez jamais traiter cette exception, car la version actuelle du SDK .NET Client intercepte implicitement cette réponse, respecte l’en-tête retry-after spécifié par le serveur, et réessaie d’effectuer la demande. La tentative suivante réussira toujours, sauf si plusieurs clients accèdent simultanément à votre compte.

Si vous avez plusieurs clients qui opèrent en même temps au-delà du taux de requêtes, le comportement par défaut peut ne pas suffire, et le client générera dans l’application une exception DocumentClientException avec le code d’état 429. Dans ce cas, vous pourriez traiter le comportement et la logique de nouvelles tentatives dans les routines de gestion d’erreurs de votre application ou accroître le débit réservé pour la collection.

## <a id="RequestRateTooLargeAPIforMongoDB"></a> Dépassement des limites de débit réservé dans l’API pour MongoDB
Les applications qui dépassent le nombre d’unités de requête configuré pour une collection seront limitées jusqu’à ce que le taux tombe sous le niveau réservé. En cas de limitation, le serveur principal interrompra la demande de manière préventive avec un code d’erreur *16500* indiquant un *trop grand nombre de demandes*. Par défaut, l’API pour MongoDB réessaiera automatiquement jusqu’à 10 fois avant de renvoyer un code d’erreur indiquant un *trop grand nombre de demandes*. Si vous recevez de nombreux codes d’erreur indiquant un *trop grand nombre de demandes*, vous pouvez ajouter le comportement de nouvelles tentatives dans les routines de gestion d’erreurs de votre application ou [accroître le débit réservé pour la collection](documentdb-set-throughput.md).

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur le débit réservé avec les bases de données Azure DocumentDB, explorez ces ressources :

* [Tarification de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/)
* [Modélisation des données dans DocumentDB](documentdb-modeling-data.md)
* [Niveaux de performances dans DocumentDB](documentdb-partition-data.md)

Pour en savoir plus sur DocumentDB, consultez la [documentation](https://azure.microsoft.com/documentation/services/documentdb/)Azure DocumentDB. 

Pour commencer avec le test des performances et de la mise à l’échelle avec DocumentDB, consultez [Test des performances et de la mise à l’échelle avec Azure DocumentDB](documentdb-performance-testing.md).

[1]: ./media/documentdb-request-units/queryexplorer.png 
[2]: ./media/documentdb-request-units/RUEstimatorUpload.png
[3]: ./media/documentdb-request-units/RUEstimatorDocuments.png
[4]: ./media/documentdb-request-units/RUEstimatorResults.png
[5]: ./media/documentdb-request-units/RUCalculator2.png
[6]: ./media/documentdb-request-units/api-for-mongodb-metrics.png

