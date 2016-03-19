<properties 
	pageTitle="Rétention des données et stockage dans Application Insights" 
	description="Retention and privacy policy statement" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/17/2015" 
	ms.author="awills"/>

# Collecte, rétention et stockage des données dans Application Insights 

*Application Insights est à l'état de version préliminaire.*

Lorsque vous installez le Kit de développement logiciel (SDK) [Visual Studio Application Insights][start] dans votre application, il envoie la télémétrie de votre application dans le Cloud. Bien-sûr, les développeurs responsables veulent savoir exactement quelles données sont envoyées, ce qu’elles deviennent et comment ils peuvent conserver le contrôle. Ils souhaitent savoir, plus particulièrement, si les données sensibles peuvent être envoyées, où elles sont stockées et à quel point elles sont sécurisées ?


Tout d’abord, la réponse courte :

* Les modules de télémétrie standard qui s’exécutent « dès le départ » sont peu susceptibles d’envoyer des données sensibles au service. La télémétrie s’attache aux métriques de charge, de performance et d’utilisation, aux rapports d’exception et autres données de diagnostic. Les données d’utilisateur principal visibles dans les rapports de diagnostics sont des URL ; mais votre application ne doit en aucun cas mettre des données sensibles en texte brut dans une URL.
* Vous pouvez écrire du code qui envoie les données de télémétrie personnalisées supplémentaires, afin de vous aider avec les diagnostics et à surveiller l’utilisation. (Cette extensibilité est une fonctionnalité intéressante de Application Insights.) Il serait possible, par erreur, d’écrire ce code de manière à ce qu’il inclue les données personnelles et d’autres données sensibles. Si votre application fonctionne avec ces données, vous devez appliquer des processus de révision stricts à l’ensemble du code que vous écrivez.
* Pendant le développement et le test de votre application, il est facile d’examiner ce qui est envoyé par le Kit de développement logiciel (SDK). Les données apparaissent dans les fenêtres de sortie de débogage de l’IDE et du navigateur. 
* Les données sont stockées sur des serveurs [Microsoft Azure](http://azure.com) aux États-Unis. Azure dispose de [processus de sécurité renforcés, il est conforme à une large gamme de normes de conformité](https://azure.microsoft.com/support/trust-center/). Seuls vous et votre équipe avez accès à vos données. Le personnel Microsoft peut avoir un accès restreint uniquement dans certaines circonstances définies, avec votre consentement. Il est chiffré en transit, mais pas dans les serveurs.

Le reste de cet article aborde plus en détail ces réponses. Il est conçu pour être autonome, afin que vous puissiez le montrer à des collègues qui ne font pas partie de votre équipe.


## Présentation d’Application Insights

[Visual Studio Application Insights][start] est un service fourni par Microsoft pour vous aider à améliorer les performances et la convivialité de votre application en direct. Il analyse votre application tout au long de son exécution, pendant les tests et une fois que vous avez l’avez publiée ou déployée. Application Insights crée des graphiques et tableaux présentant, par exemple, les heures de la journée à laquelle vous avez le plus d’utilisateurs, la réactivité de l’application et comment elle est prise en charge par les services externes dont elle dépend. En cas d’incidents, d’échecs ou de problèmes de performances, vous pouvez effectuer une recherche dans les données de télémétrie pour diagnostiquer la cause en détail. Et le service vous enverra des messages électroniques si des modifications sont apportées à la disponibilité et à la performance de votre application.

Pour obtenir cette fonctionnalité, vous devez installer un Kit de développement logiciel (SDK) de Application Insights dans votre application, il devient partie intégrante de son code. Lorsque votre application s’exécute, le Kit de développement logiciel (SDK) surveille ses opérations et envoie les données de télémétrie au service Application Insights. Il s’agit d’un service cloud hébergé par [Microsoft Azure](http://azure.com). (Mais Application Insights fonctionne pour toutes les applications, pas uniquement celles qui sont hébergées dans Azure.)

![Le Kit de développement logiciel (SDK) dans votre application envoie la télémétrie au service Application Insights.](./media/app-insights-data-retention-privacy/01-scheme.png)

Le service Application Insights stocke et analyse les données de télémétrie. Pour afficher l’analyse ou effectuer une recherche dans la télémétrie stockée, connectez-vous à votre compte Azure et ouvrez la ressource Application Insights pour votre application. Vous pouvez également partager l’accès aux données avec d’autres membres de votre équipe, ou avec des abonnés Azure spécifiés.

Vous pouvez exporter des données à partir du service Application Insights vers une base de données ou des outils externes, par exemple. Vous devez attribuer une clé spéciale, que vous obtenez de la part du service, à chaque outil. La clé peut être révoquée si nécessaire.

Les Kits de développement logiciel (SDK) d’Application Insights sont disponibles pour une variété d’applications : les services web hébergés dans vos propres serveurs J2EE ou ASP.NET ou dans Azure ; les clients web, autrement dit, le code s’exécutant dans une page web ; les applications et les services de bureau ; les applications pour appareils comme Windows Phone, iOS et Android. Toutes envoient les données de télémétrie vers le même service.

## Quelles données collecte-t-il ?

### Comment les données sont-elles collectées ?

Il existe trois sources de données :

* Le Kit de développement logiciel (SDK), que vous intégrez à votre application soit [pendant le développement](app-insights-asp-net.md), soit [au moment de l’exécution](app-insights-monitor-performance-live-website-now.md). Il existe différents Kits de développement logiciel (SDK) pour différents types d’applications. Il existe également un [Kit de développement logiciel (SDK) pour les pages web](app-insights-javascript.md), qui se charge dans le navigateur de l’utilisateur final et sur la page.

 * Chaque Kit de développement logiciel (SDK) comporte un certain nombre de [modules](app-insights-configuration-with-applicationinsights-config.md), utilisant diverses techniques pour collecter différents types de données de télémétrie.
 * Si vous installez le Kit de développement logiciel (SDK) pendant le développement, vous pouvez utiliser ses API pour envoyer votre propre télémétrie, ainsi que les modules standards. Ces données de télémétrie personnalisées peuvent inclure toutes les données que vous souhaitez envoyer.
* Sur certains serveurs web, il existe également des agents qui s’exécutent en même temps que l’application et qui envoient des données de télémétrie concernant l’UC, la mémoire et l’occupation du réseau. Par exemple, les machines virtuelles Azure, les hôtes Docker, et [les serveurs J2EE ](app-insights-java-agent.md) peuvent disposer de ces agents.
* [Les tests de disponibilité](app-insights-monitor-web-app-availability.md) sont des processus exécutés par Microsoft qui envoient des requêtes à votre application web à intervalles réguliers. Les résultats sont envoyés au service Application Insights.

### Quel genre de données est collecté ?

Les principales catégories sont :

* [La télémétrie du serveur web](app-insights-asp-net.md) : requêtes HTTP. L’URI, le temps nécessaire pour traiter la demande, le code de réponse, l’adresse IP du client. L’Id de session.
* [Les pages web](app-insights-javascript.md) : page, utilisateur et décomptes de sessions. Le temps de chargement de page. Les exceptions. Appels Ajax.
* Les performances des compteurs : mémoire, UC, E/S, occupation du réseau.
* Le contexte du client et du serveur : système d’exploitation, paramètres régionaux, type d’appareil, navigateur, résolution d’écran.
* [Les exceptions](app-insights-asp-net-exceptions.md) et les incidents : **vidages de pile**, génération d’id, type d’UC. 
* [Les dépendances](app-insights-asp-net-dependencies.md) : les appels aux services externes tels que REST, SQL, AJAX. L’URI ou la chaîne de connexion, la durée, la réussite, la commande.
* [Les tests de disponibilité](app-insights-monitor-web-app-availability.md) : durée et étapes du test, réponses.
* [Les journaux de suivi](app-insights-search-diagnostic-logs.md) et [la télémétrie personnalisée](app-insights-api-custom-events-metrics.md) : **tout ce que vous codez dans vos journaux ou télémétrie**.

[Plus de détails](#data-sent-by-application-insights).

## Comment puis-je vérifier ce qui a été collecté ?

Si vous développez l’application à l’aide de Visual Studio, exécutez l’application en mode débogage (F5). Les données de télémétrie s’affichent dans la fenêtre Sortie. À partir de là, vous pouvez les copier et les mettre au format JSON pour une inspection facile.

![](./media/app-insights-data-retention-privacy/06-vs.png)

Pour les pages web, ouvrez la fenêtre de débogage de votre navigateur.

![Appuyez sur F12 et ouvrez l’onglet Réseau.](./media/app-insights-data-retention-privacy/08-browser.png)

### Puis-je écrire le code pour filtrer les données de télémétrie avant qu’elles soient envoyées ?

Cela est possible en écrivant un [plug-in de processeur de télémétrie](app-insights-api-filtering-sampling.md).



## Combien de temps sont conservées les données ? 

Cela dépend de votre [plan de tarification](https://azure.microsoft.com/pricing/details/application-insights/).

Les points de données bruts (autrement dit, les éléments que vous pouvez inspecter dans Recherche de diagnostic) : entre 7 et 30 jours.

Les données agrégées (autrement dit, les nombres, moyennes et autres données statistiques que vous voyez dans Metrics Explorer) sont conservées pour une minute pendant 30 jours et pour une heure ou un jour (selon le type) pendant 13 mois.


## Qui peut accéder aux données ?

Les données sont visibles par vous et, si vous disposez un compte d’organisation, par les membres de votre équipe.

Vous ou les membres de votre équipe pouvez les exporter et les copier à d’autres endroits, ou les transférer à d’autres personnes.

#### Que fait Microsoft des informations que mon application envoie à Application Insights ?

Microsoft n’utilise les données que pour vous fournir le service.


## Où sont conservées les données ? 

* Aux États-Unis d'Amérique. 

#### Peuvent-elles être stockées ailleurs, par exemple en Europe ? 

* Pas encore. 

## Mes données sont-elles sécurisées ?  

Application Insights est un service Azure en version préliminaire. Tant qu’il s’agit de la version préliminaire, nous travaillons à mettre en place la protection de vos données conformément aux stratégies décrites dans le livre blanc relatif à [la sécurité, la confidentialité et la conformité Azure](http://go.microsoft.com/fwlink/?linkid=392408).


Les données sont stockées sur des serveurs Microsoft Azure. Pour les comptes du portail Azure, les restrictions sont décrites dans le [document relatif à la sécurité, la confidentialité et la conformité Azure](http://go.microsoft.com/fwlink/?linkid=392408). Pour les comptes du portail Visual Studio Team Services, le document relatif à [la protection des données dans Visual Studio Team Services](http://download.microsoft.com/download/8/E/E/8EE6A61C-44C2-4F81-B870-A267F1DF978C/MicrosoftVisualStudioOnlineDataProtection.pdf) s’applique.

L'accès à vos données par le personnel Microsoft est limité. Nous n'accédons à vos données qu'avec votre autorisation, et seulement pour vous aider à utiliser Application Insights.

Les données agrégées sur toutes les applications de nos clients (comme le débit des données et la taille moyenne des suivis) sont utilisées pour améliorer Application Insights.

#### Les données de télémétrie d’une autre personne peuvent-elles interférer avec mes données d’Application Insights ?

Elles peuvent envoyer des données de télémétrie supplémentaires à votre compte à l’aide de la clé d’instrumentation, qui se trouve dans le code de vos pages web. Avec suffisamment de données supplémentaires, vos mesures ne représentent pas correctement les performances et l’utilisation de votre application.

Si vous partagez le code avec d’autres projets, pensez à supprimer votre clé d’instrumentation.

## Les données sont-elles chiffrées ? 

Pas sur les serveurs à l’heure actuelle.

Toutes les données sont chiffrées lors de leurs déplacements entre les centres de données.

#### Les données sont-elles chiffrées lors de leur passage depuis mon application vers les serveurs Application Insights ?

Oui, nous utilisons le protocole HTTPS pour envoyer les données au portail à partir de presque tous les Kits de développement logiciel (SDK), y compris les serveurs web, les appareils et les pages web HTTPS. La seule exception concerne les données envoyées à partir des pages web HTTP.

## Informations d’identification personnelle

#### Est-ce que des informations d'identification personnelle peuvent être envoyées à Application Insights ? 

Oui, c’est possible.

En règle générale :

* La plupart des données de télémétrie standard (à savoir toute télémétrie envoyée sans que n’ayez écrit de code) ne comprennent pas d’informations explicites. Toutefois, il est parfois possible d’identifier les personnes par déduction à partir d’un ensemble d’événements.
* Les messages d’exception et les messages de suivi peuvent contenir des informations personnelles
* Les données de télémétrie personnalisée, autrement dit les appels comme TrackEvent que vous écrivez dans le code à l’aide des API ou du suivi du journal, peuvent contenir toutes les données que vous voulez.


Le tableau à la fin de ce document contient une description plus détaillée des données collectées.



#### Suis-je tenu de me conformer aux lois et règlements en ce qui concerne les informations d’identification personnelle ?

Oui. Il vous incombe de garantir que la collecte et l’utilisation des données sont conformes aux lois et réglementations et aux termes du contrat Microsoft Online Services.

Vous devez correctement informer vos clients sur les données collectées par votre application et la façon dont elles sont utilisées.

#### Mes utilisateurs peuvent-ils désactiver Application Insights ?

Pas directement. Il n’existe pas de commutateur que vos utilisateurs peuvent utiliser pour désactiver Application Insights.

Toutefois, vous pouvez implémenter cette fonctionnalité dans votre application. Tous les Kits de développement logiciel (SDK) comprennent un paramètre API qui désactive la collecte de télémétrie.

#### Mon application collecte involontairement des informations sensibles. Est-ce qu’Application Insights peut nettoyer ces données afin qu’elles ne soient pas conservées ?

Application Insights ne filtre pas les données et ne les supprime pas non plus. Vous devez gérer les données de façon appropriée et éviter d’envoyer ce type de données à Application Insights.



## Données envoyées par Application Insights

Les Kits de développement logiciel (SDK) varient en fonction des plateformes et vous pouvez installer plusieurs composants. (Consultez la page [Application Insights - Prise en main][start].) Chaque composant envoie des données différentes.

#### Classes de données envoyées dans différents scénarios

Votre action | Classes de données collectées (voir tableau suivant)
---|---
[Ajout du Kit de développement logiciel (SDK) Application Insights à un projet web .NET][greenbrown] | ServerContext<br/>Inferred<br/>Perf counters<br/>Requests<br/>**Exceptions**<br/>Session<br/>users
[Installation du moniteur d’état sur IIS][redfield]<br/>[Ajout de l’extension AI à une machine virtuelle ou application web Azure][azure]|Dependencies<br/>ServerContext<br/>Inferred<br/>Perf counters
[Ajout du Kit de développement logiciel (SDK) Application Insights à une application web Java][java]|ServerContext<br/>Inferred<br/>Request<br/>Session<br/>users
[Ajout d’un Kit de développement logiciel (SDK) JavaScript à une page web][client]|ClientContext <br/>Inferred<br/>Page<br/>ClientPerf<br/>Ajax
[Ajout du Kit de développement logiciel (SDK) à une application Windows Store][windows]|DeviceContext<br/>Users<br/>Crash data
[Définition des propriétés par défaut][apiproperties]|**Propriétés** sur tous les événements standard et personnalisés
[Appel TrackMetric][api]|Valeurs numériques<br/>**Propriétés**
[Appel Track*][api]|Nom de l’événement<br/>**Propriétés**
[Appel TrackException][api]|**Exceptions**<br/>Vidage de pile<br/>**Propriétés**
Le Kit de développement logiciel (SDK) ne peut pas collecter les données. Par exemple : <br/> - ne peut pas accéder aux compteurs de performances<br/> - exception dans l’initialiseur de télémétrie | SDK diagnostics
 

En ce qui concerne les [Kits de développement logiciel (SDK) des autres plateformes][platforms], consultez les documents correspondants.



#### Les classes de données collectées

Classe des données collectées | Comprend (liste non exhaustive) 
---|---
**Propriétés**|**Toutes les données - en fonction de votre code**
DeviceContext |ID, adresse IP, paramètres régionaux, modèle d’appareil, réseau, type de réseau, nom OEM, résolution d’écran, instance de rôle, nom du rôle, type d’appareil
ClientContext |Système d’exploitation, paramètres régionaux, langue, réseau, résolution de la fenêtre
Session | ID de session
ServerContext |Nom de l’ordinateur, paramètres régionaux, système d’exploitation, appareil, session utilisateur, contexte utilisateur, opération 
Inferred |Emplacement géographique à partir de l’adresse IP, horodatage, système d’exploitation, navigateur
Mesures | Valeur et nom de la mesure
Événements | Valeur et nom de l’événement
PageViews | URL et nom de la page ou de l’écran
Client perf | URL/nom de la page, temps de chargement du navigateur
Ajax | Appels HTTP de la page web au serveur
Demandes |URL, durée, code de réponse
Dépendances|Type (SQL, HTTP, ...), chaîne de connexion ou URI, synchronisation/désynchronisation, durée, réussite, instruction SQL (avec Status Monitor)
**Exceptions** | Type, **message**, piles d’appels, fichier source et numéro ligne, ID du thread
Crashes | ID de processus, ID de processus parent, ID de thread d’incident ; correctif de l’application, ID, version ; type d’exception, adresse, motif ; symboles et enregistrements masqués, adresses binaires de début et de fin, nom et chemin du fichier binaire, type de processeur
Trace | **Message** et niveau de gravité
Perf counters | Temps processeur, mémoire disponible, taux de demande, taux d’exception, octets privés du processus, taux d’E/S, durée de la demande, longueur de file d’attente de la demande
Availability | Code de réponse de test web, durée de chaque étape de test, nom de test, horodatage, réussite, temps de réponse, emplacement de test
SDK diagnostics | Message de suivi ou exception 

Vous pouvez [désactiver certaines données en modifiant ApplicationInsights.config][config]


## Remerciements

Ce produit contient des données GeoLite2 créées par MaxMind, disponible sur [http://www.maxmind.com](http://www.maxmind.com).

## <a name="video"></a>Vidéos

#### Introduction

> [AZURE.VIDEO application-insights-introduction]

#### Prise en main

> [AZURE.VIDEO getting-started-with-application-insights]




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0211_2016-->