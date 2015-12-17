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
	ms.date="11/16/2015" 
	ms.author="ryancraw"/>

# Kit de développement logiciel (SDK) DocumentDB

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##Kit de développement logiciel (SDK) DocumentDB Node.js

<table> <tr><td>**Téléchargement**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr> <tr><td>**Contribution**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr> <tr><td>**Documentation**</td><td>[Documentation de référence du Kit de développement logiciel (SDK) Node.js](http://azure.github.io/azure-documentdb-node/)</td></tr> <tr><td>**Échantillons**</td><td>[Échantillons de code Node.js](https://github.com/Azure/azure-documentdb-node/tree/master/samples)</td></tr> <tr><td>**Prise en main**</td><td>[Prise en main du Kit de développement logiciel (SDK) Node.js](documentdb-nodejs-get-started.md)</td></tr> <tr><td>**Plateforme actuellement prise en charge**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr> </table></br>

## Notes de publication

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
- Ajout du nouvel en-tête « progression de la transformation de l’index » à ResourceResponse.

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
Microsoft fournira une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

Les nouvelles fonctionnalités et fonctions, et les optimisations sont uniquement ajoutées au Kit de développement logiciel (SDK) actuel. Par conséquent, il est recommandé de toujours passer à la dernière version du SDK dès que possible.

Toute requête à DocumentDB utilisant un Kit de développement logiciel (SDK) supprimé est rejetée par le service.

> [AZURE.WARNING]
Toutes les versions du Kit de développement logiciel (SDK) Azure DocumentDB pour Node.js antérieures à la version **1.0.0** seront supprimées le **29 février 2016**.

<br/>

| Version | Date de lancement | Date de suppression 
| ---	  | ---	         | ---
| [1\.4.0](#1.4.0) | 6 octobre 2015 |---
| [1\.3.0](#1.3.0) | 6 octobre 2015 |---
| [1\.2.2](#1.2.2) | 10 septembre 2015 |---
| [1\.2.1](#1.2.1) | 15 août 2015 |---
| [1\.2.0](#1.2.0) | 5 août 2015 |---
| [1\.1.0](#1.1.0) | 9 juillet 2015 |---
| [1\.0.3](#1.0.3) | 4 juin 2015 |---
| [1\.0.2](#1.0.2) | 23 mai 2015 |---
| [1\.0.1](#1.0.1) | 15 mai 2015 |---
| [1\.0.0](#1.0.0) | 8 avril 2015 |---
| 0.9.4-version préliminaire | 6 avril 2015 | 29 février 2016
| 0.9.3-version préliminaire | 14 janvier 2015 | 29 février 2016
| 0.9.2-version préliminaire | 18 décembre 2014 | 29 février 2016
| 0.9.1-version préliminaire | 22 août 2014 | 29 février 2016
| 0.9.0-version préliminaire | 21 août 2014 | 29 février 2016


## Forum Aux Questions
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Voir aussi

Pour en savoir plus sur DocumentDB, consultez la page du service [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_1203_2015-->
