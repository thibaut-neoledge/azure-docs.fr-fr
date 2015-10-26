<properties 
	pageTitle="Mettre à niveau vers la version 12 de la base de données SQL" 
	description="Explique comment mettre à niveau la base de données SQL Microsoft Azure vers la version 12 à partir d’une version antérieure." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg"
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="10/08/2015" 
	ms.author="sstein"/>


# Mettre à niveau vers la version 12 de la base de données SQL


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-v12-upgrade.md)
- [PowerShell](sql-database-upgrade-server.md)


SQL Database V12 est la dernière version de SQL Database et elle propose [de nombreux avantages par rapport à la précédente version, V2](sql-database-v12-whats-new.md). Cet article explique comment mettre à niveau les serveurs V2 vers V12 avec le portail Azure en version préliminaire.

Au cours du processus de mise à niveau vers la Base de données SQL V12, vous devez également [mettre à jour toutes les bases de données Web et Business vers un nouveau niveau de service](sql-database-upgrade-new-service-tiers.md). Les instructions suivantes incluent les étapes pour mettre à jour vos bases de données Web et Business avec des recommandations de niveau tarifaire basées sur l’historique d’utilisation de votre base de données.



1. Dans le [portail Azure en version préliminaire](http://portal.azure.com/), accédez au serveur que vous souhaitez mettre à niveau en sélectionnant **PARCOURIR TOUT** > **Serveurs SQL**, puis en sélectionnant le serveur souhaité.
2. Sélectionnez **Dernière mise à jour de la base de données SQL**, puis **Mettre à niveau ce serveur**.

      ![mettre à niveau le serveur][1]

## Mise à niveau de vos bases de données Web et Business

2. Mettez à niveau toutes les bases de données Web et Business. Si votre serveur dispose d’une base de données Web ou Business, vous devez la mettre à niveau. Pour vous aider lors de la mise à niveau, le service Base de données SQL recommande un niveau de service et de performances approprié (niveau tarifaire) pour chaque base de données. En analysant l’historique d’utilisation de chaque base de données, le service recommande un niveau qui convient le mieux pour l’exécution des charges de travail de votre base de données existante. 
    
    Sélectionnez chaque base de données pour l’examiner, puis sélectionnez le niveau tarifaire recommandé pour la mise à niveau. Vous pouvez également parcourir les niveaux tarifaires disponibles et sélectionner celui qui convient le mieux à votre environnement.

     ![bases de données][2]



7. Lorsque vous cliquez sur un niveau suggéré, le panneau **Choisir votre niveau de tarification** s’affiche, dans lequel vous pouvez sélectionner un niveau, puis cliquer sur le bouton **Sélectionner** pour le modifier. Sélectionnez un nouveau niveau pour chaque base de données Web ou Business.

    ![de films][6]


Lorsque toutes les bases de données du serveur sont éligibles, vous pouvez commencer la mise à niveau.

## Lancer la mise à niveau

3. Lorsque toutes les bases de données sur le serveur sont mises à niveau, vous devez **SAISIR LE NOM DU SERVEUR** pour vérifier que vous souhaitez effectuer la mise à niveau, avant de cliquer sur **OK**. 

    ![vérifier la mise à niveau][3]


4. La mise à niveau démarre. Sa progression est affichée dans une zone de notification. Le processus de mise à jour est lancé. Selon les détails de votre mise à niveau de bases de données vers V12, la procédure peut prendre un certain temps. Pendant cette période, toutes les bases de données sur le serveur restent en ligne, mais les actions de gestion du serveur et des bases de données sont restreintes.

    ![mise à niveau en cours][4]

    Au moment précis de la transition vers le nouveau niveau de performances, une perte temporaire des connexions à la base de données peut parfois se produire pendant quelques secondes. Si une application gère les problèmes temporaires d’interruption de connexion (logique de nouvelle tentative), il suffit d’établir une protection contre la perte de connexion à la fin de la mise à jour.

5. Une fois l’opération de mise à niveau terminée, les fonctionnalités du serveur SQL Database V12 sont activées.

    ![V12 activé][5]


## Liens connexes

- [Nouveautés de la base de données SQL V12](sql-database-v12-whats-new.md)
- [Planifier et préparer la mise à niveau vers la version V12 de la base de données SQL](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-v12-upgrade/latest-sql-database-update.png
[2]: ./media/sql-database-v12-upgrade/upgrade-server2.png
[3]: ./media/sql-database-v12-upgrade/upgrade-server3.png
[4]: ./media/sql-database-v12-upgrade/online-during-upgrade.png
[5]: ./media/sql-database-v12-upgrade/enabled.png
[6]: ./media/sql-database-v12-upgrade/recommendations.png

<!---HONumber=Oct15_HO3-->