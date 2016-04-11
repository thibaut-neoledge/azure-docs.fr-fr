<properties 
	pageTitle="Kit de développement logiciel (SDK) Node.js de DocumentDB | Microsoft Azure" 
	description="Découvrez le Kit de développement logiciel (SDK) Node.js, y compris les dates de lancement, les dates de suppression et les modifications apportées entre chaque version du Kit de développement logiciel (SDK) Node.js DocumentDB." 
	services="documentdb" 
	documentationCenter="nodejs" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="03/30/2016" 
	ms.author="ryancraw"/>

# Kit de développement logiciel (SDK) DocumentDB

> [AZURE.SELECTOR]
- [KIT DE DÉVELOPPEMENT LOGICIEL (SDK) .NET](documentdb-sdk-dotnet.md)
- [Kit de développement logiciel (SDK) Node.js](documentdb-sdk-node.md)
- [Kit de développement logiciel (SDK) Java](documentdb-sdk-java.md)
- [Kit de développement logiciel (SDK) Python](documentdb-sdk-python.md)

##Kit de développement logiciel (SDK) DocumentDB Node.js

<table>
<tr><td>**Télécharger**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>
<tr><td>**Participer**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>
<tr><td>**Documentation**</td><td>[Documentation de référence du SKD Node.js](http://azure.github.io/azure-documentdb-node/)</td></tr>
<tr><td>**Exemples**</td><td>[Exemples de code Node.js](https://github.com/Azure/azure-documentdb-node/tree/master/samples)</td></tr>
<tr><td>**Prise en main**</td><td>[Prise en main du SDK Node.js] (documentdb-nodejs-get-started.md)</td></tr>
<tr><td>**Plateforme actuellement prise en charge**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

##Notes de publication

###<a name="1.6.0"/>1.6.0</a>
- Implémentation des [collections partitionnées](documentdb-partition-data.md) et des [niveaux de performances définis par l’utilisateur](documentdb-performance-levels.md). 

###<a name="1.5.6"/>1.5.6</a>

- Résolution du bogue RangePartitionResolver.resolveForRead où les liens n’étaient pas renvoyés en raison d’une mauvaise concaténation des résultats

###<a name="1.5.5"/>1.5.5</a>

- Résout hashParitionResolver resolveForRead() : levait une exception si aucune clé de partition n’était fournie, au lieu de renvoyer une liste de tous les liens enregistrés.

###<a name="1.5.4"/>1.5.4</a>

- Résolution du problème [#100](https://github.com/Azure/azure-documentdb-node/issues/100) : agent HTTPS dédié : évitez de modifier l’agent global pour DocumentDB. Utilisez un agent dédié pour toutes les demandes de la bibliothèque.

###<a name="1.5.3"/>1.5.3</a>

- Résolution du problème [#81](https://github.com/Azure/azure-documentdb-node/issues/81) : gestion correcte des tirets dans les ID de média.

###<a name="1.5.2"/>1.5.2</a>

- Résolution du problème [#95](https://github.com/Azure/azure-documentdb-node/issues/95) : avertissement de fuite de l’écouteur EventEmitter.

###<a name="1.5.1"/>1.5.1</a>

- Résolution du problème [#92](https://github.com/Azure/azure-documentdb-node/issues/90) : dossier Hash renommé en hash pour les systèmes sensibles à la casse.

### <a name="1.5.0"/>1.5.0</a>

- Implémentation de la prise en charge du partitionnement par un ajout de programmes de résolution de partitions de hachage et de plage

### <a name="1.4.0"/>1.4.0</a>

- Implémentation de l’opération Upsert. Nouvelles méthodes upsertXXX sur documentClient. 

### <a name="1.3.0"/>1.3.0</a>

- Ignorée pour aligner le numéro de version avec les autres Kits de développement logiciel (SDK)

### <a name="1.2.2"/>1.2.2</a>

- Fractionnement du wrapper des promesses Q dans un nouveau référentiel
- Mise à jour vers le fichier de package pour le registre npm

### <a name="1.2.1"/>1.2.1</a>

- Implémentation du routage basé sur l’ID.
- Résolution du problème [#49](https://github.com/Azure/azure-documentdb-node/issues/49) - propriété actuelle en conflit avec la méthode current()

### <a name="1.2.0"/>1.2.0</a>

- Ajout de la prise en charge de l’index géospatial.
- Validation de la propriété ID pour toutes les ressources. Les ID des ressources ne peuvent pas contenir les caractères ?, /, #, &#47;&#47; ou se terminer par un espace. 
- Ajout du nouvel en-tête « progression de la transformation de l’index » à ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>

- Implémentation de la stratégie d’indexation V2

### <a name="1.0.3"/>1.0.3</a>

- Problème [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - Configurations eslint et grunt implémentées dans le Kit de développement logiciel (SDK) central et de promesse

### <a name="1.0.2"/>1.0.2</a>

- Problème [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - Le wrapper de promesses n’inclut pas d’en-tête avec erreur.

### <a name="1.0.1"/>1.0.1</a>

- Mise en œuvre de la possibilité de créer une requête pour les conflits en ajoutant readConflicts, readConflictAsync et queryConflicts
- Mise à jour de la documentation de l’API
- Problème [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - Erreur client.createDocumentAsync  

### <a name="1.0.0"/>1.0.0</a>

- Kit de développement logiciel (SDK) GA

## Dates de lancement et de suppression
Microsoft fournira une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

Les nouvelles fonctionnalités et fonctions, et les optimisations sont uniquement ajoutées au Kit de développement logiciel (SDK) actuel. Par conséquent, il est recommandé de toujours passer à la dernière version du SDK dès que possible.

Toute requête à DocumentDB utilisant un Kit de développement logiciel (SDK) supprimé est rejetée par le service.

> [AZURE.WARNING]
Toutes les versions du Kit de développement logiciel (SDK) Azure DocumentDB pour Node.js antérieures à la version **1.0.0** seront supprimées le **29 février 2016**.

<br/>

| Version | Date de lancement | Date de suppression 
| ---	  | ---	         | ---
| [1.6.0](#1.6.0) | 29 mars 2016 |--- 
| [1.5.6](#1.5.6) | 08 mars 2016 |--- 
| [1.5.5](#1.5.5) | 02 février 2016 |--- 
| [1.5.4](#1.5.4) | 1er février 2016 |--- 
| [1.5.2](#1.5.2) | 26 janvier 2016 |--- 
| [1.5.2](#1.5.2) | 22 janvier 2016 |--- 
| [1.5.1](#1.5.1) | 04 janvier 2016 |--- 
| [1.5.0](#1.5.0) | 31 décembre 2015 |--- 
| [1.4.0](#1.4.0) | 06 octobre 2015 |--- 
| [1.3.0](#1.3.0) | 06 octobre 2015 |--- 
| [1.2.2](#1.2.2) | 10 septembre 2015 |--- 
| [1.2.1](#1.2.1) | 15 août 2015 |--- 
| [1.2.0](#1.2.0) | 05 août 2015 |--- 
| [1.1.0](#1.1.0) | 09 juillet 2015 |--- 
| [1.0.3](#1.0.3) | 04 juin 2015 |--- 
| [1.0.2](#1.0.2) | 23 mai 2015 |--- 
| [1.0.1](#1.0.1) | 15 mai 2015 |--- 
| [1.0.0](#1.0.0) | 08 avril 2015 |--- 
| Version préliminaire 0.9.4 | 06 avril 2015 | 29 février 2016 
| Version préliminaire 0.9.3 | 14 janvier 2015 | 29 février 2016 
| Version préliminaire 0.9.2 | 18 décembre 2014 | 29 février 2016 
| Version préliminaire 0.9.1 | 22 août 2014 | 29 février 2016 
| Version préliminaire 0.9.0 | 21 août 2014 | 29 février 2016


## Forum Aux Questions
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Voir aussi

Pour en savoir plus sur DocumentDB, consultez la page du service [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0330_2016-->