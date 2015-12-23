<properties 
	pageTitle="Kit de développement logiciel (SDK) Java DocumentDB | Microsoft Azure" 
	description="Découvrez le Kit de développement logiciel (SDK) Java, y compris les dates de lancement, les dates de suppression et les modifications apportées entre chaque version du Kit de développement logiciel (SDK) Java DocumentDB." 
	services="documentdb" 
	documentationCenter="java" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="12/07/2015" 
	ms.author="ryancraw"/>

# Kit de développement logiciel (SDK) DocumentDB

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##Kit de développement logiciel (SDK) Java DocumentDB

<table> <tr><td>**Télécharger**</td><td>[Maven](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)</td></tr> <tr><td>**Contribuer**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr> <tr><td>**Documentation**</td><td>[Documentation de référence pour le Kit de développement logiciel (SDK) Java](http://azure.github.io/azure-documentdb-java/)</td></tr> <tr><td>**Prise en main**</td><td>[Prise en main du Kit de développement logiciel (SDK) Java](documentdb-java-application.md)</td></tr> <tr><td>**Runtime actuellement pris en charge**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr> </table></br>

## Notes de publication

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

Les nouvelles fonctionnalités et fonctions, et les optimisations sont uniquement ajoutées au Kit de développement logiciel (SDK) actuel. Par conséquent, il est recommandé de toujours passer à la dernière version du SDK dès que possible.

Toute requête à DocumentDB utilisant un Kit de développement logiciel (SDK) supprimé est rejetée par le service.

> [AZURE.WARNING]Toutes les versions du Kit de développement logiciel (SDK) Azure DocumentDB pour Java antérieures à la version **1.0.0** seront supprimées le **29 février 2016**.

<br/>

| Version | Date de lancement | Date de suppression 
| ---	  | ---	         | ---
| [1\.5.0](#1.5.0) | 4 décembre 2015  |---
| [1\.4.0](#1.4.0) | 5 octobre 2015 |---
| [1\.3.0](#1.3.0) | 5 octobre 2015 |---
| [1\.2.0](#1.2.0) | 5 août 2015 |---
| [1\.1.0](#1.1.0) | 9 juillet 2015 |---
| [1\.0.1](#1.0.1) | 12 mai 2015 |---
| [1\.0.0](#1.0.0) | 7 avril 2015 |---
| 0.9.5-version préliminaire | 9 mars 2015 | 29 février 2016
| 0.9.4-version préliminaire | 17 février 2015 | 29 février 2016
| 0.9.3-version préliminaire | 13 janvier 2015 | 29 février 2016
| 0.9.2-version préliminaire | 19 décembre 2014 | 29 février 2016
| 0.9.1-version préliminaire | 19 décembre 2014 | 29 février 2016
| 0.9.0-version préliminaire | 10 décembre 2014 | 29 février 2016

## Forum Aux Questions
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Voir aussi

Pour en savoir plus sur DocumentDB, consultez la page du service [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_1210_2015-->
