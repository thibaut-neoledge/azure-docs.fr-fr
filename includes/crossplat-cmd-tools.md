
# Utilisation des outils en ligne de commande Azure pour Mac et Linux

Ce guide explique comment utiliser les outils en ligne de commande Azure pour Mac et Linux afin de créer et de gérer des services dans Azure. Les scénarios abordés sont les suivants : **installation des outils**, **importation de vos paramètres de publication**, **création et gestion de sites Web Azure** et **création et gestion de machines virtuelles Azure**. Pour accéder à une documentation de référence exhaustive, consultez la page [Documentation de l'outil en ligne de commande Azure pour Mac et Linux][1].

## Sommaire

* [Présentation des outils en ligne de commande Azure pour Mac et
  Linux](#Overview)
* [Installation des outils en ligne de commande Azure pour Mac et
  Linux](#Download)
* [Création d'un compte Azure](#CreateAccount)
* [Téléchargement et importation des paramètres de
  publication](#Account)
* [Création et gestion d'un site Web Azure](#WebSites)
* [Création et gestion d'une machine virtuelle Azure](#VMs)

<h2><a id="Overview" ></a>Présentation des outils en ligne de commande Azure pour Mac et Linux</h2>


L'ensemble d'outils en ligne de commande Azure pour Mac et Linux permet de déployer et de gérer les services Azure.

Les tâches prises en charge sont les suivantes :

* importation de paramètres de publication ;
* création et gestion de sites Web Azure ;
* création et gestion de machines virtuelles Azure.

Pour obtenir la liste complète des commandes prises en charge, tapez `azure -help` sur la ligne de commande après avoir installé les outils ou consultez la [documentation de référence][1].

<h2><a id="Download">Installation des outils en ligne de commande Azure pour Mac et Linux</a></h2>


La liste suivante contient des informations permettant d'installer les outils en ligne de commande en fonction de votre système d'exploitation :

* **Mac** : téléchargez le [programme d'installation du Kit de développement logiciel (SDK) Azure][2]. Ouvrez le fichier .pkg téléchargé et suivez les étapes d'installation indiquées.

* **Linux** : installez la dernière version de [Node.js][3] (voir [Install Node.js via Package Manager][4] (en anglais)), puis exécutez la commande suivante :
  
       	npm install azure-cli -g
  
  	**Remarque** : vous devrez peut-être exécuter cette commande avec des privilèges élevés :
  
		sudo npm install azure-cli -g

* **Windows** : exécutez le programme d'installation de Windows (fichier .msi), disponible ici : [Outils en ligne de commande Azure][5].

Pour tester l'installation, tapez `azure` à l'invite. Si l'installation a réussi, une liste de toutes les commandes `azure` disponibles apparaît.

<h2><a id="CreateAccount" ></a>Création d'un compte Azure</h2>


Pour utiliser les outils en ligne de commande Azure pour Mac et Linux, vous avez besoin d'un compte Azure.

Ouvrez un navigateur Web et accédez à [http://www.windowsazure.com][6], puis cliquez sur **free trial** dans le coin supérieur droit.

![Site Web Azure](./media/crossplat-cmd-tools/freetrial.png)

Suivez les instructions pour créer un compte.

<h2><a id="Account" ></a>Téléchargement et importation des paramètres de publication</h2>


Pour commencer, vous devez d'abord télécharger et importer vos paramètres de publication. Cela vous permet d'utiliser les outils servant à créer et à gérer les services Azure. Pour télécharger vos paramètres de publication, utilisez la commande `account download` :

    azure account download

Cette commande ouvre votre navigateur par défaut et vous invite à vous connecter au portail de gestion. Une fois que vous êtes connecté, votre fichier `.publishsettings` est téléchargé. Prenez note de son emplacement.

Importez ensuite le fichier `.publishsettings` en exécutant la commande suivante, en remplaçant `{path to .publishsettings file}` par le chemin d'accès de votre fichier `.publishsettings` :

    azure account import {path to .publishsettings file}

Vous pouvez supprimer toutes les informations stockées par la commande `import` en utilisant la commande `account clear` :

    azure account clear

Pour afficher la liste des options des commandes `account`, utilisez l'option `-help` :

    azure account -help

Après avoir importé vos paramètres de publication, veillez à supprimer le fichier `.publishsettings` pour des raisons de sécurité.

 
<div  class="dev-callout"> 
<b>Remarque</b> 
<p>Lorsque vous importez les paramètres de publication, les informations d’identification permettant d’accéder à votre abonnement Azure sont stockées dans votre dossier <code>user</code>. Votre dossier <code>user</code> est protégé par votre système d’exploitation. Toutefois, il est recommandé de prendre des mesures supplémentaires pour chiffrer votre dossier <code>user</code>. Pour ce faire, procédez comme suit :</p>

<ul>
<li>Sous Windows, modifiez les propriétés de dossier ou utilisez BitLocker.</li>
<li>Sous Mac, activez FileVault pour le dossier.</li>
<li>Sous Ubuntu, utilisez la fonctionnalité d’annuaire Encrypted Home. Les autres versions de Linux fournissent des fonctionnalités équivalentes.</li>
</ul>

</div>

 Vous êtes désormais prêt à créer et à gérer des sites Web Azure et des machines virtuelles Azure.

<h2><a id="WebSites" ></a>Création et gestion d'un site Web Azure</h2>

### Création d'un site Web

Pour créer un site Web Azure, commencez par créer un répertoire vide appelé `MySite`, puis accédez à ce répertoire.

Exécutez ensuite la commande suivante :

    azure site create MySite --git

Le résultat de cette commande contient l'URL par défaut du site Web qui vient d'être créé. L'option `--git` vous permet d'utiliser git pour publier sur votre site Web en créant des référentiels git à la fois dans votre répertoire d'application local et dans le centre de données de votre site Web. Notez que si votre dossier local est déjà un référentiel git, la commande ajoute une nouvelle référence distante au référentiel existant, en pointant vers le référentiel du centre de données de votre site Web.

Notez que vous pouvez exécuter la commande `azure site create` avec l'une des options suivantes :

* `--location [location name]`. Cette option vous permet de spécifier
  l'emplacement du centre de données dans lequel votre site Web est créé
  (par exemple, << Bretagne >>). Si vous omettez cette option, vous êtes
  invité à choisir un emplacement.
* `--hostname [custom host name]`. Cette option vous permet de spécifier
  un nom d'hôte personnalisé pour votre site Web.

Vous pouvez ensuite ajouter du contenu au répertoire de votre site Web. Utilisez le flux git normal (`git add`, `git commit`) pour valider votre contenu. Utilisez la commande git suivante pour envoyer le contenu de votre site Web vers Azure :

    git push azure master

### Configuration de la publication à partir de GitHub

Pour configurer la publication continue à partir d'un référentiel GitHub, utilisez l'option `--GitHub` lors de la création d'un site :

    azure site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

Si vous disposez d'un clone local d'un référentiel GitHub ou d'un référentiel avec une référence distante unique à un référentiel GitHub, cette commande publie automatiquement le code dans le référentiel GitHub sur votre site. À partir de là, toute modification envoyée au référentiel GitHub est automatiquement publiée sur votre site.

Lorsque vous configurez la publication à partir de GitHub, la branche par défaut utilisée est la branche principale. Pour spécifier une autre branche, exécutez la commande suivante à partir de votre référentiel local :

    azure site repository <branch  name>

### Configuration des paramètres d'application

Les paramètres d'application sont des paires clé-valeur disponibles pour votre application au moment de l'exécution. Lorsqu'elles sont définies pour un site Web Azure, les valeurs de paramètre d'application sont prioritaires par rapport aux paramètres de même clé définis dans le fichier Web.config de votre site. Pour les applications Node.js et PHP, les paramètres d'application sont disponibles en tant que variables d'environnement. L'exemple suivant illustre comment définir une paire clé-valeur :

    azure site config add <key>=<value> 

Pour afficher une liste de toutes les paires clé/valeur, utilisez la commande suivante :

    azure site config list 

Si vous connaissez la clé et que vous voulez voir la valeur, vous pouvez également utiliser :

    azure site config get <key> 

Si vous voulez modifier la valeur d'une clé existante, vous devez d'abord supprimer cette dernière, puis l'ajouter à nouveau. La commande de suppression est la suivante :

    azure site config clear <key> 

### Énumération et affichage des sites

Pour répertorier vos sites Web, utilisez la commande suivante :

    azure site list

Pour obtenir des informations détaillées sur un site, utilisez la commande `site show`. L'exemple suivant affiche les détails de `MySite` :

    azure site show MySite

### Arrêt, démarrage ou redémarrage d'un site

Vous pouvez arrêter, démarrer ou redémarrer un site au moyen des commandes `site stop`, `site start` ou `site restart` :

    azure site stop MySite
    azure site start MySite
    azure site restart MySite

### Suppression d'un site

Pour finir, vous pouvez supprimer un site avec la commande `site delete` :

    azure site delete MySite

Notez que si vous exécutez l'une des commandes ci-dessus à partir d'un dossier où vous avez exécuté `site create`, vous ne devez pas spécifier le nom de site `MySite` en tant que dernier paramètre.

Pour afficher la liste complète des commandes `site`, utilisez l'option `-help` :

    azure site -help 

<h2><a id="VMs" ></a>Création et gestion d'une machine virtuelle Azure</h2>


Une machine virtuelle Azure est créée à partir d'une image de machine virtuelle (fichier .vhd) que vous fournissez ou qui est disponible dans la galerie d'images. Pour afficher les images disponibles, utilisez la commande `vm image list` :

    azure vm image list

Vous pouvez mettre en service et démarrer une machine virtuelle à partir de l'une des images disponibles au moyen de la commande `vm create`. L'exemple suivant indique comment créer une machine virtuelle Linux (appelée `myVM`) à partir d'une image de la galerie d'images (CentOS 6.2). Le nom d'utilisateur racine et le mot de passe de la machine virtuelle sont respectivement `myusername` et `Mypassw0rd`. (Notez que le paramètre `--location` spécifie le centre de données dans lequel la machine virtuelle est créée. Si vous omettez le paramètre
`--location`, vous êtes invité à choisir un emplacement.)

    azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd myusername --location "Bretagne"

Vous pouvez transmettre l'indicateur `--ssh` (Linux) ou `--rdp` (Windows) à `vm create` pour autoriser les connexions à distance à la machine virtuelle qui vient d'être créée.

Si vous préférez mettre en service une machine virtuelle à partir d'une image personnalisée, vous pouvez créer une image à partir d'un fichier .vhd avec la commande `vm image create`, puis vous pouvez utiliser la commande `vm create` pour mettre en service la machine virtuelle. L'exemple suivant indique comment créer une image Linux (appelée `myImage`) à partir d'un fichier .vhd local. (Le paramètre `--location` spécifie les données dans lesquelles l'image est stockée.)

    azure vm image create myImage /path/to/myImage.vhd --os linux --location "Bretagne"

Au lieu de créer l'image à partir d'un fichier .vhd local, vous pouvez la créer à partir d'un fichier .vhd figurant dans le stockage des objets blob Azure. Pour cela, utilisez le paramètre `blob-url` :

    azure vm image create myImage --blob-url <url  to .vhd in Blob Storage> --os linux

Une fois l'image créée, vous pouvez mettre en service une machine virtuelle à partir de l'image en utilisant `vm create`. La commande ci-dessous crée une machine virtuelle appelée `myVM` à partir de l'image créée plus haut (`myImage`).

    azure vm create myVM myImage myusername --location "Bretagne"

Après avoir mis en service une machine virtuelle, vous pouvez par exemple créer des points de terminaison pour autoriser les accès à distance à votre machine virtuelle. L'exemple suivant utilise la commande `vm create endpoint` pour ouvrir le port externe 22 et le port local 22 sur `myVM` :

    azure vm endpoint create myVM 22 22

Pour obtenir des informations détaillées sur une machine virtuelle (y compris l'adresse IP, le nom DNS et les informations de point de terminaison), utilisez la commande `vm show` :

    azure vm show myVM

Pour arrêter, démarrer ou redémarrer la machine virtuelle, utilisez l'une des commandes suivantes :

    azure vm shutdown myVM
    azure vm start myVM
    azure vm restart myVM

Enfin, pour supprimer la machine virtuelle, utilisez la commande `vm delete` :

    azure vm delete myVM

Pour obtenir la liste complète des commandes permettant de créer et de gérer des machines virtuelles, utilisez l'option `-h` :

    azure vm -h

<!-- IMAGES -->

<!-- LINKS -->



[1]: http://go.microsoft.com/fwlink/?LinkId=252246
[2]: http://go.microsoft.com/fwlink/?LinkId=252249
[3]: http://nodejs.org/
[4]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[5]: http://go.microsoft.com/fwlink/?LinkID=275464
[6]: http://www.windowsazure.com