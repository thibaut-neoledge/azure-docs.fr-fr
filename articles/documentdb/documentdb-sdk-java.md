<properties 
	pageTitle="Kit de développement logiciel (SDK) Java DocumentDB | Microsoft Azure" 
	description="Découvrez le Kit de développement logiciel (SDK) Java, y compris les dates de lancement, les dates de suppression et les modifications apportées entre chaque version du Kit de développement logiciel (SDK) Java DocumentDB." 
	services="documentdb" 
	documentationCenter="java" 
	authors="aliuy" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="06/30/2016" 
	ms.author="andrl"/>

# Kit SDK DocumentDB

> [AZURE.SELECTOR]
- [Kit SDK .NET](documentdb-sdk-dotnet.md)
- [Kit SDK Node.js](documentdb-sdk-node.md)
- [Kit SDK Java](documentdb-sdk-java.md)
- [Kit de développement logiciel (SDK) Python](documentdb-sdk-python.md)

##Kit de développement logiciel (SDK) Java DocumentDB

<table>
<tr><td>**Télécharger**</td><td>[Maven](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)</td></tr>
<tr><td>**Participer**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>
<tr><td>**Documentation**</td><td>[Documentation de référence du Kit de développement logiciel (SDK) Java](http://azure.github.io/azure-documentdb-java/)</td></tr>
<tr><td>**Prise en main**</td><td>[Prise en main du Kit de développement logiciel (SDK) Java](documentdb-java-application.md)</td></tr>
<tr><td>**Runtime actuellement pris en charge**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## Notes de publication

### <a name="1.8.1"/>[1\.8.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.1)
  - Correction d’un bogue dans PartitionKeyDefinitionMap pour la mise en cache de collections de partitions uniques, sans aucune extraction supplémentaire des demandes de clés de partition.
  - Correction d’un bogue pour l’absence de nouvelle tentative en cas de valeur de clé de partition incorrecte.

### <a name="1.8.0"/>[1\.8.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.0)
  - Ajout de la prise en charge des comptes de base de données de plusieurs régions.
  - Ajout de la prise en charge d’une nouvelle tentative automatique sur les requêtes limitées avec options de personnalisation du nombre maximum de tentatives et du délai d’attente maximum entre deux tentatives. Voir RetryOptions et ConnectionPolicy.getRetryOptions().
  - Propriété IPartitionResolver déconseillée basée sur un code de partitionnement personnalisé. Utilisez des collections partitionnées pour bénéficier d’un niveau de stockage et de débit supérieur.

### <a name="1.7.1"/>[1\.7.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.1)
- Ajout de la prise en charge d’une stratégie de nouvelle tentative pour la limitation.

### <a name="1.7.0"/>[1\.7.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.0)
- Ajout de la prise en charge de la durée de vie (TTL) pour les documents.

### <a name="1.6.0"/>[1\.6.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.6.0)
- Implémentation des [collections partitionnées](documentdb-partition-data.md) et des [niveaux de performances définis par l’utilisateur](documentdb-performance-levels.md).

### <a name="1.5.1"/>[1\.5.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.1)
- Correction d’un bogue dans HashPartitionResolver pour générer des valeurs de hachage en little-endian dans un souci de cohérence avec les autres kits de développement logiciel.

### <a name="1.5.0"/>[1\.5.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.0)
- Ajoutez des programmes de résolution de partitions par hachage et par spécification de plages de valeurs pour vous aider lors du partitionnement des applications sur plusieurs partitions.

### <a name="1.4.0"/>[1\.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
- Implémenter l'opération Upsert. Nouvelles méthodes upsertXXX ajoutées pour prendre en charge la fonctionnalité Upsert.
- Implémenter l'ID en fonction du routage. Aucune modification d'API publique, toutes les modifications en interne.

### <a name="1.3.0"/>1.3.0
- Version ignorée pour aligner le numéro de version avec les autres Kits de développement logiciel (SDK)

### <a name="1.2.0"/>[1\.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
- Prise en charge de l'index géospatial
- Valide la propriété ID pour toutes les ressources. Les ID des ressources ne peuvent pas contenir les caractères ?, /, #, \\ ou se terminer par un espace.
- Ajoute le nouvel en-tête « progression de la transformation de l'index » à ResourceResponse.

### <a name="1.1.0"/>[1\.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
- Implémente la stratégie d'indexation V2

### <a name="1.0.0"/>[1\.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
- Kit de développement logiciel (SDK) GA

## Dates de lancement et de suppression
Microsoft fournira une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

Les nouvelles fonctionnalités et fonctions, et les optimisations sont uniquement ajoutées au Kit SDK actuel. Par conséquent, il est recommandé de toujours passer à la dernière version du SDK dès que possible.

Toute requête à DocumentDB utilisant un Kit de développement logiciel (SDK) supprimé est rejetée par le service.

> [AZURE.WARNING]
Toutes les versions du Kit de développement logiciel (SDK) Azure DocumentDB pour Java antérieures à la version **1.0.0** seront supprimées le **29 février 2016**.

<br/>

| Version | Date de lancement | Date de suppression 
| ---	  | ---	         | ---
| [1\.8.1](#1.8.1) | 30 juin 2016 |--- | [1\.8.0](#1.8.0) | 14 juin 2016 |--- | [1\.7.1](#1.7.1) | 30 avril 2016 |--- | [1\.7.0](#1.7.0) | 27 avril 2016 |--- | [1\.6.0](#1.6.0) | 29 mars 2016 |--- | [1\.5.1](#1.5.1) | 31 décembre 2015 |--- | [1\.5.0](#1.5.0) | 4 décembre 2015 |--- | [1\.4.0](#1.4.0) | 5 octobre 2015 |--- | [1\.3.0](#1.3.0) | 5 octobre 2015 |--- | [1\.2.0](#1.2.0) | 5 août 2015 |--- | [1\.1.0](#1.1.0) | 9 juillet 2015 |--- | [1\.0.1](#1.0.1) | 12 mai 2015 |--- | [1\.0.0](#1.0.0) | 7 avril 2015 |--- | 0.9.5-version préliminaire | 9 mars 2015 | 29 février 2016 | 0.9.4-version préliminaire | 17 février 2015 | 29 février 2016 | 0.9.3-version préliminaire | 13 janvier 2015 | 29 février 2016 | 0.9.2-version préliminaire | 19 décembre 2014 | 29 février 2016 | 0.9.1-version préliminaire | 19 décembre 2014 | 29 février 2016 | 0.9.0-version préliminaire | 10 décembre 2014 | 29 février 2016

## Forum Aux Questions
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Voir aussi

Pour en savoir plus sur DocumentDB, consultez la page du service [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0713_2016-->