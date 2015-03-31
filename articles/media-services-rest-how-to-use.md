<properties 
	pageTitle="Vue d'ensemble de l'API REST Media Services - Azure" 
	description="Vue d'ensemble de l'API REST Media Services" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="juliako"/>


# Vue d'ensemble de l'API REST Media Services 

Microsoft Azure Media Services est un service qui accepte les demandes HTTP OData et peut répondre dans les formats JSON ou atom+pub détaillés. Étant donné que Media Services est conforme aux règles de conception Azure, il existe un ensemble d'en-têtes HTTP obligatoires que chaque client doit utiliser lors de la connexion à Media Services, ainsi qu'un jeu d'en-têtes facultatifs pouvant être utilisé. Les sections suivantes décrivent les en-têtes et verbes HTTP que vous pouvez utiliser lors de la création de requêtes et de la réception des réponses à partir de Media Services.


## En-têtes de requête HTTP standard pris en charge par Media Services

Pour chaque appel dans Media Services, il existe un ensemble d'en-têtes requis, que vous devez inclure dans votre requête, et un ensemble d'en-têtes facultatifs que vous pouvez également inclure. Le tableau suivant répertorie les en-têtes requis :


<table border="1">
<tr><th>En-tête</th><th>Type</th><th>Valeur</th></tr>
<tr><td>Autorisation</td><td>Support</td><td>Le support est le seul mécanisme d'autorisation accepté. La valeur doit également inclure le jeton d'accès fourni par ACS.</td></tr>
<tr><td>x-ms-version</td><td>Décimal</td><td>2.9</td></tr>
<tr><td>DataServiceVersion</td><td>Décimal</td><td>3.0</td></tr>
<tr><td>MaxDataServiceVersion</td><td>Décimal</td><td>3.0</td></tr>
</table><br/>


>[AZURE.NOTE] Étant donné que Media Services utilise OData pour exposer son référentiel de métadonnées de ressources sous-jacent via les API REST, les en-têtes DataServiceVersion et MaxDataServiceVersion doivent être inclus dans une requête. Toutefois, s'ils ne le sont pas, Media Services suppose que la valeur DataServiceVersion utilisée est 3.0.

Voici un ensemble d'en-têtes facultatifs :

<table border="1">
<tr><th>En-tête</th><th>Type</th><th>Valeur</th></tr>
<tr><td>Date</td><td>Date RFC 1123</td><td>Horodatage de la demande</td></tr>
<tr><td>Acceptation</td><td>Type de contenu</td><td>Le type de contenu demandé pour la réponse, comme :
<ul><li>application/json;odata=verbose</li><li>application/atom+xml</li></ul></br> Les réponses peuvent avoir un type de contenu différent, par exemple une extraction d'objets blob, dans laquelle une réponse correcte contiendra le flux d'objets blob en tant que charge utile.</td></tr>
<tr><td>Accept-Encoding</td><td>Gzip, deflate</td><td>Codage GZIP et DEFLATE, le cas échéant. Remarque : pour les ressources volumineuses, Media Services peut ignorer cet en-tête et retourner des données non compressées.
</td></tr>
<tr><td>Accept-Language</td><td>" en ", " es " et ainsi de suite.</td><td>Spécifie la langue préférée pour la réponse.</td></tr>
<tr><td>Accept-Charset</td><td>Type de jeu de caractères comme " UTF-8 "</td><td>La valeur par défaut est UTF-8.</td></tr>
<tr><td>X-HTTP-Method</td><td>Méthode HTTP</td><td>Permet aux clients ou pare-feu ne prenant pas en charge les méthodes HTTP comme PUT ou DELETE d'utiliser ces méthodes, acheminées via un appel GET.</td></tr>
<tr><td>Content-Type</td><td>Type de contenu</td><td>Le type de contenu du corps de la requête dans les demandes PUT ou POST.</td></tr>
<tr><td>client-request-id</td><td>String</td><td>Une valeur définie par l'appelant qui identifie la requête donnée. Si spécifiée, cette valeur sera incluse dans le message de réponse comme une méthode de mappage de la requête. <br/><br/>
<b>Important</b><br/>
Les valeurs doivent être limitées à 2096 b (2k).</td></tr>
</table><br/>


## En-têtes de réponse HTTP standard pris en charge par Media Services

Voici un ensemble d'en-têtes pouvant être renvoyés, en fonction de la ressource demandée et de l'action à entreprendre.


<table border="1">
<tr><th>En-tête</th><th>Type</th><th>Valeur</th></tr>
<tr><td>request-id</td><td>String</td><td>Un identificateur unique pour l'opération actuelle, généré par le service.</td></tr>
<tr><td>client-request-id</td><td>String</td><td>Un identificateur spécifié par l'appelant dans la requête d'origine, le cas échéant.</td></tr>
<tr><td>Date</td><td>Date RFC 1123</td><td>La date à laquelle la requête a été traitée.</td></tr>
<tr><td>Content-Type</td><td>Varie</td><td>Le type de contenu du corps de la réponse.</td></tr>
<tr><td>Content-Encoding</td><td>Varie</td><td>Gzip ou deflate, le cas échéant.</td></tr>
</table><br/>

## Verbes HTTP standard pris en charge par Media Services

Voici une liste complète des verbes HTTP pouvant être utilisés lors de requêtes HTTP :


<table border="1">
<tr><th>Verbe</th><th>Description</th></tr>
<tr><td>GET</td><td>Retourne la valeur actuelle d'un objet.</td></tr>
<tr><td>POST</td><td>Crée un objet, selon les données fournies, ou envoie une commande.</td></tr>
<tr><td>PUT</td><td>Remplace un objet ou crée un objet nommé (le cas échéant).</td></tr>
<tr><td>DELETE</td><td>Supprime un objet.</td></tr>
<tr><td>MERGE</td><td>Met à jour un objet existant avec des modifications de propriété nommées.</td></tr>
<tr><td>HEAD</td><td>Retourne les métadonnées d'un objet d'une réponse GET.</td></tr>
</table><br/>

## Découverte du modèle Media Services

Pour rendre les entités Media Services plus détectables, l'opération $metadata peut être utilisée. Elle vous permet de récupérer l'ensemble des types d'entité, des propriétés d'entité, des associations, des fonctions, des actions valides, etc. L'exemple suivant montre comment construire l'URI : https://media.windows.net/API/$metadata.

Vous devez ajouter " ?api-version=2.x " à la fin de l'URI si vous souhaitez afficher les métadonnées dans un navigateur ou n'incluez pas l'en-tête x-ms-version dans votre requête.


<!-- Anchors. -->


<!-- URLs. -->
  
  [Portail de gestion]: http://manage.windowsazure.com/




<!--HONumber=47-->
