<properties linkid="manage-windows-common-tasks-install-mysql" urlDisplayName="Install MySQL" pageTitle="Create a virtual machine running MySQL in Azure " metaKeywords="Azure virtual machines, Azure Windows Server, Azure installing MySQL, Azure configuring MySQL, Azure databases" description="Create an Azure virtual machine running Windows Server 2008 R2, and then install and configure a MySQL database on the virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running Windows Server 2008 R2 in Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="12/15/2013" ms.author="kathydav"></tags>

# Installation de MySQL sur une machine virtuelle exécutant Windows Server 2008 R2 sur Azure

[MySQL][] est une base de données SQL open source connue. À l'aide du [portail de gestion Azure][], vous pouvez créer une machine virtuelle exécutant Windows Server 2008 R2 à partir de la bibliothèque d'images. Vous pouvez alors installer et configurer une base de données MySQL sur la machine virtuelle.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   créer une machine virtuelle exécutant Windows Server 2008 R2 dans le portail de gestion ;

-   installer et exécuter MySQL Community Server sur la machine virtuelle.

## Création d'une machine virtuelle exécutant Windows Server 2008 R2

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal][]]

## Association d'un disque de données

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal][]]

## Installation et exécution de MySQL Community Server sur la machine virtuelle

Pour installer, configurer et exécuter MySQL Community Server, procédez comme suit :

1.  Une fois que vous êtes connecté à la machine virtuelle à l'aide du Bureau à distance, ouvrez **Internet Explorer** à partir du menu **Démarrer**.

2.  Sélectionnez le bouton **Outils** dans le coin supérieur droit. Dans **Options Internet**, sélectionnez l'onglet **Sécurité**, l'icône **Sites de confiance**, puis cliquez sur le bouton **Sites**. Ajoutez *http://\*.mysql.com* à la liste des sites de confiance.

3.  Accédez à la page [Téléchargement de MySQL Community Server][].

4.  Sélectionnez **Microsoft Windows** dans le menu déroulant **Plateforme**, puis cliquez sur **Sélectionner**.

5.  Recherchez la version la plus récente de **Windows (x86, 64 bits), Programme d'installation de MSI**, puis cliquez sur **Télécharger**.

6.  Sélectionnez **Non merci, démarrer simplement mon téléchargement !** (ou inscrivez-vous pour obtenir un compte). Si vous y êtes invité, sélectionnez un site miroir pour télécharger le programme d'installation MySQL et l'enregistrer sur le Bureau.

7.  Double-cliquez sur le fichier d'installation sur le Bureau pour commencer l'installation.

8.  Cliquez sur **Suivant**.

    ![Installation MySQL][]

9.  Acceptez le contrat de licence, puis cliquez sur **Suivant**.

    ![Installation MySQL][1]

10. Cliquez sur **Par défaut** pour installer les fonctionnalités communes.

    ![Installation MySQL][2]

11. Cliquez sur **Installer**.

    ![Installation MySQL][3]

12. Démarrez l'Assistant Configuration MySQL et cliquez sur **Suivant**.

    ![Configurer MySQL][]

13. Sélectionnez **Configuration détaillée**, puis cliquez sur Suivant.

    ![Configurer MySQL][4]

14. Sélectionnez **Ordinateur serveur** si vous prévoyez d'exécuter MySQL avec d'autres applications sur le serveur ou sélectionnez l'option qui correspond le mieux à vos besoins. Cliquez sur **Suivant**.

    ![Configurer MySQL][5]

15. Sélectionnez **Base de données multifonctionnelle** ou l'option qui correspond le mieux à vos besoins. Cliquez sur **Suivant**.

    ![Configurer MySQL][6]

16. Sélectionnez le lecteur de données que vous avez attaché lors des étapes précédentes.

    ![Configurer MySQL][7]

17. Sélectionnez **Aide à la décision (DSS)/OLAP** ou l'option qui correspond le mieux à vos besoins. Cliquez sur **Suivant**.

    ![Configurer MySQL][8]

18. Sélectionnez **Activer le réseau TCP/IP** et **Ajouter une exception au pare-feu pour ce port** (cela crée une règle de trafic entrant dans le Pare-feu Windows nommée **MySQL Server**).

    ![Configurer MySQL][9]

19. Sélectionnez **Meilleure prise en charge du multilinguisme** si vous devez stocker du texte dans de nombreuses langues ou sélectionnez l'option qui correspond le mieux à vos besoins. Cliquez sur **Suivant**.

    ![Configurer MySQL][10]

20. Sélectionnez **Installer en tant que service Windows** et **Démarrer le serveur MySQL automatiquement**. Sélectionnez également **Inclure le répertoire Bin dans la variable PATH de Windows**. Cliquez sur **Suivant**.

    ![Configurer MySQL][11]

21. Entrez le mot de passe racine. N'activez pas **Activer l'accès racine à partir d'ordinateurs distants** ou **Créer un compte anonyme**. Cliquez sur **Suivant**.

    ![Configurer MySQL][12]

22. Cliquez sur **Exécuter**, puis attendez la fin de la configuration.

    ![Configurer MySQL][13]

23. Cliquez sur **Terminer**.

    ![Configurer MySQL][14]

24. Cliquez sur **Démarrer**, puis sélectionnez **Client de ligne de commande MySQL 5.x** pour démarrer le client de ligne de commande.

25. Entrez le mot de passe racine dans l'invite (que vous avez défini lors d'une étape précédente) pour ouvrir une invite de commandes vous permettant d'écrire des instructions SQL afin d'interagir avec la base de données.

26. Pour créer un utilisateur MySQL, exécutez la commande suivante dans l'invite **mysql\>** :

        mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

    Notez que les points-virgules (;) à la fin des lignes sont indispensables pour finir les commandes.

27. Pour créer une base de données et accorder les autorisations à l'utilisateur `mysqluser`, exécutez les commandes suivantes :

        mysql> CREATE DATABASE testdatabase;
        mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

    Notez que les noms et les mots de passe des utilisateurs de base de données sont uniquement utilisés par les scripts pour se connecter à la base de données. Les noms des comptes d'utilisateurs de base de données ne représentent pas nécessairement les comptes d'utilisateurs de l'ordinateur.

28. Pour se connecter à partir d'un autre ordinateur, exécutez la commande suivante :

        mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

    où `ip-address` est l'adresse IP de l'ordinateur sur lequel vous vous connectez à MySQL.

29. Pour quitter le client de ligne de commande MySQL, exécutez la commande suivante :

        quit

30. Après l'installation de MySQL, vous devez configurer un point de terminaison pour que MySQL soit accessible à distance. Connectez-vous au [portail de gestion Azure][]. Dans le portail Azure, cliquez sur **Machines virtuelles**, puis sur le nom de votre nouvelle machine virtuelle, sur **Points de terminaison**, puis sur **Ajouter un point de terminaison**.

    ![Points de terminaison][]

31. Sélectionnez **Ajouter un point de terminaison**, puis cliquez sur la flèche pour continuer.

    ![Points de terminaison][15]

32. Ajoutez un point de terminaison avec le nom « MySQL », le protocole **TCP** ainsi que les ports **Public** et **Privé** définis sur « 3306 ». Cliquez sur la coche. Cela permet d'accéder à distance à MySQL.

    ![Points de terminaison][16]

33. Vous pouvez vous connecter à distance au MySQL s'exécutant sur votre machine virtuelle dans Azure. À partir d'un ordinateur local exécutant MySQL, exécutez la commande suivante pour vous connecter avec l'utilisateur **mysqluser** créé lors des étapes précédentes :

        mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

    Par exemple, avec la machine virtuelle créée dans ce didacticiel, la commande est :

        mysql -u mysqluser -p -h testwinvm.cloudapp.net

## Résumé

Ce didacticiel vous a montré comment créer une machine virtuelle Windows 2008 R2 et vous y connecter à distance. Vous avez également appris à installer et à configurer MySQL sur la machine virtuelle, à créer une base de données et un utilisateur MySQL. Pour plus d'informations sur MySQL, consultez la [documentation MySQL][].

  [MySQL]: http://www.mysql.com
  [portail de gestion Azure]: http://manage.windowsazure.com
  [create-and-configure-windows-server-2008-vm-in-portal]: ../includes/create-and-configure-windows-server-2008-vm-in-portal.md
  [attach-data-disk-windows-server-2008-vm-in-portal]: ../includes/attach-data-disk-windows-server-2008-vm-in-portal.md
  [Téléchargement de MySQL Community Server]: http://www.mysql.com/downloads/mysql/
  [Installation MySQL]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall1.png
  [1]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall2.png
  [2]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall3.png
  [3]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall4.png
  [Configurer MySQL]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig1.png
  [4]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig2.png
  [5]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig3.png
  [6]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig4.png
  [7]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig5.png
  [8]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig6.png
  [9]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig7.png
  [10]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig8.png
  [11]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig9.png
  [12]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig10.png
  [13]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig11.png
  [14]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig12.png
  [Points de terminaison]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL0.png
  [15]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL1.png
  [16]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL.png
  [documentation MySQL]: http://dev.mysql.com/doc/
