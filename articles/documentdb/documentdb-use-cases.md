<properties 
    pageTitle="Cas d’utilisation courants de DocumentDB | Microsoft Azure" 
    description="Découvrez les cinq cas d'utilisation principaux pour DocumentDB : le contenu généré par l’utilisateur, la journalisation des événements, les données de catalogue, les données de préférences de l’utilisateur et l’Internet des objets (IoT)." 
    services="documentdb" 
    authors="h0n" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/10/2015" 
    ms.author="hawong"/>

# Cas d’utilisation courants de DocumentDB
Cet article présente une vue d'ensemble de plusieurs cas d'utilisation courants pour DocumentDB. Les recommandations de cet article constituent le point de départ du développement de votre application avec DocumentDB.

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :
 
- Quels sont les cas d’utilisation de DocumentDB les plus courants ?
- Quels sont les avantages de l'utilisation de DocumentDB pour le stockage du contenu généré par l’utilisateur ?
- Quels sont les avantages de l'utilisation de DocumentDB pour le stockage de données de catalogue ?
- Quels sont les avantages de l'utilisation de DocumentDB pour le stockage des journaux d’événements ?
- Quels sont les avantages de l'utilisation de DocumentDB pour le stockage des données de préférence de l’utilisateur ?
- Quels sont les avantages de l'utilisation de DocumentDB pour le stockage de données pour les systèmes Internet des objets (IoT) ?

    
## Contenu généré par l'utilisateur
Un cas d'utilisation courant pour DocumentDB consiste à stocker et à interroger du contenu généré par l’utilisateur pour des applications web et mobiles, plus particulièrement les applications des réseaux sociaux. Les sessions de conversation, les tweets, les billets de blog, les évaluations et les commentaires sont des exemples de contenus générés par l’utilisateur. Le contenu généré par l’utilisateur dans les applications des réseaux sociaux est souvent un mélange de texte sous forme libre, de propriétés, de balises et de relations qui ne sont pas limités par une structure rigide.

Les contenus tels que les discussions, les commentaires et les publications peuvent être stockés dans DocumentDB sans nécessiter des transformations ou un objet complexe de couches de mappage relationnelles. Des propriétés de données peuvent être ajoutées ou modifiées facilement pour répondre aux besoins lorsque les développeurs effectuent une itération dans le code d'application, favorisant ainsi un développement rapide.

Les applications qui s'intègrent à différents réseaux sociaux doivent répondre à l'évolution des schémas de ces réseaux. Puisque les données sont automatiquement indexées par défaut dans DocumentDB, les données sont prêtes à être interrogées à tout moment. Par conséquent, ces applications ont la possibilité de récupérer les projections selon leurs besoins respectifs.

La plupart des applications des réseaux sociaux s'exécutent à l'échelle mondiale et peuvent présenter des modèles d'utilisation imprévisibles. La flexibilité dans la mise à l'échelle des données est essentielle car la couche Application se met à l’échelle pour correspondre à la demande. Vous pouvez monter en charge en ajoutant des partitions de données supplémentaires sous un compte DocumentDB. De plus, vous pouvez également créer des comptes DocumentDB supplémentaires dans plusieurs régions. Pour la disponibilité régionale du service DocumentDB, consultez [Régions Azure](http://azure.microsoft.com/regions/#services).

## Données de catalogue
Les scénarios d'utilisation des données de catalogue impliquent le stockage et l'interrogation d'un ensemble d'attributs pour des entités telles que des personnes, des lieux et des produits. Les comptes d'utilisateurs, les catalogues de produits, les registres de périphérique pour IoT et les systèmes de facturation de matériaux sont des exemples de données de catalogue. Les attributs de ces données peuvent varier et peuvent changer au fil du temps pour répondre aux besoins de l'application.

Prenons l’exemple d'un catalogue de produits pour un fournisseur de pièces détachées de voiture. Chaque pièce peut avoir ses propres attributs en plus des attributs communs qui partagent toutes les pièces. En outre, les attributs pour une pièce spécifique peuvent changer l'année suivante lorsqu'un nouveau modèle est commercialisé. Comme pour un magasin de documents JSON, DocumentDB prend en charge des schémas flexibles et vous permet de représenter des données avec des propriétés imbriquées et se révèle ainsi bien adapté au stockage des données d’un catalogue de produits.

## Données de journal
Les journaux des applications sont souvent émis en volumes importants et peuvent avoir des attributs variables selon la version de l'application déployée ou les événements de journalisation des composants. Les données du journal ne sont pas limitées par des relations complexes ou des structures rigides. De plus en plus de données de journaux sont conservées au format JSON étant donné qu’il s’agit d’un format léger et lisible par les humains.
   
Il existe généralement deux principaux cas d'utilisation liés aux données du journal des événements. Le premier cas d'utilisation consiste à effectuer des requêtes ad hoc dans un sous-ensemble de données pour la résolution des problèmes. Lors de la résolution des problèmes, un sous-ensemble de données est d'abord récupéré à partir des journaux, généralement en fonction de la série chronologique. Ensuite, un zoom avant est effectué en filtrant le jeu de données par niveau d'erreur ou message d'erreur. Le stockage des journaux d'événements dans DocumentDB se révèle donc être un avantage. Les données des journaux stockées dans DocumentDB sont automatiquement indexées par défaut. Elles sont donc prêtes à être interrogées à tout moment. En outre, les données des journaux peuvent être conservées dans des partitions de données en tant que série chronologique. Les anciens journaux peuvent être déployés vers le stockage à froid par votre stratégie de rétention.

Le deuxième cas d'utilisation implique des tâches d'analyse de données de longue durée, effectuées hors connexion sur un volume important de données des journaux. L’analyse de disponibilité du serveur, l'analyse d’erreur de l’application et l’analyse des données de parcours sont des exemples de ce cas d’utilisation. En règle générale, Hadoop est utilisé pour effectuer ce type d'analyses. Avec le connecteur Hadoop pour DocumentDB, les bases de données DocumentDB fonctionnent comme des sources et des récepteurs de données pour des tâches Pig, Hive et Map/Reduce. Pour plus d'informations sur le connecteur Hadoop pour DocumentDB, consultez [Exécution d’une tâche Hadoop avec DocumentDB et HDInsight](documentdb-run-hadoop-with-hdinsight/).

## Données de préférence de l’utilisateur
Aujourd'hui, la plupart des applications web et mobiles offrent des affichages et des expériences complexes. Ces affichages et ces expériences sont généralement dynamiques et répondent aux préférences et aux humeurs de l’utilisateur ainsi qu’aux besoins de personnalisation. Par conséquent, les applications doivent être en mesure d'extraire efficacement des paramètres personnalisés afin de restituer rapidement les éléments de l’interface utilisateur et les expériences.

JSON est un format efficace pour représenter des données de disposition de l'interface utilisateur afin de la rendre légère, mais aussi pour qu’elle soit facilement interprétée par JavaScript. DocumentDB offre des niveaux de cohérence paramétrables qui permettent des lectures rapides avec des écritures à faible latence. Ainsi, le stockage des données de disposition de l'interface utilisateur, y compris les paramètres personnalisés en tant que documents JSON dans DocumentDB, est un moyen efficace pour obtenir ces données sur tout le réseau.

## Données du capteur du périphérique
Les cas d'utilisation IoT présentent généralement les mêmes schémas pour recevoir, traiter et stocker des données. Tout d'abord, ces systèmes permettent l'admission de données pouvant recevoir des paquets de données issues des capteurs de périphérique de différents paramètres régionaux. Ensuite, ces systèmes traitent et analysent des données en continu pour obtenir un aperçu en temps réel. Et enfin, la plupart - si ce n'est pas toutes - des données se retrouveront dans un magasin de données pour des requêtes ad hoc et une analyse hors connexion.

Microsoft Azure offre des services complets qui peuvent être exploités dans les cas d’utilisation IoT. Les services IoT Azure se composent d’un ensemble de services, notamment Azure Event Hubs, Azure DocumentDB, Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight et PowerBI.

Des paquets de données peuvent être reçus par Azure Event Hubs qui offre un débit d’ingestion de données élevé à faible latence. Les données reçues qui doivent être traitées pour un aperçu en temps réel peuvent être transférées dans Azure Stream Analytics pour une analyse en temps réel. Les données peuvent être chargées dans DocumentDB pour une interrogation ad hoc. Une fois que les données sont chargées dans DocumentDB, celles-ci sont prêtes à être interrogées. Les données de DocumentDB peuvent être utilisées en tant que données de référence pour l'analyse en temps réel. De plus, les données peuvent être davantage affinées et traitées en connectant des données de DocumentDB à HDInsight pour les tâches Pig, Hive ou Map/Reduce. Les données affinées sont ensuite chargées à nouveau dans DocumentDB pour la création de rapports.

Pour un exemple de solution IoT utilisant DocumentDB, EventHubs et Storm, consultez le [référentiel d'exemples de Storm et HDInsight sur GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Pour plus d'informations sur les offres Azure pour IoT, consultez [Création de votre Internet des objets](http://www.microsoft.com/fr-fr/server-cloud/internet-of-things.aspx).

## Étapes suivantes
 
Pour commencer avec DocumentDB, vous pouvez créer un [compte](http://azure.microsoft.com/pricing/free-trial/) puis suivre notre [guide d'apprentissage](documentdb-learning-map.md) pour en savoir plus sur DocumentDB et trouver les informations que vous avez besoin.

Si vous souhaitez en savoir plus sur les clients qui utilisent DocumentDB, consultez les témoignages de clients suivants :

- [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). L’intégrateur principal offre aux entreprises multinationales un aperçu global en quelques minutes avec les technologies de cloud flexibles.
- [News République](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Rendre les informations plus intelligentes pour offrir un contenu plus pertinent aux citoyens engagés. 
- [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Pour une harmonisation à travers le globe, les grandes marques se tournent vers SGS. Et SGS se tourne vers Azure.
- [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Le leader mondial Telenor utilise le cloud pour évoluer à la vitesse d'une startup. 
- [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). Le magasin du futur fonctionne grâce à la recherche rapide et au flux de données simplifié.
 

<!---HONumber=August15_HO7-->