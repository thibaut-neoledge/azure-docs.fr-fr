---
title: Scénarios et exemples d’applications logiques | Microsoft Docs
description: Découvrez des exemples courants d’applications logiques et apprenez à implémenter des scénarios courants
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/11/2016
ms.author: jehollan

---
# Scénarios courants et exemples d’applications logiques
Ce document présente des scénarios courants et des exemples afin de vous aider à comprendre comment utiliser les applications logiques pour automatiser les processus d’entreprise.

## Déclencheurs et actions personnalisés
Il existe plusieurs moyens de déclencher une application logique à partir d’une autre application. Voici quelques exemples courants :

* [Création d’un déclencheur ou d’une action personnalisés](app-service-logic-create-api-app.md)
* [Actions de longue durée](app-service-logic-create-api-app.md)
* [Déclencheur de requête HTTP (POST)](app-service-logic-http-endpoint.md)
* [Déclencheurs webhook et actions](app-service-logic-create-api-app.md)
* [Déclencheurs d'interrogation](app-service-logic-create-api-app.md)

### Scénarios
* [Réponse synchrone à une requête](app-service-logic-http-endpoint.md)
* [Demande/réponse avec SMS](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## Gestion et journalisation des erreurs
* [Gestion des erreurs et des exceptions](app-service-logic-exception-handling.md)
* [Configurer les alertes et diagnostics Azure](app-service-logic-monitor-your-logic-apps.md)

### Scénarios
* [Cas d’utilisation : Gestion des erreurs et des exceptions](app-service-logic-scenario-error-and-exception-handling.md)

## Déploiement et gestion
* [Créer un déploiement automatisé](app-service-logic-create-deploy-template.md)
* [Créer et déployer des applications logiques à partir de Visual Studio](app-service-logic-deploy-from-vs.md)
* [Surveiller les applications logiques](app-service-logic-monitor-your-logic-apps.md)

## Types de contenu, conversions et transformations
Le [langage de définition de flux de travail](http://aka.ms/logicappsdocs) Logic Apps contient de nombreuses fonctions permettant de convertir et d’utiliser différents types de contenu. Le moteur fait aussi tout son possible pour conserver les types de contenu comme flux de données tout au long du flux de travail.

* [Gestion des types de contenu](app-service-logic-content-type.md) comme application/json, application/xml et brut/texte
* [Création de définitions de flux de travail](app-service-logic-author-definitions.md)
* [Référence sur le langage de définition de flux de travail](http://aka.ms/logicappsdocs)

## Lots et boucles
* [SplitOn](app-service-logic-loops-and-scopes.md)
* [ForEach](app-service-logic-loops-and-scopes.md)
* [Until](app-service-logic-loops-and-scopes.md)

## Intégration avec Azure Functions
* [Intégration à Azure Functions](app-service-logic-azure-functions.md)

### Scénarios
* [Utilisation d’Azure Functions en tant que déclencheur Service Bus](app-service-logic-scenario-function-sb-trigger.md)

## HTTP, REST et SOAP
* [Appel de SOAP](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

Nous continuerons à ajouter des exemples et des scénarios dans ce document. Utilisez la section de commentaires ci-dessous pour nous faire savoir quels exemples ou scénarios vous aimeriez découvrir.

<!---HONumber=AcomDC_0817_2016-->