---
title: "Azure Active Directory B2C : Demande de jetons d’accès | Microsoft Docs"
description: "Cet article vous montre comment configurer une application cliente et acquérir un jeton d’accès."
services: active-directory-b2c
documentationcenter: android
author: parakhj
manager: krassk
editor: 
ms.assetid: 1c75f17f-5ec5-493a-b906-f543b3b1ea66
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 5d15ad7a4e75410390891b5e11f72c6a649ebf53
ms.lasthandoff: 03/23/2017


---


# <a name="azure-ad-b2c-requesting-access-tokens"></a>Azure Active Directory B2C : Demande de jetons d’accès


Un jeton d’accès (défini en tant que **access\_token**) est une forme de jeton de sécurité qu’un client peut utiliser pour accéder aux ressources sécurisées par un [serveur d’autorisation](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-protocols#the-basics), tel qu’une API web. Les jetons d’accès sont représentés en tant que jetons [JWT](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#types-of-tokens) et contiennent des informations sur le serveur de ressources prévu et les autorisations accordées au serveur. Lorsque vous appelez le serveur de ressources, le jeton d’accès doit être présent dans la requête HTTP.

Cet article explique comment configurer une application cliente et demander à acquérir un jeton d’accès **access\_token** à partir des points de terminaison `authorize` et `token`.

## <a name="prerequisite"></a>Configuration requise

Avant de demander un jeton d’accès, vous devez inscrire une API web et publier les autorisations qui peuvent être accordées à l’application cliente. Démarrez en suivant les étapes décrites dans la section [Inscrire une API web](active-directory-b2c-app-registration.md).

## <a name="granting-permissions-to-a-web-api"></a>Octroi d’autorisations à une API web

Pour qu’une application cliente obtienne des autorisations spécifiques d’accès à une API, vous devez lui octroyer ces autorisations par le biais du portail Azure. Pour octroyer des autorisations à une application cliente :

1. Accédez au menu **Applications** dans le panneau des fonctionnalités B2C.
2. Cliquez sur votre application cliente ([inscrivez une application](active-directory-b2c-app-registration.md) si vous ne l’avez pas encore fait).
3. Sélectionnez **Accès d’API**.
4. Cliquez sur **Ajouter**.
5. Sélectionnez l’API web et les étendues (autorisations) que vous souhaitez accorder.
6. Cliquez sur **OK**.

> [!NOTE]
> Azure Active Directory B2C ne demande pas leur consentement aux utilisateurs de votre application cliente. À la place, le consentement est fourni par l’administrateur, selon les autorisations configurées entre les applications décrites ci-dessus. Si l’octroi d’une autorisation pour une application est révoqué, tous les utilisateurs qui ont précédemment pu acquérir cette autorisation ne sont plus en mesure de le faire.

## <a name="requesting-a-token"></a>Demande d’un jeton

Pour obtenir un jeton d’accès pour une application de ressource, l’application cliente doit spécifier les autorisations souhaitées dans le paramètre d’**étendue** de la requête. Par exemple, pour obtenir l’autorisation de « lecture » pour l’application de ressource qui a l’URI ID d’application `https://contoso.onmicrosoft.com/notes`, l’étendue est de `https://contoso.onmicrosoft.com/notes/read`. Voici un exemple de demande de code d’autorisation adressée au point de terminaison `authorize`.

```
https://login.microsoftonline.com/<yourTenantId>.onmicrosoft.com/oauth2/v2.0/authorize?p=<yourPolicyId>&client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

Pour acquérir plusieurs autorisations dans la même requête, vous pouvez ajouter plusieurs entrées séparées par des espaces dans le même paramètre d’**étendue**. Par exemple :

URL décodée :

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

URL encodée :

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

Vous pouvez demander plus d’étendues/autorisations pour une ressource que ce qui est accordé pour votre application cliente. Quand c’est le cas, l’appel réussit si au moins une autorisation est accordée. Le jeton d’accès (**access\_token**) a sa demande « scp » remplie uniquement avec les autorisations qui ont été accordées.

> [!NOTE] 
> Nous ne prenons pas en charge les demandes d’autorisation d’accès à deux ressources web différentes dans la même demande. Ce type de demande échoue.

### <a name="special-cases"></a>Cas particuliers

Le standard OpenID Connect spécifie plusieurs valeurs spéciales d’étendue (« scope »). Les étendues spéciales suivantes représentent l’autorisation d’accès au profil de l’utilisateur :

* **OpenID** : cette étendue demande un jeton d’ID
* **offline\_access** : cette étendue demande un jeton d’actualisation (à l’aide de flux de code d’authentification).

Si le paramètre « response\_type » d’une demande `authorize` inclut « token », le paramètre « scope » doit inclure au minimum une autorisation de ressource (autre que « openid » et « offline\_access ») à accorder. Sinon, la demande `authorize` échoue.

## <a name="the-returned-token"></a>Jeton retourné

Dans un jeton d’accès (**access\_token**) correctement configuré (à partir du point de terminaison `authorize` ou `token`), les revendications suivantes sont présentes :

| Nom | Revendication | Description |
| --- | --- | --- |
|Audience |`aud` |L’\*ID d’application\* de la ressource unique à laquelle le jeton accorde l’accès. |
|Scope |`scp` |Les autorisations accordées à la ressource. Plusieurs autorisations octroyées doivent être séparées par une espace. |
|Tiers autorisé |`azp` |\*ID\* de l’application cliente qui a initié la demande. |

Lorsque votre API reçoit le jeton d’accès (**access\_token**), elle doit [valider le jeton](active-directory-b2c-reference-tokens.md) pour prouver que le jeton est authentique et qu’il a les revendications adéquates.

Nous sommes ouverts aux commentaires et suggestions ! Si vous avez des difficultés avec cette rubrique, ou si vous avez des conseils pour améliorer ce contenu, faites-nous part de vos commentaires en bas de la page. En cas de demandes liées aux fonctionnalités, utilisez [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
