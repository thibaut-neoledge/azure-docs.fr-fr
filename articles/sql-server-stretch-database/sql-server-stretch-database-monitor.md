<properties
	pageTitle="Surveillance et dépannage de la migration de données (Stretch Database) | Microsoft Azure"
	description="Découvrez comment surveiller l’état de la migration des données."
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
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# Surveillance et dépannage de la migration de données (Stretch Database)

Pour surveiller la migration des données dans Stretch Database Monitor, sélectionnez **Tâches | Stretch | Surveiller** pour une base de données dans SQL Server Management Studio.

## Vérifier l’état de la migration des données dans Stretch Database Monitor
Sélectionnez **Tâches | Stretch | Surveiller** pour une base de données dans SQL Server Management Studio pour ouvrir Stretch Database Monitor et surveiller la migration des données.

-   La partie supérieure du moniteur affiche des informations générales sur la base de données SQL Server compatible Stretch et la base de données Azure distante.

-   La partie inférieure du moniteur affiche l’état de la migration des données pour chaque table compatible Stretch dans la base de données.

![Stretch Database Monitor][StretchMonitorImage1]

## <a name="Migration"></a>Vérifier l’état de la migration des données dans une vue de gestion dynamique fournit l’état de la migration.
Ouvrez la vue de gestion dynamique **sys.dm\_db\_rda\_migration\_status** pour voir combien de lots et de lignes de données ont été migrés. Pour plus d’informations, consultez [sys.dm\_db\_rda\_migration\_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).

## <a name="Firewall"></a>Dépannage de la migration des données

**Les lignes de ma table Stretch ne sont pas migrées vers Azure. Quel est le problème ?**

Plusieurs problèmes peuvent affecter la migration. Vérifiez les éléments suivants.

-   Vérifiez la connectivité réseau pour l’ordinateur SQL Server.

-   Vérifiez que le pare-feu Azure n’empêche pas votre serveur SQL Server de se connecter au point de terminaison distant.

-   Vérifiez l’état du dernier lot dans la vue de gestion dynamique **sys.dm\_db\_rda\_migration\_status**. Si une erreur s’est produite, vérifiez les valeurs error\_number, error\_state et error\_severity pour le lot.

    -   Pour plus d’informations sur la vue, consultez [sys.dm\_db\_rda\_migration\_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).

    -   Pour plus d’informations sur le contenu d’un message d’erreur SQL Server, consultez [sys.messages (Transact-SQL)](https://msdn.microsoft.com/library/ms187382.aspx).

**Le pare-feu Azure bloque les connexions à partir de mon serveur local.**

Vous devrez peut-être ajouter une règle dans les paramètres de pare-feu Azure du serveur Azure pour permettre à SQL Server de communiquer avec le serveur Azure distant.

## Voir aussi

[Gestion et dépannage de Stretch Database](sql-server-stretch-database-manage.md)

<!--Image references-->
[StretchMonitorImage1]: ./media/sql-server-stretch-database-monitor/StretchDBMonitor.png

<!---HONumber=AcomDC_0615_2016-->