---
title: Choisir entre Flow, Logic Apps, Functions et WebJobs | Microsoft Docs
description: "Découvrez une analyse comparative des services d’intégration cloud de Microsoft pour déterminer quel(s) service(s) utiliser."
services: functions,app-service\logic
documentationcenter: na
author: cephalin
manager: wpickett
tags: 
keywords: "microsoft flow, flow, logic apps, azure functions, functions, azure webjobs, webjobs, traitement d’événements, calcul dynamique, architecture sans serveur"
ms.assetid: e9ccf7ad-efc4-41af-b9d3-584957b1515d
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/08/2016
ms.author: chrande; glenga
translationtype: Human Translation
ms.sourcegitcommit: ee94e074c574bc2e6e1a52dbfd6bd0ae102cb342
ms.openlocfilehash: 0d007bf2a0f5a40fcb7ac6dfe5beff05a4310701


---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Choisir entre Flow, Logic Apps, Functions et WebJobs
Cet article dresse une analyse comparative des services suivants de Microsoft Cloud, qui permettent tous de résoudre les problèmes d’intégration et d’automatiser les processus d’entreprise :

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service-web/web-sites-create-web-jobs.md)

Tous ces services sont utiles pour « coller » ensemble des systèmes disparates. Ils peuvent tous définir des entrées, des actions, des conditions et des sorties. Vous pouvez exécuter chacun d’eux selon une planification ou à l’aide d’un déclencheur. Toutefois, chaque service apporte sa propre valeur ajoutée, et leur comparaison ne vise pas à déterminer lequel est le meilleur, mais plutôt lequel convient le mieux à une situation donnée. Une combinaison de ces services constitue souvent le meilleur moyen de créer rapidement une solution d’intégration complète et évolutive.

<a name="flow"></a>

## <a name="flow-vs-logic-apps"></a>Flow vs Logic Apps
Microsoft Flow et Azure Logic Apps peuvent être traités ensemble, car il s’agit dans les deux cas de services d’intégration *orientés configuration* , ce qui facilite la création de processus et de flux de travail, ainsi que l’intégration avec diverses applications SaaS et d’entreprise. 

* Flow est basé sur Logic Apps.
* Ils présentent le même concepteur de flux de travail.
* [connecteurs](../connectors/apis-list.md) qui fonctionnent dans l’un fonctionnent également dans l’autre.

Les flux de Flow permettent à n’importe quel employé de bureau d’effectuer des intégrations simples (par exemple recevoir des SMS pour des e-mails importants) sans faire appel à des développeurs ou au département informatique. Les applications de Logic Apps prennent quant à elles en charge des intégrations avancées ou stratégiques (par exemple des processus B2B) nécessitant des pratiques de développement et de sécurité au niveau de l’entreprise. Il est courant qu’un flux de travail d’entreprise se complexifie au fil du temps. Par conséquent, vous pouvez commencer par un flux et le convertir ensuite en application logique selon vos besoins.

Le tableau suivant vous aide à déterminer si Flow ou Logic Apps convient le mieux à une intégration donnée.

|  | Flux | Logic Apps |
| --- | --- | --- |
| Public ciblé |Employés de bureau, utilisateurs professionnels |Professionnels de l’informatique, développeurs |
| Scénarios |Libre-service |Intégration stratégique |
| Outil de conception |Dans le navigateur, interface utilisateur uniquement |Dans le navigateur et [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [mode Code](../logic-apps/logic-apps-author-definitions.md) disponible |
| DevOps |Ad hoc, développement en production |Contrôle de code source, tests, support, et automatisation et gestion simplifiée dans [Azure Resource Management](../logic-apps/logic-apps-arm-provision.md) |
| Expérience administrateur |[https://flow.microsoft.com](https://flow.microsoft.com) |[https://portal.azure.com](https://portal.azure.com) |
| Sécurité |Pratiques standard : [souveraineté des données](https://wikipedia.org/wiki/Technological_Sovereignty), [chiffrement au repos](https://wikipedia.org/wiki/Data_at_rest#Encryption) pour les données sensibles, etc. |Garantie de sécurité d’Azure : [Azure Security](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Security Center](https://azure.microsoft.com/services/security-center/), [journaux d’audit](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/), etc. |

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>Functions vs WebJobs
Azure Functions et Azure App Service WebJobs peuvent être traités ensemble, car il s’agit dans les deux cas de services d’intégration *orientés code* et conçus pour les développeurs. Ils vous permettent d’exécuter un script ou un bloc de code en réponse à divers événements, tels que [de nouveaux objets blob de stockage](functions-bindings-storage.md) ou [une requête WebHook](functions-bindings-http-webhook.md). Voici leurs points communs : 

* Ils sont tous deux basés sur [Azure App Service](../app-service/app-service-value-prop-what-is.md) et proposent des fonctionnalités telles que [le contrôle de code source](../app-service-web/app-service-continuous-deployment.md), [l’authentification](../app-service/app-service-authentication-overview.md) et [la surveillance](../app-service-web/web-sites-monitor.md).
* Il s’agit dans les deux cas de services destinés aux développeurs.
* Ils prennent en charge les langages standard de script et de programmation.
* Ils prennent en charge NuGet et NPM.

Functions est l’évolution naturelle de WebJobs dans la mesure où il reprend les atouts de WebJobs en les améliorant. Les améliorations incluent : 

* Développement, test et exécution de code simplifiés, directement dans le navigateur.
* Intégration native avec d’autres services Azure et des services tiers tels que [GitHub WebHooks](https://developer.github.com/webhooks/creating/).
* Paiement à l’utilisation, sans avoir à payer pour un [plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
* [Mise à l’échelle dynamique](functions-scale.md)et automatique.
* Pour les clients existants d’App Service, l’utilisation d’un plan App Service reste possible (pour tirer parti des ressources sous-utilisées).
* Intégration avec Logic Apps.

Le tableau suivant récapitule les différences entre Functions et WebJobs :

|  | Fonctions | WebJobs |
| --- | --- | --- |
| Mise à l'échelle |Mise à l’échelle sans configuration |Mise à l’échelle avec un plan App Service |
| Tarification |Paiement à l’utilisation ou dans le cadre d’un plan App Service |Dans le cadre d’un plan App Service |
| Type d’exécution |Déclenchée, planifiée (par un déclencheur de minuteur) |Déclenchée, continue, planifiée |
| Événements déclencheurs |[Minuteur](functions-bindings-timer.md), [Azure DocumentDB](functions-bindings-documentdb.md), [Azure Event Hubs](functions-bindings-event-hubs.md), [HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md), [Azure App Service Mobile Apps](functions-bindings-mobile-apps.md), [Azure Notification Hubs](functions-bindings-notification-hubs.md), [Azure Service Bus](functions-bindings-service-bus.md), [Azure Storage](functions-bindings-storage.md) |[Stockage Azure](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), [Azure Service Bus](../app-service-web/websites-dotnet-webjobs-sdk-service-bus.md) |
| Développement dans le navigateur |x | |
| Scripts Windows |Expérimental |x |
| PowerShell |Expérimental |x |
| C# |x |x |
| F# |x | |
| Bash |Expérimental |x |
| PHP |Expérimental |x |
| Python |Expérimental |x |
| JavaScript |x |x |

Le choix entre Functions et WebJobs dépend en définitive de l’utilisation que vous faites d’App Service. Si vous avez une application App Service pour laquelle vous souhaitez exécuter des extraits de code et que vous souhaitez les gérer ensemble dans le même environnement DevOps, vous devez utiliser WebJobs. Si vous souhaitez exécuter des extraits de code pour d’autres services Azure ou même des applications tierces, si vous souhaitez gérer vos extraits de code d’intégration séparément de vos applications App Service ou encore si vous souhaitez appeler vos extraits de code depuis une application logique, vous devez tirer parti de toutes les améliorations offertes par Functions.  

<a name="together"></a>

## <a name="flow-logic-apps-and-functions-together"></a>Flow, Logic Apps et Functions ensemble
Comme mentionné précédemment, le service qui vous convient le mieux dépend de votre situation. 

* Pour une optimisation simple de votre activité, utilisez Flow.
* Si votre scénario d’intégration est trop avancé pour Flow ou si vous avez besoin de fonctionnalités de développement et d’une conformité en matière de sécurité, utilisez Logic Apps.
* Si une étape de votre scénario d’intégration nécessite une transformation hautement personnalisée ou du code spécialisé, écrivez une application Function App, puis déclenchez une fonction en tant qu’action dans votre application logique.

Vous pouvez appeler une application logique dans un flux. Vous pouvez également appeler une fonction dans une application logique et une application logique dans une fonction. L’intégration entre Flow, Logic Apps et Functions continue de s’améliorer au fil du temps. Vous pouvez créer quelque chose dans un service et l’utiliser dans les autres services. Par conséquent, tout investissement dans ces trois technologies est pertinent.

## <a name="next-steps"></a>Étapes suivantes
Prenez en main chacun de ces services en créant votre premier flux, application logique, application Function App ou WebJob. Cliquez sur l’un des liens suivants :

* [Prise en main de Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md)
* [Créer votre première fonction Azure](functions-create-first-azure-function.md)
* [Déployer des tâches web à l’aide de Visual Studio](../app-service-web/websites-dotnet-deploy-webjobs.md)

Vous pouvez également obtenir des informations supplémentaires sur ces services d’intégration en cliquant sur les liens suivants :

* [Leveraging Azure Functions & Azure App Service for integration scenarios (Tirer parti d’Azure Functions et Azure App Service pour les scénarios d’intégration), par Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Integrations Made Simple (Les intégrations simplifiées), par Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Diffusion web sur Logic Apps](http://aka.ms/logicappslive)
* [Forum Aux Questions sur Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)
* [Ressources de documentation relatives à Azure WebJobs](../app-service-web/websites-webjobs-resources.md)




<!--HONumber=Jan17_HO3-->


