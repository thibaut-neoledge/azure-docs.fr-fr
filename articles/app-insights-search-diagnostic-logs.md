<properties title="Search diagnostic logs with Application Insights" pageTitle="Recherche des journaux de diagnostic" description="Search logs generated with Trace, NLog, or Log4Net." metaKeywords="analytics web test" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />
 
# Recherche dans les journaux de diagnostics avec Application Insights

Une des méthodes de débogage les plus courantes consiste à insérer des lignes de code qui émettent un journal de suivi. Vous pouvez utiliser [Application Insights][start] pour capturer vos journaux de serveur web, les filtrer et y effectuer des recherches. Si vous utilisez déjà log4Net, NLog ou System.Diagnostics.Trace, vous pouvez capturer ces journaux avec notre adaptateur. Ou vous pouvez utiliser les méthodes TrackTrace et TrackException intégrés au Kit de développement logiciel (SDK) Application Insights.

Votre recherche peut également porter sur l'affichage standard d'une page et les événements de demandes utilisés pour développer les rapports d'[utilisation][usage] et de [performances][perf], ainsi que les [appels TrackEvent personnalisés][track] que vous avez écrits.


2. [Installation d'un adaptateur pour votre infrastructure de journalisation](#capture)
+ [Insertion d'appels de journaux de diagnostic](#pepper)
+ [Exceptions](#exceptions)
+ [Affichage de données de journal](#view)
+ [Recherche de données de journal](#search)
+ [Résolution des problèmes](#questions)
+ [Étapes suivantes](#next)



## <a name="capture"></a> Installation d'un adaptateur pour votre infrastructure de journalisation

Si vous ne l'avez pas encore fait, [installez Application Insights dans votre projet][start].

Si vous utilisez les appels Track*() du Kit de développement logiciel (SDK) Application Insights intégré, vous n'avez pas besoin de l'adaptateur. [Passez donc à la section suivante](#pepper).

Pour rechercher des journaux générés avec log4Net, NLog ou System.Diagnostics.Trace, installez l'adaptateur approprié :

1. Si vous prévoyez d'utiliser log4Net ou NLog, installez-le dans votre projet. 
2. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis sélectionnez **Gérer les packages NuGet**.
3. Sélectionnez En ligne > Tout, puis **Inclure la version préliminaire** et recherchez " Microsoft.ApplicationInsights "

    ![Get the prerelease version of the appropriate adapter](./media/appinsights/appinsights-36nuget.png)

4. Sélectionnez le package approprié parmi :
  + Microsoft.ApplicationInsights.TraceListener (pour capturer les appels System.Diagnostics.Trace)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

Le package NuGet installe les assemblys nécessaires et modifie également le fichier web.config ou app.config.

## <a name="pepper"></a>3. Insertion d'appels de journalisation de diagnostic

Insérez les appels de journalisation des événements dans l'infrastructure de journalisation de votre choix. 

Par exemple, si vous utilisez le Kit de développement logiciel (SDK) Application Insights, vous pouvez insérer :

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Ou, si vous utilisez System.Diagnostics.Trace :

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Si vous préférez log4net ou NLog :

    logger.Warn("Slow response - database01");

Exécutez votre application en mode débogage, ou déployez-la sur votre serveur web.

### <a name="exceptions"></a>Exceptions

Pour envoyer des exceptions dans le journal :

JavaScript vers le client

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C# vers le serveur

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB vers le serveur

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

Les paramètres de propriétés et les mesures sont facultatifs, mais sont utiles pour filtrer et ajouter des informations supplémentaires. Par exemple, si vous avez une application qui peut exécuter plusieurs jeux, vous pouvez rechercher tous les rapports d'exception liés à un jeu particulier. Vous pouvez ajouter autant d'éléments que vous le souhaitez à chaque dictionnaire.

## <a name="view"></a>4. Affichage des données du journal


1. Dans Application Insights, ouvrez la recherche de diagnostic.

    ![Open diagnostic search](./media/appinsights/appinsights-30openDiagnostics.png)
   
2. Définissez le filtre pour les types d'événements à afficher.

    ![Open diagnostic search](./media/appinsights/appinsights-331filterTrace.png)


Les types d'événements sont :

* **Suivi** : recherche de journaux de diagnostic capturés à partir de votre serveur web. Cela inclut les appels log4Net, NLog, System.Diagnostic.Trace et ApplicationInsights TrackTrace.
* **Demande** : recherche les demandes HTTP reçues par le composant serveur de votre application web, notamment les demandes de pages, de données, d'images, etc. Les événements affichés constituent la télémétrie envoyée par le Kit de développement logiciel du serveur d'Application Insights. Ils sont utilisés pour créer le rapport du nombre de demandes.
* **Affichage de page** : recherche les événements d'affichage de page. Ces événements sont envoyés par le client web et sont utilisées pour créer les rapports d'affichages de pages. (Si rien n'est affiché ici, configurez la [surveillance du client web][usage].)
* **Événement personnalisé** : si vous avez inséré des appels vers TrackEvent() et TrackMetric() pour [surveiller l'utilisation][track], vous pouvez les rechercher ici.

Sélectionnez un événement enregistré pour afficher les informations détaillées associées. 

![Open diagnostic search](./media/appinsights/appinsights-32detail.png)

Vous pouvez utiliser des chaînes de caractères standard (sans caractère générique) pour filtrer les données des champs d'un élément.

Les champs disponibles dépendent de l'infrastructure de journalisation et des paramètres utilisés dans l'appel.


## <a name="search"></a>5. Recherche dans les données

Définissez une période et les termes à rechercher. Les recherches sur une courte période sont plus rapides. 

![Open diagnostic search](./media/appinsights/appinsights-311search.png)

Notez que vous pouvez rechercher des termes, mais pas des sous-chaînes. Les termes sont des chaînes alphanumériques comprenant des signes de ponctuation (comme " . " et " _ "). Par exemple :

<table>
  <tr><th>Terme</th><th>Non trouvé en cherchant</th><th>Mais en cherchant</th></tr>
  <tr><td>HomeController.About</td><td>about<br/>home</td><td>h*about<br/>home*</td></tr>
  <tr><td>IsLocal</td><td>local<br/>is<br/>*local</td><td>isl*<br/>islocal<br/>i*l</td></tr>
  <tr><td>New Delay</td><td>w d</td><td>new<br/>delay<br/>n* AND d*</td></tr>
</table>

Expressions de recherche utilisables :

<table>
                    <tr>
                      <th>
                        <p>Exemple de requête</p>
                      </th>
                      <th>
                        <p>Résultat</p>
                      </th>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">slow</span>
                        </p>
                      </td>
                      <td>
                        <p>Trouve tous les événements dont la période comprend le terme " slow "</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">database??</span>
                        </p>
                      </td>
                      <td>
                        <p>Trouve database01, databaseAB, ...</p>
                        <p>Le caractère ? n'est pas autorisé au début du terme à rechercher.</p>
                      </td>
                    </tr>
                     <tr>
                      <td>
                        <p>
                          <span class="code">database*</span>
                        </p>
                      </td>
                      <td>
                        <p>Trouve database, database01, databaseNNNN</p>
                        <p>* n'est pas autorisé au début du terme à rechercher</p>
                      </td>
                    </tr>
                   <tr>
                      <td>
                        <p>
                          <span class="code">apple AND banana</span>
                        </p>
                      </td>
                      <td>
                        <p>Trouve les événements qui contiennent les deux termes. Utilisez " AND " en lettres majuscules (et non " and " en lettres minuscules).</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">apple OR banana</span>
                        </p>
                        <p>
                          <span class="code">apple banana</span>
                        </p>
                      </td>
                      <td>
                        <p>Trouve les événements qui contiennent un des deux termes. Utilisez " OR " en lettres capitales (et non " or " en lettres minuscules).</p>
                        <p>Forme abrégée.</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">apple NOT banana</span>
                        </p>
                        <p>
                          <span class="code">apple -banana</span>
                        </p>
                      </td>
                      <td>
                        <p>Trouve les événements qui contiennent un terme mais pas l'autre.</p>
                        <p>Forme abrégée.</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>app* AND banana NOT (grape OR pear)</p>
                        <p>
                          <span class="code">app* AND banana -(grape pear)</span>
                        </p>
                      </td>
                      <td>
                        <p>Opérateurs logiques et utilisation des parenthèses.</p>
                        <p>Forme abrégée.</p>
                      </td>
                    </tr>
       <!-- -- fielded search feature not ready yet --
                    <tr>
                      <td>
                        <p>
                          <span class="code">message:slow</span>
                        </p>
                        <p>
                          <span class="code">ipaddress:(10.0.0.* OR 192.168.0.*)</span>
                        </p>
                        <p>
                          <span class="code">properties.logEventInfo.level:Error</span>
                        </p>
                      </td>
                      <td>
                        <p>Trouve le champ spécifié. Par défaut, une recherche est effectuée dans tous les champs. Pour connaître les champs disponibles, sélectionnez un événement et examinez ses informations en détail.</p>
                      </td>
                    </tr>
 -->
</table>


## <a name="questions"></a>Questions et réponses

### <a name="emptykey"></a>J'obtiens une erreur " La clé d'instrumentation ne peut pas être vide ".

Vous avez peut-être installé le package Nuget de l'enregistreur de données sans installer Application Insights.

Dans l'Explorateur de solutions, cliquez avec le bouton droit sur `ApplicationInsights.config`, puis sélectionnez **Mettre à jour Application Insights**. Une boîte de dialogue vous invite à vous connecter à Azure et à créer une ressource Application Insights, ou à réutiliser une ressource existante. Ceci devrait résoudre le problème.

### <a name="limits"></a>Quelle est la quantité de données conservée ?

Jusqu'à 500 événements par seconde pour chaque application. Les événements sont conservés pendant sept jours.

### <a name="cani"></a>Puis-je...

- Définir des alertes pour des événements et des exceptions ?
- Exporter des journaux pour une analyse ultérieure ?
- Rechercher des propriétés spécifiques ?

Pas encore, mais toutes ces fonctionnalités sont prévues.

## <a name="add"></a>Étapes suivantes

* [Configuration des tests de disponibilité et de réactivité][availability]
* [Résolution des problèmes][qna]





[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]




