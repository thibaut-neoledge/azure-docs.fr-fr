---
title: "Résolution des problèmes des diagnostics Azure | Microsoft Docs"
description: "Résolution des problèmes lors de l'utilisation des diagnostics Azure dans Azure Virtual Machines, Service Fabric ou Cloud Services."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 66469bce-d457-4d1e-b550-a08d2be4d28c
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/12/2017
ms.author: robb
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: a0cb529836b14df71e83616f4f625a002c535b7b
ms.contentlocale: fr-fr
ms.lasthandoff: 08/24/2017

---
# <a name="azure-diagnostics-troubleshooting"></a>Résolution des problèmes des diagnostics Azure
Informations de résolution des problèmes liés à l’utilisation des diagnostics Azure. Pour plus d’informations sur les diagnostics Azure, voir [Présentation des diagnostics Azure](azure-diagnostics.md).

## <a name="logical-components"></a>Composants logiques
**Lanceur du plug-in Diagnostics (DiagnosticsPluginLauncher.exe)** : lance l’extension Azure Diagnostics. Sert de point d’entrée de processus.

**Plug-in Diagnostics (DiagnosticsPlugin.exe)** : processus principal qui est démarré par le lanceur indiqué ci-dessus et qui configure l’agent de surveillance, le lance et gère sa durée de vie. 

**Agent de surveillance (processus MonAgent\*.exe)** : ces processus effectuent la majeure partie du travail, c.-à-d. la surveillance, la collecte et le transfert des données de diagnostic.  

## <a name="logartifact-paths"></a>Chemins d’accès des journaux/artefacts
Voici les chemins d’accès aux journaux et artefacts importants. Nous y faisons référence dans la suite du document :
### <a name="cloud-services"></a>Services cloud
| Artefact | Chemin |
| --- | --- |
| **Fichier de configuration d’Azure Diagnostics** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\Config.txt |
| **Fichiers journaux** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Magasin local pour les données de diagnostic** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Tables |
| **Fichier de configuration de l’agent de surveillance** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Package d’extension Azure Diagnostics** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **Chemin d’accès à l’utilitaire de collecte des journaux** | %SystemDrive%\Packages\GuestAgent\ |
| **Fichier journal MonAgentHost** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Machines virtuelles
| Artefact | Chemin |
| --- | --- |
| **Fichier de configuration d’Azure Diagnostics** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\RuntimeSettings |
| **Fichiers journaux** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Logs\ |
| **Magasin local pour les données de diagnostic** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **Fichier de configuration de l’agent de surveillance** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Fichier d’état** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Status |
| **Package d’extension Azure Diagnostics** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Chemin d’accès à l’utilitaire de collecte des journaux** | C:\WindowsAzure\Packages |
| **Fichier journal MonAgentHost** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-show-in-azure-portal"></a>Les données métriques ne s’affichent pas dans le portail Azure
Azure Diagnostics fournit un ensemble de données métriques, qui peuvent être affichées dans le portail Azure. Si vous avez des problèmes liés à l’affichage des données dans le portail, vérifiez le compte de stockage des diagnostics -> table WADMetrics\* pour voir si les enregistrements métriques correspondants sont présents. Ici, PartitionKey de la table est l’ID de ressource des machines virtuelles ou du groupes de machines virtuelles identiques et le RowKey est le nom métrique, autrement dit, le nom du compteur de performances.

Si l’ID de ressource est incorrecte, vérifiez Configuration de Diagnostics -> Mesures -> ResourceId pour voir si l’ID de ressource est défini correctement.

S’il n’existe aucune donnée pour la mesure spécifique, vérifiez Configuration de Diagnostics -> PerformanceCounter pour voir si la mesure (compteur de performances) est incluse. Les compteurs suivants sont activés par défaut.
- \Processus(_Total)\% Temps processeur
- \Memory\Octets disponibles
- \ASP.NET Applications(__Total__)\Requests/Sec
- \ASP.NET Applications(__Total__)\Errors Total/Sec
- \ASP.NET\Demandes en file d’attente
- \ASP.NET\Demandes rejetées
- \Processor(w3wp)\% Temps processeur
- \Processus(w3wp) \Octets privés
- \Processus(WaIISHost)\% Temps processeur
- \Processus(WaIISHost)\Octets privés
- \Processus(WaWorkerHost)\% Temps processeur
- \Processus(WaWorkerHost)\Octets privés
- \Mémoire\Défauts de page/s
- \.Mémoire NET CLR(_Global_)\% Temps en GC
- \LogicalDisk(C:)\Disk Write Bytes/sec
- \LogicalDisk(C:)\Disk Read Bytes/sec
- \LogicalDisk(D:)\Disk Write Bytes/sec
- \LogicalDisk(D:)\Disk Read Bytes/sec

Si la configuration est correctement définie mais que vous ne pouvez toujours pas voir les données métriques, suivez les directives ci-dessous pour l’investigation future.


## <a name="azure-diagnostics-is-not-starting"></a>Azure Diagnostics ne démarre pas
Regardez les fichiers **DiagnosticsPluginLauncher.log** et **DiagnosticsPlugin.log** à l’emplacement des fichiers journaux indiqués ci-dessous pour en savoir plus sur les raisons de l’échec du démarrage des diagnostics. 

Si ces journaux indiquent `Monitoring Agent not reporting success after launch`, cela signifie que le lancement de MonAgentHost.exe a échoué. Consultez les journaux correspondant à l’emplacement indiqué pour `MonAgentHost log file` dans la section ci-dessus.

La dernière ligne des fichiers journaux contient le code de sortie.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Si le code de sortie est **négatif**, reportez-vous au [tableau des codes de sortie](#azure-diagnostics-plugin-exit-codes) dans la section [Références](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Les données des diagnostics ne sont pas consignées dans Azure Storage
Déterminez si aucune donnée ne s’affiche ou si seules certaines d’entre elles ne s’affichent pas.

### <a name="diagnostics-infrastructure-logs"></a>Journaux d’infrastructure de diagnostics
Les journaux d’infrastructure de diagnostics correspondent à l’emplacement de journalisation des erreurs par Azure Diagnostics. Vérifiez que vous avez activé ([Guide pratique](#how-to-check-diagnostics-extension-configuration)) la capture des journaux d’infrastructure de diagnostics dans votre configuration, puis recherchez rapidement les erreurs correspondantes, affichées dans le tableau `DiagnosticInfrastructureLogsTable` de votre compte de stockage configuré.

### <a name="no-data-is-showing-up"></a>Aucune donnée n’est affichée
La principale cause de données d’événement totalement manquantes est une définition incorrecte des informations de compte de stockage.

Solution : corrigez la configuration du plug-in Diagnostics et réinstallez-le.

Si le compte de stockage est configuré correctement, activez le Bureau à distance sur la machine et assurez-vous de l’exécution de DiagnosticsPlugin.exe et de MonAgentCore.exe. Si vous n’êtes pas en cours d’exécution, suivez [Azure Diagnostics ne démarre pas](#azure-diagnostics-is-not-starting). Si les processus sont exécutés, passez à la section [Capture locale des données](#is-data-getting-captured-locally) et suivez ce guide à partir de là.

### <a name="part-of-the-data-is-missing"></a>Une partie des données est manquante
Si vous obtenez des données, mais pas d’autres, cela signifie que la collecte de données et/ou le pipeline de transfert sont définis correctement. Suivez les sous-sections ci-après pour déterminer si l’origine du problème est liée aux éléments suivants :
#### <a name="is-collection-configured"></a>Configuration de la collecte : 
La configuration des diagnostics contient la partie qui demande la collecte d’un type particulier de données. [Vérifiez votre configuration](#how-to-check-diagnostics-extension-configuration) pour vous assurer que vous ne recherchez pas des données que vous n’avez pas configurées pour la collecte.
#### <a name="is-the-host-generating-data"></a>Génération des données par l’hôte :
- **Compteurs de performances** : ouvrez PerfMon et vérifiez le compteur.
- **Journaux de suivi** : activez le Bureau à distance sur la machine virtuelle et ajoutez un élément TextWriterTraceListener dans le fichier de configuration de l’application.  Consultez http://msdn.microsoft.com/library/sk36c28t.aspx pour configurer l’écouteur de texte.  Vérifiez que l’élément `<trace>` a la valeur `<trace autoflush="true">`.<br />
Si vous ne voyez pas de journaux de suivi générés, suivez les étapes décrites dans la section [En savoir plus sur les journaux de suivi manquants](#more-about-trace-logs-missing).
- **Traces ETW** : activez le Bureau à distance sur la machine virtuelle et installez PerfView.  Dans PerfView, exécutez Fichier -> Commande utilisateur -> Écouter etwprovider1, etwprovider2, etc.  Notez que la commande Écouter respecte la casse et qu’il ne peut y avoir d’espace entre les listes séparées par des virgules des fournisseurs ETW.  En cas d’échec de l’exécution de la commande, vous pouvez cliquer sur le bouton « Journal » dans l’angle inférieur droit de l’outil PerfView pour voir ce qui était attendu et le résultat final de l’exécution.  En supposant que l’entrée soit correcte, une nouvelle fenêtre s’affiche, et vous commencez à voir les traces ETW dans les quelques secondes qui suivent.
- **Journaux des événements** : activez le Bureau à distance sur la machine virtuelle. Ouvrez `Event Viewer` et assurez-vous que les événements existent.
#### <a name="is-data-getting-captured-locally"></a>Capture locale des données :
À présent, vérifiez que les données sont capturées localement.
Les données sont stockées localement dans les fichiers `*.tsf` dans [le magasin local des données de diagnostic](#log-artifacts-path). Différents types de journaux sont collectés dans différents fichiers `.tsf`. Les noms sont semblables à ceux des tableaux du stockage Azure. Par exemple, `Performance Counters` est collecté dans le fichier `PerformanceCountersTable.tsf`, et les journaux des événements sont collectés dans le fichier `WindowsEventLogsTable.tsf`. Suivez les instructions indiquées dans la section [Extraction locale des journaux](#local-log-extraction) pour ouvrir les fichiers de la collection locale, et assurez-vous qu’ils sont collectés sur le disque.

Si les journaux collectés localement ne s’affichent pas et que vous avez déjà vérifié que l’hôte génère des données, il s’agit probablement d’un problème de configuration. Vérifiez attentivement la section appropriée de votre configuration. Passez également en revue la configuration générée pour MonitoringAgent [MaConfig.xml](#log-artifacts-path) et vérifiez qu’il existe une section décrivant la source de journal appropriée et qu’elle n’est pas perdue au cours de la conversion entre la configuration d’Azure Diagnostics et la configuration de l’agent de surveillance.
#### <a name="is-data-getting-transferred"></a>Transfert des données :
Si vous avez vérifié que les données sont capturées localement, mais qu’elles n’apparaissent toujours pas dans votre compte de stockage : 
- Avant toute chose, vérifiez que vous avez fourni un compte de stockage correct et que vous n’avez pas renouvelé les clés, etc. correspondant au compte de stockage donné. Pour les services cloud, il arrive parfois que les utilisateurs ne mettent pas à jour le paramètre `useDevelopmentStorage=true`.
- Si le compte de stockage fourni est correct. Vérifiez que vous ne rencontrez pas certaines restrictions réseau qui n’autorisent pas les composants à atteindre les points de terminaison de stockage public. Pour ce faire, vous pouvez activer le Bureau à distance sur la machine et essayer d’écrire dans le même compte de stockage vous-même.
- Pour finir, vous pouvez essayer de voir les échecs signalés par l’agent de surveillance. L’agent de surveillance écrit ses journaux dans le fichier `maeventtable.tsf`, qui est situé dans [le magasin local des données de diagnostic](#log-artifacts-path). Suivez les instructions contenues dans la section [Extraction locale des journaux](#local-log-extraction) pour ouvrir ce fichier et essayez de déterminer si des `errors` indiquent des échecs de lecture des fichiers locaux ou d’écriture dans le stockage.

### <a name="capturing--archiving-logs"></a>Capture/Archivage des journaux
Après avoir suivi les étapes indiquées ci-dessus, vous n’avez pas pu déterminer ce qui était incorrect, et vous songez à contacter le support technique. La première chose qui peut vous être demandée est de collecter les journaux à partir de votre machine. Vous pouvez gagner du temps en effectuant vous-même cette procédure. Exécutez l’utilitaire `CollectGuestLogs.exe` dans le [chemin d’accès à l’utilitaire de collecte des journaux](#log-artifacts-path). Un fichier zip contenant tous les journaux Azure correspondants est généré dans le même dossier.

## <a name="diagnostics-data-tables-not-found"></a>Tables des données de diagnostic introuvables
Les tables de stockage Azure qui contiennent les événements ETW sont nommées à l’aide du code suivant :

```C#
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

Voici un exemple :

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
Avec génération de 4 tables :

| Événement | Nom de la table |
| --- | --- |
| provider=”prov1” &lt;Event id=”1” /&gt; |WADEvent+MD5(“prov1”)+”1” |
| provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt; |WADdest1 |
| provider=”prov1” &lt;DefaultEvents /&gt; |WADDefault+MD5(“prov1”) |
| provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt; |WADdest2 |

## <a name="references"></a>Références

### <a name="how-to-check-diagnostics-extension-configuration"></a>Vérification de la configuration de l’extension Diagnostics
Pour vérifier la configuration de l’extension, le plus simple est d’accéder à http://resources.azure.com, puis à la machine virtuelle ou au service cloud sur lesquels l’extension Azure Diagnostics (IaaSDiagnostics/PaaDiagnostics) pose question.

Vous pouvez également activer le Bureau à distance sur la machine et regarder le fichier de configuration d’Azure Diagnostics décrit dans la section appropriée [ici](#log-artifacts-path).

Dans les deux cas, recherchez **Microsoft.Azure.Diagnostics**, puis le champ **xmlCfg** ou **WadCfg**. 

Dans le cas de la machine virtuelle, si le champ WadCfg est présent, cela signifie que la configuration est au format JSON. Si le champ xmlCfg est présent, cela signifie que la configuration est au format XML et codée en base64. Vous devez [la décoder](http://www.bing.com/search?q=base64+decoder) pour afficher le code XML chargé par l’extension Diagnostics.

Pour le rôle de service cloud, si vous sélectionnez la configuration à partir du disque, les données sont encodées en Base64. Vous devrez donc [les décoder](http://www.bing.com/search?q=base64+decoder) pour afficher le code XML qui a été chargé par l’extension Diagnostics.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Codes de sortie du plug-in Azure Diagnostics
Le plug-in renvoie les codes de sortie suivants :

| Code de sortie | Description |
| --- | --- |
| 0 |Vous avez réussi ! |
| -1 |Erreur générique. |
| -2 |Impossible de charger le fichier rcf.<p>Cette erreur interne ne doit se produire que si le lanceur du plug-in d'agent invité est appelé manuellement et de manière incorrecte sur la machine virtuelle. |
| -3 |Impossible de charger le fichier de configuration Diagnostics.<p><p>Solution : cette erreur se produit lorsqu'un fichier de configuration ne passe pas la validation de schéma. La solution consiste à fournir un fichier de configuration qui est conforme au schéma. |
| -4 |Une autre instance de l'agent de surveillance Diagnostics utilise déjà le répertoire de ressources local.<p><p>Solution : spécifiez une valeur différente pour **LocalResourceDirectory**. |
| -6 |Le lanceur du plug-in d'agent invité a tenté de démarrer Diagnostics avec une ligne de commande non valide.<p><p>Cette erreur interne ne doit se produire que si le lanceur du plug-in d'agent invité est appelé manuellement et de manière incorrecte sur la machine virtuelle. |
| -10 |Le plug-in Diagnostics s'est terminé avec une exception non prise en charge. |
| -11 |L'agent invité n'a pas pu créer le processus responsable du lancement et de la surveillance de l'agent de surveillance.<p><p>Solution : vérifiez que les ressources système disponibles sont suffisantes pour lancer de nouveaux processus.<p> |
| -101 |Arguments invalides lors de l'appel du plug-in Diagnostics.<p><p>Cette erreur interne ne doit se produire que si le lanceur du plug-in d'agent invité est appelé manuellement et de manière incorrecte sur la machine virtuelle. |
| -102 |Le processus du plug-in ne peut pas démarrer tout seul.<p><p>Solution : vérifiez que les ressources système disponibles sont suffisantes pour lancer de nouveaux processus. |
| -103 |Le processus du plug-in ne peut pas démarrer tout seul. Plus précisément, il n'est pas capable de créer l'objet enregistreur d'événements.<p><p>Solution : vérifiez que les ressources système disponibles sont suffisantes pour lancer de nouveaux processus. |
| -104 |Impossible de charger le fichier rcf fourni par l'agent invité.<p><p>Cette erreur interne ne doit se produire que si le lanceur du plug-in d'agent invité est appelé manuellement et de manière incorrecte sur la machine virtuelle. |
| -105 |Le plug-in Diagnostics ne peut pas ouvrir le fichier de configuration Diagnostics.<p><p>Cette erreur interne ne doit se produire que si le plug-in Diagnostics est appelé manuellement et de manière incorrecte sur la machine virtuelle. |
| -106 |Impossible de lire le fichier de configuration Diagnostics.<p><p>Solution : cette erreur se produit lorsqu'un fichier de configuration ne passe pas la validation de schéma. La solution consiste alors à fournir un fichier de configuration qui est conforme au schéma. Voir [Vérification de la configuration de l’extension Diagnostics](#how-to-check-diagnostics-extension-configuration). |
| -107 |La transmission du répertoire de ressources à l'agent de surveillance n'est pas valide.<p><p>Cette erreur interne ne doit se produire que si l'agent de surveillance est appelé manuellement et de manière incorrecte sur la machine virtuelle.</p> |
| -108 |Impossible de convertir le fichier de configuration Diagnostics dans le fichier de configuration de l'agent de surveillance.<p><p>Cette erreur interne ne doit se produire que si le plug-in Diagnostics est appelé manuellement avec un fichier de configuration non valide. |
| -110 |Erreur de configuration générale de Diagnostics.<p><p>Cette erreur interne ne doit se produire que si le plug-in Diagnostics est appelé manuellement avec un fichier de configuration non valide. |
| -111 |Impossible de démarrer l’agent de surveillance.<p><p>Solution : vérifiez que les ressources système disponibles sont suffisantes pour lancer de nouveaux processus. |
| -112 |Erreur générale |

### <a name="local-log-extraction"></a>Extraction locale des journaux
L’agent de surveillance collecte les journaux et artefacts en tant que fichiers `.tsf`. Le fichier `.tsf` n’est pas lisible, mais vous pouvez le convertir en fichier `.csv`, comme suit : 

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Un nouveau fichier appelé `<relevantLogFile>.csv` est créé dans le même chemin d’accès que le fichier `.tsf` correspondant.

**REMARQUE** : vous devez exécuter cet utilitaire uniquement dans le fichier .tsf principal (par exemple, PerformanceCountersTable.tsf). Les fichiers d’accompagnement (par exemple, PerformanceCountersTables_\*\*001.tsf, PerformanceCountersTables_\*\*002. tsf, etc.) sont traités automatiquement.

### <a name="more-about-trace-logs-missing"></a>En savoir plus sur les journaux de suivi manquants

**Remarque :** cela ne concerne principalement que les services cloud à moins que vous n’ayez configuré l’élément DiagnosticsMonitorTraceListener dans une application s’exécutant sur votre machine virtuelle IaaS. 

- Vérifiez que l’élément DiagnosticMonitorTraceListener est configuré dans le fichier web.config ou app.config.  Par défaut, il est configuré dans les projets de service cloud, mais certains clients le commentent, ce qui entraîne la non-collecte des déclarations de trace par Diagnostics. 
- Si les journaux ne sont pas écrits à partir de la méthode OnStart ou Run, assurez-vous que l’élément DiagnosticMonitorTraceListener figure dans le fichier app.config.  Par défaut, il figure dans le fichier web.config, mais cela s’applique uniquement au code s’exécutant dans w3wp.exe. Vous en avez donc besoin dans le fichier app.config pour capturer les traces exécutées dans WaIISHost.exe.
- Veillez à utiliser Diagnostics.Trace.TraceXXX à la place de Diagnostics.Debug.WriteXXX.  Les instructions de débogage seront supprimées dans une version Release.
- Vérifiez que le code compilé contient effectivement les lignes Diagnostics.Trace (utilisez Reflector, ildasm ou ILSpy pour le vérifier).  Les commandes Diagnostics.Trace sont supprimées du fichier binaire compilé, sauf si vous utilisez le symbole de compilation conditionnelle TRACE.  Si vous utilisez msbuild pour générer le projet, il s’agit d’un problème courant d’exécution.

## <a name="known-issues-and-mitigations"></a>Problèmes connus et atténuations des risques
Voici la liste des problèmes connus avec les atténuations des risques :

**1. Dépendance de .NET 4.5 :**

WAD connaît une dépendance d’exécution dans .NET Framework 4.5 ou supérieur. Au moment de la rédaction de cet article, .NET 4.5 ou supérieur est installé sur toutes les machines approvisionnées pour les services cloud et toutes les images de base des machines virtuelles Azure officielles. Il est toutefois encore possible de se retrouver dans une situation où vous essayez d’exécuter WAD sur une machine dépourvue de .NET 4.5 ou supérieur. Cela se produit si vous créez votre machine à partir d’une ancienne image ou capture instantanée, ou encore si vous utilisez votre propre disque personnalisé.

Cela se manifeste généralement par le code de sortie **255** lorsque vous exécutez DiagnosticsPluginLauncher.exe. Les échecs se produisent en raison de l’exception non prise en charge : 
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Atténuation :** installez .NET 4.5 ou version supérieure sur votre machine.

**2. Données des compteurs de performances disponibles dans le stockage, mais non affichées dans le portail**

Par défaut, le portail des machines virtuelles affiche certains compteurs de performances. Si vous ne les voyez pas alors que vous savez qu’elles sont générées, car disponibles dans le stockage, vous devez procéder à des vérifications. Vérifiez les points suivants :
- Si les données du stockage possèdent des noms de compteur en anglais. Si les noms de compteur ne sont pas en anglais, le graphique des mesures du portail ne peut pas les reconnaître.
- Si vous utilisez des caractères génériques (\*) dans les noms de compteur de performances, le portail ne peut pas mettre en corrélation le compteur configuré et le compteur collecté.

**Atténuation** : modifiez la langue de la machine pour la définir sur l’anglais pour les comptes système. Sélectionnez Panneau de configuration -> Région -> Administration -> Paramètres de copie ->, puis décochez la case « Écran d’accueil et comptes système » afin que la langue personnalisée ne soit pas appliquée au compte système. Veillez également à ne pas utiliser de caractères génériques si vous souhaitez que le portail soit votre expérience de consommation principale.

