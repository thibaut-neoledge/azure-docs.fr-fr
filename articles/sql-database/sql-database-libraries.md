<properties
	pageTitle="Bibliothèques de connexions pour SQL Database et SQL Server"
	description="Répertorie le numéro de version minimal pour chaque pilote que les programmes clients peuvent utiliser lors de la connexion à Azure SQL Database ou à Microsoft SQL Server. Un lien est fourni pour les informations sur les versions des pilotes publiés par la communauté, et non par Microsoft."
	services="sql-database"
	documentationCenter=""
	authors="pehteh"
	manager="jeffreyg"
	editor="genemi"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="pehteh"/>

# Bibliothèques de connexions pour SQL Database et SQL Server

Cette rubrique répertorie le numéro de version minimal pour chaque bibliothèque/pilote que les programmes clients peuvent utiliser lors de la connexion à Azure SQL Database ou à Microsoft SQL Server.

## Tableau des bibliothèques de pilotes publiées par Microsoft

Le tableau suivant affiche les bibliothèques publiées par Microsoft. La colonne **Bibliothèques** fournit des liens vous permettant de télécharger chaque bibliothèque. La colonne **Version** répertorie la version minimale recommandée pour interagir avec Azure SQL Database et Microsoft SQL Server.

| Plateforme | Syst. d’exploitation | Bibliothèques<br/>à télécharger | Version<br/>du pilote | Description<br/>du pilote | Autres<br/>informations |
| :--- | :--- | :--- | :--- | :--- | :-- |
| .NET | Multiplateforme (.NET) | [ADO.NET, System .Data .SqlClient](http://www.microsoft.com/download/details.aspx?id=30653) | 4\.5 + | Fournisseur SQL Server pour .NET Framework | . |
| PHP | Windows | [PHP pour SQL Server](http://www.microsoft.com/download/details.aspx?id=20098) | 2\.0 + | Pilote PHP pour SQL Server | [Lien](http://msdn.microsoft.com/library/dn865013.aspx) |
| Java | Windows | [JDBC pour SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) | 2\.0 + | Pilote JDBC de type 4 qui fournit la connectivité de base de données via l’API JDBC standard | [Lien](https://msdn.microsoft.com/library/mt654048.aspx) |
| ODBC | Windows | [ODBC pour SQL Server](http://www.microsoft.com/download/details.aspx?id=36434) | 11\.0 + | Pilote ODBC Microsoft pour SQL Server | [Lien](http://msdn.microsoft.com/library/jj730308.aspx) |
| ODBC | Suse Linux | [ODBC pour SQL Server](http://www.microsoft.com/download/details.aspx?id=34687) | 11\.0 + | Pilote ODBC Microsoft pour SQL Server | [Lien](https://msdn.microsoft.com/fr-FR/library/hh568451.aspx) |
| ODBC | Redhat Linux | [ODBC pour SQL Server](http://www.microsoft.com/download/details.aspx?id=34687) | 11\.0 + | Pilote ODBC Microsoft pour SQL Server | [Lien](https://msdn.microsoft.com/fr-FR/library/hh568451.aspx) |

### Prise en charge ODBC

Quand vous utilisez l’Assistant Nom de source de données (DSN) pour définir une source de données pour Azure SQL Database, cliquez sur l’option **Avec l’authentification SQL Server utilisant un identificateur de connexion entré par l’utilisateur** et sélectionnez **Se connecter à SQL Server pour obtenir les paramètres par défaut pour les options de configuration supplémentaires**. Entrez votre nom d’utilisateur et votre mot de passe pour vous connecter à votre serveur Azure SQL Database dans **ID de connexion** et **Mot de passe**. Décochez la case **Se connecter à SQL Server pour obtenir les paramètres par défaut...**. Cliquez sur **Changer la base de données par défaut par** et entrez le nom de votre base de données SQL Azure même si elle n’apparaît pas dans la liste. Notez que l’Assistant répertorie plusieurs langues dans la liste **Modifier la langue des messages systèmes de SQL Server**.

Dans cette version, Microsoft Azure SQL Database prend en charge uniquement l’anglais, donc sélectionnez Anglais comme langue. Microsoft Azure SQL Database ne prend pas en charge les éléments **Serveur miroir** et **Attacher la base de données**, donc ne les renseignez pas. Cliquez sur **Tester la connexion**.

Quand vous utilisez le pilote ODBC SQL Server 2008 Native Client, le bouton **Tester la connexion** peut provoquer une erreur qui indique que **master.dbo.syscharsets** n’est pas pris en charge. Ignorez cette erreur, enregistrez le nom de source de données (DSN) et utilisez-le.

### OLE DB pour DB2 et SQL Server, pour la conception DRDA

Le fournisseur Microsoft OLE DB pour DB2 version 5.0 (fournisseur de données) vous permet de créer des applications distribuées qui ciblent les bases de données IBM DB2. Le fournisseur de données tire parti de l’architecture d’accès aux données Microsoft SQL Server combinée au client réseau Microsoft pour DB2 qui fonctionne comme un demandeur d’application DRDA (Distributed Relational Database Architecture). Le fournisseur de données convertit les commandes ole db Component Object Model (com) de Microsoft et les types de données en formats de données et points de code de protocole DRDA.

Pour plus d’informations, consultez :

- [Fournisseur Microsoft OLE DB pour DB2 version 5.0](http://msdn.microsoft.com/library/dn745875.aspx)
- [Fournisseur Microsoft OLE DB pour DB2 v4.0 pour Microsoft SQL Server 2012](http://www.microsoft.com/download/details.aspx?id=29100)

## Bibliothèques tierces

> [AZURE.IMPORTANT] Le tableau suivant affiche les bibliothèques publiées par des tiers selon les termes du contrat de licence tiers. Vous êtes responsable de la vérification et du respect des licences tierces pertinentes, afin de pouvoir utiliser ces bibliothèques. Vous utilisez ces bibliothèques à vos risques et périls. Microsoft n’offre aucune garantie, expresse ou implicite, concernant les informations fournies ici. Ces dernières sont fournies uniquement pour le confort des utilisateurs. Aucun élément du présent paragraphe n’implique une quelconque approbation de la part de Microsoft. <br/><br/>Il est de la responsabilité de la communauté des développeurs de mettre à jour et de gérer les informations figurant dans la section relative aux bibliothèques tierces, en utilisant le référentiel [azure-content](http://github.com/Azure/azure-content/) appartenant à **Azure** sur le site GitHub.com. Microsoft encourage les développeurs à mettre à jour cette section. Le personnel de Microsoft n’a *pas* l’intention de gérer les informations de cette section, en partie parce qu’il existe des experts nettement plus familiarisés avec chaque bibliothèque tierce. Merci !

Le tableau suivant affiche les bibliothèques publiées par des tiers, tels que d’autres entreprises ou la communauté. Les bibliothèques publiées par Microsoft sont limitées à la section précédente de cette rubrique.

| Plateforme | Bibliothèques |
| :-- | :-- |
| Ruby | [tinytds *(organisation stable)*](https://rubygems.org/gems/tiny_tds/versions/0.7.0) |
| GO | [go-mssqldb *(organisation stable)*](https://github.com/denisenkom/go-mssqldb) |
| Python | [pymssql *(organisation stable)*](http://pymssql.org/en/stable/) |
| Node.js | [Node-MSSQL *(npmjs)*](https://www.npmjs.com/package/node-mssql)<br/><br/>[Node-MSSQL-Connector *(npmjs)*](https://www.npmjs.com/package/node-mssql-connector) |
| C++ | [FreeTDS *(org)*](http://www.freetds.org/) |



<!--
https://en.wikipedia.org/wiki/Draft:Microsoft_SQL_Server_Libraries/Drivers
-->

<!---HONumber=AcomDC_0302_2016-->