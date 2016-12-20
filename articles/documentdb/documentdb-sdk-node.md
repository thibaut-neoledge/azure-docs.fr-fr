---
title: "API et Kit de développement logiciel (SDK) Node.js de DocumentDB | Microsoft Docs"
description: "Découvrez l&quot;API et le Kit de développement logiciel (SDK) Node.js, y compris les dates de lancement, les dates de suppression et les modifications apportées entre chaque version du Kit de développement logiciel (SDK) Node.js DocumentDB."
services: documentdb
documentationcenter: nodejs
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 9d5621fa-0e11-4619-a28b-a19d872bcf37
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 10/03/2016
ms.author: rnagpal
translationtype: Human Translation
ms.sourcegitcommit: 5e182811adc29ae00d40355bb4813c30eb0b904c
ms.openlocfilehash: fbe124f1df522426d26f21333bf4a50d32103e33


---
# <a name="documentdb-apis-and-sdks"></a>API DocumentDB et Kits de développement logiciel (SDK)
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.JS](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/en-us/rest/api/documentdb/)
> * [API REST Resource Provider](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

## <a name="documentdb-nodejs-api-and-sdk"></a>API et Kit de développement logiciel (SDK) DocumentDB Node.js
<table>

<tr><td>**Téléchargement du Kit de développement logiciel (SDK)**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>

<tr><td>**Documentation de l’API**</td><td>[Documentation de référence de l’API Node.js](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>

<tr><td>**Instructions d’installation du Kit de développement logiciel (SDK)**</td><td>[Instructions d’installation](http://azure.github.io/azure-documentdb-node/)</td></tr>

<tr><td>**Contribution au Kit de développement logiciel (SDK)**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>

<tr><td>**Exemples**</td><td>[Exemples de code Node.js](documentdb-nodejs-samples.md)</td></tr>

<tr><td>**Didacticiel de prise en main**</td><td>[Prise en main du Kit de développement logiciel (SDK) Node.js](documentdb-nodejs-get-started.md)</td></tr>

<tr><td>**Didacticiel d’application web**</td><td>[Création d’une application web Node.js avec DocumentDB](documentdb-nodejs-application.md)</td></tr>

<tr><td>**Plateforme actuellement prise en charge**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

## <a name="release-notes"></a>Notes de publication
### <a name="a-name11001100a"></a><a name="1.10.0"/>1.10.0</a>
* Ajout de la prise en charge des requêtes parallèles sur plusieurs partitions.
* Ajout de la prise en charge des requêtes TOP/ORDER BY pour les collections partitionnées.

### <a name="a-name190190a"></a><a name="1.9.0"/>1.9.0</a>
* Ajout de la prise en charge d’une stratégie de nouvelle tentative pour les requêtes limitées. (Les requêtes limitées reçoivent une exception de taux de requête excessif, code d’erreur 429.) Par défaut, DocumentDB accepte neuf nouvelles tentatives pour chaque requête lorsque le code d’erreur 429 est retourné, conformément au temps retryAfter spécifié dans l’en-tête de réponse. Il est désormais possible de définir un intervalle fixe de nouvelle tentative dans la propriété RetryOptions sur l’objet ConnectionPolicy, si vous souhaitez ignorer le temps retryAfter retourné par le serveur entre chaque nouvelle tentative. DocumentDB attend maintenant au maximum 30 secondes pour chaque requête limitée (quel que soit le nombre de nouvelles tentatives) et renvoie la réponse avec un code d’erreur 429. Cette durée peut également être remplacée dans la propriété RetryOptions sur l’objet ConnectionPolicy.
* DocumentDB retourne maintenant x-ms-throttle-retry-count and x-ms-throttle-retry-wait-time-ms comme en-têtes de réponse dans chaque requête afin d’indiquer le nombre limite de nouvelles tentatives et le cumul de temps d’attente observé par la requête entre les nouvelles tentatives.
* La classe RetryOptions a été ajoutée pour exposer la propriété RetryOptions sur la classe ConnectionPolicy, qui peut être utilisée pour substituer certaines des options de nouvelle tentative par défaut.

### <a name="a-name180180a"></a><a name="1.8.0"/>1.8.0</a>
* Ajout de la prise en charge des comptes de base de données de plusieurs régions.

### <a name="a-name170170a"></a><a name="1.7.0"/>1.7.0</a>
* Ajout de la fonctionnalité de durée de vie (TTL) pour les documents.

### <a name="a-name160160a"></a><a name="1.6.0"/>1.6.0</a>
* Implémentation des [collections partitionnées](documentdb-partition-data.md) et des [niveaux de performances définis par l’utilisateur](documentdb-performance-levels.md).

### <a name="a-name156156a"></a><a name="1.5.6"/>1.5.6</a>
* Résolution du bogue RangePartitionResolver.resolveForRead là où les liens n’étaient pas renvoyés en raison d’une concaténation incorrecte des résultats.

### <a name="a-name155155a"></a><a name="1.5.5"/>1.5.5</a>
* Résout hashParitionResolver resolveForRead() : levait une exception si aucune clé de partition n’était fournie, au lieu de renvoyer une liste de tous les liens enregistrés.

### <a name="a-name154154a"></a><a name="1.5.4"/>1.5.4</a>
* Résolution du problème [n° 100](https://github.com/Azure/azure-documentdb-node/issues/100) : Agent HTTPS dédié : éviter de modifier l’agent global pour DocumentDB. Utilisez un agent dédié pour toutes les demandes de la bibliothèque.

### <a name="a-name153153a"></a><a name="1.5.3"/>1.5.3</a>
* Résolution du problème [n° 81](https://github.com/Azure/azure-documentdb-node/issues/81) : gestion correcte des tirets dans les ID de média.

### <a name="a-name152152a"></a><a name="1.5.2"/>1.5.2</a>
* Résolution du problème [n° 95](https://github.com/Azure/azure-documentdb-node/issues/95) : avertissement de fuite de l’écouteur EventEmitter.

### <a name="a-name151151a"></a><a name="1.5.1"/>1.5.1</a>
* Résolution du problème [n° 92](https://github.com/Azure/azure-documentdb-node/issues/90) : dossier Hash renommé en hash pour les systèmes respectant la casse.

### <a name="a-name150150a"></a><a name="1.5.0"/>1.5.0</a>
* Implémentation de la prise en charge du partitionnement via l’ajout de programmes de résolution de partitions de hachage et de plage.

### <a name="a-name140140a"></a><a name="1.4.0"/>1.4.0</a>
* Implémentation de l’opération Upsert. Nouvelles méthodes upsertXXX sur documentClient.

### <a name="a-name130130a"></a><a name="1.3.0"/>1.3.0</a>
* Ignorée pour aligner le numéro de version avec les autres Kits de développement logiciel (SDK).

### <a name="a-name122122a"></a><a name="1.2.2"/>1.2.2</a>
* Fractionnement du wrapper des promesses Q dans un nouveau dépôt.
* Mise à jour du fichier de package pour le Registre npm.

### <a name="a-name121121a"></a><a name="1.2.1"/>1.2.1</a>
* Implémentation du routage basé sur l’ID.
* Résolution du problème [n° 49](https://github.com/Azure/azure-documentdb-node/issues/49) - propriété actuelle en conflit avec la méthode current().

### <a name="a-name120120a"></a><a name="1.2.0"/>1.2.0</a>
* Ajout de la prise en charge de l’index géospatial.
* Validation de la propriété ID pour toutes les ressources. Les ID des ressources ne peuvent pas contenir les caractères ?, /, #, &#47;&#47; ou se terminer par un espace.
* Ajout du nouvel en-tête « progression de la transformation de l’index » à ResourceResponse.

### <a name="a-name110110a"></a><a name="1.1.0"/>1.1.0</a>
* Implémente la stratégie d’indexation V2.

### <a name="a-name103103a"></a><a name="1.0.3"/>1.0.3</a>
* Problème [n° 40](https://github.com/Azure/azure-documentdb-node/issues/40) : implémentation des configurations eslint et grunt dans le Kit de développement logiciel (SDK) principal et de promesse.

### <a name="a-name102102a"></a><a name="1.0.2"/>1.0.2</a>
* Problème [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - Le wrapper de promesses n’inclut pas d’en-tête avec erreur.

### <a name="a-name101101a"></a><a name="1.0.1"/>1.0.1</a>
* Implémentation de la possibilité de créer une requête pour les conflits en ajoutant readConflicts, readConflictAsync et queryConflicts.
* Mise à jour de la documentation de l’API.
* Problème [n° 41](https://github.com/Azure/azure-documentdb-node/issues/41) : Erreur client.createDocumentAsync.

### <a name="a-name100100a"></a><a name="1.0.0"/>1.0.0</a>
* Kit de développement logiciel (SDK) GA

## <a name="release-retirement-dates"></a>Dates de lancement et de suppression
Microsoft fournira une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

Les nouvelles fonctionnalités et fonctions, et les optimisations sont uniquement ajoutées au Kit de développement logiciel (SDK) actuel. Par conséquent, il est recommandé de toujours passer à la dernière version du Kit de développement logiciel (SDK) dès que possible.

Toute requête à DocumentDB utilisant un Kit SDK supprimé est rejetée par le service.

> [!WARNING]
> Toutes les versions du Kit de développement logiciel (SDK) Azure DocumentDB pour Node.js antérieures à la version **1.0.0** seront supprimées le **29 février 2016**.
> 
> 

<br/>

| Version | Date de lancement | Date de suppression |
| --- | --- | --- |
| [1.10.0](#1.10.0) |3 octobre 2016 |--- |
| [1.9.0](#1.9.0) |7 juillet 2016 |--- |
| [1.8.0](#1.8.0) |14 juin 2016 |--- |
| [1.7.0](#1.7.0) |26 avril 2016 |--- |
| [1.6.0](#1.6.0) |29 mars 2016 |--- |
| [1.5.6](#1.5.6) |8 mars 2016 |--- |
| [1.5.5](#1.5.5) |2 février 2016 |--- |
| [1.5.4](#1.5.4) |1er février 2016 |--- |
| [1.5.2](#1.5.2) |26 janvier 2016 |--- |
| [1.5.2](#1.5.2) |22 janvier 2016 |--- |
| [1.5.1](#1.5.1) |4 janvier 2016 |--- |
| [1.5.0](#1.5.0) |31 décembre 2015 |--- |
| [1.4.0](#1.4.0) |6 octobre 2015 |--- |
| [1.3.0](#1.3.0) |6 octobre 2015 |--- |
| [1.2.2](#1.2.2) |10 septembre 2015 |--- |
| [1.2.1](#1.2.1) |15 août 2015 |--- |
| [1.2.0](#1.2.0) |5 août 2015 |--- |
| [1.1.0](#1.1.0) |9 juillet 2015 |--- |
| [1.0.3](#1.0.3) |4 juin 2015 |--- |
| [1.0.2](#1.0.2) |23 mai 2015 |--- |
| [1.0.1](#1.0.1) |15 mai 2015 |--- |
| [1.0.0](#1.0.0) |8 avril 2015 |--- |
| Version préliminaire 0.9.4 |6 avril 2015 |29 février 2016 |
| Version préliminaire 0.9.3 |14 janvier 2015 |29 février 2016 |
| Version préliminaire 0.9.2 |18 décembre 2014 |29 février 2016 |
| Version préliminaire 0.9.1 |22 août 2014 |29 février 2016 |
| Version préliminaire 0.9.0 |21.08.14 |29 février 2016 |

## <a name="faq"></a>Forum Aux Questions
[!INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur DocumentDB, consultez la page du service [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) .




<!--HONumber=Nov16_HO3-->


