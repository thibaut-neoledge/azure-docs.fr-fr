<properties      
    pageTitle="Partitionnement des données dans DocumentDB | Azure"      
    description="Découvrez comment partitionner les données dans DocumentDB et quand utiliser le partitionnement par hachage, par plage et par recherche."          services="documentdb"      
    authors="arramac"      
    manager="johnmac"      
    editor="monicar"      
    documentationCenter=""/> 
<tags      
    ms.service="documentdb"      
    ms.workload="data-services"      
    ms.tgt_pltfrm="na"      
    ms.devlang="na"      
    ms.topic="article"      
    ms.date="02/20/2015"      
    ms.author="arramac"/> 

# Partitionnement des données dans DocumentDB

[Microsoft Azure DocumentDB](../../services/documentdb/) est conçu pour vous aider à atteindre des performances rapides et prévisibles, et pour *monter en charge* en toute transparence parallèlement à l'évolution de votre application. DocumentDB a été utilisé pour faire fonctionner les services de production à grande échelle chez Microsoft, comme le magasin de données utilisateur qui alimente la suite MSN d'applications web et mobiles. 

Vous pouvez obtenir une échelle presque illimitée en termes de débit et de stockage pour votre application DocumentDB en partitionnant horizontalement les données, un concept connu sous le nom de **partitionnement**.  Les comptes DocumentDB peuvent évoluer linéairement avec les coûts via des unités empilables, ou **collections**. Le meilleur partitionnement de vos données dans les collections dépend du format de vos données et de vos modèles d'accès. 

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :   

 - Qu'est le partitionnement par hachage, par plage et par recherche ?
 - Quand utiliser chaque technique de partitionnement et pourquoi ?
 - Comment procéder pour créer une application partitionnée sur Azure DocumentDB ?

## Collections = Partitions

Avant d'explorer plus en profondeur les techniques de partitionnement de données, il est important de comprendre ce qu'est une collection et ce qu'elle n'est pas. Comme vous le savez peut-être déjà, une collection est un conteneur pour vos documents JSON. Les collections dans DocumentDB ne sont pas seulement des conteneurs *logiques* ; ce sont aussi des conteneurs *physiques*. Ils correspondent à la limite de transaction pour les procédures stockées et les déclencheurs, et le point d'entrée pour les requêtes et les opérations CRUD. À chaque collection est assignée une quantité réservée de débit qui n'est pas partagée avec les autres collections du même compte. Par conséquent, vous pouvez faire évoluer votre application en termes de stockage et de débit en ajoutant des collections, puis répartir vos documents entre ces dernières.

Les collections diffèrent des tables dans les bases de données relationnelles. Elles n'appliquent pas de schéma. Par conséquent, vous pouvez stocker différents types de documents avec différents schémas dans la même collection. Toutefois, vous pouvez choisir d'utiliser des collections pour stocker les objets d'un type unique, comme vous le feriez avec des tables. Le meilleur modèle dépend uniquement de la manière dont les données apparaissent ensemble dans les requêtes et les transactions.

## Partitionnement avec DocumentDB

Les techniques les plus courantes utilisées pour partitionner des données avec Azure DocumentDB sont le *partitionnement par plage*, le *partitionnement parrecherche* et le *partitionnement par hachage*. Généralement, vous désignez un seul nom de propriété JSON dans votre document comme votre clé de partition, par exemple, " timestamp " ou " userID ". Dans certains cas, il peut s'agir plutôt d'une propriété JSON interne ou d'un nom de propriété différent pour chaque type de document distinct.

Examinons ces techniques plus en détail.

## Partitionnement par plage

Dans le partitionnement par plage, l'affectation des partitions est fonction de la présence de la clé de partition dans une certaine plage. Cette méthode est couramment utilisée pour le partitionnement avec des propriétés d' *horodatage* (par exemple, eventTime entre le 1er février 2015 et le 2 février 2015). 

> [AZURE.TIP] Vous devez utiliser le partitionnement par plage si vos requêtes sont limitées à des valeurs de plage spécifiques par rapport à la clé de partition.

## Partitionnement par recherche

Dans le partitionnement par recherche, les partitions sont affectées en fonction d'une table de recherche qui affecte des valeurs discrètes à des partitions spécifiques, c'est-à-dire une table de partitionnement ou une carte de partitions (" shard map "). Cette méthode est couramment utilisée pour le partitionnement par région (par exemple, la partition pour la Scandinavie contient la Norvège, le Danemark et la Suède).

> [AZURE.TIP] Le partitionnement par recherche offre un niveau élevé de contrôle dans la gestion d'une application mutualisée. Vous pouvez affecter plusieurs clients à une seule collection, un client unique à une seule collection ou même un client unique à travers plusieurs collections. 

## Partitionnement par hachage

Dans le partitionnement par hachage, les partitions sont affectées en fonction de la valeur d'une fonction de hachage, ce qui vous permet de répartir uniformément les demandes et les données entre un certain nombre de partitions. Cette méthode sert généralement pour partitionner des données produites ou consommées par un grand nombre de clients distincts et elle s'avère utile pour stocker les profils utilisateur, les éléments du catalogue et les données de télémétrie IoT (Internet des objets). 

> [AZURE.TIP] Vous devez utiliser le partitionnement par hachage lorsqu'il y a trop d'entités à énumérer via le partitionnement par recherche (par exemple, des utilisateurs ou des appareils) et que le taux de demandes est relativement uniforme entre les entités.

## Choix de la technique de partitionnement appropriée

Quelle est la technique de partitionnement qui vous convient ? Cela dépend du type de données et de vos modèles d'accès courants. Le choix de la technique de partitionnement appropriée au moment de la conception vous permet d'éviter la dette technique et de gérer la croissance en termes de taille des données et de volume des demandes.

- Le **partionnement par plage** est normalement utilisé dans le contexte des dates, car il offre un mécanisme simple et naturel de vieillissement des partitions par horodatage. Il est également utile lorsque les requêtes sont généralement limitées à une plage de temps, dans la mesure où celle-ci est alignée avec les limites de partitionnement. 
- Le **partitionnement par recherche** permet de regrouper et d'organiser des jeux de données non ordonnés et indépendants d'une façon naturelle, par exemple, de regrouper des clients par entreprise ou des États par région géographique. La recherche offre également un contrôle précis pour migrer les données entre les collections. 
- Le **partionnement par hachage** est utile pour assurer un équilibrage de charge uniforme des demandes afin d'utiliser efficacement le débit et le stockage approvisionnés. L'utilisation d'algorithmes de *hachage cohérent* permet de réduire la quantité de données à déplacer lors de l'ajout ou de la suppression d'une partition.

Vous n'êtes pas obligé de choisir une seule technique de partitionnement. Une  *combinaison* de ces techniques (partitionnement composite) peut également être utile selon le scénario. Par exemple, si vous stockez des données télémétriques de véhicules, une approche pertinente consisterait à partitionner les données télémétriques des périphériques par plage pour l'horodatage afin de simplifier la gestion des partitions, puis à créer des partitions secondaires pour le numéro d'identification de véhicule (VIN) afin d'assurer la montée en charge pour le débit (partitionnement composite par plage/hachage).

## Développement d'une application partitionnée
Il existe trois zones de conception clés à examiner lors du développement d'une application partitionnée sur DocumentDB.

- Comment vous acheminez vos créations et vos lectures (y compris les requêtes) vers les collections appropriées.
- Comment vous rendez persistante et récupérez la configuration de la résolution des partitions, c'est-à-dire les tables de partitionnement.
- Comment vous ajoutez/supprimez des partitions au fur et à mesure que le volume de vos données et de vos requêtes augmente.

Examinons de plus près chacune de ces zones.

## Routage des créations et des requêtes

Le routage des demandes de création de document est direct pour les trois techniques dont nous avons discuté jusqu'à présent. Le document est créé sur la partition à partir de la valeur de hachage, de recherche ou de plage correspondant à la clé de partition.

La portée des lectures et les requêtes doit normalement être limitée à une seule clé de partition, afin que les requêtes puissent être distribuées uniquement sur les partitions correspondantes. Des requêtes sur toutes les données, cependant, vous obligeraient à *distribuer* la demande sur plusieurs partitions, puis à fusionner les résultats. N'oubliez pas que certaines requêtes peuvent être obligées d'exécuter une logique personnalisée pour fusionner les résultats, par exemple, lors de l'extraction des N premiers résultats.

## Gestion de votre mappage de partitions

Vous devez également décider comment vous souhaitez stocker votre table de partitionnement, comment vos clients la chargeront et recevront les mises à jour lors des changements, et comment elle sera partagée entre plusieurs clients. Si la table de partitionnement ne change pas souvent, vous pouvez simplement l'enregistrer dans votre fichier de configuration d'application. 

Sinon, vous pouvez la stocker dans n'importe quel magasin persistant. Un modèle de conception courant que nous avons vu dans la production consiste à sérialiser les tables de partitionnement au format JSON et à les stocker également dans les collections DocumentDB. Les clients peuvent ensuite mettre en cache la table de partitionnement pour éviter des aller et retour supplémentaires, puis interroger régulièrement les modifications. Si vos clients peuvent modifier la carte de partitions (shard map), assurez-vous qu'ils utilisent un schéma d'affectation de noms cohérent et l'accès concurrentiel optimiste (eTags) pour autoriser des mises à jour cohérentes dans le mappage de partitions.

## Ajout et suppression de partitions

Avec DocumentDB, vous pouvez ajouter et supprimer à tout moment des collections de créations, et les utiliser pour stocker les nouvelles données entrantes ou rééquilibrer les données disponibles sur les collections existantes. Pour le nombre de collections, consultez la page [Limites][documentdb-limits]. Vous pouvez toujours nous contacter pour augmenter ces limites.

L'ajout et la suppression d'une nouvelle partition avec le partitionnement par recherche et par plage sont des opérations simples. Par exemple, pour ajouter une nouvelle région géographique ou une nouvelle plage de temps pour les données récentes, il vous suffit d'ajouter les nouvelles partitions au mappage de partitions. Le fractionnement d'une partition existante en plusieurs partitions ou la fusion de deux partitions nécessitent un petit effort supplémentaire. Vous devez 

- soit mettre la partition hors connexion pour les lectures ;
- soit acheminer les lectures vers les deux partitions en utilisant l'ancienne configuration de partitionnement, ainsi que la nouvelle configuration de partitionnement pendant la migration. Notez que les transactions et les garanties de niveau de cohérence ne seront pas disponibles avant la fin de la migration.

Le hachage est relativement plus compliqué pour l'ajout et la suppression de partitions. Les techniques de hachage simples provoquent des mélanges et nécessitent le déplacement de la plupart des données. L'utilisation du **hachage cohérent** garantit que seule une fraction des données doit être déplacée.

Une façon relativement facile d'ajouter de nouvelles partitions sans nécessiter le déplacement des données consiste à " déverser " vos données dans  une nouvelle collection, puis à distribuer les demandes entre les anciennes et nouvelles collections. Cette approche, cependant, doit être utilisée uniquement dans les rares situations (débordement dans les charges de pointe et stockage temporaire des données jusqu'à ce qu'elles puissent être déplacées, par exemple).

### Étapes suivantes
Dans cet article, nous avons introduit certaines techniques courantes pour le partitionnement des données avec DocumentDB et indiqué quand utiliser telle technique ou combinaison de techniques. Commencez avec l'un des [Kits de développement logiciel (SDK)](https://msdn.microsoft.com/library/azure/dn781482.aspx) pris en charge et contactez-nous via les [forums de support MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB) si vous avez des questions.


<!--HONumber=47-->
