---
title: Surveiller une application web | Microsoft Docs
description: "Découvrez comment configurer la surveillance de votre application web"
services: App-Service
keywords: 
author: btardif
ms.author: byvinyal
ms.date: 04/04/2017
ms.topic: article
ms.service: app-service-web
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 63bfc6922de224f56186003991f4a51d8f99ed35
ms.lasthandoff: 04/15/2017

---
# <a name="monitor-app-service"></a>Surveiller App Service
Ce didacticiel vous explique comment utiliser les outils de plateforme intégrés pour surveiller et diagnostiquer votre application hébergée dans App Service. 

## <a name="in-this-tutorial"></a>Dans ce didacticiel

1. [Process Explorer](#explorer)
    - Obtenez des informations détaillées sur votre application s’exécutant sur plusieurs instances de votre plan App Service.
1. [Mesures App Service](#metrics) 
   - Découvrez comment surveiller votre application en utilisant les graphiques intégrés.
   - Configurez des graphiques en fonction de vos besoins.
   - Créez un tableau de bord personnalisé en épinglant vos graphiques personnalisés.
1. [Configurer des alertes](#alerts)
    - Découvrez comment configurer des alertes pour votre application et pour votre plan App Service.
1. [Compagnon App Service](#Companion)
    - Surveillez et dépannez votre application à l’aide d’un appareil mobile.
1. [Configuration de la journalisation](#logging)
    - Découvrez comment collecter les journaux de serveur et d’application.
    - Découvrez les différents emplacements de stockage de vos journaux et comment y accéder.
1. [Diffusion de journaux](#streaming)
    - Utilisez les journaux de diffusion en continu pour consulter les journaux wc3 et les journaux de votre application dès leur émission.
1. [Débogage à distance](#remote)
    - Utilisez Visual Studio pour déboguer à distance votre projet s’exécutant sur App Service.
1. [Diagnostiquer et résoudre les problèmes](#diagnose)
    - Identifiez les problèmes liés à votre application et découvrez comment les résoudre.
1. [Application Insights](#insights)
    - Profilage et surveillance avancés de votre application

## <a name="before-you-begin"></a>Avant de commencer

- Vous avez besoin d’une application web à surveiller pour suivre les étapes indiquées. 
    - Vous pouvez créer une application en suivant les étapes décrites dans le didacticiel [Créer une application ASP.NET dans Azure avec SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md).

- Si vous souhaitez essayer de **déboguer votre application à distance**, vous devez disposer de Visual Studio. 
    - Si vous n’avez pas encore installé Visual Studio 2017, vous pouvez télécharger et utiliser la version gratuite [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). 
    - Veillez à activer **le développement Azure** lors de l’installation de Visual Studio.

## <a name="explorer"></a> Étape 1 - Process Explorer

Process Explorer est un outil qui vous permet d’obtenir des informations détaillées sur le fonctionnement interne de votre plan App Service.

Utilisez **Process Explorer** pour :

- énumérer tous les processus entre différentes instances de votre plan App Service ;
- extraire et afficher les descripteurs et modules associés à chaque processus ; 
- afficher le nombre de processeurs, de plages de travail et de threads pour vous aider à identifier les boucles infinies ;
- rechercher des descripteurs de fichiers ouverts, voire arrêter une instance de processus spécifique.

Process Explorer se trouve sous **Surveillance** > **Process Explorer**.

![Process Explorer](media/app-service-web-tutorial-monitoring/app-service-monitor-processexplorer.png)

## <a name="metrics"></a> Étape 2 - Afficher les mesures App Service
Les **mesures** fournissent des informations détaillées sur votre application web et ses interactions avec les utilisateurs et la plateforme.

Vous pouvez utiliser les mesures pour mieux appréhender :
- le nombre de ressources utilisées par votre application,
- le volume de trafic pour votre application,
- le nombre total de requêtes/d’échecs,
- le volume de données entrantes et sortantes.

Pour toutes les applications hébergées dans App Service, vous devez surveiller l’application web et le plan App Service.

- Les mesures d’**application** fournissent des informations sur les requêtes http, les échecs et le temps de réponse moyen.
- Les mesures du **plan App Service** fournissent des informations sur l’utilisation des ressources.

Le portail Azure constitue un moyen rapide d’inspecter visuellement les mesures de votre application à l’aide d’**Azure Monitor**.

- Accédez au panneau **Vue d’ensemble** de l’application que vous souhaitez surveiller.

![Surveiller l’application](media/app-service-web-tutorial-monitoring/app-service-monitor.png)

- Vous pouvez afficher les mesures de l’application sous forme de **vignette Surveillance**.
- Cliquez sur la vignette pour modifier et configurer les mesures et la plage horaire à afficher.

![Configurer les graphiques](media/app-service-web-tutorial-monitoring/app-service-monitor-configure.png)

- Vous pouvez épingler des graphiques personnalisés au tableau de bord pour y accéder facilement et vous y reporter rapidement.

![Épingler un graphique](media/app-service-web-tutorial-monitoring/app-service-monitor-pin.png)

> [!TIP]
> Utilisez les liens suivants pour en savoir plus sur Azure Monitor :
> - [Prise en main d’Azure Monitor](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [Mesures Azure](..\monitoring-and-diagnostics\monitoring-overview-metrics.md)
> - [Mesures prises en charge avec Azure Monitor](..\monitoring-and-diagnostics\monitoring-supported-metrics.md#microsoftwebsites-including-functions)
> - [Tableaux de bord du portail Azure](..\azure-portal\azure-portal-dashboards.md)

## <a name="alerts"></a> Étape 3 - Configurer les alertes

Les **alertes** vous permettent d’automatiser la surveillance de votre application.

Utilisez les alertes pour être averti lorsque des situations intéressantes qui affectent votre application sont détectées.

Pour créer une alerte :
- Accédez au panneau **Vue d’ensemble** de l’application que vous souhaitez surveiller.
- Dans le menu, accédez à **Surveillance** > **Alertes**.
- Sélectionnez **[+] Ajouter une alerte**.
- Configurez l’alerte en fonction de vos besoins.

![Alertes](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts.png)

> [!TIP]
> Utilisez les liens suivants pour en savoir plus sur les alertes Azure :
> - [Que sont les alertes dans Microsoft Azure](..\monitoring-and-diagnostics\monitoring-overview-alerts.md)
> - [Effectuer une opération sur les mesures](..\monitoring-and-diagnostics\monitoring-overview.md)

## <a name="companion"></a> Étape 4 - Compagnon App Service
Le **compagnon App Service** s’avère pratique pour surveiller votre application avec une expérience native sur votre appareil mobile (iOS ou Android).

Utilisez le compagnon App Service pour :
- consulter les mesures d’application,
- consulter et agir sur les alertes d’application et les recommandations,
- procéder à un dépannage basique (parcourir, démarrer, arrêter ou redémarrer l’application).
- Obtenez des notifications push pour les événements critiques.

![Compagnon App Service](media/app-service-web-tutorial-monitoring/app-service-companion.png)

[![Compagnon App Service - App Store](media/app-service-web-tutorial-monitoring/app-service-companion-appStore.png)](https://itunes.apple.com/app/azure-app-service-companion/id1146659260)
[![Compagnon App Service - Google Play](media/app-service-web-tutorial-monitoring/app-service-companion-googlePlay.png)](https://play.google.com/store/apps/details?id=azureApps.AzureApps)

Vous pouvez installer le compagnon App Service à partir de l’[App Store](https://itunes.apple.com/app/azure-app-service-companion/id1146659260) ou de [Google Play](https://play.google.com/store/apps/details?id=azureApps.AzureApps).

## <a name="logging"></a> Étape 5 - Journalisation
La journalisation vous permet de collecter à la fois les journaux de **diagnostic d’application** et de **diagnostic de serveur web** concernant votre application web.

Utilisez les journaux de diagnostic pour comprendre le comportement de votre application, résoudre les problèmes liés à l’application et comprendre dans quels cas des échecs sont survenus.

### <a name="application-diagnostics"></a>Diagnostics d’application
Le diagnostic d’application vous permet de capturer des suivis générés par l’application pendant son exécution. 

Pour activer la journalisation de l’application :

- Accédez à **Surveillance** > **Journaux de diagnostic**. 
- Activez la journalisation des applications à l’aide des bascules.

Les journaux d’application peuvent être stockés dans le système de fichiers de votre application web ou envoyés vers le stockage d’objets blob.

> [!TIP]
> Pour les scénarios de production, il est recommandé d’utiliser le stockage d’objets blob.

![Surveiller l’application](media/app-service-web-tutorial-monitoring/app-service-monitor-applogs.png)

Dans ASP.NET, vous pouvez enregistrer les suivis d’application à l’aide de la [classe System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx), afin de générer des événements qui sont capturés par l’infrastructure de journalisation. Vous pouvez également indiquer le niveau de gravité du suivi pour un filtrage plus facile.

```csharp
public ActionResult Delete(Guid? id)
{
    System.Diagnostics.Trace.TraceInformation("GET /Todos/Delete/" + id);
    if (id == null)
    {
        System.Diagnostics.Trace.TraceError("/Todos/Delete/ failed, ID is null");
        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
    }
    Todo todo = db.Todoes.Find(id);
    if (todo == null)
    {
        System.Diagnostics.Trace.TraceWarning("/Todos/Delete/ failed, ID: " + id + " could not be found");
        return HttpNotFound();
    }
    System.Diagnostics.Trace.TraceInformation("GET /Todos/Delete/" + id + "completed successfully");
    return View(todo);
}
```

> [!IMPORTANT]
> L’activation de la journalisation a une incidence sur les performances de votre application et sur l’utilisation des ressources. Pour les scénarios de production, les journaux d’erreurs sont recommandés. Activez une journalisation plus détaillée uniquement en cas de problème.

 ### <a name="web-server-diagnostics"></a>Diagnostics de serveur web
App Service peut collecter trois types de journaux de serveur :

- **Journalisation du serveur web** 
    - Informations sur les transactions HTTP à l’aide du [format de fichier journal étendu W3C](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). 
    - Ce rapport se révèle utile pour déterminer les métriques globales d’un site, comme le nombre de demandes traitées ou le nombre de demandes émanant d’une adresse IP spécifique.
- **Messages d’erreurs détaillés** 
    - Informations d’erreur détaillées pour les codes d’état HTTP qui indiquent un échec (code d’état 400 ou supérieur). 
- **Suivi des demandes ayant échoué** 
    - Informations détaillées sur les demandes qui ont échoué, y compris une trace des composants IIS utilisés pour traiter la demande et la durée dans chaque composant. 
    - Les journaux des demandes ayant échoué sont utiles lorsque vous essayez d’isoler ce qui provoque une erreur HTTP spécifique.

Pour activer la journalisation de serveur :
- Accédez à **Surveillance** > **Journaux de diagnostic**. 
- Activez les différents types de diagnostics de serveur web à l’aide des bascules.

![Surveiller l’application](media/app-service-web-tutorial-monitoring/app-service-monitor-serverlogs.png)

> [!IMPORTANT]
> L’activation de la journalisation a une incidence sur les performances de votre application et sur l’utilisation des ressources. Pour les scénarios de production, les journaux d’erreurs sont recommandés. Activez une journalisation plus détaillée uniquement en cas de problème.

### <a name="accessing-logs"></a>Accès aux journaux
Les journaux enregistrés dans le stockage d’objets blob sont accessibles à l’aide de l’Explorateur de stockage Microsoft Azure.

Les journaux stockés dans le système de fichiers de l’application web sont accessibles via FTP aux chemins suivants :

- **Journaux d'application** : /LogFiles/Application/. 
    - Ce dossier contient un ou plusieurs fichiers texte contenant des informations générées dans le cadre de la journalisation des applications.
- **Suivi des demandes ayant échoué** : /LogFiles/W3SVC#########/. 
    - Ce dossier contient un fichier XSL et un ou plusieurs fichiers XML. 
- **Journaux d'erreurs détaillés** : /LogFiles/DetailedErrors/. 
    - Ce dossier contient un ou plusieurs fichiers .htm incluant des informations détaillées sur les erreurs HTTP générées par votre application.
- **Journaux des serveurs Web** : /LogFiles/http/RawLogs. 
    - Ce dossier contient un ou plusieurs fichiers texte au format de fichier journal étendu W3C.

## <a name="streaming"></a> Étape 6 - Diffusion de journaux
App Service peut diffuser les **journaux d’application** et les **journaux de serveur web** lorsqu’ils sont générés. 

Lorsque vous déboguez une application, la diffusion des journaux est pratique car elle s’avère plus rapide que l’accès aux journaux via d’autres méthodes FTP.

> [!TIP]
> Avant d’essayer de diffuser les journaux, assurez-vous que vous avez activé la collecte des journaux comme décrit dans la section [Journalisation](#logging).

Pour diffuser les journaux :
- Accédez à **Surveillance**> **Diffusion de journaux**.
- Sélectionnez **Journaux d’application** ou **Journaux des serveurs web** en fonction des informations que vous recherchez.
- Ici, vous pouvez également interrompre, redémarrer et effacer la mémoire tampon.

![Journaux en continu](media/app-service-web-tutorial-monitoring/app-service-monitor-logstream.png)

> [!TIP]
> Les journaux sont générés uniquement lorsqu’il existe du trafic sur l’application. Vous pouvez également augmenter le niveau de détail des journaux pour obtenir plus d’événements ou d’informations.

## <a name="remote"></a> Étape 7 - Débogage à distance
Le **débogage à distance** vous permet d’attacher un débogueur à votre application web s’exécutant dans le cloud. Vous pouvez définir des points d’arrêt, manipuler directement la mémoire, parcourir le code en détail et même modifier le chemin d’accès du code, comme vous le feriez si l’application s’exécutait en local.

Utilisez le débogage à distance et les journaux de diagnostic pour détecter et corriger les problèmes liés à votre application.

Pour attacher le débogueur à votre application s’exécutant dans le cloud :

- Avec Visual Studio 2017, ouvrez la solution correspondant à l’application à déboguer. 
- Définissez des points d’arrêt comme vous le feriez pour un développement en local.
- Ouvrez **l’explorateur de cloud** (ctr + /, ctrl + x).
- Connectez-vous avec vos informations d’identification Azure.
- Recherchez l’application à déboguer.
- Sélectionnez le formulaire **Attacher le débogueur** dans le volet **Actions**.

![Débogage à distance](media/app-service-web-tutorial-monitoring/app-service-monitor-vsdebug.png)

Visual Studio configure votre application pour le débogage à distance et ouvre une fenêtre de navigateur qui accède à votre application. Parcourez votre application pour déclencher des points d’arrêt et parcourir le code.

> [!WARNING]
> Nous vous déconseillons d'exécuter le mode débogage en production. Si votre application de production n’est pas montée en charge sur plusieurs instances de serveur, la fonction de débogage empêche le serveur web de répondre aux autres demandes. Pour résoudre les problèmes de production, la meilleure solution est de [configurer la journalisation](#logging) et [Application Insights](#insights)

## <a name="diagnose"></a> Étape 8 - Diagnostiquer et résoudre les problèmes
**Diagnostiquer et résoudre les problèmes** est un outil intégré qui analyse les dernières 24 heures d’activité de votre application web. L’interface utilisateur présente un récapitulatif de tous les problèmes identifiés.

Utilisez cette fonctionnalité pour vous aider à distinguer les problèmes liés à l’application des problèmes de plateforme et trouver des solutions possibles pour que votre application web soit de nouveau saine.

![Diagnostiquer et résoudre les problèmes](media/app-service-web-tutorial-monitoring/app-service-monitor-diagnosis.png)

## <a name="insights"></a> Étape 9 - Application Insights
**Application Insights** fournit des fonctionnalités avancées de profilage et de surveillance de votre application. 

Utilisez Application Insights pour détecter et diagnostiquer les exceptions et les problèmes de performances dans votre application web.

Vous pouvez activer Application Insights pour votre application web sous **Surveillance** > **Application Insights**. 

> [!NOTE]
> Il se peut qu’Application Insights vous demande d’installer l’extension de site Application Insights avant de commencer à collecter des données. L’installation de l’extension de site provoque un redémarrage de l’application.

![Application Insights](media/app-service-web-tutorial-monitoring/app-service-monitor-appinsights.png)

Application Insights offre de nombreuses fonctionnalités. Pour en savoir plus, consultez les liens de la section [Étapes suivantes](#next).

## <a name="next"></a> Étapes suivantes

 - [Présentation d’Application Insights](..\application-insights\app-insights-overview.md)
 - [Surveiller les performances de l’application web Azure avec Application Insights](..\application-insights\app-insights-azure-web-apps.md)
 - [Analyse de la disponibilité et de la réactivité d’un site web avec Application Insights](..\application-insights\app-insights-monitor-web-app-availability.md)
