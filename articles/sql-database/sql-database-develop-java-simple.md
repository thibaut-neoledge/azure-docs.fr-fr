<properties
	pageTitle="Connexion à une base de données SQL à l’aide de Java avec JDBC sous Windows | Microsoft Azure"
	description="Cette rubrique présente un exemple de code Java que vous pouvez utiliser pour vous connecter à la base de données SQL Azure. L'exemple utilise JDBC et s'exécute sur un ordinateur client Windows."
	services="sql-database"
	documentationCenter=""
	authors="LuisBosquez"
	manager="jeffreyg"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="04/27/2016"
	ms.author="lbosq"/>


# Connexion à la base de données SQL à l’aide de Java avec JDBC sous Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Cette rubrique présente un exemple de code Java que vous pouvez utiliser pour vous connecter à la base de données SQL Azure. L'exemple Java s'appuie sur le Kit de développement Java (JDK) version 1.8. L'exemple se connecte à une base de données SQL Azure en utilisant le pilote JDBC.

## Étape 1 : configurer l’environnement de développement

[Configurer l’environnement de développement pour le développement Java](https://msdn.microsoft.com/library/mt720658.aspx)

## Étape 2 : créer une base de données SQL

Consultez la [page de prise en main](sql-database-get-started.md) pour apprendre à créer une base de données.

## Étape 3 : obtenir la chaîne de connexion

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE] Si vous utilisez le pilote JTDS JDBC, vous devez ajouter « ssl=require » à l’URL de la chaîne de connexion et vous devez définir l’option suivante pour l’environnement JVM : « -Djsse.enableCBCProtection=false ». Cette option JVM désactive un correctif pour un problème de sécurité. Par conséquent, assurez-vous que vous savez quels risques vous prenez avant de définir cette option.

## Étape 4 : Exécuter l’exemple de code

* [Preuve de concept sur la connexion à SQL avec Java](https://msdn.microsoft.com/library/mt720656.aspx)

## Étapes suivantes

Pour plus d’informations, consultez le [Centre pour développeurs Java](/develop/java/).

<!---HONumber=AcomDC_0504_2016-->