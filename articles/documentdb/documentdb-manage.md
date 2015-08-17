<properties 
	pageTitle="Gestion des capacités de DocumentDB | Microsoft Azure" 
	description="Découvrez comment mettre à l’échelle DocumentDB pour répondre aux besoins de capacité de votre application." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/27/2015" 
	ms.author="mimig"/>

# Gestion des besoins de capacité de DocumentDB
DocumentDB est un service de base de données NoSQL orienté documents, entièrement géré et évolutif. Avec DocumentDB, il n'est pas nécessaire de louer des machines virtuelles, de déployer des logiciels, de surveiller les bases de données ou de se soucier de la récupération d'urgence. DocumentDB est surveillé en continu par les ingénieurs Microsoft afin d'offrir une disponibilité, des performances et une protection des données optimales.

Vous pouvez prendre en main DocumentDB en [créant un compte de base de données](documentdb-create-account.md) via le [portail Azure en version préliminaire](https://portal.azure.com/). DocumentDB est proposé sous forme d’unités de stockage et de débit SSD. Ces unités sont configurées en créant des collections de base de données dans votre compte de base de données. Chaque collection comprend 10 Go de stockage de base de données avec un débit réservé. Si les exigences de débit de votre application changent, vous pouvez le modifier de façon dynamique en définissant le [niveau de performances](documentdb-performance-levels.md) pour chaque collection.

Lorsque votre application dépasse les niveaux de performances d'une ou plusieurs collections, les demandes sont limitées en fonction de chaque collection. Cela signifie que certaines demandes d'application peuvent réussir tandis que d'autres peuvent être limitées.

## Compte de base de données et ressources d'administration
En tant qu'abonné Azure, vous pouvez approvisionner un ou plusieurs comptes de base de données DocumentDB. Chaque compte de base de données offre un quota de ressources d'administration, avec des bases de données, des utilisateurs et des autorisations. Ces ressources sont soumises à des [quotas et des limites](documentdb-limits.md). Si vous avez besoin de ressources d'administration supplémentaires, [contactez le support technique](documentdb-increase-limits.md).

## Bases de données avec stockage de documents illimité
Une seule base de données DocumentDB peut contenir un volume presque illimité de stockage de documents partitionné par collections. Les collections forment les domaines de transaction des documents qu'elles contiennent. La taille d’une base de données DocumentDB est flexible : elle peut aller de quelques Go à des téraoctets de stockage de documents SSD et de débit configuré. Contrairement à une base de données de SGBDR classique, une base de données DocumentDB n'est pas étendue à un seul ordinateur.

Avec DocumentDB, lorsque vous devez mettre à l’échelle vos applications, vous pouvez créer d’autres collections et/ou bases de données. Plusieurs applications de Microsoft utilisent DocumentDB à l’échelle du client en créant des bases de données DocumentDB très volumineuses, contenant chacune des centaines ou des milliers de collections regroupant des téraoctets de stockage de documents. Vous pouvez augmenter ou réduire la taille d'une base de données en ajoutant ou en supprimant des collections pour répondre aux besoins d'extensibilité de vos applications.

## Collections de base de données
Chaque base de données DocumentDB peut contenir une ou plusieurs collections. Les collections font office de partitions de données hautement disponibles pour le traitement et le stockage de documents. Chaque collection peut stocker des documents avec un schéma hétérogène. Les capacités de requête et d’indexation automatique de DocumentDB vous permettent de filtrer et de récupérer facilement des documents. Une collection fournit l'étendue du stockage de documents et de l'exécution de requêtes. Il sert également de domaine de transaction pour tous les documents qu'elle contient. Un débit est alloué aux collections en fonction de leur niveau de performances. Il peut être ajusté dynamiquement via le portail Azure en version préliminaire ou l’un des Kits de développement logiciel (SDK).

Vous pouvez créer un nombre quelconque de collections pour répondre aux besoins de vos applications en termes de stockage et de débit. Les collections peuvent être créées via le [portail Azure en version préliminaire](https://portal.azure.com/) ou l’un des [Kits de développement logiciel (SDK) DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx).

>[AZURE.NOTE]Chaque collection prend en charge le stockage de 10 Go de données de document.
 
## Unités de demande et opérations de base de données
DocumentDB propose un riche ensemble d'opérations de base de données, dont les requêtes hiérarchiques et relationnelles avec les fonctions définies par l'utilisateur, les procédures stockées et les déclencheurs, qui fonctionnent tous au niveau des documents d'une collection de base de données. Le coût de traitement associé à chacune de ces opérations varie selon le processeur, les E/S et la mémoire nécessaires à l'exécution de l'opération. Plutôt que de vous soucier de la gestion des ressources matérielles, vous pouvez considérer une unité de demande comme une mesure unique des ressources nécessaires à l'exécution des opérations de base de données et à la réponse à la demande de l'application.

Les unités de demande sont configurées et affectées en fonction du niveau de performances défini pour une collection. Un niveau de performances est affecté à chaque collection dès sa création. Ce niveau de performances détermine la capacité de traitement d’une collection en unités de demande par seconde. Les niveaux de performances peuvent être ajustés tout au long de la durée de vie d’une collection pour s’adapter aux besoins de traitement et aux modèles d’accès changeants de votre application. Pour plus d’informations, consultez la rubrique [Niveaux de performances dans DocumentDB](documentdb-performance-levels.md).

>[AZURE.IMPORTANT]Les collections sont des entités facturables. Le coût est déterminé par le niveau de performances affecté à la collection.

La consommation d'unités de demande est évaluée en fonction d'un taux par seconde. Pour les applications qui dépassent le taux d’unités de demande configuré pour une collection, les requêtes pour cette collection sont limitées jusqu’à ce que le taux tombe sous le niveau réservé. Si votre application requiert un niveau de débit supérieur, vous pouvez ajuster le niveau de performances des collections existantes ou répartir les requêtes d’applications entre les nouvelles collections.

Une unité de demande est une mesure normalisée du coût de traitement de la demande. Une seule unité de demande représente la capacité de traitement nécessaire pour lire (via un élément self link) un seul document JSON de 1 Ko composé de 10 valeurs de propriété uniques. Le coût en unités de demande est établi en supposant que le niveau de cohérence est défini à la valeur par défaut « Session » et que tous les documents sont automatiquement indexés. Une demande d'insertion, de remplacement ou de suppression du même document nécessite un plus grand traitement de la part du service et consomme donc plus d'unités de demande. Chaque réponse du service inclut un en-tête personnalisé (x-ms-request-charge) qui mesure le nombre d’unités de demande consommées pour la demande. Cet en-tête est également accessible via les [Kits de développement logiciel (SDK)](https://msdn.microsoft.com/library/azure/dn781482.aspx). Dans le Kit de développement logiciel (SDK) .NET, RequestCharge est une propriété de l’objet ResourceResponse.

>[AZURE.NOTE]La ligne de base d’une unité de demande pour un document de 1 Ko correspond à une opération GET simple par l’élément self link du document.

Plusieurs facteurs déterminent le nombre d'unités de demande consommées par une opération effectuée dans un compte de base de données DocumentDB. Exemples de facteurs :

- Taille du document. Plus la taille du document est grande, plus le nombre d'unités consommées pour lire ou écrire des données augmente.
- Nombre de propriétés. En supposant que toutes les propriétés sont indexées par défaut, le nombre d'unités consommées pour écrire un document augmente parallèlement au nombre de propriétés.
- Cohérence des données. Quand vous utilisez les niveaux de cohérence des données Strong (Fort) ou Bounded Staleness (En fonction de l'obsolescence), des unités supplémentaires sont consommées pour lire les documents.
- Propriétés indexées. Une stratégie d'indexation sur chaque collection détermine quelles propriétés sont indexées par défaut. Vous pouvez réduire la consommation d'unités de demande en limitant le nombre de propriétés indexées. 
- Indexation des documents. Par défaut, chaque document est automatiquement indexé. Vous consommerez moins d'unités de demande en choisissant de ne pas indexer certains documents.

Les requêtes, procédures stockées et déclencheurs consomment plus ou moins d'unités de demande selon la complexité des opérations effectuées. Pendant le développement de votre application, inspectez l'en-tête du coût en demandes pour mieux comprendre de quelle façon chaque opération consomme la capacité des unités de demande.

## Choix du niveau de cohérence et débit
Le choix du niveau de cohérence par défaut a une incidence sur le débit et la latence. Vous pouvez définir le niveau de cohérence par défaut via un programme et via le portail Azure en version préliminaire. Vous pouvez également remplacer le niveau de cohérence par demande. Par défaut, le niveau de cohérence est celui d'une session qui fournit des lectures/écritures unitones et lit vos garanties en écriture. Le niveau de cohérence Session (Par session) est idéal pour les applications centrées sur les utilisateurs et fournit un équilibre idéal entre cohérence et performances.

Pour obtenir des instructions sur la modification de votre niveau de cohérence dans la version préliminaire du portail de gestion Azure, consultez la section [Gestion d'un compte DocumentDB](documentdb-manage-account.md#consistency) Ou, pour plus d'informations sur les niveaux de cohérence, consultez la rubrique [Utilisation des niveaux de cohérence](documentdb-consistency-levels.md).

## Stockage de documents approvisionné et surcharge d'index
Avec chaque collection créée, vous disposez d’un stockage de documents SSD de 10 Go. Ce stockage inclut les documents, plus le stockage pour l'index. Par défaut, une collection DocumentDB est configurée de manière à indexer automatiquement tous les documents sans demander explicitement d'index ou de schémas secondaires. En fonction des applications utilisant DocumentDB, la surcharge d’index standard est comprise entre 2 et 20 %. La technologie d'indexation utilisée par DocumentDB permet de garantir que, indépendamment des valeurs des propriétés, la surcharge d'index ne dépasse pas 80 % de la taille des documents avec les paramètres par défaut.

Par défaut, tous les documents sont automatiquement indexés par DocumentDB. Toutefois, si vous voulez affiner le réglage de la surcharge d'index, vous pouvez choisir de supprimer certains documents de l'indexation au moment de l'insertion ou du remplacement d'un document, comme décrit dans la rubrique [Stratégies d’indexation de DocumentDB](documentdb-indexing-policies.md). Vous pouvez configurer une collection DocumentDB de manière à exclure tous les documents de la collection de l'indexation. Vous pouvez également configurer une collection DocumentDB de manière à ce qu'elle indexe de manière sélective uniquement certaines propriétés ou certains chemins d'accès avec des caractères génériques de vos documents JSON, comme décrit dans la rubrique [Configuration de la stratégie d'indexation d'une collection](documentdb-indexing-policies.md#configuring-the-indexing-policy-of-a-collection). L'exclusion de propriétés ou documents permet également d'améliorer le débit en écriture, ce qui signifie que vous allez consommer moins d'unités de demande.
 
## Étapes suivantes
Pour obtenir des instructions sur la surveillance des niveaux de performances dans le portail Azure en version préliminaire, consultez la rubrique [Surveillance d’un compte DocumentDB](documentdb-monitor-accounts.md).

Pour plus d’informations sur le choix des niveaux de performances des collections, consultez la rubrique [Niveaux de performances dans DocumentDB](documentdb-performance-levels).
 

<!---HONumber=August15_HO6-->