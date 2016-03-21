<properties
	pageTitle="Identifier des bases de données et des tables pour Stretch Database en exécutant Stretch Database Advisor | Microsoft Azure"
	description="Découvrez comment identifier les bases de données et les tables candidates pour Stretch Database."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Identifier des bases de données et des tables pour Stretch Database en exécutant Stretch Database Advisor

Pour identifier les bases de données candidates pour Stretch Database Advisor, téléchargez SQL Server 2016 Upgrade Advisor et exécutez le Stretch Database Advisor. Stretch Database Advisor identifie également les problèmes de blocage.

## Télécharger et installer le Conseiller de mise à niveau
Téléchargez et installez le Conseiller de mise à niveau depuis [ici](http://go.microsoft.com/fwlink/?LinkID=613421). Cet outil n’est pas inclus sur le support d’installation de SQL Server.

## Exécuter Stretch Database Advisor

1.  Exécutez le Conseiller de mise à niveau

2.  Sélectionnez**Scenarios (Scénarios)**, puis **RUN STRETCH DATABASE ADVISOR (Exécuter Stretch Database Advisor)**.

3.  Dans le panneau **Run Stretch Database Advisor (Exécuter Stretch Database Advisor)**, cliquez sur **SELECT DATABASES TO ANALYZE (Sélectionner les bases de données à analyser)**.

4.  Dans le panneau **Select databases (Sélectionner des bases de données)** panneau, cliquez sur **INSTANCE SQL**.

5.  Dans le panneau **Connect to SQL Instance (Connexion à l’instance SQL)**, saisissez le nom de l’instance de serveur SQL. Puis, cliquez sur **Se connecter**.

6.  Dans le panneau **Select databases (Sélectionner des bases de données)**, sélectionnez les bases de données à analyser. Puis cliquez sur **Select (Sélectionner)**.

7.  Dans le panneau **Run Stretch Database Advisor (Exécuter Stretch Database Advisor)**, cliquez sur **run (Exécuter)**. L’analyse s’exécute.

## Passer en revue les résultats.

1.  Une fois l’analyse terminée, sélectionnez dans le **Stretch Database Advisor**, sélectionnez l’une des bases de données analysées pour afficher le panneau **Résultats de l’analyse**.

    Le panneau **Résultats de l’analyse** répertorie les tables recommandées de la base de données qui correspondent aux critères de recommandation par défaut. Si vous le souhaitez, ajustez la taille minimale et de nombre de lignes à développer ou réduire dans la liste des tables recommandées.

2.  Dans la liste des tables recommandées du panneau **Résultats de l’analyse**, sélectionnez l’une des tables recommandées pour afficher le panneau **Table results (Résultats de la table)**.

    Le panneau **Table results (Résultats de la table)** répertorie les problèmes de blocage de la table sélectionnée. Pour plus d’informations sur les problèmes de blocage détectés par Stretch Database Advisor, consultez la rubrique [Limitations de la surface d’exposition et problèmes de blocage pour Stretch Database](sql-server-stretch-database-limitations.md).

3.  Dans la liste des problèmes de blocage du panneau des **résultats de la table**, sélectionnez l’un des problèmes pour afficher le panneau **Rule result (Résultat de la règle)**.

    Le panneau **Rule result (Résultat de la règle)** décrit le problème sélectionné et propose une solution pour le résoudre. Implémentez la solution suggérée si vous souhaitez configurer la table sélectionnée pour Stretch Database.

## Étape suivante
Activer Stretch Database

-   Pour activer Stretch Database dans une **base de données**, consultez [Activer Stretch Database pour une base de données](sql-server-stretch-database-enable-database.md)

-   Pour activer Stretch Database sur une autre **table** lorsque Stretch est déjà activé sur la base de données, consultez [Activer Stretch Database pour une table](sql-server-stretch-database-enable-table.md).

## Voir aussi
[Limitations de la surface d’exposition et problèmes de blocage pour Stretch Database](sql-server-stretch-database-limitations.md) [Activer Stretch Database pour une base de données](sql-server-stretch-database-enable-database.md) [Activer Stretch Database pour une table](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0309_2016-->