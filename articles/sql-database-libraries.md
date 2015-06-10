<properties
	pageTitle="Bibliothèques de connexions de la base de données SQL et de SQL Server"
	description="Répertorie le numéro de version minimal pour chaque pilote que les programmes clients peuvent utiliser lors de la connexion à la base de données SQL Microsoft Azure ou à Microsoft SQL Server. Un lien est fourni pour les informations sur les versions des pilotes publiés par la communauté, et non par Microsoft."
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
	ms.date="05/01/2015"
	ms.author="pehteh"/>


# Bibliothèques de connexions de la base de données SQL et de SQL Server


Cette rubrique répertorie le numéro de version minimal pour chaque bibliothèque/pilote que les programmes clients peuvent utiliser lors de la connexion à la base de données SQL Microsoft Azure ou à Microsoft SQL Server.


Cette rubrique est divisée en deux sections :


- *Table des bibliothèques de pilotes publiées par Microsoft* : porte sur les bibliothèques que Microsoft a publiées. Microsoft gère les informations figurant dans cette section.
- *Bibliothèques tierces* : répertorie les bibliothèques qui sont publiées et gérées par des tiers, et non par Microsoft. **Seule la communauté des développeurs gère cette section. Microsoft ne participe pas à sa gestion.**


## Tableau des bibliothèques de pilotes publiées par Microsoft


Le tableau suivant affiche les bibliothèques publiées par Microsoft. La colonne **Bibliothèques** fournit des liens vous permettant de télécharger chaque bibliothèque. La colonne **Version** répertorie la version minimale recommandée pour interagir avec la base de données SQL Microsoft Azure et Microsoft SQL Server.


| Plateforme | Sys Oper | Bibliothèques | Version | Description |
| :--- | :--- | :--- | :--- | :--- |
| .NET | Multiplateforme (.NET) | [ADO.NET, System .Data .SqlClient](http://www.microsoft.com/download/details.aspx?id=30653) | 4.5 + | Fournisseur SQL Server pour .NET Framework |
| PHP | Windows | [PHP pour SQL Server](http://www.microsoft.com/fr-fr/download/details.aspx?id=20098) | 2.0 + | Pilote PHP pour SQL Server |
| Java | Windows | [JDBC pour SQL Server](https://www.microsoft.com/fr-fr/download/details.aspx?id=11774) | 2.0 + | Pilote JDBC de type 4 qui fournit la connectivité de base de données via l’API JDBC standard |
| ODBC | Windows | [ODBC pour SQL Server](http://www.microsoft.com/fr-fr/download/details.aspx?id=36434) | 11.0 + | Pilote ODBC Microsoft pour SQL Server |
| ODBC | Suse Linux | [ODBC pour SQL Server](http://www.microsoft.com/fr-fr/download/details.aspx?id=34687) | 11.0 + | Pilote ODBC Microsoft pour SQL Server |
| ODBC | Redhat Linux | [ODBC pour SQL Server](http://www.microsoft.com/fr-fr/download/details.aspx?id=34687) | 11.0 + | Pilote ODBC Microsoft pour SQL Server |


## Bibliothèques tierces


> [AZURE.IMPORTANT]Le tableau suivant affiche les bibliothèques publiées par des tiers selon les termes du contrat de licence tiers. Vous êtes responsable de la vérification et du respect des licences tierces pertinentes, afin de pouvoir utiliser ces bibliothèques. Vous utilisez ces bibliothèques à vos risques et périls. Microsoft n’offre aucune garantie, expresse ou implicite, concernant les informations fournies ici. Ces dernières sont fournies uniquement pour le confort des utilisateurs. Aucun élément du présent paragraphe n’implique une quelconque approbation de la part de Microsoft. <br/><br/>Il est de la responsabilité de la communauté des développeurs de mettre à jour et de gérer les informations figurant dans la section relative aux bibliothèques tierces, en utilisant le référentiel [azure-content](http://github.com/Azure/azure-content/) appartenant à **Azure** sur le site GitHub.com. Microsoft encourage les développeurs à mettre à jour cette section. Le personnel de Microsoft n’a *pas* l’intention de gérer les informations de cette section, en partie parce qu’il existe des experts nettement plus familiarisés avec chaque bibliothèque tierce. Merci !


Le tableau suivant affiche les bibliothèques publiées par des tiers, tels que d’autres entreprises ou la communauté. Les bibliothèques publiées par Microsoft sont limitées à la section précédente de cette rubrique.


| Plateforme | Bibliothèques |
| :-- | :-- |
| Python | [pymssql *(org, stable)*](http://pymssql.org/en/stable/)<br/><br/>[pymssql *(org)*](http://pymssql.org/) |
| Node.js | [Tedious *(npmjs)*](http://www.npmjs.com/package/tedious) |
| Node.js | [Node-MSSQL *(github, patriksimek)*](https://github.com/patriksimek/node-mssql)<br/><br/>[Node-MSSQL *(npmjs)*](https://www.npmjs.com/package/node-mssql)<br/><br/>[Node-MSSQL-Connector *(npmjs)*](https://www.npmjs.com/package/node-mssql-connector) |
| Node.js | [Edge.js *(github com, tjanczuk)*](https://github.com/tjanczuk/edge)<br/><br/>[Edge.js *(tjanczuk, github e/s)*](http://tjanczuk.github.io/edge/) |
| . | [FreeTDS *(org)*](http://www.freetds.org/) |


<!--
https://en.wikipedia.org/wiki/Draft:Microsoft_SQL_Server_Libraries/Drivers
-->

<!---HONumber=58-->