<properties
   pageTitle="Scénarios et exemples d’applications logiques | Microsoft Azure"
   description="Découvrez des exemples courants d’applications logiques et apprenez à implémenter des scénarios courants"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="msftman"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/25/2016"
   ms.author="deonhe"/>

# Scénarios courants et exemples d’applications logiques

Ce document présente des scénarios courants et des exemples afin de vous aider à comprendre comment utiliser les applications logiques pour automatiser les processus d’entreprise.

## Déclenchement d’une application logique

Il existe plusieurs moyens de déclencher une application logique à partir d’une autre application. En voici quelques-uns :

- [Requête HTTP (POST)](app-service-logic-http-endpoint.md)
- [Réception d’un webhook](app-service-logic-create-api-app.md)
- [Interrogation d’un point de terminaison](app-service-logic-create-api-app.md)

### Scénarios

- [Réponse synchrone à une demande d’une application logique](app-service-logic-http-endpoint.md)

## Actions personnalisées et de longue durée

- [Création d’une activité personnalisée](app-service-logic-create-api-app.md)

## Déploiement et gestion d’une application logique

- [Création d’un modèle de déploiement d’applications logiques](app-service-logic-create-deploy-template.md)
- [Diagnostic des problèmes avec une application logique](app-service-logic-diagnosing-failures.md)
- [Déploiement d’une application logique à partir de Visual Studio](app-service-logic-deploy-from-vs.md)
- [Analyse des applications logiques](app-service-logic-monitor-your-logic-apps.md)

## Types de contenu, conversions et transformations

Le [langage de définition de flux de travail](http://aka.ms/logicappsdocs) Logic Apps contient de nombreuses fonctions permettant de convertir et d’utiliser différents types de contenu. Le moteur fait aussi tout son possible pour conserver les types de contenu comme flux de données tout au long du flux de travail.

- [Gestion des types de contenu](app-service-logic-content-type.md) comme application/json, application/xml et plain/text
- [Création de définitions de flux de travail](app-service-logic-author-definitions.md)
- [Référence sur le langage de définition de flux de travail](http://aka.ms/logicappsdocs)

## Lots et boucles

- [SplitOn](app-service-logic-loops-and-scopes.md)
- [ForEach](app-service-logic-loops-and-scopes.md)
- [Until](app-service-logic-loops-and-scopes.md)

## Intégration avec Azure Functions

- [Intégration aux fonctions Azure](app-service-logic-azure-functions.md)

### Scénarios

- [Utilisation d’Azure Functions en tant que déclencheur Service Bus](app-service-logic-scenario-function-sb-trigger.md)

## HTTP, REST et SOAP

 - [Appel de SOAP](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)


Nous continuerons à ajouter des exemples et des scénarios dans ce document. Utilisez la section de commentaires ci-dessous pour nous faire savoir quels exemples ou scénarios vous aimeriez découvrir.

<!---HONumber=AcomDC_0727_2016-->