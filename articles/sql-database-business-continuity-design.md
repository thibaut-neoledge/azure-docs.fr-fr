<properties 
   pageTitle="Conception de base de données SQL pour la continuité des activités" 
   description="Conseils sur le choix Dans cette section, vous bénéficierez de conseils sur le choix des fonctionnalités BCDR à utiliser et quand. Vous y trouverez, entre autres, des descriptions de ce que vous obtenez automatiquement en utilisant une base de données SQL."
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/13/2015"
   ms.author="elfish"/>

#Conception pour la continuité des activités

La conception de votre application pour la continuité des activités nécessite que vous répondiez aux questions suivantes :

1. Quelle fonctionnalité de continuité des activités est appropriée pour protéger mon application contre les pannes ?
2. Quel niveau de topologie de réplication et de redondance utiliser ?

##Utilisation de la géo-restauration

La base de données SQL fournit une protection intégrée de base de chaque base de données par défaut. Cette protection s'effectue par le stockage des sauvegardes de bases de données dans le stockage Azure redondant  au niveau géographique (GRS). Si vous choisissez cette méthode, aucune configuration particulière ni allocation de ressources supplémentaire n'est nécessaire. Grâce à ces sauvegardes, vous pouvez récupérer votre base de données dans n'importe quelle région à l'aide de la commande de géo-restauration. Utilisez la section [Récupération après une panne](sql-database-disaster-recovery.md) pour obtenir des détails sur l'utilisation de la géo-restauration pour restaurer votre application.

Nous vous recommandons d'utiliser la protection intégrée si votre application répond aux critères suivants :

1. Elle n'est pas essentielle. Elle n'a pas de contrat SLA contraignant. Donc, l'interruption de service de 24 heures ou plus n'entraîne pas la responsabilité financière.
2. Le taux de modification des données est faible (par exemple, en transactions par heure). Le RPO de 1 heure n'entraîne pas une perte de données massive.
3. L'application est sensible aux coûts et ne peut pas justifier le coût supplémentaire de la géo-réplication 

> [AZURE.NOTE]La géo-restauration n'alloue pas au préalable la capacité de calcul dans une région particulière pour restaurer les bases de données actives à partir de la sauvegarde pendant la panne. Le service gère la charge de travail associée aux requêtes de géo-restauration d'une manière qui réduit l'impact sur les bases de données existantes dans cette région et leurs exigences en termes de capacité auront la priorité. Par conséquent, le temps de récupération de votre base de données dépendra du nombre de bases de données en phase de récupération dans la même région en même temps.

##Utilisation de la géo-réplication

La géo-réplication crée un réplica de base de données (secondaire) dans une région différente de la région principale. Elle garantit que votre base de données aura les données et ressources de calcul nécessaires pour supporter la charge de travail de l'application après la restauration. Consultez la section [Récupération après une panne](sql-database-disaster-recovery.md) pour plus d'informations sur l'utilisation du basculement pour récupérer votre application.

Nous vous recommandons d'utiliser la géo-réplication si votre application répond aux critères suivants :

1. Elle est essentielle. Elle a un contrat SLA contraignant avec des valeurs RPO et RTO agressives. La perte de données et de disponibilité entraînent une responsabilité financière. 
2. Le taux de modification des données est élevé (par exemple, en transactions par minute ou seconde). Le RPO de 1 heure associé à la protection par défaut entraînera sans doute une perte de données inacceptable.
3. Le coût associé à l'utilisation de la géo-réplication est nettement plus faible que la responsabilité financière potentielle et la perte d'activité associée.

> [AZURE.NOTE]Si votre application utilise des bases de données de base, la géo-réplication n'est pas prise en charge.

##Quand choisir la réplication géographique standard par rapport à la Géo-réplication active

Les bases de données de niveau standard n'ont pas la possibilité d'utiliser la géo-réplication active. Par conséquent, si votre application utilise des bases de données standard et répond aux critères ci-dessus, la géo-réplication standard doit être activée. En revanche, les bases de données Premium peuvent utiliser les deux options. La géo-réplication standard a été conçue en tant que solution de récupération d'urgence plus simple et moins onéreuse, particulièrement adaptée aux applications qui l'utilisent uniquement pour se protéger contre des événements imprévus tels que des pannes. Avec la géo-réplication standard, vous ne pouvez utiliser que la région associée à la récupération d'urgence et vous avez la possibilité de créer plusieurs bases de données secondaires. Cette dernière fonctionnalité est essentielle pour le scénario de mise à niveau de l'application. Par conséquent, si ce scénario est essentiel pour votre application, vous devez plutôt activer la géo-réplication active. Consultez [Mise à niveau de l'application sans interruption](sql-database-business-continuity-application-upgrade.md) pour plus de détails.

> [AZURE.NOTE]La géo-réplication active prend également en charge l'accès en lecture seule à la base de données secondaire, offrant ainsi une capacité supplémentaire pour les charges de travail en lecture seule.

##Activation de la géo-réplication

Vous pouvez activer la géo-réplication à l'aide du portail Azure ou en appelant l'API REST ou la commande PowerShell.

###Portail Azure

1. Connectez-vous au [portail Azure](https://portal.Azure.com).
2. Sur le côté gauche de l'écran, sélectionnez **PARCOURIR**, puis sélectionnez **Bases de données SQL**
3. Accédez à votre panneau de base de données, sélectionnez la **carte de géo-réplication** et cliquez sur **Configurer la géo-réplication**.
4. Accédez au panneau de géo-réplication. Sélectionnez la région cible. 
5. Accédez au panneau Créer une base de données secondaire. Sélectionnez un serveur existant dans la région cible ou créez-en un.
6. Sélectionnez le type secondaire (*Lisible* ou *Illisible*)
7. Cliquez sur **Créer** pour terminer la configuration

> [AZURE.NOTE]La région associée à la récupération d'urgence dans le panneau de géo-réplication sera marquée comme étant *recommandée*. Si vous utilisez une base de données Premium, vous pouvez choisir une autre région. Si vous utilisez une base de données standard, vous ne pouvez pas la modifier. La base de données Premium aura le choix en termes de type secondaire (*Lisible* ou *Illisible*). La base de données standard peut uniquement sélectionner un type secondaire *Illisible*.
 
###PowerShell

Utilisez l'applet de commande PowerShell [Start-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720220.aspx) pour automatiser la configuration de la géo-réplication.

Pour créer la géo-réplication avec une base de données secondaire illisible pour une base de données Premium ou standard :
		
		Start-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -DatabaseName "SecondaryDatabaseName" -PartnerServer "PartnerServerName" –ContinuousCopy -OfflineSecondary
Pour créer la géo-réplication avec une base de données secondaire lisible pour une base de données Premium :

		Start-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -DatabaseName "SecondaryDatabaseName" -PartnerServer "PartnerServerName" –ContinuousCopy
		 
Cette commande est asynchrone. Après son retour, utilisez l'applet de commande [Get-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720235.aspx) pour vérifier l'état de cette opération. Le champ ReplicationState de l'objet retourné aura la valeur CATCH_UP une fois l'opération terminée.

		Get-AzureSqlDatabaseCopy -ServerName "PrimaryServerName" -DatabaseName "PrimaryDatabaseName" -PartnerServer "SecondaryServerName"


###API REST 

Utilisez l'API [Démarrer la copie de la base de données](https://msdn.microsoft.com/library/azure/dn509576.aspx) pour créer par programme une configuration de la géo-réplication.

Cette API est asynchrone. Après son retour, utilisez l'API [Obtenir la copie de la base de données](https://msdn.microsoft.com/library/azure/dn509570.aspx) pour vérifier l'état de cette opération. Le champ ReplicationState du corps de la réponse aura la valeur CATCH_UP une fois l'opération terminée.


##Comment choisir la configuration de basculement ? 

Lorsque vous concevez votre application pour la continuité des activités, vous devez envisager plusieurs options de configuration. Le choix dépendra de la topologie de déploiement de l'application et des parties de vos applications les plus vulnérables à une défaillance. Consultez [Conception de solutions cloud pour la récupération d'urgence à l'aide de la géo-réplication active](https://msdn.microsoft.com/library/azure/dn741328.aspx) pour obtenir des instructions.

<!---HONumber=58-->