<properties 
	pageTitle="Cas d'utilisation Data Factory - Recommandations de produits" 
	description="Découvrez un cas d'utilisation implémenté à l'aide d’Azure Data Factory et d'autres services." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2015" 
	ms.author="spelluru"/>

# Cas d’utilisation - Recommandations de produits 

Azure Data Factory est un des nombreux services permettent d'implémenter la solution d’accélérateurs Cortana Analytics Suite. Consultez la page [Cortana Analytics Suite](http://www.microsoft.com/cortanaanalytics) pour plus de détails concernant cette suite. Dans ce document, nous décrivons un cas d'utilisation courant que les utilisateurs Azure ont déjà résolu et implémenté à l'aide d’Azure Data Factory et d'autres services Cortana Analytics.

## Scénario

Les détaillants en ligne souhaitent généralement inciter leurs clients à acheter des produits en leur présentant les articles qu’ils sont les plus susceptibles d’apprécier et par conséquent d’acheter. Pour cela, ils ont besoin de personnaliser l’expérience en ligne des utilisateurs en leur proposant des recommandations de produits spécifiques à chaque utilisateur. Ces recommandations doivent prendre en compte les données relatives à leurs habitudes d’achat actuelles et passées, des informations sur le produit, les marques récemment lancées, et les données de segmentation par produit et par client afin de présenter le produit qui répond le mieux aux attentes du client. En outre, les détaillants peuvent fournir des recommandations de produits basées sur l'analyse des habitudes d'utilisation globale de tous leurs utilisateurs.

L'objectif est d'optimiser le processus de conversion des visites en ventes effectives et d’augmenter ainsi le chiffre d’affaires. Ils y parviennent en proposant des recommandations de produits contextuelles basées sur les habitudes, les centres d’intérêt et les actions du client. Pour ce cas d’utilisation, nous utilisons l’exemple de détaillants en ligne qui souhaitent améliorer l’expérience en ligne de leurs clients, mais ces principes s'appliquent à toute entreprise cherchant à fidéliser ses clients autour de ses produits et services et à améliorer leur expérience d’achat grâce à des recommandations de produits personnalisées.

## Défis

Les détaillants en ligne doivent relever de nombreux défis lorsqu’elles tentent d’implémenter ce type de cas d'utilisation. Premièrement, des données de différentes tailles et formes doivent être reçues à partir de plusieurs sources de données, à la fois en local et dans le cloud, pour capturer les données de produits, les données d’historique des habitudes des utilisateurs, et les données des utilisateurs à mesure qu’ils parcourent la boutique en ligne.

Deuxièmement, les recommandations de produits personnalisées doivent être anticipées et évaluées de manière raisonnable et précise. Outre les informations sur les produits, les marques, les habitudes du client et les données du navigateur, les détaillants en ligne doivent également afficher les commentaires d’anciens clients afin d’aider l’utilisateur à mieux cibler les produits qui lui conviennent.

Troisièmement, les recommandations doivent être immédiatement communiquées à l'utilisateur pour garantir une expérience d'achat et de navigation transparente, avec les informations les plus récentes et les plus pertinentes. Enfin, les détaillants doivent mesurer l'efficacité de leur approche par un suivi des ventes croisées et incitatives réalisées, et ajuster leurs prochaines recommandations.

## Vue d’ensemble de la solution

Cet exemple de cas d'utilisation a été résolu et implémenté par de vrais utilisateurs Azure avec Azure Data Factory et d’autres services Cortana Analytics, y compris [HDInsight](http://azure.microsoft.com/services/hdinsight/) et [Power BI](https://powerbi.microsoft.com/) pour recevoir, préparer, transformer, analyser et publier les données finales.

Le détaillant en ligne utilise un magasin d'objets blob Azure, un serveur SQL local, une base de données SQL Azure et un mini-Data Warehouse relationnel options de stockage des données tout au long du flux de travail. Le magasin d'objets blob contient des informations sur les clients, des données sur les habitudes des clients et des informations sur les produits. Les informations sur les produits incluent des informations sur les marques de produits ainsi qu’un catalogue de produits stocké localement dans un SQL Data Warehouse.

Comme le montre la figure suivante, toutes les données sont combinées et chargées dans un système de recommandations de produit pour fournir des recommandations personnalisées selon les intérêts et les actions du client à mesure que l'utilisateur parcourt le catalogue de produits sur le site web du détaillant. Le client peut également voir des produits similaires à celui qu'il recherche, selon des modèles d'utilisation du site web qui ne sont spécifiques à aucun utilisateur

![diagramme de cas d'utilisation](./media/data-factory-product-reco-usecase/diagram-1.png)

Des gigaoctets de fichiers journaux web bruts sont générés chaque jour sous forme de fichiers semi-structurés, à partir du site web du détaillant en ligne. Les fichiers journaux web bruts et les informations sur les clients et le catalogue de produits sont transmises régulièrement à un compte de stockage d'objets blob Azure en utilisant le déplacement de données déployées globalement Data Factory comme service. Les fichiers journaux bruts de la journée sont partitionnés (par année et mois) dans le stockage d’objets blob pour un stockage à long terme. [Azure HDInsight](http://azure.microsoft.com/services/hdinsight/)(Hadoop en tant que service) est utilisé pour partitionner les fichiers journaux bruts (pour améliorer la gestion, la disponibilité et les performances) dans le magasin d'objets blob et traiter à grande échelle les journaux réceptionnés à l’aide des scripts Hive et Pig. Les données des journaux web partitionnés sont ensuite traités pour extraire les entrées nécessaires pour que le système de recommandation de Machine Learning génère des recommandations de produits personnalisées.

Le système de recommandation utilisé pour Machine Learning dans cet exemple est une plateforme de recommandation Machine Learning open source d’[Apache Mahout](http://mahout.apache.org/). Notez que n’importe quel modèle [Azure Machine Learning](http://azure.microsoft.com/services/machine-learning/) ou modèle personnalisé peut être appliqué. Le modèle Mahout sert à prédire la similarité entre des éléments présents sur le site web du détaillant en fonction de modèles d'utilisation globaux, et à générer des recommandations personnalisées selon un utilisateur individuel.

Enfin, le jeu de résultats des recommandations de produits personnalisées est déplacé vers un mini-Data Warehouse relationnel afin d’être utilisé par le site web du détaillant. Le jeu de résultats peut également être accessible directement depuis le stockage d’objets blob par une autre application, ou déplacé vers des magasins supplémentaires pour d'autres consommateurs et cas d'utilisation.

## Avantages

En optimisant leur stratégie de recommandation de produits et en l’alignant sur les objectifs commerciaux, la solution répond aux besoins de merchandising et de marketing du détaillant en ligne. En outre, les détaillants ont pu gérer et rendre opérationnel le flux de travail de recommandation de produits de manière efficace, fiable et économique. Ainsi, ils ont été en mesure de mettre à jour leur modèle et d’optimiser son efficacité en s’appuyant sur les ventes concrétisées. Grâce à Azure Data Factory, ils ont pu se débarrasser de leur système de gestion manuelle des ressources de cloud, à la fois long et coûteux, pour passer à une gestion des ressources de cloud à la demande, économisant ainsi du temps, de l’argent, tout en réduisant le délai de déploiement de la solution. Ils ont pu visualiser et dépanner facilement les vues de lignage des données et l'intégrité du service opérationnel grâce à une interface utilisateur de surveillance et gestion Data Factory intuitive, disponible sur le portail Azure. Leur solution peut maintenant être planifiée et gérée de sorte que les données terminées sont produites et transmises de façon fiable à leurs utilisateurs. Par ailleurs, les données et les dépendances de traitement sont gérées automatiquement, sans intervention humaine.

Grâce à cette expérience d'achat personnalisée, le détaillant en ligne a créé une expérience client plus compétitive et plus attrayante, augmentant ainsi les ventes et la satisfaction globale des clients.



  

<!---HONumber=Oct15_HO3-->