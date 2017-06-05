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
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 29df824062d00e01b786533033097948c008588f
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017

---
# <a name="monitor-app-service"></a>Surveiller App Service
Ce didacticiel vous explique comment surveiller votre application et utiliser les outils de plateforme intégrés pour résoudre les problèmes lorsqu’ils surviennent.

Chaque section de ce document traite d’une fonctionnalité spécifique. En utilisant conjointement les fonctionnalités, vous pouvez :
- Identifier un problème dans votre application.
- Déterminer si le problème vient de votre code ou de la plateforme.
- Réduire la source du problème dans votre code.
- Déboguer et résoudre le problème.

## <a name="before-you-begin"></a>Avant de commencer
- Vous avez besoin d’une application web à surveiller pour suivre les étapes indiquées.
    - Vous pouvez créer une application en suivant les étapes décrites dans le didacticiel [Créer une application ASP.NET dans Azure avec SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md).

- Si vous souhaitez essayer de **déboguer votre application à distance**, vous devez disposer de Visual Studio.
    - Si vous n’avez pas encore installé Visual Studio 2017, vous pouvez télécharger et utiliser la version gratuite [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).
    - Veillez à activer **le développement Azure** lors de l’installation de Visual Studio.

## <a name="metrics"></a> Étape 1 : Afficher les mesures
Les **mesures** sont utiles pour comprendre les concepts suivants :
- Intégrité d’application
- Performances de l’application
- Consommation des ressources

Lorsque vous examinez un problème d’application, il peut être judicieux de commencer par consulter les mesures. Le portail Azure constitue un moyen rapide d’inspecter visuellement les mesures de votre application à l’aide d’**Azure Monitor**.

Les mesures offrent un historique de différentes agrégations clés pour votre application. Pour toutes les applications hébergées dans App Service, vous devez surveiller à la fois l’application web et le plan App Service.

> [!NOTE]
> Un plan App Service représente la collection des ressources physiques utilisées pour héberger vos applications. Toutes les applications affectées à un plan App Service partagent les ressources qu’il définit, ce qui vous permet de réduire les coûts lors de l’hébergement de plusieurs applications.
>
> Les plans App Service définissent :
> * Région : Europe du Nord, États-Unis de l’Est, Sud-Est asiatique, etc.
> * Taille d’instance : Petit, Moyen, Grand, etc.
> * Comptage : une, deux ou trois instances, etc.
> * Référence (SKU) : Gratuit, Partagé, De base, Standard, Premium, etc.

Pour consulter les mesures de votre application web, accédez au panneau **Vue d’ensemble** de l’application que vous souhaitez surveiller. Un graphique des mesures de votre application est disponible sous forme de **vignette de surveillance**. Cliquez sur la vignette pour modifier et configurer les mesures et la plage horaire à afficher.

Par défaut, le panneau de ressources présente un affichage pour les demandes d’application et les erreurs survenues au cours de la dernière heure.
![Application Monitor](media/app-service-web-tutorial-monitoring/app-service-monitor.png)

Comme vous pouvez le voir dans l’exemple, notre application génère plusieurs **erreurs de serveur HTTP**. Le grand nombre d’erreurs est le premier signe que nous devons examiner cette application.

> [!TIP]
> Utilisez les liens suivants pour en savoir plus sur Azure Monitor :
> - [Prise en main d’Azure Monitor](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [Mesures Azure](..\monitoring-and-diagnostics\monitoring-overview-metrics.md)
> - [Mesures prises en charge avec Azure Monitor](..\monitoring-and-diagnostics\monitoring-supported-metrics.md)
> - [Tableaux de bord Azure](..\azure-portal\azure-portal-dashboards.md)

## <a name="alerts"></a> Étape 2 : Configurer des alertes
Les **alertes** peuvent être configurées de manière à se déclencher sur des conditions spécifiques pour votre application.

À l’[Étape 1 : Afficher les mesures](#metrics), nous avons vu que l’application comportait un grand nombre d’erreurs.

Nous allons configurer une alerte pour recevoir une notification automatique lorsqu’une erreur se produit. Dans le cas présent, nous voulons que l’alerte envoie un message électronique chaque fois que le nombre d’erreurs HTTP 50X dépasse un certain seuil.

Pour créer une alerte, accédez à **Surveillance** > **Alertes** et cliquez sur **[+] Ajouter une alerte**.

![Alertes](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts.png)

Indiquez des valeurs pour configurer l’alerte :
- **Ressource :** site à surveiller avec l’alerte.
- **Nom :** nom de votre alerte, ici : *Grand nombre d’erreurs HTTP 50X*.
- **Description :** explication textuelle de ce que l’alerte doit surveiller.
- **Alerte sur :** les alertes peuvent surveiller les mesures ou les événements, ici nous observons les mesures.
- **Mesure :** mesure à surveiller, ici : *Erreurs de serveur HTTP*.
- **Condition :** moment de l’alerte, ici nous allons sélectionner l’option *supérieur à*.
- **Seuil :** valeur à surveiller, ici : *400*.
- **Période :** les alertes agissent au-delà de la valeur moyenne d’une mesure. Les périodes plus courtes génèrent des alertes plus sensibles. Dans notre exemple, nous choisissons *5 minutes*.
- **Envoyer un courrier électronique aux propriétaires et aux contributeurs :** ici : *Activé*.

Maintenant que l’alerte est créée, un courrier électronique est envoyé chaque fois que l’application dépasse le seuil configuré. Les alertes actives peuvent également être consultées dans le portail Azure.

![Alertes déclenchées](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts-triggered.png)


> [!TIP]
> Utilisez les liens suivants pour en savoir plus sur les alertes Azure :
> - [Que sont les alertes dans Microsoft Azure](..\monitoring-and-diagnostics\monitoring-overview-alerts.md)
> - [Effectuer une opération sur les mesures](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [Créer des alertes de mesures](..\monitoring-and-diagnostics\insights-alerts-portal.md)

## <a name="companion"></a> Étape 3 : Compagnon App Service
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

## <a name="diagnose"></a> Étape 4 : Diagnostiquer et résoudre les problèmes
**Diagnostiquer et résoudre les problèmes** permet de distinguer les problèmes d’application des problèmes de plateforme. Cela permet également d’obtenir des options d’atténuation possibles pour rétablir l’état d’intégrité de votre application web.

![Diagnostiquer et résoudre les problèmes](media/app-service-web-tutorial-monitoring/app-service-monitor-diagnosis.png)

Toujours avec l’exemple des étapes précédentes, nous constatons que l’application rencontre des problèmes de disponibilité. En revanche, la disponibilité de la plateforme est restée à 100 %.

Lorsque l’application rencontre un problème et que la plateforme reste saine, cela indique clairement que le problème est lié à l’application.

## <a name="logging"></a> Étape 5 - Journalisation
Maintenant que nous savons que les erreurs sont dues à un problème d’application, nous pouvons examiner les journaux d’application et de serveur pour obtenir plus d’informations.

La journalisation vous permet de collecter à la fois les journaux de **diagnostic d’application** et de **diagnostic de serveur web** concernant votre application web.

### <a name="application-diagnostics"></a>Diagnostics d’application
Le diagnostic d’application vous permet de capturer des suivis générés par l’application pendant son exécution.

L’ajout d’un traçage à votre application améliore considérablement votre capacité à déboguer et à déceler les problèmes.

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
Pour activer la journalisation des applications, accédez à **Surveillance** > **Journaux de diagnostic** et utilisez les touches bascules.

![Surveiller l’application](media/app-service-web-tutorial-monitoring/app-service-monitor-applogs.png)

Les journaux d’application peuvent être stockés dans le système de fichiers de votre application web ou envoyés vers le stockage d’objets blob. Pour les scénarios de production, il est recommandé d’utiliser le stockage d’objets blob.

> [!IMPORTANT]
> L’activation de la journalisation a une incidence sur les performances de votre application et sur l’utilisation des ressources. Pour les scénarios de production, les journaux d’erreurs sont recommandés. Activez une journalisation plus détaillée uniquement en cas de problème.

 ### <a name="web-server-diagnostics"></a>Diagnostics de serveur web
Les journaux des serveurs web sont générés même si votre application n’est pas instrumentée. App Service peut collecter trois types de journaux de serveur :

- **Journalisation du serveur web**
    - Informations sur les transactions HTTP à l’aide du [format de fichier journal étendu W3C](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).
    - Ce rapport se révèle utile pour déterminer les métriques globales d’un site, comme le nombre de demandes traitées ou le nombre de demandes émanant d’une adresse IP spécifique.
- **Messages d’erreurs détaillés**
    - Informations d’erreur détaillées pour les codes d’état HTTP qui indiquent un échec (code d’état 400 ou supérieur).
    - [En savoir plus sur la journalisation d’erreurs détaillée](https://www.iis.net/learn/troubleshoot/diagnosing-http-errors/how-to-use-http-detailed-errors-in-iis)
- **Suivi des demandes ayant échoué**
    - Informations détaillées sur les demandes qui ont échoué, y compris une trace des composants IIS utilisés pour traiter la demande et la durée dans chaque composant.
    - Les journaux des demandes ayant échoué sont utiles lorsque vous essayez d’isoler ce qui provoque une erreur HTTP spécifique.
    - [En savoir plus sur le suivi des demandes ayant échoué](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing/troubleshooting-failed-requests-using-tracing-in-iis)

Pour activer la journalisation de serveur :
- Accédez à **Surveillance** > **Journaux de diagnostic**.
- Activez les différents types de diagnostics de serveur web à l’aide des bascules.

![Surveiller l’application](media/app-service-web-tutorial-monitoring/app-service-monitor-serverlogs.png)

> [!IMPORTANT]
> L’activation de la journalisation a une incidence sur les performances de votre application et sur l’utilisation des ressources. Pour les scénarios de production, les journaux d’erreurs sont recommandés. Activez une journalisation plus détaillée uniquement en cas de problème.

### <a name="accessing-logs"></a>Accès aux journaux
Les journaux enregistrés dans le stockage d’objets blob sont accessibles à l’aide de l’Explorateur de stockage Microsoft Azure. Les journaux stockés dans le système de fichiers de l’application web sont accessibles via FTP aux chemins suivants :

- **Journaux d’application** - `%HOME%/LogFiles/Application/`
    - Ce dossier contient un ou plusieurs fichiers texte contenant des informations générées dans le cadre de la journalisation des applications.
- **Suivi des demandes ayant échoué** - `%HOME%/LogFiles/W3SVC#########/`
    - Ce dossier contient un fichier XSL et un ou plusieurs fichiers XML.
- **Journaux d’erreurs détaillés** - `%HOME%/LogFiles/DetailedErrors/`
    - Ce dossier contient un ou plusieurs fichiers .htm incluant des informations détaillées sur les erreurs HTTP générées par votre application.
- **Journaux des serveurs web** - `%HOME%/LogFiles/http/RawLogs`
    - Ce dossier contient un ou plusieurs fichiers texte au format de fichier journal étendu W3C.

## <a name="streaming"></a> Étape 6 - Diffusion de journaux
Lorsque vous déboguez une application, la diffusion des journaux est pratique car elle s’avère plus rapide que l’[accès aux journaux](#Accessing-Logs) via FTP.

App Service peut diffuser les **journaux d’application** et les **journaux de serveur web** lorsqu’ils sont générés.

> [!TIP]
> Avant d’essayer de diffuser les journaux, assurez-vous que vous avez activé la collecte des journaux comme décrit dans la section [Journalisation](#logging).

Pour diffuser les journaux, accédez à **Surveillance**> **Flux de journaux**. Sélectionnez **Journaux d’application** ou **Journaux des serveurs web** en fonction des informations que vous recherchez. Ici, vous pouvez également interrompre, redémarrer et effacer la mémoire tampon.

![Journaux en continu](media/app-service-web-tutorial-monitoring/app-service-monitor-logstream.png)

> [!TIP]
> Les journaux sont générés uniquement lorsqu’il existe du trafic sur l’application. Vous pouvez également augmenter le niveau de détail des journaux pour obtenir plus d’événements ou d’informations.

## <a name="remote"></a> Étape 7 - Débogage à distance
Une fois la source des problèmes d’application identifiée, utilisez le **débogage à distance** pour examiner le code.

Le débogage à distance vous permet d’attacher un débogueur à votre application web s’exécutant dans le cloud. Vous pouvez définir des points d’arrêt, manipuler directement la mémoire, parcourir le code en détail et même modifier le chemin d’accès du code, comme vous le feriez si l’application s’exécutait en local.

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
> Nous vous déconseillons d'exécuter le mode débogage en production. Si votre application de production n’est pas montée en charge sur plusieurs instances de serveur, la fonction de débogage empêche le serveur web de répondre aux autres demandes. Pour résoudre les problèmes de production, la meilleure solution est de [configurer la journalisation](#logging) et [Application Insights](#insights).



## <a name="explorer"></a> Étape 8 : Process Explorer
Lorsque votre application comporte plusieurs instances, **Process Explorer** peut vous aider à identifier les problèmes propres à une instance.

Utilisez **Process Explorer** pour :

- énumérer tous les processus entre différentes instances de votre plan App Service ;
- extraire et afficher les descripteurs et modules associés à chaque processus ;
- afficher le nombre de processeurs, de plages de travail et de threads pour vous aider à identifier les boucles infinies ;
- rechercher des descripteurs de fichiers ouverts, voire arrêter une instance de processus spécifique.

Process Explorer se trouve sous **Surveillance** > **Process Explorer**.

![Process Explorer](media/app-service-web-tutorial-monitoring/app-service-monitor-processexplorer.png)


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

