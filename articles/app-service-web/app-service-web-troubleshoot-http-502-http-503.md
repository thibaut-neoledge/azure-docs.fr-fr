---
title: Corriger les erreurs 502 Passerelle incorrecte, 503 Services indisponibles | Microsoft Docs
description: "Corriger les erreurs « 502 Passerelle incorrecte » et « 503 Service indisponible » dans votre application web hébergée dans Azure App Service."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
tags: top-support-issue
keywords: 502 Passerelle incorrecte, 503 Service indisponible, erreur 503, erreur 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f6ef85e0c05911bb3ad13a8ffb029bf9fadd8bfd


---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-your-azure-web-apps"></a>Résoudre les erreurs HTTP de type « 502 Passerelle incorrecte » et « 503 Service indisponible » dans vos applications web Azure
« 502 Passerelle incorrecte » et « 503 Service indisponible » sont des erreurs courantes dans votre application web hébergée dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Cet article vous permet de résoudre ces erreurs.

Si vous avez besoin d'aide supplémentaire concernant n'importe quel point de cet article, contactez les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site de support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.

## <a name="symptom"></a>Symptôme
Lorsque vous accédez à l'application web, elle retourne un message d’erreur HTTP « 502 Passerelle incorrecte » ou HTTP « 503 Service non disponible ».

## <a name="cause"></a>Cause :
Ce problème est souvent dû à des problèmes au niveau de l’application, tels que :

* demandes exigeant beaucoup de temps ;
* un taux d’utilisation élevé de la mémoire/UC par l’application ;
* un blocage de l’application en raison d’une exception.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Étapes de dépannage pour résoudre les erreurs « 503 Service indisponible » et « 502 Passerelle incorrecte »
Le dépannage peut être divisé en trois tâches distinctes, dans un ordre séquentiel :

1. [Observer et contrôler le comportement de l’application](#observe)
2. [Collecter les données](#collect)
3. [Résoudre le problème](#mitigate)

[App Service Web Apps](/services/app-service/web/) vous offre différentes options à chaque étape.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Observer et contrôler le comportement de l'application
#### <a name="track-service-health"></a>Suivi de l’état du service
Microsoft Azure publie chaque interruption du service et chaque dégradation des performances. Vous pouvez assurer le suivi de l’état du service sur le [portail Azure](https://portal.azure.com/). Pour plus d’informations, consultez la rubrique [Suivi de l’état du service](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>Contrôle de votre application web
Cette option vous permet de savoir si votre application rencontre des problèmes. Dans le panneau de votre application web, cliquez sur la vignette **Demandes et erreurs** . Le panneau **Mesure** affiche toutes les mesures que vous pouvez ajouter.

Parmi les mesures que vous pouvez surveiller pour votre application web, se trouvent

* Plage de travail moyenne de la mémoire
* Temps de réponse moyen
* Temps processeur
* Plage de travail de la mémoire
* Demandes

![surveiller l’application web pour résoudre les erreurs HTTP de type « 502 Passerelle incorrecte » et « 503 Service indisponible »](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Pour plus d’informations, consultez :

* [Surveiller les applications web dans Microsoft Azure App Service](web-sites-monitor.md)
* [Réception de notifications d'alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Collecter les données
#### <a name="use-the-azure-app-service-support-portal"></a>Utilisation du portail de support Azure App Service
Web Apps vous offre la possibilité de résoudre les problèmes liés à votre application web grâce à des journaux HTTP, les journaux des événements, les vidages de processus et bien plus encore. Vous pouvez accéder à toutes ces informations à l’aide de notre portail de support à l’adresse **http://&lt;your app name>.scm.azurewebsites.net/Support**

Le portail de support Azure App Service vous propose trois onglets distincts pour prendre en charge les trois étapes d’un scénario de dépannage courant :

1. Observer le comportement actuel
2. Analyser en collectant des informations de diagnostic et en exécutant les analyseurs intégrés
3. Résoudre

Si le problème est en cours, cliquez sur **Analyser** > **Diagnostics** > **Diagnostiquer maintenant** pour créer une session de diagnostic, qui recueillera les journaux HTTP, les journaux de l'observateur d'événements, les vidages de mémoire, les journaux d'erreurs PHP et le rapport de traitement PHP.

Une fois les données collectées, une analyse sera également exécutée sur les données pour vous fournir un rapport HTML.

Si vous souhaitez télécharger les données, par défaut, celles-ci sont stockées dans le dossier D:\home\data\DaaS.

Pour plus d'informations sur le portail de support Azure App Service, consultez [Nouvelles mises à jour à l'extension de site de support pour les sites web Azure](https://azure.microsoft.com/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>Utilisation de la console de débogage Kudu
Web Apps est fourni avec une console de débogage que vous pouvez utiliser pour le débogage, l’exploration, le téléchargement de fichiers, ainsi que les points de terminaison JSON pour obtenir des informations relatives à votre environnement. Il s'agit de la *console Kudu* ou du *tableau de bord SCM* pour votre application web.

Vous pouvez accéder à ce tableau de bord avec le lien **https://&lt;Your app name>.scm.azurewebsites.net/**.

Kudu fournit, entre autres, les éléments suivants :

* paramètres d’environnement pour votre application ;
* flux de journal ;
* vidage de diagnostic ;
* console de débogage dans laquelle vous pouvez exécuter les applets de commande Powershell et les commandes DOS de base.

Autre fonctionnalité utile de Kudu, dans le cas où votre application lève des exceptions de première chance, vous pouvez utiliser Kudu et l’outil Procdump de SysInternals pour créer des vidages de mémoire. Ces vidages de mémoire sont des instantanés du processus et peuvent souvent vous aider à résoudre les problèmes plus complexes avec votre application web.

Pour plus d'informations sur les fonctionnalités disponibles dans Kudu, consultez [Outils en ligne de Sites Web Azure que vous devez connaître](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Résoudre le problème
#### <a name="scale-the-web-app"></a>Mise à l’échelle de l’application web
Dans Azure App Service, pour améliorer les performances et le débit, vous pouvez ajuster l’échelle à laquelle vous exécutez votre application. La mise à l’échelle d’une application web implique deux actions associées : l’évolution de votre plan App Service vers un niveau de tarification supérieur et la configuration de certains paramètres après le passage à ce niveau de tarification supérieur.

Pour plus d'informations sur la mise à l'échelle, consultez [Mise à l'échelle d'une application web dans Microsoft Azure App Service](web-sites-scale.md).

En outre, vous pouvez choisir d’exécuter votre application sur plusieurs instances. Non seulement cela vous offre plus de capacité de traitement, mais également un certain niveau de tolérance aux pannes. Si le processus s’arrête sur une instance, l’autre instance continue de servir les requêtes.

Vous pouvez définir la mise à l’échelle pour qu’elle soit manuelle ou automatique.

#### <a name="use-autoheal"></a>Utilisation de la correction automatique (AutoHeal)
La correction automatique (AutoHeal) recycle le processus de travail pour votre application en fonction des paramètres que vous choisissez (comme les modifications de configuration, les requêtes, les limites de mémoire ou le temps nécessaire pour exécuter une requête). La plupart du temps, le recyclage du processus est le moyen le plus rapide pour résoudre un problème. Même si vous pouvez toujours redémarrer l’application web directement dans le portail Azure, la fonctionnalité de correction automatique (AutoHeal) le fera automatiquement pour vous. Il vous suffit d’ajouter des déclencheurs dans le fichier web.config racine pour votre application web. Notez que ces paramètres fonctionnent de la même façon même si votre application n’est pas une application .Net.

Pour plus d'informations, consultez [Correction automatique de Sites Web Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-web-app"></a>Redémarrage de l’application web
Il s’agit souvent du moyen le plus simple de résoudre des problèmes à usage unique. Dans le [portail Azure](https://portal.azure.com/), sur le panneau de votre application web, vous avez la possibilité d’arrêter ou de redémarrer votre application.

 ![redémarrer l’application pour résoudre les erreurs HTTP de type « 502 Passerelle incorrecte » et « 503 Service indisponible »](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Vous pouvez également gérer votre application web à l’aide d’Azure PowerShell. Pour plus d'informations, consultez [Utilisation d'Azure PowerShell avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md).




<!--HONumber=Dec16_HO2-->


