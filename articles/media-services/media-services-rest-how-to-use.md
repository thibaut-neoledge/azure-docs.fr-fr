<properties 
	pageTitle="Vue d’ensemble de l’API REST Media Services | Microsoft Azure" 
	description="Vue d’ensemble de l’API REST Media Services" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/07/2015"
	ms.author="juliako"/>


# Vue d’ensemble de l’API REST Media Services 

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Microsoft Azure Media Services est un service qui accepte les demandes HTTP OData et peut répondre dans les formats JSON ou atom+pub détaillés. Étant donné que Media Services est conforme aux règles de conception Azure, il existe un ensemble d’en-têtes HTTP obligatoires que chaque client doit utiliser lors de la connexion à Media Services, ainsi qu’un jeu d’en-têtes facultatifs pouvant être utilisé. Les sections suivantes décrivent les en-têtes et verbes HTTP que vous pouvez utiliser lors de la création de requêtes et de la réception des réponses à partir de Media Services.


## En-têtes de requête HTTP standard pris en charge par Media Services

Pour chaque appel dans Media Services, il existe un ensemble d’en-têtes requis, que vous devez inclure dans votre requête, et un ensemble d’en-têtes facultatifs que vous pouvez également inclure. Le tableau suivant répertorie les en-têtes requis :


En-tête|Type|Valeur
---|---|---
Autorisation|Support|Le support est le seul mécanisme d’autorisation accepté. La valeur doit également inclure le jeton d’accès fourni par ACS.
x-ms-version|Décimal|2\.11
DataServiceVersion|Décimal|3\.0
MaxDataServiceVersion|Décimal|3\.0



>[AZURE.NOTE]Étant donné que Media Services utilise OData pour exposer son référentiel de métadonnées de ressources sous-jacent via les API REST, les en-têtes DataServiceVersion et MaxDataServiceVersion doivent être inclus dans une requête. Toutefois, s’ils ne le sont pas, Media Services suppose que la valeur DataServiceVersion utilisée est 3.0.

Voici un ensemble d’en-têtes facultatifs :

En-tête|Type|Valeur
---|---|---
Date|Date RFC 1123|Horodatage de la demande
Acceptation|Type de contenu|Type de contenu demandé pour la réponse, comme :<p> -application/json;odata=verbose<p> - application/atom+xml<p> Les réponses peuvent avoir un type de contenu différent, par exemple une extraction d’objets blob, dans laquelle une réponse correcte contiendra le flux d’objets blob en tant que charge utile.
Accept-Encoding|Gzip, deflate|Codage GZIP et DEFLATE, le cas échéant. Remarque : pour les ressources volumineuses, Media Services peut ignorer cet en-tête et retourner des données non compressées.
Accept-Language|« en », « es » et ainsi de suite.|Spécifie la langue préférée pour la réponse.
Accept-Charset|Type de jeu de caractères comme « UTF-8 »|La valeur par défaut est UTF-8.
X-HTTP-Method|Méthode HTTP|Permet aux clients ou pare-feu ne prenant pas en charge les méthodes HTTP comme PUT ou DELETE d’utiliser ces méthodes, acheminées via un appel GET.
Content-Type|Type de contenu|Le type de contenu du corps de la requête dans les demandes PUT ou POST.
client-request-id|String|Une valeur définie par l’appelant qui identifie la requête donnée. Si spécifiée, cette valeur sera incluse dans le message de réponse comme une méthode de mappage de la requête. <p><p>**Important**<p>Les valeurs doivent être limitées à 2096b (2k).

## En-têtes de réponse HTTP standard pris en charge par Media Services

Voici un ensemble d’en-têtes pouvant être renvoyés, en fonction de la ressource demandée et de l’action à entreprendre.


En-tête|Type|Valeur
---|---|---
request-id|String|Un identificateur unique pour l’opération actuelle, généré par le service.
client-request-id|String|Un identificateur spécifié par l’appelant dans la requête d’origine, le cas échéant.
Date|Date RFC 1123|La date à laquelle la requête a été traitée.
Content-Type|Varie|Le type de contenu du corps de la réponse.
Content-Encoding|Varie|Gzip ou deflate, le cas échéant.


## Verbes HTTP standard pris en charge par Media Services

Voici une liste complète des verbes HTTP pouvant être utilisés lors de requêtes HTTP :


Verbe|Description
---|---
GET|Retourne la valeur actuelle d’un objet.
POST|Crée un objet, selon les données fournies, ou envoie une commande.
PUT|Remplace un objet ou crée un objet nommé (le cas échéant).
SUPPRIMER|Supprime un objet.
MERGE|Met à jour un objet existant avec des modifications de propriété nommées.
HEAD|Retourne les métadonnées d’un objet d’une réponse GET.


## Découverte du modèle Media Services

Pour rendre les entités Media Services plus détectables, l’opération $metadata peut être utilisée. Elle vous permet de récupérer l’ensemble des types d’entité, des propriétés d’entité, des associations, des fonctions, des actions valides, etc. L’exemple suivant montre comment construire l’URI : https://media.windows.net/API/$metadata.

Vous devez ajouter « ?api-version=2.x » à la fin de l’URI si vous souhaitez afficher les métadonnées dans un navigateur ou n’incluez pas l’en-tête x-ms-version dans votre requête.



##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

  
  [Azure Classic Portal]: http://manage.windowsazure.com/



 

<!---HONumber=AcomDC_1203_2015-->