<properties 
	pageTitle="Création d’une machine virtuelle Oracle Database dans Azure" 
	description="Parcourez un exemple vous indiquant comment créer une machine virtuelle Oracle dans Microsoft Azure, puis comment créer une base de données Oracle au sein de cette machine." 
	services="virtual-machines" 
	authors="bbenz" 
	documentationCenter=""/>
	
<tags 
	ms.service="virtual-machines" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="infrastructure-services" 
	ms.date="06/22/2015" 
	ms.author="bbenz" />

#Création d’une machine virtuelle Oracle Database dans Azure
L’exemple suivant vous indique comment créer une machine virtuelle sur la base d’une image Oracle Database fournie par Microsoft et exécutée sur Windows Server 2012 dans Azure. Cet exemple comporte deux étapes : la création de la machine virtuelle, puis la création de la base de données Oracle au sein de la machine virtuelle. L’exemple présenté concerne Oracle Database version 12c, mais les étapes sont presque identiques pour la version 11g.

##Créer une machine virtuelle Oracle Database dans Azure

1.	Connectez-vous au [portail Azure](https://ms.portal.azure.com/).

2.	Cliquez sur **Marketplace**, puis sur **Compute**, puis tapez **Oracle** dans la zone de recherche.

3.	Sélectionnez l’une des images Oracle Database disponibles **(version 11g, version 12c, Standard Edition, Enterprise Edition ou l’une des offres groupées options populaires ou options avancées).** Passez en revue les informations concernant l’image sélectionnée (par exemple, la taille minimale recommandée), puis cliquez sur **Suivant**.

4.	Spécifiez un **nom d’hôte** pour la machine virtuelle.

5.	Spécifiez un **nom d’utilisateur** pour la machine virtuelle. Remarquez que cet utilisateur convient pour la connexion à distance à la machine virtuelle ; il ne s’agit pas du nom d’utilisateur de la base de données Oracle.

6.	Spécifiez un mot de passe pour la machine virtuelle et confirmez-le, ou fournissez une clé publique SSH.

7.	Sélectionnez un **niveau de tarification**. Remarquez que les niveaux de tarification recommandés sont affichés par défaut ; pour afficher toutes les options de configuration, cliquez sur **Afficher tout** dans le coin supérieur droit.

8.	Définissez [Configuration facultative](https://msdn.microsoft.com/library/azure/dn763935.aspx) si nécessaire, en tenant compte de ces remarques :

	1. Laissez le champ **Compte de stockage** tel quel pour créer un nouveau compte de stockage avec le nom de la machine virtuelle.

	2. Laissez le champ **Groupe à haute disponibilité** sur la valeur « Non configuré ».

	3. N’ajoutez aucun **point de terminaison** pour l’instant.

9.	Choisissez ou créez un groupe de ressources.

10. Choisissez un **abonnement**.

11. Choisissez un **emplacement**.

12. Cliquez sur **Créer** ; le processus de création d’une machine virtuelle démarre. Une fois que l’état de la machine virtuelle correspond à **En cours d’exécution**, passez à l’étape suivante.


##Pour créer votre base de données à l’aide de la machine virtuelle Oracle Database dans Azure

1.	Connectez-vous au [portail Azure](https://ms.portal.azure.com/).

2.	Cliquez sur **Machines virtuelles**.

3.	Cliquez sur le nom de la machine virtuelle à laquelle vous souhaitez vous connecter.

4.	Cliquez sur **Connecter**.

5.	Répondez aux invites afin de vous connecter à la machine virtuelle. Lorsque vous y êtes invité, entrez le nom d’administrateur et le mot de passe fournis lors de la création de la machine virtuelle.

6.	Créez une variable d’environnement nommée **ORACLE_HOSTNAME** dont la valeur définie correspond au nom de la machine virtuelle. Pour créer une variable d’environnement, procédez comme suit :

	1.	Cliquez sur **Démarrage Windows**, saisissez **Panneau de configuration**, cliquez sur l’icône **Panneau de configuration** et sélectionnez **Système et sécurité**, puis **Système** et enfin cliquez sur **Paramètres système avancés**.

	2.	Sélectionnez l’onglet **Avancé**, puis cliquez sur **Variables d’environnement**.

	3.	Dans la section **Variables système**, cliquez sur **Nouveau** afin de créer la variable.

	4.	Dans la boîte de dialogue **Nouvelle variable système**, saisissez le nom de variable **ORACLE_HOSTNAME**, puis entrez le nom de la machine virtuelle en guise de valeur. Pour déterminer le nom de la machine, ouvrez une invite de commandes et exécutez la commande **SET COMPUTERNAME** (la sortie de cette commande indique le nom de la machine).
	
	5.	Cliquez sur **OK** pour enregistrer la nouvelle variable d’environnement et fermer la boîte de dialogue **Nouvelle variable système**.

	6.	Fermez les autres boîtes de dialogue ouvertes par le panneau de configuration.

7.	Cliquez sur **Démarrage Windows**, puis saisissez **Database Configuration Assistant**. Cliquez sur l’icône **Database Configuration Assistant**.

8.	Dans assistant **Database Configuration Assistant**, renseignez les valeurs attendues pour chaque étape de la boîte de dialogue :

	1.	**Étape 1 :** cliquez sur **Create Database**, puis cliquez sur **Next**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image5.png)

	2. **Étape 2 :** Saisissez une valeur pour le nom **Global database name**. Saisissez et confirmez une valeur pour le mot de passe **Administrative Password** ; ce mot de passe sera celui de l’utilisateur **SYSTEM** de votre base de données Oracle. Décochez la case **Create As Container Database**. Cliquez sur **Next**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image6.png)

	3. **Étape 3 :** les vérifications préalables s’effectuent automatiquement. Passez à l’**étape 4**.
	
	4. **Étape 4 :** passez en revue les options **Create Database - Summary**, puis cliquez sur **Finish**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image7.png)
	5. **Étape 5 :** la page **Progress Page** indique l’état de la création de votre base de données.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image8.png)
	6. Une fois votre base de données créée, vous pouvez choisir d’utiliser la boîte de dialogue **Password Management**. Si nécessaire, modifiez les paramètres du mot de passe, puis fermez les boîtes de dialogue pour quitter l’assistant **Database Configuration Assistant**.

##Vérifier l’installation de votre base de données

1.	Restez connecté à votre machine virtuelle, puis démarrez une invite de commandes SQL Plus. Cliquez sur **Démarrage Windows**, puis saisissez **SQL Plus**. Cliquez sur l’icône **SQL Plus**.

2.	Lorsque vous y êtes invité, connectez-vous à l’aide du nom d’utilisateur **SYSTEM** et le mot de passe que vous avez spécifié lors de la création de la base de données Oracle.

3.	Exécutez la commande suivante dans la l’invite de commandes SQL Plus :

		select * from GLOBAL_NAME;

	Vous devez alors obtenir le nom global de la base de données que vous avez créée.

	![](media/virtual-machines-creating-oracle-database-virtual-machine/image9.png)

##Permettre l’accès à distance à votre base de données
Pour permettre l’accès à distance à votre base de données (par exemple, à partir d’un ordinateur client exécutant du code Java), vous devez démarrer l’écouteur de bases de données, ouvrir le port 1521 dans le pare-feu de votre machine virtuelle et créer un point de terminaison public pour le port 1521.

### Démarrer l’écouteur de bases de données
1.	Connectez-vous à votre machine virtuelle.

2.	Ouvrez une invite de commandes.

3.	Exécutez ensuite la commande suivante dans l’invite de commandes :

		lsnrctl start

(Exécutez la commande `lsnrctl status` pour vérifier l’état de l’écouteur. Lorsque vous souhaitez arrêter l’écouteur, exécutez la commande `lsnrctl stop`.)

### Ouvrir le port 1521 dans le pare-feu de votre machine virtuelle

1.	Restez connecté à votre machine virtuelle. Cliquez sur **Démarrage Windows**, saisissez **Pare-feu Windows avec sécurité avancée**, puis cliquez sur l’icône **Pare-feu Windows avec sécurité avancée**. Cette opération ouvre la console de gestion **Pare-feu Windows avec sécurité avancée**.

2.	Dans la console de gestion du pare-feu, cliquez sur **Règles de trafic entrant** dans le volet de gauche (si l’option **Règles de trafic entrant** ne s’affiche pas, développez le nœud supérieur dans le volet de gauche), puis cliquez sur **Nouvelle règle** dans le volet de droite.

3.	Dans le champ **Type de règle**, sélectionnez **Port** et cliquez sur **Suivant.**

4.	Dans le champ **Protocole et port**, choisissez **TCP**, sélectionnez **Ports locaux spécifiques**, attribuez au port la valeur **1521** et cliquez sur Suivant.

5.	Sélectionnez **Autoriser la connexion**, puis cliquez sur **Suivant**.

6.	Acceptez les valeurs par défaut pour les profils concernés par la règle et cliquez sur **Suivant**.

7.	Spécifiez un nom pour la règle, ainsi qu’une description, le cas échéant, puis cliquez sur **Terminer**.

### Créer un point de terminaison public pour le port 1521

1.	Connectez-vous au [portail Azure](https://ms.portal.azure.com/).

2.	Cliquez sur **Parcourir**.

3.    Cliquez sur **Machines virtuelles**.

4.	Sélectionnez la machine virtuelle.

5.	Cliquez sur **Paramètres**.

6.	Cliquez sur **Endpoints**.

7.	Cliquez sur **Add**.

8.	Saisissez le nom du point de terminaison.

	1. Indiquez **TCP** pour le protocole.
	
	2. Indiquez **1521** pour le port public.
	
	3. Indiquez **1521** pour le port privé.

9.	Ne modifiez aucune des options restantes.

10. Cliquez sur **OK**

##Activer l’accès à distance à Oracle Database Enterprise Manager
Si vous souhaitez activer l’accès distant à Oracle Database Enterprise Manager, ouvrez le port 5500 dans votre pare-feu et créez un point de terminaison de machine virtuelle pour le port 5500 dans le portail Azure (utilisez la procédure décrite ci-dessus pour ouvrir le port 1521 et créer un point de terminaison pour le port 1521). Ensuite, pour exécuter Oracle Enterprise Manager à partir de l’ordinateur distant, ouvrez un navigateur et saisissez une URL sous la forme `http://<<unique_domain_name>>:5500/em`. (Vous pouvez déterminer la valeur de *<<unique_domain_name>>* dans le [Portail Azure](https://ms.portal.azure.com/) en cliquant sur **Machines Virtuelles**, puis en sélectionnant la machine virtuelle que vous utilisez pour exécuter Oracle Database).

##Configurer les offres groupées options populaires et options avancées
Si vous avez choisi l’offre groupée **Oracle Database avec options populaires** ou l’offre groupée **Oracle Database avec options avancées**, l’étape suivante vous permet de configurer les fonctionnalités supplémentaires de votre installation Oracle. Pour obtenir des informations concernant la configuration dans Windows, consultez la documentation Oracle, les configurations pouvant fortement varier en fonction de vos besoins concernant chaque composant.

L’**offre groupée Oracle Database avec options populaires** comporte Oracle Database Enterprise Edition avec des instances avec licence des composants suivants : [Partitioning](http://www.oracle.com/us/products/database/options/partitioning/overview/index.html), [Active Data Guard](http://www.oracle.com/us/products/database/options/active-data-guard/overview/index.html), [Oracle Tuning Pack for Database](http://docs.oracle.com/html/A86647_01/tun_ovw.htm), [Oracle Diagnostics Pack for Database](http://docs.oracle.com/cd/B28359_01/license.111/b28287/options.htm#CIHIHDDJ) et [Oracle Lifecycle Management Pack for Database](http://www.oracle.com/technetwork/oem/lifecycle-mgmt-495331.html).

L’**offre groupée Oracle Database avec options avancées** comporte les instances avec licences de tous les composants de l’offre groupée Oracle Database avec option populaires, plus les composants suivants : [Advanced Compression](http://www.oracle.com/us/products/database/options/advanced-compression/overview/index.html), [Advanced Security](http://www.oracle.com/us/products/database/options/advanced-security/overview/index.html), [Label Security](http://www.oracle.com/us/products/database/options/label-security/overview/index.html), [Database Vault](http://www.oracle.com/us/products/database/options/database-vault/overview/index.html), [Advanced Analytics](http://www.oracle.com/us/products/database/options/advanced-analytics/overview/index.html), [OLAP](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [Spatial and Graph](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [In-Memory Database Cache](http://www.oracle.com/technetwork/products/timesten/overview/timesten-imdb-cache-101293.html), [Data Masking Pack](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CHDGEEBB) et Oracle Test Data Management Pack (partie intégrante de Data Masking Pack).

##Ressources supplémentaires
Maintenant que vous avez configuré votre machine virtuelle et créé votre base de données, consultez les rubriques suivantes pour plus d’informations.

-	[Images de machine virtuelle Oracle – Considérations diverses](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

-	[Oracle Database 12c Documentation Library](http://www.oracle.com/pls/db1211/homepage) (en anglais)

-	[Connecting to Oracle Database from a Java Application](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136) (en anglais)

-	[Oracle Virtual Machine images for Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md) (en anglais)

-	[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm) (en anglais)

<!---HONumber=July15_HO4-->