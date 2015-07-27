<properties 
	pageTitle="Surveillance des dépendances, des exceptions et des temps d'exécution dans les applications web Java" 
	description="Surveillance étendue de votre site web Java avec Application Insights" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="awills"/>
 
# Surveillance des dépendances, des exceptions et des temps d'exécution dans les applications web Java

*Application Insights est à l'état de version préliminaire.*

Si vous avez [instrumenté votre application web Java avec Application Insights][java]vous pouvez utiliser l'agent Java pour obtenir des informations plus détaillées, sans aucune modification de code :

* **Dépendances distantes :** données concernant les appels passés par votre application via un pilote [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/) comme MySQL, SQL Server, PostgreSQL ou SQLite.
* **Exceptions détectées :** données concernant les exceptions gérées par votre code.
* **Temps d’exécution de la méthode :** données concernant le temps nécessaire pour exécuter des méthodes spécifiques.

Pour utiliser l'agent Java, installez-le sur votre serveur. Vos applications web doivent être instrumentées à l’aide du [Kit de développement logiciel (SDK) Java Application Insights][java].

## Installation de l'agent Application Insights pour Java

1. Sur l'ordinateur exécutant votre serveur Java, [téléchargez l'agent](http://go.microsoft.com/fwlink/?LinkId=618633).
2. Modifiez le script de démarrage de l’application serveur et ajoutez la JVM suivante :

    `javaagent:`*chemin d'accès complet au fichier JAR de l'agent*

    Par exemple, dans Tomcat sur une machine Linux :

    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`


3. Redémarrez votre serveur d'applications.

## Configuration de l’agent

Créez un fichier nommé`AI-Agent.xml` et placez-le dans le même dossier que le fichier JAR de l'agent.

Définissez le contenu du fichier xml. Modifiez l'exemple suivant pour inclure ou omettre les fonctionnalités souhaitées.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>
        
        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true"/>

        <!-- Collect data about caught exceptions 
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne" 
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String:I)V" />
        </Class>
        
      </Instrumentation>
    </ApplicationInsightsAgent>

```

Vous devez activer l'exception de rapports et le minutage pour les méthodes individuelles.

Par défaut,`reportExecutionTime` est true, `reportCaughtExceptions` est false.

## Affichage des données

Dans la ressource d'Application Insights, les temps des dépendances distantes agrégées et d'exécution de la méthode apparaissent [dans la vignette des performances][metrics].

Pour rechercher des instances individuelles de rapports sur les dépendances, les exceptions et les méthodes, ouvrez [Rechercher][diagnostic].

## Des questions ? Des problèmes ?

[Résolution des problèmes Java](app-insights-java-troubleshoot.md)



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=July15_HO3-->