<properties title="Data retention and storage in Application Insights" pageTitle="Rétention des données et stockage dans Application Insights" description="Déclaration de stratégie de rétention et de confidentialité" metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-11-14" ms.author="awills" />

# Rétention des données et stockage dans Application Insights 

*Application Insights est à l'état de version préliminaire.*


Cet article répond aux questions relatives à la façon dont nous stockons la télémétrie que votre application envoie à Application Insights.

Application Insights stocke des données sur des serveurs Microsoft Azure aux États-Unis d'Amérique. Pour plus d'informations sur la politique relative à Microsoft Azure, téléchargez le [livre blanc sur la sécurité, la confidentialité et la conformité](http://go.microsoft.com/fwlink/?LinkId=392408). Certains détails spécifiques à Application Insights sont traités ci-dessous. 

#### Quelle est la quantité de données stockée ? 

Tant qu'Application Insights est au stade de la version préliminaire gratuite, nous stockons pour chaque compte : 

* Jusqu'à 500 messages de télémétrie par seconde (ou 30 Ko par minute). 

* Jusqu'à 10 Mo d'affichages de pages ou d'événements par mois. 

*Que se passe-t-il si je dépasse les limites ?* 

* Au-delà d'un bref dépassement des limites, nous supprimons certains messages après les avoir comptabilisés. Vous ne les verrez pas dans la Recherche de diagnostic. Le décompte des événements sera correct. Les métriques moyennes sont basées sur les événements que nous conservons. Elles doivent être valides. 

* Au-delà de la limite mensuelle, les événements cessent d'être enregistrés. Ainsi, vos métriques affichent des zéros après un certain temps. 

####Combien de temps la télémétrie est-elle conservée ? 

* 7 jours pour les données d'instance visibles dans la Recherche de diagnostic. Affichages de pages individuels, événements, messages de journal, traces et exceptions. 

* 13 mois pour les données de synthèse visibles dans l'Explorateur de métriques. Les statistiques relatives aux métriques, événements et exceptions (nombres de types, échecs de fonctions, etc.) sont conservées avec une granularité d'une minute (ou moins) pendant 30 jours, et une granularité d'une heure pendant 13 mois. 

####Où sont conservées les données ? 

* Aux États-Unis d'Amérique. 

Pouvez-vous les stocker en Europe ou ailleurs ? 

* Pas encore. 

####Mes données de télémétrie sont-elles sécurisées ? 

Les données sont stockées sur des serveurs Microsoft Azure. Pour les comptes du portail Azure en version préliminaire, les restrictions correspondantes sont décrites dans le document relatif à la sécurité, la confidentialité et la conformité Azure. Pour les comptes du portail Visual Studio Online, le document relatif à la protection des données Visual Studio Online s'applique. 

L'accès à vos données par le personnel Microsoft est limité. Nous n'accédons à vos données qu'avec votre autorisation, et seulement pour vous aider à utiliser Application Insights. 

####Les données des serveurs Application Insights sont-elles chiffrées ? 

Pas à l'heure actuelle. 

####Est-ce que des informations d'identification personnelle peuvent être envoyées à Application Insights ? 

Oui. Il s'agit des informations d'identification personnelle envoyées à Application Insights par votre code dans la télémétrie personnalisée, ainsi que des informations d'identification personnelle incluses dans la télémétrie standard. La déclaration de confidentialité Azure s'applique à Application Insights. 

Les données peuvent être envoyées de plusieurs façons au portail. Elles peuvent ensuite s'afficher dans la Recherche de diagnostic. Les membres de votre organisation peuvent également exporter et télécharger les données. 

* Si vous installez le Moniteur d'état, ou si vous ajoutez Application Insights à votre projet, les traces de pile sont capturées quand des exceptions ou des alertes de performances se produisent. Cela peut inclure les données de paramètres réelles telles que les données SQL. 

* Si vous insérez des appels de télémétrie comme TrackEvent dans votre code, ou si vous capturez la journalisation des messages d'infrastructure, les propriétés peuvent inclure des informations d'identification personnelle. Les conditions de Microsoft Online Services s'appliquent également. Pour Application Insights, en particulier, vous devez respecter les lois et la réglementation applicables à la confidentialité et à la collecte des données. Vous devez déterminer si une notification ou toute autre forme de consentement est nécessaire avant de collecter des informations. 


####Quelle est la télémétrie envoyée quand j'utilise Application Insights ? 

Vous pouvez installer plusieurs composants. (Consultez [Application Insights - Prise en main][start].) 

<table>
<tr><th>Vos actions</th><th>Télémétrie envoyée au portail</th><th>Peut contenir des données sensibles ?</th></tr>
<tr><td><a href="../app-insights-start-monitoring-app-health-usage/">Ajouter Application Insights à votre projet web</a></td>
  <td>Métriques : nombre de requêtes serveur, délai de réponse du serveur</td>
  <td>Non</td></tr>
<tr><td></td>
  <td>Rapports d'exception du serveur</td><td>Les vidages de pile peuvent contenir des valeurs de paramètre</td></tr>
<tr><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Suivi des événements personnalisés et des mesures</a></td>
  <td>Événements avec des propriétés attachées par votre code</td>
  <td>Oui, si votre code envoie des informations d'identification personnelle dans les propriétés ou les titres</td></tr>
<tr><td><a href="../app-insights-search-diagnostic-logs/#trace">Journaliser et tracer la télémétrie</a></td><td>Journaliser et tracer les messages</td><td>Les messages de journalisation peuvent contenir des informations d'identification personnelle</td></tr>
<tr><td><a href="../app-insights-web-track-usage/">Insérer le script IA dans vos pages web</a></td>
  <td>Identificateur d'utilisateur et ID de compte anonymes</td><td>Non</td></tr>
<tr><td></td><td>Début et fin de session utilisateur anonyme</td><td>Non</td></tr>
<tr><td></td><td>URI de page, délais de chargement, minutage de session</td><td>Non</td></tr>
<tr><td><a href="../app-insights-monitor-performance-live-website-now/">Installer le Moniteur d'état sur votre serveur</a></td>
  <td>Appels de dépendance et durées</td>
  <td>Les données d'appel peuvent contenir des paramètres (par exemple, des champs SQL)</td></tr>
<tr><td></td><td>UC, mémoire, réseau et autres compteurs de ressources</td><td>Non</td></tr>
<tr><td><a href="../app-insights-monitor-web-app-availability/">Tests web</a></td><td>Disponibilité et temps de réponse depuis le web</td><td>Non</td></tr>
</table>

## <a name="video"></a>Vidéos

#### Introduction

> [AZURE.VIDEO application-insights-introduction]

#### Prise en main

> [AZURE.VIDEO getting-started-with-application-insights]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]



<!--HONumber=35.2-->
