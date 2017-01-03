---
title: Migrer votre Azure SQL Data Warehouse existant vers un Stockage Premium | Microsoft Docs
description: "Instructions de migration d’un entrepôt SQL Data Warehouse existant vers le stockage Premium"
services: sql-data-warehouse
documentationcenter: NA
author: happynicolle
manager: barbkess
editor: 
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 11/29/2016
ms.author: rortloff;barbkess
translationtype: Human Translation
ms.sourcegitcommit: b676630e44c49c2ab4006f04408b01cc90c4dc28
ms.openlocfilehash: ef20cf90b54c9b28c70341d7545bb55474feb39f


---
# <a name="migration-to-premium-storage-details"></a>Détails relatifs à la migration vers Premium Storage
SQL Data Warehouse a récemment introduit le [Stockage Premium afin d’améliorer la prévisibilité des performances][Premium Storage for greater performance predictability].  Nous sommes maintenant prêts à migrer des entrepôts de données figurant actuellement sur le stockage standard vers Premium Storage.  Poursuivez votre lecture pour savoir de quelle manière et à quel moment les migrations automatiques s’exécutent, et pour savoir comment effectuer la migration vous-même si vous souhaitez déterminer à quel moment le temps d’arrêt doit se produire.

Si vous disposez de plusieurs entrepôts de données, suivez les instructions de la section [Planification de la migration automatique][automatic migration schedule] ci-dessous pour déterminer à quel moment ils doivent également être migrés.

## <a name="determine-storage-type"></a>Déterminer le type de stockage
Si vous avez créé un entrepôt de données avant les dates ci-dessous, cela signifie que vous utilisez actuellement le stockage standard.

| **Région** | **Entrepôt de données créé avant cette date** |
|:--- |:--- |
| Est de l’Australie |Premium Storage non disponible pour l’instant |
| Chine orientale |1er novembre 2016 |
| Chine du Nord |1er novembre 2016 |
| Centre de l’Allemagne |1er novembre 2016 |
| Nord-Est de l’Allemagne |1er novembre 2016 |
| Inde-Ouest |Premium Storage non disponible pour l’instant |
| Ouest du Japon |Premium Storage non disponible pour l’instant |
| États-Unis - partie centrale septentrionale |10 novembre 2016 |

## <a name="automatic-migration-details"></a>Détails sur la migration automatique
Par défaut, nous allons migrer votre base de données pour vous entre 18:00 et 6:00 heures (heure locale de votre région) à un moment pendant la [planification de la migration automatique][automatic migration schedule] (voir section ci-dessous).  L’entrepôt de données existant est inutilisable lors de la migration.  Nous estimons que la migration dure environ une heure par To de stockage, pour chaque entrepôt de données.  Nous allons également nous assurer que vous n’êtes facturé à aucun moment de la migration automatique.

> [!NOTE]
> Vous ne serez pas en mesure d’utiliser votre entrepôt de données existant lors de la migration.  Une fois la migration terminée, votre entrepôt de données sera remis en ligne.
>
>

Les informations ci-dessous détaillent les étapes que Microsoft exécute pour vous afin d’effectuer la migration. Vous n’avez pas besoin d’intervenir.  Dans cet exemple, imaginez que votre entrepôt de données existant sur le stockage standard est appelé « MyDW ».

1. Microsoft renomme l’élément « MyDW » ainsi : « MyDW_ DO_NOT_USE_ [Horodatage] ».
2. Microsoft interrompt « MyDW_ DO_NOT_USE_ [Horodatage] ».  Une sauvegarde est effectuée pendant ce temps.  En cas de problème, il se peut que le processus s’interrompe et se relance plusieurs fois.
3. Microsoft crée un entrepôt de données nommé « MyDW » dans Premium Storage en s’appuyant sur la sauvegarde effectuée à l’étape 2.  L’entrepôt « MyDW » n’apparaît que lorsque la restauration est terminée.
4. Une fois la restauration terminée, « MyDW » renvoie les mêmes DWU et retrouve l’état actif ou interrompu qu’il présentait avant la migration.
5. Une fois la migration terminée, Microsoft supprime « MyDW_DO_NOT_USE_ [Horodatage] ».

> [!NOTE]
> Ces paramètres ne sont pas repris dans le cadre de la migration :
>
> * L’audit au niveau de la base de données doit être réactivé.
> * Des règles de pare-feu au niveau **base de données** doivent être rajoutées.  Les règles de pare-feu au niveau **serveur** ne sont pas affectées.
>
>

### <a name="automatic-migration-schedule"></a>Planification de la migration automatique
Les migrations automatiques se produisent entre 18:00 et 6 heures du matin (heure locale de la région) pendant le temps d’arrêt suivant.

| **Région** | **Date de début estimée** | **Date de fin estimée** |
|:--- |:--- |:--- |
| Est de l’Australie |Pas encore déterminée |Pas encore déterminée |
| Chine orientale |9 janvier 2017 |13 janvier 2017 |
| Chine du Nord |9 janvier 2017 |13 janvier 2017 |
| Centre de l’Allemagne |9 janvier 2017 |13 janvier 2017 |
| Nord-Est de l’Allemagne |9 janvier 2017 |13 janvier 2017 |
| Inde-Ouest |Pas encore déterminée |Pas encore déterminée |
| Ouest du Japon |Pas encore déterminée |Pas encore déterminée |
| États-Unis - partie centrale septentrionale |9 janvier 2017 |13 janvier 2017 |

## <a name="self-migration-to-premium-storage"></a>Migration ponctuelle vers Premium Storage
Si vous souhaitez déterminer à quel moment le temps d’arrêt doit se produire, vous pouvez suivre la procédure ci-après, qui permet de migrer un entrepôt de données existant sur un stockage standard vers Premium Storage.  Si vous optez pour une migration ponctuelle, vous devez effectuer cette opération avant le début de la migration automatique effectuée dans cette région, afin d’éviter tout conflit généré par cette dernière (voir [Planification de la migration automatique][automatic migration schedule]).

### <a name="self-migration-instructions"></a>Instructions relatives à la migration ponctuelle
Si vous souhaitez déterminer le temps d’arrêt de votre système, vous pouvez migrer votre entrepôt de données de manière ponctuelle, à l’aide de la fonction de sauvegarde/restauration.  L’opération de restauration de la migration doit durer environ une heure par To de stockage pour chaque entrepôt de données.  Si vous souhaitez conserver le même nom une fois la migration terminée, suivez les étapes ci-dessous pour [effectuer un changement de nom pendant la migration][steps to rename during migration].

1. [Interrompez][Pause] l’entrepôt de données associé à une sauvegarde automatique.
2. [Effectuez une restauration][Restore] à partir de votre instantané le plus récent.
3. Supprimez votre entrepôt de données existant sur le stockage standard. **Si vous n’effectuez pas cette étape, vous serez facturé pour les deux entrepôts de données.**

> [!NOTE]
> Ces paramètres ne sont pas repris dans le cadre de la migration :
>
> * L’audit au niveau de la base de données doit être réactivé.
> * Des règles de pare-feu au niveau **base de données** doivent être rajoutées.  Les règles de pare-feu au niveau **serveur** ne sont pas affectées.
>
>

#### <a name="optional-steps-to-rename-during-migration"></a>Facultatif : étapes pour renommer des éléments pendant la migration
Deux bases de données situées sur un même serveur logique ne peuvent pas présenter le même nom. SQL Data Warehouse permet maintenant de renommer une DWU.

Dans cet exemple, imaginez que votre entrepôt de données existant sur le stockage standard est appelé « MyDW ».

1. Renommez « MyDW » à l’aide de la commande ALTER DATABASE, par exemple de la manière suivante : « MyDW_BeforeMigration ».  Cette commande arrête toutes les transactions existantes et doit être exécutée dans la base de données pour réussir.
   ```
   ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
   ```
2. [Interrompez][Pause] l’entrepôt de données « MyDW_BeforeMigration » associé à une sauvegarde automatique.
3. À partir de votre dernier instantané, [effectuez la restauration][Restore] d’une nouvelle base de données portant l’ancien nom (par exemple « MyDW »)
4. Supprimez « MyDW_BeforeMigration ».  **Si vous n’effectuez pas cette étape, vous serez facturé pour les deux entrepôts de données.**

> [!NOTE]
> Ces paramètres ne sont pas repris dans le cadre de la migration :
>
> * L’audit au niveau de la base de données doit être réactivé.
> * Des règles de pare-feu au niveau **base de données** doivent être rajoutées.  Les règles de pare-feu au niveau **serveur** ne sont pas affectées.
>
>

## <a name="next-steps"></a>Étapes suivantes
Avec l’évolution de Premium Storage, nous avons également augmenté le nombre de fichiers blob de base de données dans l’architecture sous-jacente de votre entrepôt de données.  Pour optimiser les avantages en termes de performances de cette modification, nous vous recommandons de reconstruire vos index columnstore en cluster à l’aide du script suivant.  Le script ci-dessous force le déplacement de certaines de vos données existantes vers les objets blob supplémentaires.  Dans le cas contraire, les données seront naturellement redistribuées au fil du temps, quand vous chargerez d’autres données dans les tables de votre entrepôt de données.

**Conditions préalables :**

1. Data Warehouse doit s’exécuter avec au moins 1 000 DWU (voir [Mise à l’échelle de la puissance de calcul][scale compute power])
2. L’utilisateur qui exécute le script doit avoir le rôle [mediumrc][mediumrc role] ou un rôle supérieur
   1. Pour ajouter un utilisateur à ce rôle, exécutez la commande suivante :
      1. ````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Step 1: Create Table to control Index Rebuild
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------
create table sql_statements
WITH (distribution = round_robin)
as select
    'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement,
    row_number() over (order by s.name, t.name) as sequence
from
    sys.schemas s
    inner join sys.tables t
        on s.schema_id = t.schema_id
where
    is_external = 0
;
go

--------------------------------------------------------------------------------
-- Step 2: Execute Index Rebuilds.  If script fails, the below can be rerun to restart where last left off
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------

declare @nbr_statements int = (select count(*) from sql_statements)
declare @i int = 1
while(@i <= @nbr_statements)
begin
      declare @statement nvarchar(1000)= (select statement from sql_statements where sequence = @i)
      print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement
      exec (@statement)
      delete from sql_statements where sequence = @i
      set @i += 1
end;
go
-------------------------------------------------------------------------------
-- Step 3: Cleanup Table Created in Step 1
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

Si vous rencontrez des problèmes liés à votre entrepôt de données, [créez un ticket de support][create a support ticket], en indiquant « Migration vers Premium Storage » comme cause possible.

<!--Image references-->

<!--Article references-->
[automatic migration schedule]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[create a support ticket]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pause]: sql-data-warehouse-manage-compute-portal.md#pause-compute
[Restore]: sql-data-warehouse-restore-database-portal.md
[steps to rename during migration]: #optional-steps-to-rename-during-migration
[scale compute power]: sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[mediumrc role]: sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[Premium Storage for greater performance predictability]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure Portal]: https://portal.azure.com



<!--HONumber=Nov16_HO5-->


