<properties
   pageTitle="Utilisation d’Azure Backup pour remplacer votre infrastructure sur bande | Microsoft Azure"
   description="Découvrez comment Azure Backup fournit une sémantique de type bande qui permet de sauvegarder et de restaurer des données dans Azure."
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="05/03/2016"
   ms.author="jimpark;"/>

# Utilisation d’Azure Backup pour remplacer votre infrastructure sur bande
Les clients Azure Backup et System Center Data Protection Manager peuvent effectuer les actions suivantes :

- sauvegarder leurs données selon des planifications qui correspondent le mieux aux besoins de leur organisation ;
- conserver les données sauvegardées pendant plus longtemps ;
- intégrer Azure à leurs besoins de rétention à long terme (à la place d’une bande).

Cet article explique comment les clients peuvent mettre en place des stratégies de sauvegarde et de rétention. Les clients qui utilisent des bandes pour répondre à leurs besoins de rétention à long terme disposent désormais d’une alternative puissante et viable grâce à cette fonctionnalité. La fonctionnalité est activée dans la dernière version d’Azure Backup (disponible [ici](http://aka.ms/azurebackup_agent)). Les clients SCDPM devront migrer vers UR5 avant d’utiliser cette fonctionnalité.

## Qu’est-ce que la planification de sauvegarde ?
La planification de sauvegarde indique la fréquence de l'opération de sauvegarde. Par exemple, les paramètres dans l'écran ci-dessous indiquent que les sauvegardes seront effectuées tous les jours à 18 h 00 et à minuit.

![Planification quotidienne](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Les clients peuvent également planifier une sauvegarde hebdomadaire. Par exemple, les paramètres de l’écran ci-dessous indiquent que les sauvegardes sont effectuées le dimanche et le mercredi à 9 h 30 et à 1 h 00.

![Planification hebdomadaire](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## Qu’est-ce que la stratégie de rétention ?
La stratégie de rétention spécifie la durée de stockage de la sauvegarde. Au lieu de simplement spécifier une même stratégie pour tous les points de sauvegarde, les clients peuvent spécifier différentes stratégies de rétention en fonction du moment où est effectuée la sauvegarde. Par exemple, le point de sauvegarde effectué à la fin de chaque trimestre peut devoir être conservé plus longtemps à des fins d’audit, alors que le point de sauvegarde effectué quotidiennement (qui fait office de point de récupération opérationnel) doit être conservé pendant 90 jours.

![Stratégie de rétention](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Le nombre total de « points de rétention » spécifié dans cette stratégie est 90 (points quotidiens) + 40 (un par trimestre pendant 10 ans) = 130.

## Exemple – Combinaison des deux

![Exemple d’écran](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Stratégie de rétention quotidienne** : les sauvegardes effectuées quotidiennement sont stockées pendant 7 jours.
2. **Stratégie de rétention hebdomadaire** : les sauvegardes effectuées tous les jours à minuit et à 18 h 00 le samedi sont conservées pendant 4 semaines.
3. **Stratégie de rétention mensuelle** : les sauvegardes effectuées le dernier samedi de chaque mois à minuit et à 18 h 00 sont conservées pendant 12 mois.
4. **Stratégie de rétention annuelle** : les sauvegardes effectuées le dernier samedi de chaque mois de mars à minuit sont conservées pendant 10 ans.

Le nombre total de « points de rétention » (points à partir duquel un client peut restaurer des données) dans le schéma ci-dessus est calculé comme suit :

- 2 points par jour pendant 7 jours = 14 points de récupération
- 2 points par semaine pendant 4 semaines = 8 points de récupération
- 2 points par mois pendant 12 mois = 24 points de récupération
- 1 point par an pendant 10 ans = 10 points de récupération

Le nombre total de points de récupération est 56.

> [AZURE.NOTE] La sauvegarde Azure n'impose aucune restriction sur le nombre de points de récupération.

## Configuration avancée
En cliquant sur **Modifier** dans l’écran précédent, les clients peuvent spécifier les planifications de rétention de manière plus flexible.

![Modifier](./media/backup-azure-backup-cloud-as-tape/modify.png)

## Étapes suivantes
Pour plus d'informations sur Azure Backup, consultez la rubrique

- [Présentation d’Azure Backup](backup-introduction-to-azure-backup.md)
- [Test d’Azure Backup](backup-try-azure-backup-in-10-mins.md)

<!---HONumber=AcomDC_0504_2016-->