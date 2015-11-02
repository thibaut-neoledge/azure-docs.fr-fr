<properties
   pageTitle="Nouveautés d’Azure Data Catalog"
   description="Vue d’ensemble des nouvelles fonctionnalités de la version préliminaire d’Azure Data Catalog."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/16/2015"
   ms.author="maroche"/>

# Nouveautés d'Azure Data Catalog

Les mises à jour d’**Azure Data Catalog** sont publiées régulièrement. Comme certaines versions portent sur les fonctionnalités du service principal, chaque version ne contient pas nécessairement de nouvelles fonctionnalités orientées utilisateur. Cette page présente de nouvelles fonctionnalités orientées utilisateur ajoutées au service **Azure Data Catalog**.

## Nouveautés de la version de la semaine du 16 octobre 2015

À compter de la semaine du 16 octobre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de sources de données Hive locales. Les utilisateurs peuvent désormais inscrire et détecter des tables Hive pour Apache Hive dans Hadoop sur des sources de données locales.
- Prise en charge des recherches enregistrées dans le portail **Azure Data Catalog**. Les utilisateurs peuvent enregistrer des termes de recherche et filtrer les sélections pour répéter facilement des recherches précédentes et définir des vues utiles du contenu du catalogue. L’utilisateur peut également définir une recherche enregistrée comme recherche par défaut. Lorsqu'un utilisateur clique sur l'icône de recherche en forme de loupe sur la page d’accueil du portail **Azure Data Catalog** ou sur la page de démarrage, il accède directement à la recherche enregistrée définie comme recherche par défaut.


## Nouveautés de la version de la semaine du 9 octobre 2015

À compter de la semaine du 9 octobre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de la documentation complète pour les données et les conteneurs inscrits dans Azure Data Catalog. Les utilisateurs peuvent désormais fournir une documentation pour les données telles que les tableaux, les vues et les rapports, ainsi que pour les conteneurs tels que les bases de données et les modèles, pour les scénarios où les balises et les descriptions ne sont pas suffisantes.

## Nouveautés de la version de la semaine du 2 octobre 2015

À compter de la semaine du 2 octobre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de l'enregistrement manuel de types de sources de données connus. Les utilisateurs peuvent saisir manuellement les informations d’une source de données à l'aide du portail **Azure Data Catalog** pour tous les types de source de données pris en charge par **Azure Data Catalog**.
- Prise en charge de l’autorisation des groupes de sécurité Azure Active Directory. Les administrateurs du catalogue peuvent autoriser l’accès au catalogue à des groupes de sécurité et à des comptes d'utilisateurs, facilitant la gestion de l'accès à **Azure Data Catalog**.
- Prise en charge de l'ouverture de sources de données Hive dans Excel à partir du portail **Azure Data Catalog**.

> [AZURE.NOTE]Pour pouvoir utiliser la fonctionnalité « Ouvrir dans Excel » avec des sources de données Hive, les utilisateurs doivent avoir installé le pilote ODBC pour Hive.

## Nouveautés de la version de la semaine du 25 Septembre 2015

À compter de la semaine du 25 septembre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de l’inclusion des profils de données lors de l’inscription des sources de données Hive.
- Prise en charge de la découverte par programmation de l’API du catalogue, facilitant ainsi l’intégration des applications à **Azure Data Catalog**.

## Nouveautés de la version de la semaine du 18 Septembre 2015

À compter de la semaine du 18 septembre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Une nouvelle expérience d’initiation à la découverte de source de données dans le portail **Azure Data Catalog**. Quand les utilisateurs accèdent à la page de découverte du portail **Azure Data Catalog** sans entrer un terme de recherche, une vue d’ensemble du contenu du catalogue s’affiche, notamment les balises fréquemment utilisées, ainsi que les experts, les types de sources de données et les types d’objets.
- Prise en charge de l’inscription et de la découverte des bases de données et des objets Azure SQL Data Warehouse. Pour plus d’informations sur Azure SQL Data Warehouse, consultez [SQL Data Warehouse](http://azure.microsoft.com/services/sql-data-warehouse/).
- Prise en charge de l’inscription et de la découverte des modèles SQL Server Analysis Services et des serveurs SQL Server Reporting Services en tant que conteneurs. Au moment de l’inscription d’objets SSAS et SSRS, **Azure Data Catalog** crée une entrée pour le modèle SSAS et le serveur SSRS, ainsi que pour les rapports et d’autres objets. Les conteneurs peuvent être détectés et annotés à l’aide du portail **Azure Data Catalog**. Les utilisateurs peuvent également rechercher et filtrer le contenu d’un modèle ou serveur en plus de la recherche et du filtrage du contenu du catalogue.

> [AZURE.NOTE]Les objets SSAS et SSRS qui ont été inscrits avant la version du 18 septembre doivent être inscrits de nouveau à l’aide de l’outil d’inscription de sources de données avant que l’entrée de modèle ou de serveur soit ajoutée au catalogue. La réinscription d’une source de données n’affecte pas les annotations qui ont été ajoutées par des utilisateurs dans le portail **Azure Data Catalog**.

## Nouveautés de la version de la semaine du 11 Septembre 2015

À compter de la semaine du 11 septembre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de l’inscription et de la détection d’objets SQL Server Analysis Services via HTTP/HTTPS. Les utilisateurs peuvent désormais se connecter à des serveurs (SSAS) à l’aide d’une URL (telle que https://servername/olap/msmdpump.dll) au lieu d’un nom de serveur, et peuvent utiliser une authentification de base et des connexions anonymes en plus de l’authentification Windows. Pour plus d’informations sur les connexions HTTP/HTTPS à SSAS, consultez [Configuration de l’accès HTTP à Analysis Services](https://msdn.microsoft.com/library/gg492140.aspx).
- Prise en charge de sources de données Hive sur HDInsight. Les utilisateurs peuvent désormais inscrire et détecter des tables Hive pour Apache Hive dans Hadoop sur des sources de données HDInsight. Pour plus d’informations sur Hive sur HDInsight, consultez le [Centre de documentation HDInsight](../hdinsight-use-hive/).
- Prise en charge de l’inscription et de la détection de bases de données Oracle et de clusters HDFS en tant que conteneurs. Pendant l’inscription de tables et de vues Oracle, ou de HDFS, **Azure Data Catalog** crée une entrée pour la base de données, ainsi que pour les tables et les vues. La base de données peut être détectée et annotée à l’aide du portail **Azure Data Catalog**. Les utilisateurs peuvent également rechercher et filtrer le contenu d’une base de données ou d’un cluster, en plus de la recherche et du filtrage du contenu du catalogue.


> [AZURE.NOTE]Les tables et vues Oracle, ainsi que les fichiers et répertoires HDFS, qui ont été inscrits avant la version du 11 septembre, doivent être inscrits de nouveau à l’aide de l’outil d’inscription de sources de données avant que l’entrée de base de données ou de cluster soit ajoutée au catalogue. La réinscription d’une source de données n’affecte pas les annotations qui ont été ajoutées par des utilisateurs dans le portail **Azure Data Catalog**.

## Nouveautés de la version de la semaine du 4 Septembre 2015

À compter de la semaine du 4 septembre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de l'enregistrement manuel de types de sources de données inconnus. Les utilisateurs peuvent entrer manuellement des informations sur les sources de données à l’aide du portail **Azure Data Catalog**, afin que les sources de données non prises en charge explicitement par l’outil d’inscription de sources de données puissent être annotées et découvertes.
- Prise en charge de l'enregistrement et de la découverte des bases de données SQL Server en tant que conteneurs. Pendant l’enregistrement des tables et des vues SQL Server, **Azure Data Catalog** crée une entrée pour la base de données, ainsi que pour les tables et vues. La base de données peut être détectée et annotée à l’aide du portail **Azure Data Catalog**. Les utilisateurs peuvent également rechercher et filtrer le contenu d'une base de données en plus de la recherche et du filtrage du contenu du catalogue.

> [AZURE.NOTE]Les tables et vues SQL Server qui ont été inscrits avant la version du 4 septembre doivent être inscrits de nouveau à l’aide de l’outil d’inscription de sources de données avant que l’entrée de base de données soit ajoutée au catalogue. La réinscription d’une source de données n’affecte pas les annotations qui ont été ajoutées par des utilisateurs dans le portail **Azure Data Catalog**.

## Nouveautés de la version de la semaine du 28 août 2015

À compter de la semaine du 28 août 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge du profilage des données pour les sources de données SQL Server et Oracle. Lors de l'enregistrement des vues et des tables SQL Server et Oracle, les utilisateurs peuvent choisir d'inclure des informations de profil des données pour les objets en cours d'enregistrement. Le profil des données inclut des statistiques au niveau de l'objet et au niveau des colonnes.
- Prise en charge des sources de données HDFS Hadoop. Les utilisateurs peuvent désormais s'inscrire et découvrir les fichiers et répertoires HFDFS.

## Nouveautés de la version de la semaine du 21 août 2015

À compter de la semaine du 21 août 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de la fourniture d’informations de demande d’accès pour les sources de données inscrites. Pour toute ressource de données inscrite, les utilisateurs peuvent maintenant fournir des instructions de demande d’accès, y compris des liens de messagerie ou des URL, en vue d’une intégration aisée aux outils et processus existants.
- Info-bulles pour balises et experts, pour savoir facilement quels utilisateurs ont fourni quelles métadonnées pour les ressources de données inscrites.
- Nous avons ajouté un nouveau bouton « Utilisateur » et un menu à notre barre de navigation supérieure. Ce menu indique à l’utilisateur le compte avec lequel il se connecte à **Azure Data Catalog** (et éventuellement s’en déconnecte). Ce menu affiche également le nom du catalogue, qui est une information utile pour les développeurs qui utilisent l’API REST **Azure Data Catalog**.
- Édition Standard uniquement : quand vous ajoutez des propriétaires à des ressources de données, **Azure Data Catalog** prend désormais en charge les comptes d’utilisateurs et les groupes de sécurité en tant que propriétaires. Pour ajouter un groupe de sécurité en tant que propriétaire pour des ressources de données sélectionnées, vous pouvez entrer le nom d’affichage du groupe ou l’adresse de messagerie UPN du groupe, le cas échéant.
- Prise en charge des sources de données de stockage d’objets blob Azure. Les utilisateurs peuvent désormais s’inscrire et découvrir des objets blob Azure Storage et des répertoires.

<!---HONumber=Oct15_HO4-->