---
title: "Vue d’ensemble d’Enterprise Integration | Microsoft Docs"
description: "Utilisez les fonctionnalités d’Enterprise Integration pour activer les processus métier et les scénarios d’intégration à l’aide d&quot;applications logiques"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: dd517c4d-1701-4247-b83c-183c4d8d8aae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: d0508a40cd4ccea1d7476b366b8e1d17a0b78847


---
# <a name="overview-of-the-enterprise-integration-pack"></a>Vue d'ensemble d'Enterprise Integration Pack
## <a name="what-is-the-enterprise-integration-pack"></a>Qu'est-ce que Enterprise Integration Pack ?
Enterprise Integration Pack est la solution basée sur le cloud de Microsoft permettant d'établir de façon transparente des communications Business-to-Business (B2B). Ce pack utilise des protocoles standard, notamment [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) et [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md) pour échanger des messages entre des partenaires commerciaux. Les messages peuvent être sécurisés à l’aide du chiffrement et de signatures numériques. 

Le pack permet aux organisations qui utilisent différents protocoles et formats d'échanger des messages électroniques en convertissant les différents formats dans un format que les systèmes de deux entreprises peuvent interpréter et exploiter. 

Si vous êtes familiarisé avec BizTalk Server ou Microsoft Azure BizTalk Services, vous saurez facilement utiliser les fonctionnalités Enterprise Integration car la plupart des concepts sont similaires. La principale différence réside dans le fait qu'Enterprise Integration utilise les comptes d’intégration pour simplifier le stockage et la gestion des artefacts utilisés dans les communications B2B. 

D'un point de vue architectural, Enterprise Integration Pack repose sur des **comptes d’intégration** qui stockent tous les artefacts pouvant être utilisés pour concevoir, déployer et gérer vos applications B2B. Un compte d’intégration est essentiellement un conteneur dans le cloud, dans lequel vous stockez des artefacts tels que des schémas, des partenaires, des certificats, des mappages et des contrats. Ces artefacts peuvent ensuite être utilisés dans des applications logiques pour créer des flux de travail B2B. Avant de pouvoir utiliser les artefacts dans une application logique, vous devez lier votre compte d’intégration à votre application logique. Une fois ces deux éléments liés, votre application logique pourra accéder aux artefacts du compte d’intégration.  

## <a name="why-should-you-use-enterprise-integration"></a>Pourquoi utiliser l'intégration d’entreprise ?
* Grâce à l’intégration d’entreprise, vous pouvez stocker tous vos artefacts dans un même endroit : votre compte d’intégration. 
* Vous pouvez tirer parti du moteur de vos applications logique et de tous ses connecteurs pour créer des flux de travail B2B et les intégrer dans des applications SaaS tierces, des applications locales et des applications personnalisées
* Vous pouvez également exploiter les fonctions Azure

## <a name="how-to-get-started-with-enterprise-integration"></a>Comment prendre en main Enterprise Integration Pack ?
Vous pouvez créer et gérer des applications B2B à l’aide d'Enterprise Integration Pack via le concepteur d’applications logiques du **portail Azure**.  

Vous pouvez également utiliser [PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "Rubriques PowerShell sur les applications logiques") pour vos applications logiques. 

Voici une vue d’ensemble des étapes requises pour créer des applications dans le portail Azure : ![image de présentation](media/logic-apps-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>Quels sont les scénarios courants ?
Enterprise Integration prend en charge les normes suivantes :   

* EDI - Electronic Data Interchange  
* EAI - Enterprise Application Integration  

## <a name="heres-what-you-need-to-get-started"></a>Voici ce dont vous avez besoin pour commencer
* Un abonnement Azure avec un compte d’intégration
* Visual Studio 2015 pour créer des mappages et des schémas
* [Microsoft Azure Logic Apps Enterprise Integration Tools for Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas)  

## <a name="try-it"></a>Essayer
[Essayez-le maintenant](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) pour déployer votre propre exemple d’application d’envoi et de réception AS2 entièrement fonctionnelle utilisant les fonctionnalités B2B de Logic Apps.

## <a name="learn-more-about"></a>Pour en savoir plus :
* [Accords](../logic-apps/logic-apps-enterprise-integration-agreements.md "Découvrez les contrats d’intégration d’entreprise")
* [Scénarios Business to Business (B2B)](../logic-apps/logic-apps-enterprise-integration-b2b.md "Apprenez à créer des applications logiques avec fonctionnalités B2B")  
* [Certificats](logic-apps-enterprise-integration-certificates.md "Découvrez les certificats d’intégration d’entreprise")
* [Codage/décodage de fichier plat](logic-apps-enterprise-integration-flatfile.md "Découvrez comment encoder et décoder le contenu d’un fichier plat")  
* [Comptes d’intégration](../logic-apps/logic-apps-enterprise-integration-accounts.md "En savoir plus sur les comptes d’intégration")
* [Mappages](../logic-apps/logic-apps-enterprise-integration-maps.md "Découvrez les mappages d’intégration d’entreprise")
* [Partenaires](logic-apps-enterprise-integration-partners.md "Découvrez les partenaires d’intégration d’entreprise")
* [Schémas](logic-apps-enterprise-integration-schemas.md "Découvrez les schémas d’intégration d’entreprise")
* [Validation de message XML](logic-apps-enterprise-integration-xml.md "Découvrez comment valider des messages XML avec vos applications logiques")
* [Transformation XML](logic-apps-enterprise-integration-transform.md "Découvrez les mappages d’intégration d’entreprise")
* [Connecteurs d'intégration d’entreprise](../connectors/apis-list.md "En savoir plus sur les connecteurs Enterprise Integration Pack")
* [Métadonnées du compte d'intégration](../logic-apps/logic-apps-enterprise-integration-metadata.md "En savoir plus sur les métadonnées du compte d’intégration")
* [Suivi des messages B2B](logic-apps-monitor-b2b-message.md "En savoir plus sur le suivi des messages B2B")
* [Suivi des messages B2B dans le portail OMS](logic-apps-track-b2b-messages-omsportal.md "En savoir plus sur le suivi des messages B2B dans le portail OMS")




<!--HONumber=Jan17_HO3-->


