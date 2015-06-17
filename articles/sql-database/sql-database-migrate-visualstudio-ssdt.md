<properties 
   pageTitle="Migration effectuée à l’aide de Visual Studio et SSDT" 
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

#Mettre à jour la base de données existante et la déployer dans la base de données SQL Microsoft Azure

![alt text](./media/sql-database-migrate-visualstudio-ssdt/01VSSSDTDiagram.png)

L’utilisation de cette option lors de la migration d’une base de données vers la base de données SQL Microsoft Azure version 12 nécessite l’apport de diverses modifications au schéma, qui ne peuvent pas être gérées via l’Assistant Migration SQL Azure (SAMW). Cela se produit si la base de données utilise les fonctionnalités de SQL Server qui ne sont pas (ou pas encore) prises en charge par la base de données SQL Microsoft Azure. Dans le cadre de cette option, vous utilisez tout d’abord Visual Studio pour créer un projet de base de données à partir de la base de données source. La plateforme du projet cible est alors définie sur la base de données SQL Microsoft Azure version 12 et le projet est généré pour identifier tous les problèmes de compatibilité. L’outil SAMW peut résoudre nombre d’entre eux, mais pas tous. Il est donc utilisé pour traiter tous les scripts des projets lors d’un premier passage. Le recours à cet outil n’est pas obligatoire, mais il est fortement recommandé. En générant le projet une fois le traitement des fichiers de script par SAMW terminé, vous pouvez identifier les problèmes restants, qui doivent ensuite être traités manuellement par le biais des outils de modification du code T-SQL dans Visual Studio. Une fois le projet correctement généré, le schéma est publié vers une copie (opération recommandée) de la base de données source, afin de mettre à jour son schéma et ses données in situ. La base de données mise à jour est ensuite déployée sur Microsoft Azure, de manière directe ou via l’importation d’un fichier BACPAC, au moyen des techniques décrites dans l’option n° 1.
 
Comme cette option implique la mise à jour du schéma de la base de données in situ avant tout déploiement sur Microsoft Azure, nous vous recommandons vivement d’effectuer cette opération sur une copie de la base de données. L’outil de comparaison de schémas de Visual Studio permet de vérifier l’ensemble des modifications qui seront appliquées à la base de données avant de publier le projet.

Le recours à l’Assistant Migration SQL Azure (SAMW) n’est pas obligatoire, mais il est fortement recommandé. L’outil SAMW détecte les problèmes de compatibilité présents dans le corps des fonctions, des procédures stockées et des déclencheurs. Sans cet outil, ces problèmes passeraient inaperçus jusqu’au déploiement. Si le déploiement d’un schéma uniquement est requis, le schéma mis à jour peut être directement publié depuis Visual Studio vers la base de données SQL Microsoft Azure.

## Étapes de la migration

1.	Ouvrez l’**Explorateur d’objets SQL Server** dans Visual Studio. Utilisez l’option **Ajouter SQL Server** pour vous connecter à l’instance SQL Server qui contient la base de données en cours de migration. Localisez la base de données dans cet Explorateur, cliquez avec le bouton droit de la souris sur cette dernière et sélectionnez **Créer un nouveau projet...**. 

![alt text](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)

2.	Configurez les paramètres d’importation sur la valeur **Importer uniquement les objets de portée application**. Présélectionnez les options d’importation des connexions référencées, des autorisations et des paramètres de base de données.

![alt text](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)

3.	Cliquez sur **Démarrer** pour importer la base de données et créer le projet, qui doit contenir un fichier de script T-SQL pour chaque objet de cette base de données. Les fichiers de script sont imbriqués dans des dossiers au sein du projet.

![alt text](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)

4.	Dans l’Explorateur de solutions Visual Studio, cliquez avec le bouton droit de la souris sur le projet de base de données, puis sélectionnez Propriétés. La page **Paramètres du projet** s’ouvre, vous permettant de définir le champ Plateforme cible sur la base de données SQL Microsoft Azure version 12.

![alt text](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)

5.	Facultatif : cliquez avec le bouton droit de la souris sur le projet, puis sélectionnez **Générer** pour générer le projet (cette opération n’est pas absolument nécessaire à ce stade, mais elle vous offre une ligne de base pour évaluer le nombre de problèmes de compatibilité du projet et l’efficacité des étapes suivantes).

![alt text](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)

6.	Facultatif : cliquez avec le bouton droit de la souris sur le projet, puis sélectionnez **Projet d’instantané**. En créant un instantané au début du processus de transformation, voire, le cas échéant, au cours des étapes ultérieures de ce dernier, vous pouvez utiliser l’outil de comparaison de schémas à chaque étape.

![alt text](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png) - Une capture instantanée du projet crée un fichier .dacpac, qui contient le schéma complet de ce projet. Vous pouvez modifier le nom du fichier pour indiquer à quel stade du processus cet instantané a été créé. ![alt text](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

7.	Traitez les fichiers de script importés à l’aide de l’Assistant de Migration Azure SQL (SAMW). Utilisez l’option de dossier et sélectionnez le dossier racine du projet. ![alt text](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)

8.	L’assistant traite chaque fichier de script dans ce dossier et dans tous ses sous-dossiers. Un résumé des résultats s’affiche sur la page suivante. ![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)
9.	Cliquez sur Suivant pour afficher une liste récapitulative des fichiers qui ont été modifiés. 

![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)

>Vous pouvez constater que des copies temporaires des fichiers d’origine (avant traitement) et des fichiers affectés par la transformation sont créées aux emplacements signalés sur la partie supérieure de la page.

10.	Cliquez sur Remplacer, puis sur OK (dans la boîte de dialogue de confirmation) pour remplacer les fichiers d’origine par les fichiers modifiés. Remarque : seuls les fichiers modifiés seront remplacés.
11.	Facultatif. Utilisez l’outil de comparaison de schémas pour comparer le projet avec un instantané antérieur ou avec la base de données d’origine, afin de savoir quelles modifications ont été apportées par l’Assistant. Il peut également être souhaitable de créer un autre instantané à ce stade. 

![alt text](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)

12.	Générez le projet à nouveau (voir étape précédente) pour identifier les erreurs restantes.

13.	Résolvez chaque erreur de manière systématique. Évaluez l’impact de chaque modification sur les applications qui utilisent la base de données.

14.	Lorsque la base de données ne contient pas d’erreurs, cliquez avec le bouton droit de la souris sur le projet, puis sélectionnez **Publier** pour générer et publier la base de données vers une copie de la base de données source (nous vous recommandons vivement d’utiliser une copie, au moins au départ). L’objectif de cette étape est la mise à jour du schéma de base de données source, puis la modification des données de la base de données.
- Avant d’effectuer la publication, selon la version du système SQL Server source, vous devrez peut-être réinitialiser la plateforme du projet cible, afin d’activer le déploiement. Si vous effectuez la migration d’une base de données SQL Server de version antérieure, vous ne devez introduire aucune fonctionnalité non prise en charge par le système SQL Server source dans le projet, sauf si vous migrez la base de données vers une version plus récente de SQL Server. 
- Vous devez configurer l’étape de publication pour activer les options de publication appropriées. Par exemple, si vous avez supprimé ou commenté des objets non pris en charge dans le projet, ces derniers doivent être supprimés de la base de données. Vous devez donc autoriser l’action Publier à supprimer des données dans la base de données cible. 
- Si vous prévoyez plusieurs opérations de publication à la suite. 

15.	Déployez la base de données copiée sur la base de données SQL Microsoft Azure, en utilisant les techniques décrites dans l’option 1.

## Validation de la migration

Une fois que vous avez terminé la migration, il est conseillé de comparer le schéma figurant au sein de la base de données migrée avec celui de la base de données source, afin de vous familiariser avec toutes les modifications qui ont été apportées. Vous pouvez ainsi vous assurer qu’elles se présentent comme il convient et n’entraîneront aucun problème lors de la migration des applications vers la nouvelle base de données. Pour effectuer cette comparaison, vous pouvez utiliser l’outil de comparaison de schémas inclus avec les outils SQL Server dans Visual Studio. Vous pouvez comparer la base de données, dans la base de données SQL Microsoft Azure, avec la base de données SQL Server d’origine ou avec un instantané créé lors de la première importation de la base de données dans le projet.

1.	Connectez-vous au serveur de la base de données SQL Microsoft Azure qui contient la base de données migrée, puis recherchez la base de données. 

2.	Cliquez avec le bouton droit de la souris sur la base de données et sélectionnez **Comparaison de schémas...** Une nouvelle fenêtre de comparaison de schémas s’affiche, la base de données Microsoft Azure étant sélectionnée en tant que source sur le côté gauche. Dans la liste déroulante Sélectionner cible affichée sur le côté droit, sélectionnez la base de données cible ou le fichier d’instantané avec lesquels effectuer la comparaison.

3.	Une fois les systèmes source et cible sélectionnés, cliquez sur Comparer pour démarrer la comparaison. Le chargement d’un schéma depuis une base de données complexe dans la base de données SQL Microsoft Azure peut prendre un certain temps. Or, le chargement du schéma et l’exécution d’autres tâches sur les métadonnées au sein d’une base de données SQL Microsoft Azure peuvent s’avérer plus rapides et présenter un niveau de tarification plus élevé.

4.	Une fois que la comparaison terminée, examinez les différences. En général, nous vous recommandons d’éviter d’apporter des modifications à l’un ou l’autre des schémas, sauf en cas de doute sérieux.

Dans la comparaison de schémas ci-dessous, la base de données « Adventure Works 2014 », dans la base de données SQL Microsoft Azure version 12, sur la gauche, a été transformée et migrée par l’Assistant Migration SQL Azure ; elle est comparée à la base de données source dans SQL Server, sur la droite.

![alt text](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)

<!---HONumber=58--> 