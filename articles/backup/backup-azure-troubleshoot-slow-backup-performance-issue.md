---
title: "Résolution des problèmes de ralentissement des performances de sauvegarde des fichiers et des dossiers dans Sauvegarde Azure | Microsoft Docs"
description: "Apporte des conseils visant à vous aider à diagnostiquer la cause des problèmes de performances d’Azure Backup"
services: backup
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.assetid: e379180a-db13-4e0c-90e4-28e5dd6f5b14
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ac68ae449a03b6d9e77e6093531a47c905654f16
ms.contentlocale: fr-fr
ms.lasthandoff: 11/17/2016


---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Résolution des problèmes de sauvegarde lente de fichiers et de dossiers dans Azure Backup
Cet article apporte des conseils visant à vous aider à diagnostiquer la cause du ralentissement des performances de sauvegarde des fichiers et des dossiers avec Azure Backup. Lorsque vous utilisez l’agent Azure Backup pour sauvegarder des fichiers, le processus de sauvegarde peut prendre plus longtemps que prévu. Ce ralentissement peut être dû à une ou plusieurs des raisons suivantes :

* [Des goulots d’étranglement de performances ralentissent l’ordinateur en cours de sauvegarde.](#cause1)
* [Un autre processus ou logiciel antivirus interfère avec le processus Azure Backup.](#cause2)
* [L’agent de sauvegarde est en cours d’exécution sur une machine virtuelle Azure](#cause3)  
* [Vous sauvegardez un grand nombre de fichiers (plusieurs millions).](#cause4)

Avant de commencer à résoudre le problème, nous vous recommandons de télécharger et d’installer le [dernier agent Azure Backup](http://aka.ms/azurebackup_agent). Nous apportons des mises à jour fréquentes à l’agent de sauvegarde afin de résoudre différents problèmes, d’ajouter des fonctionnalités et d’améliorer les performances.

Par ailleurs, nous vous recommandons vivement de passer en revue le [Forum aux questions sur le service Azure Backup](backup-azure-backup-faq.md) pour vous assurer que vous ne rencontrez pas des problèmes de configuration courants.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Cause : goulots d’étranglement de performances sur l’ordinateur
La présence de goulots d’étranglement sur l’ordinateur en cours de sauvegarde peut engendrer des retards. Par exemple, la capacité de l’ordinateur à lire le disque ou à écrire dessus ou encore la disponibilité de la bande passante pour l’envoi de données peuvent créer des goulots d’étranglement.

Windows fournit un outil intégré appelé [Analyseur de performances](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Performance Monitor ou Perfmon) pour détecter les goulots d’étranglement.

Voici quelques compteurs de performances et plages qui peuvent être utiles pour diagnostiquer les goulots d’étranglement et permettre des sauvegardes optimales.

| Compteur | État |
| --- | --- |
| Disque logique (disque physique)--% temps d’inactivité |• 100 % à 50 % d’inactivité = sain</br>• 49 % à 20 % d’inactivité = avertissement ou analyse</br>• 19 % à 0 % d’inactivité = critique ou hors spécifications |
| Disque logique (disque physique)--% moy. disque s/lecture ou disque s/écriture |• 0,001 ms à 0,015 ms = sain</br>• 0,015 ms à 0,025 ms = avertissement ou analyse</br>• 0,026 ms ou plus = critique ou hors spécifications |
| Disque logique (disque physique)--Taille de file d’attente du disque actuelle (pour toutes les instances) |80 demandes pendant plus de 6 minutes |
| Mémoire--Octets de réserve non paginée |• Moins de 60 % de la réserve utilisée = sain<br>• 61 à 80 % du pool consommé = avertissement ou analyse</br>• Plus de 80 % de la réserve utilisée = critique ou hors spécifications |
| Mémoire--Octets de réserve paginée |• Moins de 60 % de la réserve utilisée = sain</br>• 61 à 80 % du pool consommé = avertissement ou analyse</br>• Plus de 80 % de la réserve utilisée = critique ou hors spécifications |
| Mémoire--Mégaoctets disponibles |• 50 % de mémoire disponible ou plus = sain</br>• 25 % de mémoire disponible = analyse</br>• 10 % de mémoire disponible = avertissement</br>• Moins de 100 Mo ou 5 % de mémoire disponible = critique ou hors spécifications |
| Processeur--\%temps processeur (toutes les instances) |• Moins de 60 % utilisés = sain</br>• 61 à 90 % utilisés = analyse ou attention</br>• 91 % à 100 % utilisés = critique |

> [!NOTE]
> Si vous déterminez que l’infrastructure est la cause du problème, nous vous recommandons de défragmenter les disques régulièrement pour de meilleures performances.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Cause : un autre processus ou logiciel antivirus interfère avec Azure Backup
Nous avons vu plusieurs exemples dans lesquels d’autres processus au sein du système Windows ont dégradé les performances du processus de l’agent Azure Backup. Par exemple, si vous utilisez l’agent Azure Backup et un autre programme pour sauvegarder des données ou si un logiciel antivirus est en cours d’exécution et qu’il verrouille les fichiers à sauvegarder, plusieurs verrouillages sur les fichiers peuvent provoquer des conflits. Dans ce cas, la sauvegarde risque d’échouer ou l’opération peut prendre plus longtemps que prévu.

La meilleure recommandation dans ce scénario consiste à désactiver l’autre programme de sauvegarde pour déterminer si le temps de sauvegarde de l’agent Azure Backup évolue. En règle générale, s’assurer que plusieurs opérations de sauvegarde n’interfèrent pas l’une sur l’autre est suffisant pour empêcher les conflits.

Pour les programmes antivirus, nous vous recommandons d’exclure les fichiers et emplacements suivants :

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe as a process
* C:\Program Files\Microsoft Azure Recovery Services Agent\ folders
* Nouvel emplacement (si l’emplacement standard n’est pas utilisé)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Cause : l’agent de sauvegarde est en cours d’exécution sur une machine virtuelle Azure
Si vous exécutez l’agent de sauvegarde sur une machine virtuelle, les performances seront plus lentes par rapport à une exécution sur un ordinateur physique. Cela s’explique par les limitations liées aux opérations d’E/S par seconde.  Toutefois, vous pouvez optimiser les performances en basculant les lecteurs de données sauvegardées vers un stockage premium Azure. Nous travaillons à la résolution de ce problème. Le correctif sera disponible dans une version ultérieure.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Cause : vous sauvegardez un grand nombre de fichiers (plusieurs millions)
Le déplacement d’un volume important de données prend plus de temps que le déplacement d’un volume plus restreint de données. Dans certains cas, la durée de la sauvegarde dépend non seulement de la taille des données, mais également du nombre de fichiers ou de dossiers. Cela se vérifie plus particulièrement lorsque des millions de petits fichiers (de quelques octets à quelques kilo-octets) sont sauvegardés.

Ce comportement se produit parce qu’Azure catalogue vos fichiers pendant que vous sauvegardez les données et les déplacez dans Azure. Dans certains cas rares, l’opération de catalogage peut prendre plus longtemps que prévu.

Les indicateurs suivants peuvent vous aider à identifier le goulot d’étranglement et appliquer en conséquence les étapes suivantes :

* **L’interface utilisateur affiche la progression du transfert de données**. Les données sont toujours en cours de transfert. La bande passante réseau ou la taille des données engendrent peut-être un ralentissement.
* **L’interface utilisateur n’affiche pas la progression du transfert de données**. Ouvrez les journaux sous C:\Microsoft Azure Recovery Services Agent\Temp, puis recherchez l’entrée FileProvider::EndData dans les journaux. Cette entrée signifie que le transfert de données est terminé et que l’opération de catalogage est en cours. N’annulez pas les tâches de sauvegarde. Attendez un peu plus longtemps que l’opération de catalogage soit terminée. Si le problème persiste, contactez le [support Azure](https://portal.azure.com/#create/Microsoft.Support).

