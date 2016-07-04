<properties
   pageTitle="Migrer votre entrepôt Azure SQL Data Warehouse existant vers le stockage Premium | Microsoft Azure"
   description="Instructions de migration d’un entrepôt SQL Data Warehouse existant vers le stockage Premium"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/21/2016"
   ms.author="nicw;barbkess;sonyama"/>

# Détails relatifs à la migration vers Premium Storage
Le logiciel SQL Data Warehouse a récemment proposé pour la première fois la fonctionnalité [Premium Storage, afin d’optimiser la prévisibilité des performances][]. Nous sommes maintenant prêts à migrer des entrepôts de données figurant actuellement sur le stockage standard vers Premium Storage. Poursuivez votre lecture pour savoir de quelle manière et à quel moment les migrations automatiques s’exécutent, et pour savoir comment effectuer la migration vous-même si vous souhaitez déterminer à quel moment le temps d’arrêt doit survenir.

Si vous disposez de plusieurs entrepôts de données, utilisez les instructions de la section [Planification de la migration automatique][] ci-dessous pour déterminer à quel moment ils doivent également être migrés.

## Détails sur la migration automatique
Par défaut, nous allons migrer votre base de données pour vous entre 18:00 et 6 heures du matin (heure locale de votre région) à un moment donné lors de la [planification de la migration automatique][] (voir section ci-dessous). Lors de la migration, l’entrepôt de données existant est inutilisable. Nous estimons que la migration dure environ 1 heure par To de stockage, pour chaque entrepôt de données. Nous allons également nous assurer que vous n’êtes facturé à aucun moment de la migration.

> [AZURE.NOTE] Vous ne serez pas en mesure d’utiliser votre entrepôt de données existant lors de la migration. Une fois la migration terminée, votre entrepôt de données sera remis en ligne.

Les informations ci-dessous détaillent les étapes que Microsoft exécute pour vous afin d’effectuer la migration. Vous n’avez pas besoin d’intervenir. Pour les besoins de cet exemple, imaginez que votre entrepôt de données existant sur le stockage standard est appelé « MyDW ».

1.	Microsoft renomme l’élément « MyDW » ainsi : « MyDW\_ DO\_NOT\_USE\_ [Horodatage] ».
2.	Microsoft interrompt « MyDW\_ DO\_NOT\_USE\_ [Horodatage] ». Pendant ce temps, Microsoft effectue une sauvegarde. En cas de problème, il se peut que le processus s’interrompe et se relance plusieurs fois.
3.	Microsoft crée un entrepôt de données nommé « MyDW » sur Premium Storage, en s’appuyant sur la sauvegarde effectuée à l’étape 2 ci-dessus. L’entrepôt « MyDW » n’apparaît que lorsque la restauration est terminée.
4.	Une fois la restauration terminée, l’entrepôt « MyDW » retrouve le niveau de DWU et l’état actif ou interrompu qu’il présentait avant la migration.
5.	Une fois la migration terminée, Microsoft supprime l’entrepôt « MyDW\_DO\_NOT\_USE\_ [Horodatage] ».
	
> [AZURE.NOTE] Ces paramètres ne seront pas repris dans le cadre de la migration :
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the **Database** level will need to be re-added.  Firewall rules at the **Server** level will not be impacted.

### Planification de la migration automatique
La migration automatique se produit entre 18:00 et 6 heures du matin (heure locale de votre région) à un moment donné lors du planning d’interruption indiqué ci-dessous.

| **Région** | **Date de début estimée** | **Date de fin estimée** |
| :------------------ | :--------------------------- | :--------------------------- |
| Est de l’Australie | Pas encore déterminée | Pas encore déterminée |
| Sud-est de l’Australie | Pas encore déterminée | Pas encore déterminée |
| Sud du Brésil | Pas encore déterminée | Pas encore déterminée |
| Centre du Canada | 23 juin 2016 | 1er juillet 2016 |
| Est du Canada | 23 juin 2016 | 1er juillet 2016 |
| Centre des États-Unis | 23 juin 2016 | 1er juillet 2016 |
| Chine orientale | Pas encore déterminée | Pas encore déterminée |
| Chine du Nord | Pas encore déterminée | Pas encore déterminée |
| Asie de l'Est | 23 juin 2016 | 1er juillet 2016 |
| Est des États-Unis | 23 juin 2016 | 1er juillet 2016 |
| Est des États-Unis 2 | 23 juin 2016 | 1er juillet 2016 |
| Inde-Centre | 23 juin 2016 | 1er juillet 2016 |
| Sud de l'Inde | 23 juin 2016 | 1er juillet 2016 |
| Inde-Ouest | Pas encore déterminée | Pas encore déterminée |
| Est du Japon | Pas encore déterminée | Pas encore déterminée |
| Ouest du Japon | Pas encore déterminée | Pas encore déterminée |
| États-Unis - partie centrale septentrionale | Pas encore déterminée | Pas encore déterminée |
| Europe du Nord | Pas encore déterminée | Pas encore déterminée |
| Centre-Sud des États-Unis | 23 juin 2016 | 1er juillet 2016 |
| Asie du Sud-Est | 23 juin 2016 | 1er juillet 2016 |
| Europe de l'Ouest | 23 juin 2016 | 1er juillet 2016 |
| Ouest des États-Unis | 23 juin 2016 | 1er juillet 2016 |

## Migration ponctuelle vers Premium Storage
Si vous souhaitez déterminer à quel moment le temps d’arrêt doit se produire, vous pouvez suivre la procédure ci-après, qui permet de migrer un entrepôt de données existant sur un stockage standard vers Premium Storage. Si vous optez pour une migration ponctuelle, vous devez effectuer cette opération avant le début de la migration automatique effectuée dans cette région, afin d’éviter tout conflit généré par cette dernière (consultez [Planification de la migration automatique][]).

> [AZURE.NOTE] Actuellement, le logiciel SQL Data Warehouse associé à Premium Storage n’est pas géo-redondant. Cela signifie qu’une fois que votre entrepôt de données est migré vers Premium Storage, les données résident uniquement dans votre région actuelle. Lorsqu’elles sont disponibles, les géo-sauvegardes copient votre entrepôt de données toutes les 24 heures dans la [région Azure associée][], ce qui vous permet d’effectuer la restauration depuis une géo-sauvegarde vers une région quelconque dans Azure. Lorsque la fonctionnalité de géo-sauvegarde est disponible pour les migrations ponctuelles, un message d’information est affiché sur le [site de documentation principal][]. En revanche, les migrations automatiques ne sont pas soumises à cette limitation.

### Déterminer le type de stockage
Si vous avez créé un entrepôt de données avant les dates ci-dessous, cela signifie que vous utilisez actuellement le stockage standard.

| **Région** | **Entrepôt de données créé avant cette date** |
| :------------------ | :-------------------------------- |
| Est de l’Australie | Premium Storage non disponible pour l’instant |
| Sud-est de l’Australie | Premium Storage non disponible pour l’instant |
| Sud du Brésil | Premium Storage non disponible pour l’instant |
| Centre du Canada | 25 mai 2016 |
| Est du Canada | 26 mai 2016 |
| Centre des États-Unis | 26 mai 2016 |
| Chine orientale | Premium Storage non disponible pour l’instant |
| Chine du Nord | Premium Storage non disponible pour l’instant |
| Asie de l'Est | 25 mai 2016 |
| Est des États-Unis | 26 mai 2016 |
| Est des États-Unis 2 | 27 mai 2016 |
| Inde-Centre | 27 mai 2016 |
| Sud de l'Inde | 26 mai 2016 |
| Inde-Ouest | Premium Storage non disponible pour l’instant |
| Est du Japon | Premium Storage non disponible pour l’instant |
| Ouest du Japon | Premium Storage non disponible pour l’instant |
| États-Unis - partie centrale septentrionale | Premium Storage non disponible pour l’instant |
| Europe du Nord | Premium Storage non disponible pour l’instant |
| Centre-Sud des États-Unis | 27 mai 2016 |
| Asie du Sud-Est | 24 mai 2016 |
| Europe de l'Ouest | 25 mai 2016 |
| Ouest des États-Unis | 26 mai 2016 |


### Instructions relatives à la migration ponctuelle
Si vous souhaitez déterminer le temps d’arrêt de votre système, vous pouvez migrer votre entrepôt de données de manière ponctuelle, à l’aide de la fonction de sauvegarde/restauration. L’opération de restauration de la migration doit durer environ 1 heure par To de stockage, pour chaque entrepôt de données. Si vous souhaitez conserver le même nom une fois la migration terminée, suivez les étapes ci-dessous, qui détaillent une [solution de contournement pour le changement de nom][].

1.	[Interrompez][] l’entrepôt de données associé à une sauvegarde automatique.
2.	[Effectuez la restauration][] à partir de votre instantané le plus récent.
3.	Supprimez votre entrepôt de données existant sur le stockage standard. **Si vous ne parvenez pas à effectuer cette étape, vous serez facturé pour les deux entrepôts de données.**

> [AZURE.NOTE] Ces paramètres ne seront pas repris dans le cadre de la migration :
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the **Database** level will need to be re-added.  Firewall rules at the **Server** level will not be impacted.

#### Facultatif : solution de contournement pour le changement de nom 
Deux bases de données situées sur un même serveur logique ne peuvent pas présenter le même nom. Actuellement, SQL Data Warehouse ne prend pas en charge la fonctionnalité de changement de nom d’un entrepôt de données. Les instructions ci-dessous vous permettent de contourner ce problème lors d’une migration ponctuelle (remarque : les migrations automatiques ne sont pas soumises à cette limitation).

Pour les besoins de cet exemple, imaginez que votre entrepôt de données existant sur le stockage standard est appelé « MyDW ».

1.	[Interrompez][] l’entrepôt de données « MyDW » associé à une sauvegarde automatique.
2.	À partir de votre dernier instantané, [effectuez la restauration][] d’une nouvelle base de données présentant un nom différent, comme « MyDWTemp ».
3.	Supprimez « MyDW ». **Si vous n’effectuez pas cette étape, vous serez facturé pour les deux entrepôts de données.**
4.	Étant donné que « MyDWTemp » est un entrepôt de données nouvellement créé, la sauvegarde ne sera pas disponible à des fins de restauration pendant une période donnée. Nous vous recommandons de continuer à exécuter des opérations sur « MyDWTemp » pendant quelques heures, puis de passer aux étapes 5 et 6.
5.	[Interrompez][] l’entrepôt « MyDWTemp » qui fait l’objet d’une sauvegarde automatique.
6.	À partir de votre dernier instantané de « MyDWTemp », [effectuez la restauration][] d’une nouvelle base de données présentant le nom « MyDW ».
7.	Supprimez « MyDWTemp ». **Si vous n’effectuez pas cette étape, vous serez facturé pour les deux entrepôts de données.**

> [AZURE.NOTE] Ces paramètres ne seront pas repris dans le cadre de la migration :
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the **Database** level will need to be re-added.  Firewall rules at the **Server** level will not be impacted.

## Étapes suivantes
Si vous rencontrez des problèmes liés à votre entrepôt de données, veuillez [créer un ticket de support][], en indiquant la migration vers Premium Storage comme cause possible.

<!--Image references-->

<!--Article references-->
[Planification de la migration automatique]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[créer un ticket de support]: ./sql-data-warehouse-get-started-create-support-ticket.md
[région Azure associée]: ./best-practices-availability-paired-regions.md
[site de documentation principal]: ./services/sql-data-warehouse.md
[Interrompez]: ./sql-data-warehouse-manage-compute-portal.md/#pause-compute
[Effectuez la restauration]: ./sql-data-warehouse-manage-database-restore-portal.md
[solution de contournement pour le changement de nom]: #optional-rename-workaround

<!--MSDN references-->


<!--Other Web references-->
[Premium Storage, afin d’optimiser la prévisibilité des performances]: https://azure.microsoft.com/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/

<!---HONumber=AcomDC_0622_2016-->