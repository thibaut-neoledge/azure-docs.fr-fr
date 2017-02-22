---
title: "Configuration de l’authentification et de l’autorisation Service Bus | Microsoft Docs"
description: "Vue d’ensemble de l’authentification de Signature d’accès partagé (SAS)."
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
ms.date: 01/13/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2c1a1f3b73466526b13bcfeb4580335390506c23
ms.openlocfilehash: 14dfe58f6296a4ec516845bace456ffd59fa608a


---
# <a name="service-bus-authentication-and-authorization"></a>Authentification et de autorisation Service Bus
Les applications peuvent s’authentifier dans Azure Service Bus à l’aide de l’authentification de la Signature d’accès partagé (SAS) ou via Azure Active Directory Access Control (également appelé Service de contrôle d’accès ou ACS). L’authentification par Signature d’accès partagé permet aux applications de s’authentifier auprès de Service Bus à l’aide d’une clé d’accès sur l’espace de noms, ou sur l’entité de messagerie (file d’attente ou rubrique) avec des droits spécifiques associés. Vous pouvez ensuite utiliser cette clé pour générer un jeton de signature d’accès partagé que les clients peuvent ensuite utiliser pour s’authentifier auprès de Service Bus.

> [!IMPORTANT]
> SAS est recommandée sur ACS, car elle offre un schéma d’authentification simple, flexible et facile à utiliser pour le Service Bus. Les applications peuvent utiliser des SAP dans les scénarios dans lesquels elles n’ont pas à tenir compte de la notion d’« utilisateur » autorisé. 

## <a name="shared-access-signature-authentication"></a>Authentification avec une signature d’accès partagé
[L’authentification SAP](service-bus-sas-overview.md) vous permet d’accorder un accès utilisateur aux ressources Service Bus avec des droits spécifiques. L’authentification SAP dans Service Bus implique la configuration d’une clé de chiffrement avec les droits associés sur une ressource Service Bus. Les clients peuvent alors accéder à cette ressource en présentant un jeton SAS qui se compose de la ressource URI à laquelle accéder et une échéance signée avec la clé configurée.

Vous pouvez configurer des clés pour SAS dans un espace de noms Service Bus. La clé s’applique à toutes les entités de messagerie dans cet espace de noms. Vous pouvez également configurer des clés sur les rubriques et files d’attente Service Bus. SAS est également pris en charge sur les relais Service Bus.

Pour utiliser des associations de sécurité, vous pouvez configurer un objet [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) sur un espace de noms, une file d’attente ou une rubrique comprenant les éléments suivants :

* *Nom de clé* qui identifie la règle.
* *clé primaire* est une clé de chiffrement utilisée pour signer/valider les jetons SAS.
* *clé secondaire* est une clé de chiffrement utilisée pour signer/valider les jetons SAS.
* *droits* représentant la collection des droits écouter, envoyer ou gérer les droits accordés.

Les règles d’autorisation configurées au niveau de l’espace de noms peuvent accorder l’accès à toutes les entités dans un espace de noms pour les clients avec des jetons signés à l’aide de la clé correspondante. Un maximum de 12 règles d’autorisation peut être configuré sur un espace de noms, une file d’attente ou une rubrique Service Bus. Par défaut, un élément [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) avec tous les droits est configuré pour chaque espace de noms dès sa mise en service initiale.

Pour accéder à une entité, le client requiert un jeton SAP créé à l’aide d’une règle [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)spécifique. Le jeton SAS est généré à l’aide du code HMAC-SHA256 d’une chaîne de ressource qui se compose de l’URI de la ressource à laquelle vous souhaitez accéder et d’une échéance avec une clé de chiffrement associée à la règle d’autorisation.

La prise en charge de l’authentification SAS pour Service Bus est incluse dans le Kit de développement Azure .NET SDK versions 2.0 et ultérieures. SAP inclut l’assistance pour [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Toutes les API qui acceptent une chaîne de connexion en tant que paramètre incluent la prise en charge des chaînes de connexion des services SAS.

## <a name="acs-authentication"></a>Authentification ACS
L’authentification de service Bus via ACS est gérée via un espace de noms ACS compagnon « -sb ». Si vous souhaitez qu’un espace de noms ACS associé soit créé pour un espace de noms Service Bus, vous ne pouvez pas créer votre espace de noms Service Bus à l’aide du portail Azure Classic. Vous devez créer l’espace de noms à l’aide de l’applet de commande PowerShell [New-AzureSBNamespace](https://docs.microsoft.com/powershell/servicemanagement/azure.compute/v1.6.1/New-AzureSBNamespace). Par exemple :

```powershell
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $true
```

Pour éviter de créer un espace de noms ACS, exécutez la commande suivante :

```powershell
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $false
```

Par exemple, si vous créez un espace de noms Service Bus appelé **contoso.servicebus.windows.net**, un espace de noms compagnon appelé **contoso-sb.accesscontrol.windows.net** est configuré automatiquement. Tous les espaces de noms créés avant août 2014 sont créés avec un espace de noms ACS compagnon.

Une identité de service par défaut « propriétaire », avec tous les droits, est configurée par défaut dans cet espace de noms ACS associé. Vous pouvez contrôler très précisément n’importe quelle entité Service Bus via ACS en configurant les relations d’approbation appropriées. Vous pouvez configurer des identités de service supplémentaires pour gérer l’accès aux entités Service Bus.

Pour accéder à une entité, le client demande un jeton SWT à ACS avec la revendication appropriée, en présentant ses informations d’authentification. Le jeton SWT doit être envoyé dans le cadre de la demande Service Bus pour autoriser le client à accéder à l’entité.

La prise en charge de l’authentification ACS pour Service Bus est incluse dans le Kit de développement Azure .NET SDK versions 2.0 et ultérieures. Cette authentification prend en charge un [SharedSecretTokenProvider](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). Toutes les API qui acceptent une chaîne de connexion en tant que paramètre incluent la prise en charge des chaînes de connexion des services ACS.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la signature d’accès partagé (SAS), consultez [Authentification par signature d’accès partagé avec Service Bus](service-bus-shared-access-signature-authentication.md) .

Pour obtenir une vue d’ensemble de SAP dans Service Bus, consultez [Signatures d’accès partagé](service-bus-sas-overview.md).



<!--HONumber=Jan17_HO3-->


