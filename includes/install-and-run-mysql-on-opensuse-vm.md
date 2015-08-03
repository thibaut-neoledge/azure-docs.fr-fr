
1. Pour élever les privilèges, exécutez :

		sudo -s
	
	Saisissez votre mot de passe.

2. Exécutez la commande suivante pour installer MySQL Community Server :

		# zypper install mysql-community-server

	Patientez lors du téléchargement et de l’installation de MySQL.
3. Pour que MySQL démarre au démarrage du système, exécutez la commande suivante :

		# insserv mysql
4. Pour démarrer manuellement le démon MySQL (mysqld), utilisez la commande suivante :

		# rcmysql start

	Pour vérifier l’état du démon MySQL, exécutez :

		# rcmysql status

	Pour arrêter le démon MySQL, exécutez :

		# rcmysql stop

5. Avertissement ! Après l’installation, le mot de passe racine MySQL est vide par défaut. Il est recommandé d’exécuter le script **mysql_secure_installation** qui permet de sécuriser MySQL. Lors de l’exécution de **mysql_secure_installation**, vous êtes invité à modifier le mot de passe racine MySQL, à supprimer les comptes d’utilisateurs anonymes, à désactiver les connexions racine à distance, à supprimer les bases de données de test et à recharger la table des privilèges. Il est recommandé de répondre oui à toutes ces options et de modifier le mot de passe racine. Pour exécuter le script, saisissez la commande suivante :

		$ mysql_secure_installation

6. Après l’exécution, vous pouvez vous connecter à MySQL :

		$ mysql -u root -p

	Entrez le mot de passe racine MySQL (que vous avez modifié à l’étape précédente) pour ouvrir une invite de commandes vous permettant d’écrire des instructions SQL pour interagir avec la base de données.

7. Pour créer un utilisateur MySQL, exécutez la commande suivante dans l'invite **mysql>** :

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Notez que les points-virgules (;) à la fin des lignes sont indispensables pour finir les commandes.

8. Pour créer une base de données et accorder les autorisations utilisateur `mysqluser` sur cette dernière, exécutez les commandes suivantes :

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Notez que les noms et les mots de passe des utilisateurs de base de données sont uniquement utilisés par les scripts pour se connecter à la base de données. Les noms des comptes d’utilisateurs de base de données ne représentent pas nécessairement les comptes d’utilisateurs du système.

9. Pour se connecter à partir d’un autre ordinateur, exécutez la commande suivante :

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	où `ip-address` est l’adresse IP de l’ordinateur à partir duquel vous allez vous connecter à MySQL.
	
10. Pour quitter l’utilitaire d’administration de base de données MySQL, exécutez la commande suivante :

		quit

11. Après l’installation de MySQL, vous devez configurer un point de terminaison pour que MySQL soit accessible à distance. Connectez-vous au [portail de gestion Azure][AzurePreviewPortal]. Dans le portail Azure, cliquez sur **Machines virtuelles**, puis sur le nom de la nouvelle machine virtuelle et sur **Points de terminaison**.

	![Endpoints][Image7]

12. Cliquez sur **Ajouter** en bas de la page. ![Endpoints][Image8]

13. Ajoutez un point de terminaison avec le nom « MySQL », le protocole **TCP** ainsi que les ports **Public** et **Privé** définis sur « 3306 ». Cela permet l’accès à distance à MySQL. ![Points de terminaison][Image9]

14. Pour vous connecter à distance au MySQL s’exécutant sur la machine virtuelle OpenSUSE dans Azure, exécutez la commande suivante sur votre ordinateur local :

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	Par exemple, pour la machine virtuelle créée dans ce didacticiel, la commande est :

		mysql -u mysqluser -p -h testlinuxvm.cloudapp.net

15. Vous avez réussi à configurer MySQL, à créer une base de données et un utilisateur. Pour plus d’informations sur MySQL, consultez la [documentation MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/
[AzurePreviewPortal]: http://manage.windowsazure.com

[Image9]: ./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpointMySQL.png

<!---HONumber=July15_HO4-->