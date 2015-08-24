<properties
	pageTitle="Limites de ressources de base de données SQL Azure"
	description="Cette page décrit certaines limites de ressources courantes pour une base de données SQL Azure."
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
	ms.date="08/10/2015"
	ms.author="jroth" />


# Limites de ressources de base de données SQL Azure


Base de données SQL Azure surveille l’utilisation de nombreuses ressources partagées, telles que le journal des transactions, les E/S, entre autres. Cela permet à la base de données SQL Azure de conserver des bases de données au sein des limites définies des ressources. Cette limite ou ce seuil de ressource est appelé limite de ressource. Si l’utilisation des ressources par les clients dépasse ces limites, au niveau d’un nœud physique ou d’un client, la base de données SQL Azure répond en gérant l’utilisation des ressources, ce qui entraîne des pertes de connexion ou des refus de demande.

> [AZURE.NOTE]Si les limites de ressources empêchent les requêtes d’analyser les problèmes de performance de base de données, vous devrez peut-être utiliser la connexion administrateur dédiée (DAC), qui est disponible depuis la base de données SQL Azure version 12. Pour plus d’informations sur l’utilisation de la connexion DAC, consultez [Connexion de diagnostic pour les administrateurs de base de données](https://msdn.microsoft.com/library/ms189595.aspx).

## Tableau de synthèse des limites de ressources

Le tableau suivant présente un résumé des limites de chaque ressource au-delà desquelles la base de données SQL Azure refuse des demandes ou met fin aux connexions à la ressource concernée, alors qu’un code d’erreur est retourné. Dans certains cas, le [niveau de service (De base, Standard, Premium)](sql-database-service-tiers.md) et le niveau de performances déterminent la limite exacte. Dans ces cas, consultez [Niveaux de service et de performance de Base de données SQL Azure](https://msdn.microsoft.com/library/azure/dn741336.aspx).

[AZURE.INCLUDE [azure-sql-database-limits](../../includes/azure-sql-database-limits.md)]

## Présentation des codes d’erreur

Parfois, un même code d’erreur est retourné pour plusieurs conditions de limitation d’une ressource. Ces conditions sont identifiées comme des états dans le message d’erreur. Par exemple, les deux messages d’erreur suivants sont affichés pour la ressource Longueur du journal des transactions. Même si le texte du message est identique, ils ont différentes valeurs d’état (1 et 2 respectivement) en fonction des différentes conditions de limitation.

Message d’erreur 1 :

	Msg 40552, Level 17, State 1, Line 1
	The session has been terminated because of excessive transaction log space usage.
	Try modifying fewer rows in a single transaction.

Message d’erreur 2 :

	Msg 40552, Level 17, State 2, Line 1
	The session has been terminated because of excessive transaction log space usage.
	Try modifying fewer rows in a single transaction.

La même situation s’applique à l’ID de la ressource qui est affiché dans certains messages. L’ID de la ressource est traduit en ressource système qui rencontre cette limite.

Le reste de cette rubrique décrit en détail les codes d’erreur possibles, y compris les instances où la valeur d’état et l’ID de la ressource fournissent des informations supplémentaires sur l’erreur.

## Taille de la base de données

| &nbsp; | Informations complémentaires |
| :--- | :--- |
| **Condition** | Lorsque l’espace alloué à une base de données utilisateur est plein, l’utilisateur reçoit une erreur de base de données complète. |
| **Code d'erreur** | **40544** : la base de données a atteint son quota de taille. Partitionnez ou supprimez des données, supprimez des index ou consultez la documentation pour connaître les résolutions possibles. |
| **Limite** | Dépend du [niveau de service et du niveau de performances](https://msdn.microsoft.com/library/azure/dn741336.aspx). |
| **Type de demande refusée** | DML Non-Select (Insert, Update, Merge qui effectuent des insertions ou des mises à jour). |
| **Recommandation** | Utilisez les instructions DELETE/DROP pour supprimer des données de la base de données jusqu’à ce que sa taille soit inférieure à la limite. |

## Connexions

| &nbsp; | Informations complémentaires |
| :--- | :--- |
| **Condition** | Base de données SQL régit la limite du nombre de connexions simultanées qui peuvent être établies avec une base de données. Lorsque la limite des connexions simultanées est atteinte pour une base de données, les nouvelles demandes de connexion à la base de données sont refusées, et le code d’erreur 10928 est retourné. |
| **Code d'erreur** | **10928** : ID de la ressource : 3. %d, la limite %s de la base de données a été atteinte. Consultez http://go.microsoft.com/fwlink/?LinkId=267637 pour obtenir une assistance. |
| **Limite** | Dépend du [niveau de service et du niveau de performances](https://msdn.microsoft.com/library/azure/dn741336.aspx). |
| **Recommandation** | Vérifiez dm\_exec\_connections pour afficher les connexions utilisateur actives actuellement.<br><br>Interrompez la connexion et réessayez de vous connecter après 10 secondes. |

> [AZURE.NOTE]La valeur d’ID de la ressource contenue dans le message d’erreur indique la ressource dont la limite a été atteinte. Pour les connexions, l’ID de la ressource = 3.

## Utilisation de la mémoire

| &nbsp; | Informations complémentaires |
| :--- | :--- |
| **Condition** | Lorsque des sessions attendent des allocations de mémoire pendant 20 secondes ou plus, les sessions consommant plus de 16 Mo de mémoire pendant plus de 20 secondes sont arrêtées dans l’ordre décroissant du temps de conservation de la ressource. La plus ancienne session est donc arrêtée en premier. L’arrêt des sessions s’arrête dès que la mémoire requise est disponible. |
| **Code d'erreur** | **40553** : la session a été arrêtée en raison d’une utilisation excessive de la mémoire. Essayez de modifier votre requête pour traiter moins de lignes. |
| **Limite** | Allocation de plus de 16 Mo de mémoire pendant plus de 20 secondes. |
| **Type de demande refusée** | Requêtes consommant des allocations de mémoire, notamment les requêtes qui utilisent des tris et jointures hachées. |
| **Recommandation** | Effectuez le réglage des requêtes qui nécessitent des tris et/ou jointures hachées. |

## Sessions

| &nbsp; | Informations complémentaires |
| :--- | :--- |
| **Condition** | Base de données SQL régit la limite du nombre de sessions simultanées qui peuvent être établies dans une base de données. Lorsque la limite de sessions simultanées est atteinte pour une base de données, les nouvelles connexions à la base de données sont refusées, et l’utilisateur reçoit le code d’erreur 10928. Toutefois, les sessions existant dans la base de données ne sont pas terminées. |
| **Code d'erreur** | **10928** : ID de la ressource : 2. %d, la limite %s de la base de données a été atteinte. Consultez http://go.microsoft.com/fwlink/?LinkId=267637 pour obtenir une assistance. |
| **Limite** | Dépend du [niveau de service et du niveau de performances](https://msdn.microsoft.com/library/azure/dn741336.aspx). |
| **Recommandation** | Vérifiez dm\_exec\_requests pour afficher les demandes utilisateur en cours d’exécution. |

> [AZURE.NOTE]La valeur d’ID de la ressource contenue dans le message d’erreur indique la ressource dont la limite a été atteinte. Pour les sessions, l’ID de la ressource = 2.

## Tempdb

| &nbsp; | Informations complémentaires |
| :--- | :--- |
| **Condition** | Vos demandes dans tempdb peuvent être refusées en raison d’une des trois conditions suivantes :<br><br>\*\*État 1 :\*\* lorsqu’une session utilise un espace tempdb de plus de 5 Go, la session est terminée.<br><br>\*\*État 2 : \*\* les transactions effectuées dans tempdb avec des journaux d’une taille supérieure à 2 Go sont tronquées. Exemples d’opérations qui peuvent consommer l’espace réservé aux journaux dans tempdb : insertion, mise à jour, suppression, fusion, création d’index.<br><br>\*\*État 3 : \*\* les transactions non validées dans tempdb peuvent bloquer la troncation des fichiers journaux. Pour éviter ce problème, la distance entre le plus ancien numéro séquentiel dans le journal (LSN) des transactions actif et la fin du journal (numéro LSN actuel) dans tempdb ne peut pas dépasser 20 % de la taille du fichier journal. En cas de violation, la transaction incriminée dans tempdb est arrêtée et annulée pour que le journal puisse être tronqué. |
| **Code d'erreur** | **40551** : la session a été arrêtée en raison de l’utilisation excessive de tempdb. Essayez de modifier votre requête pour réduire l’espace utilisé par la table temporaire. |
| **Limite** | **État 1 :** espace tempdb de 5 Go<br><br>**État 2 :** 2 Go par transaction dans tempdb <br><br>\*\*État 3 : \*\* 20 % de l’espace total réservé aux journaux dans tempdb |
| **Type de demande refusée** | Toutes les instructions DDL ou DML exécutées dans tempdb. |
| **Recommandation** | Modifiez des requêtes pour réduire l’espace utilisé par la table temporaire, supprimez les objets temporaires lorsqu’ils ne sont plus nécessaires, tronquez des tables ou supprimez des tables inutilisées. Réduisez la taille des données de votre transaction dans tempdb en diminuant le nombre de lignes ou en fractionnant l’opération en plusieurs transactions. |

## Durée de la transaction

| &nbsp; | Informations complémentaires |
| :--- | :--- |
| **Condition** | Les transactions demandent des verrous sur les ressources telles que les lignes, les pages ou les tables dont elles dépendent, puis libèrent les verrous lorsqu’elles n’ont plus de dépendance dans les ressources verrouillées. Vos demandes peuvent être refusées en raison des deux conditions suivantes : État 1 : si une transaction a été exécutée pendant plus de 24 heures, elle est arrêtée. État 2 : si une transaction verrouille une ressource requise par une tâche système sous-jacente pendant plus de 20 secondes, elle est arrêtée. |
| **Code d'erreur** | **40549** : la session est arrêtée, car l’une des transactions est de longue durée. Essayez de la raccourcir. |
| **Limite** | **État 1: **24 heures<br><br>\*\*État 2 :\*\* 20 secondes si une transaction verrouille une ressource requise par une tâche système sous-jacente. |
| **Type de demande refusée** | Toute transaction dont l’exécution dure plus de 24 heures ou toute instruction DDL ou DML qui accepte un verrou, ce qui entraîne le blocage d’une tâche système. |
| **Recommandation** | Les opérations effectuées dans la base de données SQL ne doivent pas bloquer sur les entrées utilisateur ni avoir d’autres dépendances entraînant des transactions de longue durée. |

## Nombre de verrous de transaction

| &nbsp; | Informations complémentaires |
| :--- | :--- |
| **Condition** | Les sessions consommant plus d’un million de verrous sont arrêtées. |
| **Code d'erreur** | **40550** : la session a été arrêtée, car elle a acquis trop de verrous. Essayez de lire ou de modifier moins de lignes dans une transaction unique. |
| **Limite** | 1 million de verrous par transaction |
| **Type de demande refusée** | Toutes les instructions DDL ou DML. |
| **Recommandation** | Les DMV suivantes permettent de surveiller les transactions : **sys.dm\_tran\_active\_transactions**, **sys.dm\_tran\_database\_transactions**, **sys.dm\_tran\_locks** et **sys.dm\_tran\_session\_transactions**. En fonction du type d’application, il est possible d’utiliser des indicateurs de verrou de plus grande ampleur, comme **PAGLOCK** ou **TABLOCK**, pour réduire le nombre de verrous dans une instruction/transaction donnée. Notez que cela peut avoir des effets négatifs sur l’accès concurrentiel aux applications. |


## Longueur de journal de transactions


Pour la version 12 de la base de données SQL au tarif Standard ou Premium, la longueur maximale du journal des transactions n'est plus limitée à 2 Go.

Le tableau suivant décrit la limite du journal des transactions pour la version 11.


| &nbsp; | Informations complémentaires |
| :--- | :--- |
| **Condition** | Vos demandes peuvent être refusées en raison d’une des deux conditions suivantes :<br><br>\*\*État 1 :\*\* Base de données SQL prend en charge les transactions générant un journal dont la taille peut atteindre 2 Go. Les transactions dont les journaux dépassent cette limite sont tronquées. Exemples d’opérations qui peuvent consommer l’espace réservé aux journaux dans ce volume : insertion, mise à jour, suppression, fusion, création d’index.<br><br>\*\*État 2 :\*\* les transactions non validées peuvent bloquer la troncation des fichiers journaux. Pour éviter ce problème, la distance entre le plus ancien numéro séquentiel dans le journal (LSN) des transactions actif et la fin du journal (numéro LSN actuel) ne peut pas dépasser 20 % de la taille du fichier journal. En cas de violation, la transaction incriminée est arrêtée et annulée pour que le journal puisse être tronqué. |
| **Code d'erreur** | **40552** : la session a été arrêtée en raison de l’utilisation excessive de l’espace réservé au journal des transactions. Essayez de modifier moins de lignes dans une transaction unique. |
| **Limite** | **État 1 :** 2 Go par transaction<br><br>\*\*État 2 :\*\* 20 % de l’espace total réservé aux journaux |
| **Type de demande refusée** | Toutes les instructions DDL ou DML. |
| **Recommandation** | Pour les opérations effectuées au niveau des lignes, réduisez la taille des données de votre transaction, par exemple en diminuant le nombre de lignes ou en fractionnant l’opération en plusieurs transactions. Pour les opérations effectuées au niveau des tables/index, qui requièrent une transaction unique, vérifiez que la formule suivante est respectée : nombre de lignes affectées dans la table \* (taille moyenne du champ mis à jour en octets + 80) < 2 Go (en cas de reconstruction d’index, la taille moyenne du champ mis à jour doit être remplacée par la taille moyenne de l’index). |

## Threads de travail (nombre maximal de demandes simultanées)

| &nbsp; | Informations complémentaires |
| :--- | :--- |
| **Condition** | Base de données SQL régit la limite du nombre de threads de travail (demandes simultanées) dans une base de données. Les bases de données dont le nombre de demandes simultanées est supérieur à la limite autorisée reçoivent l’erreur 10928. Les demandes ultérieures dans cette base de données peuvent alors être refusées. |
| **Codes d’erreur** | **10928** : ID de la ressource : 1. %d, la limite %s de la base de données a été atteinte. Consultez http://go.microsoft.com/fwlink/?LinkId=267637 pour obtenir une assistance.<br><br>\*\*10929\*\* : ID de la ressource : 1. La garantie minimale de %s est %d ; la limite maximale est de %d et le taux d’utilisation actuel de la base de données est de %d. Toutefois, le serveur est trop occupé pour prendre en charge les requêtes supérieures à %d pour cette base de données. Consultez http://go.microsoft.com/fwlink/?LinkId=267637 pour obtenir une assistance. Sinon, réessayez plus tard. |
| **Limite** | Pour les niveaux De base, Standard et Premium, elle dépend du [niveau de performances](https://msdn.microsoft.com/library/azure/dn741336.aspx). Pour les anciennes bases de données web/professionnelles, le nombre maximal de demandes simultanées est 180, et il peut être inférieur en fonction de l’activité du système. |
| **Recommandation** | Vérifiez dm\_exec\_requests pour afficher les demandes utilisateur en cours d’exécution.<br><br>Arrêtez la demande et renouvelez-la après 10 secondes. |

> [AZURE.NOTE]La valeur d’ID de la ressource contenue dans le message d’erreur indique la ressource dont la limite a été atteinte. Pour les threads de travail, ID de la ressource = 1.

Les erreurs de gouvernance des threads de travail (10928/10929) remplacent l’erreur (40501) de limitation du moteur d’origine pour les threads de travail. Dans des conditions normales, les utilisateurs ne doivent plus recevoir d’erreur de limitation du moteur pour les threads de travail.

Dans certains scénarios tels que l’utilisation de la fonctionnalité de base de données fédérée, il est possible d’atteindre l’erreur de plafonnement des threads de travail (10928) lors de la connexion à une base de données, car cette opération utilise un thread de travail au-dessous d’un appel Connection.Open. Cela peut placer l’application au-dessus du seuil plafond des threads de travail. Les applications doivent disposer d’une logique intégrée pour gérer cette erreur de façon appropriée dans de tels cas.

## Voir aussi

[Gestion des ressources de base de données SQL Azure](https://msdn.microsoft.com/library/azure/dn338083.aspx)

[Messages d'erreur pour les programmes clients SQL Database](sql-database-develop-error-messages.md)

[Recommandations liées à Base de données SQL Azure pour empêcher les refus de demande ou l’arrêt des connexions](https://msdn.microsoft.com/library/azure/dn338082.aspx)

<!---HONumber=August15_HO7-->