---
title: "Exemples et scénarios - Azure Logic Apps | Microsoft Docs"
description: "Revue d’exemples d’applications logiques dans le cadre de scénarios courants"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: .net,nodejs,java
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 03/14/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: dcf089d680249d0a2f9d748b315076d91c8c78e8
ms.lasthandoff: 03/30/2017


---
# <a name="examples-and-common-scenarios-for-azure-logic-apps"></a>Exemples et scénarios courants relatifs à Azure Logic Apps

Voici quelques exemples et scénarios courants qui vous aideront à en savoir plus sur les nombreux modèles et fonctionnalités d’Azure Logic Apps.

## <a name="key-scenarios-for-logic-apps"></a>Scénarios clés relatifs aux applications logiques

Azure Logic Apps assure une orchestration et une intégration résilientes pour différents services. Le service Logic Apps est un service « sans serveur ». Ainsi, vous n’avez pas à vous soucier de la mise à l’échelle ou des instances. Il vous suffit de définir le flux de travail (déclencheurs et actions). La plateforme sous-jacente gère la mise à l’échelle, la disponibilité et les performances. L’utilisation d’Azure Logic Apps convient parfaitement à tout scénario impliquant la coordination de plusieurs actions, en particulier lorsque plusieurs systèmes entrent en jeu. Voici quelques modèles et exemples.

## <a name="respond-to-triggers-and-extend-actions"></a>Répondre aux déclencheurs et étendre des actions

Chaque application logique commence avec un déclencheur. Par exemple, votre flux de travail peut être démarré par un événement de calendrier, un appel manuel ou un événement issu d’un système externe, tel que le déclencheur « when a file is added to an FTP server » (lorsqu’un fichier est ajouté à un serveur FTP). Actuellement, Azure Logic Apps prend en charge plus de 100 connecteurs prêts à l’emploi, des connecteurs SAP locaux à Azure Cognitive Services. Pour les systèmes et services pour lesquels aucun connecteur n’a été publié, vous pouvez également étendre les applications logiques.

* [Didacticiel expliquant comment créer un tableau de bord social reposant sur l’intelligence artificielle en quelques minutes avec Logic Apps et Power BI](http://aka.ms/logicappsdemo)
* [Créer des actions ou des déclencheurs personnalisés](../logic-apps/logic-apps-create-api-app.md)
* [Configurer des actions de longue durée pour les exécutions de flux de travail](../logic-apps/logic-apps-create-api-app.md)
* [Répondre aux événements et actions externes à l’aide de webhooks](../logic-apps/logic-apps-create-api-app.md)
* [Appeler, déclencher ou imbriquer des flux de travail avec des réponses synchrones aux requêtes HTTP](logic-apps-http-endpoint.md)
* [Didacticiel expliquant comment répondre aux webhooks SMS Twilio et envoyer une réponse textuelle](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="error-handling-logging-and-control-flow-capabilities"></a>Gestion des erreurs, journalisation et fonctionnalités de flux de contrôle

Les applications logiques incluent de puissantes fonctionnalités de flux de contrôle avancé, notamment des conditions, des commutateurs, des boucles et des étendues. Pour garantir la résilience de vos solutions, vous pouvez également implémenter la gestion des erreurs et des exceptions dans vos flux de travail. Pour la notification et les journaux de diagnostic relatifs à l’état d’exécution des flux de travail, Azure Logic Apps fournit également la surveillance et des alertes.

* [Effectuer différentes actions avec les instructions switch](logic-apps-switch-case.md)
* [Traiter les éléments dans des tableaux et collections avec des boucles et des lots dans les applications logiques](logic-apps-loops-and-scopes.md)
* [Gestion des erreurs de création et des exceptions dans un flux de travail](logic-apps-exception-handling.md)
* [Configurer les alertes et diagnostics Azure](logic-apps-monitor-your-logic-apps.md)
* [Cas d’utilisation : comment un prestataire de soins de santé utilise la gestion des exceptions d’application logique pour les flux de travail HL7 FHIR](logic-apps-scenario-error-and-exception-handling.md)

## <a name="deploy-and-manage-logic-apps"></a>Déployer et gérer des applications logiques

Vous pouvez développer et déployer des applications logiques entièrement avec Visual Studio, Visual Studio Team Services ou tout autre outil de compilation automatisée et contrôle de code source. Pour prendre en charge le déploiement de flux de travail et de connexions dépendantes d’un modèle de ressource, les applications logiques utilisent des modèles de déploiement de ressources Azure. Les outils Visual Studio génèrent automatiquement ces modèles, que vous pouvez archiver dans le contrôle de code source à des fins de contrôle de version.

* [Créer un modèle de déploiement automatisé](../logic-apps/logic-apps-create-deploy-template.md)
* [Créer et déployer des applications logiques à partir de Visual Studio](logic-apps-deploy-from-vs.md)
* [Surveiller l’intégrité de vos applications logiques](logic-apps-monitor-your-logic-apps.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Types de contenu, conversions et transformations d’une exécution

Vous pouvez accéder à plusieurs types de contenu, les convertir et les transformer à l’aide des nombreuses fonctions du [langage de définition de flux de travail](http://aka.ms/logicappsdocs) d’Azure Logic Apps. Par exemple, vous pouvez effectuer des conversions entre une chaîne, JSON et XML avec les expressions de flux de travail `@json()` et `@xml()`. Le moteur Logic Apps préserve les types de contenu pour prendre en charge le transfert de contenu entre les services sans perte.

* [Gérer les types de contenu non-JSON](../logic-apps/logic-apps-content-type.md), comme `application/xml`, `application/octet-stream` et`multipart/formdata`
* [Fonctionnement des expressions de flux de travail dans les applications logiques](../logic-apps/logic-apps-author-definitions.md)
* [Référence : Langage de définition de flux de travail Azure Logic Apps](http://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Autres intégrations et fonctionnalités

Les applications logiques offrent également une intégration avec de nombreux services comme Azure Functions, la Gestion des API Azure, Azure App Services et les points de terminaison HTTP personnalisés, par exemple, REST et SOAP.

* [Créer un tableau de bord social en temps réel avec Azure Serverless](logic-apps-scenario-social-serverless.md)
* [Appeler Azure Functions à partir d’applications logiques](../logic-apps/logic-apps-azure-functions.md)
* [Scénario : Déclencher des applications logiques avec Azure Functions](logic-apps-scenario-function-sb-trigger.md)
* [Blog : Appeler des points de terminaison SOAP à partir d’applications logiques](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="next-steps"></a>Étapes suivantes

- [Gérer les erreurs et les exceptions dans Azure Logic Apps](logic-apps-exception-handling.md)
- [Créer des définitions de flux de travail avec le langage de définition de flux de travail](logic-apps-author-definitions.md)
- [Envoyer vos questions, commentaires ou suggestions concernant les améliorations que nous pourrions apporter à Azure Logic Apps](https://feedback.azure.com/forums/287593-logic-apps)
