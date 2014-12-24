<properties title="Manage DocumentDB capacity and performance" pageTitle="Gestion des capacités et performances de DocumentDB | Azure" description="Learn how you can elastically scale DocumentDB to meet the performance and storage needs of your application." metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" manager="jhubbard" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />

#Gestion des capacités et performances de DocumentDB
DocumentDB est un service de base de données NoSQL orienté documents, entièrement géré et hautement évolutif.  Avec DocumentDB, il n'est pas nécessaire de louer des machines virtuelles, de déployer des logiciels, de surveiller les bases de données ou de se soucier de la récupération d'urgence. DocumentDB est surveillé en continu par les ingénieurs Microsoft afin de fournir une disponibilité, des performances et une protection des données de qualité.  

Vous pouvez démarrer avec DocumentDB en créant un compte de base de données via le portail Microsoft Azure. DocumentDB est proposé sous forme d'unités de capacité empilables du débit et du stockage SSD. Vous pouvez mettre à l'échelle DocumentDB en toute flexibilité pour répondre aux besoins de votre application en matière de performances et de stockage. 

Chaque unité de capacité est fournie avec un quota de collections élastiques pour le stockage des données de documents, un stockage de documents et un débit approvisionnés sous forme d'unités de demande par seconde. Si les besoins de capacité de votre application changent, vous pouvez augmenter ou réduire la quantité de capacité approvisionnée dans votre compte de base de données. La capacité approvisionnée sous un compte de base de données est disponible pour toutes les bases de données et collections existantes ou créées dans le compte.  

#Compte de base de données et quota administratif
En tant qu'abonné Azure, vous pouvez approvisionner un ou plusieurs comptes de base de données DocumentDB. Chaque compte de base de données offre un quota de ressources d'administration, dont 100 bases de données, 500 000 utilisateurs et 2 000 000 d'autorisations.   

#Bases de données avec stockage de documents illimité
Une seule base de données DocumentDB peut contenir un volume presque illimité de stockage de documents partitionné par collections. Les collections forment les domaines de transaction des documents qu'elles contiennent. Une base de données DocumentDB est flexible par défaut : elle peut aller de quelques Go à des pétaoctets de stockage de documents SSD et de débit approvisionné. Contrairement à une base de données de SGBDR classique, une base de données DocumentDB n'est pas étendue à un seul ordinateur.   

Avec DocumentDB, tandis que les besoins d'extensibilité de votre application augmentent, vous pouvez créer plus de collections ou de bases de données, ou les deux. En effet, plusieurs applications de Microsoft utilisent DocumentDB à l'échelle du client en créant des bases de données DocumentDB très volumineuses, contenant chacune des milliers de collections regroupant des téraoctets de stockage de documents. Vous pouvez augmenter ou réduire la taille d'une base de données en ajoutant ou en supprimant des collections pour répondre aux besoins d'extensibilité de vos applications. Vous pouvez créer n'importe quel nombre de collections dans une base de données, selon l'offre et la quantité d'unités de capacité que vous achetez. Le stockage SSD et le débit approvisionné pour vous peuvent être répartis entre les collections situées sous les bases de données de votre compte de base de données. 

#Collections élastiques
Chaque base de données DocumentDB peut contenir une ou plusieurs collections. Une collection fournit l'étendue du stockage de documents et de l'exécution de requêtes. Il sert également de domaine de transaction pour tous les documents qu'elle contient. Les collections sont élastiques, c'est-à-dire que le stockage et le débit peuvent augmenter ou diminuer. Vous pouvez créer n'importe quel nombre de collections pour répondre aux besoins de vos applications en termes d'échelle. Avant de créer des collections, vous devez acheter une ou plusieurs unités de capacité. Chaque unité de capacité inclut un quota de collections. Si vous atteignez ce dernier pour le compte, vous pouvez acheter d'autres unités de capacité.  

>[AZURE.NOTE] Dans la version préliminaire du service, la taille des collections peut aller de 0 à 10 Go. 

#Stockage et débit approvisionnés en tant qu'unités de capacité
Vous pouvez approvisionner des unités empilables de stockage de documents SSD et de débit en tant qu'unités de capacité. Vous pouvez mettre à l'échelle de façon élastique DocumentDB avec des performances prévisibles en achetant d'autres unités de capacité pour répondre aux besoins de votre application en termes de mise à l'échelle, de stockage et de débit.  
 
Chaque unité de capacité inclut 3 collections élastiques, 10 Go de stockage de documents SSD approvisionné et 2 000 unités de demande comme débit approvisionné. Le stockage approvisionné et la capacité de débit associés à l'unité de capacité sont distribués dans les collections DocumentDB que vous créez.   

#Débit approvisionné, unités de demande et opérations de base de données
Contrairement aux magasins NoSQL de clés-valeurs qui offrent de simples opérations GET et PUT, DocumentDB propose un riche ensemble d'opérations de base de données, dont les requêtes hiérarchiques et relationnelles avec les fonctions définies par l'utilisateur, les procédures stockées et les déclencheurs, qui fonctionnent tous au niveau des documents d'une collection de base de données. Le coût associé à chacune de ces opérations varie selon le processeur, les E/S et la mémoire nécessaires à l'exécution de l'opération.  Plutôt que de vous soucier de la gestion des ressources matérielles, vous pouvez considérer une unité de demande comme une mesure unique des ressources nécessaires à l'exécution des opérations de base de données et à la réponse à la demande de l'application.   

Les unités de demande sont approvisionnées pour chaque compte de base de données selon le nombre d'unités de capacité achetées. La consommation d'unités de demande est évaluée en fonction d'un taux par seconde. Les applications qui dépassent le taux d'unité de demande approvisionné pour le compte associé sont limitées jusqu'à ce que le taux descende en dessous du niveau réservé pour le compte. Si votre application a besoin d'un niveau de débit plus élevé, vous pouvez acheter des unités de capacité supplémentaires.  

Le tableau suivant répertorie les opérations de base de données et les unités de demande disponibles par unité de capacité. Il vous aidera à planifier la manière dont une opération de base de données spécifique consomme le débit approvisionné associé à une unité de capacité.  Nous supposons que le document fait 1 Ko et qu'il est composé de 10 valeurs de propriété avec le niveau de cohérence par défaut défini sur Session (Par session), et que tous les documents sont automatiquement indexés par DocumentDB. 

|Opérations de base de données|Nombre d'opérations par seconde, par unité de capacité|
|-------------------|--------------------------------------|
|Lecture d'un seul document	|2 000
|Insertion/Remplacement/Suppression d'un seul document	|500
|Interrogation d'une collection avec un simple prédicat et renvoi d'un seul document	|1 000
|Procédure stockée avec 50 insertions de documents	|20

Dans le tableau, le nombre d'unités de demande consommées par opération d'insertion/de remplacement/de suppression pour les documents indexés automatiquement est environ 4 fois supérieur au nombre de lectures d'un document. Le tableau n'est donné qu'à titre de référence. Dans la pratique toutefois  

-	La taille de vos documents peut varier (et être différente de 1 Ko) 
-	Vos documents peuvent avoir plus de 10 propriétés.
-	Vous pouvez choisir de définir le niveau de cohérence par défaut sur Strong (Fort), Bounded Staleness (En fonction de l'obsolescence) ou Eventual (Éventuel)
-	Vous pouvez choisir de ne pas indexer certains documents 
-	Vous pouvez choisir d'indexer uniquement certaines propriétés et de ne pas laisser DocumentDB tout indexer automatiquement
-	Vos requêtes et procédures stockées peuvent être beaucoup plus complexes.  

 Vous pouvez acquérir les unités de demande pour une demande donnée en inspectant l'en-tête de réponse x-ms-request-charge de la demande.  

#Collections et débit approvisionné
Le débit approvisionné de votre compte de base de données est alloué uniformément dans les collections jusqu'au niveau de débit maximal (unités de demande) pour une seule collection. Par exemple, si vous achetez une seule unité de capacité et que vous créez une seule collection, l'ensemble du débit approvisionné pour l'unité de capacité sera disponible pour la collection. Si une autre collection est créée, le débit approvisionné est alloué de manière égale entre chaque collection, recevant ainsi la moitié de l'ensemble du débit approvisionné.  

#Choix du niveau de cohérence et du débit
Le choix du niveau de cohérence par défaut a une incidence sur le débit et la latence.  Vous pouvez définir le niveau de cohérence par défaut par programme ou via le portail Azure. Vous pouvez également remplacer le niveau de cohérence par demande. Par défaut, le niveau de cohérence est celui d'une session qui fournit des lectures/écritures unitones et lit vos garanties en écriture. La cohérence de type Session est idéale pour les applications centrées sur les utilisateurs et fournit un équilibre idéal entre cohérence et performances.   

-	Les niveaux de cohérence Session et Eventual fournissent environ 2 000 demandes de lecture de documents et 500 insertions/remplacements/suppressions de documents.
-	Les niveaux de cohérence Strong et Bounded staleness fournissent environ 1 200 demandes de lecture de documents et 500 insertions/remplacements/suppressions de documents. Avec le niveau Bounded staleness, les insertions/remplacements/suppressions ont une latence sensiblement inférieure à celle du niveau Strong.  

#Stockage de documents approvisionné et surcharge d'index
Avec chaque unité de capacité achetée, votre compte est approvisionné avec un stockage de documents SSD de 10 Go. Ce stockage inclut les documents, plus le stockage pour l'index. Par défaut, une collection DocumentDB est configurée de manière à indexer automatiquement tous les documents sans demander explicitement d'index ou de schémas secondaires. L'utilisation étant basée sur la production des applications internes à l'échelle du consommateur qui utilisent DocumentDB, la surcharge d'index classique est comprise entre 2 et 20 %. La technologie d'indexation utilisée par DocumentDB permet de garantir que, indépendamment des valeurs des propriétés, la surcharge d'index ne dépasse pas 80 % de la taille des documents avec les paramètres par défaut.  

Par défaut, tous les documents sont automatiquement indexés par DocumentDB. Toutefois, si vous voulez affiner le réglage de la surcharge d'index, vous pouvez choisir de supprimer certains documents de l'indexation au moment de l'insertion ou du remplacement d'un document. Vous pouvez configurer une collection DocumentDB de manière à exclure tous les documents de la collection de l'indexation. Vous pouvez également configurer une collection DocumentDB de manière à ce qu'elle indexe de manière sélective uniquement certaines propriétés ou certains chemins d'accès avec des caractères génériques de vos documents JSON.  L'exclusion de propriétés ou documents permet également d'améliorer le débit en écriture, ce qui signifie que vous allez consommer moins d'unités de demande.   
 
