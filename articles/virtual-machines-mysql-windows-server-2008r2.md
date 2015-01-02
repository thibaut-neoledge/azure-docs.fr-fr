<properties urlDisplayName="Install MySQL" pageTitle="Création d'une machine virtuelle exécutant MySQL dans Azure " metaKeywords="Azure virtual machines, Azure Windows Server, Azure installing MySQL, Azure configuring MySQL, Azure databases" description="Create an Azure virtual machine running Windows Server 2008 R2, and then install and configure a MySQL database on the virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running Windows Server 2008 R2 in Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="kathydav" />


#Installation de MySQL sur une machine virtuelle exécutant Windows Server 2008 R2 sur Azure

[MySQL](http://www.mysql.com) est une base de données SQL open source connue. À l'aide du [portail de gestion Azure][AzurePreviewPortal], vous pouvez créer une machine virtuelle exécutant Windows Server 2008 R2 à partir de la bibliothèque d'images. Vous pouvez alors installer et configurer une base de données MySQL sur la machine virtuelle.

Ce didacticiel vous explique les procédures suivantes :

- créer une machine virtuelle exécutant Windows Server 2008 R2 dans le portail de gestion ;

- installer et exécuter MySQL Community Server sur la machine virtuelle.

##Création d'une machine virtuelle exécutant Windows Server

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

##Association d'un disque de données

Une fois la machine virtuelle créée, attachez un disque de données. Ce disque fournit le stockage de données dont vous avez besoin pendant l'installation de MySQL. Consultez [Association d'un disque de données à une machine virtuelle Windows](http://azure.microsoft.com/fr-fr/documentation/articles/storage-windows-attach-disk/) et suivez les instructions permettant d'attacher un disque vide.

##Se connecter à la machine virtuelle
Ensuite, connectez-vous à la machine virtuelle pour installer MySQL.

[WACOM.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

##Installation et exécution de MySQL Community Server sur la machine virtuelle
Pour installer, configurer et exécuter MySQL Community Server, procédez comme suit :

1. Une fois que vous êtes connecté à la machine virtuelle à l'aide du Bureau à distance, ouvrez **Internet Explorer** à partir du menu **Démarrer**. 

2. Sélectionnez le bouton **Outils** en haut à droite. Dans **Options Internet**, sélectionnez l'onglet **Sécurité**, l'icône **Sites de confiance**, puis cliquez sur le bouton **Sites**. Ajoutez *http://\*.mysql.com* à la liste des sites de confiance.

3. Accédez à la page [Téléchargement de MySQL Community Server][MySQLDownloads].

4. Sélectionnez **Microsoft Windows** dans le menu déroulant **Plateforme** et cliquez sur **Sélectionner**.

5. Recherchez la version la plus récente de **Windows (x86, 64 bits), Programme d'installation de MSI**, puis cliquez sur **Télécharger**. 

6. Sélectionnez **Non merci, démarrer simplement mon téléchargement !** (ou inscrivez-vous pour obtenir un compte).  Si vous y êtes invité, sélectionnez un site miroir pour télécharger le programme d'installation MySQL et l'enregistrer sur le Bureau.

7. Double-cliquez sur le fichier d'installation sur le Bureau pour commencer l'installation.

8. Cliquez sur **Suivant**.

9. Acceptez le contrat de licence, puis cliquez sur **Suivant**.

10. Cliquez sur **Par défaut** pour installer les fonctionnalités courantes.

11. Cliquez sur **Installer**.

12. Démarrez l'Assistant Configuration MySQL et cliquez sur **Suivant**.

13. Sélectionnez **Configuration détaillée** et cliquez sur Suivant.

14. Sélectionnez **Ordinateur serveur** si vous prévoyez d'exécuter MySQL avec d'autres applications sur le serveur ou sélectionnez l'option qui correspond le mieux à vos besoins.  Cliquez sur **Suivant**.

15. Sélectionnez **Base de données multifonctionnelle** ou l'option qui correspond le mieux à vos besoins.  Cliquez sur **Suivant**.

16. Sélectionnez le lecteur de données que vous avez attaché à la section précédente.

	![Configure MySQL][MySQLConfig5]

17. Sélectionnez **Aide à la décision (DSS)/OLAP** ou l'option qui correspond le mieux à vos besoins.  Cliquez sur **Suivant**.

18. Sélectionnez**Activer le réseau TCP/IP** et **Ajouter une exception au pare-feu pour ce port** (cela crée une règle de trafic entrant dans le Pare-feu Windows nommée **MySQL Server**).

	![Configure MySQL][MySQLConfig7]

19. Si vous devez stocker du texte en plusieurs langues, sélectionnez **Meilleure prise en charge pour plusieurs langues**. Sinon, choisissez une autre option.  Cliquez sur **Suivant**.

	![Configure MySQL][MySQLConfig8]

20. Sélectionnez **Installer en tant que service Windows** et **Démarrer le serveur MySQL automatiquement**.  Sélectionnez également **Inclure le répertoire Bin dans la variable PATH de Windows**. Cliquez sur **Suivant**.

	![Configure MySQL][MySQLConfig9]

21. Entrez le mot de passe racine. Ne cochez pas la case **Activer l'accès racine à partir d'ordinateurs distants** ou **Créer un compte anonyme**.  Cliquez sur **Suivant**.

	![Configure MySQL][MySQLConfig10]

22. Cliquez sur **Exécuter**, puis attendez la fin de la configuration.

23. Cliquez sur **Terminer**.

24. Cliquez sur **Démarrer**, puis sélectionnez **Client de ligne de commande MySQL 5.x** pour démarrer le client de ligne de commande.

25.  Entrez le mot de passe racine dans l'invite (que vous avez défini lors d'une étape précédente) pour ouvrir une invite de commandes vous permettant d'écrire des instructions SQL afin d'interagir avec la base de données.

26. Pour créer un utilisateur MySQL, exécutez la commande suivante dans l'invite **mysql>** :

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Les points-virgules (;) à la fin des lignes sont requis pour terminer les commandes.

27. Pour créer une base de données et accorder les autorisations à l'utilisateur mysqluser, exécutez les commandes suivantes :

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Notez que les noms et les mots de passe des utilisateurs de base de données sont uniquement utilisés par les scripts pour se connecter à la base de données.  Les noms des comptes d'utilisateurs de base de données ne représentent pas nécessairement les comptes d'utilisateurs de l'ordinateur.

28. Pour vous connecter à partir d'un autre ordinateur, exécutez la commande suivante :

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	où ip-address est l'adresse IP de l'ordinateur à partir duquel vous vous connectez à MySQL.
	
29. Pour quitter le client de ligne de commande MySQL, exécutez la commande suivante :

		quit

30. Après l'installation de MySQL, configurez un point de terminaison pour que MySQL soit accessible à distance. Connectez-vous au [portail de gestion Azure][AzurePreviewPortal]. Dans le portail Azure, cliquez sur **Machines virtuelles**, sur le nom de votre nouvelle machine virtuelle, sur **Points de terminaison**, puis sur **Ajouter un point de terminaison**.

31. Sélectionnez **Ajouter un point de terminaison** et cliquez sur la flèche pour continuer.
	

32. Ajoutez un point de terminaison avec le nom " MySQL ", le protocole **TCP** et les ports **Public** et **Privé** définis sur " 3306 ". Cliquez sur la coche. Cela vous permet d'accéder à distance à MySQL.
	

33. Testez votre connexion à distance à MySQL.  À partir d'un ordinateur local exécutant MySQL, exécutez une commande similaire à la suivante pour vous connecter en tant qu'utilisateur **mysqluser** :

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	Par exemple, si votre machine virtuelle s'appelle " testwinvm ", exécutez cette commande :

		mysql -u mysqluser -p -h testwinvm.cloudapp.net

##Ressources
Pour plus d'informations sur MySQL, consultez la [documentation MySQL](http://dev.mysql.com/doc/).

[AzurePortal]: http://manage.windowsazure.com
[MySQLDownloads]: http://www.mysql.com/downloads/mysql/


[MySQLConfig5]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig5.png
[MySQLConfig7]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig7.png
[MySQLConfig8]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig8.png
[MySQLConfig9]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig9.png
[MySQLConfig10]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig10.png


<!--HONumber=35_1-->
