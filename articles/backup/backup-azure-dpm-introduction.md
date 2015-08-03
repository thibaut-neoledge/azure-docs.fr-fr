<properties
	pageTitle="Présentation d'Azure DPM Backup | Microsoft Azure"
	description="Présentation de la sauvegarde de serveurs DPM à l'aide du service Azure Backup"
	services="backup"
	documentationCenter=""
	authors="SamirMehta"
	manager="jwhit"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="07/16/2015" ms.author="sammehta"; "jimpark"/>

# Présentation d'Azure DPM Backup

Cet article présente l'utilisation de Microsoft Azure Backup pour protéger vos serveurs System Center Data Protection Manager (DPM) et vos charges de travail. En le lisant, vous comprendrez :

- le fonctionnement de la sauvegarde du serveur Azure DPM ;
- les conditions requises pour une expérience de sauvegarde fluide ;
- les erreurs classiques rencontrées et comment les gérer ;
- Scénarios pris en charge

System Center DPM sauvegarde les données des fichiers et des applications. Les données sauvegardées dans DPM peuvent être stockées sur bande, sur disque, ou sauvegardées dans Azure avec Microsoft Azure Backup. DPM interagit avec Azure Backup comme suit :

- **DPM déployé comme un serveur physique ou une machine virtuelle en local** : si DPM est déployé comme un serveur physique ou comme une machine virtuelle Hyper-V en local, vous pouvez sauvegarder les données dans un coffre Azure Backup en plus d'une sauvegarde sur disque et sur bande.
- **DPM déployé comme une machine virtuelle Azure** : depuis la mise à jour 3 de System Center 2012 R2, DPM peut être déployé comme une machine virtuelle Azure. Si DPM est déployé comme une machine virtuelle Azure, vous pouvez sauvegarder les données sur des disques Azure connectés à la machine virtuelle DPM Azure, ou décharger le stockage de données en les sauvegardant dans un coffre Azure Backup.

## Pourquoi sauvegarder vos serveurs DPM ?

Les avantages commerciaux de l'utilisation d'Azure Backup pour la sauvegarde de serveurs DPM sont les suivants :

- Pour le déploiement DPM en local, vous pouvez utiliser la sauvegarde Azure au lieu d'un déploiement à long terme sur bande.
- Pour les déploiements DPM dans Azure, Azure Backup vous permet de décharger le stockage du disque Azure et ainsi d'évoluer en stockant les données plus anciennes dans Azure Backup et les nouvelles données sur un disque.

## Comment fonctionne la sauvegarde d'un serveur DPM ?
Pour sauvegarder une machine virtuelle, il est d’abord nécessaire de capturer un instantané des données. Le service Azure Backup lance le travail de sauvegarde à l’heure planifiée et déclenche l’extension de sauvegarde pour capturer un instantané. L’extension de sauvegarde se coordonne avec le service VSS in-guest pour assurer la cohérence et appelle l’API d’instantané d’objet blob du service Azure Storage une fois que la cohérence a été atteinte. Cela permet d’obtenir un instantané cohérent des disques de la machine virtuelle sans avoir à l’arrêter.

Une fois l’instantané capturé, les données sont transférées par le service Azure Backup dans le coffre de sauvegarde. Le service se charge d'identifier et de transférer uniquement les blocs qui ont été modifiés depuis la dernière sauvegarde, ce qui garantit l'efficacité du stockage des sauvegardes et les performances du réseau. Une fois le transfert de données terminé, l’instantané est supprimé et un point de récupération est créé. Ce point de récupération est affiché dans le portail de gestion Azure.

>[AZURE.NOTE]Pour les machines virtuelles Linux, seule une sauvegarde cohérente au niveau des fichiers est possible.

## Composants requis
Préparer Azure Backup pour sauvegarder des données DPM comme suit :

1. **Créer un coffre de sauvegarde** : créez un coffre dans la console Azure Backup.
2. **Télécharger les informations d'identification de l'archivage** : dans Azure Backup, téléchargez dans l'archivage le certificat de gestion que vous avez créé.
3. **Installer l'Agent Azure Backup et inscrire le serveur** : à partir d'Azure Backup, installez l'agent sur chaque serveur DPM et inscrivez le serveur DPM dans le coffre de sauvegarde.

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]


## Spécifications (et limitations)

- DPM peut s'exécuter en tant que serveur physique ou machine virtuelle Hyper-V installée sur System Center 2012 SP1 ou System Center 2012 R2. DPM peut également s'exécuter en tant que machine virtuelle Azure sur System Center 2012 R2 avec au moins le correctif cumulatif 3 pour DPM 2012 R2, ou en tant que machine virtuelle Windows dans VMWare sur System Center 2012 R2 avec au moins le correctif cumulatif 5.
- Si vous exécutez DPM avec System Center 2012 SP1, vous devez installer le correctif cumulatif 2 pour System Center Data Protection Manager SP1. Cette opération est nécessaire avant d'installer l'agent Azure Backup.
- Windows PowerShell et .Net Framework 4.5 doivent être installés sur le serveur DPM.
- DPM permet de sauvegarder la plupart des charges de travail dans Azure Backup. Pour une liste complète des éléments pris en charge par Azure Backup, consultez la liste ci-dessous.
- Des données stockées dans Azure Backup ne peuvent pas être récupérées avec l'option « Copie sur bande ».
- Vous devez posséder un compte Azure avec la fonctionnalité Azure Backup activée. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. En savoir plus sur la [tarification d'Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
- L'utilisation d'Azure Backup nécessite l'installation de l'agent Azure Backup sur les serveurs que vous souhaitez sauvegarder. Chaque serveur doit disposer d'au moins 2,5 Go d'espace de stockage local disponible (15 Go étant recommandés) pour l'emplacement du cache.
- Les données seront stockées dans le coffre Azure. Il n'existe aucune limite à la quantité de données que vous pouvez sauvegarder dans un coffre Azure Backup, mais la taille d'une source de données (par exemple, une machine virtuelle ou une base de données) ne doit pas dépasser 1,65 To.

Les types de fichiers suivants sont pris en charge pour une sauvegarde vers Azure :

- Chiffré (sauvegardes complètes uniquement)
- Compressé (sauvegardes incrémentielles prises en charge)
- Partiellement alloué (sauvegardes incrémentielles prises en charge)
- Compressé et partiellement alloué (traité comme partiellement alloué)

Et les types suivants ne sont pas pris en charge :

- Les serveurs sur des systèmes de fichiers respectant la casse ne sont pas pris en charge.
- Liens physiques (ignorés)
- Points d'analyse (ignorés)
- Chiffré et compressé (ignoré)
- Chiffré et partiellement alloué (ignoré)
- Flux compressé
- Flux partiellement alloué

>[AZURE.NOTE]À partir de System Center 2012 DPM avec SP1, vous pouvez sauvegarder dans Azure des charges de travail protégées par DPM grâce à Microsoft Azure Backup.

<!---HONumber=July15_HO4-->