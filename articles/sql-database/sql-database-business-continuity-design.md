<properties 
   pageTitle="Conception de base de données SQL pour la continuité des activités" 
   description="Conseils sur le choix Dans cette section, vous bénéficierez de conseils sur le choix des fonctionnalités BCDR à utiliser et quand. Vous y trouverez, entre autres, des descriptions de ce que vous obtenez automatiquement en utilisant une base de données SQL."
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/25/2016"
   ms.author="elfish"/>

#Conception pour la continuité des activités

La conception de votre application pour la continuité des activités nécessite que vous répondiez aux questions suivantes :

1. Quelle fonctionnalité de continuité des activités est appropriée pour protéger mon application contre les pannes ?
2. Quel niveau de topologie de réplication et de redondance utiliser ?

##Utilisation de la géo-restauration

La base de données SQL fournit une protection intégrée de base de chaque base de données par défaut. Cette protection s'effectue par le stockage des sauvegardes de bases de données dans le stockage Azure redondant au niveau géographique (GRS). Si vous choisissez cette méthode, aucune configuration particulière ni allocation de ressources supplémentaire n'est nécessaire. Grâce à ces sauvegardes, vous pouvez récupérer votre base de données dans n'importe quelle région à l'aide de la commande de géo-restauration. Utilisez la section [Récupération après une panne](sql-database-disaster-recovery.md) pour obtenir des détails sur l'utilisation de la géo-restauration pour restaurer votre application.

Nous vous recommandons d'utiliser la protection intégrée si votre application répond aux critères suivants :

1. Elle n'est pas essentielle. Elle n'a pas de contrat SLA contraignant. Donc, l'interruption de service de 24 heures ou plus n'entraîne pas la responsabilité financière.
2. Le taux de modification des données est faible (par exemple, en transactions par heure). Le RPO de 1 heure n'entraîne pas une perte de données massive.
3. L'application est sensible aux coûts et ne peut pas justifier le coût supplémentaire de la géo-réplication 

> [AZURE.NOTE] La géo-restauration n'alloue pas au préalable la capacité de calcul dans une région particulière pour restaurer les bases de données actives à partir de la sauvegarde pendant la panne. Le service gère la charge de travail associée aux requêtes de géo-restauration d'une manière qui réduit l'impact sur les bases de données existantes dans cette région et leurs exigences en termes de capacité auront la priorité. Par conséquent, le temps de récupération de votre base de données dépendra du nombre de bases de données en phase de récupération dans la même région en même temps.

##Utilisation de la géo-réplication

La géo-réplication crée un réplica de base de données (secondaire) dans une région différente de la région principale. Elle garantit que votre base de données aura les données et ressources de calcul nécessaires pour supporter la charge de travail de l'application après la restauration. Consultez la section [Récupération après une panne](sql-database-disaster-recovery.md) pour plus d'informations sur l'utilisation du basculement pour récupérer votre application.

Nous vous recommandons d'utiliser la géo-réplication si votre application répond aux critères suivants :

1. Elle est essentielle. Elle a un contrat SLA contraignant avec des valeurs RPO et RTO agressives. La perte de données et de disponibilité entraînent une responsabilité financière. 
2. Le taux de modification des données est élevé (par exemple, en transactions par minute ou seconde). Le RPO de 1 heure associé à la protection par défaut entraînera sans doute une perte de données inacceptable.
3. Le coût associé à l'utilisation de la géo-réplication est nettement plus faible que la responsabilité financière potentielle et la perte d'activité associée.


> [AZURE.NOTE] La géo-réplication active prend également en charge l'accès en lecture seule à la base de données secondaire, offrant ainsi une capacité supplémentaire pour les charges de travail en lecture seule.

##Activation de la géo-réplication

Vous pouvez activer la géo-réplication à l’aide du portail Azure Classic ou en appelant l’API REST ou la commande PowerShell.

###Portail Azure

[AZURE.VIDEO sql-database-enable-geo-replication-in-azure-portal]

1. Connectez-vous au [portail Azure](https://portal.Azure.com).
2. Sur le côté gauche de l'écran, sélectionnez **PARCOURIR**, puis sélectionnez **Bases de données SQL**
3. Accédez à votre panneau de base de données, sélectionnez la **carte de géo-réplication** et cliquez sur **Configurer la géo-réplication**.
4. Accédez au panneau de géo-réplication. Sélectionnez la région cible. 
5. Accédez au panneau Créer une base de données secondaire. Sélectionnez un serveur existant dans la région cible ou créez-en un.
6. Sélectionnez le type secondaire (*Lisible* ou *Illisible*)
7. Cliquez sur **Créer** pour terminer la configuration

> [AZURE.NOTE] La région associée à la récupération d'urgence dans le panneau de géo-réplication sera marquée comme étant *recommandée* mais vous pouvez choisir une autre région.


###PowerShell

Utilisez l'applet de commande PowerShell [New-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) pour créer la configuration de la géo-réplication. Cette commande est synchrone et s’exécute lorsque les bases de données primaires et secondaires sont synchronisées.

Pour configurer la géo-réplication avec une base de données secondaire non accessible en lecture :
		
    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "None"

Pour créer la géo-réplication avec une base de données secondaire accessible en lecture :

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"
		 

###API REST 

Utilisez l’API [Create Database](https://msdn.microsoft.com/library/mt163685.aspx) avec *createMode* défini sur *NonReadableSecondary* ou *Secondary* pour créer par programme une base de données secondaire de géo-réplication.

Cette API est asynchrone. Après son retour, utilisez l'API [Get Replication Link](https://msdn.microsoft.com/library/mt600778.aspx) pour vérifier l'état de cette opération. Le champ *replicationState* du corps de la réponse aura la valeur CATCHUP une fois l'opération terminée.


##Comment choisir la configuration de basculement ? 

Lorsque vous concevez votre application pour la continuité des activités, vous devez envisager plusieurs options de configuration. Le choix dépendra de la topologie de déploiement de l'application et des parties de vos applications les plus vulnérables à une défaillance. Consultez [Conception de solutions Cloud pour la récupération d'urgence à l'aide de la géo-réplication](sql-database-designing-cloud-solutions-for-disaster-recovery.md) pour obtenir des instructions.

<!---HONumber=AcomDC_0504_2016-->