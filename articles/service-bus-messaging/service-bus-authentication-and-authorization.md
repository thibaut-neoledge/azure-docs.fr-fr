---
title: "Configuration de l’authentification et de l’autorisation Service Bus Azure | Microsoft Docs"
description: "Authentifiez des applications dans Service Bus avec l’authentification Signature d’accès partagé (SAS)."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 28fb41499c919e5006f1be7daa97610c2a0583af
ms.contentlocale: fr-fr
ms.lasthandoff: 08/10/2017

---
# <a name="service-bus-authentication-and-authorization"></a>Authentification et de autorisation Service Bus

Les applications peuvent s’authentifier auprès d’Azure Service Bus à l’aide de l’authentification par signature d’accès partagé. L’authentification par Signature d’accès partagé permet aux applications de s’authentifier auprès de Service Bus à l’aide d’une clé d’accès sur l’espace de noms, ou sur l’entité de messagerie (file d’attente ou rubrique) avec des droits spécifiques associés. Vous pouvez ensuite utiliser cette clé pour générer un jeton de signature d’accès partagé que les clients peuvent ensuite utiliser pour s’authentifier auprès de Service Bus.

> [!IMPORTANT]
> Vous devez utiliser SAS au lieu d’Azure Active Directory Access Control (également appelé Access Control Service ou ACS), car ACS est aujourd’hui déprécié. SAS offre un schéma d’authentification simple, flexible et facile à utiliser pour Service Bus. Les applications peuvent utiliser des SAP dans les scénarios dans lesquels elles n’ont pas à tenir compte de la notion d’« utilisateur » autorisé. Pour plus d’informations, consultez [ce billet de blog](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/)

## <a name="shared-access-signature-authentication"></a>Authentification avec une signature d’accès partagé

[L’authentification SAP](service-bus-sas.md) vous permet d’accorder un accès utilisateur aux ressources Service Bus avec des droits spécifiques. L’authentification SAP dans Service Bus implique la configuration d’une clé de chiffrement avec les droits associés sur une ressource Service Bus. Les clients peuvent alors accéder à cette ressource en présentant un jeton SAS qui se compose de la ressource URI à laquelle accéder et une échéance signée avec la clé configurée.

Vous pouvez configurer des clés pour SAS dans un espace de noms Service Bus. La clé s’applique à toutes les entités de messagerie dans cet espace de noms. Vous pouvez également configurer des clés sur les rubriques et files d’attente Service Bus. SAP est également pris en charge par [Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md).

Pour utiliser SAS, vous pouvez configurer un objet [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) sur un espace de noms, une file d’attente ou une rubrique. Cette règle se compose des éléments suivants :

* *Nom de clé* qui identifie la règle.
* *clé primaire* est une clé de chiffrement utilisée pour signer/valider les jetons SAS.
* *clé secondaire* est une clé de chiffrement utilisée pour signer/valider les jetons SAS.
* *droits* représentant la collection des droits écouter, envoyer ou gérer les droits accordés.

Les règles d’autorisation configurées au niveau de l’espace de noms peuvent accorder l’accès à toutes les entités dans un espace de noms pour les clients avec des jetons signés à l’aide de la clé correspondante. Un maximum de 12 règles d’autorisation peut être configuré sur un espace de noms, une file d’attente ou une rubrique Service Bus. Par défaut, un élément [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) avec tous les droits est configuré pour chaque espace de noms dès sa mise en service initiale.

Pour accéder à une entité, le client requiert un jeton SAP créé à l’aide d’une règle [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)spécifique. Le jeton SAS est généré à l’aide du code HMAC-SHA256 d’une chaîne de ressource qui se compose de l’URI de la ressource à laquelle vous souhaitez accéder et d’une échéance avec une clé de chiffrement associée à la règle d’autorisation.

La prise en charge de l’authentification SAS pour Service Bus est incluse dans le Kit de développement Azure .NET SDK versions 2.0 et ultérieures. SAP inclut l’assistance pour [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Toutes les API qui acceptent une chaîne de connexion en tant que paramètre incluent la prise en charge des chaînes de connexion des services SAS.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la signature d’accès partagé (SAP), consultez [Authentification de Service Bus avec les signatures d’accès partagé](service-bus-sas.md).
- [Modifications des espaces de noms ACS.](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/)
- Pour obtenir les informations correspondantes sur les autorisations et l’authentification Azure Relay, consultez [Authentification et autorisation Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md). 


