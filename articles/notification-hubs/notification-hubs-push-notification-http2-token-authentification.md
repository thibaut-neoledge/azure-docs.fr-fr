---
title: "Authentification basée sur un jeton (HTTP/2) pour APNS dans Azure Notification Hubs | Microsoft Docs"
description: Cette rubrique explique comment tirer parti de la nouvelle authentification de jeton pour APNS
services: notification-hubs
documentationcenter: .net
author: kpiteira
manager: erikre
editor: 
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 05/17/2017
ms.author: kapiteir
ms.openlocfilehash: 5a21bcd9f12fc3f96b17a556ba15526c35ababe2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="token-based-http2-authentication-for-apns"></a>Authentification basée sur un jeton (HTTP/2) pour APNS
## <a name="overview"></a>Vue d'ensemble
Cet article explique comment utiliser le nouveau protocole APNS HTTP/2 avec une authentification basée sur un jeton.

Les principaux avantages de l’utilisation du nouveau protocole sont notamment :
-   La génération de jetons est relativement simple (par rapport aux certificats)
-   Plus de date d’expiration : vous contrôlez vos jetons d’authentification et leur révocation
-   Les charges utiles peuvent maintenant atteindre 4 Ko
- Les commentaires sont synchrones
-   Vous utilisez le dernier protocole d’Apple : les certificats utilisent toujours le protocole binaire, qui est marqué pour dépréciation

L’utilisation de ce nouveau mécanisme peut être lancée en deux étapes et en quelques minutes :
1.  Obtenez les informations nécessaires auprès du portail du compte de développeur Apple
2.  Configurez votre hub de notification avec les nouvelles informations

Notification Hubs est maintenant entièrement configuré pour utiliser le nouveau système d’authentification avec APNS. 

Notez que, si vous n’utilisez plus les informations d’identification de certificat pour APNS :
- les propriétés de jeton remplacent votre certificat dans notre système,
- mais votre application continue de recevoir des notifications sans interruption.

## <a name="obtaining-authentication-information-from-apple"></a>Obtention d’informations d’authentification auprès d’Apple
Pour activer l’authentification basée sur un jeton, vous devez obtenir les propriétés suivantes auprès de votre compte de développeur Apple :
### <a name="key-identifier"></a>Identificateur de clé
L’identificateur de clé peut être obtenu à partir de la page « Clés » dans le compte de développeur Apple

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Identificateur de l’application et nom de l’application
Le nom de l’application est disponible via la page des ID d’application dans le compte de développeur. 
![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

L’identificateur de l’application est disponible via la page des détails de l’adhésion dans le compte de développeur.
![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)


### <a name="authentication-token"></a>Jeton d’authentification
Le jeton d’authentification peut être téléchargé après avoir généré un jeton pour votre application. Pour plus d’informations sur la façon de générer ce jeton, reportez-vous à la [documentation du développeur Apple](http://help.apple.com/xcode/mac/current/#/dev11b059073?sub=dev1eb5dfe65).

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Configuration de votre hub de notification pour utiliser l’authentification basée sur un jeton
### <a name="configure-via-the-azure-portal"></a>Configurer via le portail Azure
Pour activer l’authentification basée sur un jeton dans le portail, connectez-vous au portail Azure et accédez au panneau Hub de notification > Services de notification > APNS. 

Il existe une nouvelle propriété : *Mode d’authentification*. La sélection de Jeton vous permet de mettre à jour votre hub avec toutes les propriétés de jeton appropriées.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

- Entrez les propriétés que vous avez récupérées à partir de votre compte de développeur Apple, 
- choisissez votre mode d’application : Production ou Bac à sable (sandbox), 
- cliquez sur Enregistrer pour mettre à jour vos informations d’identification APNS. 

### <a name="configure-via-management-api-rest"></a>Configurer via l’API de gestion (REST)

Vous pouvez utiliser nos [API de gestion](https://msdn.microsoft.com/library/azure/dn495827.aspx) afin de mettre à jour votre hub de notification pour utiliser l’authentification basée sur un jeton.
Selon que l’application que vous configurez est une application de production ou Bac à sable (sandbox), ce qui est indiqué dans votre compte de développeur Apple, utilisez l’un des points de terminaison correspondants :

- Point de terminaison sandbox : [https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
- Point de terminaison de production : [https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> L’authentification basée sur un jeton nécessite la version d’API **2017-04 ou ultérieure**.
> 
> 

Voici un exemple d’une demande PUT pour mettre à jour un hub avec l’authentification basée sur un jeton :


        PUT https://{namespace}.servicebus.windows.net/{Notification Hub}?api-version=2017-04
          "Properties": {
            "ApnsCredential": {
              "Properties": {
                "KeyId": "<Your Key Id>",
                "Token": "<Your Authentication Token>",
                "AppName": "<Your Application Name>",
                "AppId": "<Your Application Id>",
                "Endpoint":"<Sandbox/Production Endpoint>"
              }
            }
          }
        

### <a name="configure-via-the-net-sdk"></a>Configurer via le Kit SDK .NET
Vous pouvez configurer votre hub pour utiliser l’authentification basée sur un jeton à l’aide de notre [dernier Kit SDK client](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8). 

Voici un exemple de code illustrant l’utilisation correcte :


        NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
        string token = "YOUR TOKEN HERE";
        string keyId = "YOUR KEY ID HERE";
        string appName = "YOUR APP NAME HERE";
        string appId = "YOUR APP ID HERE";
        NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
        desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
        desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
        nm.UpdateNotificationHubAsync(desc);

## <a name="reverting-to-using-certificate-based-authentication"></a>Rétablissement de l’utilisation de l’authentification basée sur les certificats
Vous pouvez rétablir à tout moment l’utilisation de l’authentification basée sur les certificats en utilisant l’une des méthodes précédentes et en passant le certificat au lieu des propriétés de jeton. Cette action remplace les informations d’identification précédemment stockées.
