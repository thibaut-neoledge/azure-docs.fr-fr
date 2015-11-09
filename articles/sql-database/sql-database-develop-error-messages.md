<properties 
	pageTitle="Messages d'erreur pour les programmes clients SQL Database"
	description="Pour chaque erreur, indique l'ID numérique et le message textuel. N'hésitez pas à fournir un texte de message d'erreur plus convivial si vous le souhaitez."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor="" />


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/26/2015" 
	ms.author="genemi"/>


# Messages d'erreur pour les programmes clients SQL Database


<!--
Old Title on MSDN:  Error Messages (Azure SQL Database)
ShortId on MSDN:  ff394106.aspx
Dx 4cff491e-9359-4454-bd7c-fb72c4c452ca
-->


Cette rubrique répertorie les différentes catégories de messages d'erreur. La plupart des catégories sont spécifiques à Azure SQL Database et ne s'appliquent pas à Microsoft SQL Server.


Dans votre programme client, vous avez la possibilité d’offrir à votre utilisateur un message de remplacement personnalisé pour une erreur donnée.


**Conseil :** il est fortement recommandé de consulter la section consacrée aux *erreurs temporaires*. Ces erreurs devraient inviter le programme client à exécuter la logique de *nouvelle tentative* que vous concevez pour relancer l'opération.


<a id="bkmk_connection_errors" name="bkmk_connection_errors">&nbsp;</a>


## Erreurs temporaires, perte de connexion et autres erreurs temporaires

Le tableau suivant décrit les erreurs de perte de connexion et autres erreurs temporaires que vous pouvez rencontrer lors de l'utilisation d’Azure SQL Database via Internet.

Les erreurs temporaires sont également appelées des défaillances temporaires. Lorsque votre programme détecte une `SqlException`, il peut vérifier si la valeur `sqlException.Number` est une valeur répertoriée dans cette section des erreurs temporaires. Si la valeur `Number` indique une erreur temporaire, votre programme peut essayer de rétablir une connexion, puis relance l'interrogation via la connexion. Pour obtenir des exemples de code de logique de nouvelle tentative, voir :


- [Développement de clients et échantillons de code de démarrage rapide pour une base de données SQL](sql-database-develop-quick-start-client-code-samples.md)

- [Procédure : connexion fiable à Azure SQL Database](http://msdn.microsoft.com/library/azure/dn864744.aspx)


| Numéro d'erreur | Niveau de gravité | Description |
| ---: | ---: | :--- |
| 4060 | 16 | Impossible d'ouvrir de base de données "%.&#x2a;ls" demandée par la connexion. La connexion a échoué. |
|40197|17|Le service a rencontré une erreur lors du traitement de votre demande. Réessayez. Code d’erreur % d.<br/><br/>Vous recevez cette erreur lorsque le service est arrêté en raison de mises à niveau logicielles ou matérielles, de pannes de matériel ou tout autre problème de basculement. Le code d'erreur (%d) incorporé au message d'erreur 40197 fournit des informations supplémentaires sur le type de défaillance ou de basculement survenu. 40020, 40143, 40166 et 40540 sont des exemples de codes d’erreur incorporés au message d’erreur 40197.<br/><br/>La reconnexion à votre serveur de base de données SQL vous reconnectera automatiquement à une copie saine de votre base de données. Votre application doit détecter l'erreur 40197, consigner le code d'erreur incorporé (%d) dans le message pour la résolution des problèmes, et essayer de se reconnecter à la base de données SQL jusqu'à ce que les ressources soient disponibles et que votre connexion soit rétablie.|
|40501|20|Le service est actuellement occupé. Relancez la demande dans 10 secondes. ID de l'incident : %ls. Code : % d.<br/><br/>*Remarque :* pour plus d’informations sur cette erreur et sa résolution, consultez :<br/>•[Limitation associée à Azure SQL Database](http://msdn.microsoft.com/library/azure/dn338079.aspx).
|40613|17|La base de données ’%.&#x2a;ls’ sur le serveur ’%.&#x2a;ls’ n’est pas disponible actuellement. Veuillez réessayer la connexion ultérieurement. Si le problème persiste, contactez le support technique en indiquant l'ID de suivi de session ’%.&#x2a;ls’’.|
|49918|16|Impossible de traiter la requête. Ressources insuffisantes pour traiter la demande.<br/><br/>Le service est actuellement occupé. Relancez la requête ultérieurement. |
|49919|16|Processus ne peut pas créer ou mettre à jour de la demande. Opérations de mise à jour ou de création en cours pour l’abonnement « % ld » trop nombreuses.<br/><br/>Le service est occupé à traiter plusieurs demandes de création ou de mise à jour pour votre abonnement ou le serveur. Les requêtes sont actuellement bloquées pour l’optimisation des ressources. Requête [sys.dm\_operation\_status](https://msdn.microsoft.com/library/dn270022.aspx) pour les opérations en attente. Patientez jusqu’à ce que les demandes de création ou de mise à jour soient terminées ou supprimez l’une de vos requêtes en cours et réessayez votre requête ultérieurement. |
|49920|16|Impossible de traiter la requête. Opérations en cours pour l’abonnement « % ld » trop nombreuses.<br/><br/>Le service est occupé à traiter plusieurs demandes pour cet abonnement. Les requêtes sont actuellement bloquées pour l’optimisation des ressources. Requête [sys.dm\_operation\_status](https://msdn.microsoft.com/library/dn270022.aspx) pour l’état de l’opération. Patientez jusqu’à ce que les requêtes soient terminées ou supprimez l’une de vos requêtes en cours et réessayez votre requête ultérieurement. |

**Remarque :** les erreurs de fédération 10053 et 10054 peuvent également être incluses dans la logique de votre nouvelle tentative.


<a id="bkmk_b_database_copy_errors" name="bkmk_b_database_copy_errors">&nbsp;</a>

## Erreurs de copie de base de données


Le tableau suivant décrit les différentes erreurs que vous pouvez rencontrer lors de la copie d'une base de données dans Azure SQL Database. Pour plus d'informations, consultez [Copie de bases de données dans Azure SQL Database](http://msdn.microsoft.com/library/azure/ff951624.aspx).


|Numéro d'erreur|Niveau de gravité|Description|
|---:|---:|:---|
|40635|16|Le client avec l'adresse IP’%.&#x2a;ls’ est temporairement désactivé.|
|40637|16|La copie de base de données est actuellement désactivée.|
|40561|16|La copie de base de données a échoué. La base de données source ou cible n'existe pas.|
|40562|16|La copie de base de données a échoué. La base de données source a été supprimée.|
|40563|16|La copie de base de données a échoué. La base de données cible a été supprimée.|
|40564|16|La copie de base de données a échoué en raison d'une erreur interne. Supprimez la base de données cible et réessayez.|
|40565|16|La copie de base de données a échoué. Pas plus d'une copie de base de données simultanée de la même source est autorisée. Supprimez la base de données cible et réessayez ultérieurement.|
|40566|16|La copie de base de données a échoué en raison d'une erreur interne. Supprimez la base de données cible et réessayez.|
|40567|16|La copie de base de données a échoué en raison d'une erreur interne. Supprimez la base de données cible et réessayez.|
|40568|16|La copie de base de données a échoué. La base de données source n'est plus disponible. Supprimez la base de données cible et réessayez.|
|40569|16|La copie de base de données a échoué. La base de données cible n'est plus disponible. Supprimez la base de données cible et réessayez.|
|40570|16|La copie de base de données a échoué en raison d'une erreur interne. Supprimez la base de données cible et réessayez ultérieurement.|
|40571|16|La copie de base de données a échoué en raison d'une erreur interne. Supprimez la base de données cible et réessayez ultérieurement.|


<a id="bkmk_c_resource_gov_errors" name="bkmk_c_resource_gov_errors">&nbsp;</a>

## Erreurs de gouvernance des ressources


Le tableau suivant décrit les erreurs causées par une utilisation excessive des ressources avec Azure SQL Database. Par exemple :


- Votre transaction a peut-être été ouverte trop longtemps.
- Votre transaction comporte peut-être trop de verrous.
- Votre programme consomme peut-être trop de mémoire.
- Votre programme consomme peut-être trop d’espace `TempDb`.


**Conseil :** le lien suivant fournit des informations supplémentaires qui s’appliquent à la plupart voire à toutes les erreurs de cette section :


- [Limites de ressources Azure SQL Database](http://msdn.microsoft.com/library/azure/dn338081.aspx)


|Numéro d'erreur|Niveau de gravité|Description|
|---:|---:|:---|
|10928|20|ID de la ressource : %d. %d, la limite %s de la base de données a été atteinte. Pour plus d’informations, consultez [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>L’ID de ressource indique la ressource qui a atteint la limite. Pour les threads de travail, ID de la ressource = 1. Pour les sessions, l’ID de ressource = 2.<br/><br/>* Remarque :* pour plus d’informations sur cette erreur et sa résolution, consultez :<br/>•[Gouvernance des ressources Azure SQL Database](http://msdn.microsoft.com/library/azure/dn338078.aspx). |
|10929|20|ID de la ressource : %d. La garantie minimale de %s est %d ; la limite maximale est de %d et le taux d’utilisation actuel de la base de données est de %d. Toutefois, le serveur est trop occupé pour prendre en charge les requêtes supérieures à %d pour cette base de données. Pour plus d’informations, consultez [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). Sinon, veuillez réessayer ultérieurement.<br/><br/>L’ID de ressource indique la ressource qui a atteint la limite. Pour les threads de travail, ID de la ressource = 1. Pour les sessions, l’ID de ressource = 2.<br/><br/>* Remarque :* pour plus d’informations sur cette erreur et sa résolution, consultez :<br/>•[Gouvernance des ressources Azure SQL Database](http://msdn.microsoft.com/library/azure/dn338078.aspx).|
|40544|20|La base de données a atteint son quota de taille. Partitionnez ou supprimez des données, supprimez des index ou consultez la documentation pour connaître les résolutions possibles.|
|40549|16|La session est arrêtée, car l’une des transactions est de longue durée. Essayez de la raccourcir.|
|40550|16|La session a été arrêtée, car elle a acquis trop de verrous. Essayez de lire ou de modifier moins de lignes dans une transaction unique.|
|40551|16|La session a été arrêtée en raison de l’utilisation excessive de `TEMPDB`. Essayez de modifier votre requête pour réduire l’espace utilisé par la table temporaire.<br/><br/>*Conseil :* Si vous utilisez des objets temporaires, conservez de l’espace dans la base de données `TEMPDB` en supprimant des objets temporaires une fois qu’ils ne sont plus nécessaires à la session.|
|40552|16|La session a été arrêtée en raison de l’utilisation excessive de l’espace réservé au journal des transactions. Essayez de modifier moins de lignes dans une transaction unique.<br/><br/>*Conseil :* Si vous effectuez des insertions en bloc à l’aide de l’utilitaire `bcp.exe` ou de la classe `System.Data.SqlClient.SqlBulkCopy`, essayez d’utiliser les options `-b batchsize` ou `BatchSize` permettant de limiter le nombre de lignes copiées sur le serveur à chaque transaction. Si vous reconstruisez un index en utilisant l’instruction `ALTER INDEX`, essayez d’utiliser l’option `REBUILD WITH ONLINE = ON`.|
|40553|16|La session a été arrêtée en raison d’une utilisation excessive de la mémoire. Essayez de modifier votre requête pour traiter moins de lignes.<br/><br/>*Conseil :* La diminution du nombre d’opérations `ORDER BY` et `GROUP BY` dans votre code Transact-SQL réduit les besoins en mémoire de votre requête.|


Pour plus d'informations sur la gouvernance des ressources et les erreurs associées, voir :


- [Gouvernance des ressources Azure SQL Database](http://msdn.microsoft.com/library/azure/dn338078.aspx)


<a id="bkmk_d_federation_errors" name="bkmk_d_federation_errors">&nbsp;</a>

## Erreurs de fédération


Le tableau suivant décrit les erreurs que vous pouvez rencontrer lorsque vous travaillez avec des fédérations. Pour plus d’informations, consultez [Gestion des fédérations de bases de données (Azure SQL Database)](http://msdn.microsoft.com/library/azure/hh597455.aspx).


> [AZURE.IMPORTANT]L’implémentation actuelle des fédérations sera retirée en même temps que les niveaux de service Web et Business. La version V12 d’Azure SQL Database ne prend pas en charge les niveaux de service Web et Business.
> 
> La fonctionnalité de mise à l’échelle élastique est conçue pour créer des applications de partitionnement avec un minimum d'effort.
> 
> Pour plus d’informations sur la mise à l’échelle élastique, consultez [Rubriques de mise à l’échelle élastique Azure SQL Database](sql-database-elastic-scale-documentation-map.md). Considérez le déploiement de solutions de partitionnement personnalisées pour optimiser les performances, la flexibilité et l'évolutivité. Pour plus d’informations sur le partitionnement personnalisé, consultez [Montée en puissance parallèle de bases de données SQL Azure](http://msdn.microsoft.com/library/azure/dn495641.aspx).


|Numéro d'erreur|Niveau de gravité|Description|Atténuation|
|---:|---:|:---|:---|
|266|16|Instruction <statement> non autorisée dans une transaction à instructions multiples|Vérifiez que `@@trancount` est égal à 0 lors de la connexion avant d’émettre l’instruction.|
|2072|16|La base de données ’%.&#x2a;ls’ n'existe pas|Consultez `sys.databases` pour connaître l’état de la base de données avant d’émettre `USE FEDERATION`.|
|2209|16|Erreur de syntaxe %s près de ‘%ls’|`FEDERATED ON` peut uniquement servir à la création de tables dans les membres de la fédération.|
|2714|16|Il existe déjà un objet nommé ’%.&#x2a;ls’ dans la base de données|Ce nom de fédération existe déjà.|
|10054, 10053|20|Une erreur de niveau du transport s'est produite lors de la réception des résultats à partir du serveur. Une connexion établie a été abandonnée par le logiciel sur votre ordinateur hôte|Mettez en place la logique de nouvelle tentative dans votre application.|
|40530|15|<statement> doit être la seule instruction du lot|Vérifiez qu'aucune autre instruction ne figure dans le lot|
|40604|16|Opération `CREATE DATABASE` impossible, car elle dépasse le quota du serveur|Augmentez le quota du nombre de bases de données serveur|
|45000|16|L’opération <statement> a échoué. Le nom de fédération spécifié <federation_name> n’est pas valide|Federation\_name ne respecte pas les règles de nom de fédération ou n'est pas un identificateur valide|
|45001|16|L’opération <statement> a échoué. Le nom de fédération spécifié n’existe pas|Le nom de fédération n’existe pas|
|45002|16|L’opération <statement> a échoué. Le nom de la clé de fédération spécifié <distribution_name> n’est pas valide.|Clé de fédération inexistante ou non valide|
|45004|16|L’opération <statement> a échoué. La valeur spécifiée n’est pas valide pour la clé de fédération <distribution_name> et la fédération <federation_name>.|`USE FEDERATION` : utilisez une valeur limite figurant dans le domaine du type de données de la clé de fédération, ou qui n’est pas NULL.<br/><br/>`ALTER FEDERATION SPLIT` : utilisez une valeur valide dans le domaine de la clé de fédération qui n’est pas déjà un point de fractionnement existant.<br/><br/>`ALTER FEDERATION DROP` : utilisez une valeur valide dans le domaine de la clé de fédération qui est déjà un point de fractionnement.|
|45005|16|L’opération <statement> ne peut pas être exécutée si une autre opération de fédération est en cours sur la fédération <federation_name> et le membre don l’id est <member_id>.|Attendez la fin de l'opération exécutée en parallèle.|
|45006|16|Les opérations <statement> ont échoué. Les relations de clé étrangère dans les tables de référence désignant des tables fédérées ne sont pas autorisées dans les membres de la fédération|Non pris en charge.|
|45007|16|L’opération <statement> a échoué. Les relations de clé étrangère entre des tables fédérées doivent inclure les colonnes de clé de fédération.|Non pris en charge|
|45008|16|L’opération <statement> a échoué. Le type de données de clé de fédération ne correspond pas au type de données de la colonne|Non pris en charge.|
|45009|16|L’opération <statement> a échoué. L'opération n'est pas prise en charge sur le filtrage des connexions|Non pris en charge.|
|45010|16|L’opération <statement> a échoué. La clé de fédération ne peut pas être mise à jour|Non pris en charge.|
|45011|16|L’opération <statement> a échoué. Le schéma de la clé de fédération ne peut pas être mis à jour|Non pris en charge.|
|45012|16|La valeur spécifiée pour la clé de fédération n'est pas valide|La valeur doit figurer dans la plage utilisée par la connexion.<br/><br/>Si elle est filtrée, il s’agit de la valeur de la clé de fédération spécifiée.<br/><br/>Si elle est non filtrée, il s’agit de la plage couverte par le membre de la fédération.|
|45013|16|Le SID existe déjà sous un autre nom d'utilisateur|Le SID d'un utilisateur dans un membre de la fédération est copié à partir du SID du même compte utilisateur dans la racine de fédération. Dans certaines conditions, le SID peut déjà être en cours d'utilisation.|
|45014|16|%ls n’est pas pris en charge sur %ls|Opération non prise en charge.|
|45022|16|L’opération <statement> a échoué. La valeur limite spécifiée existe déjà pour la clé de fédération <distribution_name> et la fédération <federation_name>.|Spécifiez une valeur qui est déjà une valeur limite.|
|45023|16|L’opération <statement> a échoué. La valeur limite spécifiée n’existe pas pour la clé de fédération <distribution_name> et la fédération <federation_name>.|Spécifiez une valeur qui n’est pas déjà une valeur limite.|


<a id="bkmk_e_general_errors" name="bkmk_e_general_errors">&nbsp;</a>

## Erreurs générales.


Le tableau suivant répertorie toutes les erreurs générales qui n'appartiennent à aucune des catégories précédentes.


|Numéro d'erreur|Niveau de gravité|Description|
|---:|---:|:---|
|15006|16|<AdministratorLogin> n’est pas un nom valide, car il contient des caractères non valides.|
|18452|14|La connexion a échoué. La connexion provient d'un domaine non approuvé et ne peut pas être utilisée avec l’authentification Windows.%.&#x2a;ls (Les connexions Windows ne sont pas prises en charge dans cette version de SQL Server.)|
|18456|14|Échec de la connexion pour l'utilisateur ’%.&#x2a;ls’.%.&#x2a;ls%.&#x2a;ls(Échec de la connexion pour l’utilisateur "%.&#x2a;ls". La modification du mot de passe a échoué. La modification du mot de passe lors de la connexion n'est pas prise en charge dans cette version de SQL Server.)|
|18470|14|Échec de la connexion pour l'utilisateur '%.&#x2a;ls'. Raison : le compte est désactivé.%.&#x2a;ls|
|40014|16|Plusieurs bases de données ne peuvent pas être utilisées dans la même transaction.|
|40054|16|Les tables sans index de cluster ne sont pas prises en charge dans cette version de SQL Server. Créez un index de cluster et réessayez.|
|40133|15|Cette opération n'est pas prise en charge dans cette version de SQL Server.|
|40506|16|Le SID spécifié n'est pas valide pour cette version de SQL Server.|
|40507|16|'%.&#x2a;ls' ne peut pas être appelé avec des paramètres dans cette version de SQL Server.|
|40508|16|L'instruction USE n'est pas prise en charge pour basculer d'une base de données à une autre. Utilisez une nouvelle connexion pour vous connecter à une autre base de données.|
|40510|16|L'instruction '%.&#x2a;ls' n'est pas prise en charge dans cette version de SQL Server|
|40511|16|La fonction intégrée '%.&#x2a;ls' n'est pas prise en charge dans cette version de SQL Server.|
|40512|16|La fonctionnalité déconseillée '%ls' n'est pas prise en charge dans cette version de SQL Server.|
|40513|16|La variable de serveur '%.&#x2a;ls' n'est pas prise en charge dans cette version de SQL Server.|
|40514|16|'%.ls' n'est pas pris en charge dans cette version de SQL Server.|
|40515|16|La référence au nom de base de données et/ou serveur dans '%.&#x2a;ls' n'est pase pris en charge dans cette version de SQL Server.|
|40516|16|Les objets temporaires globaux ne sont pas pris en charge dans cette version de SQL Server.|
|40517|16|Le mot clé ou l'option d'instruction '%.&#x2a;ls' n'est pas pris en charge dans cette version de SQL Server.|
|40518|16|La commande DBCC '%.&#x2a;ls' n'est pas prise en charge dans cette version de SQL Server.|
|40520|16|La classe sécurisable '%S\_MSG' n'est pas prise en charge dans cette version de SQL Server.|
|40521|16|La classe sécurisable '%S\_MSG' n'est pas prise en charge dans le champ d'application du serveur pour cette version de SQL Server.|
|40522|16|Le type de base de données principal '%.&#x2a;ls' n'est pas pris en charge dans cette version de SQL Server.|
|40523|16|La création de l'utilisateur implicite '%.&#x2a;ls' n'est pas prise en charge dans cette version de SQL Server. Créez explicitement l'utilisateur avant de l'utiliser.|
|40524|16|Le type de données '%.&#x2a;ls' n'est pas pris en charge dans cette version de SQL Server.|
|40525|16|WITH '%.ls' n'est pas en charge cette version de SQL Server.|
|40526|16|Le fournisseur d'ensemble de lignes '%.&#x2a;ls' n'est pris en charge dans cette version de SQL Server.|
|40527|16|Les serveurs liés ne sont pas pris en charge dans cette version de SQL Server.|
|40528|16|Les utilisateurs ne peuvent pas être mappés à des certificats, clés asymétriques ou connexions Windows dans cette version de SQL Server.|
|40529|16|La fonction intégrée '%.&#x2a;ls' dans le contexte d'emprunt d'identité n'est pas prise en charge dans cette version de SQL Server.|
|40532|11|Impossible d'ouvrir le serveur "%.&#x2a;ls" demandé par la connexion. La connexion a échoué.|
|40553|16|La session a été arrêtée en raison d’une utilisation excessive de la mémoire. Essayez de modifier votre requête pour traiter moins de lignes.<br/><br/>*Remarque :* La diminution du nombre d’opérations `ORDER BY` et `GROUP BY` dans votre code Transact-SQL réduit les besoins en mémoire de votre requête.|
|40604|16|Opération CREATE/ALTER DATABASE impossible car elle dépasse le quota du serveur.|
|40606|16|L'association de bases de données n'est pas prise en charge dans cette version de SQL Server.|
|40607|16|Les connexions Windows ne sont pas prises en charge dans cette version de SQL Server.|
|40611|16|Les serveurs peuvent avoir au maximum 128 règles de pare-feu définies.|
|40614|16|L'adresse IP de début de la règle de pare-feu ne peut pas dépasser l'adresse IP de fin.|
|40615|16|Impossible d'ouvrir le serveur '{0}' demandé par la connexion. Le client avec l'adresse IP '{1}' n'est pas autorisé à accéder au serveur. Pour activer l’accès, utilisez le portail de la base de données SQL ou exécutez l’élément sp\_set\_firewall\_rule sur la base de données master afin de créer une règle de pare-feu pour cette adresse IP ou cette plage d'adresses. Cela peut prendre jusqu’à cinq minutes pour que cette modification prenne effet.|
|40617|16|Le nom de la règle de pare-feu qui commence par <rule name> est trop long. La longueur maximale est 128.|
|40618|16|Le nom de la règle de pare-feu ne peut pas être vide.|
|40620|16|Échec de la connexion pour l'utilisateur "%.&#x2a;ls". La modification du mot de passe a échoué. La modification du mot de passe lors de la connexion n'est pas prise en charge dans cette version de SQL Server.|
|40627|20|L'opération sur le serveur '{0}' et la base de données '{1}' est en cours. Veuillez patienter quelques minutes avant de réessayer.|
|40630|16|Échec de la validation de mot de passe. Le mot de passe ne respecte pas les exigences de la stratégie car il est trop court.|
|40631|16|Le mot de passe que vous avez spécifié est trop long. Le mot de passe doit avoir au maximum 128 caractères.|
|40632|16|Échec de la validation de mot de passe. Le mot de passe ne respecte pas les exigences de la stratégie car il n'est pas assez complexe.|
|40636|16|Impossible d'utiliser un nom de base de données réservé '%.&#x2a;ls' dans cette opération.|
|40638|16|ID d’abonnement <subscription-id> non valide. L'abonnement n'existe pas.|
|40639|16|La requête n’est pas conforme au schéma : <schema error>.|
|40640|20|Le serveur a rencontré une exception inattendue.|
|40641|16|L'emplacement spécifié n'est pas valide.|
|40642|17|Le serveur est actuellement trop occupé. Veuillez réessayer plus tard.|
|40643|16|La valeur d'en-tête x-ms-version spécifiée n'est pas valide.|
|40644|14|Impossible d'autoriser l'accès à l'abonnement spécifié.|
|40645|16|Le nom de serveur <servername> ne peut pas être vide ou nul. Il peut uniquement contenir les lettres minuscules 'a' à 'z', les chiffres 0 à 9 et le trait d'union. Le trait d'union ne peut pas figurer au début ou à la fin du nom.|
|40646|16|L'ID d'abonnement ne peut pas être vide.|
|40647|16|L'abonnement <subscription-id ne contient pas de nom de serveur.|
|40648|17|Trop de demandes ont été effectuées. Veuillez réessayer ultérieurement.|
|40649|16|Un type de contenu non valide est spécifié. Seul application/xml est pris en charge.|
|40650|16|L’abonnement <subscription-id> n’existe pas ou n’est pas prêt pour l’opération.|
|40651|16|Impossible de créer le serveur, car l’abonnement <subscription-id> est désactivé.|
|40652|16|Impossible de déplacer ou de créer le serveur. L’abonnement <subscription-id> va dépasser le quota du serveur.|
|40671|17|Échec de la communication entre la passerelle et le service de gestion. Veuillez réessayer ultérieurement.|
|45168|16|Le système Azure SQL est en cours de chargement et place une limite supérieure sur les opérations DB CRUD simultanées pour un serveur unique (par exemple, créer la base de données). Le serveur spécifié dans le message d'erreur a dépassé le nombre maximal de connexions simultanées. Réessayez ultérieurement.|
|45169|16|Le système Azure SQL est en cours de chargement et place une limite supérieure sur les opérations CRUD simultanées pour un abonnement unique (par exemple, créer le serveur). L'abonnement spécifié dans le message d'erreur a dépassé le nombre maximal de connexions simultanées, et la demande a été rejetée. Réessayez ultérieurement.|


## Liens connexes

- [Consignes et limitations générales d’Azure SQL Database](http://msdn.microsoft.com/library/azure/ee336245.aspx)
- [Gestion des ressources](http://msdn.microsoft.com/library/azure/dn338083.aspx)

<!---HONumber=Nov15_HO1-->