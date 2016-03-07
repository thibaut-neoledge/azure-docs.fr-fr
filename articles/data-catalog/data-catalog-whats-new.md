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
   ms.date="02/19/2016"
   ms.author="maroche"/>

# Nouveautés d'Azure Data Catalog

Les mises à jour d’**Azure Data Catalog** sont publiées régulièrement. Comme certaines versions portent sur les fonctionnalités du service principal, chaque version ne contient pas nécessairement de nouvelles fonctionnalités orientées utilisateur. Cette page présente de nouvelles fonctionnalités orientées utilisateur ajoutées au service **Azure Data Catalog**.

## Nouveautés de la version de la semaine du 19 février 2016

Depuis la semaine du 19 février 2016, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Une nouvelle expérience de sélection de sources de données dans l’outil d’inscription de sources de données **Azure Data Catalog**. L'outil d'inscription de sources de données a été mis à jour pour faciliter la localisation et la sélection à partir de sources de données prises en charge par **Azure Data Catalog**.
- Prise en charge de dix langues supplémentaires dans le portail **Azure Data Catalog** et l'outil d'inscription de sources de données. Outre l'anglais, l’expérience **Azure Data Catalog** est désormais disponible en allemand, espagnol, français, italien, japonais, coréen, portugais (Brésil), russe, chinois simplifié et chinois traditionnel. L’expérience utilisateur **Azure Data Catalog** sera localisée selon les préférences de langue définies dans Windows ou dans le navigateur web de l'utilisateur.
- Prise en charge de la géo-réplication des données **Azure Data Catalog** pour garantir la continuité de l’activité et la récupération d’urgence. Tous les contenus **Azure Data Catalog**, y compris les métadonnées de source de données et les annotations de crowdsourcing, sont désormais répliqués entre deux régions Azure sans frais supplémentaires pour les clients. Les régions Azure sont préalablement associées et distantes d’au moins 500 miles, et suivent le mappage décrit dans la rubrique [Continuité des activités et récupération d’urgence (BCDR) : régions jumelées d’Azure](../best-practices-availability-paired-regions.md). 


## Nouveautés de la version de la semaine du 5 février 2016

Depuis la semaine du 5 février 2016, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge du changement de l'abonnement Azure utilisé par **Azure Data Catalog**. Les administrateurs **Azure Data Catalog** peuvent utiliser la page Paramètres du portail **Azure Data Catalog** et sélectionner un autre abonnement Azure à des fins de facturation.

## Nouveautés de la version de la semaine du 29 janvier 2016

Depuis la semaine du 29 janvier 2016, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de l’inscription manuelle de sources de données supplémentaires. Les utilisateurs peuvent désormais utiliser Créer une entrée manuelle dans le portail **Azure Data Catalog** ou l’API REST **Azure Data Catalog** pour inscrire les sources de données suivantes :
   + OData : fonction, jeu d’entités et conteneur d’entités
   + HTTP : fichier, point de terminaison, rapport et site
   + Système de fichiers : fichier
   + SharePoint : liste
   + FTP : fichier et répertoire
   + Salesforce.com : objet
   + DB2 : table, vue et base de données
   + PostgreSQL : table, vue et base de données
- Prise en charge de la fonction « Ouvrir dans SQL Server Data Tools » pour les sources de données SQL Server (y compris Base de données SQL Azure et Azure SQL Data Warehouse).  

> [AZURE.NOTE] L’installation de la fonction « Ouvrir dans SQL Server Data Tools » requiert Visual Studio 2013 avec Update 4 et les outils SQL Server. Pour installer la dernière version de SQL Server Data Tools, visitez [Télécharger SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx).

## Nouveautés de la version de la semaine du 22 janvier 2016

Depuis la semaine du 22 janvier 2016, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de l’inscription et de la découverte des vues et packages SAP HANA. Les utilisateurs peuvent inscrire des sources de données SAP HANA à l’aide de l’outil d’inscription des sources de données **Azure Data Catalog** ainsi qu’annoter et découvrir des sources de données SAP HANA inscrites à l’aide du portail **Azure Data Catalog**.

## Nouveautés de la version de la semaine du 8 janvier 2016

Depuis la semaine du 8 janvier 2016, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Possibilité d’épingler et de désépingler des ressources de données dans le portail **Azure Data Catalog**. Les utilisateurs peuvent choisir d’épingler des ressources de données pour faciliter leur redécouverte et leur réutilisation.
- Une page d’accueil revue récemment dans le portail **Azure Data Catalog**. Cette nouvelle page d’accueil fournit des informations sur l’activité actuelle des utilisateurs, notamment les ressources publiées, les ressources épinglées et les recherches enregistrées dernièrement, mais aussi des informations sur l’activité à l’échelle de Catalog.
- Prise en charge des paramètres utilisateur persistants dans le portail **Azure Data Catalog**. Les paramètres d’expérience utilisateur, notamment la vue grille ou mosaïque, le nombre de résultats par page, l’activation ou la désactivation de la mise en surbrillance des correspondances, sont conservés entre les sessions utilisateur.
- **Azure Data Catalog** est maintenant disponible dans deux nouvelles régions Azure. Les clients peuvent approvisionner **Azure Data Catalog** dans les régions Europe du Nord et Asie du Sud-Est, outre l’Est des États-Unis, l’Ouest des États-Unis, l’Europe de l’Ouest et l’Est de l’Australie. Pour plus d’informations, consultez l’article [Régions Azure](https://azure.microsoft.com/regions/).

## Nouveautés de la version de la semaine du 18 décembre 2015

Depuis la semaine du 18 décembre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de profils de données pour les sources de données Azure SQL Data Warehouse. Lors de l'enregistrement des tables et vues Azure SQL Data Warehouse, les utilisateurs peuvent choisir d'inclure les mesures de profil de données avec les métadonnées extraites de la source de données.
- Prise en charge de l’inscription et de la découverte des bases de données et des objets MySQL. Les utilisateurs peuvent inscrire des sources de données MySQL à l'aide de l’outil d’inscription de sources de données **Azure Data Catalog** ainsi qu’annoter et découvrir des sources de données MySQL inscrites à l'aide du portail **Azure Data Catalog**.

## Nouveautés de la version de la semaine du 4 décembre 2015

Depuis la semaine du 4 décembre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de l’authentification SPNEGO et Windows pour les sources de données Teradata. Lors de l’inscription de tables et vues Teradata, les utilisateurs peuvent choisir de se connecter à Teradata à l’aide de SPNEGO et de Windows, ainsi que de l’authentification LDAP et TD2.
- Prise en charge des sources de données Azure Data Lake Store. Les utilisateurs peuvent désormais inscrire et découvrir des sources de données Azure Data Lake Store à l’aide d'Azure Data Catalog.
- Prise en charge de la spécification manuelle des paramètres de proxy réseau dans l'outil d'inscription de sources de données Azure Data Catalog. Les utilisateurs peuvent sélectionner « Modifier les paramètres de proxy » à partir de la page d'accueil de l'outil et spécifier l'adresse du proxy et le port qui doivent être utilisés par l'outil.


## Nouveautés de la version de la semaine du 20 novembre 2015

Depuis la semaine du 20 novembre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Possibilité d'afficher et de copier des chaînes de connexion depuis le portail **Azure Data Catalog** pour des sources de données SQL Server (notamment base de données SQL Azure) et Oracle. Les utilisateurs peuvent cliquer sur le lien « Afficher les chaînes de connexion » dans les informations de connexion d'une table, vue ou base de données SQL Server ou Oracle pour voir les chaînes de connexion utilisées pour se connecter à la source de données. Les chaînes de connexion ADO.NET, ODBC, OLEDB et JDBC sont fournies pour les sources de données SQL Server. Les chaînes de connexion ODBC et OLEDB sont fournies pour les sources de données Oracle.
- Prise en charge de l’inclusion des profils de données lors de l’inscription des tables et vues Teradata.
- Prise en charge de la fonction « Ouvrir dans Power BI Desktop » pour les sources SQL Server (notamment Azure SQL Database et Azure SQL Data Warehouse), SQL Server Analysis Services, Azure Storage et HDFS.  

> [AZURE.NOTE] « Ouvrir dans Power BI Desktop » requiert l’installation d’une version actuelle de l’application Power BI Desktop. Si vous rencontrez des problèmes ou des erreurs en utilisant cette fonctionnalité, vérifiez que vous disposez de la dernière version de Power BI Desktop à la page [PowerBI.com](https://powerbi.com).

## Nouveautés de la version de la semaine du 13 novembre 2015

Depuis la semaine du 13 novembre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de l’authentification LDAP pour les sources de données Teradata. Lors de l’inscription de tables et vues Teradata, les utilisateurs peuvent choisir de se connecter à Teradata à l’aide de LDAP, ainsi que de l’authentification TD2.
- Prise en charge de « Ouvrir dans Excel » pour les sources de données Teradata.
- Prise en charge des termes de recherche récents dans le portail **Azure Data Catalog**. Lors de la recherche dans le portail, les utilisateurs peuvent sélectionner les termes de recherche récemment utilisés pour accélérer l’expérience de découverte.

## Nouveautés de la version de la semaine du 6 novembre 2015

Depuis la semaine du 6 novembre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de l’aperçu des sources de données Teradata. Lors de l'enregistrement des tables et vues Teradata, les utilisateurs peuvent choisir d'inclure les enregistrements d’instantané avec les métadonnées extraites de la source de données.
- Prise en charge de la commande « Ouvrir avec Excel » pour les sources de données Azure SQL Data Warehouse.
- Prise en charge de la définition et la modification des schémas de niveau colonne pour les ressources de données inscrites. Après avoir créé manuellement une ressource de données à l’aide du portail **Azure Data Catalog**, les utilisateurs peuvent ajouter des définitions de colonne dans les propriétés des ressources de données.
- Prise en charge des requêtes « has » lors de la recherche dans **Azure Data Catalog** afin d’activer la découverte des ressources de données inscrites possédant des métadonnées spécifiques. La syntaxe des requêtes **Azure Data Catalog** comprend à présent ce qui suit :

| Syntaxe de requête | Objectif |
|-------------------------|---------|
| has:previews | Recherche les ressources de données qui comprennent une version préliminaire |
| has:documentation | Recherche les ressources de données pour lesquelles une documentation a été fournie |
| has:tableDataProfiles | Recherche des ressources de données avec des informations de profil des données de niveau table |
| has:columnsDataProfiles | Recherche des ressources de données avec des informations de profil des données de niveau colonne |


## Nouveautés de la version de la semaine du 30 octobre 2015

Depuis la semaine du 30 octobre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de chiffrement au repos d'aperçus et de profils de données pour des sources de données enregistrées. **Azure Data Catalog** chiffre de façon transparente les sources de données d’enregistrements d’aperçu et de profils de données inscrites auprès du service, sans aucun besoin de gestion de clés par des administrateurs du catalogue.

## Nouveautés de la version de la semaine du 23 octobre 2015

Depuis la semaine du 23 octobre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge des sources de données Teradata. Les utilisateurs peuvent désormais inscrire et découvrir des tables et vues Teradata.

> [AZURE.NOTE] Pour la version actuelle, seule l’authentification Teradata TD2 est pris en charge. D’autres mécanismes d’authentification seront pris en charge dans les futures versions.

## Nouveautés de la version de la semaine du 16 octobre 2015

Depuis la semaine du 16 octobre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de sources de données Hive locales. Les utilisateurs peuvent désormais inscrire et détecter des tables Hive pour Apache Hive dans Hadoop sur des sources de données locales.
- Prise en charge des recherches enregistrées dans le portail **Azure Data Catalog**. Les utilisateurs peuvent enregistrer des termes de recherche et filtrer les sélections pour répéter facilement des recherches précédentes et définir des vues utiles du contenu du catalogue. L’utilisateur peut également définir une recherche enregistrée comme recherche par défaut. Quand un utilisateur clique sur l’icône de recherche en forme de loupe dans la page d’accueil du portail **Azure Data Catalog** ou dans la page de démarrage, il accède directement à la recherche enregistrée définie comme recherche par défaut.


## Nouveautés de la version de la semaine du 9 octobre 2015

Depuis la semaine du 9 octobre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de la documentation complète pour les données et les conteneurs inscrits dans Azure Data Catalog. Les utilisateurs peuvent désormais fournir une documentation pour les données telles que les tableaux, les vues et les rapports, ainsi que pour les conteneurs tels que les bases de données et les modèles, pour les scénarios où les balises et les descriptions ne sont pas suffisantes.

## Nouveautés de la version de la semaine du 2 octobre 2015

Depuis la semaine du 2 octobre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de l'enregistrement manuel de types de sources de données connus. Les utilisateurs peuvent saisir manuellement les informations d’une source de données à l’aide du portail **Azure Data Catalog** pour tous les types de sources de données pris en charge par **Azure Data Catalog**.
- Prise en charge de l’autorisation des groupes de sécurité Azure Active Directory. Les administrateurs du catalogue peuvent autoriser l’accès au catalogue à des groupes de sécurité et à des comptes d'utilisateurs, facilitant la gestion de l'accès à **Azure Data Catalog**.
- Prise en charge de l’ouverture de sources de données Hive dans Excel à partir du portail **Azure Data Catalog**.

> [AZURE.NOTE] Pour pouvoir utiliser la fonctionnalité « Ouvrir dans Excel » avec des sources de données Hive, les utilisateurs doivent avoir installé le pilote ODBC pour Hive.

## Nouveautés de la version de la semaine du 25 Septembre 2015

Depuis la semaine du 25 septembre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de l’inclusion des profils de données lors de l’inscription des sources de données Hive.
- Prise en charge de la découverte par programmation de l’API Catalogue, facilitant ainsi l’intégration des applications à **Azure Data Catalog**.

## Nouveautés de la version de la semaine du 18 Septembre 2015

À compter de la semaine du 18 septembre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Une nouvelle expérience d’initiation à la découverte de source de données dans le portail **Azure Data Catalog**. Quand les utilisateurs accèdent à la page de découverte du portail **Azure Data Catalog** sans entrer un terme de recherche, une vue d’ensemble du contenu du catalogue s’affiche, notamment les balises fréquemment utilisées, ainsi que les experts, les types de sources de données et les types d’objets.
- Prise en charge de l’inscription et de la découverte des bases de données et des objets Azure SQL Data Warehouse. Pour plus d’informations sur Azure SQL Data Warehouse, consultez [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Prise en charge de l’inscription et de la découverte des modèles SQL Server Analysis Services et des serveurs SQL Server Reporting Services en tant que conteneurs. Au moment de l’inscription d’objets SSAS et SSRS, **Azure Data Catalog** crée une entrée pour le modèle SSAS et le serveur SSRS, ainsi que pour les rapports et d’autres objets. Les conteneurs peuvent être découverts et annotés à l’aide du portail **Azure Data Catalog**. Les utilisateurs peuvent également rechercher et filtrer le contenu d’un modèle ou serveur en plus de la recherche et du filtrage du contenu du catalogue.

> [AZURE.NOTE] Les objets SSAS et SSRS qui ont été inscrits avant la version du 18 septembre doivent être inscrits de nouveau à l’aide de l’outil d’inscription de sources de données avant que l’entrée de modèle ou de serveur soit ajoutée au catalogue. La réinscription d’une source de données n’affecte pas les annotations qui ont été ajoutées par des utilisateurs dans le portail **Azure Data Catalog**.

## Nouveautés de la version de la semaine du 11 Septembre 2015

Depuis la semaine du 11 septembre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de l’inscription et de la détection d’objets SQL Server Analysis Services via HTTP/HTTPS. Les utilisateurs peuvent désormais se connecter à des serveurs SSAS à l’aide d’une URL (telle que https://servername/olap/msmdpump.dll) au lieu d’un nom de serveur et peuvent utiliser une authentification de base et des connexions anonymes en plus de l’authentification Windows. Pour plus d’informations sur les connexions HTTP/HTTPS à SSAS, consultez [Configuration de l’accès HTTP à Analysis Services](https://msdn.microsoft.com/library/gg492140.aspx).
- Prise en charge de sources de données Hive sur HDInsight. Les utilisateurs peuvent désormais inscrire et détecter des tables Hive pour Apache Hive dans Hadoop sur des sources de données HDInsight. Pour plus d'informations sur Hive dans HDInsight, consultez le [Centre de documentation HDInsight](../hdinsight-use-hive/).
- Prise en charge de l’inscription et de la détection de bases de données Oracle et de clusters HDFS en tant que conteneurs. Lors de l’inscription de tables et de vues Oracle ou de HDFS, **Azure Data Catalog** crée une entrée pour la base de données, ainsi que pour les tables et les vues. La base de données peut être découverte et annotée à l’aide du portail **Azure Data Catalog**. Les utilisateurs peuvent également rechercher et filtrer le contenu d’une base de données ou d’un cluster, en plus de la recherche et du filtrage du contenu du catalogue.


> [AZURE.NOTE] Les tables et vues Oracle, ainsi que les fichiers et répertoires HDFS, qui ont été inscrits avant la version du 11 septembre, doivent être inscrits de nouveau à l’aide de l’outil d’inscription de sources de données avant que l’entrée de base de données ou de cluster soit ajoutée au catalogue. La réinscription d’une source de données n’affecte pas les annotations qui ont été ajoutées par des utilisateurs dans le portail **Azure Data Catalog**.

## Nouveautés de la version de la semaine du 4 Septembre 2015

Depuis la semaine du 4 septembre 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de l'enregistrement manuel de types de sources de données inconnus. Les utilisateurs peuvent entrer manuellement des informations sur les sources de données à l’aide du portail **Azure Data Catalog**, afin que les sources de données non prises en charge explicitement par l’outil d’inscription de sources de données puissent être annotées et découvertes.
- Prise en charge de l'enregistrement et de la découverte des bases de données SQL Server en tant que conteneurs. Pendant l'enregistrement des tables et des vues SQL Server, **Azure Data Catalog** crée une entrée pour la base de données, ainsi que pour les tables et les vues. La base de données peut être découverte et annotée à l’aide du portail **Azure Data Catalog**. Les utilisateurs peuvent également rechercher et filtrer le contenu d'une base de données en plus de la recherche et du filtrage du contenu du catalogue.

> [AZURE.NOTE] Les tables et vues SQL Server qui ont été inscrits avant la version du 4 septembre doivent être inscrits de nouveau à l’aide de l’outil d’inscription de sources de données avant que l’entrée de base de données soit ajoutée au catalogue. La réinscription d’une source de données n’affecte pas les annotations qui ont été ajoutées par des utilisateurs dans le portail **Azure Data Catalog**.

## Nouveautés de la version de la semaine du 28 août 2015

Depuis la semaine du 28 août 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge du profilage des données pour les sources de données SQL Server et Oracle. Lors de l'enregistrement des vues et des tables SQL Server et Oracle, les utilisateurs peuvent choisir d'inclure des informations de profil des données pour les objets en cours d'enregistrement. Le profil des données inclut des statistiques au niveau de l'objet et au niveau des colonnes.
- Prise en charge des sources de données HDFS Hadoop. Les utilisateurs peuvent désormais s'inscrire et découvrir les fichiers et répertoires HFDFS.

## Nouveautés de la version de la semaine du 21 août 2015

À compter de la semaine du 21 août 2015, les fonctionnalités suivantes ont été ajoutées à **Azure Data Catalog** :

- Prise en charge de la fourniture d’informations de demande d’accès pour les sources de données inscrites. Pour toute ressource de données inscrite, les utilisateurs peuvent maintenant fournir des instructions de demande d’accès, y compris des liens de messagerie ou des URL, en vue d’une intégration aisée aux outils et processus existants.
- Info-bulles pour balises et experts, pour savoir facilement quels utilisateurs ont fourni quelles métadonnées pour les ressources de données inscrites.
- Nous avons ajouté un nouveau bouton « Utilisateur » et un menu à notre barre de navigation supérieure. Ce menu indique à l’utilisateur le compte avec lequel il se connecte à **Azure Data Catalog** (et éventuellement s’en déconnecte). Ce menu affiche également le nom du catalogue, qui est une information utile pour les développeurs qui utilisent l’API REST **Azure Data Catalog**.
- Édition Standard uniquement : quand vous ajoutez des propriétaires à des ressources de données, **Azure Data Catalog** prend désormais en charge les comptes d’utilisateurs et les groupes de sécurité en tant que propriétaires. Pour ajouter un groupe de sécurité en tant que propriétaire pour des ressources de données sélectionnées, vous pouvez entrer le nom d’affichage du groupe ou l’adresse de messagerie UPN du groupe, le cas échéant.
- Prise en charge des sources de données de stockage d’objets blob Azure. Les utilisateurs peuvent désormais s’inscrire et découvrir des objets blob Azure Storage et des répertoires.

<!---HONumber=AcomDC_0224_2016-->