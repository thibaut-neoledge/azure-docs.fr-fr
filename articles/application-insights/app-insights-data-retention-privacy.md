<properties 
	pageTitle="Rétention des données et stockage dans Application Insights" 
	description="Retention and privacy policy statement" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/11/2015" 
	ms.author="awills"/>

# Collecte, rétention et stockage des données dans Application Insights 

*Application Insights est à l'état de version préliminaire.*

## Vue d'ensemble

Cet article répond à certaines questions sur les données collectées par [Visual Studio Application Insights][start] et sur la façon dont elles sont traitées et stockées.

Application Insights est un service Azure en version préliminaire. Tant qu’il s’agit de la version préliminaire, nous travaillons à mettre en place la protection de vos données conformément aux stratégies décrites dans le livre blanc relatif à [la sécurité, la confidentialité et la conformité Azure](http://go.microsoft.com/fwlink/?linkid=392408).


## Collection

#### Comment les données sont-elles collectées par Application Insights ?

Les Kits de développement logiciel (SDK) d’Application Insights et les agents associés à votre application envoient des données au service Application Insights. Ces données sont traitées par le service pour vous fournir des rapports, des alertes, etc. Il peut s’agir de données que vous choisissez de capturer à l’aide de l’API, par exemple dans les propriétés et les événements personnalisés.

#### Quelle est la quantité de données qui peut être capturée ? 

**Par seconde** : jusqu’à 500 points de données de télémétrie par seconde par clé d’instrumentation (autrement dit, par application). Pour le[niveau de tarification][pricing] gratuit, la limite est de 100 pd/s.

**Par mois** : entre 5 et 15 millions de points de données par mois, selon votre [plan de tarification](http://azure.microsoft.com/pricing/details/application-insights/). À l'exception du [niveau de tarification][pricing] gratuit, vous pouvez acheter une capacité supplémentaire si vous avez atteint la limite.


Un *point de données* est un élément de télémétrie, par exemple :

* Appels API `Track...`, comme `TrackEvent` ou `trackPageView`.
* Éléments de télémétrie envoyés par les modules du Kit de développement logiciel (SDK), par exemple pour signaler une demande ou une panne.
* Données de compteur de performances - un point pour chaque mesure.

*Comment savoir combien de points de données envoie mon application ?*

* Ouvrez Paramètres/Quota et tarification pour découvrir le graphique du volume de données.
* Ou dans Metrics Explorer, ajoutez un nouveau graphique et sélectionnez la mesure **Volume du point de données**. Basculez sur le regroupement et regroupez par **Type de données**.


#### Combien de temps sont conservées les données ? 

Cela dépend de votre [plan de tarification](http://azure.microsoft.com/pricing/details/application-insights/).

Les points de données bruts (autrement dit, les éléments que vous pouvez inspecter dans Recherche de diagnostic) : entre 7 et 30 jours.

Les données agrégées (autrement dit, les nombres, moyennes et autres données statistiques que vous voyez dans Metrics Explorer) sont conservées pour une minute pendant 30 jours et pour une heure ou un jour (selon le type) pendant 13 mois.

#### Quelles sont les limites sur les différents types de données ?

1.	Un maximum de 200 noms de mesure uniques et de 200 noms de propriété unique pour votre application. Les mesures comprennent l’envoi de données via TrackMetric ainsi que des mesures sur d’autres types de données tels que des événements. Les [noms de mesure et de propriété][api] sont globaux pour chaque clé d’instrumentation et ne s’étendent pas au type de données.
2.	Les [propriétés][apiproperties] peuvent être utilisées pour le filtrage et le regroupement uniquement lorsqu’il y a moins de 100 valeurs uniques pour chaque propriété. Lorsque les valeurs uniques dépassent 100, la propriété peut toujours être utilisée pour effectuer une recherche et un filtrage, mais elle ne peut plus être utilisée pour des filtres.
3.	Les propriétés standard telles que le nom de la requête et l’URL de la page sont limitées à 1 000 valeurs uniques par semaine. Au-delà de 1 000 valeurs uniques, les valeurs supplémentaires sont marquées comme « Autres valeurs ». La valeur d’origine peut toujours être utilisée pour une recherche de texte intégral et pour le filtrage.


## Access

#### Qui peut voir les données ?

Les données sont visibles par vous et, si vous disposez un compte d’organisation, par les membres de votre équipe.

Vous ou les membres de votre équipe pouvez les exporter et les copier à d’autres endroits, ou les transférer à d’autres personnes.

#### Que fait Microsoft des informations que mon application envoie à Application Insights ?

Microsoft n’utilise les données que pour vous fournir le service.


## Emplacement

#### Où sont conservées les données ? 

* Aux États-Unis d'Amérique. 

#### Peuvent-elles être stockées ailleurs, par exemple en Europe ? 

* Pas encore. 

## Sécurité 

#### Mes données sont-elles sécurisées ? 

Les données sont stockées sur des serveurs Microsoft Azure. Pour les comptes du portail Azure en version préliminaire, les restrictions correspondantes sont décrites dans le [document relatif à la sécurité, la confidentialité et la conformité Azure.](http://go.microsoft.com/fwlink/?linkid=392408) Pour les comptes du portail Visual Studio Online, le document relatif à la [protection des données Visual Studio Online](http://download.microsoft.com/download/8/E/E/8EE6A61C-44C2-4F81-B870-A267F1DF978C/MicrosoftVisualStudioOnlineDataProtection.pdf) s’applique.

L'accès à vos données par le personnel Microsoft est limité. Nous n'accédons à vos données qu'avec votre autorisation, et seulement pour vous aider à utiliser Application Insights.

Les données agrégées sur toutes les applications de nos clients (comme le débit des données et la taille moyenne des suivis) sont utilisées pour améliorer Application Insights.

#### Les données de télémétrie d’une autre personne peuvent-elles interférer avec mes données d’Application Insights ?

Elles peuvent envoyer des données de télémétrie supplémentaires à votre compte à l’aide de la clé d’instrumentation, qui se trouve dans le code de vos pages web. Avec suffisamment de données supplémentaires, vos mesures ne représentent pas correctement les performances et l’utilisation de votre application.

Si vous partagez le code avec d’autres projets, pensez à supprimer votre clé d’instrumentation.

## Chiffrement

#### Les données des serveurs Application Insights sont-elles chiffrées ? 

Pas sur les serveurs à l’heure actuelle.

Toutes les données sont chiffrées lors de leurs déplacements entre les centres de données.

#### Les données sont-elles chiffrées lors de leur passage depuis mon application vers les serveurs Application Insights ?

Oui, nous utilisons le protocole HTTPS pour envoyer les données au portail à partir de presque tous les Kits de développement logiciel (SDK), y compris les serveurs web, les appareils et les pages web HTTPS. La seule exception concerne les données envoyées à partir des pages web HTTP.

## Informations d’identification personnelle

#### Est-ce que des informations d'identification personnelle peuvent être envoyées à Application Insights ? 

Oui.

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
[Ajout d’un Kit de développement logiciel (SDK) JavaScript à une page web][client]|ClientContext <br/>Inferred<br/>Page<br/>ClientPerf
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
Demandes |URL, durée, code de réponse
Dépendances|Type (SQL, HTTP, ...), chaîne de connexion ou URI, synchronisation/désynchronisation, durée, réussite, instruction SQL (avec Status Monitor)
**Exceptions** | Type, **message**, piles d’appels, fichier source et numéro ligne, ID du thread
Crashes | ID de processus, ID de processus parent, ID de thread d’incident ; correctif de l’application, ID, version ; type d’exception, adresse, motif ; symboles et enregistrements masqués, adresses binaires de début et de fin, nom et chemin du fichier binaire, type de processeur
Trace | **Message** et niveau de gravité
Perf counters | Temps processeur, mémoire disponible, taux de demande, taux d’exception, octets privés du processus, taux d’E/S, durée de la demande, longueur de file d’attente de la demande
Availability | Code de réponse de test web, durée de chaque étape de test
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
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=62-->