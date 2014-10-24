<properties title="Search diagnostic logs with Application Insights" pageTitle="Search diagnostic logs" description="Search logs generated with Trace, NLog, or Log4Net." metaKeywords="analytics web test" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# Recherche dans les journaux de diagnostics avec Application Insights

Vous pouvez capturer les données de diagnostic de System.Diagnostics.Trace, NLog et Log4Net et y effectuer des recherches. Application Insights constitue un outil efficace et ergonomique pour la collecte et l'examen des événements enregistrés depuis une ou plusieurs sources : cette possibilité complète les fonctions de surveillance de l'intégrité des applications.

L'application web surveillée peut être hébergée localement ou sur une machine virtuelle ; il peut s'agir également d'un site web Microsoft Azure.

1.  [Ajout d'un enregistreur de données][Ajout d'un enregistreur de données]
-   [Configuration de la collecte des diagnostics][Configuration de la collecte des diagnostics]
-   [Insertion d'instructions de journalisation, génération et déploiement][Insertion d'instructions de journalisation, génération et déploiement]
-   [Affichage de données de journal][Affichage de données de journal]
-   [Recherche dans les données][Recherche dans les données]
-   [Étapes suivantes][Étapes suivantes]

## <a name="add"></a>1. Ajout d'un enregistreur de données

1.  Si vous ne l'avez pas encore fait, [ajoutez Application Insights à votre projet de service web][ajoutez Application Insights à votre projet de service web] dans Visual Studio.

    Si vous ajoutez Application Insights après avoir ajouté la journalisation à votre projet, vous constatez que l'enregistreur de données est déjà configuré. Il vous suffit de [redéployer votre projet][Insertion d'instructions de journalisation, génération et déploiement] et [d'afficher vos données][Affichage de données de journal].

2.  Dans l'Explorateur de solutions, dans le menu contextuel de votre projet, sélectionnez **Gérer les packages NuGet**.
3.  Sélectionnez En ligne \> Tout, puis **Inclure la version préliminaire** et recherchez « Microsoft.ApplicationInsights »

    ![Get the prerelease version of the appropriate adapter][Get the prerelease version of the appropriate adapter]

4.  Sélectionnez la version préliminaire du package correspondant parmi :

	+   Microsoft.ApplicationInsights.TraceListener
	-   Microsoft.ApplicationInsights.NLogTarget
	-   Microsoft.ApplicationInsights.Log4NetAppender

## <a name="configure"></a>2. Configuration de la collecte des diagnostics

### Pour System.Diagnostics.Trace

Dans Web.config, insérez le code suivant dans la section `<configuration>` :

    <system.diagnostics>
     <trace autoflush="true" indentsize="0">
      <listeners>
       <add name="myAppInsightsListener"  
          type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, 
         Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
     </trace>
    </system.diagnostics> 

### Pour NLog

Dans Nlog.config, fusionnez les extraits de code suivants dans les sections `<extensions>`, `<targets>` et `<rules>`. Créez ces sections, le cas échéant.

    <extensions> 
     <add  assembly="Microsoft.ApplicationInsights.NLogTarget" /> 
    </extensions> 

    <targets>
     <target xsi:type="ApplicationInsightsTarget" name="aiTarget"/>
    </targets>

    <rules>
     <logger name="*" minlevel="Trace" writeTo="aiTarget"/>
    </rules>

### Pour Log4Net

Dans Web.config, fusionnez ces extraits de code dans les sections `<configsections>` et `<log4net>` :

    <configSections>
      <section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler, log4net" />
    </configSections>

    <log4net>
     <root>
      <level value="ALL" /> <appender-ref ref="aiAppender" />
     </root>
     <appender name="aiAppender" type="Microsoft.ApplicationInsights.Log4NetAppender.ApplicationInsightsAppender, Microsoft.ApplicationInsights.Log4NetAppender">
      <layout type="log4net.Layout.PatternLayout">
       <conversionPattern value="%date [%thread] %-5level %logger - %message%newline" />
      </layout>
     </appender>
    </log4net>

## <a name="deploy"></a>3. Insertion d'instructions de journalisation, génération et déploiement

Insérez les appels de journalisation des événements dans l'infrastructure de journalisation de votre choix. Exemple : si vous utilisez Log4Net, vous pouvez avoir des appels tels que celui-ci :

    log.Warn("Slow response - database01");

Les événements enregistrés sont envoyés à Application Insights en développement et en fonctionnement.

## <a name="view"></a>4. Affichage de données de journal

Dans Application Insights, ouvrez la recherche de diagnostic.

![Open diagnostic search][Open diagnostic search]

Sélectionnez un événement enregistré pour afficher les informations détaillées associées.

![Open diagnostic search][1]

Les champs disponibles dépendent de l'infrastructure de journalisation et des paramètres utilisés dans l'appel.

Vous pouvez utiliser des chaînes de caractères standard (sans caractère générique) pour filtrer les données des champs d'un élément.

## <a name="search"></a>5. Recherche dans les données

Définissez une période et les termes à rechercher. Les recherches sur une courte période sont plus rapides.

![Open diagnostic search][2]

Notez que vous pouvez rechercher des termes, mais pas des sous-chaînes. Les termes sont des chaînes alphanumériques comprenant des signes de ponctuation (comme « . » et « \_ »). Par exemple :

<table>
  <tr><th>terme</th><th>NON trouv&eacute; en recherchant</th><th>mais en recherchant</th></tr>
  <tr><td>HomeController.About</td><td>about<br/>home</td><td>h*about<br/>home*</td></tr>
  <tr><td>IsLocal</td><td>local<br/>is<br/>*local</td><td>isl*<br/>islocal<br/>i*l</td></tr>
  <tr><td>New Delay</td><td>w d</td><td>new<br/>delay<br/>n* AND d*</td></tr>
</table>

Expressions de recherche utilisables :

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><p>Exemple de requête</p></th>
<th align="left"><p>Résultat</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><span class="code">slow</span></p></td>
<td align="left"><p>Trouve tous les événements dont la période comprend le terme « slow »</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">database??</span></p></td>
<td align="left"><p>Trouve database01, databaseAB, ...</p>
<p>? n'est pas autorisé au début du terme à rechercher.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><span class="code">database*</span></p></td>
<td align="left"><p>Trouve database, database01, databaseNNNN</p>
<p>* n'est pas autorisé au début du terme à rechercher</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">apple AND banana</span></p></td>
<td align="left"><p>Trouve les événements qui contiennent les deux termes. Utilisez « AND » en lettres majuscules (et non « and » en lettres minuscules).</p></td>
</tr>
<tr class="odd">
<td align="left"><p><span class="code">apple OR banana</span></p>
<p><span class="code">apple banana</span></p></td>
<td align="left"><p>Trouve les événements qui contiennent un des deux termes. Utilisez « OR » en lettres capitales (et non « or » en lettres minuscules).</p>
<p>Forme abrégée.</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">apple NOT banana</span></p>
<p><span class="code">apple -banana</span></p></td>
<td align="left"><p>Trouve les événements qui contiennent un terme mais pas l'autre.</p>
<p>Forme abrégée.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>app* AND banana NOT (grape OR pear)</p>
<p><span class="code">app* AND banana -(grape pear)</span></p></td>
<td align="left"><p>Opérateurs logiques et utilisation des parenthèses.</p>
<p>Forme abrégée.</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">message:slow</span></p>
<p><span class="code">ipaddress:(10.0.0.* OR 192.168.0.*)</span></p>
<p><span class="code">properties.logEventInfo.level:Error</span></p></td>
<td align="left"><p>Trouve le champ spécifié. Par défaut, une recherche est effectuée dans tous les champs. Pour connaître les champs disponibles, sélectionnez un événement et examinez ses informations en détail.</p></td>
</tr>
</tbody>
</table>

## <a name="add"></a>Étapes suivantes

-   [Ajout de Application Insights à un projet][ajoutez Application Insights à votre projet de service web]
-   [Configuration des tests de disponibilité et de réactivité][Configuration des tests de disponibilité et de réactivité]
-   [Résolution des problèmes][Résolution des problèmes]

## En savoir plus

-   [Application Insights][Application Insights]
-   [Ajout de Application Insights à votre projet][ajoutez Application Insights à votre projet de service web]
-   [Surveillance d'un serveur web en activité][Surveillance d'un serveur web en activité]
-   [Exploration des mesures dans Application Insights][Exploration des mesures dans Application Insights]
-   [Recherche dans un journal de diagnostics][Recherche dans un journal de diagnostics]
-   [Suivi de la disponibilité avec des tests web][Configuration des tests de disponibilité et de réactivité]
-   [Suivi de l'utilisation avec des événements et des mesures][Suivi de l'utilisation avec des événements et des mesures]
-   [Questions et réponses - Résolution des problèmes][Résolution des problèmes]

<!--Link references-->

  [Ajout d'un enregistreur de données]: #add
  [Configuration de la collecte des diagnostics]: #configure
  [Insertion d'instructions de journalisation, génération et déploiement]: #deploy
  [Affichage de données de journal]: #view
  [Recherche dans les données]: #search
  [Étapes suivantes]: #next
  [ajoutez Application Insights à votre projet de service web]: ../app-insights-monitor-application-health-usage/
  [Get the prerelease version of the appropriate adapter]: ./media/appinsights/appinsights-36nuget.png
  [Open diagnostic search]: ./media/appinsights/appinsights-30openDiagnostics.png
  [1]: ./media/appinsights/appinsights-32detail.png
  [2]: ./media/appinsights/appinsights-31search.png
  [Configuration des tests de disponibilité et de réactivité]: ../app-insights-monitor-web-app-availability/
  [Résolution des problèmes]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Surveillance d'un serveur web en activité]: ../app-insights-monitor-performance-live-website-now/
  [Exploration des mesures dans Application Insights]: ../app-insights-explore-metrics/
  [Recherche dans un journal de diagnostics]: ../app-insights-search-diagnostic-logs/
  [Suivi de l'utilisation avec des événements et des mesures]: ../app-insights-track-usage-custom-events-metrics/
