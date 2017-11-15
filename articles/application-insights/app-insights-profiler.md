---
title: Profiler des applications web dynamiques sur Azure avec Application Insights Profiler | Microsoft Docs
description: "Identifiez le chemin réactif dans le code de votre serveur web avec un profileur de faible encombrement."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mbullwin
ms.openlocfilehash: e66dc2af18785c6c8e83815129c8bca5b877d25b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2017
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profiler des applications web dynamiques sur Azure avec Application Insights

*Cette fonctionnalité d’Application Insights est généralement disponible pour Azure App Service et est en version préliminaire pour les ressources de calcul Azure.*

Découvrez combien de temps vous consacrez à chaque méthode dans votre application web dynamique à l’aide d’[Application Insights Profiler](app-insights-overview.md). L’outil de profilage Application Insights vous montre les profils détaillés des requêtes dynamiques qui ont été traitées par votre application et met en évidence le *chemin réactif* qui est le plus souvent utilisé. Le profileur sélectionne automatiquement les exemples ayant des temps de réponse différents, puis utilise diverses techniques pour réduire la surcharge.

Le profileur fonctionne actuellement pour les applications web ASP.NET s’exécutant sur Azure App Service, au moins au niveau de service de base.

## <a id="installation"></a> Activer le profileur

[Installez Application Insights](app-insights-asp-net.md) dans votre code. S’il est déjà installé, assurez-vous que vous disposez de la version la plus récente. Pour vérifier la version la plus récente, dans l’Explorateur de solutions, cliquez avec le bouton droit de la souris sur votre projet, puis sélectionnez **Gérer les packages NuGet** > **Mises à jour** > **Mettre à jour tous les packages**. Redéployez ensuite votre application.

*Vous utilisez ASP.NET Core ? Obtenez [plus d’informations](#aspnetcore).*

Dans [le portail Azure](https://portal.azure.com), ouvrez la ressource Application Insights correspondant à votre application web. Sélectionnez **Performances** > **Activer Application Insights Profiler**.

![Sélectionner la bannière Activer le profileur][enable-profiler-banner]

Sinon, vous pouvez sélectionner **Configurer** pour afficher l’état du profileur, et l’activer ou le désactiver.

![Sous Performances, sélectionnez Configurer][performance-blade]

Les applications web configurées avec Application Insights sont répertoriées sous **Configurer**. Suivez les instructions pour installer l’agent profileur, si nécessaire. Si aucune application web n’a été configurée avec Application Insights, sélectionnez **Ajouter des applications liées**.

Pour contrôler le profileur dans toutes vos applications web liées, sélectionnez **Activer le profileur** ou **Désactiver le profileur** dans le panneau **Configurer**.

![Configurer les options du volet][linked app services]

Contrairement aux applications web hébergées par le biais de plans App Service, les applications hébergées dans des ressources de calcul Azure (par exemple, une machine virtuelle Azure, un groupe identique de machines virtuelles, Azure Service Fabric et les services cloud Azure) ne sont pas gérées directement par Azure. Dans ce cas, il n’y a aucune application web à associer. Au lieu d’établir une liaison avec une application, sélectionnez le bouton **Activer le profileur**.

## <a name="disable-the-profiler"></a>Désactiver le profileur
Pour arrêter ou redémarrer le profileur pour une instance App Service individuelle, accédez à la ressource App Service, sous **Tâches web**. Pour supprimer le profileur, accédez à **Extensions**.

![Désactiver le profileur pour des tâches web][disable-profiler-webjob]

Nous vous recommandons d’activer, dès que possible, le profileur sur toutes vos applications web afin de découvrir d’éventuels problèmes de performance.

Si vous utilisez WebDeploy pour déployer des modifications sur votre application web, veillez à ne pas supprimer le dossier App_Data lors du déploiement. Dans le cas contraire, les fichiers de l’extension du profileur seront supprimés la prochaine fois que vous déploierez l’application web dans Azure.

### <a name="using-profiler-with-azure-vms-and-azure-compute-resources-preview"></a>En utilisant le profileur avec les machines virtuelles Azure et les ressources de calcul Azure (version préliminaire)

Lorsque vous [activez Application Insights pour Azure App Service en cours d’exécution](app-insights-azure-web-apps.md#run-time-instrumentation-with-application-insights), Application Insights Profiler est automatiquement disponible. Si vous avez déjà activé Application Insights pour la ressource, vous devrez peut-être effectuer une mise à jour vers la dernière version disponible par le biais de l’Assistant Configuration.

Obtenez des informations sur une [version préliminaire du profileur pour les ressources de calcul Azure](https://go.microsoft.com/fwlink/?linkid=848155).


## <a name="limitations"></a>Limites

Par défaut, la durée de rétention des données est de 5 jours. La quantité maximale de données ingérées par jour est de 10 Go.

L’utilisation du service du profileur est gratuite. Pour utiliser le service du profileur, votre application web doit être hébergée au moins au niveau de base de App Service.

## <a name="overhead-and-sampling-algorithm"></a>Surcharge et algorithme d’échantillonnage

Le profileur s’exécute aléatoirement pendant 2 minutes toutes les heures sur chaque machine virtuelle qui héberge l’application sur laquelle le profileur est activé pour capturer des traces. Lorsque le profileur est en cours d’exécution, il ajoute une surcharge d’UC de 5 à 15 % au serveur.
Plus le nombre de serveurs disponibles pour héberger l’application est important, moins le profileur a d’impact sur les performances globales de l’application. Cela est dû au fait qu’en raison de l’algorithme d’échantillonnage le profileur s’exécute à tout moment sur seulement 5 % des serveurs. Davantage de serveurs sont disponibles pour traiter les demandes web afin de compenser la surcharge du serveur provoquée par l’exécution du profileur.


## <a name="view-profiler-data"></a>Afficher les données du profileur

Accédez au volet **Performances**, puis faites défiler l’écran vers le bas jusqu'à la liste des opérations.

![Colonne Exemples du volet Performances d’Application Insights][performance-blade-examples]

Le tableau des opérations comporte les colonnes suivantes :

* **NOMBRE** : le nombre de ces requêtes dans l’intervalle de temps du volet **NOMBRE**.
* **MÉDIANE** : délai standard de votre application pour répondre à une requête. La moitié de toutes les réponses étaient plus rapides que cette valeur.
* **95E CENTILE** : 95 % des réponses étaient plus rapides que cette valeur. Si cette valeur est très différente de la valeur médiane, il existe peut-être un problème intermittent au niveau de votre application. (Cette différence peut aussi s’expliquer par une fonctionnalité de conception, telle que la mise en cache.)
* **TRACES DU PROFILEUR** : une icône indique que le profileur a capturé des traces de la pile pour cette opération.

Sélectionnez **Vue** pour ouvrir l’Explorateur de trace. L’Explorateur affiche plusieurs exemples que le profileur a capturés, classés par temps de réponse.

Si vous utilisez le volet **Aperçu des performances**, accédez à la section **Prendre des mesures** de la page pour afficher les traces du profileur. Sélectionnez le bouton **Traces du profileur**.

![Aperçu du volet de performances Application Insights - Traces du profileur][performance-blade-v2-examples]

Sélectionnez un exemple pour afficher, au niveau du code, une répartition du temps d’exécution de la requête.

![Explorateur de trace d’Application Insights][trace-explorer]

L’Explorateur de trace affiche les informations suivantes :

* **Afficher le chemin réactif** : ouvre le plus grand nœud feuille, ou du moins un élément qui s’en rapproche. Dans la plupart des cas, ce nœud est adjacent à un goulot d’étranglement.
* **Étiquette** : nom de la fonction ou de l’événement. L’arborescence affiche une combinaison des codes et des événements qui se sont produits (par exemple, des événements SQL et HTTP). L’événement supérieur représente la durée globale de la requête.
* **Écoulé** : intervalle de temps entre le début de l’opération et la fin.
* **Quand** : indique à quel moment la fonction ou l’événement était en cours d’exécution par rapport à d’autres fonctions.

## <a name="how-to-read-performance-data"></a>Comment lire les données de performances

Le profileur de service Microsoft utilise un ensemble de méthodes d’échantillonnage et une instrumentation pour analyser les performances de votre application. Lorsque la collecte détaillée est en cours, le profileur de service échantillonne le pointeur d’instruction de chaque processeur de l’ordinateur à chaque milliseconde. Chaque échantillon capture l’ensemble de la pile des appels du thread en cours d’exécution. Il fournit des informations utiles et détaillées sur l’activité de ce thread, à un niveau d’abstraction élevé et faible. Le profileur de service collecte également d’autres événements afin de suivre la corrélation et la causalité des activités, tels que les événements de changement de contexte, les événements TPL (Task Parallel Library) et les événements de pool de threads.

La pile des appels présentée dans l’affichage chronologique est le résultat de l’échantillonnage et de l’instrumentation. Étant donné que chaque échantillon capture l’ensemble de la pile des appels du thread, il inclut le code du framework Microsoft .NET, et des autres infrastructures que vous référencez.

### <a id="jitnewobj"></a>Allocation d’objets (clr!JIT\_Nouveau ou clr!JIT\_Newarr1)
**clr!JIT\_Nouveau** et **clr!JIT\_Newarr1** sont des fonctions d’assistance du framework .NET qui allouent la mémoire à partir d’un tas managé. **clr!JIT\_Nouveau** est appelé lorsqu’un objet est alloué. **clr!JIT\_Newarr1** est appelé lorsqu’un tableau d’objet est alloué. Ces deux fonctions sont généralement très rapides et s’exécutent en relativement peu de temps. Si vous constatez que **clr!JIT\_Nouveau** ou **clr!JIT\_Newarr1** prend un certain temps dans votre scénario, cela indique que le code alloue peut-être de nombreux objets et qu’il consomme une quantité importante de mémoire.

### <a id="theprestub"></a>Code de chargement (clr!ThePreStub)
**clr!ThePreStub** est une fonction d’assistance intégrée au framework .NET qui prépare le code en vue de sa première exécution. Elle implique en général au moins une compilation JIT (juste à temps). Pour chaque méthode C#, **clr!ThePreStub** doit être appelé au maximum une fois pendant la durée de vie d’un processus.

Si **clr!ThePreStub** prend un certain temps pour traiter une requête, cela indique que la requête est la première à exécuter cette méthode. Le runtime du framework .NET a besoin de temps pour charger cette méthode. Vous pouvez envisager d’utiliser un processus de mise en route qui exécute cette partie du code avant que vos utilisateurs y accèdent, ou encore envisager d’exécuter Native Image Generator (ngen.exe) sur vos assemblys.

### <a id="lockcontention"></a>Contention de verrouillage (clr!JITutil\_MonContention or clr!JITutil\_MonEnterWorker)
**clr!JITutil\_MonContention** ou **clr!JITutil\_MonEnterWorker** indique que le thread actuel est en attente d’ouverture d’un verrou. Cela se produit généralement lors de l’exécution d’une instruction **LOCK** C#, lors de l’appel de méthode **Monitor.Enter** ou encore lors de l’appel d’une méthode avec l’attribut **MethodImplOptions.Synchronized**. La contention de verrouillage se produit généralement lorsque le thread A acquiert un verrou et que le thread B tente d’acquérir le même verrou avant qu’il ne soit libéré par le thread A.

### <a id="ngencold"></a>Code de chargement ([COLD])
Si le nom de la méthode contient **[COLD]**, par exemple **mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined**, cela signifie que le runtime du framework .NET exécute pour la première fois du code qui n’utilise pas une <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">optimisation guidée par profil</a>. Pour chaque méthode, il doit s’afficher au maximum une fois pendant la durée de vie du processus.

Si le code de chargement prend beaucoup de temps pour traiter une requête, cela indique que la requête est la première à exécuter la partie non optimisée de la méthode. Vous pouvez envisager d’utiliser un processus de mise en route qui exécute cette partie du code avant que vos utilisateurs y accèdent.

### <a id="httpclientsend"></a>Envoyer une requête HTTP
Les méthodes telles que **HttpClient.Send** indiquent que le code attend l’exécution d’une requête HTTP.

### <a id="sqlcommand"></a>Opération de base de données
Une méthode telle que **SqlCommand.Execute** indique que le code attend l’exécution d’une opération de base de données.

### <a id="await"></a>En attente (AWAIT\_TIME)
**AWAIT\_TIME** indique que le code attend la fin de l’exécution d’une autre tâche. Cela se produit généralement avec l’instruction C# **AWAIT**. Lorsque le code exécute une instruction C# **AWAIT**, le thread se déroule et redonne le contrôle au pool de threads : aucun thread n’est bloqué en attendant la fin de l’exécution de l’instruction **AWAIT**. Toutefois, le thread qui a exécuté l’instruction **AWAIT** est logiquement « bloqué » en attendant que l’opération se termine. L’instruction **AWAIT\_TIME** indique le temps de blocage en attendant l’exécution de la tâche.

### <a id="block"></a>Temps de blocage
**BLOCKED_TIME** indique que le code attend qu’une autre ressource soit disponible. Il peut, par exemple, attendre un objet de synchronisation, la disponibilité d’un thread ou la fin d’une requête.

### <a id="cpu"></a>Temps processeur
Le processeur est occupé à exécuter les instructions.

### <a id="disk"></a>Temps du disque
L’application effectue les opérations de disque.

### <a id="network"></a>Temps réseau
L’application effectue les opérations réseau.

### <a id="when"></a>Colonne « When » (Quand)
La colonne **When** permet de voir comment les exemples INCLUSIFS collectés pour un nœud varient au fil du temps. La plage totale de la requête est divisée en 32 périodes. Les exemples inclusifs de ce nœud sont regroupés dans ces 32 périodes. Chaque période est représentée sous forme de barre. La hauteur de la barre représente une valeur à l’échelle. Pour les nœuds marqués **CPU_TIME** ou **BLOCKED_TIME**, ou lorsqu’il existe une relation évidente de consommation d’une ressource (processeur, disque, thread), la barre représente la consommation d’une de ces ressources au cours de cette période. Pour ces mesures, il est possible d’obtenir une valeur supérieure à 100 % en consommant plusieurs ressources. Par exemple, si vous utilisez en moyenne deux processeurs sur un intervalle donné, vous obtenez 200 %.


## <a id="troubleshooting"></a>Résolution des problèmes

### <a name="too-many-active-profiling-sessions"></a>Trop de sessions de profilage actives

Actuellement, vous pouvez activer le profileur sur un maximum de quatre applications web Azure et emplacements de déploiement exécutés sur le même plan de service. Si la tâche web du profileur signale un nombre trop important de sessions de profilage actives, vous devez déplacer certaines applications web vers un autre plan de service.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Comment savoir si Application Insights Profiler est en cours d’exécution ?

Le profileur s’exécute comme une tâche web en continu dans l’application web. Vous pouvez ouvrir la ressource de l’application web dans le [portail Azure](https://portal.azure.com). Dans le volet **WebJobs**, vérifiez l’état de **ApplicationInsightsProfiler**. S’il n’est pas en cours d’exécution, ouvrez les **Journaux** pour en savoir plus.

### <a name="why-cant-i-find-any-stack-examples-even-though-the-profiler-is-running"></a>Pourquoi aucun exemple de pile ne s’affiche même si le profileur est en cours d’exécution ?

Vous pouvez vérifier plusieurs points :

* Assurez-vous que votre plan de service d’application web est au moins au niveau De base.
* Assurez-vous que le SDK Application Insights 2.2 Bêta ou version ultérieure est activé sur votre application web.
* Vérifiez dans votre application web que le paramètre **APPINSIGHTS_INSTRUMENTATIONKEY** utilise la même clé d’instrumentation que celle utilisée par le SDK Application Insights.
* Assurez-vous que votre application web s’exécute sur .Net Framework 4.6.
* Si votre application web est une application ASP.NET Core, vérifiez [les dépendances requises](#aspnetcore).

Après le démarrage du profileur, vous devez vous attendre à une courte période de mise en route au cours de laquelle le profileur collecte activement plusieurs traces de performances. Après cela, le profileur collecte des traces de performances pendant deux minutes, toutes les heures.  

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>J’utilisais avant l’agent Azure Service Profiler. Qu’est-il devenu ?  

Lorsque vous activez Application Insights Profiler, l’agent Azure Service Profiler est désactivé.

### <a id="double-counting"></a>Double comptage dans des threads parallèles

Dans certains cas, la mesure du temps total dans la visionneuse de pile est supérieure à la durée de la requête.

Cela peut se produire lorsque deux threads au moins sont associés à une requête et qu’ils s’exécutent en parallèle. Dans ce cas, le temps total de threads est supérieur au temps écoulé. Un thread peut être en attente le temps que l’autre se termine. La visionneuse tente de détecter et d’omettre les attentes sans intérêt, mais se trompe en affichant trop d’informations plutôt qu’en omettant ce qui pourrait être des informations critiques.  

Lorsque vous voyez des threads parallèles dans vos traces, identifiez les threads en attente afin de déterminer le chemin critique pour la requête. Dans la plupart des cas, le thread qui passe rapidement à un état d’attente attend simplement les autres threads. Concentrez-vous sur ces autres threads et ignorez le temps dans les threads en attente.

### <a id="issue-loading-trace-in-viewer"></a>Aucune donnée de profilage

Vous pouvez vérifier plusieurs points :

* Si les données que vous essayez d’afficher datent d’il y a plus de deux semaines, essayez de limiter votre filtre de temps puis réessayez.
* Vérifiez que votre proxy ou pare-feu n’a pas bloqué l’accès à https://gateway.azureserviceprofiler.net.
* Vérifiez que la clé d’instrumentation Application Insights que vous utilisez dans votre application est identique à celle de la ressource Application Insights avec laquelle vous avez activé le profilage. La clé se trouve généralement dans le fichier ApplicationInsights.config, mais peut également se trouver dans les fichiers web.config ou app.config.

### <a name="error-report-in-the-profiling-viewer"></a>Rapport d’erreurs dans la visionneuse de profilage

Soumettez un ticket de support dans le portail. Veillez à indiquer l’ID de corrélation du message d’erreur.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Erreur de déploiement : Répertoire non vide 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Si vous redéployez votre application web vers une ressource App Service avec le profileur activé, un message similaire au message suivant peut s’afficher :

Répertoire non vide 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Cette erreur se produit si vous exécutez Web Deploy à partir de scripts ou de Visual Studio Team Services Deployment Pipeline. La solution consiste à ajouter les paramètres de déploiement supplémentaires suivants à la tâche Web Deploy :

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Ces paramètres suppriment le dossier utilisé par Application Insights Profiler et débloquent le processus de redéploiement. Ils n’affectent pas l’instance de profileur est en cours d’exécution.


## <a name="manual-installation"></a>Installation manuelle

Lorsque vous configurez le profileur, des mises à jour sont appliquées aux paramètres de l’application web. Vous pouvez appliquer les mises à jour manuellement si votre environnement l’exige. Par exemple, si votre application s’exécute dans un environnement App Service pour PowerApps.

1. Dans le panneau de contrôle de l’application web, ouvrez **Paramètres**.
2. Définissez **Version du .NET Framework** sur **4.6**.
3. Activez l’option **Toujours actif**.
4. Ajoutez le paramètre d’application __APPINSIGHTS_INSTRUMENTATIONKEY__ et définissez la valeur sur la même clé d’instrumentation que celle utilisée par le kit de développement logiciel (SDK).
5. Ouvrez **Outils avancés**.
6. Sélectionnez **Aller à** pour ouvrir le site web Kudu.
7. Sur le site web Kudu, sélectionnez **Extensions de site**.
8. Installez __Application Insights__ à partir de la galerie Azure Web Apps.
9. Redémarrez l’application web.

## <a id="aspnetcore"></a>Prise en charge d’ASP.NET Core

Une application ASP.NET Core doit installer le package NuGet Microsoft.ApplicationInsights.AspNetCore 2.1.0-beta6 ou une version ultérieure pour fonctionner avec le profileur. Depuis le 27 juin 2017, nous ne prenons plus en charge les versions antérieures.

## <a name="next-steps"></a>Étapes suivantes

* [Utilisation d’Application Insights dans Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png
