---
title: "Découvrez comment utiliser le connecteur Twitter dans les applications logiques | Microsoft Docs"
description: "Vue d’ensemble du connecteur Twitter avec les paramètres d’API REST"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 13ecb8936484b1c86938a16c7dd6da8000d4ffec


---
# <a name="get-started-with-the-twitter-connector"></a>Prise en main du connecteur Twitter
Le connecteur Twitter vous permet d’effectuer les opérations suivantes :

* publier et recevoir des tweets ;
* accéder à des fils d’actualités, des amis et des abonnés ;
* exécuter la totalité des actions et des déclencheurs décrits ci-dessous.  

Pour utiliser [n’importe quel connecteur](apis-list.md), vous devez commencer par créer une application logique. Vous pouvez démarrer maintenant en [créant une application logique](../logic-apps/logic-apps-create-a-logic-app.md).  

## <a name="connect-to-twitter"></a>Se connecter à Twitter
Pour que votre application logique puisse accéder à un service, vous devez commencer par créer une *connexion* à celui-ci. Une [connexion](connectors-overview.md) permet d’assurer la connectivité entre une application logique et un autre service.  

### <a name="create-a-connection-to-twitter"></a>Créer une connexion à Twitter
> [!INCLUDE [Steps to create a connection to Twitter](../../includes/connectors-create-api-twitter.md)]
> 
> 

## <a name="use-a-twitter-trigger"></a>Utiliser un déclencheur Twitter
Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. [En savoir plus sur les déclencheurs](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Dans cet exemple, nous allons vous indiquer comment utiliser le déclencheur **When a new tweet is posted** (Quand un nouveau tweet est publié) pour rechercher #Seattle et, si le texte #Seattle est trouvé, pour mettre à jour un fichier dans Dropbox avec le texte du tweet. Dans un contexte d’entreprise, vous pourriez rechercher le nom de votre société et mettre à jour une base de données SQL avec le texte du tweet.

1. Entrez *twitter* dans la zone de recherche du concepteur d’applications logiques, puis sélectionnez le déclencheur **Twitter - When a new tweet is posted** (Twitter - Quand un nouveau tweet est publié).   
   ![Image de déclencheur Twitter 1](./media/connectors-create-api-twitter/trigger-1.png)  
2. Entrez *#Seattle* dans le contrôle **Search Text** (Texte de recherche).  
   ![Image de déclencheur Twitter 2](./media/connectors-create-api-twitter/trigger-2.png) 

À ce stade, votre application logique a été configurée avec un déclencheur qui lance une série d’autres déclencheurs et actions dans le flux de travail. 

> [!NOTE]
> Pour qu’une application logique soit fonctionnelle, elle doit contenir au moins un déclencheur et une action. Suivez la procédure décrite ci-après pour ajouter une action.  
> 
> 

## <a name="add-a-condition"></a>Ajouter une condition
Étant donné que nous sommes uniquement intéressés par les tweets publiés par des utilisateurs disposant de plus de 50 abonnés, nous devons commencer par ajouter à l’application logique une condition confirmant le nombre d’abonnés.  

1. Sélectionnez **+ Nouvelle étape** pour ajouter l’action à exécuter quand le texte #Seattle est trouvé dans un nouveau tweet.  
   ![Image d’action Twitter 1](../../includes/media/connectors-create-api-twitter/action-1.png)  
2. Sélectionnez le lien **Ajouter une condition**.  
   ![Image de condition Twitter 1](../../includes/media/connectors-create-api-twitter/condition-1.png)   
   Cette opération ouvre le contrôle **Condition** qui vous permet de sélectionner des conditions telles que *est égal à*, *est inférieur à*, *est supérieur à*, *contient*, etc.  
   ![Image de condition Twitter 2](../../includes/media/connectors-create-api-twitter/condition-2.png)   
3. Sélectionnez le contrôle **Choisir une valeur**.  
   Dans ce contrôle, vous pouvez sélectionner une ou plusieurs des propriétés des actions ou déclencheurs précédents en tant que valeur dont la condition sera évaluée comme étant vraie ou fausse.
   ![Image de condition Twitter 3](../../includes/media/connectors-create-api-twitter/condition-3.png)   
4. Sélectionnez le bouton **...** pour développer la liste de propriétés et visualiser toutes les propriétés disponibles.        
   ![Image de condition Twitter 4](../../includes/media/connectors-create-api-twitter/condition-4.png)   
5. Sélectionnez la propriété **Followers count** (Nombre d’abonnés).    
   ![Image de condition Twitter 5](../../includes/media/connectors-create-api-twitter/condition-5.png)   
6. Notez que la propriété Followers count (Nombre d’abonnés) figure désormais dans le contrôle de valeur.    
   ![Image de condition Twitter 6](../../includes/media/connectors-create-api-twitter/condition-6.png)   
7. Sélectionnez **est supérieur à** dans la liste des opérateurs.    
   ![Image de condition Twitter 7](../../includes/media/connectors-create-api-twitter/condition-7.png)   
8. Entrez 50 en tant qu’opérande pour l’opérateur *est supérieur à*.  
   La condition est à présent ajoutée. Enregistrez votre travail à l’aide du lien **Enregistrer** dans le menu supérieur.    
   ![Image de condition Twitter 8](../../includes/media/connectors-create-api-twitter/condition-8.png)   

## <a name="use-a-twitter-action"></a>Utiliser une action Twitter
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

Une fois le déclencheur ajouté, procédez comme suit pour ajouter une action qui publiera un nouveau tweet avec le contenu des tweets trouvés par le déclencheur. Pour les besoins de cette procédure pas à pas, seuls les tweets émanant d’utilisateurs disposant de plus de 50 abonnés seront publiés.  

À l’étape suivante, vous allez ajouter une action Twitter qui publiera un tweet en utilisant certaines des propriétés de chaque tweet ayant été publié par un utilisateur comptant plus de 50 abonnés.  

1. Sélectionnez **Ajouter une action**. Cette opération ouvre le contrôle de recherche qui vous permet de rechercher d’autres actions et déclencheurs.  
   ![Image de condition Twitter 9](../../includes/media/connectors-create-api-twitter/condition-9.png)   
2. Entrez *twitter* dans la zone de recherche, puis sélectionnez l’action **Twitter - Post a tweet** (Twitter - Publier un tweet). Cette opération ouvre le contrôle **Post a tweet** (Publier un tweet) dans lequel vous entrerez tous les détails concernant le tweet en cours de publication.      
   ![Image d’action Twitter 1-5](../../includes/media/connectors-create-api-twitter/action-1-5.png)   
3. Sélectionnez le contrôle **Tweet text** (Texte du tweet). Toutes les sorties des actions et déclencheurs précédents dans l’application logique sont désormais visibles. Vous pouvez sélectionner les actions et déclencheurs de votre choix et les utiliser comme partie du texte du nouveau tweet.     
   ![Image d’action Twitter 2](../../includes/media/connectors-create-api-twitter/action-2.png)   
4. Sélectionnez **Nom d’utilisateur**.   
5. Entrez *dit :* dans le contrôle du texte du tweet. Effectuez cette opération immédiatement après avoir sélectionné le nom d’utilisateur.  
6. Sélectionnez *Tweet text* (Texte du tweet).       
   ![Image d’action Twitter 3](../../includes/media/connectors-create-api-twitter/action-3.png)   
7. Enregistrez votre travail et envoyez un tweet avec le mot-dièse #Seattle pour activer votre workflow.  

## <a name="technical-details"></a>Détails techniques
Voici les détails des déclencheurs, actions et réponses pris en charge par cette connexion :

## <a name="twitter-triggers"></a>Déclencheurs Twitter
Le connecteur Twitter comporte le déclencheur suivant :  

| Déclencheur | Description |
| --- | --- |
| [When a new tweet is posted](connectors-create-api-twitter.md#when-a-new-tweet-is-posted) (Quand un nouveau tweet est publié) |Cette opération déclenche un flux lorsqu’un nouveau tweet correspondant à une requête de recherche donnée est publié. |

## <a name="twitter-actions"></a>Actions Twitter
Le connecteur Twitter comporte les actions suivantes :

| Action | Description |
| --- | --- |
| [Get user timeline](connectors-create-api-twitter.md#get-user-timeline) (Obtenir un fil d’utilisateur) |Cette opération récupère une liste des derniers tweets publiés par un utilisateur donné. |
| [Get home timeline](connectors-create-api-twitter.md#get-home-timeline) (Obtenir un fil d’actualités) |Cette opération récupère les derniers tweets et retweets publiés par mes abonnés et moi-même. |
| [Search tweets](connectors-create-api-twitter.md#search-tweets) (Rechercher des tweets) |Cette opération récupère une liste des tweets pertinents correspondant à la requête de recherche. |
| [Get followers](connectors-create-api-twitter.md#get-followers) (Obtenir des abonnés) |Cette opération récupère la liste des utilisateurs qui suivent un utilisateur spécifique. |
| [Get my followers](connectors-create-api-twitter.md#get-my-followers) (Obtenir mes abonnés) |Cette opération récupère la liste des utilisateurs qui me suivent. |
| [Get following](connectors-create-api-twitter.md#get-following) (Obtenir un abonnement) |Cette opération récupère la liste des personnes suivies par l’utilisateur sélectionné. |
| [Get my following](connectors-create-api-twitter.md#get-my-following) (Obtenir mon abonnement) |Cette opération récupère la liste des utilisateurs que je suis. |
| [Get user](connectors-create-api-twitter.md#get-user) (Obtenir un utilisateur) |Cette opération récupère les détails du profil d’un utilisateur spécifique, tels que le nom d’utilisateur, la description, le nombre d’abonnés, etc. |
| [Post a tweet](connectors-create-api-twitter.md#post-a-tweet) (Publier un tweet) |Cette opération publie un nouveau tweet. |

## <a name="action-details"></a>Détails de l’action
Voici les détails des actions et des déclencheurs de ce connecteur, ainsi que leurs réponses :

### <a name="get-user-timeline"></a>Obtenir un fil d’utilisateur
Cette opération récupère une liste des derniers tweets publiés par un utilisateur donné. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| userName* |Nom d'utilisateur |Pseudo Twitter de l’utilisateur |
| maxResults |Nombre maximal de résultats |Nombre maximal de tweets à renvoyer |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
TweetModel : représentation d’un objet tweet

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| tweetText |string |Texte du tweet |
| TweetId |string |Identificateur du tweet |
| CreatedAt |string |Heure de publication du tweet |
| RetweetCount |integer |Nombre total de retweets du tweet |
| TweetedBy |string |Nom de l’utilisateur ayant publié le tweet |
| MediaUrls |array |URL du média publié en même temps que le tweet |
| TweetLanguageCode |string |Code de langue du tweet |
| TweetInReplyToUserId |string |Identificateur d’utilisateur de l’auteur du tweet auquel répond le tweet actuel |
| Favorited |booléenne |Indique si le tweet est marqué ou non comme favori |
| UserMentions |array |Liste des utilisateurs mentionnés dans le tweet |
| OriginalTweet |non défini |Tweet initial à partir duquel le tweet actuel est retweeté |
| UserDetails |non défini |Détails de l’utilisateur ayant tweeté |

### <a name="get-home-timeline"></a>Obtenir un fil d’actualités
Cette opération récupère les derniers tweets et retweets publiés par mes abonnés et moi-même. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| maxResults |Nombre maximal de résultats |Nombre maximal de tweets à renvoyer |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
TweetModel : représentation d’un objet tweet

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| tweetText |string |Texte du tweet |
| TweetId |string |Identificateur du tweet |
| CreatedAt |string |Heure de publication du tweet |
| RetweetCount |integer |Nombre total de retweets du tweet |
| TweetedBy |string |Nom de l’utilisateur ayant publié le tweet |
| MediaUrls |array |URL du média publié en même temps que le tweet |
| TweetLanguageCode |string |Code de langue du tweet |
| TweetInReplyToUserId |string |Identificateur d’utilisateur de l’auteur du tweet auquel répond le tweet actuel |
| Favorited |booléenne |Indique si le tweet est marqué ou non comme favori |
| UserMentions |array |Liste des utilisateurs mentionnés dans le tweet |
| OriginalTweet |non défini |Tweet initial à partir duquel le tweet actuel est retweeté |
| UserDetails |non défini |Détails de l’utilisateur ayant tweeté |

### <a name="search-tweets"></a>Rechercher des tweets
Cette opération récupère une liste des tweets pertinents correspondant à la requête de recherche. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| searchQuery* |Search text (Texte de recherche) |Terme de recherche comme « happy hour », #haïku, amour OU haine |
| maxResults |Nombre maximal de résultats |Nombre maximal de tweets à renvoyer |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
TweetModel : représentation d’un objet tweet

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| tweetText |string |Texte du tweet |
| TweetId |string |Identificateur du tweet |
| CreatedAt |string |Heure de publication du tweet |
| RetweetCount |integer |Nombre total de retweets du tweet |
| TweetedBy |string |Nom de l’utilisateur ayant publié le tweet |
| MediaUrls |array |URL du média publié en même temps que le tweet |
| TweetLanguageCode |string |Code de langue du tweet |
| TweetInReplyToUserId |string |Identificateur d’utilisateur de l’auteur du tweet auquel répond le tweet actuel |
| Favorited |booléenne |Indique si le tweet est marqué ou non comme favori |
| UserMentions |array |Liste des utilisateurs mentionnés dans le tweet |
| OriginalTweet |non défini |Tweet initial à partir duquel le tweet actuel est retweeté |
| UserDetails |non défini |Détails de l’utilisateur ayant tweeté |

### <a name="get-followers"></a>Obtenir des abonnés
Cette opération récupère la liste des utilisateurs qui suivent un utilisateur spécifique. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| userName* |Nom d'utilisateur |Pseudo Twitter de l’utilisateur |
| maxResults |Nombre maximal de résultats |Nombre maximal d’utilisateurs à renvoyer |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
UserDetailsModel : détails de l’utilisateur Twitter

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| FullName |string |Nom de l’utilisateur |
| Lieu |string |Emplacement de l’utilisateur |
| id |integer |Identificateur Twitter de l’utilisateur |
| Nom d’utilisateur |string |Pseudo de l’utilisateur |
| FollowersCount |integer |Nombre d’abonnés |
| Description |string |Description de l’utilisateur |
| StatusesCount |integer |Nombre de statuts de l’utilisateur |
| FriendsCount |integer |Nombre d’amis |
| FavouritesCount |integer |Nombre de tweets marqués par l’utilisateur comme favoris |
| ProfileImageUrl |string |URL de l’image du profil |

### <a name="get-my-followers"></a>Obtenir mes abonnés
Cette opération récupère la liste des utilisateurs qui me suivent. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| maxResults |Nombre maximal de résultats |Nombre maximal d’utilisateurs à récupérer |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
UserDetailsModel : détails de l’utilisateur Twitter

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| FullName |string |Nom de l’utilisateur |
| Lieu |string |Emplacement de l’utilisateur |
| id |integer |Identificateur Twitter de l’utilisateur |
| Nom d’utilisateur |string |Pseudo de l’utilisateur |
| FollowersCount |integer |Nombre d’abonnés |
| Description |string |Description de l’utilisateur |
| StatusesCount |integer |Nombre de statuts de l’utilisateur |
| FriendsCount |integer |Nombre d’amis |
| FavouritesCount |integer |Nombre de tweets marqués par l’utilisateur comme favoris |
| ProfileImageUrl |string |URL de l’image du profil |

### <a name="get-following"></a>Obtenir un abonnement
Cette opération récupère la liste des personnes suivies par l’utilisateur sélectionné. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| userName* |Nom d'utilisateur |Pseudo Twitter de l’utilisateur |
| maxResults |Nombre maximal de résultats |Nombre maximal d’utilisateurs à renvoyer |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
UserDetailsModel : détails de l’utilisateur Twitter

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| FullName |string |Nom de l’utilisateur |
| Lieu |string |Emplacement de l’utilisateur |
| id |integer |Identificateur Twitter de l’utilisateur |
| Nom d’utilisateur |string |Pseudo de l’utilisateur |
| FollowersCount |integer |Nombre d’abonnés |
| Description |string |Description de l’utilisateur |
| StatusesCount |integer |Nombre de statuts de l’utilisateur |
| FriendsCount |integer |Nombre d’amis |
| FavouritesCount |integer |Nombre de tweets marqués par l’utilisateur comme favoris |
| ProfileImageUrl |string |URL de l’image du profil |

### <a name="get-my-following"></a>Obtenir mon abonnement
Cette opération récupère la liste des utilisateurs que je suis. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| maxResults |Nombre maximal de résultats |Nombre maximal d’utilisateurs à renvoyer |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
UserDetailsModel : détails de l’utilisateur Twitter

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| FullName |string |Nom de l’utilisateur |
| Lieu |string |Emplacement de l’utilisateur |
| id |integer |Identificateur Twitter de l’utilisateur |
| Nom d’utilisateur |string |Pseudo de l’utilisateur |
| FollowersCount |integer |Nombre d’abonnés |
| Description |string |Description de l’utilisateur |
| StatusesCount |integer |Nombre de statuts de l’utilisateur |
| FriendsCount |integer |Nombre d’amis |
| FavouritesCount |integer |Nombre de tweets marqués par l’utilisateur comme favoris |
| ProfileImageUrl |string |URL de l’image du profil |

### <a name="get-user"></a>Obtenir un utilisateur
Cette opération récupère les détails du profil d’un utilisateur spécifique, tels que le nom d’utilisateur, la description, le nombre d’abonnés, etc. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| userName* |Nom d'utilisateur |Pseudo Twitter de l’utilisateur |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
UserDetailsModel : détails de l’utilisateur Twitter

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| FullName |string |Nom de l’utilisateur |
| Lieu |string |Emplacement de l’utilisateur |
| id |integer |Identificateur Twitter de l’utilisateur |
| Nom d’utilisateur |string |Pseudo de l’utilisateur |
| FollowersCount |integer |Nombre d’abonnés |
| Description |string |Description de l’utilisateur |
| StatusesCount |integer |Nombre de statuts de l’utilisateur |
| FriendsCount |integer |Nombre d’amis |
| FavouritesCount |integer |Nombre de tweets marqués par l’utilisateur comme favoris |
| ProfileImageUrl |string |URL de l’image du profil |

### <a name="post-a-tweet"></a>Post a tweet (Publier un tweet)
Cette opération publie un nouveau tweet. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| tweetText |Tweet text (Texte du tweet) |Texte à publier |
| body |Médias |Média à publier |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
TweetResponseModel : modèle représentant un tweet publié

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| TweetId |string |Identificateur du tweet récupéré |

### <a name="when-a-new-tweet-is-posted"></a>When a new tweet is posted (Lorsqu’un nouveau tweet est publié)
Cette opération déclenche un flux lorsqu’un nouveau tweet correspondant à une requête de recherche donnée est publié. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| searchQuery* |Search text (Texte de recherche) |Terme de recherche comme « happy hour », #haïku, amour OU haine |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
TriggerBatchResponse[TweetModel]

| Nom de la propriété | Type de données |
| --- | --- |
| value |array |

## <a name="http-responses"></a>Réponses HTTP
Les actions et déclencheurs ci-dessus peuvent renvoyer un ou plusieurs des codes d’état HTTP suivants : 

| Nom | Description |
| --- | --- |
| 200 |OK |
| 202 |Acceptée |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


