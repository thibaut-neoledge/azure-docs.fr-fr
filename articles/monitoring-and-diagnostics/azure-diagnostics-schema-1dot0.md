---
title: "Schéma de configuration Azure Diagnostics 1.0 | Microsoft Docs"
description: "Applicable UNIQUEMENT si vous utilisez le Kit de développement logiciel (SDK) Azure 2.4 et les versions antérieures avec Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric ou Cloud Services."
services: multiple
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 8c9c9dea1248205aa6303e11e1166d5d38786c1b
ms.openlocfilehash: 92bb7ca3414962f18da8505b87ca23f0b704e811


---
# <a name="azure-diagnostics-10-configuration-schema"></a>Schéma de configuration Azure Diagnostics 1.0
> [!NOTE]
> Azure Diagnostics est le composant utilisé pour collecter les compteurs de performances et d’autres statistiques d’Azure Virtual Machines, de Virtual Machine Scale Sets, de Service Fabric et de Cloud Services.  Cette page vous concerne uniquement si vous utilisez l’un de ces services.
>

Azure Diagnostics est utilisé conjointement avec d’autres produits de diagnostic Microsoft comme Azure Monitor, Application Insights et Log Analytics.

Le fichier de configuration Azure Diagnostics définit les valeurs qui sont utilisées pour initialiser le moniteur de diagnostics. Ce fichier est utilisé pour initialiser les paramètres de configuration de diagnostic lorsque le moniteur de diagnostic démarre.  

 Par défaut, le fichier de schéma de configuration Azure Diagnostics est installé dans le répertoire `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas`. Remplacez `<version>` par la version installée du [Kit de développement logiciel (SDK) Azure](http://www.windowsazure.com/develop/downloads/).  

> [!NOTE]
>  Le fichier de configuration de diagnostic est généralement utilisé avec les tâches de démarrage qui requièrent la collecte de données de diagnostic au début du processus de démarrage. Pour plus d’informations sur l’utilisation d’Azure Diagnostics, consultez [Collect Logging Data by Using Azure Diagnostics](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7) (Collecte de données de journalisation à l’aide d’Azure Diagnostics).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exemple du fichier de configuration des diagnostics  
 L’exemple suivant montre un fichier de configuration de diagnostic standard :  

```xml  
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"  
      configurationChangePollInterval="PT1M"  
      overallQuotaInMB="4096">  
   <DiagnosticInfrastructureLogs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  
   <Logs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  

   <Directories bufferQuotaInMB="1024"   
      scheduledTransferPeriod="PT1M">  

      <!-- These three elements specify the special directories   
           that are set up for the log types -->  
      <CrashDumps container="wad-crash-dumps" directoryQuotaInMB="256" />  
      <FailedRequestLogs container="wad-frq" directoryQuotaInMB="256" />  
      <IISLogs container="wad-iis" directoryQuotaInMB="256" />  

      <!-- For regular directories the DataSources element is used -->  
      <DataSources>  
         <DirectoryConfiguration container="wad-panther" directoryQuotaInMB="128">  
            <!-- Absolute specifies an absolute path with optional environment expansion -->  
            <Absolute expandEnvironment="true" path="%SystemRoot%\system32\sysprep\Panther" />  
         </DirectoryConfiguration>  
         <DirectoryConfiguration container="wad-custom" directoryQuotaInMB="128">  
            <!-- LocalResource specifies a path relative to a local   
                 resource defined in the service definition -->  
            <LocalResource name="MyLoggingLocalResource" relativePath="logs" />  
         </DirectoryConfiguration>  
      </DataSources>  
   </Directories>  

   <PerformanceCounters bufferQuotaInMB="512" scheduledTransferPeriod="PT1M">  
      <!-- The counter specifier is in the same format as the imperative   
           diagnostics configuration API -->  
      <PerformanceCounterConfiguration   
         counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT5S" />  
   </PerformanceCounters>  

   <WindowsEventLog bufferQuotaInMB="512"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M">  
      <!-- The event log name is in the same format as the imperative   
           diagnostics configuration API -->  
      <DataSource name="System!*" />  
   </WindowsEventLog>  
</DiagnosticMonitorConfiguration>  
```  

## <a name="diagnosticsconfiguration-namespace"></a>Espace de noms DiagnosticsConfiguration  
 L’espace de noms XML du fichier de configuration des diagnostics est :  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>Éléments du schéma  
 Le fichier de configuration de diagnostic inclut les éléments suivants.


## <a name="diagnosticmonitorconfiguration-element"></a>Élément DiagnosticMonitorConfiguration  
Élément de niveau supérieur du fichier de configuration de diagnostic.  

Attributs :

|Attribut  |Type   |Requis| Default | Description|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|duration|Facultatif | PT1M| Spécifie l’intervalle auquel le moniteur de diagnostic s’enquiert des modifications de configuration de diagnostic.|  
|**overallQuotaInMB**|unsignedInt|Facultatif| 4 000 Mo. La valeur indiquée ne doit pas dépasser ce montant |Quantité totale de stockage du système de fichiers allouée pour la journalisation de toutes les mémoires tampons.|  

## <a name="diagnosticinfrastructurelogs-element"></a>Élément DiagnosticInfrastructureLogs  
Définit la configuration de la mémoire tampon pour les journaux générés par l’infrastructure de diagnostic sous-jacente.

Élément parent : [Élément DiagnosticMonitorConfiguration](#DiagnosticMonitorConfiguration).  

Attributs :

|Attribut|Type|Description|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|facultatif. Définit la quantité maximale de stockage du système de fichiers disponible pour les données spécifiées.<br /><br /> La valeur par défaut est 0.|  
|**scheduledTransferLogLevelFilter**|string|facultatif. Définit le niveau de gravité minimal des entrées de journal transférées. La valeur par défaut est **Non défini**. Les autres valeurs possibles sont **Détaillé**, **Informations**, **Avertissement**, **Erreur**, et **Critique**.|  
|**scheduledTransferPeriod**|duration|facultatif. Définit l’intervalle entre les transferts planifiés de données, arrondi à la minute la plus proche.<br /><br /> La valeur par défaut est PT0S.|  

## <a name="logs-element"></a>Élément Logs  
 Définit la configuration de la mémoire tampon des journaux Azure de base.

 Élément parent : [Élément DiagnosticMonitorConfiguration](#DiagnosticMonitorConfiguration).  

Attributs :  

|Attribut|Type|Description|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|facultatif. Définit la quantité maximale de stockage du système de fichiers disponible pour les données spécifiées.<br /><br /> La valeur par défaut est 0.|  
|**scheduledTransferLogLevelFilter**|string|facultatif. Définit le niveau de gravité minimal des entrées de journal transférées. La valeur par défaut est **Non défini**. Les autres valeurs possibles sont **Détaillé**, **Informations**, **Avertissement**, **Erreur**, et **Critique**.|  
|**scheduledTransferPeriod**|duration|facultatif. Définit l’intervalle entre les transferts planifiés de données, arrondi à la minute la plus proche.<br /><br /> La valeur par défaut est PT0S.|  

## <a name="directories-element"></a>Élément Directories  
Définit la configuration de la mémoire tampon pour les journaux basés sur des fichiers que vous pouvez définir.

Élément parent : [Élément DiagnosticMonitorConfiguration](#DiagnosticMonitorConfiguration).  


Attributs :  

|Attribut|Type|Description|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|facultatif. Définit la quantité maximale de stockage du système de fichiers disponible pour les données spécifiées.<br /><br /> La valeur par défaut est 0.|  
|**scheduledTransferPeriod**|duration|facultatif. Définit l’intervalle entre les transferts planifiés de données, arrondi à la minute la plus proche.<br /><br /> La valeur par défaut est PT0S.|  

## <a name="crashdumps-element"></a>Élément CrashDumps  
 Définit le répertoire de vidages sur incident.

 Élément parent : [élément Directories](#Directories).  

Attributs :  

|Attribut|Type|Description|  
|---------------|----------|-----------------|  
|**container**|string|Nom du conteneur dans lequel le contenu du répertoire doit être transféré.|  
|**directoryQuotaInMB**|unsignedInt|facultatif. Définit la taille maximale du répertoire en mégaoctets.<br /><br /> La valeur par défaut est 0.|  

## <a name="failedrequestlogs-element"></a>Élément FailedRequestLogs  
 Définit le répertoire de journaux des demandes ayant échoué.

 Élément parent : [élément Directories](#Directories).  

Attributs :  

|Attribut|Type|Description|  
|---------------|----------|-----------------|  
|**container**|string|Nom du conteneur dans lequel le contenu du répertoire doit être transféré.|  
|**directoryQuotaInMB**|unsignedInt|facultatif. Définit la taille maximale du répertoire en mégaoctets.<br /><br /> La valeur par défaut est 0.|  

##  <a name="iislogs-element"></a>Élément IISLogs  
 Définit le répertoire des journaux IIS.

 Élément parent : [élément Directories](#Directories).  

Attributs :  

|Attribut|Type|Description|  
|---------------|----------|-----------------|  
|**container**|string|Nom du conteneur dans lequel le contenu du répertoire doit être transféré.|  
|**directoryQuotaInMB**|unsignedInt|facultatif. Définit la taille maximale du répertoire en mégaoctets.<br /><br /> La valeur par défaut est 0.|  

## <a name="datasources-element"></a>Élément DataSources  
 Définit zéro ou plusieurs répertoires de journaux supplémentaires.

 Élément parent : [élément Directories](#Directories).

## <a name="directoryconfiguration-element"></a>Élément DirectoryConfiguration  
 Définit le répertoire de fichiers journaux à surveiller.

 Élément parent : [élément DataSources](#DataSources).

Attributs :

|Attribut|Type|Description|  
|---------------|----------|-----------------|  
|**container**|string|Nom du conteneur dans lequel le contenu du répertoire doit être transféré.|  
|**directoryQuotaInMB**|unsignedInt|facultatif. Définit la taille maximale du répertoire en mégaoctets.<br /><br /> La valeur par défaut est 0.|  

## <a name="absolute-element"></a>Élément Absolute  
 Définit un chemin d’accès absolu du répertoire à surveiller avec une extension d’environnement facultative.

 Élément parent : [élément DirectoryConfiguration](#DirectoryConfiguration).  

Attributs :  

|Attribut|Type|Description|  
|---------------|----------|-----------------|  
|**path**|string|Obligatoire. Chemin d’accès absolu au répertoire à surveiller.|  
|**expandEnvironment**|booléenne|Obligatoire. Si la valeur **true** est attribuée, les variables d’environnement du chemin d’accès sont développées.|  

## <a name="localresource-element"></a>Élément LocalResource  
 Définit un chemin d’accès relatif à une ressource locale spécifiée dans la définition de service.

 Élément parent : [élément DirectoryConfiguration](#DirectoryConfiguration).  

Attributs :  

|Attribut|Type|Description|  
|---------------|----------|-----------------|  
|**name**|string|Obligatoire. Nom de la ressource locale qui contient le répertoire à surveiller.|  
|**relativePath**|string|Obligatoire. Chemin d’accès relatif à la ressource locale à surveiller.|  

## <a name="performancecounters-element"></a>Élément PerformanceCounters  
 Définit le chemin d’accès au compteur de performance à collecter.

 Élément parent : [Élément DiagnosticMonitorConfiguration](#DiagnosticMonitorConfiguration).


 Attributs :  

|Attribut|Type|Description|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|facultatif. Définit la quantité maximale de stockage du système de fichiers disponible pour les données spécifiées.<br /><br /> La valeur par défaut est 0.|  
|**scheduledTransferPeriod**|duration|facultatif. Définit l’intervalle entre les transferts planifiés de données, arrondi à la minute la plus proche.<br /><br /> La valeur par défaut est PT0S.|  

## <a name="performancecounterconfiguration-element"></a>Élément PerformanceCounterConfiguration  
 Définit le compteur de performance à collecter.

 Élément parent : [élément PerformanceCounters](#PerformanceCounters).  

 Attributs :  

|Attribut|Type|Description|  
|---------------|----------|-----------------|  
|**counterSpecifier**|string|Obligatoire. Chemin d’accès au compteur de performance à collecter.|  
|**sampleRate**|duration|Obligatoire. Vitesse à laquelle le compteur de performance doit être collecté.|  

## <a name="windowseventlog-element"></a>Élément WindowsEventLog  
 Définit les journaux des événements à surveiller.

 Élément parent : [Élément DiagnosticMonitorConfiguration](#DiagnosticMonitorConfiguration).

  Attributs :

|Attribut|Type|Description|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|facultatif. Définit la quantité maximale de stockage du système de fichiers disponible pour les données spécifiées.<br /><br /> La valeur par défaut est 0.|  
|**scheduledTransferLogLevelFilter**|string|facultatif. Définit le niveau de gravité minimal des entrées de journal transférées. La valeur par défaut est **Non défini**. Les autres valeurs possibles sont **Détaillé**, **Informations**, **Avertissement**, **Erreur**, et **Critique**.|  
|**scheduledTransferPeriod**|duration|facultatif. Définit l’intervalle entre les transferts planifiés de données, arrondi à la minute la plus proche.<br /><br /> La valeur par défaut est PT0S.|  

## <a name="datasource-element"></a>Élément DataSource  
 Définit les journaux des événements à surveiller.

 Élément parent : [élément WindowsEventLog](#windowsEventLog).  

 Attributs :

|Attribut|Type|Description|  
|---------------|----------|-----------------|  
|**name**|string|Obligatoire. Expression XPath spécifiant le journal à collecter.|  



<!--HONumber=Jan17_HO5-->


