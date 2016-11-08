---
title: Authentification de service à service Azure AD à l’aide d’OAuth2.0 | Microsoft Docs
description: Cet article explique comment utiliser des messages HTTP pour mettre en œuvre l’authentification de service à service à l’aide du flux d’octroi des informations d’identification du client OAuth2.0.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: priyamo

---
# <a name="service-to-service-calls-using-client-credentials"></a>Appels de service à service à l’aide des informations d’identification du client
Le flux d’octroi des informations d’identification du client OAuth2.0 permet à un service web (un *client confidentiel*) d’utiliser ses propres informations d’identification pour s’authentifier lorsqu’il appelle un autre service web, au lieu d’emprunter l’identité d’un utilisateur. Dans ce scénario, le client est généralement un service web de niveau intermédiaire, un service démon ou un site web.

## <a name="client-credentials-grant-flow-diagram"></a>Diagramme représentant le flux d’octroi des informations d’identification du client
Le diagramme suivant explique comment fonctionne le flux d’octroi des informations d’identification du client dans Azure Active Directory (Azure AD).

![Flux d’octroi des informations d’identification du client OAuth2.0](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. L’application cliente s’authentifie auprès du point de terminaison d’émission de jeton Azure AD et demande un jeton d’accès.
2. Le point de terminaison d’émission de jeton Azure AD émet le jeton d’accès.
3. Le jeton d’accès est utilisé pour l’authentification auprès de la ressource sécurisée.
4. Les données de la ressource sécurisée sont renvoyées à l’application web.

## <a name="register-the-services-in-azure-ad"></a>Inscription des services dans Azure AD
Inscrivez à la fois le service appelant et le service de destination dans Azure Active Directory (Azure AD). Pour obtenir des instructions détaillées, consultez l’article [Adding, Updating, and Removing an App (Ajouter, mettre à jour et supprimer une application)](active-directory-integrating-applications.md#BKMK_Native)

## <a name="request-an-access-token"></a>Demander un jeton d’accès
Pour demander un jeton d’accès, envoyez une requête HTTP POST au point de terminaison Azure AD propre au client.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Demande de jeton d’accès de service à service
Une demande de jeton d’accès de service à service contient les paramètres suivants.

| Paramètre |  | Description |
| --- | --- | --- |
| response_type |required |Spécifie le type de réponse demandé. Dans un flux d’octroi des informations d’identification du client, la valeur doit être **client_credentials**. |
| client_id |required |Spécifie l’ID de client Azure AD du service web appelant. Pour rechercher l’ID de client de l’application appelante, dans le portail de gestion Azure, cliquez successivement sur **Active Directory**, le répertoire, l’application, puis sur **Configurer**. |
| client_secret |required |Entrez une clé enregistrée pour le service web appelant dans Azure AD. Pour créer une clé, dans le portail de gestion Azure, cliquez successivement sur **Active Directory**, le répertoire, l’application, puis sur **Configurer**. |
| resource |required |Entrez l’URI ID d’application du service web de destination. Pour rechercher l’URI ID d’application, dans le portail de gestion Azure, cliquez successivement sur **Active Directory**, le répertoire, l’application, puis sur **Configurer**. |

## <a name="example"></a>Exemple
La requête HTTP POST suivante demande un jeton d’accès pour le service web https://service.contoso.com/. `client_id` identifie le service web qui demande le jeton d’accès.

```
POST contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

## <a name="service-to-service-access-token-response"></a>Réponse de jeton d’accès de service à service
Une réponse affirmative contient une réponse JSON OAuth 2.0 avec les paramètres suivants.

| Paramètre | Description |
| --- | --- |
| access_token |Le jeton d’accès demandé. Le service web appelant peut utiliser ce jeton pour s’authentifier auprès du service web de destination. |
| access_type |Indique la valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est le **jeton porteur**. Pour plus d’informations sur les jetons du porteur, consultez le document [OAuth 2.2 Authorization Framework: Bearer Token Usage (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)(Infrastructure d’autorisation OAuth 2.0 : Utilisation des jetons du porteur [RFC 6750]). |
| expires_in |La durée de validité (en secondes) du jeton d’accès. |
| expires_on |L’heure d’expiration du jeton d’accès. La date est représentée en nombre de secondes à partir du 1er janvier 1970 (1970-01-01T0:0:0Z) UTC jusqu’au moment de l’expiration. Cette valeur est utilisée pour déterminer la durée de vie des jetons en cache. |
| resource |L’URI ID d’application du service web de destination. |

## <a name="example"></a>Exemple
L’exemple suivant illustre une réponse affirmative à une demande de jeton d’accès à un service web.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Voir aussi
* [OAuth 2.0 dans Azure AD](active-directory-protocols-oauth-code.md)

<!--HONumber=Oct16_HO2-->


