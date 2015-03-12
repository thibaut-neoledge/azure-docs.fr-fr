<properties 
	pageTitle="Création d'une pile LAMP avec Microsoft Azure" 
	description="Learn how to create a LAMP Stack with Microsoft Azure using Azure virtual machines (VMs) running Linux." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="NingKuang" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/27/2014" 
	ms.author="ningk"/>

#Création d'une pile LAMP avec Microsoft Azure 

Une pile " LAMP " est un groupe de logiciels open source généralement installés ensemble pour permettre à un serveur d'héberger des sites web dynamiques et des applications web. Ce terme est en fait un acronyme qui représente le système d'exploitation Linux avec le serveur web Apache. Les données de site sont stockées dans une base de données MySQL et le contenu dynamique est traité par PHP.  

Dans ce guide, nous allons installer une pile LAMP sur une image Linux et la déployer sur Microsoft Azure.  

Vous apprendrez à effectuer les opérations suivantes :  

-	Création d'une machine virtuelle Azure.
-	Préparation de la machine virtuelle pour la pile LAMP.
-	Installation des logiciels requis par votre serveur LAMP sur la machine virtuelle.

Nous partons du principe que le lecteur possède déjà un abonnement Azure.  Si ce n'est pas le cas, vous pouvez vous inscrire pour obtenir une évaluation gratuite sur [http://azure.microsoft.com](http://azure.microsoft.com). Si vous avez un abonnement MSDN, consultez [Tarifs préférentiels Microsoft Azure : avantages MSDN, MPN et Bizspark](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Pour en savoir plus sur Azure, consultez [Présentation d'Azure](http://azure.microsoft.com/overview/what-is-azure/).

Outre cette rubrique, la vidéo suivante présente les détails de l'utilisation de la pile LAMP sur Microsoft Azure :  

> [AZURE.VIDEO lamp-stack-on-azure-vms-with-guy-bowerman]

##Phase 1 : création d'une image
Lors de cette phase, vous allez créer la machine virtuelle à l'aide d'une image Linux dans Azure.  

###Étape 1 : générer une clé d'authentification SSH
SSH est un outil important pour les administrateurs système. Toutefois, s'appuyer sur un mot de passe défini par un humain pour la sécurité n'est pas toujours judicieux. Une clé SSH forte permet de laisser l'accès à distance ouvert sans se soucier des mots de passe. La méthode se compose d'une authentification avec chiffrement asymétrique. La clé privée de l'utilisateur est celle qui accorde l'authentification. Vous pouvez même verrouiller le compte de l'utilisateur pour interdire complètement l'authentification par mot de passe.    

Pour générer la clé d'authentification SSH, procédez comme suit.

-	Téléchargez et installez puttygen à partir de l'emplacement suivant : [http://www.chiark.greenend.org.uk/~sgtatham/](http://www.chiark.greenend.org.uk/~sgtatham/)putty/download.html 
-	Exécutez puttygen.exe.
-	Cliquez sur **Generate** pour générer les clés. Dans le processus, vous pouvez augmenter le caractère aléatoire en déplaçant la souris sur la zone vide dans la fenêtre.  
![][1]
-	Après le processus de génération, Puttygen.exe affiche votre clé générée. Par exemple :  
![][2]
-	Sélectionnez et copiez la clé publique dans **Key** et enregistrez-la dans un fichier nommé **publicKey.pem**. Ne cliquez pas sur **Save public key**, car le format de fichier de la clé publique enregistrée est différent de la clé publique que nous voulons.
-	Cliquez sur **Save private key** et enregistrez-la dans un fichier nommé **privateKey.ppk**. 

###Étape 2 : création de l'image dans le portail Azure Preview.
Dans le [portail Azure Preview](https://portal.azure.com/), cliquez sur **Nouveau** dans la barre des tâches et créez une image en suivant ces instructions, en choisissant l'image Linux selon vos besoins. Cet exemple utilise l'image Ubuntu 14.04. 

![][3] 

Pour **Nom d'hôte**, spécifiez le nom de l'URL que les clients Internet utiliseront pour accéder à cette machine virtuelle. Définissez la dernière partie du nom DNS, par exemple LAMPDemo, et Azure génère l'URL comme Lampdemo.cloudapp.net.   

Pour **Nom d'utilisateur**, choisissez un nom que vous utiliserez ultérieurement pour vous connecter à la machine virtuelle.   

Pour **Clé d'authentification SSH**, copiez la valeur de clé à partir du fichier **publicKey.pem** qui contient la clé publique générée par puttygen.  

![][4]
  
Configurez d'autres paramètres selon les besoins, puis cliquez sur **Créer**.

##Phase 2 : préparation de votre machine virtuelle pour la pile LAMP
Lors de cette phase, vous allez configurer un point de terminaison pour le trafic web, puis vous connecter à votre nouvelle machine virtuelle.

###Étape 1 : ouverture du port HTTP pour autoriser l'accès web
Les points de terminaison dans Azure se composent d'un protocole (TCP ou UDP) et d'un port public et privé. Le port privé est celui que le service écoute sur la machine virtuelle. Le port public est celui que le service cloud Azure écoute en externe pour le trafic Internet. Dans certains cas, il s'agit du même numéro de port.  

Le port TCP 80 est le numéro de port par défaut sur lequel Apache écoute. L'ouverture de ce port avec un point de terminaison Azure vous permettra (à vous et à d'autres clients Internet) d'accéder au serveur web Apache.  

Dans le portail Azure Preview, cliquez sur **Parcourir -> Machine virtuelle**, puis cliquez sur la machine virtuelle que vous avez créée.

![][5] 

Pour ajouter un point de terminaison à une machine virtuelle, cliquez sur la zone **Points de terminaison**.

![][6]
 
Cliquez sur **Ajouter**. Quand vous approvisionnez une nouvelle machine virtuelle, vous pouvez activer ou désactiver des points de terminaison selon les besoins.   

Configurez le point de terminaison :  

1.	Tapez un nom pour le point de terminaison dans **Point de terminaison**.
2.	Tapez 80 dans **Port public**. Si vous avez modifié le port d'écoute par défaut d'Apache, vous devez mettre à jour Port privé pour qu'il s'agisse du même que le port d'écoute Apache. 
3.	Tapez 80 dans **Port public**. Par défaut, le trafic HTTP utilise le port 80.
Si vous affectez le port 80, vous n'avez pas besoin d'inclure le numéro de port dans l'URL qui vous permet d'accéder au service web Apache. Par exemple, http://lampdemo.cloudapp.net.
Si vous affectez une autre valeur pour le port d'écoute Apache, par exemple 81, vous devez ajouter le numéro de port à l'URL pour accéder au service web Apache. Par exemple, http://lampdemo.cloudapp.net:81/.

![][7] 

Cliquez sur **OK** pour ajouter le point de terminaison à votre machine virtuelle.




###Étape 2 : connexion à l'image que vous avez créée
Vous pouvez choisir n'importe quel outil SSH pour vous connecter à votre machine virtuelle. Dans cet exemple, nous utilisons Putty.  

Tout d'abord, obtenez le nom DNS de votre machine virtuelle à partir du portail Azure Preview. Cliquez sur **Parcourir -> Machines virtuelles->** nom de votre machine virtuelle **-> Propriétés**, puis regardez dans le champ **Nom de domaine** de la vignette **Propriétés**. 

Obtenez le numéro de port pour les connexions SSH à partir du champ **SSH**.   Voici un exemple.  

![][8]
  
Téléchargez Putty [ici](http://www.putty.org/).  

Après le téléchargement, cliquez sur le fichier exécutable PUTTY.EXE. Configurez les options de base avec le nom d'hôte et le numéro de port obtenus à partir des propriétés de votre machine virtuelle. Voici un exemple :
 
![][9]

Dans le volet gauche, cliquez sur **Connexion -> SSH -> Authentification**, puis cliquez sur **Parcourir** pour spécifier l'emplacement du fichier **privateKey.ppk** qui contient la clé privée générée par puttygen lors de la Phase 1 : création d'une image. Voici un exemple :  

![][10]
 
Cliquez sur **Ouvrir**. Une boîte de message peut s'afficher. Si vous avez configuré le nom DNS et le numéro de port correctement, cliquez sur **Oui**.
  
![][11]


Les éléments suivants doivent s'afficher. 
 
![][12]

Entrez le nom d'utilisateur spécifié lors de la création de la machine virtuelle lors de la Phase 1 : création d'une image. Vous verrez quelque chose comme suit :  
 
![][13]

##Phase 3 : installation de la pile LAMP

Selon la distribution de Linux utilisée pour créer votre machine virtuelle, il existe plusieurs façons d'installer la pile LAMP. Les sections suivantes contiennent les étapes classiques sur certains systèmes d'exploitation Linux courants.

###Red Hat, base CentOS

####Installer Apache
Pour installer Apache, ouvrez terminal et exécutez la commande suivante :  

	sudo yum install httpd

Une fois l'installation terminé, démarrez Apache avec cette commande :  

	sudo service httpd start
####Tester Apache
Pour vérifier si Apache est correctement installé, recherchez le nom DNS de votre serveur Apache (pour l'exemple d'URL dans cet article, http://lampdemo.cloudapp.net/). La page doit afficher les mots " It works! "
![][14]

####Résolution des problèmes 
Si Apache est en cours d'exécution mais que vous ne voyez pas la page Apache par défaut ci-dessus, vérifiez ce qui suit :  

-	Port/adresse d'écoute du service web Apache
	-	Vérifiez le paramètre de point de terminaison pour votre machine virtuelle Azure. Assurez-vous que la configuration du point de terminaison est correcte. Consultez les instructions de la Phase 1 : création d'une image dans cet article.
	-	Ouvrez /etc/httpd/conf/httpd.conf et recherchez la chaîne " Listen ". Assurez-vous que le port d'écoute Apache est identique au Port privé que vous avez configuré pour votre point de terminaison. Le port par défaut pour Apache est 80. Voici un exemple.  

			......
			......
				# prevent Apache from glomming onto all bound IP addresses (0.0.0.0)
				#
				#Listen 12.34.56.78:80
				Listen 80
				#
				# Dynamic Shared Object (DSO) Support
			......
			......  

-	Pare-feu, configuration iptables  
Si vous voyez la page Apache par défaut à partir de l'hôte local, il est possible que le port sur lequel Apache écoute soit bloqué par le pare-feu. Vous pouvez utiliser l'outil w3m pour parcourir la page web Apache. Les commandes suivantes installent w3m et accèdent à la page par défaut Apache :  

		sudo yum  install w3m w3m-img  
		w3m http://localhost

	Si le problème est dû au pare-feu ou à iptables, ajoutez les lignes suivantes à /etc/sysconfig/iptables :  

		-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
		-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT

	Notez que la deuxième ligne est nécessaire uniquement pour le trafic https.  

	Assurez-vous que ces lignes sont au-dessus de toutes les lignes qui limiteraient globalement l'accès, telles que les suivantes :  

		-A INPUT -j REJECT --reject-with icmp-host-interdit  

	Pour que les nouveaux paramètres prennent effet, exécutez la commande suivante :  

		service iptables restart

####Installation de MySQL
Pour installer MySQL, ouvrez terminal et exécutez ces commandes :  

	sudo yum install mysql-server
	sudo service mysqld start

Pendant l'installation, MySQL vous demande votre autorisation deux fois. Une fois que vous avez répondu Oui aux deux requêtes, MySQL s'installe.

####Configurer MySQL
Une fois l'installation terminée, vous pouvez définir un mot de passe MySQL racine avec ce qui suit :  

	sudo /usr/bin/mysql_secure_installation  

L'invite vous demande votre mot de passe racine actuel.    

Étant donné que vous venez d'installer MySQL, vous n'en avez sans doute pas. Laissez donc cette zone vide en appuyant sur la touche Entrée.  

	Enter current password for root (enter for none): 
	OK, successfully used password, moving on...  

Vous serez invité à définir un mot de passe racine. Continuez, choisissez Y et suivez les instructions.  

CentOS automatise le processus de configuration de MySQL et vous pose une série de questions auxquelles vous devez répondre Oui ou Non. Ces questions sont présentées ci-dessous. Choisissez Y ou N pour votre configuration. À la fin, MySQL est rechargé et implémente les modifications.  

	By default, a MySQL installation has an anonymous user, allowing anyone
	to log into MySQL without having to have a user account created for
	them.  This is intended only for testing, and to make the installation
	go a bit smoother.  You should remove them before moving into a
	production environment.
	
	Remove anonymous users? [Y/n] y                                            
	 ... Success!
	
	Normally, root should only be allowed to connect from 'localhost'.  This
	ensures that someone cannot guess at the root password from the network.
	
	Disallow root login remotely? [Y/n] y
	... Success!
	
	By default, MySQL comes with a database named 'test' that anyone can
	access.  This is also intended only for testing, and should be removed
	before moving into a production environment.
	
	Remove test database and access to it? [Y/n] y
	 - Dropping test database...
	 ... Success!
	 - Removing privileges on test database...
	 ... Success!
	
	Reloading the privilege tables will ensure that all changes made so far
	will take effect immediately.
	
	Reload privilege tables now? [Y/n] y
	 ... Success!
	
	Cleaning up...
	
	All done!  If you've completed all of the above steps, your MySQL
	installation should now be secure.
	
	Thanks for using MySQL!  

####Installation de PHP
PHP est un langage de script web open source largement utilisé pour créer des pages web dynamiques.  

Pour installer PHP sur votre machine virtuelle, ouvrez terminal et exécutez la commande suivante :  

	sudo yum install php php-mysql  

Réponses " y " pour télécharger des packages logiciels. Répondez " y " à la demande d'importation de clé GPG 0xE8562897 " CentOS-5 Key (CentOS 5 Official Signing Key) ". PHP s'installe.   

	warning: rpmts_HdrFromFdno: Header V3 DSA signature: NOKEY, key ID e8562897
	updates/gpgkey                                                                                                                                                                       | 1.5 kB     00:00
	Importing GPG key 0xE8562897 "CentOS-5 Key (CentOS 5 Official Signing Key) <centos-5-key@centos.org>" from /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5
	Is this ok [y/N]: y  

###Debian, base Ubuntu
Cela a été testé sur Ubuntu 14.04.  

Ubuntu est basé sur Debian. Vous pouvez installer la pile LAMP de la même manière que la série Red Hat. Pour simplifier les étapes, utilisez l'outil Tasksel.

Tasksel est un outil Debian/Ubuntu qui installe plusieurs packages connexes comme tâche coordonnée sur votre système. Pour plus d'informations, consultez [Tasksel - Wiki d'aide communautaire](https://help.ubuntu.com/community/Tasksel).

Utilisez tasksel pour installer les logiciels requis pour la pile LAMP.

- Pour télécharger les listes de packages à partir des référentiels et les mettre à jour pour obtenir des informations sur les dernières versions des packages et leurs dépendances :  

		sudo apt-get update
-	Pour installer la pile LAMP Ubuntu à l'aide de Tasksel :  

		sudo apt-get install tasksel
		sudo tasksel install lamp-server

Ensuite, exécutez l'Assistant et choisissez votre **Mot de passe racine MySQL**.

![][15] 


##Test de LAMP sur votre serveur
Vous pouvez tester le système LAMP en créant une page d'informations php rapide.   

Tout d'abord, créez un fichier :  

	sudo nano /var/www/html/info.php  

Ajoutez la ligne suivante :  

	<?php
	phpinfo();
	?>  

Ensuite, enregistrez et quittez.  

Redémarrez Apache pour que toutes les modifications prennent effet sur votre ordinateur. Si le système d'exploitation de votre machine virtuelle est CentOS, exécutez la commande suivante pour redémarrer Apache :  

	sudo service httpd restart

Si le système d'exploitation de votre machine virtuelle est Ubuntu, exécutez la commande suivante pour redémarrer Apache :  

	sudo service apache2 restart  

Terminez en accédant à votre page d'informations php (pour l'exemple de serveur web dans cette rubrique, l'URL serait http://lampdemo.cloudapp.net/info.php).  

Votre fenêtre de navigateur doit ressembler à ceci :
 
![][16]

##Étapes supplémentaires

En général, vous modifierez certains paramètres par défaut pour préparer le déploiement d'applications web.  

###Autorisation de l'accès à distance à MySQL
Si vous avez plusieurs machines virtuelles installées avec MySQL et qu'elles doivent échanger des données, vous devez activer l'accès à distance MySQL et accorder les autorisations appropriées.  
 
**Format de référence de commande :**    

	grant [authority] on [databaseName].[tableName] to [username]@[login host] identified by "[passwd]"  

**Exemple :**  

	grant select,insert,update,delete on studentManage.student to user1@"%" Identified by "abc";

Vous devez également modifier le profil /etc/mysql/my.cnf. Si vous avez des lignes comme suit :  

	skip-networking
	bind-address = 127.0.0.1  

Vous devez les mettre en commentaire (ajouter un signe # au début des lignes), puis redémarrer MySQL.  

Pour ajouter un point de terminaison pour autoriser l'accès à distance, reportez-vous aux instructions de la Phase 1 : création d'une image pour créer un point de terminaison. Le numéro de port TCP d'accès à distance par défaut de MySQL est 3306. Voici un exemple :
 
![][17]

###Déploiement de vos applications web sur le serveur apache
Une fois la pile LAMP configurée avec succès, vous pouvez déployer votre application web existante sur le serveur web Apache (votre machine virtuelle). Les étapes sont identiques à celles du déploiement d'une application web existante sur un serveur web physique.

-	La racine du serveur web se trouve dans **/var/www/html**. Vous devez accorder des privilèges aux utilisateurs qui ont besoin de télécharger des fichiers dans ce dossier. L'exemple suivant montre comment ajouter une autorisation d'écriture à un groupe nommé lampappgroup et placer le nom d'utilisateur azureuser dans ce groupe :  
 
		sudo groupadd lampappgroup                      # Create a group
		sudo gpasswd -a azureuser lampappgroup    # Add azureuser to lampappgroup
		sudo chgrp lampappgroup /var/www/html/  # Change the ownership to group lampappgroup
		sudo chmod g+w /var/www/html/                 # grant write permission to group lampappgroup    

	>[AZURE.NOTE] Vous devrez peut-être vous reconnecter si vous souhaitez modifier un fichier dans /var/www/html/. 
-	Utilisez n'importe quel client SFTP (par exemple, FileZilla) pour vous connecter au nom DNS de votre machine virtuelle (par exemple, lampdemo.cloudapp.net) et accéder à /**var/www/html** pour publier votre site.  
![][18]

 

##Problèmes courants et dépannage

###Impossible d'accéder à une machine virtuelle avec Apache et Moodle à partir d'Internet

-	**Symptôme**
Apache est en cours d'exécution, mais vous ne voyez pas la page par défaut Apache avec votre navigateur.
-	**Cause principale possible**
	1.	Le port d'écoute Apache n'est pas identique au Port privé du point de terminaison de votre machine virtuelle pour le trafic web.</br>   
	Vérifiez vos paramètres de points de terminaison de port public et de port privé et assurez-vous que le Port privé est identique au port d'écoute Apache. Consultez la Phase 1 : création d'une image pour obtenir des instructions sur la configuration des points de terminaison pour votre machine virtuelle.</br>   
	Pour déterminer le port d'écoute Apache, ouvrez /etc/httpd/conf/httpd.conf (version Red Hat) ou /etc/apache2/ports.conf (version Debian), recherchez la chaîne " Listen ". Le port par défaut est 80.

	2.	Le pare-feu a désactivé le port d'écoute Apache.</br>  
	Si vous voyez la page Apache par défaut à partir de l'hôte local, il est probable que le port sur lequel Apache écoute soit bloqué par le pare-feu. Vous pouvez utiliser l'outil w3m pour parcourir la page web. Les commandes suivantes installent w3m et accèdent à la page par défaut Apache :  
  
			sudo yum  install w3m w3m-img
			w3m http://localhost 

-	**Solution**

	1.	Si le port d'écoute Apache n'est pas identique au Port privé du point de terminaison pour le trafic web sur la machine virtuelle, vous devez modifier le Port privé du point de terminaison pour qu'il soit identique au port d'écoute Apache.
	2.	Si le problème est dû au pare-feu/iptables, ajoutez les lignes suivantes à /etc/sysconfig/iptables :  
	
			-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
			-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT    

		Notez que la deuxième ligne est nécessaire uniquement pour le trafic https.   
 
		Assurez-vous que ces lignes sont au-dessus de toutes les lignes qui limiteraient globalement l'accès, telles que les suivantes :  

			-A INPUT -j REJECT --reject-with icmp-host-prohibited  

		Pour recharger les iptables, exécutez la commande suivante :  

			service iptables restart  

		Cela a été testé sur CentOS 6.3.

###Autorisation refusée quand vous téléchargez vos fichiers de projet vers /var/www/html/  

-	**Symptôme**
Quand vous utilisez un client SFTP (par exemple, FileZilla) pour vous connecter à votre machine virtuelle et que vous accédez à /var/www/html pour publier votre site, un message d'erreur semblable au suivant s'affiche :  

		status:	Listing directory /var/www/html
		Command:	put "C:\Users\liang\Desktop\info.php" "info.php"
		Error:	/var/www/html/info.php: open for write: permission denied
		Error:	File transfer failed

-	**Cause principale possible**
Vous n'êtes pas autorisé à accéder au dossier /var/www/html.  
-	**Solution**  
Vous devez obtenir l'autorisation à partir du compte racine. Vous pouvez modifier le propriétaire par défaut de ce dossier (la racine) et choisir le nom d'utilisateur que vous avez utilisé lors de l'approvisionnement de la machine. Voici un exemple avec le nom de compte azureuser :  

		sudo chown azureuser -R /var/www/html  

	Utilisez l'option -R pour appliquer aussi les autorisations pour tous les fichiers contenus dans un répertoire.  

	Notez que cette commande fonctionne également pour les répertoires. L'option -R modifie les autorisations pour tous les fichiers et répertoires contenus dans le répertoire. Voici un exemple :  

		sudo chown -R username:group directory  

	Cette commande modifie le propriétaire (utilisateur et groupe) pour tous les fichiers et répertoires contenus dans le répertoire et le répertoire lui-même.  

	La commande suivante modifie uniquement l'autorisation du répertoire mais laisse intacts les fichiers et dossiers dans le répertoire.  

		sudo chown username:group directory  

###Impossible de déterminer avec fiabilité le nom de domaine complet du serveur 

-	**Symptôme**
Quand vous redémarrez le serveur Apache en exécutant l'une des commandes suivantes :  

		sudo /etc/init.d/apache2 restart  # Debian release  

	ou   

		sudo /etc/init.d/httpd restart   # Red Hat release  

	Vous obtenez l'erreur suivante :  

		Restarting web server apache2
		apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName
		... waiting apache2:
		Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName  

-	**Cause principale possible**
	Vous n'avez pas défini le nom du serveur Apache.

-	**Solution**  
	Insérez une ligne " ServerName localhost " dans le fichier httpd.conf (version Red Hat) ou apache2.conf (version Debian) dans /etc/apache2 et redémarrez Apache. La notice disparaît. 



[1]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-01.png
[2]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-02.png
[3]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-03.png
[4]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-04.png
[5]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-05.png
[6]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-06.png
[7]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-07.png
[8]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-08.png
[9]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-09.png
[10]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-10.png
[11]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-11.png
[12]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-12.png
[13]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-13.png
[14]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-14.png
[15]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-15.png
[16]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-16.png
[17]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-17.png
[18]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-18.jpg




<!--HONumber=42-->
