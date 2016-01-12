<properties
	pageTitle="Configuration de Apache Tomcat sur une machine virtuelle Linux | Microsoft Azure"
	description="Apprenez à configurer Apache Tomcat7 avec une machine virtuelle Azure exécutant Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="NingKuang"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="ningk"/>

#Configuration de Tomcat7 sur un ordinateur virtuel Linux avec Microsoft Azure

Apache Tomcat (ou simplement Tomcat, auparavant également Jakarta Tomcat) est un serveur web open source et un conteneur de servlet développé par Apache Software Foundation (ASF). Tomcat implémente le Servlet Java et les spécifications Java Server Pages (JSP) de Sun Microsystems et fournit un environnement de serveur web HTTP Java pur dans lequel exécuter un code Java. Dans la configuration la plus simple, Tomcat s’exécute dans un processus de système d’exploitation unique. Ce processus exécute une machine virtuelle Java (JVM). Chaque requête HTTP d’un navigateur vers Tomcat est traitée comme un thread séparé dans le processus de Tomcat.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


Dans ce guide, vous installerez Tomcat7 sur une image Linux et le déploierez dans Microsoft Azure.

Vous apprendrez à effectuer les opérations suivantes :

-	Création d’une machine virtuelle dans Azure.
-	Préparation de la machine virtuelle pour tomcat7.
-	Installation de tomcat7.

Nous partons du principe que le lecteur possède déjà un abonnement Azure. Si ce n’est pas le cas, vous pouvez vous inscrire pour obtenir une évaluation gratuite sur [http://azure.microsoft.com](http://azure.microsoft.com). Si vous disposez d’un abonnement MSDN, consultez la page présentant les [tarifs préférentiels Microsoft Azure : avantages MSDN, MPN et Bizspark](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Pour en savoir plus sur Azure, consultez [Présentation d’Azure](http://azure.microsoft.com/overview/what-is-azure/).

Cette rubrique suppose que vous avez des connaissances de base relatives à tomcat et Linux.

##Phase 1 : Création d’une image
Lors de cette phase, vous allez créer une machine virtuelle à l’aide d’une image Linux dans Azure.

###Étape 1 : Générer une clé d’authentification SSH
SSH est un outil important pour les administrateurs système. Toutefois, une configuration de la sécurité d’accès basée sur un mot de passe déterminé par l’homme n’est pas recommandée. Les utilisateurs malveillants peuvent s’introduire dans votre système si vous disposez d’un nom d’utilisateur et d’un mot de passe faibles.

Toutefois, il existe un moyen de laisser l’accès à distance ouvert. Vous n’aurez donc pas à vous soucier des mots de passe. La méthode se compose d’une authentification avec chiffrement asymétrique. La clé privée de l’utilisateur est celle qui accorde l’authentification. Vous pouvez même verrouiller le compte de l’utilisateur pour interdire complètement l’authentification par mot de passe.

Un autre avantage de cette méthode est que vous n’avez pas besoin de mots de passe différents pour vous connecter à des serveurs différents. Vous pouvez vous authentifier à l’aide de la clé privée personnelle sur tous les serveurs, ce qui vous évite d’avoir à mémoriser plusieurs mots de passe.

Il est également possible de se connecter avec un mot de passe avec cette méthode.

Pour générer la clé d’authentification SSH, procédez comme suit.

1.	Téléchargez et installez Puttygen à partir de l’emplacement suivant : [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
2.	Exécutez PUTTYGEN.EXE.
3.	Cliquez sur **Generate** pour générer les clés. Dans le processus, vous pouvez augmenter le caractère aléatoire en déplaçant la souris sur la zone vide dans la fenêtre. ![][1]
4.	Après le processus de génération, Puttygen.exe affiche votre clé générée. Par exemple : ![][2]
5.	Sélectionnez et copiez la clé publique dans **Key** et enregistrez-la dans un fichier nommé publicKey.pem. Ne cliquez pas sur **Save public key**, car le format de fichier de la clé publique enregistrée est différent de la clé publique que nous voulons.
6.	Cliquez sur **Save private key** et enregistrez-la dans un fichier nommé privateKey.ppk.

###Étape 2 : Créer l’image dans le portail Azure.
Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Nouveau** dans la barre des tâches pour créer une image, en choisissant l'image Linux selon vos besoins. L’exemple suivant utilise l’image Ubuntu 14.04. ![][3]

Pour **Nom d’hôte**, spécifiez le nom de l’URL que les clients Internet utiliseront pour accéder à cette machine virtuelle. Définissez la dernière partie du nom DNS, par exemple tomcatdemo, et Azure génère l’URL comme tomcatdemo.cloudapp.net.

Pour **Clé d’authentification SSH**, copiez la valeur de clé à partir du fichier **publicKey.pem** qui contient la clé publique générée par puttygen. ![][4]

Configurez d’autres paramètres selon les besoins, puis cliquez sur Créer.

##Phase 2 : Préparation de la machine virtuelle pour Tomcat7
Lors de cette phase, vous allez configurer un point de terminaison pour le trafic tomcat, puis vous connecter à votre nouvelle machine virtuelle.
###Étape 2 : Ouverture du port HTTP pour autoriser l’accès web
Les points de terminaison dans Azure se composent d’un protocole (TCP ou UDP) et d’un port public et privé. Le port privé est celui que le service écoute sur la machine virtuelle. Le port public est celui que le service cloud Azure écoute en externe pour le trafic Internet entrant.

Le port TCP 8080 est le numéro de port par défaut sur lequel tomcat écoute. L’ouverture de ce port avec un point de terminaison Azure vous permettra (à vous et à d’autres clients Internet) d’accéder aux pages tomcat.

1.	Dans le portail Azure, cliquez sur **Parcourir** -> **Machine virtuelle**, puis cliquez sur la machine virtuelle que vous avez créée. ![][5]
2.	Pour ajouter un point de terminaison à votre machine virtuelle, cliquez sur la zone **Points de terminaison**. ![][6]
3.	Cliquez sur **Ajouter**.  
	1.	Entrez un nom pour le **Point de terminaison** dans Point de terminaison, puis tapez 80 dans **Port public**.  

		Si vous affectez le port 80, vous n’avez pas besoin d’inclure le numéro de port dans l’URL qui vous permet d’accéder à tomcat. Par exemple, http://tomcatdemo.cloudapp.net.

		Si vous affectez une autre valeur pour le port, par exemple 81, vous devez ajouter le numéro de port à l’URL pour accéder à tomcat. Par exemple, http://tomcatdemo.cloudapp.net:81/.
	2.	Tapez 8080 dans Port privé. Par défaut, tomcat écoute sur le port TCP 8080. Si vous avez modifié le port d’écoute par défaut de tomcat, vous devez mettre à jour Port privé pour qu’il s’agisse du même que le port d’écoute tomcat. ![][7]

4.	Cliquez sur **OK** pour ajouter le point de terminaison à votre machine virtuelle.



###Étape 2 : Connexion à l’image que vous avez créée
Vous pouvez choisir n’importe quel outil SSH pour vous connecter à votre machine virtuelle. Dans cet exemple, nous utilisons Putty.

Tout d’abord, obtenez le nom DNS de votre machine virtuelle à partir du portail Azure. **Cliquez sur Parcourir** -> **Machines virtuelles**-> nom de votre machine virtuelle -> **Propriétés**, puis regardez dans le champ **Nom de domaine** de la vignette **Propriétés**.

Obtenez le numéro de port pour les connexions SSH à partir du champ **SSH**. Voici un exemple. ![][8]

Téléchargez Putty [ici](http://www.putty.org/).

Après le téléchargement, cliquez sur le fichier exécutable PUTTY.EXE. Configurez les options de base avec le nom d’hôte et le numéro de port obtenus à partir des propriétés de votre machine virtuelle. Voici un exemple : ![][9]

Dans le volet gauche, cliquez sur **Connexion** -> **SSH** -> **Authentification**, puis cliquez sur **Parcourir** pour spécifier l’emplacement du fichier **privateKey.ppk** qui contient la clé privée générée par puttygen lors de la Phase 1 : Création d’une image. Voici un exemple : ![][10]

Cliquez sur **Ouvrir**. Une boîte de message peut s’afficher. Si vous avez configuré le nom DNS et le numéro de port correctement, cliquez sur **Oui**. ![][11]


Les éléments suivants doivent s’afficher : ![][12]

Entrez le nom d’utilisateur spécifié lors de la création de la machine virtuelle lors de la Phase 1 : Création d’une image. Vous verrez quelque chose comme suit : ![][13]





##Phase 3 : Installation du logiciel
Dans cette phase, vous installez l’environnement d’exécution Java, tomcat et d’autres composants de tomcat.

###Environnement d’exécution Java
Tomcat est écrit en Java. Il existe deux types de kits de développement Java (JDK) (OpenJDK et JDK Oracle). Vous pouvez choisir celui que vous souhaitez.

>AZURE.NOTE : les deux JDK ont pratiquement le même code pour les classes dans l’API Java, mais le code de la machine virtuelle est en fait différent. En ce qui concerne les bibliothèques, OpenJDK utilise généralement des bibliothèques libres tandis que celles d’Oracle sont fermées. Mais le JDK Oracle a plus de classes et quelques correctifs et il est plus stable qu’OpenJDK.

Les commandes suivantes téléchargent les différents JDK.

open-jdk

	sudo apt-get update  
	sudo apt-get install openjdk-7-jre  

oracle-jdk

-	Pour télécharger le JDK depuis le site Web d’Oracle :  

		wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  

-	Pour créer un répertoire contenant les fichiers JDK :

		sudo mkdir /usr/lib/jvm  

-	Pour extraire les fichiers JDK dans le répertoire /usr/lib/jvm :

		sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  

-	Pour définir le JDK Oracle comme JVM par défaut :

		sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  
		sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

####Test :
Vous pouvez utiliser une commande semblable à la suivante pour vérifier si l’environnement d’exécution Java est correctement installé :

	java -version  

Si vous avez installé open-jdk, vous devez voir un message semblable au suivant : ![][14]

Si vous avez installé oracle-jdk, vous devez voir un message semblable au suivant : ![][15]

###Tomcat7
Utilisez la commande suivante pour installer tomcat7 :

	sudo apt-get install tomcat7  

Si vous n’utilisez pas tomcat7, utilisez la variation appropriée de cette commande.

####Test :

Pour vérifier si Tomcat7 est correctement installé, recherchez le nom DNS de votre serveur Tomcat (pour l’exemple d’URL dans cet article, http://tomcatexample.cloudapp.net/). Si vous voyez une page similaire à celle-ci, Tomcat7 est correctement installé. ![][16]


###Installation d’autres composants de Tomcat
Il existe d’autres composants facultatifs de tomcat que vous pouvez également installer.

Utilisez la commande ** sudo apt-cache search tomcat7** pour afficher tous les composants disponibles. Les commandes suivantes sont des exemples d’installation de certaines parties utiles.

	sudo apt-get install tomcat7-admin      #admin web applications
	sudo apt-get install tomcat7-user         #tools to create user instances  

##Phase 4 : Configuration de Tomcat
Dans cette phase, vous administrez tomcat.
###Démarrage et arrêt de tomcat7
Le serveur de tomcat7 démarre automatiquement lorsque vous l’installez. Vous pouvez également le lancer vous-même avec la commande suivante :

	sudo /etc/init.d/tomcat7 start

Pour arrêter tomcat7 ：

	sudo /etc/init.d/tomcat7 stop

Pour afficher l’état de tomcat7 ：

	sudo /etc/init.d/tomcat7 status

Pour redémarrer les services de tomcat ：

	sudo /etc/init.d/tomcat7 restart

###Administration de Tomcat
Vous pouvez modifier le fichier de configuration utilisateur de Tomcat pour configurer vos informations d’identification d’administration avec la commande suivante :

	sudo vi  /etc/tomcat7/tomcat-users.xml   

Voici un exemple : ![][17]

>AZURE.NOTE : Création d’un mot de passe fort pour le nom d’utilisateur admin.

Après avoir modifié ce fichier, vous devez redémarrer les services tomcat7 avec la commande suivante pour vous assurer que les modifications prennent effet :

	sudo /etc/init.d/tomcat7 restart  

Ouvrez votre navigateur et entrez l’URL **http://<your tomcat server DNS name>/manager/html**. Par exemple, dans cet article, l’URL est http://tomcatexample.cloudapp.net/manager/html.

Une fois connecté, vous devez voir quelque chose de similaire à ce qui suit : ![][18]

##Problèmes courants

###Impossible d’accéder à une machine virtuelle avec Tomcat et Moodle à partir d’Internet

-	**Symptôme** Tomcat est en cours d’exécution, mais vous ne voyez pas la page par défaut Tomcat avec votre navigateur.
-	**Cause principale possible**   
	1.	Le port d’écoute tomcat n’est pas identique au Port privé du point de terminaison de votre machine virtuelle pour le trafic tomcat.  

		Vérifiez vos paramètres de points de terminaison de port public et de port privé et assurez-vous que le Port privé est identique au port d’écoute tomcat. Consultez la Phase 1 : Création d’une image pour obtenir des instructions sur la configuration des points de terminaison pour votre machine virtuelle.

		Pour déterminer le port d’écoute de tomcat, ouvrez /etc/httpd/conf/httpd.conf (version Red Hat) ou /etc/tomcat7/server.xml (version Debian). Par défaut, le port d’écoute de tomcat est 8080. Voici un exemple :

			<Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"  URIEncoding="UTF-8"            redirectPort="8443" />  

		Si vous utilisez un ordinateur virtuel comme Debian ou Ubuntu et que vous souhaitez modifier la valeur par défaut de port d’écoute de Tomcat (par exemple 8081), vous devez également ouvrir le port pour le système d’exploitation. Tout d’abord, ouvrez le profil :

			sudo vi /etc/default/tomcat7  

		Supprimez ensuite le commentaire de la dernière ligne, puis remplacez « non » par « oui ».

			AUTHBIND=yes

	2.	Le pare-feu a désactivé le port d’écoute tomcat.

		Si vous ne voyez que la page Tomcat par défaut à partir de l’hôte local, il est probable que le port sur lequel tomcat écoute soit bloqué par le pare-feu. Vous pouvez utiliser l’outil w3m pour parcourir la page web. Les commandes suivantes installent w3m et accèdent à la page par défaut Tomcat :

			sudo yum  install w3m w3m-img
			w3m http://localhost:8080  

-	**Solution**
	1. Si le port d’écoute tomcat n’est pas identique au Port privé du point de terminaison pour le trafic sur la machine virtuelle, vous devez modifier le Port privé pour qu’il soit identique au port d’écoute de tomcat.   

	2.	Si le problème est dû au pare-feu/iptables, ajoutez les lignes suivantes à /etc/sysconfig/iptables :

			-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
			-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

		Notez que la deuxième ligne est nécessaire uniquement pour le trafic https.

		Assurez-vous que ces lignes sont au-dessus de toutes les lignes qui limiteraient globalement l’accès, telles que les suivantes :

			-A INPUT -j REJECT --reject-with icmp-host-prohibited  

		Pour recharger les iptables, exécutez la commande suivante :

			service iptables restart  

		Cela a été testé sur CentOS 6.3.

###Autorisation refusée quand vous téléchargez vos fichiers de projet vers /var/lib/tomcat7/webapps/  

-	**Symptôme** Quand vous utilisez un client SFTP (par exemple, FileZilla) pour vous connecter à votre machine virtuelle et que vous accédez à /var/lib/tomcat7/webapps/ pour publier votre site, un message d’erreur semblable au suivant s’affiche :  

		status:	Listing directory /var/lib/tomcat7/webapps
		Command:	put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
		Error:	/var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
		Error:	File transfer failed

-	**Cause principale possible** Vous n’êtes pas autorisé à accéder au dossier /var/lib/tomcat7/webapps.
-	**Solution** Vous devez obtenir l’autorisation à partir du compte racine. Vous pouvez modifier le propriétaire par défaut de ce dossier (la racine) et choisir le nom d’utilisateur que vous avez utilisé lors de l’approvisionnement de la machine. Voici un exemple avec le nom de compte azureuser :  

		sudo chown azureuser -R /var/lib/tomcat7/webapps

	Utilisez l’option -R pour appliquer aussi les autorisations pour tous les fichiers contenus dans un répertoire.

	Notez que cette commande fonctionne également pour les répertoires. L’option -R modifie les autorisations pour tous les fichiers et répertoires contenus dans le répertoire. Voici un exemple :

		sudo chown -R username:group directory  

	Cette commande modifie le propriétaire (utilisateur et groupe) pour tous les fichiers et répertoires contenus dans le répertoire et le répertoire lui-même.

	La commande suivante modifie uniquement l’autorisation du répertoire mais laisse intacts les fichiers et dossiers dans le répertoire.

		sudo chown username:group directory



[1]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-18.png

<!---HONumber=AcomDC_1223_2015-->