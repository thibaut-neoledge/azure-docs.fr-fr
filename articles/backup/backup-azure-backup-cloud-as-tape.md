<properties
   pageTitle="Utilisation d’Azure Backup pour remplacer votre infrastructure sur bande"
   description="Découvrez comment Azure Backup fournit une sémantique de type bande qui permet de sauvegarder et de restaurer des données dans Azure."
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

# Utilisation d’Azure Backup pour remplacer votre infrastructure sur bande

Les clients Azure Backup et System Center Data Protection Manager peuvent effectuer les actions suivantes :

+ sauvegarder des données selon la planification qui convient le mieux aux besoins de leur organisation ;

+ conserver plus longtemps les données de sauvegarde ;

+ intégrer Azure à leurs besoins de rétention à long terme (à la place des bandes).

Cet article explique comment les clients peuvent mettre en place des stratégies de sauvegarde et de rétention. Les clients qui utilisent des bandes pour répondre à leurs besoins de rétention à long terme disposent désormais d’une alternative puissante et viable grâce à cette fonctionnalité. La fonctionnalité est activée dans la dernière version d’Azure Backup (disponible [ici](http://aka.ms/azurebackup_agent)). Les clients SCDPM doivent migrer vers UR5 avant d’utiliser cette fonctionnalité.

## Qu’est-ce que la planification de sauvegarde ?
La planification de sauvegarde indique la fréquence de l’opération de sauvegarde, par exemple, les paramètres de l’écran ci-dessous indiquent que les sauvegardes sont effectuées tous les jours à 18 h 00 et à minuit. <br/>

![Planification quotidienne][1]

Les clients peuvent également planifier une sauvegarde hebdomadaire, par exemple, les paramètres de l’écran ci-dessous indiquent que les sauvegardes sont effectuées le dimanche et le mercredi à 9 h 30 et à 1 h 00. <br/>

![Planification hebdomadaire][2]

## Qu’est-ce que la stratégie de rétention ?
La stratégie de rétention spécifie la durée de stockage de la sauvegarde. Au lieu de simplement spécifier une même stratégie pour tous les points de sauvegarde, les clients peuvent spécifier différentes stratégies de rétention en fonction du moment où est effectuée la sauvegarde. Par exemple, le point de sauvegarde effectué à la fin de chaque trimestre peut devoir être conservé plus longtemps à des fins d’audit, alors que le point de sauvegarde effectué quotidiennement (qui fait office de point de récupération opérationnel) doit être conservé pendant 90 jours. <br/>

![Stratégie de rétention][3]

Le nombre total de « points de rétention » spécifié dans cette stratégie est 90 (points quotidiens) + 40 (un par trimestre pendant 10 ans) = 130.

## Exemple – Combinaison des deux
<br/> ![Exemple d’écran][4]

1. **Stratégie de rétention quotidienne** : les sauvegardes effectuées quotidiennement sont stockées pendant 7 jours.
2. **Stratégie de rétention hebdomadaire** : les sauvegardes effectuées tous les samedis à minuit et 18 h 00 sont conservées pendant 4 semaines.
3. **Stratégie de rétention mensuelle** : les sauvegardes effectuées le dernier samedi de chaque mois à minuit et à 18 h 00 sont conservées pendant 12 mois.
4. **Stratégie de rétention annuelle** : les sauvegardes effectuées le dernier samedi de chaque mois de mars à minuit sont conservées pendant 10 ans.

Le nombre total de « points de rétention » (points à partir duquel un client peut restaurer des données) dans le schéma ci-dessus est calculé comme suit :

+ 2 points par jour pendant 7 jours = 14 points de récupération

+ 2 points par semaine pendant 4 semaines = 8 points de récupération

+ 2 points par mois pendant 12 mois = 24 points de récupération

+ 1 point par an pendant 10 ans = 10 points de récupération

Le nombre total de points de récupération est 56.

## Configuration avancée

En cliquant sur **Modifier** dans l’écran précédent, les clients peuvent spécifier les planifications de rétention de manière plus flexible. <br/>

![Modifier][5]


<!--Image references-->
[1]: ./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png
[2]: ./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png
[3]: ./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png
[4]: ./media/backup-azure-backup-cloud-as-tape/samplescreen.png
[5]: ./media/backup-azure-backup-cloud-as-tape/modify.png
 

<!---HONumber=62-->