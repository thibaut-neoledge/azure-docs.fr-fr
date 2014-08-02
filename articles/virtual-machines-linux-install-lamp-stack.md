<properties linkid="manage-linux-common-tasks-lampstack" urlDisplayName="Install LAMP stack" pageTitle="Install the LAMP stack on a Linux virtual machine" metaKeywords="" description="Learn how to install the LAMP stack on a Linux virtual machine (VM) in Azure. You can install on Ubuntu or CentOS." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install the LAMP Stack on a Linux virtual machine in Azure" authors="" solutions="" manager="" editor="" />

Installation de la pile LAMP sur une machine virtuelle Linux dans Azure
=======================================================================

Une pile LAMP se compose des différents éléments suivants :

-   **L**inux : système d'exploitation
-   **A**pache : serveur Web
-   **M**ySQL : serveur de base de données
-   **P**HP : langage de programmation

Installation sur Ubuntu
-----------------------

Les packages suivants doivent être installés :

-   `apache2`
-   `mysql-server`
-   `php5`
-   `php5-mysql`
-   `libapache2-mod-auth-mysql`
-   `libapache2-mod-php5`
-   `php5-xsl`
-   `php5-gd`
-   `php-pear`

Pour ce faire, exécutez cette commande `apt-get install` unique :

    apt-get install apache2 mysql-server php5 php5-mysql libapache2-mod-auth-mysql libapache2-mod-php5 php5-xsl php5-gd php-pear

Installation sur CentOS
-----------------------

Les packages suivants doivent être installés :

-   `httpd`
-   `mysql`
-   `mysql-server`
-   `php`
-   `php-mysql`

Pour ce faire, exécutez cette commande `yum install` unique :

    yum install httpd mysql mysql-server php-php-mysql

Configuration
-------------

1.  Configurez **Apache**.

    1.  Vous devez redémarrer manuellement le serveur Web Apache. Exécutez la commande suivante :

             sudo /etc/init.d/apache2 restart

    2.  Vérifiez si l'installation est en cours. Dans votre navigateur, accédez à : <http://localhost>. « It works! » (Elle fonctionne) doit être affiché.

2.  Configurez **MySQL**.
    1.  Définissez le mot de passe racine pour mysql en exécutant la commande suivante :

             mysqladmin -u root -p password yourpassword

    2.  Connectez-vous à la console à l'aide de `mysql` ou d'un éventail de clients MySQL.

3.  Configurez **PHP**.

    1.  Activez le module PHP Apache en exécutant la commande suivante :

             sudo a2enmod php5

    2.  Relancez Apache en exécutant la commande suivante :

             sudo service apache2 restart

Pour aller plus loin
--------------------

Il existe de nombreuses ressources pour configurer une pile LAMP sur Ubuntu.

-   <https://help.ubuntu.com/community/ApacheMySQLPHP>
-   <http://fedorasolved.org/server-solutions/lamp-stack>

