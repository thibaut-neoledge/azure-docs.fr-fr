<properties 
   pageTitle="Modification du niveau de service et du niveau de performances d’une base de données" 
   description="Apprenez à faire évoluer une base de données de cloud de manière dynamique, via des mises à niveau vers des niveaux supérieurs ou inférieurs, en utilisant les niveaux de service de la base de données SQL Microsoft Azure. Ces niveaux vous permettent d’augmenter ou de réduire les performances, en fonction des besoins en termes de coûts et d’activité et ce, sans interrompre les applications." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="04/02/2015"
   ms.author="sstein"/>


# Modification du niveau de service et du niveau de performances d’une base de données 

Cette rubrique décrit les étapes nécessaires pour faire passer une base de données SQL Microsoft Azure vers un autre niveau de service ou de performances.

## Passage d’un niveau de service à un autre 

Exploitez les informations des sections [Mise à jour des bases de données SQL des éditions Web ou Business vers les nouveaux niveaux de service](sql-database-upgrade-new-service-tiers.md) et [Niveaux de service et de performance de base de données SQL Azure](https://msdn.microsoft.com/library/azure/dn741336.aspx) pour déterminer le niveau de service et de performances adéquat pour votre base de données SQL Microsoft Azure.

Vous pouvez aisément passer d’un niveau de service à un autre via le portail de gestion Microsoft Azure, [PowerShell](https://msdn.microsoft.com/library/azure/dn546726.aspx) ou les [API REST](https://msdn.microsoft.com/library/dn505719.aspx).

Lorsque vous planifiez cette opération, tenez compte de ce qui suit : - Avant d’effectuer la mise à niveau d’un niveau de services ou de performances vers un autre, assurez-vous que vous disposez du quota disponible requis sur le serveur. S’il vous faut un quota supérieur, appelez le support technique. - Les bases de données fédérées ne peuvent pas être mises à niveau vers les niveaux de service de base, Standard ou Premium.

- Pour qu’une base de données puisse passer à une version antérieure, sa taille doit être inférieure à la taille maximale autorisée par le niveau de service voulu. Si vous voulez en savoir plus sur la taille autorisée pour chaque niveau de service, consultez le tableau relatif aux tailles des bases de données et aux niveaux de service proposé dans cette section.

- Avant d’effectuer le passage à une version antérieure depuis un niveau de service Premium, vous devez arrêter toutes les relations de géo-réplication. Vous pouvez suivre les étapes décrites dans la section [Arrêt d’une relation de copie continue](https://msdn.microsoft.com/library/azure/dn741323.aspx) pour arrêter le processus de réplication entre la base de données principale et les bases de données secondaires actives.

- Les offres de service de restauration sont différentes selon les niveaux de service. Si vous passez à une version antérieure, vous risquez de ne plus pouvoir effectuer de restauration à un moment donné, ou de bénéficier d’une période de rétention des sauvegardes moins étendue. Pour en savoir plus, voir [Sauvegarde et restauration de base de données SQL Azure](https://msdn.microsoft.com/library/azure/jj650016.aspx).

- Vous pouvez apporter jusqu’à quatre modifications à une base de données individuelle (concernant un niveau de service ou de performances) par période de 24 heures.

- Les nouvelles propriétés de la base de données ne sont appliquées qu’une fois les modifications terminées.

Notez les points suivants : - Les niveaux de service Web et Business seront retirés en septembre 2015. Pour en savoir plus, voir [Forum aux questions sur la disparition des éditions Web et Business](https://msdn.microsoft.com/library/azure/dn741330.aspx).

<note included> - Il est important de noter que l’implémentation actuelle des [fédérations sera retirée en même temps que les niveaux de service Web et Business](https://msdn.microsoft.com/library/azure/dn741330.aspx). Nous vous recommandons d’utiliser [l’infrastructure élastique pour la base de données SQL Microsoft Azure](sql-database-elastic-scale-get-started.md) afin de générer une solution partitionnée et montée en charge sur la base de données SQL Microsoft Azure. Pour essayer cette infrastructure, voir « Prise en main de l’infrastructure élastique de base de données SQL Azure (version préliminaire) ».

## Mise à niveau vers un niveau de service plus élevé
Utilisez l’une des méthodes suivantes pour mettre une base de données à niveau. Ces étapes sont spécifiques à la mise à niveau vers un niveau de service Premium, mais s’appliquent à toutes les mises à niveau.

### Utilisation du portail de gestion Azure
1. Connectez-vous au portail de gestion Microsoft Azure en utilisant votre compte Microsoft.
2. Accédez à l’onglet **BASES DE DONNÉES SQL**.
3. Dans la liste **Bases de données**, sélectionnez une base de données. La base de données choisie s’ouvre alors sur la page **Tableau de bord de la base de données** ou **Démarrage rapide**.
4. Sélectionnez l’onglet **Mise à l’échelle** de la base de données.
5. Sous la section **Général**, sélectionnez le niveau de service **PREMIUM**.
6. Dans le champ **Niveau de performances**, sélectionnez **P1**, **P2** ou **P3**. Les ressources associées à chaque niveau de performances sont représentées sous la forme de DTU. Pour en savoir plus sur les niveaux de performances et les DTU, voir « Niveaux de service et de performance de Base de données SQL Azure ».
8. Dans la barre de commandes figurant au bas de l’écran, cliquez sur le bouton **Enregistrer**.
9. Une fenêtre **Confirmation** s’affiche. Lisez les informations fournies et activez la case à cocher pour confirmer l’opération.


### Utilisation de Microsoft Azure PowerShell
1. Utilisez le paramètre Set-AzureSqlDatabase pour spécifier le niveau de performances, la taille maximale de la base de données et le niveau de service de cette dernière. Pour obtenir la liste des tailles de base de données prises en charge par les différents niveaux de service, voir « Niveaux de service de la Base de données SQL Azure (éditions) ».
2. Définissez le contexte de serveur avec l’applet de commande New-AzureSqlDatabaseServerContext. Un exemple de syntaxe est fourni dans la section « Utilisation des commandes Microsoft Azure PowerShell ».
3. Obtenez un handle vers la base de données et le niveau de performances cible. Spécifiez le niveau de performances à l’aide du paramètre Set-AzureSqlDatabase – ServiceObjective.

**Exemple de syntaxe** Dans cet exemple : - La mise à niveau illustrée porte sur le niveau de service Premium. - Le handle $db est créé et pointe vers la base de données « somedb ». - Le handle $P1 est créé et pointe vers le niveau de performances Premium 1. - Le niveau de performances de la base de données $db est défini sur $P1.

		Windows PowerShell:

		$db = Get-AzureSqlDatabase $serverContext –DatabaseName "somedb"

		$P1= Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "P1"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $P1 –Edition Premium



## Mise à niveau vers un niveau de service inférieur
Utilisez l’une des méthodes suivantes pour mettre à niveau une base de données vers un niveau de service inférieur :

### Utilisation du portail de gestion Azure
1. Connectez-vous au portail de gestion Microsoft Azure en utilisant votre compte Microsoft.
2. Accédez à l’onglet **BASES DE DONNÉES SQL**.
3. Sélectionnez l’onglet **MISE À L’ÉCHELLE** de la base de données voulue.
4. Sous **Général**, sélectionnez le niveau de service inférieur de votre choix.
5. Dans la barre de commandes figurant au bas de l’écran, cliquez sur le bouton **Enregistrer**.
6. Le cas échéant, sur la page **Confirmation**, lisez les informations fournies et activez la case à cocher pour confirmer la modification.

### Utilisation de Microsoft Azure PowerShell
1. Utilisez le paramètre Set-AzureSqlDatabase pour spécifier le niveau de service, le niveau de performances et la taille maximale de la base de données.
2. Définissez le contexte de serveur à l’aide du paramètre New-AzureSqlDatabaseServerContext fourni dans l’exemple de syntaxe de la section « Utilisation des commandes Microsoft Azure PowerShell ».
3. Effectuez les actions suivantes :
 - Obtenez un handle vers la base de données.
 - Obtenez un handle vers le niveau de performances.
 - Spécifiez le niveau de service, le niveau de performances et la taille maximale de la base de données via le paramètre Set-AzureSqlDatabase –ServiceObjective.

**Exemple de syntaxe**

Cet exemple indique comment mettre à niveau une base de données du niveau de service Premium vers le niveau de service Standard (niveau inférieur) :

- Le handle $db est créé et pointe vers la base de données « somedb ».

- La variable $S2 est créée et pointe vers le niveau de performances Standard S2.

- Le niveau de performances de la base de données $db est défini sur $S2.

- Spécifiez le niveau de service de la base de données et la taille maximale de cette dernière au moyen des paramètres  –Edition et –MaxSizeGB. La valeur spécifiée pour le paramètre – MaxSizeGB doit être valide pour le niveau de service cible. Pour connaître les valeurs MaxSize pour chaque niveau de service, consultez le tableau disponible plus haut dans cette section.

		Windows PowerShell:

		$db = Get-AzureSqlDatabase $serverContext –DatabaseName “somedb”

		$S2 = Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "S2"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $S2 –Edition Standard –MaxSizeGB 40

##Passage d’un niveau de performances à un autre
Vous pouvez augmenter ou réduire les niveaux de performances d’une base de données Standard ou Premium, via l’une des méthodes suivantes. La procédure de modification du niveau de performances d’une base de données peut prendre un certain temps. Pour en savoir plus, voir [Impact des modifications relatives aux bases de données](https://msdn.microsoft.com/library/azure/dn369872.aspx#Impact) (ci-dessous).

Si vous modifiez le niveau de performances d’une base de données Premium pour laquelle des relations de géo-réplication sont actives, utilisez l’ordre suivant pour indiquer la base de données principale et les bases de données secondaires actives :

En effet, les bases de données secondaires actives doivent présenter un niveau de performances supérieur ou égal à celui de la base de données principale. - Si vous passez à un niveau de performances inférieur, commencez par appliquer la procédure à la base de données principale, puis passez à la ou aux bases de données secondaires actives.

- Si vous passez vers un niveau de performances supérieur, commencez par les bases de données secondaires actives, puis terminez par la base de données principale.

###Utilisation du portail de gestion Azure
1. Connectez-vous au portail de gestion Microsoft Azure en utilisant votre compte Microsoft.
2. Accédez à l’onglet **BASES DE DONNÉES SQL**.
3. Sélectionnez une base de données dans la liste **Bases de données**, pour le compte ou pour un serveur spécifique. La base de données choisie s’ouvre alors sur la page **Tableau de bord de la base de données** ou **Démarrage rapide**.
5. Sélectionnez l’onglet **Mise à l’échelle** de la base de données.
6. Choisissez un niveau de performances pour l’option **Niveau de performances**.
7. Dans la barre de commandes figurant au bas de l’écran, cliquez sur le bouton **Enregistrer**.

###Utilisation de Microsoft Azure PowerShell
1. Utilisez le paramètre Set-AzureSqlDatabase pour spécifier le niveau de performances de la base de données.
2. Définissez le contexte de serveur avec l’applet de commande New-AzureSqlDatabaseServerContext. Un exemple de syntaxe est fourni dans la section « Utilisation des commandes Microsoft Azure PowerShell ».
3. Effectuez les actions suivantes :
- Obtenez un handle vers la base de données.
- Obtenez un handle vers le niveau de performances.
- Spécifiez le niveau de performances via le paramètre Set-AzureSqlDatabase –ServiceObjective.

**Exemple de syntaxe**

Dans cet exemple :

- Le handle $db est créé et pointe vers la base de données « somedb ».

- Le handle $P2 est créé et pointe vers le niveau de performances Premium 2.

- Le niveau de performances de la base de données $db est défini sur $P2.

		Windows PowerShell
		$db = Get-AzureSqlDatabase $serverContext –DatabaseName “somedb”

		$P2 = Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "P2"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $P2

##Impact des modifications relatives aux bases de données
Cette section fournit des informations sur les effets de la mise à niveau vers un niveau de service Standard ou Premium, ou de la modification des niveaux de performances associés à votre base de données.

###Gestion des connexions aux applications lors d’une modification relative à une base de données
Il se peut que les connexions à la base de données soient temporairement interrompues suite à la modification de son niveau de performances ou à sa mise à niveau vers un niveau supérieur ou inférieur. Le système peut attendre quelques secondes avant de les rétablir. Les applications de base de données SQL doivent être codées de façon à résister à l’interruption d’une connexion. En effet, ce problème peut survenir à tout moment au sein d’une base de données SQL lorsqu’un ordinateur cesse de fonctionner dans le centre de données et que le service de la base de données SQL fait basculer la base de données. L’application ne requiert aucune modification de l’implémentation pour l’utilisation d’une base de données Premium ou la modification du niveau de performances d’une base de données Premium.

###Présentation et estimation de la latence des modifications relatives aux bases de données
Une modification apportée à l’objectif de niveau de service (SLO) d’une base de données implique souvent un déplacement des données. De ce fait, de longues heures peuvent s’écouler avant qu’une demande de modification ne se termine et que le changement de facturation associé ne soit appliqué. Ce déplacement des données se produit lors de la mise à niveau d’une base de données (vers un niveau supérieur ou intérieur) et peut également survenir lorsque vous modifiez le niveau de performances de la base de données.

**Latence des modifications apportées aux SLO et impliquant un déplacement des données**

Une fois la taille du stockage de la base de données déterminée, la latence d’une demande de modification concernant les SLO peut être estimée en fonction du calcul heuristique :

3 x (5 minutes + taille de la base de données / 150 Mo par minute)

Par exemple, si la taille de la base de données est de 50 Go, la latence de la demande de modification concernant les SLO est estimée en fonction du calcul heuristique suivant :

3 x (5 minutes + 50 Go x 1 024 Mo/Go / 150 Mo par minute) ≈ 17 heures

Les estimations relatives aux limites inférieure et supérieure en fonction de ces heuristiques varient entre 15 minutes (pour une base de données vide) et environ 2 jours (pour une base de données de 150 Go). Elles peuvent également être affectées par les conditions au sein du centre de données.

**Latence d’une mise à niveau vers un niveau de performances inférieur**

En règle générale, aucun déplacement de données ne survient lors de la mise à niveau d’une base de données vers un niveau de performances inférieur. Dans ce cas, la latence de la modification apportée aux SLO est beaucoup plus rapide ; elle se compte généralement en secondes.

Avertissement : l’instruction ci-dessus s’applique uniquement aux mises à niveau depuis un niveau de service Premium ou Standard vers un niveau inférieur. La mise à niveau vers un niveau inférieur (Web, Business ou de base) implique un déplacement des données.

###Vérification de l’état d’une modification apportée à la base de données
Vous pouvez vérifier l’état de votre base de données lors d’une mise à niveau vers un niveau de service inférieur ou supérieur, ou lorsque vous modifiez le niveau de performances via l’une des méthodes suivantes.

####Utilisation du portail de gestion Azure
1. Connectez-vous au portail de gestion Microsoft Azure en utilisant votre compte Microsoft.
2. Dans la liste **Bases de données**, sélectionnez une base de données. La base de données choisie s’ouvre alors sur la page **Tableau de bord de la base de données** ou **Démarrage rapide**.
3. Sur la page **Tableau de bord de la base de données**, consultez la zone **Aperçu rapide** pour accéder aux informations d’état de la section **Édition**.
4. Un objectif de niveau de service (SLO) représente le niveau de performances au sein d’un niveau de service.


##Utilisation des commandes Microsoft Azure PowerShell
Cette section indique les conditions préalables à respecter pour pouvoir faire appel aux commandes PowerShell de Microsoft Azure.

**Configuration requise**

Pour utiliser les applets de commande Microsoft Azure PowerShell décrites dans la présente rubrique, vous devez installer les logiciels suivants sur l’ordinateur hébergeant PowerShell. 1. Téléchargez une version de Windows PowerShell équivalente à 3.0 ou plus, en accédant à la page http://www.microsoft.com/fr-fr/download/details.aspx?id=34595.

2. Téléchargez Microsoft Azure PowerShell à partir de la section sur les outils de ligne de commande, sur la page de [téléchargement des outils et kits de développement logiciel (SDK) de Microsoft Azure](http://azure.microsoft.com/downloads/).

Procédez comme suit : dans l’écran **Démarrer** ou le menu **Démarrer**, accédez au logiciel Microsoft Azure PowerShell et démarrez-le.

Saisissez le nom d’utilisateur et le mot de passe associés au serveur.

Créez un contexte de serveur en utilisant le paramètre **New-AzureSqlDatabaseServerContext**.

**Exemple**

		Windows PowerShell
		$subId = <Subscription ID>
		$thumbprint = <Certificate Thumbprint>
		$myCert = Get-Item Cert:\CurrentUser\My\$thumbprint
		Set-AzureSubscription -SubscriptionName "mySubscription" -SubscriptionId $subId -Certificate $myCert
		Select-AzureSubscription -SubscriptionName "mySubscription"
		$serverContext = New-AzureSqlDatabaseServerContext -ServerName "myserver" -UseSubscription


**Références relatives à Microsoft Azure PowerShell** Pour afficher des informations détaillées sur les applets de commande de Microsoft Azure PowerShell utilisées dans la présente rubrique, voir [Applets de commande de base de données SQL Azure](https://msdn.microsoft.com/library/dn546726.aspx).

[New-AzureSqlDatabaseServerContext](http://go.microsoft.com/fwlink/?LinkId=391026)

[New-AzureSqlDatabase](http://go.microsoft.com/fwlink/?LinkId=391027)

[Set-AzureSqlDatabase](http://go.microsoft.com/fwlink/?LinkId=391412)

<!---HONumber=58-->