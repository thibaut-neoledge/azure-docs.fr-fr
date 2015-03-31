<properties 
	   pageTitle="Automatisation du déploiement de machine virtuelle Azure avec Chef 
	   description ="L'art de l'automatisation de machine virtuelle Azure avec Chef" 
	   services="virtual-machines" 
	   documentationCenter="" 
	   authors="diegoviso" 
	   manager="timlt" 
	   editor=""/>

<tags 
	ms.service="virtual-machines" ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2015" 
	ms.author="diviso"/>

# Automatisation du déploiement de machine virtuelle Azure avec Chef

Chef est un excellent outil d'automatisation et de fourniture des configurations d'état souhaitées. 

Avec notre dernière version d'api-cloud, Chef assure l'intégration transparente avec Azure, ce qui vous donne la possibilité de configurer et de déployer les états de configuration via une commande unique.

Dans cet article, vous apprendrez à configurer votre environnement Chef pour approvisionner des machines virtuelles Azure et à créer une stratégie ou " Livre de recettes " avant de la déployer sur une machine virtuelle Azure.

Commençons !

## Principes fondamentaux de Chef

Avant de commencer, nous vous suggérons de vous familiariser avec les principes fondamentaux de Chef. Des éléments très intéressants sont disponibles <a href="http://www.chef.io/chef" target="_blank">ici,</a> et nous vous recommandons de les passer en revue avant de suivre ce guide. Nous récapitulerons les principes fondamentaux avant de commencer.

Le schéma ci-dessous illustre l'architecture de Chef de haut niveau.

![][2]

Chef a trois principaux composants architecturaux. Le **serveur Chef, le client Chef (nœud)** et **la station de travail Chef.**

Le **serveur Chef** est notre point de gestion. Il existe deux options pour le serveur Chef : une solution hébergée ou une solution locale. Nous allons utiliser une solution hébergée.

Le **client Chef (nœud)** est l'agent qui se trouve sur les serveurs que vous gérez.

La **station de travail Chef** est notre station de travail d'administration où nous créons nos stratégies et exécutons nos commandes de gestion. Nous exécutons la commande **" couteau "** à partir de la station de travail Chef pour gérer notre infrastructure.

Les concepts de " Livres de recettes " et de " Recettes " existent aussi. Il s'agit des stratégies que nous définissons et qui s'appliquent à nos serveurs.

## Préparation de la station de travail

Tout d'abord, préparons la station de travail. J'utilise une station de travail Windows standard. Nous devons créer un répertoire pour stocker nos fichiers de configuration et nos livres de recettes.

Commencez par créer un répertoire appelé **C:\chef**. 

Créez ensuite un second répertoire **c:\chef\cookbooks**

Nous devons maintenant télécharger notre fichier de paramètres Azure pour que Chef puisse communiquer avec notre abonnement Azure.

Téléchargez vos paramètres de publication ici : <a href="https://manage.windowsazure.com/publishsettings/" target="_blank">https://manage.windowsazure.com/publishsettings/</a>

Enregistrez le fichier de paramètres de publication dans **C:\chef**

## Création d'un compte Chef géré

Inscrivez-vous à un compte Chef hébergé : <a href="https://manage.chef.io/signup" target="_blank">https://manage.chef.io/signup</a>

Pendant le processus d'inscription, vous devrez créer une organisation.

![][3]

Une fois que votre organisation est créée, téléchargez le starter kit.

![][4]

**Remarque :** si vous recevez une invite pour vous prévenir que vos clés seront réinitialisées, continuez, étant donné que nous ne disposons pas encore d'une infrastructure configurée.

Le fichier zip du starter kit comprend vos clés et fichiers de configuration d'organisation.

## Configuration de la station de travail Chef

Extrayez le contenu du fichier chef-starter.zip vers **C:\chef**

Copiez tous les fichiers sous **chef-starter\chef-repo\.chef** vers votre **c:\chef**

Votre répertoire doit ressembler à ceci :

![][5]

Vous devez maintenant avoir quatre fichiers, y compris le fichier de publication Azure dans la racine de c:\chef

Les fichiers PEM contiennent votre organisation et les clés privées d'administration pour la communication, tandis que le fichier **knife.rb** contient votre configuration de couteau. Il convient de modifier le fichier **knife.rb**.

Ouvrez le fichier dans l'éditeur de votre choix et modifiez " cookbook_path " en supprimant /.../ du chemin d'accès afin qu'il apparaisse comme ci-dessous :

	cookbook_path  ["#{current_dir}/cookbooks"]

Ajoutez également la ligne suivante, pour refléter le nom de votre fichier de paramètres de publication Azure.   

	knife[:azure_publish_settings_file] = "yourfilename.publishsettings" 

Votre fichier knife.rb doit maintenant ressembler à ceci :

![][6]

Ces lignes garantissent que le couteau effectue ses références dans notre répertoire de livres de recettes dans c:\chef\cookbooks et utilise également notre fichier de paramètres de publication Azure pendant les opérations d'Azure.

## Installation du kit de développement Chef

Puis, téléchargez et installez le ChefDK (Kit de développement Chef) pour configurer votre station de travail Chef.

<a href="http://downloads.getchef.com/chef-dk/windows" target="_blank">http://downloads.getchef.com/chef-dk/windows</a>

![][7]

C'est très simple. Laissez-le s'installer dans son emplacement par défaut c:\opscode. Cette opération prendra environ 10 minutes.

Vérifiez que votre variable PATH comprend les entrées pour C:\opscode\chefdk\bin;C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

S'ils sont absents, assurez-vous de les ajouter !

**NOTEZ QUE L'ORDRE DU CHEMIN D'ACCÈS EST IMPORTANT !** Si vos chemins opscode ne sont pas dans l'ordre adéquat, cela créera des problèmes.

Redémarrez votre station de travail avant de continuer.

Ensuite, nous allons installer l'extension Knife Azure. Cela fournit le " plug-in Azure " à Knife.

Exécutez la commande suivante :

	chef gem install knife-azure --pre

**Remarque :** l'argument -pre garantit que vous recevez la dernière version RC du plug-in Knife Azure, qui permet d'accéder à la dernière série d'API.

Il est probable que plusieurs dépendances soient également installées en même temps.

![][8]


Pour vous assurer que tout est configuré correctement, exécutez :

	knife azure image list

Si tout est configuré correctement, vous verrez une liste d'images Azure disponibles défiler.

Félicitations ! La station de travail a été configurée.

## Création d'un livre de recettes

Dans Chef, un livre de recettes permet de définir un ensemble de commandes que vous souhaitez exécuter sur votre client managé. La création d'un livre de recettes est très simple et nous utilisons la commande Générer un livre de recettes Chef pour générer notre modèle de livre de recettes. Nous appellerons notre livre de recettes webserver, étant donné que je souhaite disposer d'une stratégie qui déploie IIS automatiquement.

Dans le répertoire C:\Chef, exécutez la commande suivante :

	chef generate cookbook webserver

Cela génère un ensemble de fichiers dans le répertoire **C:\Chef\cookbooks\webserver.** Nous devons maintenant définir le jeu de commandes que notre client Chef doit exécuter sur nos ordinateurs virtuels gérés.

Les commandes sont stockées dans le fichier **default.rb.** Dans ce fichier, je vais définir un ensemble de commandes qui installe les services IIS, démarre IIS et copie un fichier de modèle dans le dossier wwwroot.

Modifiez le fichier **C:\chef\cookbooks\webserver\recipes\default.rb** et ajoutez les lignes suivantes :

	powershell_script 'Install IIS' do
 		action :run
 		code 'add-windowsfeature Web-Server'
	end

	service 'w3svc' do
 		action [ :enable, :start ]
	end

	template 'c:\inetpub\wwwroot\Default.htm' do
 		source 'Default.htm.erb'
 		rights :read, 'Everyone'
	end

Enregistrez le fichier une fois que vous avez terminé.

## Création d'un modèle

Comme nous l'avons mentionné précédemment, nous devons générer un fichier de modèle qui sera utilisé comme notre page default.html.

Exécutez la commande suivante pour générer le modèle.

	chef generate template webserver Default.htm

Maintenant, accédez au fichier **C:\chef\cookbooks\webserver\templates\default\Default.htm.erb** et modifiez-le.

Ajoutez le code html simple " Hello World " et enregistrez le fichier.

## Téléchargez le livre de recettes sur le serveur Chef

Dans cette étape, nous allons faire une copie du livre de recettes que nous avons créé sur notre ordinateur local et le télécharger vers le serveur hébergé Chef. Une fois téléchargé, le livre de recettes apparaît sous l'onglet stratégie.

	knife cookbook upload webserver

![][9]

## Déploiement d'un ordinateur virtuel avec Knife Azure

Nous déploierons maintenant une machine virtuelle Azure et appliquerons le livre de recettes " Webserver " qui installera notre page web par défaut et notre service Web IIS.

Pour ce faire, utilisez la commande **knife azure server create**.

Voici un exemple de cette commande :

	knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Les paramètres sont clairs. Remplacez vos variables particulières et exécutez.

**Remarque :** à l'aide de la ligne de commande, nous automatisons également les règles de filtrage du réseau de point de terminaison en utilisant le paramètre -tcp-endpoints. Nous avons ouvert les ports 80 et 3389 pour fournir l'accès à notre page web et à la session RDP.

Après avoir exécuté la commande, naviguez sur le portail Azure et vous verrez votre ordinateur commencer à se configurer.

![][13]

Revenez à l'invite de commandes :

![][10]

Une fois le déploiement terminé, nous devrions être en mesure de nous connecter au service Web sur le port 80 que nous avions ouvert le port lorsque nous avons approvisionné la machine virtuelle avec la commande knife azure. Étant donné que cette machine virtuelle est la seule machine virtuelle dans notre service de cloud, nous allons la connecter avec l'url du service cloud.

![][11]

Comme vous pouvez le voir, le code html est créatif :)

N'oubliez pas que nous pouvons également nous connecter via une session RDP à partir du portail Azure via le port 3389.

Nous espérons que cela a été utile ! Démarrez votre voyage en matière d'Infrastructure en tant que code avec Azure dès aujourd'hui !

Diego Viso [MSFT]

<!--Image references-->
[2]: ./media/virtual-machines-automation-with-chef/2.png
[3]: ./media/virtual-machines-automation-with-chef/3.png
[4]: ./media/virtual-machines-automation-with-chef/4.png
[5]: ./media/virtual-machines-automation-with-chef/5.png
[6]: ./media/virtual-machines-automation-with-chef/6.png
[7]: ./media/virtual-machines-automation-with-chef/7.png
[8]: ./media/virtual-machines-automation-with-chef/8.png
[9]: ./media/virtual-machines-automation-with-chef/9.png
[10]: ./media/virtual-machines-automation-with-chef/10.png
[11]: ./media/virtual-machines-automation-with-chef/11.png
[13]: ./media/virtual-machines-automation-with-chef/13.png


<!--Link references-->

<!--HONumber=47-->
