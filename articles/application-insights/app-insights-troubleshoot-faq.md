<properties 
	pageTitle="Dépannage et questions : Application Insights" 
	description="Un aspect de Visual Studio Application Insights vous semble abscons ou ne fonctionne pas correctement ? Essayez ici." 
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
	ms.date="11/04/2015" 
	ms.author="awills"/>
 
# Dépannage et questions : Application Insights pour ASP.NET

## Puis-je utiliser Application Insights avec... ?

[Voir Plateformes][platforms]

## Est-ce gratuit ?

* Oui, si vous choisissez le [niveau de tarification](app-insights-pricing.md) gratuit. Vous obtenez la plupart des fonctionnalités et un quota important de données. 
* Vous devez fournir les données de votre carte de crédit pour vous enregistrer auprès de Microsoft Azure, mais aucun service ne vous sera facturé, sauf si vous utilisez un autre service Azure payant, ou si vous mettez explicitement à niveau vers un niveau payant.
* Si votre application envoie plus de données que le quota mensuel autorisé pour le niveau gratuit, les données ne sont plus consignées. Si cela se produit, vous pouvez choisir de commencer à payer ou d'attendre jusqu'à ce que le quota soit réinitialisé à la fin du mois.
* Les données de session et l'utilisation de base ne sont pas soumises à un quota.
* Il existe aussi une version d'évaluation gratuite de 30 jours qui vous permet d'obtenir les fonctionnalités du niveau Premium gratuitement.
* Chaque ressource d'application possède un quota distinct, et vous définissez son niveau de tarification indépendamment des autres.

#### Que vais-je obtenir si je paye ?

* Un [quota de données mensuel](http://azure.microsoft.com/pricing/details/application-insights/) plus important.
* L'option consistant à payer le « dépassement » pour poursuivre la collecte des données une fois le quota mensuel dépassé. Si vos données dépassent le quota, vous êtes facturé par Mo.
* [Exportation continue](app-insights-export-telemetry.md).

## Ajout du Kit de développement logiciel (SDK)

#### <a name="q01"></a>Je ne vois pas s'afficher l'option Ajouter Application Insights à mon projet dans Visual Studio.

+ Vérifiez que vous disposez de [Visual Studio 2013 Update 3 ou version ultérieure](http://go.microsoft.com/fwlink/?LinkId=397827). Ce logiciel est fourni préinstallé avec les outils Application Insights.
+ Bien que les outils ne prennent pas en charge tous les types d'applications, vous pouvez sans doute ajouter manuellement un Kit de développement logiciel (SDK) Application Insights à votre projet. Suivez [cette procédure][windows]. 


#### <a name="q02"></a>Mon nouveau projet web a été créé, mais l'ajout d'Application Insights a échoué.

Cela peut se produire si :

* la communication avec le portail Application Insights a échoué ;
* il existe un problème avec votre compte ;
* vous disposez uniquement d’un [accès en lecture à l’abonnement ou au groupe dans lequel vous essayiez de créer la nouvelle ressource](app-insights-resources-roles-access-control.md).

Solution :

+ Vérifiez que les informations d'identification que vous avez fournies correspondent au compte Azure correct. Dans certaines versions précédentes des outils, ces informations d'identification Microsoft Azure, qui sont visibles dans la boîte de dialogue Nouveau projet, peuvent être différentes de celles affichées dans le coin supérieur droit de Visual Studio.
+ Dans votre navigateur, vérifiez que vous avez accès au [portail Azure](https://portal.azure.com). Ouvrez Paramètres et vérifiez s’il existe des restrictions.
+ [Ajoutez Application Insights à votre projet existant :][start] dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre projet et sélectionnez « Ajouter Application Insights ».
+ Si cela ne fonctionne toujours pas, suivez la [procédure manuelle](app-insights-start-monitoring-app-health-usage.md) pour ajouter une ressource dans le portail, puis ajoutez le Kit SDK à votre projet. 

#### <a name="emptykey"></a>J’obtiens une erreur « La clé d’instrumentation ne peut pas être vide ».

Il semble qu'une erreur est survenue durant l'installation d'Application Insights ou d'un enregistreur de données.

Dans l’Explorateur de solutions, cliquez avec le bouton droit sur `ApplicationInsights.config`, puis sélectionnez **Mettre à jour Application Insights**. Une boîte de dialogue vous invite à vous connecter à Azure et à créer une ressource Application Insights, ou à réutiliser une ressource existante.


#### <a name="q14"></a>Que modifie Application Insights dans mon projet ?

Cela dépend du type de projet. Pour une application web :


+ Ajoute ces fichiers à votre projet :

 + ApplicationInsights.config.
 + ai.js


+ Installe ces packages NuGet :

 -  *Application Insights API* - API de base

 -  *Application Insights API for Web Applications* - permet d'envoyer des données de télémétrie depuis le serveur

 -  *Application Insights API for JavaScript Applications* - permet d'envoyer des données de télémétrie depuis le client

    Ces packages comprennent les assemblys suivants :

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ Insère des éléments dans :

 - Web.config

 - packages.config

+ (Nouveaux projets uniquement - si vous [ajoutez Application Insights à un projet existant][start], vous devez le faire manuellement). Insère des extraits de code dans le code du client et du serveur, afin de les initialiser avec l'ID de la ressource Application Insights. Par exemple, dans une application MVC, le code est inséré dans la page maître Views/Shared/\_Layout.cshtml.

####<a name="NuGetBuild"></a> Un message signalant qu'un ou plusieurs packages NuGet sont manquants s'affiche sur mon serveur de builds, même si la compilation fonctionne correctement sur mes ordinateurs de développement.

Consultez les sections relatives à la [restauration des packages NuGet](http://docs.nuget.org/Consume/Package-Restore) et à la [restauration automatique des packages](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

####<a name="FailUpdate"></a> Un message signalant que le projet fait référence à un ou plusieurs packages NuGet manquants sur mon PC s'affiche lorsque je tente de lancer la compilation après la mise à jour des packages NuGet vers la version 0.17 ou une version plus récente.

Si cette erreur s'affiche après la mise à jour des packages NuGet vers la version 0.17 ou une version plus récente, vous devez modifier le fichier du projet et supprimer les cibles BCL qui ont été abandonnées.

Pour ce faire :

1. Cliquez avec le bouton droit de la souris sur votre projet dans l'Explorateur de solutions, puis sélectionnez Décharger le projet.
2. Cliquez de nouveau avec le bouton droit de la souris sur le projet, puis choisissez Modifier *votreProjet.csproj*. 
3. Atteignez la fin du fichier du projet et supprimez les cibles BCL semblables à ce qui suit : ``` <Import Project="..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets" Condition="Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" />
	  
	  <Target Name="EnsureBclBuildImported" BeforeTargets="BeforeBuild" Condition="'$(BclBuildImported)' == ''">
	  
	    <Error Condition="!Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" Text="This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=317567." HelpKeyword="BCLBUILD2001" />
	    
	    <Error Condition="Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" Text="The build restored NuGet packages. Build the project again to include these packages in the build. For more information, see http://go.microsoft.com/fwlink/?LinkID=317568." HelpKeyword="BCLBUILD2002" />
	    
	</Target> ```
4. Enregistrez le fichier .
5. Cliquez avec le bouton droit de la souris sur le projet, puis choisissez Recharger *votreProjet.csproj*.

## Comment mettre à niveau à partir d'anciennes versions du Kit de développement logiciel (SDK) ?

Consultez les [notes de publication](app-insights-release-notes.md) du Kit de développement logiciel (SDK) adapté à votre type d'application.


## Absence de données

#### <a name="q03"></a>J'ai ajouté Application Insights sans problème et exécuté mon application, mais je ne vois aucune donnée dans le portail.

+ Dans la page Vue d'ensemble, cliquez sur la vignette Rechercher pour ouvrir la fonction Recherche de diagnostic. Les données s'affichent d'abord ici.
+ Cliquez sur le bouton Actualiser. Le panneau s’actualise à intervalles réguliers, mais vous pouvez également l’actualiser manuellement. Plus les intervalles de temps sur lesquels portent les graphiques sont étendus, plus l’intervalle d’actualisation est long.
+ Dans le tableau de démarrage Microsoft Azure, examinez la carte d'état du service. Si des alertes sont indiquées, attendez qu'elles soient corrigées (OK), puis fermez et rouvrez le volet de votre application Application Insights.
+ Vérifiez également [notre blog d'état](http://blogs.msdn.com/b/applicationinsights-status/).
+ Si vous avez modifié ApplicationInsights.config, vérifiez attentivement la configuration de TelemetryInitializers et de TelemetryProcessors. Un type ou un paramètre nommé de manière incorrecte peut empêcher le kit de développement logiciel d'envoyer des données.

#### Aucune donnée n’apparaît depuis que j’ai publié l’application sur mon serveur

+ Vérifiez que vous avez réellement copié toutes les DLL Microsoft ApplicationInsights sur le serveur, avec Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
+ Vous devrez ouvrir les ports TCP 80 et 443 de votre pare-feu pour le trafic sortant vers dc.services.visualstudio.com et f5.services.visualstudio.com.
+ Si vous devez utiliser un proxy pour l'envoi depuis votre réseau d'entreprise, définissez le paramètre [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) dans le fichier Web.config.
+ Windows Server 2008 : assurez-vous que vous avez installé les mises à jour suivantes : [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523) et [KB2600217](https://support.microsoft.com/kb/2600217).


#### <a name="q04"></a>Je ne vois apparaître aucune donnée dans la zone Analyse de l'utilisation pour mon site Web.

+ Les données proviennent de scripts dans les pages web. Si vous avez ajouté Application Insights à un projet existant, [vous devez ajouter manuellement les scripts][start].
+ Assurez-vous que Microsoft Internet Explorer n'affiche pas votre site en mode de compatibilité.
+ Utilisez la fonctionnalité de débogage du navigateur (accessible via F12 dans certains navigateurs, puis Réseau) pour vérifier que les données sont envoyées à l'adresse dc.services.visualstudio.com.

#### <a name="q08"></a>Puis-je utiliser Application Insights pour surveiller un serveur web intranet ?

Oui. Vous pouvez surveiller l'intégrité et l'utilisation si votre serveur peut envoyer des données sur le réseau Internet public. Dans votre pare-feu, ouvrez les ports TCP 80 et 443 pour le trafic sortant vers dc.services.visualstudio.com et f5.services.visualstudio.com.

Cependant, si vous voulez exécuter des tests web sur votre service, ce dernier doit être accessible depuis le réseau Internet public, sur le port 80.

#### Puis-je surveiller un serveur web intranet qui n'a pas accès au réseau Internet public ?

Vous devez configurer un proxy capable de transmettre des appels POST https à l'adresse dc.services.visualstudio.com.

#### Je pouvais voir les données, mais plus maintenant

* Vérifiez le [blog d'état](http://blogs.msdn.com/b/applicationinsights-status/).
* Vous souhaitez savoir si vous avez atteint votre quota mensuel de points de données ? Ouvrez les champs Paramètres/Quota et Tarification pour le savoir. Le cas échéant, vous pouvez mettre à niveau votre forfait ou payer pour disposer d'une capacité supplémentaire. Consultez le [mécanisme de tarification](http://azure.microsoft.com/pricing/details/application-insights/).

## Status Monitor ne fonctionne pas

Consultez [Résolution des problèmes liés à Status Monitor](app-insights-monitor-performance-live-website-now.md#troubleshooting). Les ports du pare-feu sont le plus souvent à l’origine du problème.

## Le portail

#### <a name="q05"></a>Je consulte le tableau de démarrage de la version préliminaire de Microsoft Azure. Comment trouver mes données dans Application Insights ?

Voici les choix qui s'offrent à vous :

* Sélectionnez Parcourir, Application Insights, puis le nom de votre projet. Si vous ne voyez pas de projet ici, [ajoutez Application Insights à votre projet web dans Visual Studio][start].

* Dans l'Explorateur de solutions Visual Studio, cliquez avec le bouton droit sur votre projet web, puis sélectionnez Ouvrir le portail Application Insights.


#### <a name="update"></a>Comment puis-je changer la ressource Azure à laquelle mon projet envoie des données ?

Dans l’Explorateur de solutions, cliquez avec le bouton droit sur `ApplicationInsights.config`, puis sélectionnez **Mettre à jour Application Insights**. Vous pouvez envoyer les données à une ressource existante ou à une nouvelle ressource dans Azure. L'Assistant Mise à jour modifie la clé d'instrumentation dans ApplicationInsights.config, qui détermine où le Kit de développement logiciel (SDK) du serveur envoie vos données. Cela modifie également la clé où de l'affichage dans vos pages web, sauf si vous désactivez l'option « Tout mettre à jour ».


#### <a name="q06"></a>Sur l'écran d'accueil de la version préliminaire de Microsoft Azure, cette carte indique-t-elle l'état de mon application ?

Non. Elle indique l'état du service Azure. Pour afficher et examiner les résultats de vos tests web, sélectionnez Parcourir > Application Insights > (votre application).



#### <a name="data"></a>Combien de temps les données sont-elles conservées dans le portail ? Sont-elles sécurisées ?

Voir [Rétention de données et confidentialité][data].

## Journalisation

#### <a name="post"></a>Comment consulter les données POST dans la fonction Recherche de diagnostic ?

Les données POST ne sont pas automatiquement consignées, mais vous pouvez utiliser un appel TrackTrace ; placez les données dans le paramètre message. Ce dernier présente une limite de taille plus longue que les limites relatives aux propriétés de type chaîne, bien que vous ne puissiez pas lui appliquer de filtres.

## Sécurité

#### Mes données sont-elles sécurisées sur le portail ? Pendant combien de temps sont-elles conservées ?

Voir [Rétention de données et confidentialité][data].


## <a name="q17"></a> Comment savoir si j'ai activé tout ce qu'il faut pour utiliser Application Insights ?

<table border="1">
<tr><th>Ce qui suit doit s'afficher</th><th>Comment y accéder</th><th>Utilité</th></tr>
<tr><td>Graphiques de disponibilité</td><td><a href="../app-insights-monitor-web-app-availability/">Tests&#160;web</a></td><td>Savoir si votre application&#160;web est active</td></tr>
<tr><td>Performances des applications de serveur (temps de réponse, etc.)
</td><td><a href="../app-insights-start-monitoring-app-health-usage/">Ajout d'Application&#160;Insights à votre projet</a><br/>ou <br/><a href="../app-insights-monitor-performance-live-website-now/">Installation d’AI Status Monitor sur le serveur</a> (ou écrivez votre propre code pour <a href="../app-insights-api-custom-events-metrics/#track-dependency">suivre des dépendances</a>)</td><td>Détecter les problèmes de performances</td></tr>
<tr><td>Télémétrie des dépendances</td><td><a href="../app-insights-monitor-performance-live-website-now/">Installation d'AI Status Monitor sur le serveur</a></td><td>Diagnostiquer les problèmes relatifs à des bases de données ou à d'autres composants externes</td></tr>
<tr><td>Obtention de l'arborescence des appels de procédure à partir des exceptions</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">Insertion d'appels&#160;TrackException dans votre code</a> (certains d'entre eux sont cependant signalés automatiquement)</td><td>Détecter et diagnostiquer les exceptions</td></tr>
<tr><td>Recherche des données de suivi des journaux</td><td><a href="../app-insights-search-diagnostic-logs/">Ajout d'un enregistreur de données</a></td><td>Diagnostiquer les exceptions et problèmes de performances</td></tr>
<tr><td>Principes fondamentaux d'utilisation des clients (vues de page, sessions, etc.)</td><td><a href="../app-insights-javascript/">Initialiseur&#160;JavaScript dans les pages&#160;web</a></td><td>Analyse de l'utilisation</td></tr>
<tr><td>Mesures personnalisées des clients</td><td><a href="../app-insights-api-custom-events-metrics/">Appels de suivi dans les pages&#160;web</a></td><td>Améliorer l'expérience utilisateur</td></tr>
<tr><td>Mesures personnalisées des serveurs</td><td><a href="../app-insights-api-custom-events-metrics/">Appels de suivi dans le code serveur</a></td><td>Décisionnel</td></tr>
</table>

Si votre service web est exécuté dans une machine virtuelle Azure, vous pouvez également [obtenir des diagnostics][azurediagnostic] ici.

## Automatisation

Vous pouvez [écrire un script PowerShell](app-insights-powershell-script-create-resource.md) pour créer une ressource Application Insights.

## Réponses supplémentaires

* [Forum Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/fr-FR/home?forum=ApplicationInsights)


<!--Link references-->

[azurediagnostic]: ../insights-how-to-use-diagnostics.md
[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=Nov15_HO2-->