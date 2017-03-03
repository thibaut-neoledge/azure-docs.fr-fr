---
title: "Résolution des problèmes de connexion courants à Azure SQL Database"
description: "Opérations servant à identifier et résoudre les erreurs de connexion courantes pour Azure SQL Database."
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
ms.assetid: ac463d1c-aec8-443d-b66e-fa5eadcccfa8
ms.service: sql-database
ms.custom: troubleshoot
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2017
ms.author: daleche
translationtype: Human Translation
ms.sourcegitcommit: 676cecdd886cfb557e7859e1e9583f0a0f9f749c
ms.openlocfilehash: 222b9fe98592e0c78ec3d7c5ae4804bf75dd0d1e
ms.lasthandoff: 02/16/2017


---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Résoudre des problèmes de connexion à la base de données SQL Azure
En cas d’échec de la connexion à la base de données SQL Azure, vous recevez des [messages d’erreur](sql-database-develop-error-messages.md). Cet article est une rubrique centralisée qui vous permet de résoudre les problèmes de connectivité des bases de données SQL Azure. Il présente les [causes courantes](#cause) des problèmes de connexion, vous recommande un [outil de dépannage](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) qui vous permet d’identifier le problème, et fournit les étapes de dépannage permettant de résoudre les [erreurs temporaires](#troubleshoot-transient-errors) et les [erreurs persistantes ou non temporaires](#troubleshoot-the-persistent-errors). Enfin, il répertorie [tous les articles pertinents par rapport aux problèmes de connectivité des bases de données SQL Azure](#all-topics-for-azure-sql-database-connection-problems).

Si vous rencontrez des problèmes de connexion, essayez de suivre les étapes de dépannage décrites dans cet article.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Cause :
Les problèmes de connexion peuvent avoir l’une des causes suivantes :

* Échec de la mise en œuvre de bonnes pratiques et de directives de conception pendant le processus de conception de l’application.  Pour commencer, consultez la page [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md) .
* Reconfiguration de la base de données SQL Azure
* Paramètres du pare-feu
* Expiration du délai de connexion
* Informations de connexion incorrectes
* Dépassement de la limite maximale sur certaines ressources de la base de données SQL Azure

En général, les problèmes de connexion à la base de données SQL Azure peuvent être classés ainsi :

* [Erreurs temporaires (de courte durée ou intermittentes)](#troubleshoot-transient-errors)
* [Erreurs persistantes ou non temporaires (erreurs qui se produisent régulièrement)](#troubleshoot-the-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Essayez l’utilitaire de résolution des problèmes de connectivité des bases de données SQL Azure
Si vous rencontrez une erreur de connexion spécifique, essayez [cet outil](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), qui vous aidera à identifier et à résoudre rapidement votre problème.

## <a name="troubleshoot-transient-errors"></a>Résoudre les erreurs temporaires
Si votre application connaît des erreurs temporaires, consultez les rubriques suivantes pour obtenir des conseils sur la façon de les résoudre et de réduire la fréquence de ces erreurs :

* [La résolution des problèmes pour la base de données &lt;x&gt; sur le serveur &lt;y&gt; n’est pas disponible (Erreur : 40613)](sql-database-troubleshoot-connection.md)
* [Diagnostiquer, résoudre et empêcher les erreurs de connexion SQL et les erreurs temporaires de Base de données SQL](sql-database-connectivity-issues.md)

<a id="troubleshoot-the-persistent-errors" name="troubleshoot-the-persistent-errors"></a>

## <a name="troubleshoot-persistent-errors-non-transient-errors"></a>Résoudre les erreurs persistantes (erreurs non temporaires)
Si l’application échoue de façon permanente à se connecter à la base de données SQL Azure, cela indique généralement un problème avec l’un des éléments suivants :

* Configuration du pare-feu. Le pare-feu Azure SQL Database ou côté client bloque les connexions à SQL Azure Database.
* Reconfiguration du réseau côté client : par exemple, une nouvelle adresse IP ou un nouveau serveur proxy.
* Erreur utilisateur : par exemple, paramètres de connexion saisis de façon incorrecte, comme le nom du serveur dans la chaîne de connexion.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Étapes permettant résoudre les problèmes de connectivité persistants
1. Configurez les [règles de pare-feu](sql-database-configure-firewall-settings.md) pour autoriser l’adresse IP du client. Définissez une règle de pare-feu avec une plage d’adresses IP allant de 0.0.0.0 à 255.255.255.255 à des fins de test temporaire. Cette opération ouvrira le serveur à toutes les adresses IP. Si elle résout votre problème de connectivité, supprimez cette règle et créez une règle de pare-feu pour une adresse ou une plage d’adresses correctement bornée. 
2. Sur tous les pare-feu situés entre le client et Internet, assurez-vous que le port 1433 est ouvert pour les connexions sortantes. Consultez les pages [Configurer le pare-feu Windows de façon à autoriser l’accès à SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) et [Ports et protocoles requis pour l’identité hybride](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) pour connaître les pointeurs supplémentaires liés aux ports supplémentaires que vous devez ouvrir pour l’authentification Azure Active Directory.
3. Vérifiez votre chaîne de connexion et d’autres paramètres de connexion. Consultez la section Chaîne de connexion dans la [rubrique concernant les problèmes de connectivité](sql-database-connectivity-issues.md#connections-to-azure-sql-database).
4. Vérifiez l’état du service dans le tableau de bord. Si vous soupçonnez une panne régionale, consultez [Restauration à la suite d’une panne](sql-database-disaster-recovery.md) pour connaître les étapes de restauration vers une nouvelle zone.

## <a name="all-topics-for-azure-sql-database-connection-problems"></a>Toutes les rubriques pour les problèmes de connexion des bases de données SQL Azure
Le tableau suivant répertorie les rubriques de problèmes de connexion qui s’appliquent directement au service de base de données SQL Azure.

| &nbsp; | Intitulé | Description |
| ---:|:--- |:--- |
| 1 |[Résoudre des problèmes de connexion à la base de données SQL Azure](sql-database-troubleshoot-common-connection-issues.md) |Il s’agit de la page d’accueil destinée à résoudre les problèmes de connectivité des bases de données SQL Azure. Elle explique comment identifier et résoudre les erreurs temporaires et les erreurs persistantes ou non temporaires dans la base de données SQL Azure. |
| 2 |[Diagnostiquer, résoudre et empêcher les erreurs de connexion SQL et les erreurs temporaires de Base de données SQL](sql-database-connectivity-issues.md) |Découvrez comment diagnostiquer, résoudre et empêcher une erreur de connexion SQL ou une erreur temporaire dans Base de données SQL Azure. |
| 3 |[Aide générale sur le traitement des erreurs temporaires](../best-practices-retry-general.md) |Apporte une aide générale concernant le traitement des erreurs temporaires lors de la connexion à la base de données SQL Azure. |
| 4 |[Résoudre les problèmes de connectivité à la base de données SQL Microsoft Azure](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database) |Cet outil permet d’identifier votre problème et de résoudre les erreurs de connexion. |
| 5 |[Résoudre l’erreur « La base de données &lt;x&gt; sur le serveur &lt;y&gt; n’est pas disponible actuellement. Veuillez réessayer la connexion ultérieurement. »](sql-database-troubleshoot-connection.md) |Explique comment identifier et résoudre une erreur 40613 : « La base de données &lt;x&gt; sur le serveur &lt;y&gt; n’est pas disponible actuellement. Veuillez réessayer la connexion ultérieurement. » |
| 6 |[Codes d’erreur SQL pour les applications clientes SQL Database : erreur de connexion à la base de données et autres problèmes](sql-database-develop-error-messages.md) |Fournit des informations sur les codes d’erreur SQL pour les applications clientes de base de données SQL, tels que les erreurs de connexion de base de données courantes, les problèmes de copie de base de données et les erreurs générales. |
| 7 |[Guide des performances de base de données SQL Azure pour les bases de données uniques](sql-database-performance-guidance.md) |Vous aide à déterminer le niveau de service adapté à votre application. Fournit également des recommandations pour le paramétrage de votre application afin de tirer le meilleur parti de votre base de données SQL Azure. |
| 8 |[Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md) |Fournit des liens vers des exemples de code pour diverses technologies que vous pouvez utiliser pour vous connecter à et interagir avec une base de données SQL Azure. |

## <a name="next-steps"></a>Étapes suivantes
* [Résoudre les problèmes de performances des bases de données SQL Azure](sql-database-troubleshoot-performance.md)
* [Rechercher dans la documentation de Microsoft Azure](http://azure.microsoft.com/search/documentation/)
* [Affichage des dernières mises à jour du service de base de données SQL Azure](http://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Ressources supplémentaires
* [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md)
* [Aide générale sur le traitement des erreurs temporaires](../best-practices-retry-general.md)
* [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md)


