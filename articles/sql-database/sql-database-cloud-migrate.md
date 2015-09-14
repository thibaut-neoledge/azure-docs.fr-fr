<properties
   pageTitle="Migration d’une base de données vers Azure SQL Database"
	description="Microsoft Azure SQL Database, déployer base de données, migration base de données, importer base de données, exporter base de données, assistant de migration"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor=""/>

<tags
   ms.service="sql-database"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-management"
	ms.date="09/02/2015"
	ms.author="carlrab"/>

# Migration d’une base de données vers Base de données SQL Azure

Azure SQL Database V12 offre une compatibilité moteur presque entière avec SQL Server 2014 et versions ultérieures. Par conséquent, la tâche de migration de la plupart des bases de données à partir d’une instance locale de SQL Server 2005 ou supérieure à une base de données SQL Azure est beaucoup plus simple. La migration de plusieurs bases de données est une opération simple et l’opération de déplacement de données nécessite peu, voire aucune, modification du schéma ou pas de nouvelle ingénierie des applications. Si les bases de données doivent être modifiées, l’étendue de ces modifications est plus limitée.

Par conception, les fonctions de d’étendue de serveur SQL Server ne sont pas prises en charge par la base de données V12 SQL Azure. Les bases de données et applications qui reposent sur ces fonctionnalités ont besoin d’une nouvelle ingénierie avant de pouvoir être migrées. Bien que la base de données Azure SQL V12 améliore la compatibilité avec la base de données SQL Server locale, la migration doit malgré tout être planifiée et exécutée avec précaution, en particulier pour ce qui concerne les bases de données volumineuses et complexes.

## Détermination de la compatibilité
Pour déterminer si votre base de données SQL Server locale est compatible Base de données SQL Azure V12, vous pouvez commencer la migration à l’aide d’une des deux méthodes décrites sous l’option n° 1 ci-dessous et voir si les routines de validation de schéma détectent une incompatibilité, ou vous pouvez utiliser des outils de données SQL Server dans Visual Studio comme indiqué dans l’option n° 2 ci-dessous afin de valider la compatibilité. Si votre base de données SQL Server locale présente des problèmes de compatibilité, vous pouvez utiliser les outils de données SQL Server dans Visual Studio ou SQL Server Management Studio pour corriger et résoudre les problèmes de compatibilité.

## Méthodes de migration
Il existe plusieurs méthodes permettant la migration d’une base de données utilisateur SQL Server locale vers une base de données SQL Server Azure V12.

- Pour les bases de données petites à moyennes, la migration de bases de données SQL Server 2005 compatibles ou ultérieures revient à exécuter l’Assistant de déploiement de base de données base de données vers Microsoft Azure dans SQL Server Management Studio, à condition que vous n’ayez pas de problèmes de connectivité (aucune connectivité, faible bande passante ou problèmes de délai d’attente).
- Pour les bases de données moyennes à grandes ou lorsque vous avez des problèmes de connectivité, vous pouvez utiliser SQL Server Management Studio pour exporter les données et le schéma vers un fichier BACPAC (stocké localement ou dans un objet blob Azure), puis importer le fichier BACPAC dans votre instance de SQL Azure. Si vous stockez le fichier BACPAC dans un objet blob Azure, vous pouvez également importer le fichier BACPAC à partir de l’intérieur du portail Azure. Pour plus d'informations sur un fichier BACPAC, consultez [Applications de couche données](https://msdn.microsoft.com/library/ee210546.aspx).
- Pour les bases de données plus volumineuses, vous obtiendrez les meilleures performances en faisant migrer séparément le schéma et les données. Vous pouvez extraire le schéma dans un projet de base de données à l’aide de SQL Server Management Studio ou de Visual Studio, puis déployer le schéma pour créer la base de données SQL Azure. Vous pouvez ensuite extraire les données à l’aide de BCP et utiliser BCP pour importer les données à l’aide de flux parallèles dans la base de données SQL Azure. La migration d’une base de données volumineuse et complexe peut prendre plusieurs heures, quelle que soit la méthode que vous choisissez.

### Option 1
******Migrer une base de données compatible à l’aide de SQL Server Management Studio (SSMS) ***

SQL Server Management Studio fournit deux méthodes pour migrer votre base de données SQL Server locale compatible avec une base de données SQL Azure. Vous pouvez soit utiliser l’Assistant de déploiement de base de données SQL Microsoft Azure, soit exporter la base de données vers un fichier BACPAC, qui peut ensuite être importé pour créer une nouvelle base de données SQL Azure. L’Assistant valide la compatibilité de base de données SQL V12 Azure, extrait le schéma et les données dans un fichier BACPA, puis importe ce dernier dans l’instance de base de données SQL Azure spécifiée. Pour utiliser cette option, consultez [Utiliser SSMS](sql-database-migrate-ssms.md).

### Option 2
***Mettre à jour le schéma de base de données hors connexion à l’aide de Visual Studio, puis le déployer avec SQL Server Management Studio***

Si votre base de données SQL Server locale n’est pas compatible ou pour déterminer si elle l’est, vous pouvez importer le schéma de base de données dans un projet de base de données de Visual Studio pour analyse. Pour l’analyse, vous devez désigner la plate-forme cible du projet en tant que base de données V12, puis réalisez le projet. Si la version est réussie, la base de données est compatible. Si la version échoue, vous pouvez résoudre les erreurs dans SQL Server Data Tools pour Visual Studio (« SSDT »). Une fois la génération du projet réussie, vous pouvez de nouveau publier ce dernier en tant que copie de base de données source, puis utiliser la fonction de comparaison des données de SSDT pour copier les données de la base de données source vers la base de données Azure SQL V12 compatible. Cette base de données mise à jour est ensuite déployée vers Azure SQL Database à l'aide de l'option 1. Si seule la migration de schéma est requise, vous pouvez publier le schéma directement depuis Visual Studio vers la base de données SQL Azure. Utilisez cette méthode lorsque le schéma de base de données nécessite un plus grand nombre de modifications qui peuvent être gérées par l’Assistant seul. Pour utiliser cette option, consultez [Utiliser Visual Studio](sql-database-migrate-visualstudio-ssdt.md).

## Choix des options à utiliser
- Si vous pensez qu’une base de données peut être migrée sans modification, nous vous conseillons d’utiliser l’option 1 qui est simple et rapide. Si vous avez des doutes, commencez par exporter un BACPAC de schéma uniquement à partir de la base de données, comme indiqué dans l’option 1. Si l’exportation réussit sans erreur, vous pouvez utiliser l’option 1 pour migrer la base de données avec ses données.  
- Si vous rencontrez des erreurs lors de l’exportation de l’option n ° 1, utilisez l’option n ° 2 et corrigez le schéma de base de données hors connexion dans Visual Studio en utilisant une combinaison de l’Assistant migration et de modifications de schéma appliquées manuellement. Une copie de la base de données source est ensuite mise à jour sur place et migrée vers Azure à l’aide de l’option 1.

## Outils de migration
Les outils utilisés incluent SQL Server Management Studio (SSMS), les outils SQL Server dans Visual Studio (VS, SSDT) ainsi que le portail Azure.

> Veillez à installer les dernières versions des outils clients, car les versions antérieures ne sont pas compatibles avec les bases de données SQL Azure V12.

### SQL Server Management Studio (SSMS)
SSMS peut être utilisé pour déployer une base de données compatible directement vers Azure SQL Database ou pour exporter une sauvegarde logique de la base de données sous forme de fichier BACPAC, qui peut ensuite être importé, toujours à l’aide de SSMS, pour créer une nouvelle base de données SQL Azure.

[Téléchargez la dernière version de SSMS](https://msdn.microsoft.com/library/mt238290.aspx).

### Outils SQL Server dans Visual Studio (VS, SSDT)
Vous pouvez utiliser les outils SQL Server dans Visual Studio pour créer et gérer un projet de base de données comprenant un ensemble de fichiers Transact-SQL pour chaque objet du schéma. Le projet peut être importé à partir d'une base de données ou d'un fichier de script. Une fois le projet créé, il peut être importé vers Azure SQL Database v12. La création du projet valide ensuite la compatibilité du schéma. Le fait de cliquer sur une erreur ouvre le fichier Transact-SQL correspondant, ce qui permet de l’éditer et de corriger l’erreur. Une fois que toutes les erreurs sont corrigées, le projet peut être publié, soit directement vers SQL Database pour créer une base de données vide, soit vers (une copie de) la base de données SQL Server d'origine pour en mettre à jour le schéma, ce qui permet de déployer la base de données avec ses données à l'aide de SSMS, comme indiqué ci-dessus.

Utilisez la [dernière version de SQL Server Data Tools pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) avec Visual Studio 2013 Update 4 ou version ultérieure.

## Comparaisons
| Option 1 | Option 2 |
| ------------ | ------------ | ------------ |
| Déployer une base de données compatible vers Azure SQL Database | Mettre à jour la base de données existante et la déployer vers Azure SQL Database |
|![SSMS](./media/sql-database-cloud-migrate/01SSMSDiagram.png)| ![Modification hors connexion](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png) |
| Utilise SSMS | Utilise Visual Studio et SSMS |
|Processus simple nécessitant que le schéma soit compatible. Le schéma est migré inchangé. | Le schéma est importé dans un projet de base de données dans Visual Studio. Des mises à jour supplémentaires sont effectuées à l’aide de SQL Server Data Tools pour Visual Studio et le schéma final est utilisé pour mettre à jour la base de données sur place. |
| Déploie ou exporte toujours la base de données entière. | Contrôle total des objets qui sont inclus dans la migration. |
| Aucune disposition pour modifier la sortie en cas d'erreur, le schéma source doit être compatible. | Toutes les fonctions de SSDT pour Visual Studio sont disponibles. Le schéma est modifié hors connexion. | La validation de l'application se produit dans Azure. Doit être minime car le schéma est migré sans modification. | La validation de l'application peut être effectuée dans SQL Server avant le déploiement de la base de données vers Azure. |
| Processus à une ou deux étapes, simple et facile à configurer. | Processus en plusieurs étapes, plus complexe (plus simple en cas de déploiement du schéma uniquement). |

<!---HONumber=September15_HO1-->