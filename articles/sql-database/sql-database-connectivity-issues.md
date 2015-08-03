<properties 
	pageTitle="Problèmes de connectivité de la base de données SQL Azure" 
	description="Identification et détermination des échecs de connexion à base de données SQL" 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="NA" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA" 
	ms.date="07/17/2015" 
	ms.author="sstein"/>


# Problèmes de connexion à la base de données SQL

Cet article fournit une vue d'ensemble sur l'utilisation de divers outils de dépannage lorsque vous ne pouvez pas vous connecter à la base de données SQL Azure.


## Déterminez si le problème de connectivité est spécifique à une application individuelle ou s'il vous est tout simplement impossible de vous connecter à la base de données.

Utilisez SQL Server Management Studio ou SQLCMD.EXE pour vérifier que vous pouvez vous connecter à la base de données.

- Pour obtenir des instructions sur la connexion à une base de données SQL avec SQL Server Management Studio (SSMS), consultez [Connexion à une base de données SQL Azure avec SSMS](sql-database-connect-to-database.md).
- Pour obtenir des instructions sur la connexion à une base de données SQL avec SQLCMD, consultez [Connexion à une base de données SQL Azure avec SQLCMD](https://msdn.microsoft.com/library/azure/ee336280.aspx).



## L'application tente-t-elle de se connecter à la base de données SQL en cours d'exécution dans Azure (par exemple, l'application qui ne parvient pas à se connecter à la base de données est-elle un service cloud ou une application Web) ?

Vérifiez que la règle de pare-feu autorisant tous les services Azure est activée pour le serveur/la base de données.

- Pour plus d'informations sur les règles de pare-feu et l'activation des connexions à partir d'Azure, consultez [Pare-feu de la base de données SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure).



## L'application tente-t-elle de se connecter à la base de données SQL à partir d'un réseau privé ?

Vérifiez que les règles de pare-feu autorisant l'accès à partir de réseaux spécifiques est activée pour le serveur/la base de données.

- Pour obtenir des informations générales sur les règles de pare-feu, consultez [Pare-feu de la base de données SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).
- Pour obtenir des instructions sur la configuration des règles de pare-feu, consultez [Configuration des paramètres de pare-feu (base de données SQL Azure)](https://msdn.microsoft.com/library/azure/jj553530.aspx).


## Si les règles de pare-feu sont correctement configurées, alors exécutez la procédure guidée décrite dans [Dépannage de la connectivité de la base de données SQL Microsoft Azure](https://support2.microsoft.com/common/survey.aspx?scid=sw;en;3844&showpage=1).

L'article d'assistance ci-dessus fournit une aide pour les problèmes de connectivité suivants de la base de données SQL :

- Impossible d'ouvrir la connexion au serveur en raison des paramètres de pare-feu 
- Le serveur est introuvable ou inaccessible 
- Impossible de se connecter au serveur 
- Erreurs de délai d'expiration de connexion 
- Erreurs temporaires 
- Connexion terminée en raison d'une limite définie par le système 
- Échec des connexions à partir de SQL Server Management Studio (SSMS) 


## Informations supplémentaires

- Pour plus d'informations sur la connexion à la base de données SQL, consultez [Recommandations pour la connexion à la base de données SQL Azure par programme](https://msdn.microsoft.com/library/azure/ee336282.aspx).   

- Vous trouverez plus d'informations sur les erreurs de connexion spécifiques dans la section **Erreurs de perte de connexion** sous [Messages d'erreur (base de données SQL Azure)](https://msdn.microsoft.com/library/azure/ff394106.aspx#bkmk_connection_errors).

- Vous pouvez accéder aux données de l'événement de connexion en interrogeant les événements de connectivité à l'aide de la vue [**sys.event_log (base de données de SQL Azure)**](https://msdn.microsoft.com/library/dn270018.aspx).

- Vous pouvez accéder aux mesures des événements de connectivité de la base de données en interrogeant la vue [**sys.database_connection_stats (base de données de SQL Azure)**](https://msdn.microsoft.com/library/dn269986.aspx).

 

<!---HONumber=July15_HO4-->