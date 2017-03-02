---
title: "Schéma de configuration Azure Diagnostics 1.3, 1.4, 1.5, 1.6, 1.7 | Microsoft Docs"
description: "Schéma version 1.3 et ultérieures pour les diagnostics Azure fournis avec le Kit SDK Microsoft Azure."
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
ms.sourcegitcommit: 72b343df7df38a43e5d8fe72b453e56e29d0aba4
ms.openlocfilehash: b5d800db47284ca9fa82fc07ed67617ab32cd143
ms.lasthandoff: 02/22/2017


---
# <a name="azure-diagnostics-13-14-15-configuration-schema"></a>Schéma de configuration Azure Diagnostics 1.3, 1.4, 1.5
> [!NOTE]
> Azure Diagnostics est le composant utilisé pour collecter les compteurs de performances et d’autres statistiques d’Azure Virtual Machines, de Virtual Machine Scale Sets, de Service Fabric et de Cloud Services.  Cette page vous concerne uniquement si vous utilisez l’un de ces services.
>

Azure Diagnostics est utilisé conjointement avec d’autres produits de diagnostic Microsoft comme Azure Monitor, Application Insights et Log Analytics.

Le fichier de configuration Azure Diagnostics est utilisé pour définir les valeurs qui sont utilisées pour définir les paramètres de configuration de diagnostic lorsque le moniteur de diagnostic démarre.  

Cette page est valable pour les versions 1.3, 1.4 et 1.5 (Kit de développement logiciel Azure 2.4 et versions postérieures). Les sections de configuration les plus récentes sont commentées pour montrer dans quelle version elles ont été ajoutées.  

 Téléchargez la définition de schéma de fichier de configuration publique en exécutant la commande PowerShell suivante :  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Pour plus d’informations sur les diagnostics Azure, consultez la page [Activation de Diagnostics dans Azure Cloud Service](http://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exemple du fichier de configuration des diagnostics  
 L’exemple suivant montre un fichier de configuration de diagnostic standard :  

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
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
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
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

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

    </WadCfg>  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" />
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" />
    </SecondaryStorageAccounts>
    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  

Voici l’équivalent JSON du fichier de configuration XML précédent.  
```json
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount"
}


{
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

## <a name="reading-this-page"></a>Lecture de cette page  
 Les balises suivantes sont à peu près dans l’ordre indiqué dans l’exemple précédent.  Si vous ne voyez pas de description complète à l’emplacement prévu, recherchez l’élément ou l’attribut dans la page.  

## <a name="common-attribute-types"></a>Types d’attributs courants  
 L’attribut **scheduledTransferPeriod** apparaît dans plusieurs éléments. Il s’agit de l’intervalle entre les transferts planifiés vers le stockage Azure, arrondi à la minute la plus proche. La valeur est un [« Type de données de durée » XML.](http://www.w3schools.com/schema/schema_dtypes_date.asp)

## <a name="diagnostics-configuration-namespace"></a>Espace de noms de configuration de diagnostic  
 L’espace de noms XML du fichier de configuration des diagnostics est :  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="diagnosticsconfiguration-element"></a>Élément DiagnosticsConfiguration  
 Ajouté à la version 1.3.  

 Élément de niveau supérieur du fichier de configuration de diagnostic.  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**PublicConfig**|Obligatoire. Consultez la description sur cette page.|  
|**PrivateConfig**|facultatif. Consultez la description sur cette page.|  
|**IsEnabled**|Booléen. Consultez la description sur cette page.|  

## <a name="publicconfig-element"></a>Élément PublicConfig  
 Décrit la configuration de diagnostic public.  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**WadCfg**|Obligatoire. Consultez la description sur cette page.|  
|**StorageAccount**|Nom du compte de stockage Azure où stocker les données. Cela peut également être spécifié en tant que paramètre lors de l’exécution de l’applet de commande Set-AzureServiceDiagnosticsExtension.|  
|**LocalResourceDirectory**|Répertoire se trouvant sur la machine virtuelle sur laquelle Monitoring Agent stocke les données d’événement. S’il n’est pas défini, le répertoire par défaut est utilisé :<br /><br /> Pour un rôle Worker/web : `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Pour une machine virtuelle : `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Les attributs requis sont :<br /><br /> - **path** - Répertoire sur le système à utiliser par Azure Diagnostics.<br /><br /> - **expandEnvironment** - Contrôle si les variables d’environnement sont développées dans le nom du chemin d’accès.|  

## <a name="wadcfg-element"></a>WadCFG Element  
 Identifie et configure les données de télémétrie à collecter.  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Élément requis.<br /><br /> Les attributs facultatifs sont les suivants :<br /><br /> - **overallQuotaInMB** - Quantité maximale d’espace disque local pouvant être utilisé par les différents types de données de diagnostic collectés par Azure Diagnostics. Le paramètre par défaut est 5 210 Mo.<br /><br /> - **useProxyServer** - Configurer Azure Diagnostics de manière à utiliser les paramètres du serveur proxy comme défini dans les paramètres d’Internet Explorer.|  
|**CrashDumps**|Consultez la description sur cette page.|  
|**DiagnosticInfrastructureLogs**|Permet la collecte des journaux générés par Azure Diagnostics. Les journaux d’infrastructure de diagnostic sont utiles pour le dépannage du système de diagnostic lui-même. Les attributs facultatifs sont les suivants :<br /><br /> - **scheduledTransferLogLevelFilter** - Configure le niveau de gravité minimal des journaux collectés.<br /><br /> - **scheduledTransferPeriod** - Intervalle entre les transferts planifiés vers le stockage Azure, arrondi à la minute la plus proche. La valeur est un [« Type de données de durée » XML.](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  
|**Directories**|Consultez la description sur cette page.|  
|**EtwProviders**|Consultez la description sur cette page.|  
|**Métriques**|Consultez la description sur cette page.|  
|**PerformanceCounters**|Consultez la description sur cette page.|  
|**WindowsEventLog**|Consultez la description sur cette page.|  

## <a name="crashdumps-element"></a>Élément CrashDumps  
 Permet la collecte des vidages sur incident.  

|Attributs|Description|  
|----------------|-----------------|  
|**containerName**|facultatif. Nom du conteneur d’objets blob de votre compte de stockage Azure à utiliser pour stocker les vidages sur incident.|  
|**crashDumpType**|facultatif.  Configure Azure Diagnostics pour collecter les mini-vidages sur incident ou les vidages sur incident complets.|  
|**directoryQuotaPercentage**|facultatif.  Configure le pourcentage de **overallQuotaInMB** à réserver pour les vidages sur incident sur la machine virtuelle.|  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Obligatoire. Définit les valeurs de configuration pour chaque processus.<br /><br /> L’attribut suivant est également requis :<br /><br /> **processName** - Nom du processus pour lequel vous voulez qu’Azure Diagnostics collecte un vidage sur incident.|  

## <a name="directories-element"></a>Élément Directories  
 Permet la collecte du contenu d’un répertoire, des journaux de demandes d’accès ayant échouées IIS et/ou des journaux IIS.  

 Attribut **scheduledTransferPeriod** facultatif. Voir l’explication précédente.  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**DataSources**|Liste de répertoires à analyser.|  
|**FailedRequestLogs**|Incluez cet élément dans la configuration pour permettre la collecte des journaux concernant les demandes ayant échoué sur une application ou un site IIS. Vous devez également activer les options de suivi sous **system.WebServer** dans **Web.config**.|  
|**IISLogs**|Incluez cet élément dans la configuration pour permettre la collecte des journaux IIS :<br /><br /> **containerName** - Nom du conteneur d’objets blob de votre compte de stockage Azure à utiliser pour stocker les vidages sur incident.|  

## <a name="datasources-element"></a>Élément DataSources  
 Liste de répertoires à analyser.  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Obligatoire. Attribut requis :<br /><br /> **containerName** - Nom du conteneur d’objets blob de votre compte de stockage Azure à utiliser pour stocker les fichiers journaux.|  

## <a name="directoryconfiguration-element"></a>Élément DirectoryConfiguration  
 Peut inclure l’élément **Absolute** ou **LocalResource**, mais pas les deux.  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**Absolute**|Chemin d’accès absolu au répertoire à surveiller. Les attributs suivants sont requis :<br /><br /> - **Path** - Chemin d’accès absolu au répertoire à surveiller.<br /><br /> - **expandEnvironment** - Détermine si les variables d’environnement de Path sont développées.|  
|**LocalResource**|Chemin d’accès relatif à une ressource locale à surveiller. Les attributs requis sont :<br /><br /> - **Name** - Nom de la ressource locale contenant le répertoire à surveiller<br /><br /> - **relativePath** - Chemin d’accès relatif au nom qui contient le répertoire à surveiller|  

## <a name="etwproviders-element"></a>Élément EtwProviders  
 Configure la collecte d’événements ETW issus des fournisseurs de manifeste EventSource et/ou ETW.  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Configure la collection d’événements générés à partir de la [classe EventSource](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Attribut requis :<br /><br /> **provider** - Nom de classe de l’événement EventSource.<br /><br /> Les attributs facultatifs sont les suivants :<br /><br /> - **scheduledTransferLogLevelFilter** - Niveau de gravité minimal à transférer vers votre compte de stockage.<br /><br /> - **scheduledTransferPeriod** - Intervalle entre les transferts planifiés vers le stockage Azure, arrondi à la minute la plus proche. La valeur est un [« Type de données de durée » XML.](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Attribut requis :<br /><br /> **provider** -GUID du fournisseur d’événements<br /><br /> Les attributs facultatifs sont les suivants :<br /><br /> - **scheduledTransferLogLevelFilter** - Niveau de gravité minimal à transférer vers votre compte de stockage.<br /><br /> - **scheduledTransferPeriod** - Intervalle entre les transferts planifiés vers le stockage Azure, arrondi à la minute la plus proche. La valeur est un [« Type de données de durée » XML.](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  

## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 Configure la collection d’événements générés à partir de la [classe EventSource](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**DefaultEvents**|Attribut facultatif :<br/><br/> **eventDestination** -Nom de la table dans laquelle stocker les événements|  
|**Event**|Attribut requis :<br /><br /> **id** : ID de l’événement.<br /><br /> Attribut facultatif :<br /><br /> **eventDestination** -Nom de la table dans laquelle stocker les événements|  

## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**DefaultEvents**|Attribut facultatif :<br /><br /> **eventDestination** -Nom de la table dans laquelle stocker les événements|  
|**Event**|Attribut requis :<br /><br /> **id** : ID de l’événement.<br /><br /> Attribut facultatif :<br /><br /> **eventDestination** -Nom de la table dans laquelle stocker les événements|  

## <a name="metrics-element"></a>Élément Metrics  
 Permet de générer une table de compteur de performance optimisée pour les requêtes rapides. Chaque compteur de performance défini dans l’élement **PerformanceCounters** est stocké dans la table Metrics et dans la table Performance Counter.  

 L’attribut **resourceId** est requis.  Il s’agit de l’ID de ressource de la machine virtuelle sur laquelle vous déployez Azure Diagnostics. Obtenez le **resourceID** à partir du [portail Azure](https://portal.azure.com). Sélectionnez **Parcourir** -> **Groupe de ressources** -> **<>\>**. Cliquez sur la vignette **Propriétés** et copiez la valeur à partir du champ **ID**.  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**MetricAggregation**|Attribut requis :<br /><br /> **scheduledTransferPeriod** - Intervalle entre les transferts planifiés vers le stockage Azure, arrondi à la minute la plus proche. La valeur est un [« Type de données de durée » XML.](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  

## <a name="performancecounters-element"></a>Élément PerformanceCounters  
 Permet la collecte des compteurs de performance.  

 Attribut facultatif :  

 Attribut **scheduledTransferPeriod** facultatif. Voir l’explication précédente.

|Élément enfant|Description|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|Les attributs suivants sont requis :<br /><br /> - **counterSpecifier** - Nom du compteur de performance. Par exemple, `\Processor(_Total)\% Processor Time`. Pour obtenir une liste des compteurs de performances se trouvant sur votre hôte, exécutez la commande `typeperf`.<br /><br /> - **sampleRate** - Fréquence à laquelle le compteur doit être échantillonné.<br /><br /> Attribut facultatif :<br /><br /> **unit** -Unité de mesure du compteur.|  

## <a name="windowseventlog-element"></a>Élément WindowsEventLog  
 Permet la collecte des journaux des événements Windows.  

 Attribut **scheduledTransferPeriod** facultatif. Voir l’explication précédente.  

|Élément enfant|Description|  
|-------------------|-----------------|  
|**DataSource**|Journaux des événements Windows à collecter. Attribut requis :<br /><br /> **name** - Requête XPath décrivant les événements windows à collecter. Par exemple :<br /><br /> `Application!*[Application[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[Security[(Level <= 3)]`<br /><br /> Pour collecter tous les événements, spécifiez « * ».|  

## <a name="logs-element"></a>Élément Logs  
 Présent dans la version 1.0 et 1.1. Absent dans la version 1.2. Rajouté dans la version 1.3.  

 Définit la configuration de la mémoire tampon des journaux Azure de base.  

|Attribut|Type|Description|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|facultatif. Définit la quantité maximale de stockage du système de fichiers disponible pour les données spécifiées.<br /><br /> La valeur par défaut est 0.|  
|**scheduledTransferLogLevelFilterr**|**string**|facultatif. Définit le niveau de gravité minimal des entrées de journal transférées. La valeur par défaut qui transfère tous les journaux est **Undefined**. Les autres valeurs possibles sont, du plus informatif au moins informatif : **Détaillé**, **Informations**, **Avertissement**, **Erreur**, **Critique**.|  
|**scheduledTransferPeriod**|**duration**|facultatif. Définit l’intervalle entre les transferts planifiés de données, arrondi à la minute la plus proche.<br /><br /> La valeur par défaut est PT0S.|  
|**sinks** - Ajouté à la version 1.5|**string**|facultatif. Pointe vers un emplacement de récepteur permettant d’envoyer également des données de diagnostic. Par exemple, Application Insights.|  

## <a name="sinksconfig-element"></a>Élément SinksConfig  
 Liste d’emplacements vers lesquels envoyer des données de diagnostic et la configuration associée à ces emplacements.  

|Nom de l’élément|Description|  
|------------------|-----------------|  
|**Section sink**|Consultez la description sur cette page.|  

## <a name="sink-element"></a>Élément Sink  
 Ajouté à la version 1.5.  

 Définit les emplacements vers lesquels envoyer des données de diagnostic. Par exemple, le service Application Insights.  

|Attribut|Type|Description|  
|---------------|----------|-----------------|  
|**name**|string|Chaîne identifiant le nom du récepteur.|  

|Élément|Type|Description|  
|-------------|----------|-----------------|  
|**Application Insights**|string|Utilisé uniquement lors de l’envoi de données à Application Insights. Contient la clé d’instrumentation d’un compte Application Insights actif auquel vous avez accès.|  
|**Canaux**|string|Un pour chaque filtrage supplémentaire qui diffuse cela en continu|  

## <a name="channels-element"></a>Élément Channels  
 Ajouté à la version 1.5.  

 Définit les filtres pour les flux de données de journaux passant par un récepteur.  

|Élément|Type|Description|  
|-------------|----------|-----------------|  
|**Channel**|string|Consultez la description sur cette page.|  

## <a name="channel-element"></a>Élément Channel  
 Ajouté à la version 1.5.  

 Définit les emplacements vers lesquels envoyer des données de diagnostic. Par exemple, le service Application Insights.  

|Attributs|Type|Description|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|Définit le niveau de gravité minimal des entrées de journal transférées. La valeur par défaut qui transfère tous les journaux est **Undefined**. Les autres valeurs possibles sont, du plus informatif au moins informatif : **Détaillé**, **Informations**, **Avertissement**, **Erreur**, **Critique**.|  
|**name**|**string**|Nom unique du canal auquel faire référence|  

## <a name="privateconfig-element"></a>Élément PrivateConfig  
 Ajouté à la version 1.3.  

 Facultatif  

 Stocke les détails privés du compte de stockage (nom, clé et point de terminaison). Cette information est envoyée à la machine virtuelle, mais ne peut pas être récupérée à partir de celui-ci.  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**StorageAccount**|Compte de stockage à utiliser. Les attributs suivants sont requis :<br /><br /> - **name** - Nom du compte de stockage.<br /><br /> - **key** - Clé du compte de stockage.<br /><br /> - **endpoint** - Point de terminaison permettant d’accéder au compte de stockage.|  

## <a name="isenabled-element"></a>Élément IsEnabled  
 Booléen. Utilisez `true` pour activer les diagnostics ou `false` pour les désactiver.

