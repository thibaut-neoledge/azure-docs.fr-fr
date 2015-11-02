<properties
	pageTitle="Configuration de la sécurité Standard ou de la Géo-réplication active"
	description="Cette rubrique décrit les éléments de sécurité pour la gestion des scénarios Standard ou géo-réplication Active pour la base de données SQL."
	services="sql-database"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="10/22/2015"
	ms.author="jroth" />

# Configuration de la sécurité Standard ou de la Géo-réplication active

## Vue d'ensemble
Cette rubrique décrit les exigences d’authentification requises pour configurer et contrôler la [géo-réplication standard et active](sql-database-geo-replication-overview.md) et les opérations requises pour configurer l’accès utilisateur à la base de données secondaire. Pour plus d’informations sur l’utilisation de Géo-réplication, consultez [Récupérer une base de données SQL Azure après une défaillance](sql-database-disaster-recovery.md).

## Utilisation des utilisateurs contenus
Avec la [version V12 de la base de données SQL Azure](sql-database-v12-whats-new.md), la base de données SQL prend désormais en charge les utilisateurs contenus. Contrairement aux utilisateurs classiques, qui doivent être mappés sur les connexions dans la base de données master, un utilisateur contenu est géré entièrement par la base de données elle-même. Cela a deux avantages. Dans le scénario de géo-réplication, les utilisateurs peuvent continuer de se connecter à la base de données secondaire sans configuration supplémentaire, car c’est la base de données qui gère les utilisateurs. Du point de vue de la connexion, cette configuration présente également des possibilités de mise à l’échelle et d’amélioration des performances. Pour plus d’informations, voir [Utilisateurs de base de données à relation contenant-contenu - Rendre votre base de données portable](https://msdn.microsoft.com/library/ff929188.aspx).

Avec des utilisateurs contenus, si plusieurs bases de données utilisent la même connexion, vous devez gérer l’utilisateur séparément pour chaque base de données (par exemple, en changeant un mot de passe), plutôt que de gérer la connexion au niveau du serveur.

>[AZURE.NOTE]Si vous souhaitez modifier l’accès en lecture principal et secondaire de façon indépendante, vous devez utiliser les utilisateurs et les connexions traditionnelles. Les utilisateurs contenus ne peuvent pas être gérés sur le serveur secondaire indépendamment du site principal.

## Utilisation de connexions et d’utilisateurs traditionnels
Si vous utilisez des connexions traditionnelles et les utilisateurs (et non les utilisateurs contenus), vous devez effectuer des mesures supplémentaires pour vous assurer que les mêmes connexions existent sur le serveur de base de données secondaire. Les sections suivantes décrivent les étapes impliquées et d’autres considérations relatives.

### Configurez l’accès utilisateur pour la ligne secondaire
Pour que la base de données secondaire soit utilisable en tant que base de données en lecture seule (secondaire en ligne) ou copie de la base de données viable en cas de basculement, la base de données secondaire doit avoir la configuration de sécurité appropriée .

Seul l’administrateur du serveur peut mener à bien avec succès toutes les étapes décrites ultérieurement dans cette rubrique. Les autorisations spécifiques de chaque étape sont décrites plus tard dans cette rubrique.

La préparation de l’accès utilisateur à un actif géo-réplication en ligne secondaire peut se faire à tout moment. L’opération implique les étapes décrites ci-dessous :

1. Déterminez les connexions ayant accès à la base de données principale.
2. Recherchez le SID de ces connexions sur le serveur source.
3. Générez les connexions sur le serveur cible avec le SID correspondant à partir du serveur source.

#### 1\. Déterminez les connexions ayant accès à la base de données principale :
La première étape du processus consiste à déterminer les noms de connexion qui doivent être dupliqués sur le serveur cible. Pour ce faire, vous devez disposer d’une paire d’instructions SELECT, l’une dans la base de données master logique sur le serveur source et l’autre dans la base de données principale elle-même.

Seuls l’administrateur du serveur ou un membre avec le rôle serveur **LoginManager** peut déterminer les connexions sur le serveur source avec l’instruction SELECT suivante.

	SELECT [name], [sid] 
	FROM [sys].[sql_logins] 
	WHERE [type_desc] = 'SQL_Login'

Seul un membre du rôle db\_owner, l’utilisateur dbo ou l’administrateur du serveur peuvent déterminer toutes les entités de base de données utilisateur dans la base de données principale.

	SELECT [name], [sid]
	FROM [sys].[database_principals]
	WHERE [type_desc] = 'SQL_USER'

#### 2\. Recherchez SID pour les connexions identifiées à l’étape 1 :
En comparant le résultat des requêtes issues de la section précédente et en faisant correspondre les SID, vous pouvez mapper la connexion serveur sur l’utilisateur de base de données. Les connexions dont l’utilisateur de base de données correspond au SID ont un accès utilisateur à cette base de données en tant que qu’utilisateur principal de base de données.

La requête suivante peut être utilisée pour voir toutes les entités utilisateur et leur SID dans une base de données. Seul un membre du rôle de base de données db\_owner ou un administrateur serveur peut exécuter cette requête.

	SELECT [name], [sid]
	FROM [sys].[database_principals]
	WHERE [type_desc] = 'SQL_USER'

>[AZURE.NOTE]Les utilisateurs de **INFORMATION\_SCHEMA** et **sys** ont des SID *NULL*, et le SID **invité** est **0x00**. Le SID **dbo** peut commencer par *0x01060000000001648000000000048454*, si le créateur de la base de données est l’administrateur serveur et non un membre de **DbManager**.

#### 3\. Générer les connexions sur le serveur cible :
La dernière étape consiste à accéder au(x) serveur(s) cible, et à générer les connexions avec les SID appropriés. La syntaxe de base est la suivante :

	CREATE LOGIN [<login name>]
	WITH PASSWORD = <login password>,
	SID = <desired login SID>

>[AZURE.NOTE]Si vous souhaitez accorder un accès utilisateur à la base de données secondaire, mais pas au serveur principal, vous pouvez le faire en modifiant la connexion de l’utilisateur sur le serveur principal à l’aide de la syntaxe suivante.
>
>ALTER LOGIN <login name> DISABLE
>
>DISABLE ne modifie pas le mot de passe, pour que vous puissiez toujours l’activer si nécessaire.

## Configurer l’accès utilisateur à la fin d’une relation de copie continue
En cas de basculement, la relation de copie continue entre le serveur principal et les serveurs secondaires doit être interrompue. Pour plus d’informations sur ce processus, consultez [Récupérer une base de données SQL Azure après une défaillance ](sql-database-disaster-recovery.md).

Dans le cas d’une géo-réplication Standard, l’utilisateur ne peut pas accéder à la base de données secondaire hors connexion. Les modifications aux comptes d’utilisateur doivent être apportées lorsqu’il est mis fin à la relation de copie continue.

Si le SID de connexion n’est pas dupliqué sur le serveur cible, l’accès à la base de données secondaire après l’arrêt est limité au seul administrateur de serveur. Si l’utilisateur qui lance la réplication est un DbManager, il n’aura pas accès à la base de données secondaire, sauf si son SID de connexion est dupliqué à partir du serveur source. Cela reste vrai pendant la durée de vie du processus de réplication.

Lorsque la réplication est terminée, dans le cadre du processus d’arrêt, l’utilisateur [dbo] principal et modifié pour correspondre au SID de connexion de l’utilisateur qui a initié la réplication et cet utilisateur récupère son accès, contrairement aux autres utilisateurs de base de données.

Le compte d’utilisateur et de la connexion associée utilisée pour lancer l’opération d’arrêt doivent être présents sur le serveur cible et la base de données pour que vous puissiez vous assurer que le compte d’utilisateur ne peut accéder à la base de données secondaire après que la fin.

Pour plus d’informations sur les opérations à exécuter après le basculement, consultez [Finaliser votre base de données SQL Azure récupérée](sql-database-recovered-finalize.md).

## Étapes suivantes
Pour plus d’informations sur les fonctionnalités de géo-réplication et de poursuite d’activité supplémentaire de la base de données SQL, consultez [Vue d’ensemble de la continuité des activités métier](sql-database-business-continuity.md).

<!---HONumber=Oct15_HO4-->