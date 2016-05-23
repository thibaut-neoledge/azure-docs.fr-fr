<properties
	pageTitle="Résolution des problèmes de connexion courants à Azure SQL Database"
	description="Opérations servant à identifier et résoudre les erreurs de connexion courantes pour Azure SQL Database."
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/10/2016"
	ms.author="daleche"/>

# Résolution des problèmes de connexion courants à Azure SQL Database
Les problèmes de connexion à Azure SQL Database peuvent être classés selon les catégories suivantes :

- [Erreurs temporaires (de courte durée ou intermittentes)](#troubleshoot-the-transient-errors)
- [Erreurs persistantes ou non temporaires (erreurs qui se produisent régulièrement)](#troubleshoot-the-persistent-errors-non-transient-errors)

Si vous rencontrez des problèmes de connexion, essayez de suivre les étapes de dépannage décrites dans cet article.[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Résoudre les erreurs temporaires
Si votre application connaît des erreurs temporaires, consultez les rubriques suivantes pour obtenir des conseils sur la façon de les résoudre et de réduire la fréquence de ces erreurs :

- [La résolution des problèmes pour la base de données &lt;x&gt; sur le serveur &lt;y&gt; n’est pas disponible (Erreur : 40613)](sql-database-troubleshoot-connection.md)
- [Diagnostiquer, résoudre et empêcher les erreurs de connexion SQL et les erreurs temporaires de Base de données SQL](sql-database-connectivity-issues.md)

<a id="troubleshoot-the-persistent-errors" name="troubleshoot-the-persistent-errors"></a>
## Résoudre les erreurs persistantes (erreurs non temporaires)
Si l’application échoue de façon permanente à se connecter SQL Azure Database, cela indique généralement un problème avec l’un des éléments suivants :

- Configuration du pare-feu. Le pare-feu Azure SQL Database ou côté client bloque les connexions à SQL Azure Database.
- Reconfiguration du réseau côté client : par exemple, une nouvelle adresse IP ou un nouveau serveur proxy.
- Erreur utilisateur : par exemple, paramètres de connexion saisis de façon incorrecte, comme le nom du serveur dans la chaîne de connexion.

### Étapes permettant résoudre les problèmes de connectivité persistants
1.	Configurez les [règles de pare-feu](sql-database-configure-firewall-settings.md) pour autoriser l’adresse IP du client.
2.	Sur tous les pare-feu situés entre le client et Internet, assurez-vous que le port 1433 est ouvert pour les connexions sortantes. Consultez [Configuration du pare-feu Windows pour autoriser l’accès à SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) pour obtenir des informations supplémentaires.
3.	Vérifiez votre chaîne de connexion et d’autres paramètres de connexion. Consultez la section Chaîne de connexion dans la [rubrique des problèmes de connectivité](sql-database-connectivity-issues.md#connections-to-azure-sql-database).
4.	Vérifiez l’état du service dans le tableau de bord. Si vous soupçonnez une panne régionale, consultez [Restauration à la suite d’une panne](sql-database-disaster-recovery.md) pour connaître les étapes de restauration vers une nouvelle zone.

<!---HONumber=AcomDC_0511_2016-->