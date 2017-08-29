## <a name="install-wordpress"></a>Installer WordPress

Si vous souhaitez essayer votre pile, installez un exemple d’application. Ainsi, les étapes suivantes installent la plateforme open source [WordPress](https://wordpress.org/) pour créer des sites web et des blogs. Les autres charges de travail à essayer incluent [Drupal](http://www.drupal.org) et [Moodle](https://moodle.org/). 

Ce programme d’installation de WordPress est pour la preuve de concept. Consultez la [documentation WordPress](https://codex.wordpress.org/Main_Page)pour obtenir plus d’informations et de paramètres sur l’installation de production. 



### <a name="install-the-wordpress-package"></a>Installez le package WordPress

Exécutez la commande suivante :

```bash
sudo apt install wordpress
```

### <a name="configure-wordpress"></a>Configurer WordPress

Configurer WordPress pour utiliser PHP et MySQL. Exécutez la commande suivante pour ouvrir un éditeur de texte de votre choix et créer le fichier `/etc/wordpress/config-localhost.php`:

```bash
sudo sensible-editor /etc/wordpress/config-localhost.php
```
Copiez les lignes suivantes dans le fichier, en remplaçant votre mot de passe de base de données par *votreMotdePasse* (laissez les autres valeurs inchangées). Puis enregistrez le fichier.

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'yourPassword');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');
?>
```

Dans un répertoire de travail, créez un fichier texte `wordpress.sql` pour configurer la base de données WordPress : 

```bash
sudo sensible-editor wordpress.sql
```

Ajoutez les commandes suivantes, en remplaçant votre mot de passe de base de données par *votreMotdePasse* (laissez les autres valeurs inchangées). Puis enregistrez le fichier.

```sql
CREATE DATABASE wordpress;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
ON wordpress.*
TO wordpress@localhost
IDENTIFIED BY 'yourPassword';
FLUSH PRIVILEGES;
```


Exécutez la commande suivante pour créer la base de données :

```bash
cat wordpress.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

Une fois la commande terminée, supprimez le fichier `wordpress.sql`.

Déplacez l’installation de WordPress à la racine du document du serveur web :

```bash
sudo ln -s /usr/share/wordpress /var/www/html/wordpress

sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
```

Vous pouvez désormais terminer l’installation de WordPress et publier sur la plateforme. Ouvrez un navigateur web et accédez à `http://yourPublicIPAddress/wordpress`. Remplacez l’adresse IP publique de votre machine virtuelle. Elle doit ressembler à cette image.

![Page d’installation de WordPress](./media/virtual-machines-linux-tutorial-wordpress/wordpressstartpage.png)