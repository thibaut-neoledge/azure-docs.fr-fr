<properties 
	pageTitle="Guide de dépannage : Application Insights pour .NET" 
	description="Vous ne voyez pas les données dans Visual Studio Application Insights ? Essayez ici." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2016" 
	ms.author="awills"/>
 
# Guide de dépannage : Application Insights pour .NET

## Certaines de mes données télémétriques manquent

*Dans Application Insights, je vois seulement une fraction des événements qui sont générés par mon application.*

* Si vous voyez régulièrement la même fraction, cela est probablement causé par [l’échantillonnage](app-insights-sampling.md) adaptatif. Pour vérifier cela, ouvrez la recherche (dans le panneau de vue d’ensemble) et recherchez une instance d’une demande ou d’autres événements. En bas de la section Propriétés, cliquez sur «... » pour obtenir des détails de propriété complets. Si le nombre de requêtes est supérieur à 1, l’échantillonnage est en cours. 
* Dans le cas contraire, il est possible que vous rencontriez une [limite de débit](app-insights-pricing.md#limits-summary) pour votre plan tarifaire. Ces limites sont appliquées par minute.

## Problèmes Moniteur d’état

*J’ai [installé Moniteur d’état](app-insights-monitor-performance-live-website-now.md) sur mon serveur web pour surveiller les applications existantes. Aucun résultat ne s’affiche.*

Consultez [Résolution des problèmes liés à Status Monitor](app-insights-monitor-performance-live-website-now.md#troubleshooting). Les ports du pare-feu sont le plus souvent à l’origine du problème.


## <a name="q01"></a> Aucune option « Application Insights » dans Visual Studio

*Lorsque je crée un nouveau projet dans Visual Studio, ou lorsque j’effectue un clic droit sur le projet existant dans l’Explorateur de solutions, je ne vois aucune option Application Insights.*

+ Les outils ne prennent pas en charge tous les types de projets .NET. Les projets Web et WCF sont pris en charge. Pour les autres types de projets, notamment les applications de bureau ou de service, vous avez toujours la possibilité [d’ajouter un kit de développement logiciel Application Insights à votre projet manuellement](app-insights-windows-desktop.md).
+ Vérifiez que vous disposez de [Visual Studio 2013 Update 3 ou version ultérieure](http://go.microsoft.com/fwlink/?LinkId=397827). Ce logiciel est fourni préinstallé avec les outils Application Insights.
+ Sélectionnez **Outils**, **Extensions et mises à jour** et vérifiez qu**’Outils Application Insights** est installé et activé. Dans ce cas, cliquez sur **Mises à jour** pour voir si une mise à jour est disponible.
+ Ouvrez la boîte de dialogue Nouveau projet et sélectionnez l’application Web ASP.NET. Si vous voyez l’option Application Insights à cet endroit, les outils sont installés. Si ce n’est pas le cas, essayez de désinstaller, puis de réinstaller Outils Application Insights.


## <a name="q02"></a>Échec de l’ajout d’Application Insights

*Lorsque je crée un nouveau projet web, ou que j’essaie d’ajouter Application Insights à un projet existant, je reçois un message d’erreur.*

Causes probables :

* la communication avec le portail Application Insights a échoué ;
* votre compte Azure présente un problème ;
* vous disposez uniquement d’un [accès en lecture à l’abonnement ou au groupe dans lequel vous essayiez de créer la nouvelle ressource](app-insights-resources-roles-access-control.md).

Correctif :

+ vérifiez que les informations de connexion que vous avez fournies correspondent au bon compte Azure. 
+ Dans votre navigateur, vérifiez que vous avez accès au [portail Azure](https://portal.azure.com). Ouvrez Paramètres et vérifiez s’il existe des restrictions.
+ [Ajoutez Application Insights à votre projet existant :](app-insights-asp-net.md) dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre projet et sélectionnez « Ajouter Application Insights ».
+ Si cela ne fonctionne toujours pas, suivez la [procédure manuelle](app-insights-asp-net-manual.md) pour ajouter une ressource dans le portail, puis ajoutez le Kit SDK à votre projet. 

## <a name="emptykey"></a>J’obtiens une erreur « La clé d’instrumentation ne peut pas être vide ».

Il semble qu'une erreur est survenue durant l'installation d'Application Insights ou d'un enregistreur de données.

Dans l’Explorateur de solutions, cliquez avec le bouton droit sur `ApplicationInsights.config`, puis sélectionnez **Configurer Application Insights**. Une boîte de dialogue vous invite à vous connecter à Azure et à créer une ressource Application Insights, ou à réutiliser une ressource existante.


##<a name="NuGetBuild"></a> « Packages NuGet manquants » sur mon serveur de builds

*Sur ma machine de développement, tout fonctionne correctement, mais j’obtiens une erreur NuGet sur le serveur de builds.*

Consultez les sections relatives à la [restauration des packages NuGet](http://docs.nuget.org/Consume/Package-Restore) et à la [restauration automatique des packages](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## Commande de menu manquante pour ouvrir Application Insights à partir de Visual Studio

*Lorsque j’effectue un clic droit sur mon projet Explorateur de solutions, je ne vois pas toutes les commandes Application Insights, ou je ne vois pas de commande Ouvrir Application Insights.*

Causes probables :

* vous avez créé la ressource Application Insights manuellement, ou le type de projet n’est pas pris en charge par les outils Application Insights.
* Les outils Application Insights sont désactivés dans Visual Studio.
* Votre version de Visual Studio est antérieure à 2013 Mise à jour 3.

Correctif :

* assurez-vous que votre version de Visual Studio est bien 2013 Mise à jour 3 ou une version ultérieure.
* Sélectionnez **Outils**, **Extensions et mises à jour** et vérifiez qu**’Outils Application Insights** est installé et activé. Dans ce cas, cliquez sur **Mises à jour** pour voir si une mise à jour est disponible.
* Cliquez avec le bouton droit sur l’Explorateur de solutions. Si vous voyez la commande **Configurer Application Insights**, utilisez-la pour raccorder votre projet à la ressource dans le service d’Application Insights.


Dans le cas contraire, votre type de projet n’est pas directement pris en charge par les outils Application Insights. Pour voir votre télémétrie, connectez-vous au [portail Azure](https://portal.azure.com), cliquez sur Application Insights à gauche, puis sélectionnez votre application.

## « Accès refusé » lors de l’ouverture d’Application Insights dans Visual Studio

*La commande de menu « Ouvrir Application Insights » m’amène vers le portail Azure, mais j’obtiens une erreur « Accès refusé ».*

![](./media/app-insights-asp-net-troubleshoot-no-data/access-denied.png)

La connexion Microsoft que vous avez utilisée en dernier dans votre navigateur par défaut n’a pas accès à [la ressource créée au moment de l’ajout de cette application d’Application Insights](app-insights-asp-net.md). Il existe deux raisons possibles :

* Vous avez plusieurs comptes Microsoft, et peut-être un compte professionnel et un compte personnel Microsoft ? La connexion que vous avez utilisée en dernier dans votre navigateur par défaut servait à un autre compte que celui ayant accès à [Ajout de l’application Insights au projet](app-insights-asp-net.md). 

 * Correctif : cliquez sur votre nom en haut à droite de la fenêtre du navigateur et déconnectez-vous. Connectez-vous ensuite avec le compte ayant l’accès. Dans la barre de navigation de gauche, cliquez sur Application Insights, puis sélectionnez votre application.

* Quelqu’un d’autre a ajouté Application Insights au projet, et a oublié de vous octroyer [l’accès au groupe de ressources](app-insights-resources-roles-access-control.md) dans lequel elle a été créée.

 * Correctif : si c’est un compte professionnel qui a été utilisé, vous pouvez être ajouté à l’équipe ; ou vous pouvez vous voir accorder l’accès individuel au groupe de ressources individuel.



## « Actif introuvable » lors de l’ouverture d’Application Insights dans Visual Studio

*La commande de menu « Ouvrir Application Insights » m’amène vers le portail Azure, mais j’obtiens une erreur « Actif introuvable ».*

Causes probables :

* La ressource Application Insights pour votre application a été supprimée ; ou
* La clé d’instrumentation a été définie ou modifiée dans le fichier ApplicationInsights.config par une modification directe, sans mise à jour du fichier de projet. 

La clé d’instrumentation dans ApplicationInsights.config détermine l’endroit où la télémétrie est envoyée. Une ligne dans le fichier projet détermine quelle ressource est ouverte lorsque vous utilisez la commande dans Visual Studio.

Correctif :

* Dans l’explorateur de solutions, cliquez avec le bouton droit sur le projet, puis sélectionnez Application Insights, Configurer Application Insights. Dans la boîte de dialogue, vous pouvez choisir d’envoyer la télémétrie à une ressource existante ou en créer une nouvelle. Ou :
* Ouvrez la ressource directement. Connectez-vous au [portail Azure](https://portal.azure.com), cliquez sur Application Insights à gauche, puis sélectionnez votre application.



## Où puis-je trouver mes données de télémétrie ?

*Je me suis connecté au [portail Microsoft Azure](https://portal.azure.com), et je regarde le tableau de bord d’accueil Azure. Alors, où puis-je trouver mes données Application Insights ?*

* Dans la barre de navigation de gauche, cliquez sur Application Insights, puis sur le nom de votre application. Si aucun projet ne se trouve à cet endroit, vous devez [ajouter ou configurer Application Insights dans votre projet web](app-insights-asp-net.md).

    Vous allez voir certains graphiques récapitulatifs. Vous pouvez cliquer dessus pour afficher d’autres détails.

* Dans Visual Studio, cliquez sur le bouton Application Insights, pendant le débogage de votre application.


## <a name="q03"></a> Aucune donnée sur serveur (ou aucune donnée)

*J’ai exécuté mon application, puis j’ai ouvert le service Application Insights dans Microsoft Azure, mais tous les graphiques affichent « Découvrez comment collecter... » ou « Non configuré ».* Ou, *Mode page et données utilisateur uniquement, mais aucune donnée de serveur.*

+ Exécutez votre application en mode Débogage dans Visual Studio (F5). Utilisez l’application afin de générer des données de télémétrie. Vérifiez que vous pouvez voir les événements consignés dans la fenêtre Sortie de Visual Studio. 

    ![](./media/app-insights-asp-net-troubleshoot-no-data/output-window.png)

+ Dans Application Insights, ouvrez [Recherche de diagnostic](app-insights-diagnostic-search.md). En général, les données s’affichent d’abord ici.
+ Cliquez sur le bouton Actualiser. Le panneau s’actualise à intervalles réguliers, mais vous pouvez également l’actualiser manuellement. Plus les intervalles de temps sur lesquels portent les graphiques sont étendus, plus l’intervalle d’actualisation est long.
+ Consultez la correspondance des clés d’instrumentation Sur le panneau principal de votre application dans le portail Application Insights, dans la liste déroulante **Essentials**, examinez **Clé d’instrumentation**. Ensuite, dans votre projet dans Visual Studio, ouvrez le fichier ApplicationInsights.config et recherchez le `<instrumentationkey>`. Vérifiez que les deux clés sont semblables. Si ce n’est pas le cas :
 + Dans le portail, cliquez sur Application Insights et recherchez la ressource d’application avec la clé adéquate ; ou
 + Dans l’Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le projet, puis sélectionnez Application Insights, Configurer. Réinitialisez l’application pour envoyer des données de télémétrie à la ressource appropriée.
 + Si vous ne trouvez pas les clés correspondantes, vérifiez que vous utilisez pour Visual Studio les mêmes informations de connexion que pour le portail.

    ![](./media/app-insights-asp-net-troubleshoot-no-data/ikey-check.png)
    
+ Dans [tableau de bord d’accueil de Microsoft Azure](https://portal.azure.com), examinez la carte État du service. Si des alertes sont indiquées, attendez qu'elles soient corrigées (OK), puis fermez et rouvrez le volet de votre application Application Insights.
+ Vérifiez également [notre blog d'état](http://blogs.msdn.com/b/applicationinsights-status/).
+ Avez-vous écrit de code pour le [côté serveur SDK](app-insights-api-custom-events-metrics.md) susceptible de modifier la clé d’instrumentation dans `TelemetryClient` instances ou dans `TelemetryContext` ? Ou avez-vous rédigé une [configuration de filtre ou d’échantillonnage](app-insights-api-filtering-sampling.md) trop exclusive ?
+ Si vous avez modifié ApplicationInsights.config, vérifiez attentivement la configuration de [TelemetryInitializers et de TelemetryProcessors](app-insights-api-filtering-sampling.md). Un type ou un paramètre nommé de manière incorrecte peut empêcher le kit de développement logiciel d'envoyer des données.

## <a name="q04"></a>Aucune donnée sur l’Affichage des pages, les navigateurs, l’utilisation

*Je vois des données dans les graphiques Heures de réponse de serveur et Demandes de serveur, mais aucune donnée Temps de chargement de la page consultée, ou dans les panneaux Navigateur ou Utilisation.*

Les données proviennent de scripts dans les pages web.

+ Si vous avez ajouté Application Insights à un projet existant, [vous devez ajouter manuellement les scripts](app-insights-javascript.md).
+ Assurez-vous que Microsoft Internet Explorer n'affiche pas votre site en mode de compatibilité.
+ Utilisez la fonctionnalité de débogage du navigateur (accessible via F12 dans certains navigateurs, puis Réseau) pour vérifier que les données sont envoyées à `dc.services.visualstudio.com`.

## Aucune donnée de dépendance ou d’exception

Consultez les sections [télémétrie des dépendances](app-insights-asp-net-dependencies.md) et [télémétrie d’exception](app-insights-asp-net-exceptions.md).

## Aucune donnée de performances

Les données de performances (UC, taux E/S, et ainsi de suite) sont disponibles pour les [services web Java](app-insights-java-collectd.md), les [applications de bureau Windows](app-insights-windows-desktop.md), les [services et applications web IIS si vous installez le moniteur d’état](app-insights-monitor-performance-live-website-now.md) et [Azure Cloud Services](app-insights-azure.md). Vous trouverez ces données sous Paramètres, Serveurs.

Ces données ne sont pas disponibles pour les sites web Azure.

## Aucune donnée (serveur) n’apparaît depuis que j’ai publié l’application sur mon serveur

+ Vérifiez que vous avez réellement copié toutes les DLL Microsoft ApplicationInsights sur le serveur, avec Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
+ Vous devrez ouvrir les ports TCP 80 et 443 de votre pare-feu pour le trafic sortant vers dc.services.visualstudio.com et f5.services.visualstudio.com.
+ Si vous devez utiliser un proxy pour l'envoi depuis votre réseau d'entreprise, définissez le paramètre [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) dans le fichier Web.config.
+ Windows Server 2008 : assurez-vous que vous avez installé les mises à jour suivantes : [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523) et [KB2600217](https://support.microsoft.com/kb/2600217).


## Je pouvais voir les données, mais plus maintenant

* Vérifiez le [blog d'état](http://blogs.msdn.com/b/applicationinsights-status/).
* Vous souhaitez savoir si vous avez atteint votre quota mensuel de points de données ? Ouvrez les champs Paramètres/Quota et Tarification pour le savoir. Le cas échéant, vous pouvez mettre à niveau votre forfait ou payer pour disposer d'une capacité supplémentaire. Consultez le [mécanisme de tarification](https://azure.microsoft.com/pricing/details/application-insights/).


## Je ne vois pas toutes les données que j’attends

Si votre application envoie des données en grand nombre et si vous utilisez le kit de développement logiciel Application Insights pour ASP.NET version 2.0.0-beta3 ou ultérieure, la fonctionnalité [d’échantillonnage adaptatif](app-insights-sampling.md) peut fonctionner et transmettre uniquement un pourcentage de vos données de télémétrie.

Vous pouvez le désactiver, mais cela n’est pas recommandé. L’échantillonnage est conçu pour que la télémétrie associée soit correctement transmise pour faciliter le diagnostic.

## Données géographiques erronées dans la télémétrie de l’utilisateur

Les dimensions de la ville, de la région et des pays proviennent des adresses IP et ne sont pas toujours précises.

## Exception « méthode introuvable » lors de l’exécution dans Azure Cloud Services

Avez-vous effectué une génération pour .NET 4.6 ? 4.6 n’est pas automatiquement pris en charge dans les rôles Azure Cloud Services. [Installez 4.6 sur chaque rôle](../cloud-services/cloud-services-dotnet-install-dotnet.md) avant d’exécuter votre application.

## Ne fonctionne toujours pas...

* [Forum Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/fr-FR/home?forum=ApplicationInsights)

<!---HONumber=AcomDC_0608_2016-->