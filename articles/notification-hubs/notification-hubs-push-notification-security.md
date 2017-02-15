---
title: "Sécurité des concentrateurs de notification"
description: "Cette rubrique décrit la sécurité des hubs de notification Azure."
services: notification-hubs
documentationcenter: .net
author: ysxu
manager: erikre
editor: 
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 9128fa76cc0b0f4d879faaaf98d55b5b9180f46e


---
# <a name="security"></a>Sécurité
## <a name="overview"></a>Vue d'ensemble
Cette rubrique décrit le modèle de sécurité d'Azure Notification Hubs. Notification Hubs étant une entité Service Bus, elle implémente le même modèle de sécurité que Service Bus. Pour plus d'informations, consultez les rubriques [Authentification Service Bus](https://msdn.microsoft.com/library/azure/dn155925.aspx) .

## <a name="shared-access-signature-security-sas"></a>Sécurité Signature d'accès partagé (SAP)
Notification Hubs implémente un modèle de sécurité de niveau entité appelé SAP (Signature d'accès partagé). Ce schéma permet aux entités de messagerie de déclarer dans leur description jusqu'à 12 règles d'autorisation accordant des droits sur cette entité.

Chaque règle contient un nom, une valeur clé (secret partagé) et un ensemble de droits, comme expliqué dans la section « Revendications de sécurité ». Lorsque vous créez un concentrateur de notification, deux règles sont automatiquement créées : une avec des droits d'écoute (utilisée par l'application cliente) et l'autre avec tous les droits (utilisé par le serveur principal d'application).

Lors de la gestion de l'inscription à partir d’applications clientes, si les informations envoyées via des notifications ne sont pas sensibles (par exemple, des mises à jour météorologiques), une méthode courante pour accéder à un concentrateur de notification consiste à attribuer la valeur clé de la règle d'accès en écoute uniquement à l'application cliente et à attribuer la valeur clé de la règle d’accès complet au serveur principal.

Il est déconseillé d'incorporer la valeur clé dans les applications clientes Windows Store. Pour éviter l'incorporation de la valeur clé, l'application cliente peut la récupérer à partir du serveur principal d'application au démarrage.

Il est important de comprendre que la clé avec un accès en écoute permet à une application cliente de s’inscrire pour n’importe quelle balise. Si votre application doit limiter les inscriptions à des balises spécifiques pour des clients spécifiques (par exemple, lorsque les balises représentent des ID utilisateur), votre serveur principal d’application doit effectuer ces inscriptions. Pour plus d'informations, consultez Gestion des inscriptions. Notez que de cette façon, l'application cliente ne disposera pas d'un accès direct à Notification Hubs.

## <a name="security-claims"></a>Revendications de sécurité
Comme pour les autres entités, les opérations Notification Hubs sont autorisées pour trois revendications de sécurité : Écoute, Envoyer et Gérer.

| Revendication | Description | Opérations autorisées |
| --- | --- | --- |
| Écouter |Créer/mettre à jour, lire et supprimer des inscriptions uniques |Créer/mettre à jour une inscription<br><br>Lire une inscription<br><br>Lire toutes les inscriptions pour un handle<br><br>Supprimer une inscription |
| Envoyer |Envoyer de messages au concentrateur de notification |Envoyer un message |
| Gérer |Opérations CRUD sur Notification Hubs (y compris la mise à jour des informations d'identification PNS et les clés de sécurité) et lecture des inscriptions en fonction des balises |Créer/Mettre à jour/Lire/Supprimer des hubs de notification<br><br>Lire des inscriptions par balise |

Notification Hubs accepte les revendications accordées par les jetons de contrôle d'accès Microsoft Azure et les jetons de signature générés avec des clés partagées configurées directement sur le concentrateur de notification.




<!--HONumber=Nov16_HO3-->


