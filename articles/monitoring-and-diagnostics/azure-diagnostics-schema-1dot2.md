---
title: "Schéma de configuration Azure Diagnostics 1.2 | Microsoft Docs"
description: "Applicable UNIQUEMENT si vous utilisez le Kit de développement logiciel (SDK) Azure 2.5 avec Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric ou Cloud Services."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/09/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 66f733d7602a8d26627fcc205f357e7a4a266d11
ms.openlocfilehash: b76ef954d8a00e190817e3d7f8e2b064210d0357
ms.lasthandoff: 02/22/2017


---
# <a name="azure-diagnostics-12-configuration-schema"></a>Schéma de configuration Azure Diagnostics 1.2
> [!NOTE]
> Azure Diagnostics est le composant utilisé pour collecter les compteurs de performances et d’autres statistiques d’Azure Virtual Machines, de Virtual Machine Scale Sets, de Service Fabric et de Cloud Services.  Cette page vous concerne uniquement si vous utilisez l’un de ces services.
>

Azure Diagnostics est utilisé avec d’autres produits de diagnostic Microsoft tels que Azure Monitor, Application Insights et Log Analytics.

Ce schéma définit les valeurs pouvant être utilisées pour initialiser les paramètres de configuration du diagnostic lors du démarrage du moniteur de diagnostics.  


 Téléchargez la définition de schéma de fichier de configuration publique en exécutant la commande PowerShell suivante :  

```PowerShell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Pour plus d’informations sur les diagnostics Azure, consultez la page [Activation de Diagnostics dans Azure Cloud Service](http://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exemple du fichier de configuration des diagnostics  
 L’exemple suivant montre un fichier de configuration de diagnostic standard :  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">  
  <WadCfg>  
    <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  
      <PerformanceCounters scheduledTransferPeriod="PT1M">  
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
      </PerformanceCounters>  
      <Directories scheduledTransferPeriod="PT5M">  
        <IISLogs containerName="iislogs" />  
        <FailedRequestLogs containerName="iisfailed" />  
        <DataSources>  
          <DirectoryConfiguration containerName="mynewprocess">  
            <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
          </DirectoryConfiguration>  
          <DirectoryConfiguration containerName="badapp">  
            <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
          </DirectoryConfiguration>  
          <DirectoryConfiguration containerName="goodapp">  
            <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
          </DirectoryConfiguration>  
        </DataSources>  
      </Directories>  
      <EtwProviders>  
        <EtwEventSourceProviderConfiguration provider="MyProviderClass" scheduledTransferPeriod="PT5M">  
          <Event id="0"/>  
          <Event id="1" eventDestination="errorTable"/>  
          <DefaultEvents />  
        </EtwEventSourceProviderConfiguration>  
        <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
          <Event id="0"/>  
          <DefaultEvents eventDestination="defaultTable"/>  
        </EtwManifestProviderConfiguration>  
      </EtwProviders>  
      <WindowsEventLog scheduledTransferPeriod="PT5M">  
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
        <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
        <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
      </WindowsEventLog>  
      <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
        <CrashDumpConfiguration processName="mynewprocess.exe" />  
        <CrashDumpConfiguration processName="badapp.exe"/>  
      </CrashDumps>  
    </DiagnosticMonitorConfiguration>  
  </WadCfg>  
</PublicConfig>  

```  

## <a name="diagnostics-configuration-namespace"></a>Espace de noms de configuration de diagnostic  
 L’espace de noms XML du fichier de configuration des diagnostics est :  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>Élément PublicConfig  
 Élément de niveau supérieur du fichier de configuration de diagnostic. Le tableau suivant décrit les éléments du fichier de configuration :  

|Nom de l’élément|Description|  
|------------------|-----------------|  
|**WadCfg**|Obligatoire. Paramètres de configuration pour les données de télémétrie à collecter.|  
|**StorageAccount**|Nom du compte de stockage Azure où stocker les données. Cela peut également être spécifié en tant que paramètre lors de l’exécution de l’applet de commande Set-AzureServiceDiagnosticsExtension.|  
|**LocalResourceDirectory**|Répertoire se trouvant sur la machine virtuelle à utiliser par Monitoring Agent pour stocker les données d’événement. S’il n’est pas défini, le répertoire par défaut est utilisé :<br /><br /> Pour un rôle Worker/web : `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Pour une machine virtuelle : `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Les attributs requis sont :<br /><br /> -                      **path** - Répertoire sur le système à utiliser par Azure Diagnostics.<br /><br /> -                      **expandEnvironment** - Contrôle si les variables d’environnement sont développées dans le nom du chemin d’accès.|  

## <a name="wadcfg-element"></a>WadCFG Element  
Définit les paramètres de configuration pour les données de télémétrie à collecter. Le tableau suivant décrit les éléments enfants :  

|Nom de l'élément|Description|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Obligatoire. Les attributs facultatifs sont les suivants :<br /><br /> -                     **overallQuotaInMB** - Quantité maximale d’espace disque local pouvant être utilisé par les différents types de données de diagnostic collectés par Azure Diagnostics. Le paramètre par défaut est 5 210 Mo.<br /><br /> -                     **useProxyServer** - Configurer Azure Diagnostics de manière à utiliser les paramètres du serveur proxy comme défini dans les paramètres d’Internet Explorer.|  
|**CrashDumps**|Permet la collecte des vidages sur incident. Les attributs facultatifs sont les suivants :<br /><br /> -                     **containerName** - Nom du conteneur d’objets blob de votre compte de stockage Azure à utiliser pour stocker les vidages sur incident.<br /><br /> -                     **crashDumpType** - Configure Azure Diagnostics pour collecter les mini-vidages sur incident ou les vidages sur incident complets.<br /><br /> -                     **directoryQuotaPercentage** - Configure le pourcentage de **overallQuotaInMB** à réserver pour les vidages sur incident sur la machine virtuelle.|  
|**DiagnosticInfrastructureLogs**|Permet la collecte des journaux générés par Azure Diagnostics. Les journaux d’infrastructure de diagnostic sont utiles pour le dépannage du système de diagnostic lui-même. Les attributs facultatifs sont les suivants :<br /><br /> -                     **scheduledTransferLogLevelFilter** - Configure le niveau de gravité minimal des journaux collectés.<br /><br /> -                     **scheduledTransferPeriod** - Intervalle entre les transferts planifiés vers le stockage Azure, arrondi à la minute la plus proche. La valeur est un [« Type de données de durée » XML.](http://www.w3schools.com/schema/schema_dtypes_date.asp)|  
|**Directories**|Permet la collecte du contenu d’un répertoire, des journaux de demandes d’accès ayant échouées IIS et/ou des journaux IIS. Attribut facultatif :<br /><br /> **scheduledTransferPeriod** - Intervalle entre les transferts planifiés vers le stockage Azure, arrondi à la minute la plus proche. La valeur est un [« Type de données de durée » XML.](http://www.w3schools.com/schema/schema_dtypes_date.asp)|  
|**EtwProviders**|Configure la collecte d’événements ETW issus des fournisseurs de manifeste EventSource et/ou ETW.|  
|**Métriques**|Cet élément permet de générer une table de compteur de performance optimisée pour les requêtes rapides. Chaque compteur de performance défini dans l’élement **PerformanceCounters** est stocké dans la table Metrics et dans la table Performance Counter. Attribut requis :<br /><br /> **resourceId** - Il s’agit de l’ID de ressource de la machine virtuelle sur laquelle vous déployez Azure Diagnostics. Obtenez le **resourceID** à partir du [portail Azure](https://portal.azure.com). Sélectionnez **Parcourir** -> **Groupe de ressources** -> **<>\>**. Cliquez sur la vignette **Propriétés** et copiez la valeur à partir du champ **ID**.|  
|**PerformanceCounters**|Permet la collecte des compteurs de performance. Attribut facultatif :<br /><br /> **scheduledTransferPeriod** - Intervalle entre les transferts planifiés vers le stockage Azure, arrondi à la minute la plus proche. La valeur est un [« Type de données de durée » XML.](http://www.w3schools.com/schema/schema_dtypes_date.asp)|  
|**WindowsEventLog**|Permet la collecte des journaux des événements Windows. Attribut facultatif :<br /><br /> **scheduledTransferPeriod** - Intervalle entre les transferts planifiés vers le stockage Azure, arrondi à la minute la plus proche. La valeur est un [« Type de données de durée » XML.](http://www.w3schools.com/schema/schema_dtypes_date.asp)|  

## <a name="crashdumps-element"></a>Élément CrashDumps  
 Permet la collecte des vidages sur incident. Le tableau suivant décrit les éléments enfants :  

|Nom de l’élément|Description|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Obligatoire. Attribut requis :<br /><br /> **processName** - Nom du processus pour lequel vous voulez qu’Azure Diagnostics collecte un vidage sur incident.|  
|**crashDumpType**|Configure Azure Diagnostics pour collecter les mini-vidages sur incident ou les vidages sur incident complets.|  
|**directoryQuotaPercentage**|Configure le pourcentage de **overallQuotaInMB** à réserver pour les vidages sur incident sur la machine virtuelle.|  

## <a name="directories-element"></a>Élément Directories  
 Permet la collecte du contenu d’un répertoire, des journaux de demandes d’accès ayant échouées IIS et/ou des journaux IIS. Le tableau suivant décrit les éléments enfants :  

|Nom de l’élément|Description|  
|------------------|-----------------|  
|**DataSources**|Liste de répertoires à analyser.|  
|**FailedRequestLogs**|Incluez cet élément dans la configuration pour permettre la collecte des journaux concernant les demandes ayant échoué sur une application ou un site IIS. Vous devez également activer les options de suivi sous **system.WebServer** dans **Web.config**.|  
|**IISLogs**|Incluez cet élément dans la configuration pour permettre la collecte des journaux IIS :<br /><br /> **containerName** - Nom du conteneur d’objets blob de votre compte de stockage Azure à utiliser pour stocker les vidages sur incident.|  

## <a name="datasources-element"></a>Élément DataSources  
 Liste de répertoires à analyser. Le tableau suivant décrit les éléments enfants :  

|Nom de l’élément|Description|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Obligatoire. Attribut requis :<br /><br /> **containerName** - Nom du conteneur d’objets blob de votre compte de stockage Azure à utiliser pour stocker les fichiers journaux.|  

## <a name="directoryconfiguration-element"></a>Élément DirectoryConfiguration  
 **DirectoryConfiguration** peut inclure l’élément **Absolute** ou **LocalResource**, mais pas les deux. Le tableau suivant décrit les éléments enfants :  

|Nom de l’élément|Description|  
|------------------|-----------------|  
|**Absolute**|Chemin d’accès absolu au répertoire à surveiller. Les attributs suivants sont requis :<br /><br /> -                     **Path** - Chemin d’accès absolu au répertoire à surveiller.<br /><br /> -                      **expandEnvironment** - Détermine si les variables d’environnement de Path sont développées.|  
|**LocalResource**|Chemin d’accès relatif à une ressource locale à surveiller. Les attributs requis sont :<br /><br /> -                     **Name** - Nom de la ressource locale contenant le répertoire à surveiller<br /><br /> -                     **relativePath** - Chemin d’accès relatif au nom qui contient le répertoire à surveiller|  

## <a name="etwproviders-element"></a>Élément EtwProviders  
 Configure la collecte d’événements ETW issus des fournisseurs de manifeste EventSource et/ou ETW. Le tableau suivant décrit les éléments enfants :  

|Nom de l’élément|Description|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Configure la collection d’événements générés à partir de la [classe EventSource](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Attribut requis :<br /><br /> **provider** - Nom de classe de l’événement EventSource.<br /><br /> Les attributs facultatifs sont les suivants :<br /><br /> -                     **scheduledTransferLogLevelFilter** - Niveau de gravité minimal à transférer vers votre compte de stockage.<br /><br /> -                     **scheduledTransferPeriod** - Intervalle entre les transferts planifiés vers le stockage Azure, arrondi à la minute la plus proche. La valeur est un [Type de données de durée XML](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  
|**EtwManifestProviderConfiguration**|Attribut requis :<br /><br /> **provider** -GUID du fournisseur d’événements<br /><br /> Les attributs facultatifs sont les suivants :<br /><br /> - **scheduledTransferLogLevelFilter** - Niveau de gravité minimal à transférer vers votre compte de stockage.<br /><br /> -                     **scheduledTransferPeriod** - Intervalle entre les transferts planifiés vers le stockage Azure, arrondi à la minute la plus proche. La valeur est un [Type de données de durée XML](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  

## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 Configure la collection d’événements générés à partir de la [classe EventSource](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Le tableau suivant décrit les éléments enfants :  

|Nom de l’élément|Description|  
|------------------|-----------------|  
|**DefaultEvents**|Attribut facultatif :<br /><br /> **eventDestination** -Nom de la table dans laquelle stocker les événements|  
|**Event**|Attribut requis :<br /><br /> **id** : ID de l’événement.<br /><br /> Attribut facultatif :<br /><br /> **eventDestination** -Nom de la table dans laquelle stocker les événements|  

## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 Le tableau suivant décrit les éléments enfants :  

|Nom de l’élément|Description|  
|------------------|-----------------|  
|**DefaultEvents**|Attribut facultatif :<br /><br /> **eventDestination** -Nom de la table dans laquelle stocker les événements|  
|**Event**|Attribut requis :<br /><br /> **id** : ID de l’événement.<br /><br /> Attribut facultatif :<br /><br /> **eventDestination** -Nom de la table dans laquelle stocker les événements|  

## <a name="metrics-element"></a>Élément Metrics  
 Permet de générer une table de compteur de performance optimisée pour les requêtes rapides. Le tableau suivant décrit les éléments enfants :  

|Nom de l’élément|Description|  
|------------------|-----------------|  
|**MetricAggregation**|Attribut requis :<br /><br /> **scheduledTransferPeriod** - Intervalle entre les transferts planifiés vers le stockage Azure, arrondi à la minute la plus proche. La valeur est un [Type de données de durée XML](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  

## <a name="performancecounters-element"></a>Élément PerformanceCounters  
 Permet la collecte des compteurs de performance. Le tableau suivant décrit les éléments enfants :  

|Nom de l’élément|Description|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|Les attributs suivants sont requis :<br /><br /> -                     **counterSpecifier** - Nom du compteur de performance. Par exemple, `\Processor(_Total)\% Processor Time`. Pour obtenir une liste des compteurs de performances se trouvant sur votre hôte, exécutez la commande `typeperf`.<br /><br /> -                     **sampleRate** - Fréquence à laquelle le compteur doit être échantillonné.<br /><br /> Attribut facultatif :<br /><br /> **unit** -Unité de mesure du compteur.|  

## <a name="performancecounterconfiguration-element"></a>Élément PerformanceCounterConfiguration  
 Le tableau suivant décrit les éléments enfants :  

|Nom de l’élément|Description|  
|------------------|-----------------|  
|**annotation**|Attribut requis :<br /><br /> **displayName** - Nom complet du compteur<br /><br /> Attribut facultatif :<br /><br /> **locale** - Paramètres régionaux à utiliser lors de l’affichage du nom du compteur|  

## <a name="windowseventlog-element"></a>Élément WindowsEventLog  
 Le tableau suivant décrit les éléments enfants :  

|Nom de l’élément|Description|  
|------------------|-----------------|  
|**DataSource**|Journaux des événements Windows à collecter. Attribut requis :<br /><br /> **name** - Requête XPath décrivant les événements windows à collecter. Par exemple :<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> Pour collecter tous les événements, spécifiez « * ».|

