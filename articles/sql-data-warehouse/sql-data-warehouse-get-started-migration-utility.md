<properties
   pageTitle="Migrer : utilitaire de migration de l’entrepôt de données | Microsoft Azure"
   description="Migrer vers SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/04/2016"
   ms.author="lodipalm"/>


#Utilitaire de migration de l’entrepôt de données (version préliminaire)
L’utilitaire de migration de l’entrepôt de données est un outil conçu pour migrer le schéma et les données du SQL Server et de la base de données SQL Azure vers Azure SQL Data Warehouse. Lors de la migration du schéma, l’outil mappe automatiquement le schéma correspondant à partir de la source vers la destination. Une fois que le schéma a été migré, les utilisateurs ont également la possibilité de déplacer des données au moyen de scripts générés automatiquement.

Outre la migration du schéma et des données, cet outil permet aux utilisateurs de générer des rapports de compatibilité qui résument les incompatibilités entre les instances cible et source, qui empêcheraient la migration simplifiée.

##Prise en main
L’utilitaire de migration de l’entrepôt de données peut être téléchargé [ici][]. Comme condition préalable à l’installation, vous aurez besoin de l’utilitaire de ligne de commande BCP, pour exécuter des scripts de migration, et d’Office, pour afficher le rapport de compatibilité. Après le lancement de l’exécutable téléchargé, vous êtes invité à accepter un CLUF standard avant l’installation de l’outil.

###Lancement de l’outil et connexion
L’outil peut être lancé facilement en cliquant sur l’icône du Bureau qui apparaît après l’installation. Lors de l’ouverture de l’outil, une page de connexion initiale vous permet de choisir votre source et votre destination pour l’outil de migration. À ce stade, nous prenons en charge SQL Server et la base de données SQL Azure en tant que sources et SQL Data Warehouse en tant que destination. Après la sélection de ces options, vous devez vous connecter à votre serveur source en renseignant le nom du serveur et en vous authentifiant, puis en cliquant sur Se connecter.
 
Une fois l’authentification effectuée, l’outil affiche une liste des bases de données présentes sur le serveur auquel vous vous êtes connecté. Vous pouvez commencer la migration en sélectionnant une base de données que vous souhaitez migrer, puis en cliquant sur Migrer les éléments sélectionnés.
 
##Rapport de migration
La sélection de l’option Vérifier la compatibilité des bases de données dans l’outil génère un rapport qui résume toutes les incompatibilités dans les objets de la base de données que vous cherchez à migrer. Vous trouverez une liste élargie de certaines des fonctionnalités de SQL Server qui ne sont pas présentes dans SQL Data Warehouse dans notre [documentation relative à la migration][]. Une fois que le rapport est généré, vous pouvez l’enregistrer et l’ouvrir dans Excel.

Notez que lors de la génération du schéma de migration, la plupart des problèmes identifiés comme Object sont ajustés afin de permettre la migration immédiate de ces données. Veuillez consulter les modifications afin de vous assurer que vous ne souhaitez pas apporter d’ajustements supplémentaires avant d’appliquer le schéma.

##Migration du schéma

Une fois la connexion effectuée, la sélection de l’option Migrer le schéma génère un script de migration de schéma pour les tables sélectionnées. Ce script porte la structure de la table, mappe les types de données incompatibles vers des formulaires plus compatibles et crée des informations d’identification de sécurité et un schéma si cela a été indiqué par l’utilisateur dans les paramètres de migration. Ce code peut être exécuté sur l’instance SQL Data Warehouse ciblée, enregistré dans un fichier, copié dans votre Presse-papiers, voire modifié en ligne avant d’entreprendre une action supplémentaire.
 
Comme mentionné ci-dessus, le schéma de migration passe en revue les modifications de migration effectuées par l’outil afin de vérifier que vous les comprenez bien.

##Migration des données

En cliquant sur l’option Migrer les données, vous pouvez générer des scripts BCP qui déplaceront vos données vers les fichiers plats de votre serveur dans un premier temps, puis directement dans votre SQL Data Warehouse. Nous recommandons ce processus pour déplacer de petites quantités de données et, étant donné que nous n’avons pas intégré de nouvelles tentatives, sachez que des erreurs peuvent se produire en cas de pannes réseau. Pour exécuter ce processus, l’utilitaire de ligne de commande BCP doit être installé et le schéma pour les données doit avoir déjà été créé.
 
Après avoir rempli les paramètres ci-dessus, il vous suffit de cliquer sur Exécuter la migration ; un ensemble de deux packages est généré à l’emplacement spécifié. Exécutez le fichier d’exportation pour exporter les données depuis votre source de migration vers des fichiers plats, et exécutez le fichier d’importation pour importer vos données dans SQL Data Warehouse.

## Étapes suivantes
Maintenant que vous avez migré certaines données, découvrez comment [développer][].

<!--Image references-->

<!--Article references-->
[documentation relative à la migration]: https://azure.microsoft.com/fr-FR/documentation/articles/sql-data-warehouse-overview-migrate/
[développer]: https://azure.microsoft.com/fr-FR/documentation/articles/sql-data-warehouse-overview-develop/
[ici]: https://migrhoststorage.blob.core.windows.net/sqldwsample/DataWarehouseMigrationUtility.zip

<!---HONumber=AcomDC_0107_2016-->