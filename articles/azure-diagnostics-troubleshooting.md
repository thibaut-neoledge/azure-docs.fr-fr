---
title: "Résolution des problèmes des diagnostics Azure | Microsoft Docs"
description: "Résolution des problèmes lors de l&quot;utilisation des diagnostics Azure dans Azure Virtual Machines, Service Fabric ou Cloud Services."
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
ms.date: 02/09/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 7b27c396791ec537e38a85d0557c6bbe9b70c24c
ms.openlocfilehash: 77559208f6cb81526e59bd2db440a3611175e780
ms.lasthandoff: 02/22/2017


---
# <a name="azure-diagnostics-troubleshooting"></a>Résolution des problèmes des diagnostics Azure
Informations de résolution des problèmes liés à l’utilisation des diagnostics Azure. Pour plus d’informations sur les diagnostics Azure, voir [Présentation des diagnostics Azure](azure-diagnostics.md).

## <a name="azure-diagnostics-is-not-starting"></a>Azure Diagnostics ne démarre pas
Diagnostics comprend deux composants : un plug-in d’agent invité et l’agent de surveillance. Vous pouvez vérifier les fichiers journaux **DiagnosticsPluginLauncher.log** et **DiagnosticsPlugin.log** pour plus d’informations sur les raisons pour lesquelles le démarrage des diagnostics échoue.  

Dans un rôle Cloud Service, les fichiers journaux du plug-in d’agent invité sont situés dans :

```
C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.7.4.0\
```

Dans une machine virtuelle Azure, les fichiers journaux du plug-in d’agent invité sont situés dans :

```
C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.7.4.0\Logs\
```

La dernière ligne des fichiers journaux contient le code de sortie.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```

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
| -106 |Impossible de lire le fichier de configuration Diagnostics.<p><p>Solution : cette erreur se produit lorsqu'un fichier de configuration ne passe pas la validation de schéma. La solution consiste alors à fournir un fichier de configuration qui est conforme au schéma. Pour la machine virtuelle Azure, vous trouverez la configuration fournie pour l’extension de Diagnostics dans le dossier *%SystemDrive%\WindowsAzure\Config* sur la machine virtuelle. Ouvrez le fichier XML approprié et recherchez **Microsoft.Azure.Diagnostics**, puis le cham **xmlCfg** ou **WadCfg** field. Si le champ WadCfg est présent, cela signifie que la configuration est au format JSON. Si le champ xmlCfg est présent, cela signifie que la configuration est au format XML et codée en base64. Vous devez la décoder pour afficher le fichier XML qui a été chargé par Diagnostics. Pour le rôle Service Cloud, vous trouverez la configuration fournie à l’extension de Diagnostics sous C:\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.7.4.0\config.txt sur la machine virtuelle. Les données sont encodées en base64. Vous devrez donc [les décoder](http://www.bing.com/search?q=base64+decoder) pour afficher le XML qui a été chargé par Diagnostics.<p> |
| -107 |La transmission du répertoire de ressources à l'agent de surveillance n'est pas valide.<p><p>Cette erreur interne ne doit se produire que si l'agent de surveillance est appelé manuellement et de manière incorrecte sur la machine virtuelle.</p> |
| -108 |Impossible de convertir le fichier de configuration Diagnostics dans le fichier de configuration de l'agent de surveillance.<p><p>Cette erreur interne ne doit se produire que si le plug-in Diagnostics est appelé manuellement avec un fichier de configuration non valide. |
| -110 |Erreur de configuration générale de Diagnostics.<p><p>Cette erreur interne ne doit se produire que si le plug-in Diagnostics est appelé manuellement avec un fichier de configuration non valide. |
| -111 |Impossible de démarrer l’agent de surveillance.<p><p>Solution : vérifiez que les ressources système disponibles sont suffisantes pour lancer de nouveaux processus. |
| -112 |Erreur générale |

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Les données des diagnostics ne sont pas consignées dans Azure Storage
Les diagnostics Azure stockent toutes les données dans Azure Storage.

La principale cause de données d'événement manquantes est une mauvaise définition des informations du compte de stockage.

Solution : corrigez votre fichier de configuration Diagnostics et réinstallez Diagnostics.
Si le problème persiste après la réinstallation de l'extension de diagnostic, vous devrez peut-être pousser plus loin le débogage en parcourant toutes les erreurs de l'agent de surveillance. Avant que les données d'événement ne soient chargées sur votre compte de stockage, elles sont stockées dans LocalResourceDirectory.
Ce chemin d’accès au répertoire varie. 

Pour les rôles de service Cloud :

    C:\Resources\Directory\<CloudServiceDeploymentID>.<RoleName>.DiagnosticStore\WAD<DiagnosticsMajorandMinorVersion>\Tables

Pour les machines virtuelles : 

    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD<DiagnosticsMajorandMinorVersion>\Tables

Si le dossier LocalResourceDirectory ne contient aucun fichier, l'agent de surveillance ne peut être lancé. Cette erreur est généralement causée par un fichier de configuration non valide. Un tel événement doit être signalé dans CommandExecution.log.

Si l'agent de surveillance collecte avec succès des données d'événement, vous voyez les fichiers .tsf pour chaque événement défini dans votre fichier de configuration. L'agent de surveillance consigne ses erreurs dans le fichier MaEventTable.tsf. Pour inspecter le contenu de ce fichier, vous pouvez utiliser l'application tabel2csv pour convertir le fichier .tsf en un fichier de valeurs séparées par des virgules (.csv) :

Sur un rôle Cloud Service :

    %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

*%SystemDrive%* sur un rôle Cloud Service est généralement D :

Sur une machine virtuelle :

    C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

Les commandes précédentes génèrent le fichier journal *maeventtable.csv*, que vous pouvez ouvrir et inspecter pour consulter les messages d'échec.    

## <a name="diagnostics-data-tables-not-found"></a>Tables des données de diagnostic introuvables
Les tables d’Azure Storage qui contiennent les données des diagnostics Azure sont nommées à l’aide du code suivant :

```csharp
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
        <EtwEventSourceProviderConfiguration provider=”prov1”>
          <Event id=”1” />
          <Event id=”2” eventDestination=”dest1” />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider=”prov2”>
          <DefaultEvents eventDestination=”dest2” />
        </EtwEventSourceProviderConfiguration>
```

Avec génération de 4 tables :

| Événement | Nom de la table |
| --- | --- |
| provider=”prov1” &lt;Event id=”1” /&gt; |WADEvent+MD5(“prov1”)+”1” |
| provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt; |WADdest1 |
| provider=”prov1” &lt;DefaultEvents /&gt; |WADDefault+MD5(“prov1”) |
| provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt; |WADdest2 |

