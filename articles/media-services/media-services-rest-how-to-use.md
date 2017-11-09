---
title: "Vue d’ensemble de l’API REST Media Services Operations | Microsoft Docs"
description: "Vue d’ensemble de l’API REST Media Services"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/10/2017
ms.author: juliako
ms.openlocfilehash: eada8f2bcd2488d5ed36b0c24aa3d1b917815517
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="media-services-operations-rest-api-overview"></a>Vue d’ensemble de l’API REST Media Services Operations
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

L’API **REST de Media Services Operations** est utilisée pour créer des tâches, des ressources, des stratégies d’accès et d’autres opérations sur les objets dans un compte Media Service. Pour plus d’informations, consultez [Référence de l’API REST Media Services Operations](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Microsoft Azure Media Services est un service qui accepte les demandes HTTP OData et peut répondre dans les formats JSON ou atom+pub détaillés. Étant donné que Media Services est conforme aux règles de conception Azure, il existe un ensemble d’en-têtes HTTP obligatoires que chaque client doit utiliser lors de la connexion à Media Services, ainsi qu’un jeu d’en-têtes facultatifs pouvant être utilisé. Les sections suivantes décrivent les en-têtes et verbes HTTP que vous pouvez utiliser lors de la création de requêtes et de la réception des réponses à partir de Media Services.

Cette rubrique donne une vue d’ensemble de l’utilisation de REST v2 avec Media Services.

## <a name="considerations"></a>Considérations

Les considérations suivantes s'appliquent lors de l'utilisation de REST.

* Lors de l'interrogation des entités, il existe une limite de 1 000 entités retournées simultanément car l'API REST v2 publique limite les résultats des requêtes à 1 000 résultats. Vous devez utiliser **Skip** et **Take** (.NET)/ **top** (REST) comme décrit dans [cet exemple .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) et cet exemple [d’API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* Lors de l’utilisation de JSON et de la spécification d’utilisation du mot clé **metadata** dans la demande (par exemple pour faire référence à un objet lié), vous DEVEZ définir l’en-tête **Accept** au [format JSON Verbose](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (consultez l’exemple suivant). OData ne comprend pas la propriété **metadata** dans la demande, sauf si vous la définissez sur « verbose ».  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.11
        Authorization: Bearer <token> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>En-têtes de requête HTTP standard pris en charge par Media Services
Pour chaque appel dans Media Services, il existe un ensemble d’en-têtes requis, que vous devez inclure dans votre requête, et un ensemble d’en-têtes facultatifs que vous pouvez également inclure. Le tableau suivant répertorie les en-têtes requis :

| En-tête | Type | Valeur |
| --- | --- | --- |
| Autorisation |Support |Le support est le seul mécanisme d’autorisation accepté. La valeur doit également inclure le jeton d’accès fourni par ACS. |
| x-ms-version |Décimal |2.11 |
| DataServiceVersion |Décimal |3.0 |
| MaxDataServiceVersion |Décimal |3.0 |

> [!NOTE]
> Étant donné que Media Services utilise OData pour exposer son référentiel de métadonnées de ressources sous-jacent via les API REST, les en-têtes DataServiceVersion et MaxDataServiceVersion doivent être inclus dans une requête. Toutefois, s’ils ne le sont pas, Media Services suppose que la valeur DataServiceVersion utilisée est 3.0.
> 
> 

Voici un ensemble d’en-têtes facultatifs :

| En-tête | Type | Valeur |
| --- | --- | --- |
| Date |Date RFC 1123 |Horodatage de la demande |
| Acceptation |Type de contenu |Type de contenu demandé pour la réponse, comme :<p> -application/json;odata=verbose<p> - application/atom+xml<p> Les réponses peuvent avoir un type de contenu différent, par exemple une extraction d’objets blob, dans laquelle une réponse correcte contiendra le flux d’objets blob en tant que charge utile. |
| Accept-Encoding |Gzip, deflate |Codage GZIP et DEFLATE, le cas échéant. Remarque : pour les ressources volumineuses, Media Services peut ignorer cet en-tête et retourner des données non compressées. |
| Accept-Language |« en », « es » et ainsi de suite. |Spécifie la langue préférée pour la réponse. |
| Accept-Charset |Type de jeu de caractères comme « UTF-8 » |La valeur par défaut est UTF-8. |
| X-HTTP-Method |Méthode HTTP |Permet aux clients ou pare-feu ne prenant pas en charge les méthodes HTTP comme PUT ou DELETE d’utiliser ces méthodes, acheminées via un appel GET. |
| Content-Type |Type de contenu |Le type de contenu du corps de la requête dans les demandes PUT ou POST. |
| client-request-id |String |Une valeur définie par l’appelant qui identifie la requête donnée. Si spécifiée, cette valeur sera incluse dans le message de réponse comme une méthode de mappage de la requête. <p><p>**Important**<p>Les valeurs doivent être limitées à 2096 b (2k). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>En-têtes de réponse HTTP standard pris en charge par Media Services
Voici un ensemble d’en-têtes pouvant être renvoyés, en fonction de la ressource demandée et de l’action à entreprendre.

| En-tête | Type | Valeur |
| --- | --- | --- |
| request-id |String |Un identificateur unique pour l’opération actuelle, généré par le service. |
| client-request-id |String |Un identificateur spécifié par l’appelant dans la requête d’origine, le cas échéant. |
| Date |Date RFC 1123 |La date à laquelle la requête a été traitée. |
| Content-Type |Varie |Le type de contenu du corps de la réponse. |
| Content-Encoding |Varie |Gzip ou deflate, le cas échéant. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Verbes HTTP standard pris en charge par Media Services
Voici une liste complète des verbes HTTP pouvant être utilisés lors de requêtes HTTP :

| Verbe | Description |
| --- | --- |
| GET |Retourne la valeur actuelle d’un objet. |
| POST |Crée un objet, selon les données fournies, ou envoie une commande. |
| PUT |Remplace un objet ou crée un objet nommé (le cas échéant). |
| SUPPRIMER |Supprime un objet. |
| MERGE |Met à jour un objet existant avec des modifications de propriété nommées. |
| HEAD |Retourne les métadonnées d’un objet d’une réponse GET. |

## <a name="discover-media-services-model"></a>Découvrir le modèle Media Services
Pour rendre les entités Media Services plus détectables, l’opération $metadata peut être utilisée. Elle vous permet de récupérer l’ensemble des types d’entité, des propriétés d’entité, des associations, des fonctions, des actions valides, etc. L’exemple suivant montre comment construire l’URI : https://media.windows.net/API/$metadata.

Vous devez ajouter « ?api-version=2.x » à la fin de l’URI si vous souhaitez afficher les métadonnées dans un navigateur ou n’incluez pas l’en-tête x-ms-version dans votre requête.

## <a name="connect-to-media-services"></a>Connexion à Media Services

Pour savoir comment vous connecter à l’API AMS, consultez [Accéder à l’API Azure Media Services avec l’authentification Azure AD](media-services-use-aad-auth-to-access-ams-api.md). Après vous être connecté à https://media.windows.net, vous recevrez une redirection 301 spécifiant un autre URI Media Services. Vous devez faire d’autres appels au nouvel URI.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment accéder aux API AMS avec REST, consultez [Utiliser l’authentification Azure AD pour accéder à l’API Azure Media Services avec .NET](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

