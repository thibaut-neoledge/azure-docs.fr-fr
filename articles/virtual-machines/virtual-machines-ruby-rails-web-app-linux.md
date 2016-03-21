<properties
	pageTitle="Hébergement d’un Ruby sur le site web de Rails sur une machine virtuelle Linux | Microsoft Azure"
	description="Configuration et hébergement d'un Ruby sur le site web de Rails dans Azure en utilisant une machine virtuelle Linux."
	services="virtual-machines"
	documentationCenter="ruby"
	authors="rmcmurray"
	manager="wpickett"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="web"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="03/04/2016"
	ms.author="robmcm"/>





#Application Web Ruby on Rails sur une machine virtuelle Azure

Ce didacticiel explique comment héberger un site web Ruby on Rails sur Azure à l'aide d'une machine virtuelle Linux.

Ce didacticiel a été validé à l’aide d’Ubuntu Server 14.04 LTS. Si vous utilisez une distribution Linux différente, vous devrez peut-être modifier les étapes pour installer Rails.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


## Création d’une machine virtuelle Azure

Commencez par créer une machine virtuelle Azure avec une image Linux.

Pour créer la machine virtuelle, vous pouvez utiliser le portail Azure Classic ou l’interface de ligne de commande (CLI) Azure.

### Portail de gestion Azure

1. Connectez-vous au [portail Azure Classic](http://manage.windowsazure.com)
2. Cliquez sur **Nouveau** > **Calcul** > **Machine virtuelle** > **Création rapide**. Sélectionnez une image Linux.
3. Entrez un mot de passe.

Une fois que la machine virtuelle est configurée, cliquez sur son nom, puis cliquez sur **Tableau de bord**. Recherchez le point de terminaison SSH répertorié sous **Détails SSH**.

### Interface de ligne de commande Azure

Suivez les étapes de [Création d’une machine virtuelle exécutant Linux][vm-instructions].

Une fois que la machine virtuelle est configurée, vous pouvez obtenir le point de terminaison SSH en exécutant la commande suivante :

	azure vm endpoint list <vm-name>  

## installation de Ruby sur Rails

1. Utilisez le protocole SSH pour vous connecter à la machine virtuelle.

2. Depuis la session SSH, utilisez les commandes suivantes pour installer Ruby sur la machine virtuelle :

		sudo apt-get update -y
		sudo apt-get upgrade -y
		sudo apt-get install ruby ruby-dev build-essential libsqlite3-dev zlib1g-dev nodejs -y

	L’installation peut prendre quelques minutes. Lorsqu’elle est terminée, utilisez la commande suivante pour vérifier que Ruby est installé :

		ruby -v

	Cette commande renvoie la version de Ruby qui a été installée.

3. Utilisez la commande suivante pour installer Rails :

		sudo gem install rails --no-rdoc --no-ri

	Utilisez les indicateurs --no-rdoc et--no-ri pour ignorer l’installation de la documentation, ce qui est plus rapide.

## Création et exécution d’une application

Alors que vous êtes toujours connecté via SSH, exécutez les commandes suivantes :

	rails new myapp
	cd myapp
	rails server -b 0.0.0.0 -p 3000

La [nouvelle](http://guides.rubyonrails.org/command_line.html#rails-new) commande crée une nouvelle application Rails. La commande [server](http://guides.rubyonrails.org/command_line.html#rails-server) démarre le serveur web WEBrick qui est fourni avec Rails. (À des fins de production, vous souhaiterez probablement utiliser un autre serveur, par exemple Unicorn ou Passenger.)

Le résultat ressemble à ce qui suit.

	=> Booting WEBrick
	=> Rails 4.2.1 application starting in development on http://0.0.0.0:3000
	=> Run `rails server -h` for more startup options
	=> Ctrl-C to shutdown server
	[2015-06-09 23:34:23] INFO  WEBrick 1.3.1
	[2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
	[2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000


## Ajout d’un point de terminaison

1. Accédez au [portail Azure Classic][management-portal] et sélectionnez votre machine virtuelle.

	![liste des machines virtuelles][vmlist]

2. Sélectionnez **ENDPOINTS** dans la partie supérieure de la page, puis cliquez sur **+ ADD ENDPOINT** dans la partie inférieure de la page.

	![page des points de terminaison][endpoints]

3. Dans la boîte de dialogue **AJOUTER UN POINT DE TERMINAISON**, sélectionnez « Ajouter un point de terminaison autonome », puis cliquez sur la flèche **Suivant**.

	![nouvelle boîte de dialogue de points de terminaison][new-endpoint1]

3. Dans la page suivante de la boîte de dialogue, entrez les informations suivantes :

	* **NOM** : HTTP

	* **PROTOCOLE** : TCP

	* **PORT PUBLIC** : 80

	* **PORT PRIVÉ** : 3000

	Ainsi, un port public 80 est créé pour acheminer le trafic vers le port privé 3000, où le serveur Rails écoute.

	![nouvelle boîte de dialogue de points de terminaison][new-endpoint]

4. Cliquez sur la coche pour enregistrer le point de terminaison.

5. Un message indiquant **UPDATE IN PROGRESS** doit s'afficher. Une fois que ce message disparaît, le point de terminaison est actif. Vous pouvez désormais tester votre application en accédant au nom DNS de votre machine virtuelle. Le site web qui apparaît doit être semblable à l'illustration ci-dessous :

	![page rails par défaut][default-rails-cloud]


##<a id="next"></a>Étapes suivantes

Dans ce didacticiel, vous avez effectué la plupart des étapes manuellement. Dans un environnement de production, vous écririez votre application sur un ordinateur de développement et la déploieriez sur la machine virtuelle Azure. En outre, la plupart des environnements de production hébergent l'application Rails en association avec un autre processus serveur tel que Apache ou NginX, qui traite l'acheminement des requêtes vers plusieurs instances de l'application Rails et l'envoi des ressources statiques. Pour plus d'informations, consultez http://rubyonrails.org/deploy/.

Pour en savoir plus sur Ruby on Rails, consultez les [guides Ruby on Rails][rails-guides].

Pour utiliser des services Azure depuis votre application Ruby, consultez :

* [Utilisation du stockage d’objets blob à partir de Ruby][blobs]

* [Utilisation du stockage de tables à partir de Ruby][tables]

* [Distribution de contenu à bande passante élevée avec le CDN][cdn-howto]



<!-- WA.com links -->
[blobs]: ../storage-ruby-how-to-use-blob-storage.md

[cdn-howto]: /develop/ruby/app-services/

[management-portal]: https://manage.windowsazure.com/

[tables]: /develop/ruby/how-to-guides/table-service/

[vm-instructions]: virtual-machines-linux-tutorial.md


<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/

[sqlite3]: http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailscloud.png

[vmlist]: ./media/virtual-machines-ruby-rails-web-app-linux/vmlist.png

[endpoints]: ./media/virtual-machines-ruby-rails-web-app-linux/endpoints.png

[new-endpoint]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint.png

[new-endpoint1]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint1.png

<!------HONumber=AcomDC_0309_2016-->