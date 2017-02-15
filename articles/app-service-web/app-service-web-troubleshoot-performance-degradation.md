---
title: "Baisse de performances d’une application web dans App Service | Microsoft Docs"
description: "Cet article vous aide à résoudre les problèmes de baisse de performances d’une application web dans Azure App Service."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
tags: top-support-issue
keywords: performances d&quot;application web, application lente
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 5ea043ce3bcd0f500fd765f13764ea3ee83e1ba9
ms.openlocfilehash: 83c3592014c73c0cf36d371d2752bc76b7c8a4e8


---
# <a name="troubleshoot-slow-web-app-performance-issues-in-azure-app-service"></a>Résoudre les problèmes de baisse de performances d’une application web dans Azure App Service
Cet article vous aide à résoudre les problèmes de baisse de performances d’une application web dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

Si vous avez besoin d'aide supplémentaire concernant n'importe quel point de cet article, contactez les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site de support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.

## <a name="symptom"></a>Symptôme
Lorsque vous parcourez l’application web, le chargement des pages est lent et il est possible que le délai soit dépassé.

## <a name="cause"></a>Cause :
Ce problème est souvent dû à des problèmes au niveau de l’application, tels que :

* demandes exigeant beaucoup de temps ;
* un taux d’utilisation élevé de la mémoire/UC par l’application ;
* un blocage de l’application en raison d’une exception.

## <a name="troubleshooting-steps"></a>Étapes de dépannage
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
* Requêtes

![surveiller les performances d’une application web](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Pour plus d'informations, consultez les pages suivantes :

* [Surveiller les applications web dans Microsoft Azure App Service](web-sites-monitor.md)
* [Réception de notifications d'alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Surveillance de l'état d'un point de terminaison Web
Si vous exécutez votre application web sur le niveau de tarification **Standard** , les applications web vous permettent de surveiller les points de 2 terminaisons issus de 3 régions géographiques.

Elle paramètre des tests Web à partir de différents emplacements où sont évalués les temps de réponse et de disponibilité des URL. Ce type de test lance une requête HTTP get sur l’URL Web pour déterminer les temps de réponse et de disponibilité depuis chaque emplacement. Chaque emplacement configuré exécute un test toutes les cinq minutes.

La disponibilité est contrôlée à l'aide des codes réponse HTTP et le temps de réponse est mesuré en millisecondes. Un test de surveillance échoue si le code de réponse HTTP est supérieur ou égal à 400 ou si la réponse prend plus de 30 secondes. Un point de terminaison est considéré comme disponible s'il passe avec succès les différents tests de surveillance à partir de tous les emplacements spécifiés.

Pour le configurer, voir [Surveillance des applications dans Azure App Service](web-sites-monitor.md).

Consultez également [Assurer la gestion des sites Web Azure et la surveillance des points de terminaison, avec Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) pour obtenir une vidéo décrivant la surveillance d’un point de terminaison.

#### <a name="application-performance-monitoring-using-extensions"></a>Analyse des performances des applications à l’aide d’Extensions
Vous pouvez également surveiller les performances de votre application en mettant à profit les *extensions de site*.

Chaque application web d’App Service fournit un point de terminaison de gestion extensible qui vous permet de tirer parti d’un ensemble performant d’outils déployés en tant qu’extensions de site. Ces outils vont d’éditeurs de code source comme [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx) à des outils de gestion de ressources connectées telles qu’une base de données MySQL connectée à une application web.

[Azure Application Insights](/services/application-insights/) et [New Relic](/marketplace/partners/newrelic/newrelic/) sont tous les deux des extensions de site de surveillance des performances disponibles. Pour utiliser New Relic, vous installez un agent lors de l'exécution. Pour utiliser Azure Application Insights, vous devez régénérer votre code avec un kit de développement logiciel (SDK). Vous avez également la possibilité d’installer une extension qui fournit l’accès aux données supplémentaires. Le Kit de développement logiciel (SDK) vous permet d'écrire un code pour surveiller plus en détail l'utilisation et les performances de votre application.

Pour utiliser Application Insights, consultez [Surveiller les performances dans les applications web](../application-insights/app-insights-web-monitor-performance.md).

Pour utiliser New Relic, consultez la page [Gestion des performances des applications New Relic sur Azure](../store-new-relic-cloud-services-dotnet-application-performance-management.md).

<a name="collect" />

### <a name="2-collect-data"></a>2. Collecter les données
#### <a name="enable-diagnostics-logging-for-your-web-app"></a>Activez la journalisation des diagnostics pour votre application web
L’environnement Web Apps fournit des fonctionnalités de diagnostic pour les informations de journalisation provenant du serveur Web et de l’application web. Ces informations sont réparties, en toute logique, en diagnostics de serveur web et en diagnostics d’application.

##### <a name="web-server-diagnostics"></a>Diagnostics de serveur web
Vous pouvez activer ou désactiver les types de journaux suivants :

* **Messages d’erreur détaillés** : informations d’erreur détaillées pour les codes d’état HTTP qui indiquent un échec (code d’état 400 ou supérieur). Il peut s'agir d'informations qui vous aident à déterminer la raison pour laquelle le serveur a renvoyé le code d'erreur.
* **Suivi des demandes ayant échoué** : informations détaillées sur les demandes qui ont échoué, y compris une trace des composants IIS utilisés pour traiter la demande et la durée dans chaque composant. Cela peut se révéler utile si vous essayez d’améliorer le fonctionnement des performances d’application web ou d’isoler la cause d’une erreur HTTP spécifique.
* **Journalisation du serveur Web** : informations sur les transactions HTTP à l’aide du format de fichier journal étendu W3C. Ce rapport se révèle utile pour déterminer les mesures globales d’une application web, comme le nombre de demandes traitées ou le nombre de demandes émanant d’une adresse IP spécifique.

##### <a name="application-diagnostics"></a>Diagnostic d'application
Le diagnostic d’application vous permet de recueillir des informations générées par une application web. Les applications ASP.NET peuvent utiliser la classe `System.Diagnostics.Trace` pour enregistrer des informations dans le journal de diagnostic d’application.

Pour obtenir des instructions détaillées sur la façon de configurer votre application de journalisation, consultez [Activer la journalisation des diagnostics pour les applications web dans le Service d’application Azure](web-sites-enable-diagnostic-log.md).

#### <a name="use-remote-profiling"></a>Utiliser le profilage distant
Dans Azure Service App, Web Apps, API Apps et WebJobs peuvent être profilés à distance. Si votre processus s’exécute plus lentement que prévu, ou si la latence des demandes HTTP est supérieure à la normale et le taux d’utilisation de l’UC du processus élevé, vous pouvez analyser à distance le profil de vos processus et faire en sorte que les piles d’appel d’exemple analysent l’activité du processus et les chemins actifs de code.

Pour plus d’informations, consultez [Prise en charge d’analyse de profil distant dans Azure App Service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

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

Pour plus d’informations sur les fonctionnalités disponibles dans Kudu, consultez [Outils Team Services de Sites Web Azure que vous devez connaître](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

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

 ![redémarrer une application web pour résoudre les problèmes de performances](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Vous pouvez également gérer votre application web à l’aide d’Azure PowerShell. Pour plus d'informations, consultez [Utilisation d'Azure PowerShell avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md).



<!--HONumber=Feb17_HO2-->


