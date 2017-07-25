---
title: "Procédure de configuration des paramètres de serveur dans Azure Database pour MySQL | Microsoft Docs"
description: "Cet article décrit comment configurer les paramètres de serveur disponibles dans Azure Database pour MySQL à l’aide du portail Azure."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 06/19/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 718bbf359253849fbc989c563ffd6d1099f92348
ms.contentlocale: fr-fr
ms.lasthandoff: 06/30/2017

---

# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>Procédure de configuration des paramètres de serveur dans Azure Database pour MySQL

Azure Database pour MySQL prend en charge la configuration de certains paramètres de serveur. Cet article explique comment configurer ces paramètres à l’aide du portail Azure et répertorie les paramètres pris en charge, les valeurs par défaut et la plage de valeurs valides. Les paramètres du serveur ne sont pas tous modifiables. Seuls ceux qui sont répertoriés ci-dessous sont pris en charge.

## <a name="navigate-to-server-parameters-blade-on-azure-portal"></a>Accédez au panneau Paramètres du serveur sur le portail Azure

Connectez-vous au portail Azure, puis cliquez sur le nom de votre serveur Azure Database pour MySQL. Sous la section **PARAMÈTRES**, cliquez sur **Paramètres du serveur** afin d’ouvrir le panneau Paramètres du serveur correspondant à Azure Database pour MySQL.

![Panneau Paramètres du serveur du portail Azure](./media/howto-server-parameters/auzre-portal-server-parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Liste des paramètres de serveur configurables

Le tableau suivant répertorie les paramètres de serveur actuellement pris en charge. Les paramètres peuvent être configurés en fonction des exigences de votre application.

> [!div class="mx-tableFixed"]
|Nom du paramètre|Valeur par défaut|Plage|Description|
|---|---|---|---|
|binlog_group_commit_sync_delay|1 000|0, 11-1000000|Contrôle la durée en microsecondes de l’attente de la validation du journal binaire avant la synchronisation du fichier journal binaire sur le disque.|
|binlog_group_commit_sync_no_delay_count|0|0-1000000|Nombre maximal de transactions à attendre avant d’abandonner le délai actuel tel que spécifié par binlog-group-commit-sync-delay.|
|character_set_server|LATIN1|BIG5, UTF8MB4, etc.|Utilisez charset_name en tant que jeu de caractères du serveur par défaut.|
|div_precision_increment|4|0-30|Nombre de chiffres de l’augmentation de l’échelle du résultat des opérations de division.|
|group_concat_max_len|1 024|4-16777216|Longueur de résultat maximale autorisée en octets pour GROUP_CONCAT().|
|innodb_adaptive_hash_index|ACTIVÉ|ON, OFF|Indique si les index de hachage adaptatifs innodb sont activés ou désactivés.|
|innodb_lock_wait_timeout|50|1-3600|Durée en secondes pendant laquelle une transaction InnoDB attend un verrou de ligne avant abandon.|
|interactive_timeout|1 800|10-1800|Nombre de secondes pendant lesquelles le serveur attend une quelconque activité sur une connexion interactive avant sa fermeture.|
|log_bin_trust_function_creators|ÉTEINT|ON, OFF|Cette variable s’applique lorsque la journalisation binaire est activée. Elle contrôle le fait que les créateurs de fonction stockée ne créent pas de fonctions stockées entraînant l’écriture d’événements non sécurisés dans le journal binaire.|
|log_queries_not_using_indexes|ÉTEINT|ON, OFF|Enregistre les requêtes attendues pour récupérer toutes les lignes dans le journal des requêtes lentes.|
|log_slow_admin_statements|ÉTEINT|ON, OFF|Inclue les instructions d’administration lente dans les instructions du journal des requêtes lentes.|
|log_throttle_queries_not_using_indexes|0|0-4294967295|Limite le nombre de requêtes de ce type par minute qui peuvent être écrites dans le journal des requêtes lentes.|
|long_query_time|10|1-1E+100|Si une requête dure plus longtemps que le nombre de secondes indiqué, le serveur incrémente la variable d’état Slow_queries.|
|max_allowed_packet|536870912|1024-1073741824|Taille maximale d’un seul paquet ou de n’importe quelle chaîne intermédiaire/générée ou n’importe quel paramètre envoyé par la fonction d’API C mysql_stmt_send_long_data().|
|min_examined_row_limit|0|0-18446744073709551615|Enregistre les requêtes dont le nombre de lignes est supérieur au nombre de lignes configuré dans le journal des requêtes lentes. |
|server_id|3293747068|1000-4294967295|ID de serveur, utilisé dans la réplication pour conférer à chaque maître et chaque subordonné une identité unique.|
|slave_net_timeout|60|30-3600|Nombre de secondes d’attente pour l’obtention de données supplémentaires à partir du maître avant que le subordonné ne considère la connexion comme interrompue, abandonne la lecture et tente de se reconnecter.|
|slow_query_log|ÉTEINT|ON, OFF|Activez ou désactivez le journal des requêtes lentes.|
|sql_mode|0 sélectionné|ALLOW_INVALID_DATES, IGNORE_SPACE, etc.|Mode SQL du serveur en cours.|
|time_zone|SYSTEM|exemples : -8:00, +05:30|Fuseau horaire du serveur.|
|wait_timeout|120|60-240|Nombre de secondes pendant lesquelles le serveur attend une quelconque activité sur une connexion non interactive avant sa fermeture.|

## <a name="next-steps"></a>Étapes suivantes
- [Bibliothèques de connexions de la base de données Azure pour MySQL](concepts-connection-libraries.md)

