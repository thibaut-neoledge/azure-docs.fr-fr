<properties
	pageTitle="Créer une machine virtuelle Oracle Database à l’aide du portail Azure | Microsoft Azure"
	description="Découvrez comment créer une machine virtuelle sur laquelle figure une base de données Oracle Database à l’aide du modèle de déploiement classique et du portail Azure en version préliminaire."
	services="virtual-machines"
	authors="bbenz"
	documentationCenter=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="Windows"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />

#Créer une machine virtuelle Oracle Database dans Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


L’exemple suivant vous indique comment créer une machine virtuelle sur la base d’une image Oracle Database fournie par Microsoft et exécutée sur Windows Server 2012 dans Azure. La procédure à suivre implique deux étapes. Tout d’abord, créez la machine virtuelle, puis créez l’instance Oracle Database dans la machine virtuelle. L’exemple présenté concerne Oracle Database version 12c, mais les étapes sont presque identiques pour la version 11g.

##Pour créer une machine virtuelle Oracle Database dans Azure

1.	Connectez-vous au [Portail Azure en version préliminaire](https://ms.portal.azure.com/).

2.	Cliquez sur **Marketplace**, sur **Compute**, puis entrez **Oracle** dans la zone de recherche.

3.	Sélectionnez l’une des images Oracle Database disponibles **(version 11g, version 12c, Standard Edition, Enterprise Edition ou l’une des offres groupées options populaires ou options avancées).** Passez en revue les informations concernant l’image sélectionnée (par exemple, la taille minimale recommandée), puis cliquez sur **Suivant**.

4.	Spécifiez un **nom d’hôte** pour la machine virtuelle.

5.	Spécifiez un **nom d’utilisateur** pour la machine virtuelle. Remarquez que cet utilisateur convient pour la connexion à distance à la machine virtuelle ; il ne s’agit pas du nom d’utilisateur de la base de données Oracle.

6.	Spécifiez un mot de passe pour la machine virtuelle et confirmez-le, ou fournissez une clé publique SSH.

7.	Sélectionnez un **niveau tarifaire**. Remarquez que les niveaux tarifaires recommandés sont affichés par défaut ; pour afficher toutes les options de configuration, cliquez sur **Afficher tout** en haut à droite.

8.	Définissez la configuration facultative si nécessaire, en tenant compte des considérations suivantes :

	a. Laissez le champ **Compte de stockage** tel quel pour créer un nouveau compte de stockage avec le nom de la machine virtuelle.

	b. Laissez le champ **Groupe à haute disponibilité** sur la valeur « Non configuré ».

	c. N’ajoutez aucun **point de terminaison** pour l’instant.

9.	Choisissez ou créez un groupe de ressources.

10. Choisissez un **Abonnement**.

11. Choisissez un **Emplacement**.

12. Cliquez sur **Créer** pour lancer le processus de création d’une machine virtuelle. Une fois que l’état de la machine virtuelle est **En cours d’exécution**, passez à la procédure suivante.


##Pour créer votre base de données à l’aide de la machine virtuelle Oracle Database dans Azure

1.	Connectez-vous au [Portail Azure en version préliminaire](https://ms.portal.azure.com/).

2.	Cliquez sur **Virtual Machines**.

3.	Cliquez sur le nom de la machine virtuelle à laquelle vous voulez vous connecter.

4.	Cliquez sur **Connecter**.

5.	Répondez aux invites pour vous connecter à la machine virtuelle. Lorsque vous y êtes invité, entrez le nom d’administrateur et le mot de passe fournis lors de la création de la machine virtuelle.

6.	Créez une variable d’environnement nommée **ORACLE\_HOSTNAME** dont la valeur correspond au nom d’ordinateur de la machine virtuelle. Vous pouvez créer cette variable d’environnement en procédant comme suit :

	a. Dans Windows, cliquez sur **Démarrer**, tapez **Panneau de configuration**, cliquez sur l’icône **Panneau de configuration**, cliquez sur **Système et sécurité**, sur **Système**, puis sur **Paramètres système avancés**.

	b. Cliquez sur l’onglet **Avancé**, puis sur **Variables d’environnement**.

	c. Dans la section **Variables système**, cliquez sur **Nouvelle** pour créer la variable.

	d. Dans la boîte de dialogue **Nouvelle variable système**, entrez le nom de variable **ORACLE\_HOSTNAME**, puis tapez le nom d’ordinateur de la machine virtuelle en guise de valeur. Pour déterminer le nom d’ordinateur, ouvrez une invite de commandes, puis exécutez la commande **SET COMPUTERNAME** (la sortie de cette commande indique le nom d’ordinateur).

	e. Cliquez sur **OK** pour enregistrer la nouvelle variable d’environnement et fermer la boîte de dialogue **Nouvelle variable système**.

	f. Fermez les autres boîtes de dialogue ouvertes par le panneau de configuration.

7.	Dans Windows, cliquez sur **Démarrer**, puis tapez **Database Configuration Assistant**. Cliquez sur l’icône **Database Configuration Assistant**.

8.	Dans l’Assistant **Database Configuration Assistant**, fournissez les valeurs nécessaires pour chaque étape de la boîte de dialogue :

	a. **Étape 1 :** cliquez sur **Create Database**, puis sur **Next**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image5.png)

	b. **Étape 2 :** entrez un nom pour **Global Database Name**. Entrez et confirmez une valeur de mot de passe pour **Administrative Password**. Ce mot de passe est dédié à votre utilisateur **SYSTEM** de base de données Oracle. Désactivez l’option **Create As Container Database**. Cliquez sur **Next**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image6.png)

	c. **Étape 3 :** la vérification des conditions préalables doit s’effectuer automatiquement et vous amener à l’**étape 4**.

	d. **Étape 4 :** examinez les options de **Create Database - Summary**, puis cliquez sur **Finish**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image7.png)
	e. **Étape 5 :** la page **Progress Page** indique l’état de la création de votre base de données.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image8.png)
	f. Une fois votre base de données créée, vous pouvez choisir d’utiliser la boîte de dialogue **Password Management**. Si nécessaire, modifiez les paramètres du mot de passe, puis fermez les boîtes de dialogue pour quitter l’Assistant **Database Configuration Assistant**.

##Vérifier l’installation de votre base de données

1.	En restant connecté à votre machine virtuelle, démarrez une invite de commande SQL Plus. Dans Windows, cliquez sur *Démarrer**, puis tapez **SQL Plus**. Cliquez sur l’icône **SQL Plus**.

2.	Lorsque vous y êtes invité, connectez-vous à l’aide du nom d’utilisateur **SYSTEM** et du mot de passe que vous avez spécifié lors de la création de la base de données Oracle.

3.	Exécutez la commande suivante dans l’invite de commandes SQL Plus.

		**select * from GLOBAL\_NAME;**

	Vous devez alors obtenir le nom global de la base de données que vous avez créée.

	![](media/virtual-machines-creating-oracle-database-virtual-machine/image9.png)

##Permettre l’accès à distance à votre base de données
Pour permettre l’accès à distance à votre base de données (par exemple, à partir d’un ordinateur client exécutant du code Java), vous devez démarrer l’écouteur de bases de données, ouvrir le port 1521 dans le pare-feu de votre machine virtuelle et créer un point de terminaison public pour le port 1521.

### Démarrer l’écouteur de bases de données
1.	Connectez-vous à votre machine virtuelle.

2.	Ouvrez une invite de commandes.

3.	Exécutez ensuite la commande suivante dans l’invite de commandes.

		**lsnrctl start**

> [AZURE.NOTE]Vous pouvez exécuter la commande **lsnrctl status** pour vérifier l’état de l’écouteur. Lorsque vous souhaitez arrêter l’écouteur, vous pouvez exécuter la commande **lsnrctl stop**.

### Ouverture du port 1521 dans le pare-feu de votre machine virtuelle

1.	Tout en restant connecté à votre machine virtuelle, dans Windows, cliquez sur **Démarrer**, tapez **Pare-feu Windows avec sécurité avancée**, puis cliquez sur l’icône **Pare-feu Windows avec sécurité avancée**. Cette opération ouvre la console de gestion **Pare-feu Windows avec fonctions avancées de sécurité**.

2.	Dans la console de gestion du pare-feu, cliquez sur **Règles de trafic entrant** dans le volet de gauche (si l’option **Règles de trafic entrant** ne s’affiche pas, développez le nœud supérieur dans le volet de gauche), puis cliquez sur **Nouvelle règle** dans le volet de droite.

3.	Pour **Type de règle**, sélectionnez **Port**, puis cliquez sur **Suivant**.

4.	Dans le champ **Protocole et port**, choisissez **TCP**, sélectionnez **Ports locaux spécifiques**, attribuez au port la valeur **1521**, puis cliquez sur **Suivant**.

5.	Sélectionnez **Autoriser la connexion**, puis cliquez sur **Suivant**.

6.	Acceptez les valeurs par défaut pour les profils auxquels la règle s’applique, puis cliquez sur **Suivant**.

7.	Spécifiez un nom pour la règle, ainsi qu’une description éventuelle, puis cliquez sur **Terminer**.

### Créer un point de terminaison public pour le port 1521

1.	Connectez-vous au [Portail Azure en version préliminaire](https://ms.portal.azure.com/).

2.	Cliquez sur **Parcourir**.

3.  Cliquez sur **Virtual Machines**.

4.	Sélectionnez la machine virtuelle.

5.	Cliquez sur **Paramètres**.

6.	Cliquez sur **Endpoints**.

7.	Cliquez sur **Add**.

8.	Saisissez le nom du point de terminaison :

	a. Spécifiez le protocole **TCP**.

	b. Spécifiez le port public **1521**.

	c. Spécifiez le port privé **1521**.

9.	Ne modifiez aucune des options restantes.

10. Cliquez sur **OK**.

##Activer l’accès à distance à Oracle Database Enterprise Manager
Si vous souhaitez activer l’accès distant à Oracle Database Enterprise Manager, ouvrez le port 5500 dans votre pare-feu et créez un point de terminaison de machine virtuelle pour le port 5500 dans le portail Azure (utilisez la procédure décrite ci-dessus pour ouvrir le port 1521 et créer un point de terminaison pour le port 1521). Ensuite, pour exécuter Oracle Enterprise Manager à partir de l’ordinateur distant, ouvrez un navigateur et accédez à l’URL `http://<<unique_domain_name>>:5500/em`.

> [AZURE.NOTE](Vous pouvez déterminer la valeur de *<<unique\_domain\_name>>* dans le [Portail Azure](https://ms.portal.azure.com/) en cliquant sur **Virtual Machines**, puis en sélectionnant la machine virtuelle que vous utilisez pour exécuter Oracle Database).

##Configuration des offres groupées, options populaires et options avancées
Si vous avez choisi l’offre groupée **Oracle Database avec options populaires** ou **Oracle Database avec options avancées**, l’étape suivante consiste à configurer les fonctionnalités supplémentaires de votre installation Oracle. Pour obtenir des informations concernant la configuration dans Windows, consultez la documentation Oracle, les configurations pouvant fortement varier en fonction de vos besoins concernant chaque composant.

L’**offre groupée Oracle Database avec options populaires** comporte Oracle Database Enterprise Edition et des instances avec licence des composants suivants : [Partitioning](http://www.oracle.com/us/products/database/options/partitioning/overview/index.html), [Active Data Guard](http://www.oracle.com/us/products/database/options/active-data-guard/overview/index.html), [Oracle Tuning Pack for Database](http://docs.oracle.com/html/A86647_01/tun_ovw.htm), [Oracle Diagnostics Pack for Database](http://docs.oracle.com/cd/B28359_01/license.111/b28287/options.htm#CIHIHDDJ) et [Oracle Lifecycle Management Pack for Database](http://www.oracle.com/technetwork/oem/lifecycle-mgmt-495331.html).

L’**offre groupée Oracle Database avec options avancées** comporte les instances avec licences de tous les composants de l’offre groupée Oracle Database avec options populaires, plus les composants suivants : [Advanced Compression](http://www.oracle.com/us/products/database/options/advanced-compression/overview/index.html), [Advanced Security](http://www.oracle.com/us/products/database/options/advanced-security/overview/index.html), [Label Security](http://www.oracle.com/us/products/database/options/label-security/overview/index.html), [Database Vault](http://www.oracle.com/us/products/database/options/database-vault/overview/index.html), [Advanced Analytics](http://www.oracle.com/us/products/database/options/advanced-analytics/overview/index.html), [OLAP](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [Spatial and Graph](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [In-Memory Database Cache](http://www.oracle.com/technetwork/products/timesten/overview/timesten-imdb-cache-101293.html), [Data Masking Pack](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CHDGEEBB) et Oracle Test Data Management Pack (partie intégrante de Data Masking Pack).

##Ressources supplémentaires
Maintenant que vous avez configuré votre machine virtuelle et créé votre base de données, consultez les rubriques suivantes pour plus d’informations.

-	[Images de machines virtuelles Oracle – Considérations diverses](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

-	[Oracle Database 12c Documentation Library](http://www.oracle.com/pls/db1211/homepage) (en anglais)

-	[Connexion à une base de données Oracle à partir d’une application Java](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

-	[Liste des images de machine virtuelle Oracle](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

-	[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=AcomDC_1125_2015-->