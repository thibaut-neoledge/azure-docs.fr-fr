<properties
   pageTitle="Résolution des problèmes de ralentissement des performances de sauvegarde des fichiers et des dossiers dans Azure Backup| Microsoft Azure"
   description="Apporte des conseils visant à vous aider à diagnostiquer et à identifier la cause des problèmes de performances d’Azure Backup"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="jimpark"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/20/2016"
    ms.author="genli"/>

# Résolution des problèmes de ralentissement des performances de sauvegarde des fichiers et des dossiers dans Azure Backup

Cet article apporte des conseils visant à vous aider à diagnostiquer et à identifier la cause du ralentissement des performances de sauvegarde des fichiers et des dossiers avec Azure Backup. Lorsque vous utilisez l’agent Azure Backup pour sauvegarder des fichiers, le processus de sauvegarde peut prendre plus longtemps que prévu. Ce problème peut être dû à une ou plusieurs des raisons suivantes :

-	[Des goulots d’étranglement de performances sur l’ordinateur en cours de sauvegarde](#cause1).
-	[Un autre processus ou logiciel antivirus interfère avec Azure Backup](#cause2).
-	[L’agent de sauvegarde est en cours d’exécution sur une machine virtuelle Azure](#cause3).
-	[Vous tentez de sauvegarder un grand nombre de fichiers (plusieurs millions)](#cause4).

La section suivante vous aide à déterminer la cause spécifique du problème.

Avant de commencer à résoudre le problème, nous vous recommandons de télécharger et d’installer le [dernier agent Azure Backup](http://aka.ms/azurebackup_agent). Nous apportons des mises à jour fréquentes à l’agent de sauvegarde afin de résoudre différents problèmes, d’ajouter des fonctionnalités et d’améliorer les performances.

Par ailleurs, nous vous recommandons vivement de passer en revue le [Forum aux questions sur le service Azure Backup](backup-azure-backup-faq.md) pour vous assurer que vous ne rencontrez pas des problèmes de configuration courants.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Étapes de dépannage
<a id="cause1"></a>
## Cause 1 : ralentissement de la sauvegarde dû à des goulots d’étranglement de performances sur l’ordinateur en cours de sauvegarde

### Solution

Il existe peut-être des goulots d’étranglement sur l’ordinateur en cours de sauvegarde, ce qui peut entraîner des ralentissements. Par exemple, la capacité de l’ordinateur à lire le disque ou à écrire dessus, l’envoi de données sur le réseau via la bande passante, etc.

Windows fournit un outil intégré appelé [Analyseur de performances](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) destiné à détecter les goulots d’étranglement. Le tableau ci-dessous récapitule les compteurs de performances et les plages pour des sauvegardes optimales.

Voici quelques compteurs de performances et plages qui peuvent être utiles pour diagnostiquer les goulots d’étranglement.

| Compteur | Statut |
|---|---|
|Disque logique (disque physique)--% temps d’inactivité | • 100 % à 50 % d’inactivité = sain</br>• 49 % à 20 % d’inactivité = avertissement ou surveillance</br>• 19 % à 0 % d’inactivité = critique ou hors spécifications|
| Disque logique (disque physique)--% moy. disque s/lecture ou disque s/écriture | • 0,001 ms à 0,015 ms = sain</br>• 0,015ms à 0,025 = avertissement ou surveillance</br>• 0,026 ms ou plus = critique ou hors spécifications|
| Disque logique (disque physique)--Taille de file d’attente du disque actuelle (pour toutes les instances) | 80 demandes pendant plus de 6 minutes |
| Mémoire--Octets de réserve non paginée|• Moins de 60 % de la réserve utilisée = sain<br>• 61 % à 80 % de la réserve utilisée = avertissement ou surveillance</br>• Plus de 80 % de la réserve utilisée = critique ou hors spécifications|
| Mémoire--Octets de réserve paginée |• Moins de 60 % de la réserve utilisée = sain</br>• 61 % à 80 % de la réserve utilisée = avertissement ou surveillance</br>• Plus de 80 % de la réserve utilisée = critique ou hors spécifications|
| Mémoire--Mégaoctets disponibles| • 50 % de mémoire disponible ou plus = sain</br>• 25 % de mémoire disponible = surveillance</br>• 10 % de mémoire disponible = avertissement</br>• Moins de 100 Mo ou 5 % de mémoire disponible = critique ou hors spécifications|
|Processeur--\\% temps processeur (toutes les instances)|• Moins de 60 % utilisés = sain</br>• 61 % à 90 % utilisés = analyse ou surveillance</br>• 91 % à 100 % utilisés = critique|


> [AZURE.NOTE] Si l’infrastructure est identifiée comme potentielle cause, il est conseillé de défragmenter le(s) disque(s) protégé(s) régulièrement pour garantir de meilleures performances.

<a id="cause2"></a>
## Cause 2 Un autre processus ou logiciel antivirus interfère avec le processus Azure Backup

Nous avons vu plusieurs exemples dans lesquels d’autres processus au sein du système Windows ont dégradé les performances du processus de l’agent Azure Backup. Par exemple, si vous utilisez l’agent Azure Backup et un autre programme pour sauvegarder des données ou si un logiciel antivirus est en cours d’exécution et qu’il verrouille les fichiers à sauvegarder, plusieurs verrouillages sur les fichiers peuvent provoquer des conflits. Dans ce cas, la sauvegarde risque d’échouer ou l’opération peut prendre plus longtemps que prévu.

### Solution

La meilleure recommandation dans ce scénario consiste à désactiver l’autre programme de sauvegarde pour déterminer si le temps de sauvegarde de l’agent Azure Backup évolue. En règle générale, s’assurer que plusieurs opérations de sauvegarde ne s’exécutent pas simultanément est suffisant pour empêcher les conflits.

Pour les programmes antivirus, nous vous recommandons d’exclure les fichiers et emplacements suivants :

- Exclure C:\\Program Files\\Microsoft Azure Recovery Services Agent\\bin\\cbengine.exe en tant que processus.
- Exclure les dossiers C:\\Program Files\\Microsoft Azure Recovery Services Agent\\.
- Exclure l’emplacement de fichier temporaire (si l’emplacement standard ci-dessus n’est pas utilisé).

<a id="cause3"></a>
## Cause 3 L’agent de sauvegarde est en cours d’exécution sur une machine virtuelle Azure

### Solution

Si vous exécutez l’agent de sauvegarde sur une machine virtuelle, les performances seront plus lentes par rapport à une exécution sur un ordinateur physique. Cela s’explique par les limitations liées aux opérations d’E/S par seconde. Toutefois, vous pouvez optimiser les performances en basculant les lecteurs de données sauvegardées vers un stockage premium. Nous travaillons à la résolution de ce problème. Le correctif sera disponible dans une version ultérieure.

<a id="cause4"></a>
## Cause 4 Sauvegarde d’un grand nombre de fichiers (plusieurs millions)

### Solution

Il est normal que le déplacement d’un volume important de données prenne plus de temps qu’un volume plus restreint. Toutefois, dans certains cas, la durée de l’opération de sauvegarde n’est pas liée uniquement à la taille des données, mais également au nombre de fichiers ou de dossiers, en particulier dans le cas où des millions de petits fichiers (de quelques octets à quelques kilo-octets) sont sauvegardés.

Ce comportement se produit car lorsque nous effectuons la sauvegarde des données et les déplaçons vers Azure, nous effectuons un catalogage de vos fichiers et, dans de rares cas, cette opération de catalogage peut être plus longue.

Suivez les étapes ci-dessous pour identifier le goulot d’étranglement et appliquer en conséquence les étapes suivantes :

a. **L’interface utilisateur affiche la progression de la quantité de données transférées** : dans ce cas, les données sont toujours transférées et la bande passante du réseau ou la taille des données peuvent être à l’origine des ralentissements.

b. **L’interface utilisateur n’affiche pas la progression** : dans ce cas, ouvrez les journaux situés à l’emplacement « C:\\Microsoft Azure Recovery Services Agent\\Temp » et vérifiez la présence de l’entrée « FileProvider::EndData ». Cette entrée signifie que le transfert de données est terminé et que l’opération de catalogage est en cours. N’annulez pas les tâches de sauvegarde. Il est préférable de patienter jusqu’à la fin du catalogage. Si le problème persiste, contactez le [support Azure](https://portal.azure.com/#create/Microsoft.Support).

<!---HONumber=AcomDC_0727_2016-->