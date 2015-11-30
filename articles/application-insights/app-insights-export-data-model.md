<properties 
	pageTitle="Modèle de données Application Insights" 
	description="Décrit les propriétés exportées à partir de l’exportation continue dans JSON et utilisées comme filtres." 
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
	ms.date="11/11/2015" 
	ms.author="awills"/>

# Modèle d’exportation de données Application Insights

Cette table répertorie les propriétés de télémétrie envoyées à partir des Kits de développement logiciel (SDK) [Application Insights](app-insights-overview.md) au portail. Vous verrez ces propriétés dans les données issues d’une [exportation continue](app-insights-export-telemetry.md). Elles apparaissent également dans les filtres de propriétés, dans [Metrics Explorer](app-insights-metrics-explorer.md) et dans [Recherche de diagnostic](app-insights-diagnostic-search.md).

Plusieurs [exemples](app-insights-export-telemetry.md#code-samples) illustrent comment les utiliser.

Le « &lt;telemetryType&gt; » de la première section est un espace réservé pour tout nom de type de télémétrie : affichage, demande, etc.


## &lt;telemetryType&gt;

**<measurement>**

    KVPs <string, double> <telemetryType>.measurement      Max: 100
* 
    Conteneur des propriétés de paire clé/valeur (KVP) fournissant l’extensibilité sur les éléments de télémétrie AppInsights pour l’ajout de métriques personnalisés. 

    *Dérivation :* les noms de mesure présentent une longueur maximale de 100 caractères.

    *Valeur par défaut :* si la clé existe, mais que la valeur est manquante, alors count = 1, value = 0, min/max = 0.

**<property>**

    KVPs <string, string> <telemetryType>.properties      Max: 100
* 
    Conteneur des propriétés de paire clé/valeur fournissant l’extensibilité sur les éléments de télémétrie AppInsights pour l’ajout de propriétés personnalisées. Le développeur a la possibilité de fournir une liste de paires clé/valeur associée à un élément de télémétrie. Chaque clé fait l’objet d’un suivi, et il est possible de fournir un maximum de 200 clés uniques par clé d’instrumentation (application) AppInsights. Une clé présente une longueur maximale de 100 caractères. Toutes les valeurs sont traitées en tant que chaînes et peuvent comporter jusqu’à 1 000 caractères. Chaque propriété est initialement classée en tant que dimension, autorisant ainsi des fonctionnalités de segmentation reposant sur l’ensemble de valeurs de chaque propriété. La cardinalité de chaque ensemble de valeurs fait l’objet d’un suivi par clé de propriété. Lorsque la cardinalité d’une clé dépasse 100 valeurs uniques, la propriété est classée en tant qu’attribut. Un attribut peut faire l’objet d’une recherche, mais ne peut pas constituer la cible de segmentation (agrégation ou regroupement). 

    *Dérivation :* les noms de propriété présentent une taille maximale de 100 caractères, et les valeurs de propriété une taille maximale de 1 024 caractères.

    *Valeur par défaut :* si la clé existe, mais que la valeur est manquante, alors value = Null.

**count**

    long <telemetryType>.count      
* 
    Comptage de l’élément de télémétrie   

    *Dérivation :* en cas de valeur Null, count = 1.

**duration**

    simpleMetric <telemetryType>.duration   ticks   
* 
    Durée de l’élément de télémétrie. Dans le cas d’une demande, il s’agit de la durée d’exécution de la demande. 

    *Valeur par défaut :* R1 : dans le cas d’un affichage, ce champ est facultatif.

**message**

    string <telemetrytype>.message      Max: 10240
* 
    Message texte concernant le type de télémétrie. Cette chaîne est disponible pour la recherche en texte intégral. 

**name**

    string <telemetrytype>.name      Max: 250
* 
    Nom de l’élément de télémétrie. Ce nom n’est pas unique sur l’ensemble des instances et représente un regroupement de types de télémétrie. Dans le cas des affichages, le nom est défini par défaut sur la valeur URLData.base. Dans le cas des événements, il s’agit d’une étiquette fournie par le développeur. Dans le cas des requêtes, il s’agit d’une forme lisible de la demande, par exemple contrôleur\\action. 

    *Exemples*<br/> Noms d’affichage :<br/>70-486 Question d’examen 1<br/>À propos de - Mon application ASP.NET<br/><br/>Noms de demande :<br/>POST /Components/WebHandlers/ItemCompare.ashx<br/>GET /signalr/poll<br/>GET /signalr/negotiate

**severity**

    string <telemetryType>.severity      Max: 100
* 
    Gravité de l’élément de télémétrie. Valide pour les éléments de télémétrie Trace et Exception. 

**url**

    string <telemetrytype>.url      Max: 2048
* 
    URL de page consultée, d’événement, de demande ou de RDD. URL complète, prise en charge dans les recherches en texte intégral et dans les exportations. Ce champ peut présenter une cardinalité très importante et correspond à un attribut. Il est analysé sous la forme d’un ensemble d’éléments de données urlData utilisable pour les agrégations dans Metrics Explorer. 

    *Valeur par défaut :* R2 : sur remotedepencyType, si dependencyType = HTTP, ce champ est obligatoire.<br/> Sur clientperformanceType, ce champ est obligatoire.

    *Exemples*<br/> https://icecream.contoso.com/main.aspx?etc=3&extraqs=%3fetc%3d3%26formid%3dc40d07a7-1cf1-4e1d-b00e-e61876d1284e&pagemode=iframe&pagetype=entityrecord<br/>http://fabrikam-oats.azurewebsites.net/index.htm

**urlData.base**

    string <telemetrytype>.urldata.base      Max: 2048
* 
    Partie de l’élément de données d’URL excluant l’hôte et les paramètres de requête. Il s’agit de l’URI racine. Cette valeur est utilisable pour la segmentation/l’agrégation. 

    *Dérivation :* voir l’annexe concernant la transformation d’URL.

    *Exemples*<br/> /main.aspx?etc=3&extraqs=%3fetc%3d3%26formid%3dc40d07a7-1cf1-4e1d-b00e-e61876d1284e&pagemode=iframe&pagetype=entityrecord<br/>/default.aspx<br/>/Patients/Search/<br/>

**urldata.hashTag**

    string <telemetrytype>.urldata.hashtag      Max: 100
* 
    Texte de la balise de hachage de l’élément de données d’URL 

    *Dérivation :* voir l’annexe concernant la transformation d’URL.

**urlData.host**

    string <telemetrytype>.urldata.host      Max: 200
* 
    Hôte de l’élément de données d’URL. Si l’élément de données d’URL est un URI local, ce champ est représenté comme étant vide. 

    *Dérivation :* voir l’annexe concernant la transformation d’URL.

    *Exemples*<br/> www.fabrikam.com<br/>www.contoso.com<br/>bretwpc711.azurewebsites.net<br/>



## availability

**availability**

    simpleMetric availability.availability      
* 
    Indicateur de la réussite du test de disponibilité 

**dataSize**

    simpleMetric availability.datasize      
* 
    Taille du message texte &lt;télémétrie&gt;.message 

**result**

    enum (pass/fail) availability.result      
* 
    Pointeur (appel HTTP) permettant de récupérer les informations détaillées relatives au test web de disponibilité 

**runLocation**

    string availability.runlocation      Max: 100
* 
    Emplacement d’exécution du test de disponibilité 

**testName**

    string availability.testname      Max: 1024
* 
    Nom du test de disponibilité 

**testRunId**

    string availability.testrunid      Max: 100
* 
    Identificateur unique de l’instance de la série de tests de disponibilité 

**testTimeStamp**

    datetime availability.testtimestamp      
* 
    Horodateur du début de l’instance de la série de tests de disponibilité 


## basicexception

**assembly**

    string basicexception.assembly      Max: 100
**exceptionGroup**

    string basicException.exceptionGroup      
**exceptionType**

    string basicexception.exceptiontype      Max: 100
**failedUserCodeAssembly**

    string basicException.failedUserCodeAssembly      
**failedUserCodeMethod**

    string basicException.failedUserCodeMethod      
**handledAt**

    string basicexception.handledat      Max: 100
**innerMostExceptionMessage**

    string basicException.innermostExceptionMessage      
**innerMostExceptionThrownAtAssembly**

    string basicException.innermostExceptionThrownAtAssembly      
**innerMostExceptionThrownAtMethod**

    string basicException.innermostExceptionThrownAtMethod      
**innerMostExceptionType**

    string basicException.innermostExceptionType      
**statique**

    string basicexception.method      Max: 100
**outerMostExceptionMessage**

    string basicException.outerExceptionMessage      
**outerMostExceptionThrownAtAssembly**

    string basicException.outerExceptionThrownAtAssembly      
**outerMostExceptionTrownAtMethod**

    string basicException.outerExceptionThrownAtMethod      
**outerMostExceptionType**

    string basicException.outerExceptionType      
**problemid**

    string basicexception.problemid      Max: 100
* 
    *Dérivation :* voir l’annexe concernant l’analyse de piles d’appels. 

**Exceptions.Assembly**

    string basicexception.exceptions.assembly      Max: 100
**Exceptions.fileName**

    string basicexception.exceptions.filename      Max: 100
**Exceptions.hasFullStack**

    boolean basicexception.exceptions.hasfullstack      
**Exceptions.Level**

    string basicexception.exceptions.level      Max: 100
**Exceptions.Line**

    long basicexception.exceptions.line      
**exceptions.message**

    string basicexception.exceptions.message      Max: 10240
**Exceptions.Method**

    string basicexception.exceptions.method      Max: 100
**Exceptions.outerId**

    long basicexception.exceptions.outerid      
**Exceptions.parsedStack**

    List<StackFrame> basicexception.exceptions.parsedstack      
**Exceptions.Stack**

    string basicexception.exceptions.stack      Max: 10240
**Exceptions.typeName**

    string basicexception.exceptions.typename      Max: 100
**parsedStackAssembly**

    string basicException.parsedStack.assembly      
**parsedStackFilename**

    string basicException.parsedStack.fileName      
**parsedStackLevel**

    string basicException.parsedStack.level      
**parsedStackLine**

    string basicException.parsedStack.line      
**paseStackMethod**

    string basicException.parsedStack.method      

## clientperformance

**domProcessing**

    simpleMetric clientperformance.domprocessing   ms   
* 
    Partie du temps perTotal. Cette partie représente le temps que l’application a consacré au traitement de la réponse. Pour un client web, il s’agit de la durée de traitement du modèle DOM (Document Object Model). Ce minutage est capturé à l’aide de l’API perfTiming de navigateur moderne. 

**networkConnect**

    simpleMetric clientperformance.networkconnect   ms   
* 
    Partie du temps perTotal. Cette partie représente le temps que l’application a consacré à l’établissement de la connexion réseau. Ce minutage est capturé à l’aide de l’API perfTiming de navigateur moderne. 

**perfTotal**

    simpleMetric clientperformance.perftotal   ms   
* 
    Temps total du chargement de l’affichage. Pour les clients web, cette valeur équivaut au « temps de chargement de la page ». Ce minutage est capturé à l’aide de l’API perfTiming de navigateur moderne. 

**receiveResponse**

    simpleMetric clientperformance.receiveresponse   ms   
* 
    Partie du temps perTotal. Cette partie représente le temps que l’application a consacré à la réception de la réponse à la demande. Ce minutage est capturé à l’aide de l’API perfTiming de navigateur moderne. 

**sendRequest**

    simpleMetric clientperformance.sendrequest   ms   
* 
    Partie du temps perTotal. Cette partie représente le temps que l’application a consacré à l’envoi de la demande au serveur. Ce minutage est capturé à l’aide de l’API perfTiming de navigateur moderne. 


## context

**applicationBuild**

    string context.application.build      Max: 100
* 
    Numéro de build de l’application 

**applicationVersion**

    string context.application.version      Max: 100
* 
    Version de l’application cliente Non disponible si la valeur est définie sur Unknown. 

    *Exemples*<br/> 2015.5.21.3<br/>NokiaMailBye\_CD\_20150227.4

**telemetryType**

    string context.billing.telemetrytype      Max: 100
* 
    Représente le type de télémétrie d’un enregistrement de facturation et est utilisé en interne en tant que segmentation des éléments de télémétrie facturables pour une application 

**dataId**

    string context.data.id      Max: 100
* 
    Identificateur unique de l’élément de télémétrie. Attribué au niveau du point de terminaison de collecte de données. 

    *Dérivation :* UUID4 généré.

    *Exemples*<br/> edc6eaf3-3459-46a0-bb81-bedc24913864

**eventTime**

    datetime context.data.eventtime      
* 
    Date et heure de l’événement de télémétrie enregistrées au format UTC (temps universel coordonné). En règle générale, ce champ est rempli au niveau du client. Si ce champ est manquant, il est renseigné au niveau du point de terminaison de collecte de données. Ce champ présente le format AAAA-MM-JJTHH:MM:SS.sssZ.    

    *Exemples*<br/> 2015-05-20T04:00:46.8338283Z

**samplingRate**

    string context.data.samplerate      Max: 100
* 
    Taux d’échantillonnage du producteur de données (SDK). Une valeur différente de 1 signifie que les métriques associés à cet élément de télémétrie représentent les valeurs échantillonnées. Par conséquent, dans le cas d’un taux d’échantillonnage de 0,05, tout élément de télémétrie 1 représenterait un nombre de 20. 

**iPhone**

    string context.device.browser      Max: 100
* 
    Navigateur du client 

    *Valeur par défaut :* en cas de valeur Null, la valeur par défaut est définie en fonction du traitement de l’agent utilisateur. Pour plus d’informations, voir l’annexe relative à l’analyse de l’agent utilisateur.

    *Exemples*<br/> Opera<br/>Mobile Safari<br/>Ovi Browser<br/>Chrome<br/>Firefox<br/>Internet Explorer

**browserVersion**

    string context.device.browserversion      Max: 100
* 
    Version du navigateur du client 

    *Valeur par défaut :* en cas de valeur Null, la valeur par défaut est définie en fonction du traitement de l’agent utilisateur. Pour plus d’informations, voir l’annexe relative à l’analyse de l’agent utilisateur.

    *Exemples*<br/> Opera 12.17<br/>Mobile Safari 8.0<br/>Ovi Browser 5.5<br/>Chrome 37.0<br/>Firefox 21.0<br/>Internet Explorer 7.0

**deploymentId**

    string context.device.deploymentid      Max: 100
* 
    Identificateur de déploiement du serveur 


**deviceName**

    string context.device.name      Max: 100
* 
    Nom de l’appareil sur lequel s’exécute l’application 

**locale**

    string context.device.locale      Max: 100
* 
    Paramètres régionaux de l’application sur le client. Si cette valeur n’est pas fournie explicitement sur l’élément de télémétrie, elle découle du traitement du champ de l’agent utilisateur. 

    *Exemples*<br/> ru<br/>fr-FR<br/>fr-FR<br/>inconnu

**machineName**

    string context.device.vmname      Max: 100
* 
    Nom d’ordinateur du serveur. Dans le cas d’un calcul virtualisé, cet élément de données correspond à l’hôte sous-jacent. Dans le cas d’un calcul dédié, cet élément est le nom de l’ordinateur. 


**operatingSystem**

    string context.device.os      Max: 100
* 
    Système d’exploitation du client 

    *Valeur par défaut :* en cas de valeur Null, la valeur par défaut est définie en fonction du traitement de l’agent utilisateur. Pour plus d’informations, voir l’annexe relative à l’analyse de l’agent utilisateur.

    *Exemples*<br/> Windows<br/>iOS iPad<br/>Nokia

**operatingSystemVersion**

    string context.device.osversion      Max: 100
* 
    Version du système d’exploitation du client 

    *Valeur par défaut :* en cas de valeur Null, la valeur par défaut est définie en fonction du traitement de l’agent utilisateur. Pour plus d’informations, voir l’annexe relative à l’analyse de l’agent utilisateur.

    *Exemples*<br/> Windows XP<br/>iOS 8.3<br/>Nokia Série 40<br/>Windows 7<br/>Windows 8

**roleInstance**

    string context.device.roleinstance      Max: 100
* 
    Instance de calcul du serveur. Dans un scénario de type cloud/virtualisé, il s’agit du nom virtuel de l’instance de calcul. Dans le cas d’une instance de calcul dédié, cet élément correspond au nom de l’ordinateur. Dans le cas d’Azure Cloud Services, ce champ doit être défini par défaut sur le nom d’instance de rôle PaaS (platform as a service) ou sur le nom de machine virtuelle IaaS (infrastructure as a service).  

**roleName**

    string context.device.rolename      Max: 100
* 
    Espace de noms logique destiné à regrouper les instances de calcul du serveur. Dans le cas des services hébergés par Azure, les rôles PaaS doivent être définis par défaut sur le nom de rôle PaaS. Les rôles IaaS doivent être définis par défaut sur le nom de la machine virtuelle.  

**screenHeight**

    string context.device.screenresolution.height      Max: 100
* 
    Hauteur d’écran de l’application sur le matériel client au moment de l’enregistrement de l’élément de télémétrie. Si cette valeur n’est pas fournie explicitement, elle découle d’une transformation de l’élément de données screenresolution. 

    *Dérivation :* valeur analysée à partir de context.device.screenresolution si cet élément est présent.

    *Exemples*<br/> 360<br/>1 280<br/>1 920

**screenResolution**

    string context.device.screenresolution      Max: 100
* 
    Résolution d’écran au moment de la capture de l’élément de télémétrie par l’application. Cette résolution peut basculer entre portrait et paysage au cours d’une session. Lorsque cet attribut est transmis au niveau session, il s’agit de la première résolution d’écran capturée pour représenter la session complète. 

    *Exemples*<br/> Hauteur/largeur de résolution d’écran<br/>360 X 640<br/>1 280 X 800<br/>1 920 x 1 080

**screenWidth**

    string context.device.screenresolution.width      Max: 100
* 
    Largeur d’écran de l’application sur le matériel client au moment de l’enregistrement de l’élément de télémétrie. Si cette valeur n’est pas fournie explicitement, elle découle d’une transformation de l’élément de données screenresolution. 

    *Dérivation :* valeur analysée à partir de context.device.screenresolution si cet élément est présent.

    *Exemples*<br/> 640<br/>800<br/>1 080


**aiAgentVersion**

    string context.internal.agentversion      Max: 100
* 
    Élément de données interne représentant la version d’agent du Kit de développement logiciel (SDK) qui a généré l’élément de télémétrie 

**city**

    string context.location.city      Max: 100
* 
    Ville de la session d’application. Cette information peut être fournie directement sur l’élément de télémétrie. Si ce champ n’est pas renseigné, il est rempli en fonction de la valeur IPv4 sur l’élément de télémétrie. Si aucune valeur IPv4 n’est fournie, le champ est laissé vide. 

    *Exemples*<br/> Minsk<br/>Haarlem

**clientIpAddress**

    ipv4 context.location.clientip      
* 
    Adresse IPv4 du client au format xxx.xxx.xxx.xxx.   

    *Valeur par défaut :* en cas de valeur Null, le champ est défini par défaut sur l’adresse IP HTTP capturée au niveau du point de terminaison de collecte de données.

    *Exemples*<br/> 0.123.63.143<br/>123.203.131.197

**continent**

    string context.location.continent      Max: 100
* 
    Continent de la session d’application. Cette information peut être fournie directement sur l’élément de télémétrie. Si ce champ n’est pas renseigné, il est rempli en fonction de la valeur IPv4 sur l’élément de télémétrie. Si aucune valeur IPv4 n’est fournie, le champ est laissé vide. 

    *Exemples*<br/> Europe<br/>Amérique du Nord

**country**

    string context.location.country      Max: 100
* 
    Pays de la session d’application. Cette information peut être fournie directement sur l’élément de télémétrie. Si ce champ n’est pas renseigné, il est rempli en fonction de la valeur IPv4 sur l’élément de télémétrie. Si aucune valeur IPv4 n’est fournie, le champ est laissé vide. 

    *Exemples*<br/> Bélarus<br/>Pays-Bas<br/>Allemagne


**state**

    string context.location.province      Max: 100
* 
    Département/province de la session d’application. Cette information peut être fournie directement sur l’élément de télémétrie. Si ce champ n’est pas renseigné, il est rempli en fonction de la valeur IPv4 sur l’élément de télémétrie. Si aucune valeur IPv4 n’est fournie, le champ est laissé vide. 

    *Exemples*<br/> Minsk<br/>Oregon<br/>Serbie centrale<br/>Provincia di Oristano

**operationId**

    string context.operation.id      Max: 100
* 
    Identificateur de corrélation utilisable dans l’ensemble des éléments de télémétrie. Par exemple, un identificateur de demande peut être renseigné dans l’élément operation.id de tous les éléments de télémétrie associés, permettant ainsi d’établir une corrélation entre les différents éléments de télémétrie, tels que RDD, exception, événements, etc.  

**operationName**

    string context.operation.name      Max: 100
* 
    Nom d’opération explicite. Voir l’élément operationId. Cet élément permet de regrouper les identificateurs d’opérations similaires, comme Achat terminé.   

**operationParentId**

     context.operation.parentid      
* 
    Identificateur parent d’operation.id, permettant ainsi d’imbriquer les identificateurs à des fins de corrélation de télémétrie.   

**syntheticSource**

    string context.data.syntheticsource      Max: 100
* 
    Si issynthetic = true, cet élément de données représente la source des données synthétiques. 

    *Valeur par défaut :* en cas de valeur Null, le système inspecte l’agent utilisateur pour rechercher des sources synthétiques connues (robots d’indexation, etc.) et peut alors définir la source sur cette base.

**syntheticTransaction**

    boolean context.data.issynthetic      
* 
    Indicateur précisant que l’élément de télémétrie a été généré par suite de tests synthétiques et non d’une activité utilisateur réelle. 

    *Valeur par défaut :* en cas de valeur Null, l’agent utilisateur est inspecté par rapport à une liste connue d’agents synthétiques. Si une correspondance est trouvée, ce champ est défini sur la valeur true.<br/>Si la correspondance d’agent utilisateur est nulle, le champ prend la valeur false.

**session.Id**

    String context.session.id      Max: 100
* 
    Identificateur unique d’une interaction d’utilisateurs réels avec une application. Cette interaction constitue une « session ». Toutes les données de télémétrie générées par l’application sous la même clé d’instrumentation iKey doivent contenir cet identificateur unique. <br/><br/>Une session se définit par une série d’événements consécutifs au sein d’une même interaction utilisateur. Une période de plus de 30 minutes sans événement de télémétrie signale la fin d’une session.   

    *Valeur par défaut :* non valide sur MetricType, BillingType.

    *Exemples*<br/> CFFC8B21-9828-4F56-AD7C-B6B5AC26B133

**accountAcquisitionDate**

    datetime context.user.accountAcquisitionDate  
    
**anonUserId**

    string context.user.anonId      Max: 100
* 
    Identificateur unique définissant un utilisateur anonyme dans l’application. Lorsqu’un Kit de développement logiciel (SDK) est utilisé, cet identificateur est généré automatiquement et est persistant sur le client. Alors que cet identificateur n’établit aucune distinction entre les utilisateurs réels qui partagent un appareil sous la même connexion, il différencie les utilisateurs réels qui utilisent des connexions différentes lorsque le système d’exploitation prend en charge les profils. Cet élément constitue le meilleur proxy pour les utilisateurs uniques en l’absence d’une expérience authentifiée. 

    *Exemples*<br/> f23854a1b01c4b1fa79fa2d9a5768526

**anonymousUserAcquisitionDate**

    datetime context.user.anonAcquisitionDate      

**authenticatedUserAcquisitionDate**

    datetime context.user.authAcquisitionDate     
 
**authUserId**

    string context.user.authId      Max: 100
* 
    Identificateur unique définissant un utilisateur authentifié au sein de l’application. Cet identificateur est fourni par le développeur. L’avantage d’un utilisateur authentifié réside dans le fait que l’identificateur peut être itinérant sur plusieurs appareils au sein de l’application tout en conservant son unicité. 

**storeRegion**

    string context.user.storeregion      Max: 100
* 
    Région du magasin d’où provient l’application. 

**userAcountId**

    string context.user.accountId      Max: 100
* 
    Identificateur unique définissant un compte au sein de l’application. Cet identificateur est fourni par le développeur. 

### Mesures personnalisées

    context.custom.metrics.<metric-name>

      double value
      double count
      double min
      double max
      double stdDev
      double sampledValue
      double sum


## remotedependency

**async**

    boolean remotedependency.async      
* 
    Indicateur précisant si l’appel de dépendance distant était asynchrone. 

**commandName**

    string remotedependency.commandname      Max: 2048
* 
    Nom de commande SQL de la dépendance distante, si cette dernière est SQL 

**dependencyTypeName**

    string remotedependency.dependencytypename      Max: 2048
* 
    Nom du type de la dépendance distante 

**remoteDependencyName**

    string remotedependency.remotedependencyname      Max: 2048
* 
    Nom de la dépendance distante 

    *Dérivation :* normalisation sous la forme &lt;telemetryType.name&gt;

**remoteDependencyType**

    string remotedependency.remotedependencytype      Max: 100
* 
    Type de dépendance distante. Les types pris en charge sont SQL, ressources AZURE (stockage, file d’attente, etc.) et HTTP. 

**réussi**

    boolean remotedependency.success      
* 
    Indicateur précisant si l’appel de dépendance distante a réussi ou échoué. 


## request

**hasdetaileddata**

    boolean request.hasdetaileddata      
* 
    Indicateur précisant s’il existe des éléments de télémétrie associés basés sur l’élément operation.id 

**httpMethod**

    string request.httpmethod      Max: 100
* 
    Méthode HTTP utilisée sur la demande   

**id**

    string request.id      Max: 100
* 
    Identificateur unique d’une demande, généré par le Kit de développement logiciel (SDK). Cet identificateur peut être inséré dans la propriété d’identificateur d’opération pour permettre la mise en corrélation des éléments de télémétrie qui résultent d’une même demande. 

**responseCode**

    long request.responsecode      
* 
    Code de réponse d’une demande 

**réussi**

    boolean request.success      
* 
    Indicateur précisant si la demande a réussi. Un code de réponse obtenu dans les 200 s est considéré comme une réussite. 

    *Valeur par défaut :* en cas de valeur Null, le champ est défini par défaut sur true.


## sessionmetric

**anonymousUserDurationSinceLastVisit**

    Long sessionmetric.anonymoususerdurationsincelastvisit      
* 
    Temps écoulé depuis la dernière visite effectuée par cet identificateur d’utilisateur anonyme. Lors de la première visite, ce champ est vide. Pour chaque visite ultérieure, il s’agit du temps écoulé entre les visites, exprimé en nombre de jours. Une valeur de 3 signifie que 3 jours se sont écoulés entre l’instance de session précédente et cette instance de session. 

**anonymousUserSessionCount**

    Long sessionmetric.anonymoususersessioncount      
* 
    Nombre de visites de l’utilisateur anonyme. Il s’agit d’un compteur incrémentiel du nombre total de sessions historiques correspondant à cet identificateur d’utilisateur anonyme unique. Chaque session ouverte par cet identificateur incrémente le compteur. Ce compteur est effacé si l’identificateur d’utilisateur n’est pas rencontré dans un délai de 30 jours. Passé ce délai, le compteur est réinitialisé, et la prochaine visite de l’identificateur d’utilisateur sera considérée comme un nouvel utilisateur. 

**authenticatedAccountDurationSinceLastVisit**

    Long sessionmetric.authenticatedaccountdurationsincelastvisit      
* 
    Temps écoulé depuis la dernière visite effectuée par cet identificateur de compte. Lors de la première visite, ce champ est vide. Pour chaque visite ultérieure, il s’agit du temps écoulé entre les visites, exprimé en nombre de jours. Une valeur de 3 signifie que 3 jours se sont écoulés entre l’instance de session précédente et cette instance de session. 

**authenticatedAccountSessionCount**

    Long sessionmetric.authenticatedaccountsessioncount      
* 
    Nombre de visites de l’identificateur de compte authentifié. Il s’agit d’un compteur incrémentiel du nombre total de sessions historiques correspondant à cet identificateur de compte unique. Chaque session ouverte par cet identificateur incrémente le compteur. Ce compteur est effacé si l’identificateur d’utilisateur n’est pas rencontré dans un délai de 30 jours. Passé ce délai, le compteur est réinitialisé, et la prochaine visite de l’identificateur d’utilisateur sera considérée comme un nouvel utilisateur. 

**authenticatedUserDurationSinceLastVisit**

    Long sessionmetric.authenticateduserdurationsincelastvisit      
* 
    Temps écoulé depuis la dernière visite effectuée par cet identificateur d’utilisateur authentifié. Lors de la première visite, ce champ est vide. Pour chaque visite ultérieure, il s’agit du temps écoulé entre les visites, exprimé en nombre de jours. Une valeur de 3 signifie que 3 jours se sont écoulés entre l’instance de session précédente et cette instance de session. 

**authenticatedUserSessionCount**

    Long sessionmetric.authenticatedusersessioncount      
* 
    Nombre de visites de l’identificateur d’utilisateur authentifié. Il s’agit d’un compteur incrémentiel du nombre total de sessions historiques correspondant à cet identificateur d’utilisateur authentifié unique. Chaque session ouverte par cet identificateur incrémente le compteur. Ce compteur est effacé si l’identificateur d’utilisateur n’est pas rencontré dans un délai de 30 jours. Passé ce délai, le compteur est réinitialisé, et la prochaine visite de l’identificateur d’utilisateur sera considérée comme un nouvel utilisateur. 

**crashCount**

    Long sessionmetric.crashcount      
* 
    Nombre d’incidents qui sont survenus au cours de cette instance de session 

**duration**

    timespan sessionmetric.duration      
* 
    Durée d’une instance de session 

**entryEvent**

    string sessionmetric.entryevent      Max: 200
* 
    Premier événement de la session. Cette information découle de l’élément event.name et est disponible en tant que segmentation/agrégation pour les métriques sessionMetric. 

    *Dérivation :* valeur découlant de l’élément event.name.

**entryUrl**

    string sessionmetric.entryurl      Max: 2048
* 
    Première URL de la session. Cette information découle de l’élément urlData.base et est disponible en tant que segmentation/agrégation pour les métriques sessionMetric. 

    *Dérivation :* valeur découlant de l’élément &lt;telemetryType&gt;.Url.

**eventCount**

    Long sessionmetric.eventcount      
* 
    Nombre d’événements survenus au cours de cette instance de session 

**exceptionCount**

    Long sessionmetric.exceptioncount      
* 
    Nombre d’exceptions survenues au cours de cette instance de session 

**exitEvent**

    string sessionmetric.exitevent      Max: 200
* 
    Dernier événement de la session. Cette information découle de l’élément event.name et est disponible en tant que segmentation/agrégation pour les métriques sessionMetric. 

    *Dérivation :* valeur découlant de l’élément event.name.

**exitUrl**

    string sessionmetric.exiturl      Max: 2048
* 
    Dernière URL de la session. Cette information découle de l’élément urlData.base et est disponible en tant que segmentation/agrégation pour les métriques sessionMetric. 

    *Dérivation :* valeur découlant de l’élément &lt;telemetryType&gt;.Url.

**pageBounceCount**

    boolean sessionmetric.pagebouncecount      
* 
    Nombre de sessions de rebond représenté par cet élément de télémétrie sessionMetric. Une session de rebond est une session créée sur la base d’un affichage d’élément de télémétrie unique. 

    *Dérivation :* si sessionMetric.viewCount + sessionMetric.requestCount = 1, alors 1 sinon 0

**pageCount**

    Long sessionmetric.pagecount      
* 
    Nombre d’affichages survenus au cours de cette instance de session 

**requestCount**

    Long sessionmetric.requestcount      
* 
    Nombre de demandes survenues au cours de cette instance de session 

**sessionCount**

    Long sessionmetric.sessioncount      
* 
    Nombre de sessions représenté par cette instance de télémétrie sessionMetric 


## trace

**context**

    string trace.context      Max: 100
* 
    Contexte de l’application au moment du suivi ou de l’exception 

**exception**

    string trace.exception      Max: 10240
* 
    Exception associée à l’élément de télémétrie de suivi 

**excerpt**

    string trace.excerpt      Max: 100
* 
    Message texte court d’un élément de télémétrie de suivi 

**formatMessage**

    string trace.formatmessage      Max: 100
* 
    Message de format concernant l’élément de télémétrie de suivi 

**formatProvider**

    string trace.formatprovider      Max: 100
* 
    Fournisseur de format concernant l’élément de télémétrie de suivi 

**hasStackTrace**

    boolean trace.hasstacktrace      
* 
    Indicateur précisant si l’élément de télémétrie de suivi inclut une arborescence des appels de procédure 

**level**

    string trace.level      Max: 100
* 
    Niveau du message de suivi 

**loggerName**

    string trace.loggername      Max: 100
* 
    Nom d’enregistreur d’événements de suivi 

**loggerShortName**

    string trace.loggershortname      Max: 100
* 
    Nom court d’enregistreur d’événements 

**message**

    string trace.message      Max: 10240
* 
    Message texte complet d’un élément de télémétrie de suivi 

**messageId**

    string trace.messageId      Max: 100
* 
    Identificateur unique de l’élément de télémétrie de suivi 

**parameters**

    string trace.parameters      Max: 100
* 
    Paramètres fournis à l’enregistreur de suivi concernant l’élément de télémétrie de suivi 

**processId**

    string trace.processId      Max: 100
* 
    Identificateur de processus de l’application lors de l’enregistrement de l’élément de télémétrie 

**sourceType**

    string trace.sourceType      Max: 100
* 
    Type de source d’un élément de télémétrie de suivi 

**sqquenceId**

    string trace.sequenceid      Max: 100
* 
    Identificateur de séquence qu’un fournisseur de suivi peut utiliser pour enregistrer la séquence des éléments de télémétrie de suivi 

**stacktrace**

    string trace.stacktrace      Max: 100
* 
    Arborescence des appels de procédure capturée pour l’élément de télémétrie de suivi 

**threadId**

    string trace.threadId      Max: 100
* 
    Identificateur de thread de l’application au moment de l’enregistrement de l’élément de télémétrie 

**userStackframe**

    string trace.userstackframe      Max: 100
* 
    Frame de pile d’utilisateur concernant l’élément de télémétrie de suivi 

**userStackframNum**

    string trace.userstackframenum      Max: 100
* 
    Numéro de frame de pile d’utilisateur concernant l’élément de télémétrie de suivi 


## view

**referrerDataUrl**

    string view.referralurl      Max: 2048
* 
    URL de référence de la page consultée. URL complète, prise en charge dans les recherches en texte intégral et dans les exportations. Ce champ peut présenter une cardinalité très importante et correspond à un attribut. Il est analysé dans un ensemble d’éléments de données referralData utilisable pour les agrégations dans Metrics Explorer. 

**referrerData.base**

    string view.referrerdata.base      Max: 2048
* 
    Partie de l’URL de référence excluant l’hôte et les paramètres de requête. Il s’agit de l’URI racine. Cette valeur est utilisable pour la segmentation/l’agrégation. 

    *Dérivation :* voir l’annexe concernant la transformation d’URL.

**referrerData.hashTag**

    string view.referrerdata.hashtag      Max: 100
* 
    Texte de la balise de hachage de l’URL de référence 

    *Dérivation :* voir l’annexe concernant la transformation d’URL.

**referrerData.host**

    string view.referrerdata.host      Max: 200
* 
    Hôte de l’URL de référence. Si l’URL est un URI local, ce champ est représenté comme étant vide. 

    *Dérivation :* voir l’annexe concernant la transformation d’URL.

**referrerData.port**

    string view.referrerdata.port      Max: 100
* 
    Port de l’URL de référence, s’il est représenté sur l’URL complète. Dans le cas contraire, ce champ est vide. 

    *Dérivation :* voir l’annexe concernant la transformation d’URL.

**referrerData.protocol**

    string view.referrerdata.protocol      Max: 100
* 
    Protocole (HTTP, FTP, etc.) de l’URL de référence 

    *Dérivation :* voir l’annexe concernant la transformation d’URL.

    *Exemples*<br/> http<br/>https

**referrerData.queryParameters.parameter**

    string view.referrerdata.queryparameters.parameter      Max: 100
* 
    Tableau des noms de paramètre de requête de l’URL de référence 

    *Dérivation :* voir l’annexe concernant la transformation d’URL.

**referrerData.queryParameters.value**

    string view.referrerdata.queryparameters.value      Max: 100
* 
    Tableau des valeurs de paramètre de requête analysées à partir de l’URL des données de référence. 

    *Dérivation :* voir l’annexe concernant la transformation d’URL.



## Voir aussi

* [Application Insights](app-insights-overview.md) 
* [Exportation continue](app-insights-export-telemetry.md)
* [Exemples de code](app-insights-export-telemetry.md#code-samples)

<!---HONumber=Nov15_HO4-->