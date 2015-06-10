<properties 
   pageTitle="Migration à l’aide de l’Assistant Migration SQL" 
   description="Base de données SQL Microsoft Azure, migration de base de données, importer une base de données, exporter une base de données, assistant de migration" 
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
   ms.date="04/14/2015"
   ms.author="pehteh"/>

# Opérations de migration à l’aide de l’Assistant Migration SQL Azure

![alt text](./media/sql-database-migration-wizard/01SAMWDiagram.png)

Cette option utilise l’Assistant Migration SQL Azure pour générer un script T-SQL à partir d’une base de données source, laquelle est ensuite transformée par l’assistant afin d’être compatible avec la base de données SQL. Ensuite, elle se connecte à la base de données SQL Microsoft Azure pour exécuter le script sur une base de données SQL Microsoft Azure vide. Ce script peut être généré avec un schéma uniquement, ou peut inclure des données au format BCP. L’assistant permet également d’inclure ou d’exclure des objets spécifiques de la base de données dans le script.

Remarque : les schémas non compatibles que l’assistant est susceptible de détecter ne sont pas tous traités par ses transformations intégrées. Un script non compatible qui ne peut pas être traité est signalé comme une erreur et des commentaires sont insérés dans le script généré. Dans ce cas, le script doit être enregistré et modifié manuellement avant d’être soumis à la base de données SQL Microsoft Azure. S’il est nécessaire d’apporter des modifications, vous pouvez enregistrer le script, puis le modifier avec SSMS ou les outils SQL Server de VS. Une fois sa compatibilité assurée, il peut être exécuté hors-bande sur la base de données SQL Microsoft Azure.

> Remarque : si de nombreuses erreurs sont détectées et si leur correction n’est pas simple, vous pouvez importer le fichier script généré dans un projet de base de données au sein de Visual Studio, en plus de cette option. Si vous définissez la cible du projet sur la version 12 de la base de données SQL, vous pouvez ensuite générer le projet et corriger les erreurs de manière progressive, à l’aide des outils SQL Server de VS. Lorsque le schéma ne contient plus d’erreurs, vous pouvez le publier vers une copie de la base de données source, puis utiliser SSMS pour déployer ou exporter/importer la base de données dans la base de données SQL Microsoft Azure (comme indiqué dans l’option 1).

## Étapes de la migration
1.	Configurez une nouvelle base de données, sur un nouveau serveur au sein de la base de données SQL ou sur un serveur existant, mis à niveau selon la procédure décrite précédemment. L’exécution du script de migration créé via cette option sur cette base de données constitue l’étape finale. 

2.	Ouvrez l’assistant de migration, choisissez l’option **Analyser/migrer une base de données** et définissez l’option **Serveur cible** sur la base de données SQL Microsoft Azure version 12. Ensuite, cliquez sur **Suivant**.

![alt text](./media/sql-database-migration-wizard/02MigrationWizard.png)

3.	Sur la page qui s’affiche ensuite, cliquez sur **Se connecter au serveur** et fournissez les informations de connexion de votre serveur source. Indiquez une base de données source dans la boîte de dialogue de connexion, ou connectez-vous au serveur, puis choisissez la base de données source dans la liste de bases de données. 

![alt text](./media/sql-database-migration-wizard/03MigrationWizard.png)

4.	Sur la page Sélectionner les objets (qui s’affiche ensuite), vous pouvez indiquer si vous voulez écrire un script pour tous les objets de base de données (par défaut) ou sélectionner les objets de base de données devant faire l’objet d’un script. Peut-être est-il plus simple de commencer par écrire un script pour tous les objets lors d’un premier passage, puis de revenir à cette étape et d’exclure des objets, le cas échéant, lorsque l’assistant signale des erreurs de script ou de transformation. L’assistant commence par écrire un script pour les objets de la base de données (en utilisant la fonction SMO), puis effectue un traitement du script généré, afin d’appliquer un ensemble de règles de transformation et de validation basées sur des expressions régulières.

![alt text](./media/sql-database-migration-wizard/04MigrationWizard.png)

4.	Sélectionnez Avancé, puis passez en revue les options de configuration utilisées par l’assistant. En cas de premier passage, notamment si la base de données est volumineuse, nous vous recommandons de définir l’option **Script de table/données** sur la valeur Schéma de table uniquement, de vérifier que l’option **Serveur cible** est bien définie sur la base de données SQL Microsoft Azure version 12 et que l’option ** 	Contrôles de compatibilité** a pour valeur Remplacer : Effectuer tous les contrôles de compatibilité.

![alt text](./media/sql-database-migration-wizard/05MigrationWizard.png)

5.	Cliquez sur **Suivant** pour vérifier les options, puis à nouveau sur **Suivant** pour générer et transformer le script. L’onglet Script SQL vous permet de vérifier le script.

6.	Le processus de génération du script signale une erreur lorsque le schéma n’est pas compatible avec la base de données SQL et ne peut pas être transformé par l’assistant. Dans la capture d’écran ci-dessous, le processus de transformation a détecté des problèmes concernant une procédure stockée. Dans ce cas, une procédure a été écrite pour indiquer au système d’utiliser la fonction de recherche en texte intégral, qui n’est pas encore prise en charge par la version 12 (mais le sera par les versions ultérieures).

![alt text](./media/sql-database-migration-wizard/06MigrationWizard.png) - En fonction de votre évaluation des erreurs, vous pouvez être amené à revenir en arrière dans l’assistant et à exclure les objets qui sont à l’origine des problèmes, puis à régénérer le script. Lorsque vous décidez de la méthode à suivre pour gérer les erreurs, tenez compte de l’impact de cette méthode sur d’autres objets de la base de données, ainsi que sur les applications qui utilisent cette dernière. - Si le script présente des erreurs qui doivent être corrigées, vous pouvez enregistrer un fichier de script de schéma uniquement, via l’onglet Script SQL, puis ouvrir et modifier le script dans l’éditeur de votre choix, afin de corriger les erreurs avant d’exécuter ce dernier hors de l’outil SAMW, sur la nouvelle base de données que vous avez créée à l’étape 1. Si le script est étendu ou s’il existe un grand nombre d’erreurs, vous devez utiliser l’option 3. Remarque : vous pouvez importer le fichier de script généré dans Visual Studio, mais une opération d’importation à partir d’un fichier SQL peut prendre nettement plus de temps qu’une importation à partir d’une base de données (voir option 3).

7.	S’il n’y a aucune erreur ou si vous avez supprimé la source d’erreurs résultant de la génération, vous pouvez cliquer sur **Suivant**, puis sur **Se connecter au serveur** (sur la page suivante) afin de vous connecter au serveur de base de données SQL Microsoft Azure que vous avez créé à l’étape 1.

![alt text](./media/sql-database-migration-wizard/07MigrationWizard.png)

8.	L’étape suivante consiste à sélectionner la base de données sur laquelle le script sera exécuté. Toutes les bases de données figurant sur le serveur sont répertoriées. Sélectionnez celle que vous avez créée à l’étape 1. Cette base de données doit être vide et présenter un niveau de tarification approprié pour cette opération. 
- Si vous préférez, vous pouvez créer une base de données à ce stade, à l’aide de l’assistant. Pour cela, cliquez sur Créer une base de données, afin de la créer et de la configurer. Consultez la section Prise en main pour obtenir des conseils sur la sélection du niveau de performances à utiliser lors du processus de migration.
- Une fois que vous avez créé la base de données, continuez. 

9.	Sélectionnez une base de données vide, cliquez sur **Suivant** et confirmez que vous souhaitez exécuter le script pour terminer la migration.

<!---HONumber=58-->