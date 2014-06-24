### Installation via Composer

1.  [Installez Git][1].
    
    <div  class="dev-callout" markdown="1">


**Remarque**
<p>Sous Windows, vous devez aussi ajouter l'exécutable Git à votre
variable d'environnement PATH.
</p>
</div>


2.  Créez un fichier nommé **composer.json** à la racine de votre projet
    et ajoutez-y le code suivant :
    
         {
             "require": {
                 "microsoft/windowsazure": "*"
             },			
             "repositories": [
                 {
                     "type": "pear",
                     "url": "http://pear.php.net"
                 }
             ],
             "minimum-stability": "dev"
         }

3.  Téléchargez **[composer.phar][2]** à la racine du projet.

4.  Ouvrez une invite de commandes et exécutez cette commande à la
    racine du projet
    
         php composer.phar install
### Installation manuelle

Pour télécharger et installer manuellement les bibliothèques clientes
PHP pour Azure, procédez comme suit :

1.  Téléchargez une archive ZIP qui contient les bibliothèques de
    [GitHub][3]. Sinon, répliquez le répertoire et clonez-le sur votre
    ordinateur local. (La deuxième option requiert un compte GitHub et
    l'installation locale de Git.)
    
    <div  class="dev-callout" markdown="1">

	**Remarque**

	<p>Les bibliothèques clientes PHP pour Azure ont une dépendance sur les
	packages PEAR [HTTP\_Request2][4],
	[Mail\_mime][5] et [Mail\_mimeDecode][6]. La méthode recommandée pour résoudre ces
	dépendances consiste à installer ces packages à l'aide du
	[Gestionnaire de package PEAR][7].
	
	</div>


2.  Copiez le répertoire `WindowsAzure` de l'archive téléchargée dans la
    structure de répertoires de votre application.

Pour plus d'informations sur l'installation des bibliothèques clientes
PHP pour Azure (y compris des informations sur l'installation en tant
que package PEAR), consultez la page [Télécharger le Kit de
développement logiciel (SDK) Azure pour PHP](../php-download-sdk/).



[1]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[2]: http://getcomposer.org/composer.phar
[3]: http://go.microsoft.com/fwlink/?LinkId=252719
[4]: http://pear.php.net/package/HTTP_Request2
[5]: http://pear.php.net/package/Mail_mime
[6]: http://pear.php.net/package/Mail_mimeDecode
[7]: http://pear.php.net/manual/en/installation.php
