<properties 
	pageTitle="Gestion des capacités et performances de DocumentDB | Azure" 
	description="Découvrez comment mettre à l'échelle DocumentDB avec de la flexibilité pour répondre aux besoins de capacité de votre application." 
	services="documentdb" 
	authors="mimig1, johnfmacintyre" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="mimig"/>

#Gestion des capacités et performances de DocumentDB
DocumentDB est un service de base de données NoSQL orienté documents, entièrement géré et évolutif.  Avec DocumentDB, il n'est pas nécessaire de louer des machines virtuelles, de déployer des logiciels, de surveiller les bases de données ou de se soucier de la récupération d'urgence. DocumentDB est surveillé en continu par les ingénieurs Microsoft afin d'offrir une disponibilité, des performances et une protection des données optimales.  

Vous pouvez démarrer avec DocumentDB en créant un compte de base de données via le [portail Azure en version préliminaire](https://portal.azure.com/). DocumentDB est proposé sous forme d'unités empilables de capacité de stockage et de débit SSD. Vous pouvez mettre à l'échelle DocumentDB en toute flexibilité pour répondre aux besoins de votre application en matière de performances et de stockage. 

Chaque unité de capacité est fournie avec un quota de collections pour le stockage des données de documents, le stockage des documents approvisionnés et le débit approvisionné sous forme d'unités de demande par seconde. Si les besoins de capacité de votre application changent, vous pouvez augmenter ou réduire la quantité de capacité approvisionnée dans votre compte de base de données. La capacité approvisionnée sous un compte de base de données est disponible pour toutes les bases de données et collections existantes ou créées dans le compte.

> [AZURE.NOTE] Chaque collection peut évoluer en termes de stockage et de débit, jusqu'à la [limite](documentdb-limits.md) maximale prise en charge. Le débit est réparti uniformément entre toutes les collections jusqu'à la limite maximale par collection.

Lorsque votre application dépasse les niveaux de performances d'une ou plusieurs collections, les demandes sont limitées en fonction de chaque collection. Cela signifie que certaines demandes d'application peuvent réussir tandis que d'autres peuvent être limitées.

##<a name="DBaccount"></a>Compte de base de données et ressources d'administration
En tant qu'abonné Azure, vous pouvez approvisionner un ou plusieurs comptes de base de données DocumentDB. Chaque compte de base de données offre un quota de ressources d'administration, avec des bases de données, des utilisateurs et des autorisations. Ces ressources sont soumises à [des quotas et des limites](documentdb-limits.md). Si vous avez besoin de ressources d'administration supplémentaires, contactez le support technique.   

##<a name="DBstorage"></a> Bases de données avec stockage de documents illimité
Une seule base de données DocumentDB peut contenir un volume presque illimité de stockage de documents partitionné par collections. Les collections forment les domaines de transaction des documents qu'elles contiennent. Une base de données DocumentDB est flexible par défaut : elle peut aller de quelques Go à des pétaoctets de stockage de documents SSD et de débit approvisionné. Contrairement à une base de données de SGBDR classique, une base de données DocumentDB n'est pas étendue à un seul ordinateur.   

Avec DocumentDB, tandis que les besoins d'extensibilité de votre application augmentent, vous pouvez créer plus de collections ou de bases de données, ou les deux. En effet, plusieurs applications de Microsoft utilisent DocumentDB à l'échelle du client en créant des bases de données DocumentDB très volumineuses, contenant chacune des milliers de collections regroupant des téraoctets de stockage de documents. Vous pouvez augmenter ou réduire la taille d'une base de données en ajoutant ou en supprimant des collections pour répondre aux besoins d'extensibilité de vos applications. Vous pouvez créer n'importe quel nombre de collections dans une base de données, selon l'offre et la quantité d'unités de capacité que vous achetez. Le stockage SSD et le débit approvisionné pour vous peuvent être répartis entre les collections situées sous les bases de données de votre compte de base de données. 

##<a name="DBCollections"></a>Collections de base de données
Chaque base de données DocumentDB peut contenir une ou plusieurs collections. Une collection fournit l'étendue du stockage de documents et de l'exécution de requêtes. Il sert également de domaine de transaction pour tous les documents qu'elle contient. Vous pouvez créer n'importe quel nombre de collections pour répondre aux besoins de vos applications en termes d'échelle. Avant de créer des collections, vous devez acheter une ou plusieurs unités de capacité. Chaque unité de capacité inclut un quota de collections. Si vous atteignez ce dernier pour le compte, vous pouvez acheter d'autres unités de capacité.  

>[AZURE.NOTE] Chaque collection prend en charge le stockage de 10 Go de données de document. 

##<a name="ProvStorage"></a>Stockage et débit approvisionnés en tant qu'unités de capacité
Vous pouvez approvisionner des unités empilables de stockage de documents SSD et de débit en tant qu'unités de capacité. Vous pouvez mettre à l'échelle de façon élastique DocumentDB avec des performances prévisibles en achetant d'autres unités de capacité pour répondre aux besoins de votre application en termes de mise à l'échelle, de stockage et de débit.  
 
Chaque unité de capacité inclut 3 collections élastiques, 10 Go de stockage de documents SSD approvisionné et 2 000 unités de demande comme débit approvisionné. Le stockage approvisionné et la capacité de débit associés à l'unité de capacité sont distribués dans les collections DocumentDB que vous créez.   

##<a name="ProvThroughput"></a>Unités de demande et opérations de base de données
DocumentDB propose un riche ensemble d'opérations de base de données, dont les requêtes hiérarchiques et relationnelles avec les fonctions définies par l'utilisateur, les procédures stockées et les déclencheurs, qui fonctionnent tous au niveau des documents d'une collection de base de données. Le coût de traitement associé à chacune de ces opérations varie selon le processeur, les E/S et la mémoire nécessaires à l'exécution de l'opération. Plutôt que de vous soucier de la gestion des ressources matérielles, vous pouvez considérer une unité de demande comme une mesure unique des ressources nécessaires à l'exécution des opérations de base de données et à la réponse à la demande de l'application.

Les unités de demande sont approvisionnées pour chaque compte de base de données selon le nombre d'unités de capacité achetées. Dans la version préliminaire de DocumentDB, chaque collection créée est associée à une limite d'unités de demande. L'allocation est calculée en allouant uniformément toutes les unités de demande fournies par les unités de capacité achetées à chaque collection contenue dans un compte. Prenons l'exemple d'un compte avec 2 unités de capacité achetées et 4 collections créées. Chaque collection reçoit 2 unités de capacité x 2 000 unités de demande / 4 collections pour une allocation de 1 000 unités de demande par collection. Après la mise à disposition générale d'Azure DocumentDB, le service ne répartit plus les unités de demande entre les différentes collections créées dans un compte. Des unités de demande sont achetées et affectées à des collections spécifiques en fonction des niveaux de performances. Pour plus d'informations, consultez [ Niveaux de performances de DocumentDB](documentdb-performance-levels.md). 

>[AZURE.NOTE] Dans la version préliminaire d'Azure DocumentDB, une collection ne peut pas recevoir plus de 2 000 unités de demande. 

La consommation d'unités de demande est évaluée en fonction d'un taux par seconde. Les applications qui dépassent le taux d'unité de demande approvisionné pour le compte associé sont limitées jusqu'à ce que le taux descende en dessous du niveau réservé pour chaque collection. Si votre application a besoin d'un niveau de débit plus élevé, vous pouvez acheter des unités de capacité supplémentaires.

Une unité de demande est une mesure normalisée du coût de traitement de la demande. Une seule unité de demande représente la capacité de traitement nécessaire pour lire un seul document JSON de 1 Ko composé de 10 valeurs de propriété uniques. Le coût en unités de demande est établi en supposant que le niveau de cohérence est défini à la valeur par défaut " Session " et que tous les documents sont automatiquement indexés. Une demande d'insertion, de remplacement ou de suppression du même document nécessite un plus grand traitement de la part du service et consomme donc plus d'unités de demande. Chaque réponse à une demande du service inclut un en-tête personnalisé (x-ms-request-charge) qui mesure le nombre d'unités de demande consommées pour la demande. Cet en-tête est également accessible via les Kits de développement logiciel (SDK). Dans le Kit de développement logiciel (SDK) de .Net, RequestCharge est une propriété de l'objet ResourceResponse.

Plusieurs facteurs déterminent le nombre d'unités de demande consommées par une opération effectuée dans un compte de base de données DocumentDB. Exemples de facteurs :

- Taille du document : plus la taille du document est grande, plus le nombre d'unités consommées pour lire ou écrire des données augmente.
- Nombre de propriétés : en supposant que toutes les propriétés sont indexées par défaut, le nombre d'unités consommées pour écrire un document augmente parallèlement au nombre de propriétés.
- Cohérence des données : quand vous utilisez les niveaux de cohérence des données Strong (Fort) ou Bounded Staleness (En fonction de l'obsolescence), des unités supplémentaires sont consommées pour lire les documents.
- Propriétés indexées : une stratégie d'indexation sur chaque collection détermine quelles propriétés sont indexées par défaut. Vous pouvez réduire la consommation d'unités de demande en limitant le nombre de propriétés indexées.
- Indexation des documents : par défaut, chaque document est automatiquement indexé. Vous consommerez moins d'unités de demande en choisissant de ne pas indexer certains documents.

Les requêtes, procédures stockées et déclencheurs consomment plus ou moins d'unités de demande selon la complexité des opérations effectuées. Pendant le développement de votre application, inspectez l'en-tête du coût en demandes pour mieux comprendre de quelle façon chaque opération consomme la capacité des unités de demande.

##<a name="Collections"></a>Collections et débit approvisionné
Le débit approvisionné de votre compte de base de données est alloué uniformément dans les collections jusqu'au niveau de débit maximal (unités de demande) pour une seule collection. Par exemple, si vous achetez une seule unité de capacité et que vous créez une seule collection, l'ensemble du débit approvisionné pour l'unité de capacité sera disponible pour la collection. Si une autre collection est créée, le débit approvisionné est alloué de manière égale entre chaque collection, recevant ainsi la moitié de l'ensemble du débit approvisionné.  

##<a name="Consistency"></a>Choix du niveau de cohérence et débit
Le choix du niveau de cohérence par défaut a une incidence sur le débit et la latence.  Vous pouvez définir le niveau de cohérence par défaut par programme ou via le portail Azure. Vous pouvez également remplacer le niveau de cohérence par demande. Par défaut, le niveau de cohérence est celui d'une session qui fournit des lectures/écritures unitones et lit vos garanties en écriture. Le niveau de cohérence Session (Par session) est idéal pour les applications centrées sur les utilisateurs et fournit un équilibre idéal entre cohérence et performances.    

Pour obtenir des instructions sur la modification de votre niveau de cohérence dans la version préliminaire du portail de gestion Azure, consultez la section " Gestion des paramètres de cohérence DocumentDB " dans l'article [Gestion d'un compte DocumentDB](documentdb-manage-account.md).

##<a name="IndexOverhead"></a>Stockage de documents approvisionné et surcharge d'index
Avec chaque unité de capacité achetée, votre compte est approvisionné avec un stockage de documents SSD de 10 Go. Ce stockage inclut les documents, plus le stockage pour l'index. Par défaut, une collection DocumentDB est configurée de manière à indexer automatiquement tous les documents sans demander explicitement d'index ou de schémas secondaires. L'utilisation étant basée sur la production des applications internes à l'échelle du consommateur qui utilisent DocumentDB, la surcharge d'index classique est comprise entre 2 et 20 %. La technologie d'indexation utilisée par DocumentDB permet de garantir que, indépendamment des valeurs des propriétés, la surcharge d'index ne dépasse pas 80 % de la taille des documents avec les paramètres par défaut.  

Par défaut, tous les documents sont automatiquement indexés par DocumentDB. Toutefois, si vous voulez affiner le réglage de la surcharge d'index, vous pouvez choisir de supprimer certains documents de l'indexation au moment de l'insertion ou du remplacement d'un document. Vous pouvez configurer une collection DocumentDB de manière à exclure tous les documents de la collection de l'indexation. Vous pouvez également configurer une collection DocumentDB de manière à ce qu'elle indexe de manière sélective uniquement certaines propriétés ou certains chemins d'accès avec des caractères génériques de vos documents JSON.  L'exclusion de propriétés ou documents permet également d'améliorer le débit en écriture, ce qui signifie que vous allez consommer moins d'unités de demande.   
 
##<a name="NextSteps"></a>Étapes suivantes
Pour obtenir des instructions sur la gestion de votre compte DocumentDB dans la version préliminaire du portail Azure, consultez la section " Gestion des paramètres de cohérence DocumentDB " dans l'article [Gestion d'un compte DocumentDB](documentdb-manage-account.md).

Pour obtenir des instructions sur la surveillance des niveaux de performances dans le portail Azure en version préliminaire, consultez la page [Surveillance d'un compte DocumentDB](documentdb-monitor-accounts.md).

<!--HONumber=49--> 