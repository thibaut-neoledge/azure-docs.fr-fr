---
title: "Considérations sur les performances et l’optimisation de Recherche Azure | Microsoft Docs"
description: "Réglage des performances d’Azure Search et configuration d’une mise à l’échelle optimale"
services: search
documentationcenter: 
author: LiamCavanagh
manager: pablocas
editor: 
ms.assetid: 4d3cd864-29d2-4921-be0d-a3f1a819de46
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/17/2016
ms.author: liamca
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 9024c47e7d483129d66105012e0d67cfa9700cb9


---
# <a name="azure-search-performance-and-optimization-considerations"></a>Considérations sur les performances et l’optimisation d’Azure Search
Une expérience de recherche optimale est essentielle à la réussite de nombreuses applications mobiles et web. Du secteur de l’immobilier à celui des véhicules d’occasion en passant par les catalogues en ligne, une recherche rapide et des résultats pertinents affectent l’expérience du client. Ce document a pour but de vous présenter les meilleures pratiques permettant de tirer le meilleur parti d’Azure Search, en particulier dans des scénarios avancés avec des exigences plus complexes en matière d’évolutivité, de prise en charge multilingue ou de classement personnalisé.  En outre, ce document détaille les mécanismes internes et décrit les approches qui fonctionnent efficacement dans les applications clientes réelles.

## <a name="performance-and-scale-tuning-for-search-services"></a>Réglage des performances et de la mise à l’échelle pour les services de recherche
Nous sommes tous habitués aux moteurs de recherche comme Bing et Google et aux performances élevées qu'ils offrent.  Par conséquent, lorsque les clients utilisent la fonction de recherche de votre application mobile ou web, ils s’attendent à bénéficier des mêmes performances.  Lors de l’optimisation des performances de recherche, une des meilleures approches consiste à se concentrer sur la latence, c’est-à-dire la durée nécessaire pour qu’une requête renvoie des résultats.  Lors de l’optimisation de la latence de recherche, il est important de noter les points suivants :

1. Choisissez une latence cible (ou durée maximale) nécessaire à l’exécution d’une recherche standard .
2. Créez et testez une charge de travail réelle avec votre service de recherche en utilisant un jeu de données réaliste pour mesurer les taux de latence.
3. Commencez par un petit nombre de requêtes par seconde (RPS) puis augmentez le nombre d’exécutions dans le test jusqu'à ce que la latence des requêtes soit inférieure à la latence cible définie.  Il s’agit d’un test d’évaluation important qui vous aidera à planifier la mise à l’échelle à mesure que l’utilisation de votre application s’intensifie.
4. Dans la mesure du possible, réutilisez les connexions HTTP.  Si vous utilisez le Kit de développement logiciel (SDK) .NET Azure Search, cela signifie que vous devez réutiliser une instance ou [SearchIndexClient](https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchindexclient.aspx) , et si vous utilisez l’API REST, vous devez réutiliser une instance HttpClient unique.

Lors de la création de ces charges de travail de test, certaines caractéristiques d’Azure Search doivent être prises en compte :

1. Il est possible de lancer un si grand nombre de requêtes en même temps que les ressources disponibles dans votre service Azure Search finissent par être saturées.  Dans ce cas, vous verrez les codes de réponse HTTP 503.  Pour cette raison, il est préférable de commencer avec différentes plages de requêtes de recherche pour afficher les différences de taux de latence à mesure que vous ajoutez d’autres requêtes de recherche.
2. Le téléchargement de contenu vers Azure Search aura un impact sur les performances globales et la latence du service Azure Search.  Si vous prévoyez d’envoyer des données pendant que les utilisateurs effectuent des recherches, il est important de prendre en compte cette charge de travail dans vos tests.
3. Les requêtes de recherche n’offrent pas toutes les mêmes niveaux de performances.  Par exemple, une recherche de document ou une suggestion de recherche s’exécutera plus rapidement qu’une requête comportant un nombre important de facettes et les filtres.  Il est préférable de définir les différentes requêtes que vous compter afficher lors de la génération de vos tests.  
4. Il est important de choisir différentes requêtes de recherche car si vous exécutez continuellement les mêmes requêtes de recherche, la mise en cache des données semble offrir de meilleures performances qu’avec un jeu de requêtes plus disparates.

> [!NOTE]
> Le [test de charge Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) est un excellent moyen d’exécuter vos tests de performances car il permet d’exécuter des requêtes HTTP comme s’il s’agissait de requêtes dans la Recherche Azure et permet ainsi la parallélisation des requêtes.
> 
> 

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>Mise à l’échelle d’Azure Search pour obtenir des taux de requêtes élevés et des requêtes limitées
Lorsque vous recevez un trop grand nombre de requêtes limitées ou dépassez votre taux de latence cible suite à une charge de requête accrue, vous pouvez essayer de diminuer les taux de latence de deux manières :

1. **Augmenter les réplicas :** un réplica s’apparente à une copie de vos données et permet à la Recherche Azure d’équilibrer la charge des requêtes sur plusieurs copies.  L’intégralité de l’équilibrage de la charge et de la réplication des données entre les réplicas est gérée par Azure Search et vous pouvez à tout moment modifier le nombre de réplicas alloués à votre service.  Vous pouvez allouer jusqu'à 12 réplicas dans un service de recherche Standard, et 3 dans un service de recherche de base.  Les réplicas peuvent être ajustés sur le [Portail Azure](search-create-service-portal.md) ou à l’aide de [l’API de gestion de la Recherche Azure](search-get-started-management-api.md).
2. **Augmenter le niveau de recherche :** la Recherche Azure est fournie avec un certain [nombre de niveaux](https://azure.microsoft.com/pricing/details/search/) et chacun de ces niveaux offre différents niveaux de performances.  Dans certains cas, vous aurez tant de requêtes que le niveau sélectionné est incapable de fournir des taux de latence suffisamment faibles, même lorsque les réplicas sont optimisés.  Dans ce cas, vous pouvez passer à l’un des niveaux de recherche supérieurs tel que le niveau Azure Search S3, parfaitement adapté aux scénarios comportant un grand nombre de documents et des charges de travail de requêtes extrêmement élevées.

## <a name="scaling-azure-search-for-slow-individual-queries"></a>Mise à l’échelle d’Azure Search pour des requêtes individuelles lentes
Une requête unique qui prend trop de temps peut également entraîner de faibles taux de latence.  Dans ce cas, l’ajout de réplicas n’améliorera pas les taux de latence.  Il existe alors deux options possibles :

1. **Augmenter les partitions** Une partition est un mécanisme permettant de répartir vos données sur des ressources supplémentaires.  Pour cette raison, lorsque vous ajoutez une deuxième partition, vos données sont divisées en deux.  Une troisième partition divise votre index en trois, etc..  Cette opération a également pour effet que, dans certains cas, les requêtes lentes s’exécuteront plus rapidement en raison de la parallélisation du calcul.  Il existe quelques exemples dans lesquels cette parallélisation fonctionne très bien avec des requêtes affichant une faible sélectivité.  Il s’agit de requêtes correspondant à de nombreux documents ou lorsque l’utilisation de facettes doit compter un grand nombre de documents.  Dans la mesure où de nombreux calculs sont nécessaires pour évaluer la pertinence des documents ou pour compter le nombre de documents, l’ajout de partitions supplémentaires peut contribuer à fournir des calculs supplémentaires.  
   
   Il peut y avoir un maximum de 12 partitions dans le service de recherche standard, et 1 partition dans le service de recherche de base.  Les partitions peuvent être ajustées sur le [Portail Azure](search-create-service-portal.md) ou à l’aide de [l’API de gestion de la Recherche Azure](search-get-started-management-api.md).
2. **Limiter les champs à cardinalité élevée :** Un champ à cardinalité élevée est un champ pouvant être utilisé comme facette ou comme filtre et qui comporte un nombre important de valeurs uniques et, par conséquent, consomme beaucoup de ressources pour calculer les résultats.   Par exemple, la définition d’un champ ID produit ou Description comme un champ pouvant être utilisé comme facette ou comme filtre peut offrir une cardinalité élevée car la plupart des valeurs sont uniques pour chaque document. Dans la mesure du possible, limitez le nombre de champs à cardinalité élevée.
3. **Augmenter le niveau de recherche :** Choisir un niveau de Recherche Azure supérieur est une autre façon d’améliorer les performances des requêtes lentes.  Chaque niveau supérieur propose également un processeur plus rapide et plus de mémoire, ce qui peut avoir un impact positif sur les performances des requêtes.

## <a name="scaling-for-availability"></a>Mise à l’échelle pour la disponibilité
Les réplicas permettent non seulement de réduire la latence des requêtes mais ils peuvent également offrir une plus grande disponibilité.  Avec un seul réplica, attendez-vous à des temps d’arrêt périodiques en raison du redémarrage du serveur après les mises à jour logicielles ou lorsque d’autres événements de maintenance se produisent.  Par conséquent, il est important d’évaluer si votre application requiert une haute disponibilité des recherches (requêtes) ainsi que des écritures (événements d’indexation).  Azure Search offre des options de contrat de niveau de service sur tous les services de recherche payants avec les attributs suivants :

* 2 réplicas pour la haute disponibilité des charges de travail en lecture seule (requêtes)
* 3 réplicas (ou plus) pour la haute disponibilité des charges de travail en lecture-écriture (requêtes et indexation)

Pour plus d’informations, consultez le [contrat de niveau de service Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Étant donné que les réplicas sont des copies de vos données, avoir plusieurs réplicas permet à Azure Search de gérer les redémarrages et la maintenance de l’ordinateur, réplica par réplica, tout en permettant aux requêtes de continuer à s’exécuter par rapport à d’autres réplicas.  Pour cette raison, vous devrez également prendre en compte la façon dont ce temps d’arrêt peut influer sur les requêtes qui doivent désormais être exécutées sur une copie des données.

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>Mise à l’échelle de charges de travail géo-distribuées et géo-redondance
Pour les charges de travail géo-distribuées, vous constaterez que les utilisateurs se trouvant loin du centre de données hébergeant votre service Azure Search bénéficieront de taux de latence plus élevées.  Pour cette raison, il est souvent important de disposer de plusieurs services de recherche dans les régions proches de ces utilisateurs.  Azure Search n’offre actuellement pas de méthode automatisée de géo-réplication des index Azure Search sur différentes régions, mais quelques techniques permettent d’implémenter et de gérer simplement ce processus. Elles sont présentées dans les sections suivantes.

L’objectif d’un jeu géo-distribué de services de recherche est d’avoir plusieurs index disponibles dans deux ou plusieurs régions où l’utilisateur sera redirigé vers le service Azure Search qui offre la plus faible latence, comme illustré dans cet exemple :

   ![Tableau croisé des services par région][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Synchronisation des données entre plusieurs services Azure Search
Il existe deux options pour synchroniser vos services de recherche distribués : à l’aide de [l’indexeur de Recherche Azure](search-indexer-overview.md) ou de l’API Push (également appelée [API REST de Recherche Azure](https://msdn.microsoft.com/library/dn798935.aspx)).  

### <a name="azure-search-indexers"></a>Indexeurs Azure Search
Si vous utilisez l’indexeur Azure Search, vous importez déjà les modifications des données à partir d’un magasin de données central comme la base de données SQL Azure ou DocumentDB. Lorsque vous créez un service de recherche, vous créez tout simplement un indexeur Azure Search pour ce service qui pointe vers ce même magasin de données. De cette façon, chaque fois que de nouvelles modifications sont envoyées au magasin de données, ces informations seront indexées par plusieurs indexeurs.  

Voici à quoi ressemblerait une telle architecture.

   ![Source de données unique avec indexeur distribué et combinaisons de service][2]

### <a name="push-api"></a>API push
Si vous utilisez l’API Push Azure Search pour [mettre à jour le contenu de l’index Azure Search](https://msdn.microsoft.com/library/dn798930.aspx), vous pouvez synchroniser vos différents services de recherche en envoyant les modifications vers tous les services de recherche chaque fois qu’une mise à jour est nécessaire.  Ce faisant, il est important de veiller à gérer les cas où une mise à jour d’un service de recherche échoue et où une ou plusieurs mises à jour réussissent.

## <a name="leveraging-azure-traffic-manager"></a>Utilisation d’Azure Traffic Manager
[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) vous permet d’acheminer les requêtes vers plusieurs sites géo-localisés et pris en charge par plusieurs services Azure Search.  Traffic Manager offre l’avantage de pouvoir tester Azure Search pour vous assurer qu’il est disponible et de rediriger les utilisateurs vers d’autres services de recherche en cas d’interruption du service.  En outre, si vous acheminez des requêtes de recherche via des sites Web Azure, Azure Traffic Manager permet d’équilibrer les charges lorsque le site web est opérationnel mais pas Azure Search.  Voici un exemple d’architecture tirant parti de Traffic Manager.

   ![Tableau croisé des services par région, avec Traffic Manager central][3]

## <a name="monitoring-performance"></a>Analyse des performances
Azure Search permet d’analyser et de surveiller les performances de votre service via [Search Traffic Analytics (STA)](search-traffic-analytics.md). STA vous permet de consigner des opérations de recherche individuelles ainsi que des mesures agrégées sur un compte de stockage Azure, puis de les analyser ou de les visualiser dans Power BI.  À l’aide de mesures STA, vous pouvez afficher les statistiques de performances comme le nombre moyen de requêtes ou les temps de réponse.  En outre, la journalisation des opérations vous permet d’examiner en détail des opérations de recherche spécifique.

STA est un outil précieux pour comprendre les taux de latence du point de vue d’Azure Search.  Comme les mesures de performances des requêtes consignées sont basées sur la durée nécessaire pour qu’une requête soit entièrement traitée dans Azure Search (depuis le moment où elle est demandée jusqu’à son envoi), vous pouvez l’utiliser pour déterminer si les problèmes de latence se produisent du côté du service Azure Search ou à l’extérieur de ce service, par exemple en raison d’une latence du réseau.  

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les niveaux tarifaires et les limites de service de chacun d’eux, consultez [Limites de service d’Azure Search](search-limits-quotas-capacity.md).

Consultez la page [Planification des capacités](search-capacity-planning.md) pour en savoir plus sur les combinaisons de partitions et de réplicas.

Pour plus d’informations sur les performances et obtenir des démonstrations sur la façon d’implémenter les optimisations présentées dans cet article, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png



<!--HONumber=Dec16_HO2-->


