Suivez cette procédure pour installer et exécuter MongoDB sur une machine virtuelle exécutant CentOS Linux.

> [AZURE.WARNING]Les fonctionnalités de sécurité MongoDB, comme l'authentification et la liaison d'adresse IP, ne sont pas activées par défaut. Elles doivent être activées avant le déploiement de MongoDB dans un environnement de production. Pour plus d'informations, consultez la page [Sécurité et authentification](http://www.mongodb.org/display/DOCS/Security+and+Authentication).

1. Configurez le système de gestion de packages \(YUM\) pour pouvoir installer MongoDB. Créez un fichier */etc/yum.repos.d/10gen.repo* pour conserver les informations liées au référentiel et ajoutez ce qui suit :

		[10gen]
		name=10gen Repository
		baseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64
		gpgcheck=0
		enabled=1

2. Enregistrez le fichier repo, puis exécutez la commande suivante pour mettre à jour la base de données du package local :

		$ sudo yum update

3. Pour installer le package, exécutez la commande suivante pour installer la dernière version stable de MongoDB et les outils associés :

		$ sudo yum install mongo-10gen mongo-10gen-server

	Patientez lors du téléchargement et de l'installation de MongoDB.

4. Créez un répertoire de données. Par défaut, MongoDB stocke les données dans le répertoire */data/db*, mais vous devez le créer. Pour ce faire, exécutez :

		$ sudo mkdir -p /srv/datadrive/data
		$ sudo chown `id -u` /srv/datadrive/data

	Pour plus d'informations sur l'installation de MongoDB sous Linux, consultez la page [Démarrage rapide sous Unix][QuickstartUnix].

5. Pour démarrer la base de données, exécutez :

		$ mongod --dbpath /srv/datadrive/data --logpath /srv/datadrive/data/mongod.log

	Tous les messages du journal sont dirigés vers le fichier */srv/datadrive/data/mongod.log* lorsque le serveur MongoDB démarre et préalloue les fichiers journaux. Il se peut que plusieurs minutes soient nécessaires pour que MongaDB préalloue les fichiers journaux et commence à écouter les connexions.

6. Pour démarrer l'interpréteur de commandes d'administration de MongoDB, ouvrez une fenêtre SSH ou PuTTY distincte et exécutez :

		$ mongo
		> db.foo.save ( { a:1 } )
		> db.foo.find()
		{ _id : ..., a : 1 }
		> show dbs  
		...
		> show collections  
		...  
		> help  

	La base de données est créée par l'insertion.

7. Après l'installation de MongoDB, vous devez configurer un point de terminaison pour que MongoDB soit accessible à distance. Dans le portail de gestion, cliquez sur **Virtual Machines**, sur le nom de votre nouvelle machine virtuelle, puis sur **Endpoints**.
	
	![Points de terminaison][Image7]

8. Cliquez sur **Add Endpoint** en bas de la page.
	
	![Points de terminaison][Image8]

9. Ajoutez un point de terminaison avec les paramètres suivants :

 - **Nom** : Mongo
 - **Protocole** : TCP
 - **Port public** : 27017
 - **Port privé** : 27017
 
 Cela permet d'accéder à distance à MongoDB.



[QuickStartUnix]: http://www.mongodb.org/display/DOCS/Quickstart+Unix


[Image7]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint2.png

<!--HONumber=52-->
