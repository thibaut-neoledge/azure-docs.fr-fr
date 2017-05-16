---
title: Profilage des applications web dynamiques sur Azure avec Application Insights | Microsoft Docs
description: "Identifiez le chemin réactif dans le code de votre serveur web avec un profileur de faible encombrement."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: awills
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 13a2883c59092c964cf3c353e767839c5f9ef788
ms.contentlocale: fr-fr
ms.lasthandoff: 04/27/2017


---
# <a name="profiling-live-azure-web-apps-with-application-insights-preview"></a>Profilage des applications web dynamiques sur Azure avec Application Insights (version préliminaire)

*Cette fonction d’Application Insights est en version préliminaire.*

Découvrez combien de temps vous consacrez à chaque méthode dans votre application web dynamique à l’aide de l’outil de profilage [d’Azure Application Insights](app-insights-overview.md). Cet outil vous montre les profils détaillés des requêtes dynamiques qui ont été traitées par votre application et met en évidence le « chemin réactif » qui est le plus souvent utilisé. Il sélectionne automatiquement des exemples associés à des temps de réponse différents. Le profileur utilise diverses techniques pour réduire la charge. 

Le profileur fonctionne actuellement pour les applications web ASP.NET s’exécutant sur Azure App Services, au moins au niveau tarifaire de base. (Si vous utilisez ASP.NET Core, le framework cible doit être `.NetCoreApp`.)

<a id="installation"></a>
## <a name="enable-the-profiler"></a>Activer le profileur

[Installez Application Insights](app-insights-asp-net.md) dans votre code. S’il est déjà installé, assurez-vous que vous disposez de la version la plus récente. (Pour ce faire, cliquez avec le bouton droit sur votre projet dans l'Explorateur de solutions, puis sélectionnez Gérer les packages NuGet. Sélectionnez Mises à jour et mettez à jour tous les packages.) Redéployez votre application.

*Vous utilisez ASP.NET Core ? [Cliquez ici](#aspnetcore).*

Dans [https://portal.azure.com](https://portal.azure.com), ouvrez la ressource Application Insights correspondant à votre application web. Ouvrez **Performances** et cliquez sur **Configurer**. Sélectionnez votre application et suivez l’Assistant.

![Dans le panneau Performances, cliquez sur Configurer.][performance-blade]

* *Vous ne voyez pas le bouton Configurer ? Utilisez la [procédure manuelle](#manual-installation).*

Si vous avez besoin d’arrêter ou redémarrer le profileur, vous devez vous rendre **dans la ressource App Service**, sous **Tâches Web**. Pour le supprimer, allez dans **Extensions**.

Si vous utilisez WebDeploy pour déployer des modifications sur votre application web, veillez à ne pas supprimer le dossier **App_Data** lors du déploiement. Dans le cas contraire, les fichiers de l’extension du profileur seront supprimés lorsque vous déploierez ensuite l’application web dans Azure.

**[Mise à jour]**  L’extension de site Application Insights a intégré l’agent profileur de la version 2.3. Elle remplace l’extension de site Application Insights Profiler initiale. Vous pouvez migrer vers la version la plus récente via l’Assistant **Configuration**.

## <a name="viewing-profiler-data"></a>Affichage des données du profileur

Ouvrez le panneau Performances et faites défiler jusqu’à la liste des opérations.




![Colonne Exemples du panneau Performances d’Application Insights][performance-blade-examples]

Le tableau contient les colonnes suivantes :

* **Nombre** : le nombre de ces requêtes dans l’intervalle de temps du panneau.
* **Médiane** : délai standard de votre application pour répondre à une requête. La moitié de toutes les réponses étaient plus rapides que cela.
* **95e centile** : 95 % des réponses étaient plus rapides que cela. Si ce chiffre est très différent de la valeur médiane, il existe peut-être un problème intermittent au niveau de votre application. (Cette différence peut aussi s’expliquer par une fonctionnalité de conception, telle que la mise en cache.)
* **Exemples** : une icône indique que le profileur a capturé des arborescences d’appels de procédure pour cette opération.

Cliquez sur l’icône Exemples pour ouvrir l’Explorateur de trace. L’Explorateur affiche plusieurs exemples que le profileur a capturés, classés par temps de réponse.

Sélectionnez un exemple pour afficher, au niveau du code, une répartition du temps d’exécution de la requête.

![Explorateur de trace d’Application Insights][trace-explorer]

**Afficher le chemin réactif** : ouvre le plus grand nœud feuille, ou du moins un élément qui s’en rapproche. Dans la plupart des cas, ce nœud est adjacent à un goulot d’étranglement.



* **Étiquette** : nom de la fonction ou de l’événement. L’arborescence affiche une combinaison des codes et des événements qui se sont produits (par exemple, des événements SQL et http). L’événement supérieur représente la durée globale de la requête.
* **Métrique** : le temps écoulé.
* **Quand** : indique à quel moment la fonction/l’événement était en cours d’exécution par rapport à d’autres fonctions. 

## <a name="how-to-read-performance-data"></a>Comment lire les données de performances

Le profileur de service Microsoft utilise à la fois une méthode d’échantillonnage et une instrumentation pour analyser les performances de votre application. Lorsque la collecte détaillée est en cours, le profileur de service échantillonne le pointeur d’instruction de chaque processeur de l’ordinateur à chaque milliseconde. Chaque exemple capture la pile des appels complète du thread en cours d’exécution, en donnant des informations utiles et détaillées sur l’activité du thread à des niveaux d’abstraction faibles et élevés. Le profileur de service collecte également d’autres événements tels que les événements de changement de contexte, les événements TPL et les événements de pool de threads afin de suivre la corrélation et la causalité des activités. 

La pile des appels présentée dans l’affichage chronologique est le résultat de l’échantillonnage et de l’instrumentation décrits ci-dessus. Étant donné que chaque échantillon capture la pile des appels complète du thread, il inclut le code du framework .NET, ainsi que les autres infrastructures que vous référencez.

### <a id="jitnewobj"></a>Allocation d’objets (`clr!JIT\_New or clr!JIT\_Newarr1`)
Les fonctions d’assistance `clr!JIT\_New and clr!JIT\_Newarr1` intégrées au framework .NET allouent la mémoire à partir du segment de mémoire géré. `clr!JIT\_New` est appelé lorsqu’un objet est alloué. `clr!JIT\_Newarr1` est appelé lorsqu’un tableau d’objets est alloué. Ces deux fonctions sont généralement très rapides et s’exécutent en relativement peu de temps. Si vous constatez que `clr!JIT\_New` ou `clr!JIT\_Newarr1` prend un certain temps dans votre scénario, cela indique que le code alloue peut-être de nombreux objets et qu’il consomme une quantité importante de mémoire. 

### <a id="theprestub"></a>Code de chargement (`clr!ThePreStub`)
`clr!ThePreStub` est une fonction d’assistance intégrée au framework .NET qui prépare le code en vue de sa première exécution. Elle implique en général au moins une compilation JIT (juste à temps). Pour chaque méthode C#, `clr!ThePreStub` doit être appelé au maximum une fois pendant la durée de vie d’un processus.

Si vous constatez que `clr!ThePreStub` prend beaucoup de temps pour le traitement d’une requête, cela indique que cette requête est la première à exécuter cette méthode, et que le runtime du framework .NET prend beaucoup de temps pour charger cette méthode. Vous pouvez envisager un processus de mise en route qui exécute cette partie du code avant que vos utilisateurs y accèdent, ou encore envisager d’exécuter NGen sur vos assemblys. 

### <a id="lockcontention"></a>Contention de verrouillage (`clr!JITutil\_MonContention` ou `clr!JITutil\_MonEnterWorker`)
`clr!JITutil\_MonContention` ou `clr!JITutil\_MonEnterWorker` indique que le thread actuel attend qu’un verrou soit libéré. Cela se produit généralement lors de l’exécution d’une instruction lock C#, lors de l’appel de méthode Monitor.Enter, ou encore lors de l’appel d’une méthode avec l’attribut MethodImplOptions.Synchronized. La contention de verrouillage se produit généralement lorsque le thread A acquiert un verrou et que le thread B tente d’acquérir le même verrou avant qu’il ne soit libéré par le thread A. 

### <a id="ngencold"></a>Code de chargement (`[COLD]`)
Si le nom de la méthode contient `[COLD]`, par exemple `mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined`, cela signifie que le runtime du framework .NET exécute pour la première fois du code qui n’utilise pas une <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">optimisation guidée par profil</a>. Pour chaque méthode, il doit s’afficher au maximum une fois pendant la durée de vie du processus. 

Si le code de chargement de code prend beaucoup de temps pour traiter une requête, cela indique que cette requête est la première à exécuter la partie non optimisée de la méthode. Vous pouvez envisager un processus de mise en route qui exécute cette partie du code avant que vos utilisateurs y accèdent. 

### <a id="httpclientsend"></a>Envoyer une requête HTTP
Les méthodes telles que `HttpClient.Send` indiquent que le code attend l’exécution d’une requête HTTP.

### <a id="sqlcommand"></a>Opération de base de données
Une méthode telle que SqlCommand.Execute indique que le code attend l’exécution d’une opération de base de données.

### <a id="await"></a>En attente (`AWAIT\_TIME`)
`AWAIT\_TIME` indique que le code attend la fin de l’exécution d’une autre tâche. Cela se produit généralement avec l’instruction C# « await ». Lorsque le code exécute une instruction C# « await », le thread se déroule et redonne le contrôle au pool de threads : aucun thread n’est bloqué en attendant la fin de l’exécution de l’instruction « await ». Toutefois, le thread qui a exécuté l’instruction « await » est logiquement « bloqué » en attendant que l’opération se termine. `AWAIT\_TIME` indique le temps de blocage dans l’attente d’exécution de la tâche.

### <a id="block"></a>Temps de blocage
`BLOCKED_TIME` indique que le code attend qu’une autre ressource soit disponible ; c’est le cas par exemple d’une attente d’un objet de synchronisation, d’une attente de disponibilité d’un thread ou d’une attente de fin d’exécution d’une requête. 

### <a id="cpu"></a>Temps processeur
Le processeur est occupé à exécuter les instructions.

### <a id="disk"></a>Temps du disque
L’application effectue les opérations de disque.

### <a id="network"></a>Temps réseau
L’application effectue les opérations réseau.

### <a id="when"></a>Colonne « When » (Quand)
Cette colonne permet de voir comment les exemples INCLUSIFS collectés pour un nœud varient au fil du temps. La plage totale de la requête est divisée en 32 compartiments de temps et les exemples inclusifs pour ce nœud sont accumulés dans ces 32 compartiments. Chaque compartiment est ensuite représenté par une barre dont la hauteur reflète une valeur à l’échelle. Pour les nœuds marqués de `CPU_TIME` ou `BLOCKED_TIME`, ou lorsqu’il existe une relation évidente de consommation d’une ressource (processeur, disque, thread), la barre représente la consommation d’une de ces ressources pendant la période de temps de ce compartiment. Pour ces mesures, vous pouvez dépasser le seuil de 100 % en consommant plusieurs ressources. Par exemple, si vous utilisez en moyenne deux processeurs sur un intervalle donné, vous obtenez 200 %.


## <a id="troubleshooting"></a>Résolution des problèmes

### <a name="how-can-i-know-whether-application-insights-profiler-is-running"></a>Comment savoir si le profileur d’Application Insights est en cours d’exécution ?

Le profileur s’exécute comme une tâche web en continu dans Web App. Vous pouvez ouvrir la ressource Web App dans https://portal.azure.com et vérifier l’état « ApplicationInsightsProfiler » dans le panneau Tâches Web. S’il n’est pas en cours d’exécution, ouvrez les **Journaux** pour en savoir plus. 

### <a name="why-cant-i-find-any-stack-examples-even-though-the-profiler-is-running"></a>Pourquoi aucun exemple de pile ne s’affiche même si le profileur est en cours d’exécution ?

Vous pouvez vérifier plusieurs points.

1. Assurez-vous que votre plan Web App Service est au moins au niveau tarifaire De base.
2. Assurez-vous que le SDK Application Insights 2.2 Bêta ou version supérieure est activé sur votre application web.
3. Vérifiez dans votre application web que le paramètre APPINSIGHTS_INSTRUMENTATIONKEY utilise la même clé d’instrumentation que celle utilisée par le SDK Application Insights.
4. Assurez-vous que votre application web s’exécute sur .Net Framework 4.6.
5. S’il s’agit d’une application ASP.NET Core, vérifiez également [les dépendances requises](#aspnetcore).

Après le démarrage du profileur, vous devez vous attendre à une courte période de mise en route au cours de laquelle le profileur collecte activement plusieurs traces de performances. Après cela, le profileur collecte des traces de performances pendant deux minutes, toutes les heures.  

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>J’utilisais avant l’agent Azure Service Profiler. Qu’est-il devenu ?  

Lorsque vous activez le profileur d’Application Insights, l’agent Azure Service Profiler est désactivé.

### <a id="double-counting"></a>Double comptage dans des threads parallèles

Dans certains cas, la mesure du temps total dans la visionneuse de pile est supérieure à la durée réelle de la requête. 

Cela peut se produire lorsque deux threads au moins sont associés à une requête et qu’ils s’exécutent en parallèle. Le temps total de threads est alors supérieur au temps écoulé. Dans de nombreux cas, un thread peut être en attente le temps que l’autre se termine. La visionneuse tente de détecter et d’omettre les attentes sans intérêt, mais se trompe en affichant trop d’informations plutôt qu’en omettant ce qui pourrait être des informations critiques.  

Lorsque vous voyez des threads parallèles dans vos traces, vous devez identifier les threads en attente afin de déterminer le chemin critique pour la requête. Dans la plupart des cas, le thread qui passe rapidement à un état d’attente attend simplement les autres threads. Concentrez-vous sur ces autres threads et ignorez le temps dans les threads en attente.

### <a id="issue-loading-trace-in-viewer"></a>Aucune donnée de profilage

1. Si les données que vous essayez d’afficher datent d’il y a plus de deux semaines, essayez de limiter votre filtre de temps puis réessayez.

2. Vérifiez que votre proxy ou pare-feu n’a pas bloqué l’accès à https://gateway.azureserviceprofiler.net. 

3. Vérifiez que la clé d’instrumentation Application Insights que vous utilisez dans votre application est identique à celle de la ressource Application Insights avec laquelle vous avez activé le profilage. La clé figure généralement dans ApplicationInsights.config, mais vous pouvez également la trouver dans le fichier web.config ou app.config.

### <a name="error-report-in-the-profiling-viewer"></a>Rapport d’erreurs dans la visionneuse de profilage

Émettez un ticket de support à partir du portail. Pensez à indiquer l’ID de corrélation du message d’erreur.


## <a name="manual-installation"></a>Installation manuelle

Lorsque vous configurez le profileur, les mises à jour suivantes sont appliquées aux paramètres de Web App. Vous pouvez aussi les appliquer vous-même manuellement :

1. Dans le panneau de contrôle Application web, ouvrez Paramètres.
2. Définissez « Version du .NET Framework » sur 4.6.
3. Activez l’option « Toujours actif ».
4. Ajoutez le paramètre d’application « __APPINSIGHTS_INSTRUMENTATIONKEY__ » et définissez la valeur sur la même clé d’instrumentation que celle utilisée par le Kit de développement logiciel (SDK).
5. Dans **Extensions**, ajoutez Application Insights. L’installation prend quelques minutes.

## <a id="aspnetcore"></a>Prise en charge d’ASP.NET Core

Les applications ASP.NET Core sont actuellement prises en charge sur le runtime .NET Core.

L’application doit également inclure les composants suivants pour activer le profilage.

1. [Application Insights pour ASP.NET Core 2.0](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases/tag/v2.0.0)
2. [System.Diagnostics.DiagnosticSource 4.4.0-beta-25022-02](https://dotnet.myget.org/feed/dotnet-core/package/nuget/System.Diagnostics.DiagnosticSource/4.4.0-beta-25022-02)
    * Dans Visual Studio, cliquez sur « Outils -> Gestionnaire de package NuGet -> Paramètres du gestionnaire de package ».
    * Dans la boîte de dialogue Options, sélectionnez « Gestionnaire de package NuGet-> Sources de package ».
    * Cliquez sur le bouton « + » pour ajouter une nouvelle source de package avec le nom « DotNet-Core-MyGet » et la valeur « https://dotnet.myget.org/F/dotnet-core/api/v3/index.json ».
    * Cliquez sur le bouton « Mettre à jour » et fermez la boîte de dialogue Options.
    * Ouvrez l’Explorateur de solutions, cliquez avec le bouton droit sur le projet ASP.NET Core, puis sélectionnez « Gérer les packages NuGet ».
    * Sur l’onglet « Parcourir », sélectionnez « Source du Package : DotNet-Core-MyGet » et cochez « Inclure la version préliminaire ».
    * Recherchez « System.Diagnostics.DiagnosticSource » et choisissez « __4.4.0-beta-25022-02__ » pour l’installer.


## <a name="next-steps"></a>Étapes suivantes

* [Utilisation d’Application Insights dans Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png

