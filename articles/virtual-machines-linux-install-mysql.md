<properties
	pageTitle="Installation de MySQL sur Azure"
	description="Apprenez à installer la pile MySQL sur une machine virtuelle Linux dans Azure. Vous pouvez l&#39;installer sur Ubuntu ou sur CentOS."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/12/2015"
	ms.author="mingzhan"/>


#Installation de MySQL sur Azure


Dans cette rubrique, nous partons du principe que le lecteur possède déjà un compte Azure. Si tel n’est pas le cas, il est recommandé de s’inscrire sur le site [Azure](http://azure.microsoft.com).



##Création d’une image de machine virtuelle dans Microsoft Azure
Ici, nous allons créer une nouvelle machine virtuelle à partir du portail de gestion Microsoft Azure.
###Génération d’une « clé d’authentification SSH »
Nous aurons besoin de la clé SSH pour accéder au portail Azure.


- Téléchargez et installez Puttygen à partir d’[ici](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 
- Exécutez puttygen.exe.
- Cliquez sur le bouton « Générer » pour générer les clés.


 ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p01.png)
 
- Une fois la clé générée, celle-ci s’affiche comme indiqué ci-dessous. 
 
 ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p02.png)

- Copiez la clé publique et enregistrez-la dans un fichier nommé « publicKey.key ». Le format de la clé publique enregistrée est différent de la clé publique que nous voulons, c’est pourquoi il ne suffit pas de cliquer sur le bouton « Enregistrer la clé publique ».
- Cliquez sur « Enregistrer la clé privée » et enregistrez-la dans un fichier nommé « privateKey.ppk ». 

###Connexion au portail Azure

Accédez à https://portal.azure.com/ et connectez-vous.

###Création d’une machine virtuelle Linux

Cliquez sur « Nouveau » dans la partie inférieure gauche, créez une image en suivant les instructions tout en choisissant l’image Linux qui correspond à vos besoins. Ici, je choisis Ubuntu 14.04 comme exemple.

  ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p03.png)

###Configuration de NOM D’HÔTE

Pour « NOM d’hôte » : il s’agit de l’URL que vous pouvez utiliser pour accéder à votre machine virtuelle. Il vous suffit de spécifier le nom DNS, par exemple « mysqlnode1 », pour qu’Azure génère l’URL sous la forme « mysqlnode1.cloudapp.net » ; pour « CLÉ D’AUTHENTIFICATION SSH », c’est la clé publique générée par puttygen que vous devez copier à partir du contenu dans le fichier « publicKey.key ».

  ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p04.png)
  

##Ouverture du port par défaut de MySQL
Les points de terminaison dans Microsoft Azure se composent d’un protocole et d’un port public et privé. Le port privé est le port que le service écoute sur l’ordinateur local. Le port public est celui que le service écoute de manière externe. Le port 3306 est le numéro de port par défaut qui sera écouté par MySQL. Cliquez sur « PARCOURIR » ⇒ « MACHINE VIRTUELLE », puis cliquez sur l’image que vous avez créée.
 
   ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p05.png)


##connexion à l'image que vous avez créée
Vous pouvez choisir n’importe quel outil SSH pour vous connecter à votre machine virtuelle. Dans cet exemple, nous utilisons Putty.
 

- Commencez par télécharger Putty. L’URL de téléchargement se trouve ici.
- Après le téléchargement, cliquez sur le fichier exécutable « PUTTY.EXE ». Définissez les paramètres comme suit.


     The “Host Name (or IP address)” is the URL as “DNS NAME” when you create an image.
     
     The “Port” we can chose 22.  This is default port of SSH services.

   ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p06.png)
 
- Avant de sélectionner Ouvrir, cliquez sur l’onglet Connexion \> SSH \> Auth pour parcourir votre fichier généré par Puttygen et qui contient la clé privée. Consultez la capture d'écran ci-dessous pour le champ à remplir.

   ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p07.png)
 
- Puis cliquez sur « Ouvrir ». Il se peut qu’une boîte de dialogue, comportant un message indiquant que la machine à laquelle vous vous connectez n’est peut-être pas celle à laquelle vous souhaitez vous connecter, s’affiche. Si vous avez configuré le nom DNS et le numéro de port correctement, cliquez sur « Oui ».
  
 ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p08.png)

- Une fois cette opération effectuée, les informations suivantes s’afficheront. 
 
 ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p09.png)


##Installation de MySQL sur votre machine virtuelle
Trois méthodes d’installation sont prises en charge pour MySQL : les packages binaires, les packages rpm et les packages sources. Pour des questions de performances, nous allons utiliser le package rpm pour MySQL 5.6 comme exemple pour cet article. Les performances de MySQL 5.6 ont été considérablement améliorées par rapport à MySQL5.5. Des informations supplémentaires sont disponibles [ici](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).


###Installation de MySQL5.6 sur Ubuntu ou Debian
Nous allons utiliser Ubuntu 14.04 LTS comme exemple pour cet article.

- Étape 1 : Installation de MySQL Server 5.6

    Installation de mysql-server 5.6 à l’aide de la commande apt-get

              # azureuser@mysqlnode1:~$ sudo apt-get update
              # azureuser@mysqlnode1:~$ sudo apt-get -y install mysql-server-5.6

    Pendant l’installation, une boîte de dialogue contextuelle s’affiche vous demandant de définir le mot de passe racine MySQL. Vous devrez spécifier le nouveau mot de passe racine utilisateur MySQL. Vous trouverez ci-dessous la capture d’écran.

 ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p10.png)

    Confirm the password again when it is asked.

 ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p11.png)
 
- Étape 2 : Connexion au serveur MySQL

    Une fois l’installation du serveur MySQL terminée, le service MySQL est démarré automatiquement. Vous pouvez vous connecter au serveur MySQL à l’aide de la racine de l’utilisateur. Pour vous connecter au serveur MySQL, utilisez la commande ci-dessous. Celle-ci demande le mot de passe racine mysql que vous avez défini lors de l’installation du serveur mysql.

             # azureuser@mysqlnode1:~$ mysql -uroot -p

- Étape 3 : Vérification du service MySQL sur la machine virtuelle
    
    Après la connexion, assurez-vous que le service MySQL fonctionne. Vous pouvez utiliser les commandes suivantes pour démarrer/redémarrer le service.

    \(a\) pour obtenir l’état du service MySQL

             #sudo service mysql status

    \(b\) pour démarrer le service MySQL

             #sudo service mysql start

    \(c\) pour arrêter le service MySQL

             #sudo service mysql stop

    \(b\) pour redémarrer le service MySQL

             #sudo service mysql restart


###Installation de MySQL sur la famille de systèmes d’exploitation Redhat ou Oracle Linux
- Étape 1 : Ajout du référentiel MySQL Yum Pour obtenir les autorisations racine, exécutez la commande : 

            #sudo su -
            #[root@azureuser ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm 
            #[root@azureuser ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm 

- Étape 2 : Sélection d’une série de version
 
            #[root@azureuser ~]# vim /etc/yum.repos.d/mysql-community.repo

    Il s’agit d’une entrée habituelle pour un sous-référentiel de série de version dans le fichier :

        \# *Enable to use MySQL 5.6*

        [mysql56-community]
        name=MySQL 5.6 Community Server

        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/

        enabled=1

        gpgcheck=1

        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

- Étape 3 : Installation de MySQL avec Yum Installez MySQL à l’aide de la commande suivante :

           #[root@azureuser ~]#yum install mysql-community-server 

    Cette commande installe le package pour le serveur MySQL, ainsi que les autres packages requis.

- Étape 4 : Vérification de l’état d’exécution de MySQL

    Vous pouvez vérifier l’état du serveur MySQL à l’aide de la commande suivante :
   
           #[root@azureuser ~]#service mysqld status

    Vous pouvez vérifier si le port par défaut du serveur MySQL est en cours d’exécution :

           #[root@azureuser ~]#netstat  –tunlp|grep 3306

- Étape 5 : Démarrage et arrêt du serveur MySQL

    Démarrez le serveur MySQL à l’aide de la commande suivante :

           #[root@azureuser ~]#service mysqld start

    Arrêtez le serveur MySQL à l’aide de la commande suivante :

           #[root@azureuser ~]#service mysqld stop

    Pour que MySQL démarre au démarrage du système, exécutez la commande suivante :

           #[root@azureuser ~]#chkconfig mysqld on


###Installation de MySQL sur Suse Linux

- Étape 1 : Installation de MySQL Server

    Pour élever les autorisations, exécutez la commande :

           #sudo su -

    Installez MySQL à l’aide de la commande suivante :

           #mysql-test:~ # zypper update

           #mysql-test:~ # zypper install mysql-server mysql-devel mysql

- Étape 2 : Vérification de l’état d’exécution de MySQL

    Vous pouvez vérifier l’état du serveur MySQL à l’aide de la commande suivante :

           #mysql-test:~ # rcmysql status

    Vous pouvez vérifier si le port par défaut du serveur MySQL est en cours d’exécution ;

           #mysql-test:~ # netstat  –tunlp|grep 3306

- Étape 3 : Démarrage et arrêt du serveur MySQL

    Démarrez le serveur MySQL à l’aide de la commande suivante :

           #mysql-test:~ # rcmysql start

    Arrêtez le serveur MySQL à l’aide de la commande suivante :

           #mysql-test:~ # rcmysql stop

    Pour que MySQL démarre au démarrage du système, exécutez la commande suivante :

           #mysql-test:~ # insserv mysql
<!--HONumber=52-->
