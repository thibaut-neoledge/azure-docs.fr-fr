<properties 
	pageTitle="Dépannage - Questions et réponses - Application Insights" 
	description="Incompréhension ou dysfonctionnement ? Essayez ici." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="awills"/>
 
# Dépannage - Questions et réponses - Application Insights dans la version préliminaire Microsoft Azure

+ [Je ne vois pas l'option Ajouter Application Insights à mon projet dans Visual Studio](#q01)
+ [Mon nouveau projet web a été créé, mais l'ajout de Application Insights a échoué.](#q02)
+ [J'ai ajouté Application Insights sans problème et exécuté mon application, mais je ne vois aucune donnée dans le portail.](#q03)
+ [Je ne vois aucune donnée sous Analyse de l'utilisation](#q04)
+ [Je cherche le tableau de démarrage de la version préliminaire de Microsoft Azure. Comment trouver mes données dans Application Insights ?](#q05)
+ [Comment puis-je modifier la ressource Azure sous laquelle apparaissent mes données ?](#update)
+ [J'obtiens une erreur " La clé d'instrumentation ne peut pas être vide ".](#emptykey)
+ [Dans l'écran d'accueil de la version préliminaire de Microsoft Azure, cette carte indique-t-elle l'état de mon application ?](#q06)
+ [Quand j'ajoute Application Insights à mon application et quand j'ouvre le portail Application Insights, l'affichage est complètement différent de vos captures d'écran.](#q07)
+ [Puis-je utiliser Application Insights pour surveiller un serveur web intranet ?](#q08)
+ [Comment obtenir des données Windows Phone ou Windows Store ?](#q09)
+ [Comment afficher les événements et les vues de pages que j'ai consignés dans mon code ?](#q10)
+ [Comment se fait-il qu'il existe deux versions de Application Insights ?](#q11)
+ [Comment retrouver toutes les fonctions que j'avais dans la version Visual Studio Online de Application Insights ?](#q13)
+ [Que modifie Application Insights dans mon projet ?](#q14)
+ [Comment trouver mes résultats dans Application Insights ?](#q15)
+ [Quels ports de mon pare-feu dois-je ouvrir ?](#q16)
+ [Comment savoir si j'ai activé tout ce qu'il faut pour utiliser Application Insights ?](#q17)
+ [En savoir plus](#next)



## <a name="q01"></a>Je ne vois pas l'option Ajouter Application Insights à mon projet dans Visual Studio

+ Vérifiez que vous disposez de [Visual Studio Update 3](http://go.microsoft.com/fwlink/?LinkId=397827). Il est préinstallé avec les Outils Application Insights que vous pouvez voir dans le Gestionnaire d'extensions.
+ Application Insights dans la version préliminaire de Microsoft Azure est actuellement disponible uniquement pour les projets web ASP.NET en C# ou Visual Basic.
+ Si vous avez un projet existant, ouvrez l'Explorateur de solutions et vérifiez que vous avez cliqué sur le projet web (et non sur un autre projet ou sur la solution). Vous devez voir l'option de menu " Ajouter la télémétrie Application Insights au projet ".
+ Si vous créez un projet, dans Visual Studio, ouvrez Fichier > Nouveau projet et sélectionnez {Visual C#|Visual Basic} > Web > Application Web ASP.NET. L'option Ajouter Application Insights au projet doit être affichée.

## <a name="q02"></a>Mon nouveau projet web a été créé, mais l'ajout de Application Insights a échoué.

Cela peut se produire si la communication avec le portail Application Insights a échoué ou si votre compte présente un problème.

+ Vérifiez que les informations d'identification que vous avez fournies correspondent au compte Azure correct. Ces informations d'identification Microsoft Azure, que vous voyez dans la boîte de dialogue Nouveau projet, peuvent être différentes de celles de Visual Studio Online affichées dans le coin supérieur droit de Visual Studio.
+ Attendez quelques instants, puis [ajoutez Application Insights à votre projet existant][start].
+ Dans les paramètres de votre compte Microsoft Azure, vérifiez la présence de limitations. Voyez si vous pouvez ajouter manuellement une application Application Insights.


## <a name="q03"></a>J'ai ajouté Application Insights sans problème et exécuté mon application, mais je ne vois aucune donnée dans le portail.

+ Vous devez fermer et ouvrir un volet là où vous attendez des données. Dans la version actuelle, le contenu d'un volet n'est pas automatiquement actualisé.
+ Dans le tableau de démarrage Microsoft Azure, examinez la carte d'état du service. Si des alertes sont indiquées, attendez qu'elles soient corrigées (OK), puis fermez et rouvrez le volet de votre application Application Insights.
+ Vous devrez ouvrir les ports TCP 80 et 443 de votre pare-feu pour le trafic sortant vers dc.services.visualstudio.com et f5.services.visualstudio.com.

## <a name="q04"></a>Je ne vois aucune donnée sous Analyse de l'utilisation

+ Les données proviennent de scripts dans les pages web. Si vous avez ajouté Application Insights à un projet existant, [vous devez ajouter manuellement les scripts][start].


## <a name="q05"></a>Je cherche le tableau de démarrage de la version préliminaire de Microsoft Azure. Comment trouver mes données dans Application Insights ?

Voici les choix qui s'offrent à vous :

* Sélectionnez Parcourir, Application Insights, puis le nom de votre projet. Si vous ne voyez pas de projet ici, [ajoutez Application Insights à votre projet web dans Visual Studio][start].

* Dans l'Explorateur de solutions Visual Studio, cliquez avec le bouton droit sur votre projet web, puis sélectionnez Ouvrir le portail Application Insights.

## <a name="update"></a>Comment puis-je changer la ressource Azure à laquelle mon projet envoie des données ?

Dans l'Explorateur de solutions, cliquez avec le bouton droit sur `ApplicationInsights.config`, puis sélectionnez **Mettre à jour Application Insights**. Vous pouvez envoyer les données à une ressource existante ou à une nouvelle ressource dans Azure. L'Assistant Mise à jour modifie la clé d'instrumentation dans ApplicationInsights.config, qui détermine où le Kit de développement logiciel (SDK) du serveur envoie vos données. Cela modifie également la clé où de l'affichage dans vos pages web, sauf si vous désactivez l'option " Tout mettre à jour ".

## <a name="emptykey"></a>J'obtiens une erreur " La clé d'instrumentation ne peut pas être vide ".

Il semble qu'une erreur est survenue durant l'installation d'Application Insights ou d'un enregistreur de données.

Dans l'Explorateur de solutions, cliquez avec le bouton droit sur `ApplicationInsights.config`, puis sélectionnez **Mettre à jour Application Insights**. Une boîte de dialogue vous invite à vous connecter à Azure et à créer une ressource Application Insights, ou à réutiliser une ressource existante.

## <a name="q06"></a>Dans l'écran d'accueil de la version préliminaire de Microsoft Azure, cette carte indique-t-elle l'état de mon application ?

Non. Elle indique l'état du service Azure. Pour afficher et examiner les résultats de vos tests web, sélectionnez Parcourir > Application Insights > (votre application). 


## <a name="q07"></a>Quand j'ajoute Application Insights à mon application et quand j'ouvre le portail Application Insights, l'affichage est complètement différent de vos captures d'écran.

Vous utilisez peut-être [une ancienne version des Outils Application Insights](http://msdn.microsoft.com/library/dn793604.aspx) qui se connecte à la version Visual Studio Online.

The help pages you're looking at refer to [Les pages d'aide que vous examinez font référence à Application Insights pour la version préliminaire de Microsoft Azure][start] qui est déjà connectée par défaut à Visual Studio Update 3. 

## <a name="q08"></a>Puis-je utiliser Application Insights pour surveiller un serveur web intranet ?

Oui. Vous pouvez surveiller l'intégrité et l'utilisation si votre serveur peut envoyer des données sur le réseau Internet public.

Mais si vous voulez exécuter des tests web sur votre service, il doit être accessible depuis le réseau Internet public.

## <a name="q09"></a>Comment obtenir des données Windows Phone ou Windows Store ?

Ceci n'est encore pris en charge dans cette version de Microsoft Azure. Veuillez utiliser la [version Visual Studio Online][older].


## <a name="q10"></a>Comment afficher les événements et les vues de pages que j'ai consignés dans mon code ?

Nous ne prenons pas encore en charge cette fonction dans la version Microsoft Azure. Bientôt disponible. Actuellement, vous pouvez essayer [l'ancienne version][older].


## <a name="q11"></a>Comment se fait-il qu'il existe deux versions de Application Insights ?

L'ancien portail fait partie de Visual Studio Online. Nous n'apporterons pas d'autres modifications importantes à cette version. Si vous disposez d'une ancienne version des outils Application Insights pour Visual Studio, ils se connectent au portail Visual Studio Online.

Visual Studio Update 3 est fourni avec une version préinstallée des nouveaux outils Application Insights qui se connectent au nouveau portail Application Insights, composant de la version préliminaire de Microsoft Azure. Nous portons actuellement Application Insights dans ce nouvel environnement. Ce travail n'est pas encore terminé.

## <a name="q13"></a>Comment retrouver toutes les fonctions que j'avais dans la version Visual Studio Online de Application Insights ?

1. Ouvrez le Gestionnaire d'extensions de Visual Studio. 
2. Désinstallez les Outils Application Insights.
3. Exécutez [le programme d'installation de l'ancienne version des outils](http://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a) et lisez le [Guide de mise en route][older].

## <a name="q14"></a>Que modifie Application Insights dans mon projet ?

Cela dépend du type de projet. Pour une application web : +


+ Ajoute ces fichiers à votre projet :

 + ApplicationInsights.config. 
 + ai.js


+ Installe ces packages NuGet :

 -  *Application Insights API* - API de base

 -  *Application Insights API for Web Applications* - permet d'envoyer des données de télémétrie depuis le serveur

 -  *Application Insights API for JavaScript Applications* - permet d'envoyer des données de télémétrie depuis le client

    Ces packages comprennent les assemblys suivants :

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ Insère des éléments dans :

 - Web.config

 - packages.config

+ (Nouveaux projets uniquement - si vous [ajoutez Application Insights à un projet existant][start], vous devez le faire manuellement). Insère des extraits de code dans le code du client et du serveur pour les initialiser avec l'ID de la ressource Application Insights : par exemple, dans une application MVC, le code est inséré dans :


## <a name="q15"></a>Comment trouver mes résultats dans Application Insights ?
1. Ouvrez Microsoft Azure :
 - Dans Visual Studio, cliquez avec le bouton droit sur le projet de votre application web et sélectionnez **Ouvrir le portail de la version préliminaire Application Insights**.
 - Ou, dans un navigateur web, vous pouvez ouvrir votre compte dans la version préliminaire de Microsoft Azure.

2. Sélectionnez Parcourir, Application Insights, puis votre projet.

## <a name="q16"></a>Il existe un pare-feu entre mon serveur ou ma machine de développement et l'Internet public. Quel trafic dois-je autoriser pour activer Application Insights ?

Les données de performances et d'utilisation sont envoyées vers les ports TCP 80 et 443 à dc.services.visualstudio.com et f5.services.visualstudio.com.

Les tests de disponibilité web dépendent de l'accès entrant à votre serveur web sur le port 80.

## <a name="q17"></a> Comment savoir si j'ai activé tout ce qu'il faut pour utiliser Application Insights ?

<table border="1">
<tr><th>Ce que vous devez voir</th><th>Comment l'obtenir</th><th>Pourquoi le vouloir</th></tr>
<tr><td>Graphiques de disponibilité</td><td><a href="../app-insights-monitor-web-app-availability/">Web tests</a></td><td>Vérifiez la disponibilité de votre application web</td></tr>
<tr><td>Performances de l'application de serveur : temps de réponse, ...
</td><td><a href="../app-insights-start-monitoring-app-health-usage/">Ajoutez Application Insights à votre projet</a><br/>ou <br/><a href="../app-insights-monitor-performance-live-website-now/">Installez AI Status Monitor sur le serveur</a></td><td>Détection des problèmes de performances</td></tr>
<!-- ####future#### <tr><td>Télémétrie de dépendance</td><td><a href="../app-insights-monitor-performance-live-website-now/">Installez AI Status Monitor sur le serveur</a></td><td>Diagnostic de problèmes rencontrés au niveau des bases de données ou des composants externes</td></tr> -->
<!-- #####74.1#### <tr><td>Variables globales du serveur : Processeur, mémoire, ...</td><td><a href="../app-insights-monitor-performance-live-website-now/">Installez AI Status Monitor sur le server</a></td><td>Diagnostic des problèmes de capacité</td></tr> --> 
<tr><td>Recherches de suivis de journalisation</td><td><a href="../app-insights-search-diagnostic-logs/">Ajout d'un enregistreur de données</a></td><td>Diagnostic des exceptions et des problèmes de performances</td></tr>
<tr><td>Principes de base de l'utilisation client : Affiches des pages, renvois, ...</td><td><a href="../app-insights-start-monitoring-app-health-usage/#webclient">Programme d'initialisation JavaScript dans les pages web</a></td><td>Analyses de l'utilisation</td></tr>
<tr><td>Métriques personnalisées du client</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Suivi des appels dans les pages web</a></td><td>Amélioration de l'expérience utilisateur</td></tr>
<tr><td>Métriques personnalisées du serveur</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Suivi des appels dans le code du serveur</a></td><td>Business intelligence</td></tr>
</table>

Si votre service web est exécuté dans une machine virtuelle Azure, vous pouvez également [obtenir des diagnostics][azurediagnostic] ici.



[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]




[azurediagnostic]: ../insights-how-to-use-diagnostics/

[older]: http://www.visualstudio.com/get-started/get-usage-data-vs


<!--HONumber=46--> 
 