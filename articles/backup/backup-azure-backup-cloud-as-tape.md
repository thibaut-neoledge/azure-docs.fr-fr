<properties
	pageTitle="Sauvegarde Azure permet de remplacer votre infrastructure sur bande"
	description="Découvrez comment Azure Backup fournit une sémantique de type bande qui permet de sauvegarder et restaurer des données dans Azure"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="03/27/2015"
	ms.author="prvijay"/>

# Sauvegarde Azure permet de remplacer votre infrastructure sur bande

Les clients de sauvegarde et de System Center Data Protection Manager Azure peuvent :

+ Données de sauvegarde dans les planifications qui mieux adaptées à leurs besoins de l'organisation

+ Conserver les données de sauvegarde pour des durées plus longues

+ Rendre Azure a besoin d'une partie de leur rétention à long terme (au lieu de bandes).

Cet article explique comment les clients peuvent permettre de sauvegarde et de rétention. Les clients qui utilisent des bandes pour résoudre leur long-terme rétention doivent ont désormais une alternative puissante et viable avec la disponibilité de cette fonctionnalité. La fonctionnalité est activée dans la dernière version de la sauvegarde Azure (disponible [ici](http://aka.ms/azurebackup_agent)). Les clients SCDPM qu'à UR5 avant d'utiliser cette fonctionnalité.

## Quelle est la planification de sauvegarde ?
Planification de sauvegarde indique la fréquence (ou procédure souvent) de l'opération de sauvegarde, par exemple les paramètres dans l'écran ci-dessous indique que les sauvegardes seront prises tous les jours à 18 h 00 et à minuit. <br/>

![Planification quotidienne][1]

Les clients peuvent également planifier une sauvegarde hebdomadaire, par exemple, les paramètres dans l'écran ci-dessous indique que les sauvegardes s'affichera alors chaque dimanche autre & le mercredi à 9 h 30 et 1 h 00. <br/>

![Planification hebdomadaire][2]

## Qu'est la stratégie de rétention ?
Stratégie de rétention spécifie la durée pour laquelle la sauvegarde doit être stockée. Plutôt que de simplement spécifier une stratégie « plate » pour tous les points de sauvegarde, les clients peuvent spécifier des stratégies de rétention différentes basées sur lorsque la sauvegarde est effectuée. Pour par exemple, le point de sauvegarde effectuée à la fin de chaque trimestre peut devoir être conservés pendant une durée supérieure à des fins d'audit, alors que le point de sauvegarde effectuée quotidiennement (point qui sert d'une récupération opérationnelle) doit être conservé pendant 90 jours. <br/>

![Stratégie de rétention][3]

Le nombre total de points de rétention « » spécifié dans cette stratégie est 90 (points quotidiens) + 40 (un pour chaque trimestre depuis 10 ans) = 130.

## Exemple – les assembler
<br/> ![Exemple d'écran][4]

1. **Quotidienne de rétention**: les sauvegardes effectuées quotidiennement sont stockés pendant 7 jours.
2. **Hebdomadaire de rétention**: les sauvegardes effectuées tous les jours à minuit et 18 h 00 samedi seront conservées pendant 4 semaines
3. **Stratégie de rétention mensuelles**: les sauvegardes effectuées à minuit et 18 h 00 le samedi de chaque mois dernier seront conservés pour 12months
4. **Stratégie de rétention annuelles**: effectuées à minuit le dernier samedi de mars, toutes les sauvegardes seront conservées pendant 10 ans

Le nombre total de « points de rétention » (points à partir de laquelle un client peut restaurer les données) dans le diagramme ci-dessus est calculée comme suit :

+ 2 points par jour pour la récupération de 7 jours = 14 points

+ 2 points par semaine pour 4 semaines = 8 points de récupération

+ 2 points par mois pendant 12 mois = 24 points de récupération

+ 1 point par an et par récupération de 10 ans = 10 points

Le nombre total de points de récupération est 56.

## Configuration avancée

En cliquant sur **Modifier** dans l'écran précédent, les clients ont davantage de flexibilité dans la spécification des planifications de rétention. <br/>

![Modifier][5]


<!--Image references-->
[1]: ./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png
[2]: ./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png
[3]: ./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png
[4]: ./media/backup-azure-backup-cloud-as-tape/samplescreen.png
[5]: ./media/backup-azure-backup-cloud-as-tape/modify.png

<!---HONumber=GIT-SubDir--> 