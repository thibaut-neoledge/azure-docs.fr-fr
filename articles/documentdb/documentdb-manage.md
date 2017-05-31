---
redirect_url: https://azure.microsoft.com/services/cosmos-db/
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b9c8ef0343259d5a0adb2cefa9063f9d86edbc3c
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="storage-and-predictable-performance-provisioning-in-azure-cosmos-db"></a>Stockage et approvisionnement des performances prévisibles dans Azure Cosmos DB
Azure Cosmos DB est un service de base de données NoSQL orienté documents, entièrement géré et évolutif pour les documents JSON. Avec Azure Cosmos DB, il n’est pas nécessaire de louer des machines virtuelles, de déployer des logiciels ou de surveiller les bases de données. Azure Cosmos DB est utilisé et surveillé en continu par les ingénieurs Microsoft afin d’offrir une disponibilité, des performances et une protection des données optimales.  

Vous pouvez prendre en main Azure Cosmos DB en [créant un compte de base de données](documentdb-create-account.md), puis en créant une [collection et une base de données Azure Cosmos DB](documentdb-create-collection.md) via le [portail Azure](https://portal.azure.com/). Les bases de données Azure Cosmos DB sont proposées sous forme d’unités de stockage et de débit SSD. Ces unités de stockage sont configurées lorsque vous créez des collections de base de données, chaque collection avec un débit réservé pouvant être mise à l’échelle vers le haut ou vers le bas à tout moment pour répondre aux besoins de votre application.

Si votre application dépasse le débit réservé d'une ou plusieurs collections, les demandes sont limitées en fonction de chaque collection. Cela signifie que certaines demandes d'application peuvent réussir tandis que d'autres peuvent être limitées.

Cet article fournit une vue d'ensemble des ressources et métriques disponibles pour gérer la capacité et planifier le stockage de données.

## <a name="database-account"></a>Compte de base de données
En tant qu’abonné Azure, vous pouvez approvisionner un ou plusieurs comptes de base de données Azure Cosmos DB pour gérer vos ressources de base de données. Chaque abonnement est associé à un seul abonnement Azure.

Vous pouvez créer des comptes Azure Cosmos DB à partir du [portail Azure](documentdb-create-account.md) ou à l’aide d’un [modèle ARM ou de l’interface de ligne de commande Azure](documentdb-automation-resource-manager-cli.md).

## <a name="databases"></a>Bases de données
Une base de données Azure Cosmos DB peut contenir un volume presque illimité de stockage de documents regroupés en collections. Les collections assurent l’isolement des performances : chaque collection peut être configurée avec un débit qui n’est pas partagé avec les autres collections dans la même base de données ou le même compte. La taille d’une base de données Azure Cosmos DB est flexible : elle peut aller de quelques Go à plusieurs téraoctets de stockage de documents SSD et de débit configuré. Contrairement à une base de données de SGBDR classique, une base de données Azure Cosmos DB n’est pas étendue à un seul ordinateur et peut s’étaler sur plusieurs machines.  

Avec Azure Cosmos DB, lorsque vous devez mettre à l’échelle vos applications, vous pouvez créer d’autres collections et/ou bases de données. Les bases de données peuvent être créées via le [portail Azure](documentdb-create-database.md) ou l’un des [Kits de développement logiciel (SDK) Azure Cosmos DB](documentdb-dotnet-samples.md).   

## <a name="database-collections"></a>Collections de base de données
Chaque base de données Azure Cosmos DB peut contenir une ou plusieurs collections. Les collections font office de partitions de données hautement disponibles pour le traitement et le stockage de documents. Chaque collection peut stocker des documents avec un schéma hétérogène. Les capacités de requête et d’indexation automatique d’Azure Cosmos DB vous permettent de filtrer et de récupérer facilement des documents. Une collection fournit l'étendue du stockage de documents et de l'exécution de requêtes. Il sert également de domaine de transaction pour tous les documents qu'elle contient. Les collections se voient affecter un débit en fonction de la valeur définie dans le portail Azure ou via les kits de développement logiciel (SDK).

Les collections sont automatiquement partitionnées en un ou plusieurs serveurs physiques par Azure Cosmos DB. Lorsque vous créez une collection, vous pouvez spécifier le débit approvisionné en unités de demande par seconde et définir une propriété de clé de partition. La valeur de cette propriété est utilisée par Azure Cosmos DB pour distribuer des documents entre les différentes partitions et acheminer les demandes sous la forme de requêtes. La valeur de la clé de partition sert également de limite de transaction pour les procédures stockées et les déclencheurs. Chaque collection a un volume réservé de débit qui n’est pas partagé avec les autres collections du même compte. Vous pouvez donc étendre votre application aussi bien en termes de débit que de stockage.

Les collections peuvent être créées via le [portail Azure](documentdb-create-collection.md) ou l’un des [Kits de développement logiciel (SDK) DocumentDB](documentdb-sdk-dotnet.md).   

## <a name="request-units-and-database-operations"></a>Unités de demande et opérations de base de données
Lorsque vous créez une collection, vous réservez un débit en termes [d’unités de requête (RU)](documentdb-request-units.md) par seconde. Plutôt que de vous soucier de la gestion des ressources matérielles, vous pouvez considérer une **unité de demande** comme une mesure unique des ressources nécessaires à l'exécution des opérations de base de données et à la réponse à une demande de l'application. Une lecture de 1 Ko consomme 1 RU, quel que soit le nombre d’éléments stockés dans la collection ou le nombre de demandes simultanées en cours d’exécution. Toutes les demandes DocumentDB, y compris les opérations complexes comme les requêtes SQL, ont une valeur RU prévisible qui peut être déterminée au moment du développement. Si vous connaissez la taille de vos documents et la fréquence de chaque opération (lecture, écriture et requête) à prendre en charge pour votre application, vous pouvez configurer la quantité exacte de débit nécessaire pour répondre aux besoins de votre application et adapter la taille de votre base de données en fonction des performances.

Chaque collection peut être réservée avec un débit par blocs de 100 unités de demande (RU) par seconde, quelques centaines ou plusieurs millions d’unités de demande par seconde. Le débit prévu peut être ajusté tout au long de la durée de vie d’une collection pour s’adapter aux besoins de traitement et aux modèles d’accès changeants de votre application. Pour plus d’informations, consultez les [niveaux de performances d’Azure Cosmos DB](documentdb-performance-levels.md).

> [!IMPORTANT]
> Les collections sont des entités facturables. Le coût est déterminé par le débit défini pour la collection mesuré en unités de demande par seconde, avec le stockage total consommé en gigaoctets.
>
>

Combien d’unités de demande une opération comme l’insertion, la suppression, la requête ou l’exécution d’une procédure stockée consomme-t-elle ? Une unité de requête est une mesure normalisée du coût de traitement de la requête. La lecture d’un document de 1 ko correspond à une 1 RU, mais une demande d’insertion, de remplacement ou de suppression du même document nécessite un plus grand traitement de la part du service et consomme donc plus d’unités de demande. Chaque réponse du service inclut un en-tête personnalisé (`x-ms-request-charge`) qui indique le nombre d’unités de demande consommées pour la demande. Cet en-tête est également accessible via les [Kits de développement logiciel (SDK)](documentdb-sdk-dotnet.md). Dans le Kit de développement logiciel (SDK) .NET, [RequestCharge](https://msdn.microsoft.com/library/azure/dn933057.aspx#P:Microsoft.Azure.Documents.Client.ResourceResponse`1.RequestCharge) est une propriété de l’objet [ResourceResponse](https://msdn.microsoft.com/library/azure/dn799209.aspx). Si vous voulez estimer vos besoins de débit avant d’effectuer un appel unique, vous pouvez utiliser le [Capacity planner](documentdb-request-units.md#estimating-throughput-needs) pour faciliter le calcul de cette estimation.

> [!NOTE]
> La base de 1 unité de demande pour un document de 1 ko correspond à une opération GET simple du document avec [cohérence de session](documentdb-consistency-levels.md).
>
>

Plusieurs facteurs déterminent le nombre d’unités de requête consommées par une opération effectuée dans un compte de base de données Azure Cosmos DB. Exemples de facteurs :

* Taille du document. Plus la taille du document est grande, plus le nombre d'unités consommées pour lire ou écrire des données augmente.
* Nombre de propriétés. En supposant que toutes les propriétés sont indexées par défaut, le nombre d'unités consommées pour écrire un document augmente parallèlement au nombre de propriétés.
* Cohérence des données. Quand vous utilisez les niveaux de cohérence des données Strong (Fort) ou Bounded Staleness (En fonction de l'obsolescence), des unités supplémentaires sont consommées pour lire les documents.
* Propriétés indexées. Une stratégie d'indexation sur chaque collection détermine quelles propriétés sont indexées par défaut. Vous pouvez réduire la consommation d'unités de demande en limitant le nombre de propriétés indexées.
* Indexation des documents. Par défaut, chaque document est automatiquement indexé. Vous consommerez moins d'unités de requête en choisissant de ne pas indexer certains documents.

Pour plus d’informations, consultez [Azure Cosmos DB request units](documentdb-request-units.md) (Unités de requête Azure Cosmos DB).

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
            <td valign="top"><p>(500 * 1) + (100 * 5) = 1 000 UR/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 Ko</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 5) + (100 * 5) = 3 000 UR/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 Ko</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1.3) + (100 * 7) = 1 350 UR/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 Ko</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1.3) + (500 * 7) = 4 150 UR/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 Ko</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 10) + (100 * 48) = 9 800 UR/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 Ko</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 10) + (500 * 48) = 29 000 UR/s</p></td>
        </tr>
    </tbody>
</table>

Les requêtes, procédures stockées et déclencheurs consomment plus ou moins d'unités de demande selon la complexité des opérations effectuées. Pendant le développement de votre application, inspectez l'en-tête des frais de requêtes pour mieux comprendre de quelle façon chaque opération consomme la capacité des unités de requête.  

## <a name="choice-of-consistency-level-and-throughput"></a>Choix du niveau de cohérence et débit
Le choix du niveau de cohérence par défaut a une incidence sur le débit et la latence. Vous pouvez définir le niveau de cohérence par défaut par programme ou via le portail Azure. Vous pouvez également remplacer le niveau de cohérence par demande. Par défaut, le niveau de cohérence est défini sur **Session**qui fournit des lectures/écritures unitones et lit vos garanties en écriture. Le niveau de cohérence Session (Par session) est idéal pour les applications centrées sur les utilisateurs et fournit un équilibre idéal entre cohérence et performances.    

Pour obtenir des instructions sur la modification de votre niveau de cohérence dans le portail Azure, consultez la section [Gestion d’un compte Azure Cosmos DB](documentdb-manage-account.md#consistency). Ou, pour plus d'informations sur les niveaux de cohérence, consultez la rubrique [Utilisation des niveaux de cohérence](documentdb-consistency-levels.md).

## <a name="provisioned-document-storage-and-index-overhead"></a>Stockage de documents approvisionné et surcharge d'index
Azure Cosmos DB prend en charge la création de partitions uniques et de collections partitionnées. Chaque partition de Azure Cosmos DB prend en charge jusqu’à 10 Go de stockage SSD. Ce stockage inclut les documents, plus le stockage pour l'index. Par défaut, une collection Azure Cosmos DB est configurée de manière à indexer automatiquement tous les documents sans demander explicitement d’index ou de schémas secondaires. En fonction des applications qui utilisent Azure Cosmos DB, la surcharge d’index standard est comprise entre 2 et 20 %. La technologie d’indexation utilisée par Azure Cosmos DB permet de garantir que, indépendamment des valeurs des propriétés, la surcharge d’index ne dépasse pas 80 % de la taille des documents avec les paramètres par défaut.

Par défaut, tous les documents sont automatiquement indexés par Azure Cosmos DB. Toutefois, si vous voulez affiner le réglage de la surcharge d’index, vous pouvez choisir de supprimer certains documents de l’indexation au moment de l’insertion ou du remplacement d’un document, comme décrit dans la rubrique [Stratégies d’indexation d’Azure Cosmos DB](documentdb-indexing-policies.md). Vous pouvez configurer une collection Azure Cosmos DB de manière à exclure tous les documents de la collection de l’indexation. Vous pouvez également configurer une collection Azure Cosmos DB de manière à ce qu’elle indexe de manière sélective uniquement certaines propriétés ou certains chemins d’accès avec des caractères génériques de vos documents JSON, comme décrit dans la rubrique [Configuration de la stratégie d’indexation d’une collection](documentdb-indexing-policies.md#CustomizingIndexingPolicy). L'exclusion de propriétés ou documents permet également d'améliorer le débit en écriture, ce qui signifie que vous allez consommer moins d'unités de demande.   

## <a name="next-steps"></a>Étapes suivantes
Pour poursuivre l’apprentissage sur le fonctionnement d’Azure Cosmos DB, consultez [Partitionnement et mise à l’échelle dans Azure Cosmos DB](documentdb-partition-data.md).

Pour obtenir des instructions sur la surveillance des niveaux de performances dans le portail Azure, consultez la rubrique [Surveillance d’un compte Azure Cosmos DB](documentdb-monitor-accounts.md). Pour plus d’informations sur le choix des niveaux de performances des collections, consultez les [niveaux de performances d’Azure Cosmos DB](documentdb-performance-levels.md).

