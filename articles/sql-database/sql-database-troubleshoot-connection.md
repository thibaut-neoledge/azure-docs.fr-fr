<properties
	pageTitle="La résolution des problèmes de base de données de serveur n’est pas disponible actuellement pour la base de données SQL Azure"
	description="Opérations servant à identifier et résoudre les erreurs de connexion de la base de données SQL Azure."
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
	ms.date="02/12/2016"
	ms.author="daleche"/>

# Dépannage « La base de données sur serveur n’est pas disponible actuellement. Veuillez réessayer la connexion ultérieurement. » et autres erreurs de connexion
« Base de données <dbname> sur le serveur <servername> n’est pas disponible... » est l’erreur provisoire la plus fréquente de la base de données SQL Azure. Les erreurs de connexion temporaires sont généralement liées à un événement planifié (par exemple, une mise à niveau logicielle) ou à un événement non planifié (par exemple, un arrêt de processus). Elles sont généralement de courte durée, allant de quelques secondes à une minute au maximum. Si vous recevez une autre erreur, évaluez le [message d’erreur](sql-database-develop-error-messages.md) pour obtenir des indices sur la cause, déterminer si le problème est provisoire ou permanent et suivre les instructions fournies dans cette rubrique.

## Étapes pour résoudre les problèmes de connectivité transitoire
1.	Vérifiez le [tableau de bord des services Microsoft Azure](https://azure.microsoft.com/status) pour les coupures prévues.
2.	Assurez-vous que votre application utilise le programme pour la nouvelle tentative. Consultez les [problèmes de connectivité](sql-database-connectivity-issues.md) et les [meilleures pratiques recommandées et directives de conception](sql-database-connect-central-recommendations.md) pour les stratégies de restauration générale. Consultez la section [exemples de code](sql-database-develop-quick-start-client-code-samples.md) pour obtenir des détails.
3.	Lorsqu’une base de données approche des limites de ressources, cela peut s’apparenter à un problème de connectivité transitoire. Consultez la page [Résolution des problèmes de performances](sql-database-troubleshoot-performance.md).
4.	Si les problèmes de connectivité persistent, créez une demande de support Azure en sélectionnant **Obtenir une assistance** sur le site du [support Azure](https://azure.microsoft.com/support/options).

## Étapes permettant résoudre les problèmes de connectivité persistants
Si l’application ne peut pas du tout se connecter, cela est généralement dû à la configuration IP et de pare-feu. Cela peut être dû à la reconfiguration du réseau côté client (par exemple, une nouvelle adresse IP ou un nouveau proxy), Les erreurs de saisie des paramètres de connexion, tels que la chaîne de connexion sont également communs.

1.	Configurez les [règles de pare-feu](sql-database-configure-firewall-settings.md) pour autoriser l’adresse IP du client.
2.	Sur tous les pare-feu situés entre le client et Internet, assurez-vous que le port 1433 est ouvert pour les connexions sortantes.
3.	Vérifiez votre chaîne de connexion et d’autres paramètres de connexion. Consultez la section Chaîne de connexion dans la [rubrique des problèmes de connectivité](sql-database-connectivity-issues.md).
4.	Vérifiez l’état du service dans le tableau de bord. Si vous soupçonnez une panne régionale, consultez [Restauration à la suite d’une panne](sql-database-disaster-recovery.md) pour connaître les étapes de restauration vers une nouvelle zone.
5.	Si les problèmes de connectivité persistent, créez une demande de support Azure en sélectionnant **Obtenir une assistance** sur le site du [support Azure](https://azure.microsoft.com/support/options).

<!---HONumber=AcomDC_0218_2016-->