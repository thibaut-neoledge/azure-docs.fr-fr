<properties 
   pageTitle="Migration vers la base de données SQL Azure" 
   description="Base de données Microsoft Azure SQL, déployer base de données, migration base de données, importer base de données, exporter base de données, assistant de migration" 
   services="sql-database" 
   documentationCenter="" 
   authors="pehteh" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/15/2015"
   ms.author="pehteh"/>

# Vue d'ensemble
La base de données SQL Azure V12 offre une compatibilité moteur presque entière avec SQL Server 2014. Par conséquent, elle simplifie considérablement la tâche de migration de la plupart des bases de données de SQL Server vers la base de données SQL Azure. La migration de plusieurs bases de données est une opération de déplacement simple nécessitant peu voire aucune modification du schéma et peu ou pas de nouvelle ingénierie des applications. Et si les bases de données doivent être modifiées, l'étendue de ces modifications est plus limitée.

De par leur conception, les fonctionnalités de portée serveur de SQL Server ne sont pas prises en charge par la base de données SQL. Ainsi, les bases de données et les applications qui reposent sur ces fonctionnalités nécessiteront une réingénierie supplémentaire avant de pouvoir être migrées. Bien que la base de données SQL V12 améliore la compatibilité avec SQL Server, la migration doit malgré tout être planifiée et exécutée avec précaution, en particulier pour les bases de données volumineuses plus complexes.

## Aperçu
Il existe différentes approches pour la migration d'une base de données SQL Server vers Azure et chaque approche utilise un ou plusieurs outils. Certaines approches sont rapides et faciles, tandis que d'autres sont plus longues à préparer. N'oubliez pas que la migration d'une base de données volumineuse complexe peut prendre plusieurs heures.

### Option 1
***Migrer une base de données compatible à l'aide de SQL Server Management Studio (SSMS)***

La base de données est déployée à partir de la base de données SQL Azure à l'aide de SSMS. La base de données peut être déployé directement ou exportée vers un fichier BACPAC, qui est ensuite importé pour créer une nouvelle base de données SQL Azure. À utiliser lorsque la base de données source est entièrement compatible avec la base de données SQL Azure.

### Option 2
***Migrer une base de données presque compatible à l'aide de l'Assistant Migration SQL Azure (SAMW)***

La base de données est traitée à l'aide de l'Assistant Migration SQL Azure pour générer un script de migration contenant un schéma ou un schéma et des données au format BCP. À utiliser lorsque le schéma de base de données nécessite une mise à niveau et que les modifications peuvent être gérées par l'Assistant.

### Option 3
***Mettre à jour le schéma de base de données hors connexion à l'aide de Visual Studio (VS) et de SAMW, et déployer avec SSMS***

La base de données source est importée dans un projet de base de données Visual Studio pour le traitement hors connexion. L'Assistant Migration SQL Azure est alors exécuté sur tous les scripts du projet pour appliquer une série de transformations et de corrections. Le projet a pour cible la base de données SQL V12, il est créé et les erreurs restantes sont signalées. Ces erreurs sont ensuite résolues manuellement à l'aide des outils SQL Server dans Visual Studio. Une fois le projet créé avec succès, il est publié vers une copie de la base de données source. Cette base de données mise à jour est ensuite déployée vers la base de données SQL Azure à l'aide de l'option 1. Si seule la migration de schéma est requise, vous pouvez publier le schéma directement depuis Visual Studio vers la base de données SQL Azure. À utiliser lorsque le schéma de base de données nécessite plus de modifications que ce qui peut être géré par SAMW.

## Choix des options à utiliser
- Si vous pensez qu'une base de données peut être migrée sans modification, nous vous conseillons d'utiliser l'option 1 qui est rapide et facile. Si vous avez des doutes, commencez par exporter un BACPAC de schéma uniquement à partir de la base de données, comme indiqué dans l'option 1. Si l'exportation réussit sans erreur, vous pouvez utiliser l'option 1 pour migrer la base de données avec ses données.  
- Si vous rencontrez des erreurs lors de l'exportation de l'option 1, utilisez l'Assistant Migration SQL Azure (SAMW) pour traiter la base de données en mode schéma uniquement comme décrit dans l'option 2. Si SAMW ne signale aucune erreur, l'option 2 peut être utilisée. 
- Si SAMW indique que le schéma doit être retravaillé, nous vous recommandons d'utiliser l'option 3 (à moins que les modifications requises ne soient simples) et de corriger le schéma de base de données hors connexion dans Visual Studio à l'aide de SAMW et de modifications du schéma appliquées manuellement. Une copie de la base de données source est ensuite mise à jour sur place et migrée vers Azure à l'aide de l'option 1.

## Outils de migration
Les outils utilisés incluent SQL Server Management Studio (SSMS), les outils SQL Server dans Visual Studio (VS, SSDT) et l'Assistant Migration SQL Azure (SAMW), ainsi que le portail Azure.

> Veillez à installer les dernières versions des outils clients car les versions antérieures ne sont pas compatibles avec la base de données SQL v12.

### SQL Server Management Studio (SSMS)
SSMS peut être utilisé pour déployer une base de données compatible directement vers une base de données SQL Azure ou pour exporter une sauvegarde logique de la base de données sous forme de fichier BACPAC, qui peut ensuite être importé, toujours à l'aide de SSMS, pour créer une nouvelle base de données SQL Azure.

Vous devez utiliser la version la plus récente de SSMS (CU6 dans SQL Server 2013 et versions ultérieures) ou télécharger la [dernière version](http://msdn.microsoft.com/evalcenter/dn434042.aspx) de l'outil.

### Assistant Migration SQL Azure (SAMW)
SAMW peut être utilisé pour analyser le schéma d'une base de données existante en termes de compatibilité avec la base de données SQL Azure. Dans de nombreux cas, il peut être utilisé pour générer et déployer un script T-SQL contenant le schéma et les données. S'il rencontre du contenu de schéma qu'il ne peut pas transformer, l'Assistant signale les erreurs au cours de la transformation. Le cas échéant, le script généré nécessitera une édition supplémentaire avant de pouvoir être déployé avec succès. SAMW traite le corps des fonctions ou des procédures stockées qui est normalement exclu de la validation effectuée par les outils SQL Server dans Visual Studio (voir ci-dessous). Il est donc susceptible de trouver des problèmes qui ne seraient pas signalés par la validation dans Visual Studio uniquement. L'utilisation de SAMW en association avec les outils SQL Server dans Visual Studio peut réduire considérablement la quantité de travail nécessaire pour migrer un schéma complexe.

Veillez à utiliser la dernière version de l'[Assistant Migration SQL Azure](http://sqlazuremw.codeplex.com/) de CodePlex.

### Outils SQL Server dans Visual Studio (VS, SSDT)
Vous pouvez utiliser les outils SQL Server dans Visual Studio pour créer et gérer un projet de base de données comprenant un ensemble de fichiers T-SQL pour chaque objet dans le schéma. Le projet peut être importé à partir d'une base de données ou d'un fichier de script. Une fois le projet créé, il peut être importé vers la base de données SQL Azure v12. La création du projet valide ensuite la compatibilité du schéma. Le fait de cliquer sur une erreur ouvre le fichier T-SQL correspondant, ce qui permet de l'éditer et de corriger l'erreur. Une fois que toutes les erreurs sont corrigées, le projet peut être publié, soit directement vers la base de données SQL pour créer une base de données vide, soit vers (une copie de) la base de données SQL Server d'origine pour en mettre à jour le schéma, ce qui permet de déployer la base de données avec ses données à l'aide de SSMS, comme indiqué ci-dessus.

Vous devez utiliser la dernière version des outils de base de données SQL Server pour Visual Studio pour la base de données SQL Azure V12. Vérifiez que vous disposez de Visual Studio 2013 avec la mise à jour 4 installée.

## Comparaisons
| Option 1 | Option 2 | Option 3 |
| ------------ | ------------ | ------------ |
| Déployer une base de données compatible vers la base de données SQL Azure | Générer un script de migration avec des modifications et exécuter sur la base de données SQL Azure | Mettre à jour la base de données existante et la déployer vers la base de données SQL Azure |
|![SSMS](./media/sql-database-cloud-migrate/01SSMSDiagram.png)| ![SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png) | ![Modification hors connexion](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png) |
| Utilise SSMS | Utilise SAMW | Utilise SAMW, VS, SSMS |
|Processus simple nécessitant que le schéma soit compatible. Le schéma est migré inchangé. | Le script T-SQL est généré par SAMW et inclut les modifications requises pour assurer la compatibilité. Certaines fonctionnalités non prises en charge sont supprimées du schéma, la plupart est signalée comme des erreurs. | Le schéma est importé dans un projet de base de données dans Visual Studio et (éventuellement) transformé avec SAMW. Des mises à jour supplémentaires sont effectuées à l'aide des outils SQL Server dans Visual Studio et le schéma final est utilisé pour mettre à jour la base de données sur place. |
| En cas d'exportation d'un fichier BACPAC, il est possible de choisir de migrer uniquement le schéma. | Possibilité de configurer l'Assistant pour créer un script de schéma ou un schéma et des données. | Possibilité de publier uniquement le schéma directement vers Azure à partir de Visual Studio. La base de données est mise à jour avec les modifications requises pour permettre le déploiement/l'exporation du schéma et des données. |
| Déploie ou exporte toujours la base de données entière. | Peut choisir d'exclure des objets spécifiques de la migration. | Contrôle total des objets qui sont inclus dans la migration. |
| Aucune disposition pour modifier la sortie en cas d'erreur, le schéma source doit être compatible. | Le script généré monolithique unique peut s'avérer difficile à modifier si nécessaire. Le script peut être ouvert et modifié dans SSMS ou Visual Studio avec les outils de base de données SQL Server. Toutes les erreurs doivent être corrigées avant que le script puisse être déployé vers la base de données SQL Azure.| Toutes les fonctionnalités des outils SQL Server dans Visual Studio sont disponibles. Le schéma est modifié hors connexion. |
| La validation de l'application se produit dans Azure. Doit être minime car le schéma est migré sans modification. | La validation de l'application se produit dans Azure après la migration. Le script généré peut également être installé sur site pour la validation de l'application initiale. | La validation de l'application peut être effectuée dans SQL Server avant le déploiement de la base de données vers Azure. |
| Outil pris en charge par Microsoft. | Outil communautaire pris en charge téléchargé à partir de CodePlex. | Outils pris en charge par Microsoft avec utilisation facultative de l'outil communautaire pris en charge téléchargé à partir de CodePlex. |
| Processus simple en une ou deux étapes configuré facilement. | La transformation de schéma, la génération et le déploiement dans le cloud sont orchestrés à partir d'un assistant unique facile à utiliser. | Processus en plusieurs étapes plus complexe (plus simple en cas de déploiement du schéma uniquement). |

<!---HONumber=58-->