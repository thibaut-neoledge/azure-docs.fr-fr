<properties
	pageTitle="Installer et configurer PostgreSQL sur une machine virtuelle Microsoft Azure exécutant Linux"
	description="Découvrez comment installer et configurer PostgreSQL sur une machine virtuelle Linux dans Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
  tags=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="linux"
	ms.workload="infrastructure-services"
	ms.date="08/10/2015"
	ms.author="mingzhan"/>


#Installer et configurer PostgreSQL sur Microsoft Azure

PostgreSQL est une base de données open source avancée similaire à Oracle et DB2. Il inclut des fonctionnalités destinées aux entreprises, comme la conformité complète à ACID, un traitement transactionnel fiable et un contrôle d’accès concurrentiel multiversion. Il prend également en charge des normes comme ANSI SQL et SQL/MED (y compris les wrappers de données externes pour Oracle, MySQL, MongoDB et beaucoup d’autres). Il est hautement extensible, avec la prise en charge de 12 langages procéduraux, les index GIN et GIST, la prise en charge des données spatiales et plusieurs fonctionnalités de type NoSQL pour les applications JSON ou basées sur les paires clé-valeur.

Dans cet article, vous allez apprendre à installer et à configurer PostgreSQL sur une machine virtuelle Azure exécutant Linux.

> [AZURE.NOTE]Vous devez déjà disposer d’une machine virtuelle Microsoft Azure exécutant Linux pour effectuer ce didacticiel. Consultez le [didacticiel sur les machines virtuelles Linux Azure](virtual-machines-linux-tutorial.md) pour créer et configurer une machine virtuelle Linux avant de continuer.

[Dans ce cas, utilisez le port 1999 comme port PostgreSQL.]

## Installation de PostgreSQL

Connectez-vous à la machine virtuelle Linux que vous avez créée via putty. Si c’est la première fois que vous utilisez la machine virtuelle Linux Azure, découvrez comment utiliser putty pour se connecter à une machine virtuelle Linux [ici](virtual-machines-linux-use-ssh-key.md).

1. Exécutez la commande suivante pour passer à l’utilisateur root (admin) :

		# sudo su -

2. Certaines distributions ont des dépendances à installer avant d’installer PostgreSQL. Recherchez votre distribution dans cette liste et exécutez la commande appropriée :

	- Red Hat Linux :

			# yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

	- Debian Linux :

 			# apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  

	- SUSE Linux :

			# zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

3. Téléchargez PostgreSQL dans le répertoire racine, puis décompressez le package :

		# wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/

		# tar jxvf  postgresql-9.3.5.tar.bz2

	Voici un exemple ci-dessus. Vous pouvez trouver l’adresse de téléchargement détaillée [ici](https://ftp.postgresql.org/pub/source/).

4. Pour démarrer la génération, exécutez ces commandes :

		# cd postgresql-9.3.5

		# ./configure --prefix=/opt/postgresql-9.3.5

5. Si vous voulez générer tout ce qui peut l’être, y compris la documentation (HTML et les pages man) et les modules complémentaires (contrib), exécutez à la place la commande suivante :

		# gmake install-world

	Vous recevez normalement le message de confirmation suivant :

		PostgreSQL, contrib, and documentation successfully made. Ready to install.

## Configurer PostgreSQL

1. (Facultatif) Créez un lien symbolique pour raccourcir la référence PostgreSQL en n’y incluant pas le numéro de version :

		# ln -s /opt/pgsql9.3.5 /opt/pgsql

2. Créez un répertoire pour la base de données :

		# mkdir -p /opt/pgsql_data

3. Créez un utilisateur non-root et modifiez le profil de cet utilisateur. Passez à ce nouvel utilisateur (appelé *postgres* dans notre exemple) :

		# useradd postgres

		# chown -R postgres.postgres /opt/pgsql_data

		# su - postgres

   >[AZURE.NOTE]Pour des raisons de sécurité, PostgreSQL utilise un utilisateur non-root pour initialiser, démarrer ou arrêter la base de données.


4. Modifiez le fichier *bash\_profile* en entrant les commandes ci-dessous. Ces lignes seront ajoutées à la fin du fichier *bash\_profile* :

		cat >> ~/.bash_profile <<EOF
		export PGPORT=1999
		export PGDATA=/opt/pgsql_data
		export LANG=en_US.utf8
		export PGHOME=/opt/pgsql
		export PATH=\$PATH:\$PGHOME/bin
		export MANPATH=\$MANPATH:\$PGHOME/share/man
		export DATA=`date +"%Y%m%d%H%M"`
		export PGUSER=postgres
		alias rm='rm -i'
		alias ll='ls -lh'
		EOF

5. Exécutez le fichier *bash\_profile* :

		$ source .bash_profile

6. Vérifiez votre installation avec la commande suivante :

		$ which psql

	Si votre installation est réussie, vous voyez la réponse suivante :

		/opt/pgsql/bin/psql

7. Vous pouvez également vérifier la version de PostgreSQL :

		$ psql -V

8. Initialisez la base de données :

		$ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W

	Les résultats suivants doivent normalement s’afficher :

![image](./media/virtual-machines-linux-postgresql/no1.png)

## Configurer PostgreSQL

<!--	[postgres@ test ~]$ exit -->

Exécutez les commandes suivantes :

	# cd /root/postgresql-9.3.5/contrib/start-scripts

	# cp linux /etc/init.d/postgresql

Modifiez deux variables dans le fichier /etc/init.d/postgresql. Le préfixe est défini sur le chemin d’installation de PostgreSQL : **/opt/pgsql**. PGDATA est défini sur le chemin de stockage des données de PostgreSQL : **/opt/pgsql\_data**.

	# sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

	# sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/virtual-machines-linux-postgresql/no2.png)

Changez le fichier pour le rendre exécutable :

	# chmod +x /etc/init.d/postgresql

Démarrez PostgreSQL :

	# /etc/init.d/postgresql start

Vérifiez si le point de terminaison de PostgreSQL est activé :

	# netstat -tunlp|grep 1999

Vous devez normalement voir la sortie suivante.

![image](./media/virtual-machines-linux-postgresql/no3.png)

## Connexion à la base de données Postgres

Continuez et repassez à l’utilisateur postgres :

	# su - postgres

Créez une base de données Postgres :

	$ createdb events

Connectez-vous à la base de données d’événements que vous venez de créer :

	$ psql -d events

## Comment créer et supprimer une table Postgres

Maintenant que nous nous sommes connectés à la base de données, nous pouvons y créer des tables.

Par exemple, créez un exemple de table Postgres avec la commande suivante :

	CREATE TABLE potluck (name VARCHAR(20),	food VARCHAR(30),	confirmed CHAR(1), signup_date DATE);

Nous avons maintenant configuré une table de 4 colonnes avec ces noms de colonnes et ces restrictions :

1. La colonne « name » a été limitée par la commande VARCHAR à une longueur de 20 caractères.
2. La colonne « food » indique l’aliment que chaque personne emporte. VARCHAR limite ce texte à moins de 30 caractères.
3. La colonne « confirmed » indique si la personne a répondu à l’invitation au repas. Les valeurs acceptables sont « Y » et « N ».
4. La colonne « date » affiche la date à laquelle la personne s’est inscrite. Postgres requiert que les dates soient écrites au format aaaa-mm-jj.

Voici ce que vous devez voir si votre table a été créée correctement :

![image](./media/virtual-machines-linux-postgresql/no4.png)

Vous pouvez également vérifier la structure de la table avec la commande suivante :

![image](./media/virtual-machines-linux-postgresql/no5.png)

### Ajouter des données à une table

Insérez d’abord des informations dans une ligne :

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Cette sortie doit s’afficher :

![image](./media/virtual-machines-linux-postgresql/no6.png)

Vous pouvez aussi ajouter quelques personnes de plus à la table. Voici quelques propositions, mais vous pouvez créer vos propres informations :

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### Afficher les tables

Utilisez la commande suivante pour afficher une table :

	select * from potluck;

La sortie est la suivante :

![image](./media/virtual-machines-linux-postgresql/no7.png)

### Supprimer des données dans une table

Utilisez les commandes suivantes pour supprimer des données dans une table :

	delete from potluck where name=’John’;

Cette opération va supprimer toutes les informations dans la ligne « John ». La sortie est la suivante :

![image](./media/virtual-machines-linux-postgresql/no8.png)

### Mettre à jour des données dans une table

Utilisez les commandes suivantes pour mettre à jour des données dans une table : Pour celle-ci, Sandy a confirmé qu’elle participait et nous allons donc changer sa réponse de « N » en « Y » :

 	UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


##Plus d’informations sur PostgreSQL
Vous venez de terminer l’installation de PostgreSQL dans la machine virtuelle Linux Microsoft Azure et vous pouvez maintenant l’utiliser dans Microsoft Azure. Plus d’informations sur PostgreSQL, cliquez [ici](http://www.postgresql.org/).

<!---HONumber=August15_HO8-->