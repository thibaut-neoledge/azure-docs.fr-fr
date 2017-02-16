---
title: "Utiliser le connecteur Office 365 Video dans vos applications logiques | Microsoft Docs"
description: "Utiliser le connecteur Office 365 Video dans vos applications logiques Microsoft Azure App Service"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 738e5aa7-2523-4116-8b65-211b9063852d
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: e6f569355dd73919824f22c88ce3eead10e5b853


---
# <a name="get-started-with-the-office365-video-connector"></a>Prise en main du connecteur Office 365 Video
Connexion à Office 365 Video pour obtenir des informations sur une vidéo Office 365, la liste des vidéos, et bien plus encore. Le connecteur Office 365 Video peut être utilisé dans :

* Logic Apps 

> [!NOTE]
> Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques. Ce connecteur n’est pas pris en charge sur les versions de schéma précédentes.
> 
> 

Avec Office 365 Video, vous pouvez :

* Créer votre flux d’activité en fonction des données que vous obtenez d’Office 365 Video. 
* Utiliser des actions pour vérifier l’état du portail vidéo, obtenir une liste de toutes les vidéos dans un canal, et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Vous pouvez, par exemple, utiliser le connecteur Bing Search pour rechercher des vidéos Office 365, puis utiliser le connecteur Office 365 Video pour obtenir des informations sur ces vidéos. Si la vidéo répond à vos besoins, vous pouvez la publier sur Facebook. 

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Le connecteur Office 365 Video met à votre disposition les actions ci-après. Il n'y a aucun déclencheur.

| Déclencheurs | Actions |
| --- | --- |
| Aucun |<ul><li>Vérifie l’état du portail vidéo</li><li>Obtenir tous les canaux visibles</li><li>Obtenir l'URL de la lecture du manifeste Azure Media Services pour une vidéo</li><li>Obtenir le jeton du porteur pour accéder au déchiffrage de la vidéo</li><li>Obtient des informations sur une vidéo Office&365; particulière</li><li>Répertorie toutes les vidéos Office&365; présentes dans un canal</li></ul> |

Tous les connecteurs prennent en charge les données aux formats JSON et XML. 

## <a name="create-a-connection-to-office365-video-connector"></a>Créer une connexion au connecteur Office 365 Video
Quand vous ajoutez ce connecteur à vos applications logiques, vous devez vous connecter à votre compte Office 365 Video et autoriser les applications logiques à se connecter à votre compte.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]
> 
> 

Après avoir créé la connexion, vous entrez les propriétés Office 365 Video, comme le nom du client ou l’ID du canal. La section **Informations de référence sur l'API REST** dans cette rubrique décrit ces propriétés.

> [!TIP]
> Vous pouvez utiliser cette même connexion Office 365 Video dans d’autres applications logiques.
> 
> 

## <a name="swagger-rest-api-reference"></a>Informations de référence sur l'API REST Swagger
S’applique à la version 1.0.

### <a name="checks-video-portal-status"></a>Vérifie l’état du portail vidéo
Vérifie l’état du portail vidéo pour voir si les services vidéo sont activés.  
```GET: /{tenant}/IsEnabled``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| locataire |string |Oui |path |Aucun |Le nom du client pour le répertoire dont fait partie l'utilisateur |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |BadRequest |
| 401 |Non autorisé |
| 404 |Introuvable |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

### <a name="get-all-viewable-channels"></a>Obtenir tous les canaux visibles
Permet d’obtenir tous les canaux que l’utilisateur est autorisé à afficher.  
```GET: /{tenant}/Channels``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| locataire |string |Oui |path |Aucun |Le nom du client pour le répertoire dont fait partie l'utilisateur |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |BadRequest |
| 401 |Non autorisé |
| 404 |Introuvable |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

### <a name="lists-all-the-office365-videos-present-in-a-channel"></a>Répertorie toutes les vidéos Office&365; présentes dans un canal
Répertorie toutes les vidéos Office&365; présentes dans un canal.  
```GET: /{tenant}/Channels/{channelId}/Videos``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| locataire |string |Oui |path |Aucun |Le nom du client pour le répertoire dont fait partie l'utilisateur |
| channelId |string |Oui |path |Aucun |L'ID du canal où les vidéos doivent être récupérées |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |BadRequest |
| 401 |Non autorisé |
| 404 |Introuvable |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

### <a name="gets-information-about-a-particular-office365-video"></a>Obtient des informations sur une vidéo Office&365; particulière
Obtient des informations sur une vidéo Office&365; particulière.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| locataire |string |Oui |path |Aucun |Le nom du client pour le répertoire dont fait partie l'utilisateur |
| channelId |string |Oui |path |Aucun |L’ID du canal |
| videoId |string |Oui |path |Aucun |L'ID de la vidéo |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |BadRequest |
| 401 |Non autorisé |
| 404 |Introuvable |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

### <a name="get-playback-url-of-the-azure-media-services-manifest-for-a-video"></a>Obtenir l'URL de la lecture du manifeste Azure Media Services pour une vidéo
Obtenir l’URL de la lecture du manifeste Azure Media Services pour une vidéo.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| locataire |string |Oui |path |Aucun |Le nom du client pour le répertoire dont fait partie l'utilisateur |
| channelId |string |Oui |path |Aucun |L’ID du canal |
| videoId |string |Oui |path |Aucun |L'ID de la vidéo |
| streamingFormatType |string |yes |query |Aucun |Type de format de la diffusion en continu. 1 - Smooth Streaming ou MPEG-DASH. 0 - HLS Streaming |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |BadRequest |
| 401 |Non autorisé |
| 404 |Introuvable |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

### <a name="get-the-bearer-token-to-get-access-to-decrypt-the-video"></a>Obtenir le jeton du porteur pour accéder au déchiffrage de la vidéo
Obtenir le jeton du porteur pour accéder au déchiffrage de la vidéo.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| locataire |string |Oui |path |Aucun |Le nom du client pour le répertoire dont fait partie l'utilisateur |
| channelId |string |Oui |path |Aucun |L’ID du canal |
| videoId |string |Oui |path |Aucun |L'ID de la vidéo |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |BadRequest |
| 401 |Non autorisé |
| 404 |Introuvable |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

## <a name="object-definitions"></a>Définitions d'objet
#### <a name="channel-channel-class"></a>Channel : classe de canal
| Nom | Type de données | Requis |
| --- | --- | --- |
| id |string |no |
| Intitulé |string |no |
| Description |string |no |

#### <a name="video"></a>Vidéo
| Nom | Type de données | Requis |
| --- | --- | --- |
| id |string |no |
| Intitulé |string |no |
| Description |string |no |
| CreationDate |string |no |
| Propriétaire |string |no |
| ThumbnailUrl |string |no |
| VideoUrl |string |no |
| VideoDuration |integer |no |
| VideoProcessingStatus |integer |no |
| ViewCount |integer |no |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).




<!--HONumber=Feb17_HO3-->


