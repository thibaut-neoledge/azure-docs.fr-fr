<properties
   pageTitle="Outils de gestion pour SQL Data Warehouse | Microsoft Azure"
   description="Introduction aux outils de gestion pour SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="HappyNicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="mausher;nicw;barbkess;JRJ@BigBangData.co.uk;"/>

# Outils de gestion pour SQL Data Warehouse
Cette rubrique explore et compare les différents outils et options de gestion de SQL Data Warehouse, de manière à ce que vous puissiez sélectionner l’outil adapté à vos besoins. Or, la sélection de l’outil adapté dépend du nombre de bases de données que vous gérez, de la tâche effectuée et de la fréquence de son exécution.

## Portail Azure
Le [portail Azure][] est un portail de gestion basé sur le Web dans lequel vous pouvez créer, mettre à jour et supprimer les bases de données et surveiller les ressources de base de données. Cet outil est très utile si vous n’êtes pas familiarisé avec Microsoft Azure, si vous gérez un petit nombre de bases de données Data Warehouse, ou si vous devez effectuer rapidement une action.

Le portail fournit des mesures relatives aux paramètres actuels et archivés de performances des unités DWU, au volume de stockage utilisé, aux connexions SQL réussies et échouées et un ensemble de visualisations et de données que vous mettez à profit pour mieux comprendre les requêtes exécutées sur votre instance et les détails associés.

## SQL Server Data Tools dans Visual Studio	
[SQL Server Data Tools][] (SSDT) dans Visual Studio est un outil client qui s’exécute sur votre ordinateur et vous permet de connecter, gérer et développer votre base de données dans le Cloud. Si vous êtes un développeur d’applications familiarisé avec Visual Studio ou d’autres environnements de développement intégré (IDE), essayez la fonction SSDT de Visual Studio.

SSDT comprend la solution SQL Server Explorer qui vous permet de visualiser, de connecter et d’exécuter des scripts dans des bases de données SQL Data Warehouse. Pour vous connecter rapidement à SQL Data Warehouse, il vous suffit de cliquer sur le bouton **Ouvrir dans Visual Studio** de la barre de commandes lorsque vous affichez les détails de la base de données dans le portail Azure.

Vous pouvez télécharger la dernière version de [SQL Server Data Tools][] (SSDT) qui inclut la mise en charge de SQL Data Warehouse.

## Outils de ligne de commande
Vous pouvez éventuellement utiliser les outils de ligne de commande PowerShell ou sqlcmd pour gérer SQL Data Warehouse et pour automatiser les déploiements de ressources Microsoft Azure. Nous vous recommandons d’utiliser ces outils pour gérer un grand nombre de serveurs logiques et pour déployer des modifications de ressources au sein d’un environnement de production, dans la mesure où les tâches requises peuvent alors être incluses dans un script, puis automatisées.

## Étapes suivantes
Pour commencer à utiliser ces outils, accédez à la rubrique [connexion][].

<!--Image references-->

<!--Article references-->
[connexion]: sql-data-warehouse-develop-connections.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/fr-FR/library/mt204009.aspx

<!--Other web references-->
[portail Azure]: http://portal.azure.com/

<!---HONumber=Oct15_HO1-->