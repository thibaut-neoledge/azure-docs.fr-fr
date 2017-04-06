---
title: "Cas d’utilisation courants et scénarios NoSQL pour Azure DocumentDB | Microsoft Docs"
description: "Découvrez les cinq cas d’utilisation principaux pour DocumentDB : le contenu généré par l’utilisateur, la journalisation des événements, les données de catalogue, les données de préférences de l’utilisateur et l’Internet des objets (IoT)."
services: documentdb
author: h0n
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: eca68a58-1a8c-4851-8cf8-6e4d2b889905
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: hawong
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 9a0ba106b4c2aded8aaac673dfba3610bd4ca0fb
ms.lasthandoff: 03/24/2017


---

# <a name="common-documentdb-use-cases"></a>Cas d’utilisation courants de DocumentDB
Cet article présente une vue d'ensemble de plusieurs cas d'utilisation courants pour DocumentDB.  Les recommandations de cet article constituent le point de départ du développement de votre application avec DocumentDB.   

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes : 

* Quels sont les cas d’utilisation de DocumentDB les plus courants ?
* Quels sont les avantages de l’utilisation de DocumentDB pour les applications de vente au détail ?
* Quels sont les avantages de l'utilisation de DocumentDB pour le stockage de données pour les systèmes Internet des objets (IoT) ?
* Quels sont les avantages de l’utilisation de DocumentDB pour les applications web et mobiles ?

## <a name="common-use-cases-for-documentdb"></a>Cas d’utilisation courants de DocumentDB
Azure DocumentDB est une base de données NoSQL à usage général qui est utilisée dans un large éventail d’applications et de cas d’utilisation. Elle constitue un bon choix pour toute application qui a besoin de temps de réponse réduits de l’ordre des millisecondes et qui doit pouvoir évoluer rapidement. Voici certains attributs de DocumentDB qui rendent cette base de données parfaitement adaptée aux applications hautes performances.

* DocumentDB partitionne en mode natif vos données une disponibilité et une évolutivité élevées.
* DocumentDB dispose d’un stockage SSD avec des temps de réponse de l’ordre des millisecondes à faible latence.
* La prise en charge par DocumentDB des niveaux de cohérence finale, de session et liée sans état offre un rapport coût-performances faible. 
* DocumentDB possède un modèle de tarification flexible et adapté aux données qui mesure indépendamment le stockage et le débit.
* Le modèle de débit réservé de DocumentDB vous permet de raisonner en termes de nombre de lectures/écritures au lieu de processeur/mémoire/IOPS du matériel sous-jacent.
* La conception de DocumentDB vous permet de monter en puissance pour des volumes de requêtes énormes, de l’ordre de milliards de requêtes par jour.

Ces attributs sont particulièrement utiles dans les applications web, mobiles, de jeu et IoT qui ont besoin de temps de réponse rapides et doivent gérer des quantités énormes de lectures et d’écritures. 

## <a name="iot-and-telematics"></a>IoT et télématique
Les cas d'utilisation IoT présentent généralement les mêmes schémas pour la réception, le traitement et le stockage des données.  Tout d'abord, ces systèmes autorisent la réception de paquets de données émanant de capteurs d’appareil dans différents paramètres régionaux. Ensuite, ces systèmes traitent et analysent les données en continu pour générer des informations pertinentes en temps réel. Les données sont alors archivées dans un stockage à froid en vue d’une analyse par batch. Microsoft Azure propose des services enrichis applicables aux cas d’utilisation IoT, dont Azure Event Hubs, Azure DocumentDB, Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight et PowerBI. 

![Architecture de référence IoT d’Azure DocumentDB](./media/documentdb-use-cases/documentdb-iot.png)

Des paquets de données peuvent être reçus par Azure Event Hubs qui offre un débit d’ingestion de données élevé à faible latence. Les données reçues qui doivent être traitées pour un aperçu en temps réel peuvent être transférées dans Azure Stream Analytics en vue d’une analyse en temps réel. Les données peuvent être chargées dans DocumentDB pour une interrogation ad hoc. Une fois que les données sont chargées dans DocumentDB, celles-ci sont prêtes à être interrogées.  Les données dans DocumentDB peuvent être utilisées comme des données de référence pour l’analyse en temps réel. De plus, les données peuvent être davantage affinées et traitées en connectant des données de DocumentDB à HDInsight pour les tâches Pig, Hive ou Map/Reduce.  Les données affinées sont ensuite chargées à nouveau dans DocumentDB pour la création de rapports.   

Pour un exemple de solution IoT utilisant DocumentDB, EventHubs et Storm, consultez le [référentiel d'exemples de Storm et HDInsight sur GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Pour plus d’informations sur les offres Azure pour IoT, consultez [Création de votre Internet des objets](http://www.microsoft.com/en-us/server-cloud/internet-of-things.aspx). 

## <a name="retail-and-marketing"></a>Ventes et marketing
DocumentDB est couramment utilisé dans le secteur de la vente au détail pour le stockage des données de catalogue. Les scénarios d’utilisation des données de catalogue impliquent le stockage et l’interrogation d’un ensemble d’attributs pour des entités telles que des personnes, des lieux et des produits.  Les comptes d'utilisateurs, les catalogues de produits, les registres de périphérique pour IoT et les systèmes de facturation de matériaux sont des exemples de données de catalogue.  Les attributs de ces données peuvent varier et peuvent changer au fil du temps pour répondre aux besoins de l'application.  

Prenons l’exemple d'un catalogue de produits pour un fournisseur de pièces détachées de voiture. Chaque pièce peut avoir ses propres attributs en plus des attributs communs qui partagent toutes les pièces.  En outre, les attributs pour une pièce spécifique peuvent changer l'année suivante lorsqu'un nouveau modèle est commercialisé.  En tant que banque de documents JSON, DocumentDB prend en charge les schémas flexibles et les données hiérarchiques, ce qui en fait une solution idéale pour stocker les données d’un catalogue de produits.

![Architecture de référence d’un catalogue de vente Azure DocumentDB](./media/documentdb-use-cases/documentdb-retail-catalog.png)

 En outre, les données stockées dans DocumentDB peuvent être intégrées dans HDInsight en vue d’une analyse Big Data grâce à des tâches Pig, Hive ou Map/Reduce. Pour plus d'informations sur le connecteur Hadoop pour DocumentDB, consultez [Exécution d’une tâche Hadoop avec DocumentDB et HDInsight](documentdb-run-hadoop-with-hdinsight.md).

 ![Architecture de référence des commandes de vente Azure DocumentDB](./media/documentdb-use-cases/documentdb-retail-orders.png)
## <a name="gaming"></a>Jeux
Le niveau de base de données est un composant essentiel des applications de jeu. Les jeux modernes traitent des graphiques sur les clients mobiles/console, mais s’appuient sur le cloud pour fournir un contenu personnalisé, comme les statistiques du jeu, l’intégration aux médias sociaux et les tableaux des meilleurs scores. Souvent, les jeux nécessitent des latences de l’ordre de la milliseconde pour les lectures et écritures afin de fournir une bonne qualité de jeu. Une base de données de jeu doit être rapide et pouvoir gérer les hausses importantes du nombre de demandes lors du lancement de nouveaux jeux ou de nouvelles fonctionnalités.

DocumentDB est utilisé par des jeux tels que [The Walking Dead: No Man’s Land](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) de [Next Games](http://www.nextgames.com/) et [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). DocumentDB offre les avantages suivants aux développeurs de jeux :

* DocumentDB permet la mise à l’échelle flexible des performances vers le haut ou vers le bas. Cela permet aux jeux de gérer la mise à jour du profil et des statistiques de dizaines jusqu’à des millions de joueurs simultanés à l’aide d’un seul appel de l’API.
* DocumentDB prend en charge les lectures et écritures en millisecondes afin d’éviter les retards lors du jeu.
* L’indexation automatique de DocumentDB permet le filtrage sur plusieurs propriétés différentes en temps réel, par exemple, localisez les joueurs avec leur ID de joueur en interne, ou leur ID GameCenter, Facebook, Google, ou exécutez une requête basée sur l’appartenance du joueur à un groupe. Cela est possible sans générer d’indexation complexe, ni infrastructure de partitionnement.
* Les fonctionnalités sociales, y compris les messages dans le jeu, l’appartenance des joueurs à des groupes, les défis terminés, les tableaux des meilleurs scores, et les graphiques sociaux sont plus faciles à implémenter avec un schéma flexible.
* DocumentDB comme plateforme en tant que service géré (PaaS) requiert une gestion et une configuration minimales pour offrir une itération rapide et réduire le délai de mise sur le marché.

![Architecture de référence des jeux Azure DocumentDB](./media/documentdb-use-cases/documentdb-gaming-architecture.png)

## <a name="web-and-mobile-applications"></a>Applications web et mobiles
DocumentDB est couramment utilisé dans les applications web et mobiles et il est particulièrement bien adapté à la modélisation des interactions sociales, à l’intégration avec les services tiers et à la création d’expériences personnalisées riches. Les Kits de développement logiciel (SDK) DocumentDB permettent de générer des applications iOS et Android enrichies utilisant [l’infrastructure Xamarin](documentdb-mobile-apps-with-xamarin.md) qui est connue.  

### <a name="social-applications"></a>Applications des réseaux sociaux
Un cas d'utilisation courant pour DocumentDB consiste à stocker et à interroger du contenu généré par l’utilisateur pour des applications web et mobiles, plus particulièrement les applications des réseaux sociaux. Les sessions de conversation, les tweets, les billets de blog, les évaluations et les commentaires sont des exemples de contenus générés par l’utilisateur. Souvent, les contenus générés par les utilisateurs dans les applications des réseaux sociaux combinent du texte au format libre, des propriétés, des balises et des relations qui ne sont pas contraintes par une structure rigide. Les contenus tels que les discussions, les commentaires et les publications peuvent être stockés dans DocumentDB sans nécessiter des transformations ou un objet complexe de couches de mappage relationnelles.  Des propriétés de données peuvent être ajoutées ou modifiées facilement pour répondre aux besoins lorsque les développeurs effectuent une itération dans le code d'application, favorisant ainsi un développement rapide.  

Les applications qui s’intègrent dans des réseaux sociaux tiers doivent répondre à l’évolution des schémas de ces réseaux. Puisque les données sont automatiquement indexées par défaut dans DocumentDB, les données sont prêtes à être interrogées à tout moment. Par conséquent, ces applications ont la possibilité de récupérer les projections selon leurs besoins respectifs.

La plupart des applications des réseaux sociaux s'exécutent à l'échelle mondiale et peuvent présenter des modèles d'utilisation imprévisibles. La flexibilité dans la mise à l'échelle des données est essentielle car la couche Application se met à l’échelle pour correspondre à la demande.  Vous pouvez monter en charge en ajoutant des partitions de données supplémentaires sous un compte DocumentDB.  De plus, vous pouvez également créer des comptes DocumentDB supplémentaires dans plusieurs régions. Pour la disponibilité régionale du service DocumentDB, consultez [Régions Azure](https://azure.microsoft.com/regions/#services).

![Architecture de référence des applications web Azure DocumentDB](./media/documentdb-use-cases/documentdb-web.png)

### <a name="personalization"></a>Personnalisation
Aujourd’hui, la plupart des applications offrent des affichages et des expériences complexes. Elles sont généralement dynamiques et répondent aux préférences et aux humeurs de l’utilisateur ainsi qu’aux besoins de personnalisation. Par conséquent, les applications doivent pouvoir extraire efficacement des paramètres personnalisés pour restituer rapidement les éléments de l’interface utilisateur et les expériences. 

JSON est un format efficace pour représenter des données de disposition de l'interface utilisateur afin de la rendre légère, mais aussi pour qu’elle soit facilement interprétée par JavaScript. DocumentDB offre des niveaux de cohérence paramétrables qui permettent des lectures rapides avec des écritures à faible latence. Ainsi, le stockage des données de disposition de l'interface utilisateur, y compris les paramètres personnalisés en tant que documents JSON dans DocumentDB, est un moyen efficace pour obtenir ces données sur tout le réseau.

## <a name="next-steps"></a>Étapes suivantes
Pour commencer avec DocumentDB, vous pouvez créer un [compte](https://azure.microsoft.com/pricing/free-trial/) puis suivre notre [parcours d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/documentdb/) pour en savoir plus sur DocumentDB et trouver les informations dont vous avez besoin. 

Si vous souhaitez en savoir plus sur les clients qui utilisent DocumentDB, consultez les témoignages de clients suivants :

* [Sulekha.com](https://customers.microsoft.com/en-US/story/sulekha-uses-azure-documentdb-to-connect-customers-and-businesses-across-india). Sulekha utilise Azure DocumentDB pour se connecter aux clients et aux entreprises en Inde.
* [NewOrbit](https://customers.microsoft.com/en-US/story/neworbit-takes-flight-with-azure-documentdb). NewOrbit prend de l’altitude avec Azure DocumentDB.
* [Affinio](https://customers.microsoft.com/en-US/doclink/affinio-switches-from-aws-to-azure-documentdb-to-harness-social-data-at-scale). Affinio passe d’AWS à Azure DocumentDB pour exploiter des données sociales à grande échelle.
* [Next Games](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). Le jeu The Walking Dead: No Man's Land atteint la première place avec le soutien d’Azure DocumentDB.
* [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Comment Halo 5 a mis en œuvre l’expérience de jeu de société avec Azure DocumentDB.
* [Galerie Cortana Analytics](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Galerie Cortana Analytics : un site communautaire évolutif basé sur Azure DocumentDB.
* [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). L’intégrateur principal offre aux entreprises multinationales un aperçu global en quelques minutes avec les technologies de cloud flexibles.
* [News République](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Rendre les informations plus intelligentes pour offrir un contenu plus pertinent aux citoyens engagés. 
* [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Pour une harmonisation à travers le globe, les grandes marques se tournent vers SGS. Et SGS se tourne vers Azure.
* [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Le leader mondial Telenor utilise le cloud pour évoluer à la vitesse d'une startup. 
* [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). Le magasin du futur fonctionne grâce à la recherche rapide et au flux de données simplifié.
* [Nucleo](https://customers.microsoft.com/story/azure-based-software-platform-breaks-down-barriers-bet). Plateforme logicielle basée sur Azure, qui élimine les barrières entre les entreprises et les clients
* [Weka](https://customers.microsoft.com/story/weka-smart-fridge-improves-vaccine-management-so-more-people-can-be-protected-against-diseases). Weka Smart Fridge améliore la gestion des vaccins, ce qui permet de protéger davantage de personnes contre les maladies
* [Orange Tribes](https://customers.microsoft.com/en-US/story/theres-more-to-that-food-app-than-meets-the-eye-or-the-mouth). Cette application dédiée à l’alimentation satisfait encore plus la vue, et le goût.
* [Real Madrid](https://customers.microsoft.com/en-US/story/real-madrid-brings-the-stadium-closer-to-450-million-f). Le Real Madrid permet à 450 millions de fans du monde entier de ressentir les émotions du stade grâce à Microsoft Cloud.
* [Tuku](https://customers.microsoft.com/en-US/story/tuku-makes-car-buying-fun-with-help-from-azure-services). Avec les services Azure, il est très amusant d’acheter une voiture sur TUKU 

