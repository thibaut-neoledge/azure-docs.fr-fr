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
   ms.date="08/19/2016"
   ms.author="nicw;barbkess;sonyama"/>

# Détails relatifs à la migration vers Premium Storage
Le logiciel SQL Data Warehouse a récemment proposé pour la première fois la fonctionnalité [Premium Storage, afin d’optimiser la prévisibilité des performances][]. Nous sommes maintenant prêts à migrer des entrepôts de données figurant actuellement sur le stockage standard vers Premium Storage. Poursuivez votre lecture pour savoir de quelle manière et à quel moment les migrations automatiques s’exécutent, et pour savoir comment effectuer la migration vous-même si vous souhaitez déterminer à quel moment le temps d’arrêt doit se produire.

Si vous disposez de plusieurs entrepôts de données, utilisez les instructions de la section [Planification de la migration automatique][] ci-dessous pour déterminer à quel moment ils doivent également être migrés.

## Déterminer le type de stockage
Si vous avez créé un entrepôt de données avant les dates ci-dessous, cela signifie que vous utilisez actuellement le stockage standard. Chaque entrepôt de données dans le stockage Standard qui sera migré est accompagné d’une annotation dans le [portail Azure][] en haut du panneau de l’entrepôt de données indiquant « *Une mise à niveau à venir vers Premium Storage va nécessiter une interruption. En savoir plus->* ».

| **Région** | **Entrepôt de données créé avant cette date** |
| :------------------ | :-------------------------------- |
| Est de l’Australie | Premium Storage non disponible pour l’instant |
| Sud-est de l’Australie | 5 août 2016 |
| Sud du Brésil | 5 août 2016 |
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
| Est du Japon | 5 août 2016 |
| Ouest du Japon | Premium Storage non disponible pour l’instant |
| États-Unis - partie centrale septentrionale | Premium Storage non disponible pour l’instant |
| Europe du Nord | 5 août 2016 |
| Centre-Sud des États-Unis | 27 mai 2016 |
| Asie du Sud-Est | 24 mai 2016 |
| Europe de l'Ouest | 25 mai 2016 |
| Centre-Ouest des États-Unis | Premium Storage non disponible pour l’instant |
| Ouest des États-Unis | 26 mai 2016 |
| Ouest des États-Unis 2 | Premium Storage non disponible pour l’instant |

## Détails sur la migration automatique
Par défaut, nous allons migrer votre base de données pour vous entre 18:00 et 6 heures du matin (heure locale de votre région) à un moment pendant la [planification de la migration automatique][] \(voir section ci-dessous). L’entrepôt de données existant est inutilisable lors de la migration. Nous estimons que la migration dure environ une heure par To de stockage, pour chaque entrepôt de données. Nous allons également nous assurer que vous n’êtes facturé à aucun moment de la migration automatique.

> [AZURE.NOTE] Vous ne serez pas en mesure d’utiliser votre entrepôt de données existant lors de la migration. Une fois la migration terminée, votre entrepôt de données sera remis en ligne.

Les informations ci-dessous détaillent les étapes que Microsoft exécute pour vous afin d’effectuer la migration. Vous n’avez pas besoin d’intervenir. Dans cet exemple, imaginez que votre entrepôt de données existant sur le stockage standard est appelé « MyDW ».

1.	Microsoft renomme l’élément « MyDW » ainsi : « MyDW\_ DO\_NOT\_USE\_ [Horodatage] ».
2.	Microsoft interrompt « MyDW\_ DO\_NOT\_USE\_ [Horodatage] ». Une sauvegarde est effectuée pendant ce temps. En cas de problème, il se peut que le processus s’interrompe et se relance plusieurs fois.
3.	Microsoft crée un entrepôt de données nommé « MyDW » dans Premium Storage en s’appuyant sur la sauvegarde effectuée à l’étape 2. L’entrepôt « MyDW » n’apparaît que lorsque la restauration est terminée.
4.	Une fois la restauration terminée, « MyDW » renvoie les mêmes DWU et retrouve l’état actif ou interrompu qu’il présentait avant la migration.
5.	Une fois la migration terminée, Microsoft supprime « MyDW\_DO\_NOT\_USE\_ [Horodatage] ».
	
> [AZURE.NOTE] Ces paramètres ne sont pas repris dans le cadre de la migration :
> 
>	-  Auditing at the Database level needs to be re-enabled
>	-  Firewall rules at the **Database** level need to be readded.  Firewall rules at the **Server** level are not be impacted.

### Planification de la migration automatique
Les migrations automatiques se produisent entre 18:00 et 6 heures du matin (heure locale de la région) pendant le temps d’arrêt suivant.

| **Région** | **Date de début estimée** | **Date de fin estimée** |
| :------------------ | :--------------------------- | :--------------------------- |
| Est de l’Australie | Pas encore déterminée | Pas encore déterminée |
| Sud-est de l’Australie | 10 août 2016 | 24 août 2016 |
| Sud du Brésil | 10 août 2016 | 24 août 2016 |
| Centre du Canada | 23 juin 2016 | 1er juillet 2016 |
| Est du Canada | 23 juin 2016 | 1er juillet 2016 |
| Centre des États-Unis | 23 juin 2016 | 4 juillet 2016 |
| Chine orientale | Pas encore déterminée | Pas encore déterminée |
| Chine du Nord | Pas encore déterminée | Pas encore déterminée |
| Asie de l'Est | 23 juin 2016 | 1er juillet 2016 |
| Est des États-Unis | 23 juin 2016 | 11 juillet 2016 |
| Est des États-Unis 2 | 23 juin 2016 | 8 juillet 2016 |
| Inde-Centre | 23 juin 2016 | 1er juillet 2016 |
| Sud de l'Inde | 23 juin 2016 | 1er juillet 2016 |
| Inde-Ouest | Pas encore déterminée | Pas encore déterminée |
| Est du Japon | 10 août 2016 | 24 août 2016 |
| Ouest du Japon | Pas encore déterminée | Pas encore déterminée |
| États-Unis - partie centrale septentrionale | Pas encore déterminée | Pas encore déterminée |
| Europe du Nord | 10 août 2016 | 31 août 2016 |
| Centre-Sud des États-Unis | 23 juin 2016 | 2 juillet 2016 |
| Asie du Sud-Est | 23 juin 2016 | 1er juillet 2016 |
| Europe de l'Ouest | 23 juin 2016 | 8 juillet 2016 |
| Centre-Ouest des États-Unis | 14 août 2016 | 31 août 2016 |
| Ouest des États-Unis | 23 juin 2016 | 7 juillet 2016 |
| Ouest des États-Unis 2 | 14 août 2016 | 31 août 2016 |

## Migration ponctuelle vers Premium Storage
Si vous souhaitez déterminer à quel moment le temps d’arrêt doit se produire, vous pouvez suivre la procédure ci-après, qui permet de migrer un entrepôt de données existant sur un stockage standard vers Premium Storage. Si vous optez pour une migration ponctuelle, vous devez effectuer cette opération avant le début de la migration automatique effectuée dans cette région, afin d’éviter tout conflit généré par cette dernière (consultez [Planification de la migration automatique][]).

### Instructions relatives à la migration ponctuelle
Si vous souhaitez déterminer le temps d’arrêt de votre système, vous pouvez migrer votre entrepôt de données de manière ponctuelle, à l’aide de la fonction de sauvegarde/restauration. L’opération de restauration de la migration doit durer environ une heure par To de stockage pour chaque entrepôt de données. Si vous souhaitez conserver le même nom une fois la migration terminée, suivez les étapes ci-dessous pour [effectuer un changement de nom pendant la migration][].

1.	[Interrompez][] l’entrepôt de données associé à une sauvegarde automatique
2.	[Effectuez la restauration][] à partir de votre instantané le plus récent
3.	Supprimez votre entrepôt de données existant sur le stockage standard. **Si vous n’effectuez pas cette étape, vous serez facturé pour les deux entrepôts de données.**

> [AZURE.NOTE] Ces paramètres ne sont pas repris dans le cadre de la migration :
> 
>	-  Auditing at the Database level needs to be re-enabled
>	-  Firewall rules at the **Database** level need to be readded.  Firewall rules at the **Server** level are not be impacted.

#### Facultatif : étapes pour renommer des éléments pendant la migration 
Deux bases de données situées sur un même serveur logique ne peuvent pas présenter le même nom. SQL Data Warehouse permet maintenant de renommer une DWU.

Dans cet exemple, imaginez que votre entrepôt de données existant sur le stockage standard est appelé « MyDW ».

1.	Renommez « MyDW » à l’aide de la commande ALTER DATABASE, par exemple de la manière suivante : « MyDW\_BeforeMigration ». Cette commande arrête toutes les transactions existantes et doit être exécutée dans la base de données pour réussir.
```
ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
```
2.	[Interrompez][] l’entrepôt de données « MyDW\_BeforeMigration » associé à une sauvegarde automatique
3.	À partir de votre dernier instantané, [effectuez la restauration][] d’une nouvelle base de données portant l’ancien nom (par exemple « MyDW »)
4.	Supprimez « MyDW\_BeforeMigration ». **Si vous n’effectuez pas cette étape, vous serez facturé pour les deux entrepôts de données.**

> [AZURE.NOTE] Ces paramètres ne sont pas repris dans le cadre de la migration :
> 
>	-  Auditing at the Database level needs to be re-enabled
>	-  Firewall rules at the **Database** level need to be readded.  Firewall rules at the **Server** level are not be impacted.

## Étapes suivantes
Avec l’évolution de Premium Storage, nous avons également augmenté le nombre de fichiers blob de base de données dans l’architecture sous-jacente de votre entrepôt de données. Si vous rencontrez des problèmes de performances, nous vous recommandons de reconstruire vos index columnstore en cluster à l’aide du script suivant. Le script ci-dessous force le déplacement de certaines de vos données existantes vers les objets blob supplémentaires. Dans le cas contraire, les données seront naturellement redistribuées au fil du temps, quand vous chargerez d’autres données dans les tables de votre entrepôt de données.

**Conditions préalables :**

1.	Data Warehouse doit s’exécuter avec 1 000 DWU ou plus (voir [mettre à l’échelle une puissance de calcul][])
2.	L’utilisateur qui exécute le script doit avoir un [rôle mediumrc][] ou supérieur
	1.	Pour ajouter un utilisateur à ce rôle, exécutez la commande suivante :
		1.	````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Étape 1 : Créer une table pour contrôler la reconstruction d’index
-- Exécuter en tant qu’utilisateur mediumrc ou supérieur
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
-- Étape 2 : Exécuter les reconstructions d’index Si le script échoue, la partie ci-dessous peut être réexécutée pour redémarrer là où le script s’est arrêté
-- Exécuter en tant qu’utilisateur mediumrc ou supérieur
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
-- Étape 3 : Nettoyer la table créée à l’étape 1
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

Si vous rencontrez des problèmes liés à votre entrepôt de données, [créez un ticket de support][], en indiquant « Migration vers Premium Storage » comme cause possible.

<!--Image references-->

<!--Article references-->
[Planification de la migration automatique]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[créez un ticket de support]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Interrompez]: sql-data-warehouse-manage-compute-portal.md/#pause-compute
[Effectuez la restauration]: sql-data-warehouse-restore-database-portal.md
[effectuer un changement de nom pendant la migration]: #optional-steps-to-rename-during-migration
[mettre à l’échelle une puissance de calcul]: sql-data-warehouse-manage-compute-portal/#scale-compute-power
[rôle mediumrc]: sql-data-warehouse-develop-concurrency/#workload-management

<!--MSDN references-->


<!--Other Web references-->
[Premium Storage, afin d’optimiser la prévisibilité des performances]: https://azure.microsoft.com/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[portail Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0824_2016-->
