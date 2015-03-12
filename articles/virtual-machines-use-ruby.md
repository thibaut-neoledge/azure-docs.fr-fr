<properties 
	pageTitle="Présentation d'Azure Multi-Factor Authentication" 
	description="En savoir plus sur l'authentification multi-facteur : méthode d'authentification qui nécessite l'utilisation de plusieurs méthodes de vérification et ajoute une deuxième couche critique de sécurité aux connexions et transactions des utilisateurs." 
	services="active-directory, multi-factor-authentication" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>



#Gestion d'Azure Virtual Machines au moyen de Ruby

Ce guide vous montre comment effectuer par programme des tâches de gestion courantes pour Azure Virtual Machines, telles que la création et la configuration de machines virtuelles et l'ajout de disques de données. Le Kit de développement logiciel (SDK) Azure pour Ruby permet d'accéder aux fonctionnalités de gestion des services pour une série de services Azure, y compris Azure Virtual Machines.

##Sommaire

* [Présentation de la gestion des services](#what-is)
* [Concepts](#concepts)
* [Création d'un certificat de gestion](#setup-certificate)
* [Création d'une application Ruby](#create-app)
* [Configuration de votre application pour utiliser le Kit de développement logiciel (SDK)](#configure-access)
* [Configuration d'une connexion de gestion Azure](#setup-connection)
* [Procédure : Utilisation de machines virtuelles](#virtual-machine)
* [Procédure : Utilisation d'images et de disques](#vm-images)
* [Procédure : Utilisation de services cloud](#cloud-services)
* [Procédure : Utilisation de services de stockage](#storage-services)
* [Étapes suivantes](#next-steps)

## <a name="what-is"> </a>Présentation de la gestion des services

Azure fournit des [API REST pour les opérations de gestion des services](http://msdn.microsoft.com/library/windowsazure/ee460799.aspx), y compris la gestion des machines virtuelles Azure. Le Kit de développement logiciel (SDK) Azure pour Ruby expose les opérations de gestion pour les machines virtuelles par le biais de la classe **Azure::VirtualMachineService**. La plupart des fonctionnalités de gestion des machines virtuelles disponibles par le biais du [portail de gestion Azure](https://manage.windowsazure.com) sont accessibles au moyen de cette classe.

Bien que l'API de gestion des services puisse être utilisée pour gérer une série de services hébergés sur Azure, ce document fournit uniquement des détails pour la gestion de machines virtuelles Azure.

## <a name="concepts"> </a>Concepts

Les machines virtuelles Azure sont implémentées en tant que 'rôles' au sein d'un service cloud. Chaque service cloud peut contenir un ou plusieurs rôles, qui sont regroupés de façon logique en déploiements. Le rôle définit les caractéristiques physiques globales de la machine virtuelle, telles que la quantité de mémoire disponible, le nombre de cœurs d'UC, etc.

Chaque machine virtuelle comporte un disque du système d'exploitation, qui contient le système d'exploitation amorçable. Une machine virtuelle peut avoir un ou plusieurs disques de données, qui sont des disques supplémentaires devant être utilisés pour stocker des données d'application. Les disques sont mis en œuvre en tant que disques durs virtuels stockés dans le stockage d'objets blob Azure. Les disques durs virtuels peuvent être exposés sous forme d''images', qui sont des modèles permettant de créer des disques utilisés par une machine virtuelle lors de sa création. Par exemple, la création d'une machine virtuelle utilisant une image Ubuntu résulte en la création d'un disque de système d'exploitation à partir de l'image Ubuntu.

La plupart des images sont fournies par Microsoft ou des partenaires, mais vous pouvez créer vos propres images ou en créer une à partir d'une machine virtuelle hébergée dans Azure.

## <a name="setup-certificate"> </a>Création d'un certificat de gestion Azure

Lors de l'exécution d'opérations de gestion des services, telles que celles exposées par le biais de la classe **Azure::VirtualMachineService**, vous devez fournir votre ID d'abonnement Azure et un fichier contenant un certificat de gestion pour votre abonnement. Tous deux sont utilisés par le Kit de développement logiciel (SDK) lors de l'authentification auprès de l'API REST Azure.

Vous pouvez obtenir l'ID d'abonnement et un certificat de gestion en utilisant l'interface de ligne de commande interplateforme Azure (xplat-cli). Consultez la rubrique [Installation et configuration de l'interface de ligne de commande interplateforme Azure](http://azure.microsoft.com/manage/install-and-configure-cli/) pour des informations sur l'installation et la configuration de xplat-cli.

Une fois que xplat-cli est configuré, vous pouvez effectuer les étapes suivantes pour récupérer votre ID d'abonnement Azure et exporter un certificat de gestion :

1. Pour récupérer l'ID d'abonnement, utilisez :

		azure account list

2. Pour exporter le certificat de gestion, utilisez la commande suivante :

		azure account cert export

	Une fois la commande exécutée, le certificat est exporté dans un fichier nommé &lt;azure-subscription-name&gt;.pem. Par exemple, si votre abonnement est intitulé **monsuperabonnement**, le fichier créé s'appellera **monsuperabonnement.pem**.

Prenez note de l'ID d'abonnement et de l'emplacement du fichier PEM contenant le certificat exporté, dans la mesure où ceux-ci seront utilisés plus tard dans ce document.

## <a name="create-app"></a>Création d'une application Ruby

Créez une application Ruby. Les exemples figurant dans ce document peuvent être mis en œuvre dans un fichier **.rb** unique.

## <a name="configure-access"></a>Configuration de votre application

Pour gérer les services Azure, vous devez télécharger et utiliser l'application gem Azure, qui contient le Kit de développement logiciel (SDK) Azure pour Ruby.

### Utilisation de la commande gem pour installer le package

1.  Utilisez une interface de ligne de commande telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (UNIX) pour accéder au dossier dans lequel vous avez créé votre exemple d'application.

2. Utilisez la commande suivante pour installer l'application gem azure :

		gem install azure

	Le résultat ressemble à ce qui suit :

		Fetching: mini_portile-0.5.1.gem (100%)
		Fetching: nokogiri-1.6.0-x86-mingw32.gem (100%)
		Fetching: mime-types-1.25.gem (100%)
		Fetching: systemu-2.5.2.gem (100%)
		Fetching: macaddr-1.6.1.gem (100%)
		Fetching: uuid-2.3.7.gem (100%)
		Fetching: azure-0.5.0.gem (100%)
		Successfully installed mini_portile-0.5.1
		Successfully installed nokogiri-1.6.0-x86-mingw32
		Successfully installed mime-types-1.25
		Successfully installed systemu-2.5.2
		Successfully installed macaddr-1.6.1
		Successfully installed uuid-2.3.7
		Successfully installed azure-0.5.0
		7 gems installed

	> [AZURE.NOTE] Si vous recevez une erreur en lien avec les autorisations, utilisez <code>sudo gem install azure</code> à la place.

### Appel de l'application gem

Au moyen d'un éditeur de texte, ajoutez ce qui suit en haut de votre fichier d'application Ruby. Cette commande permet de charger l'application gem Azure et rend le Kit de développement logiciel (SDK) Azure pour Ruby disponible pour votre application :

	require 'azure'

## <a name="setup-connection"> </a>Procédure : Connexion à la gestion des services

Pour effectuer correctement des opérations de gestion des services dans Azure, vous devez spécifier l'ID d'abonnement et le certificat obtenu dans la section [Création d'un certificat de gestion Azure](#setup-certificate) . La façon la plus simple de procéder consiste à spécifier l'ID et le chemin d'accès au fichier de certificat au moyen des variables d'environnement suivantes :

* AZURE\_MANAGEMENT\_CERTIFICATE - le chemin d'accès au fichier .PEM contenant le certificat de gestion.

* AZURE\_SUBSCRIPTION\_ID - l'ID d'abonnement pour votre abonnement Azure.

Vous pouvez également définir ces valeurs par programme dans votre application au moyen du code suivant :

	Azure.configure do |config|
	  config.management_certificate = 'path/to/certificate'
	  config.subscription_id = 'subscription ID'
	end

##<a name="virtual-machine"> </a>Procédure : Utilisation de machines virtuelles

Les opérations de gestion pour Azure Virtual Machines sont effectuées au moyen de la classe **Azure::VirtualMachineService**.

###Procédure : Création d'une machine virtuelle

Pour créer une machine virtuelle, utilisez la méthode **create\_virtual\_machine**. Cette méthode accepte un hachage contenant les paramètres suivants et renvoie une instance **Azure::VirtualMachineManagement::VirtualMachine** décrivant la machine virtuelle qui a été créée :

**Paramètres**

* **:vm\_name** - Nom de la machine virtuelle

* **:vm\_user** - Nom de l'utilisateur racine ou admin

* **:password** - Mot de passe à utiliser pour l'utilisateur racine ou admin

* **:image** - Image de système d'exploitation qui sera utilisée pour créer le disque de système d'exploitation pour cette machine virtuelle. Le disque de système d'exploitation sera stocké dans un disque dur virtuel créé dans un stockage d'objets blob.

* **:location** - Région dans laquelle la machine virtuelle sera créée. Il doit s'agir de la même région que le compte de stockage contenant les disques utilisés par cette machine virtuelle.

L'exemple suivant illustre la création d'une machine virtuelle au moyen de ces paramètres :

	vm_params = {
	  :vm_name => 'mygreatvm',
	  :vm_user => 'myuser',
	  :password => 'mypassword',
	  :image => 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20130824-fr-fr-30GB',
	  :location = 'East US'
	}

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.create_virtual_machine(vm_params)

	puts "A VM named #{vm.vm_name} was created in a cloud service named #{vm.cloud_service_name}."
	puts "It uses a disk named #{vm.disk_name}, which was created from a #{vm.os_type}-based image."
	puts "The virtual IP address of the machine is #{vm.ipaddress}."
    puts "The fully qualified domain name is #{vm.cloud_service_name}.cloudapp.net."

**Options**

Vous pouvez fournir un hachage de paramètres facultatifs vous permettant d'ignorer le comportement par défaut de la création de machine virtuelle, tel qu'en spécifiant un compte de stockage existant au lieu d'en créer un.

Voici les options disponibles lors de l'utilisation de la méthode **create\_virtual\_machine** :

* **:storage\_account\_name** - Nom du compte de stockage à utiliser pour le stockage d'images de disque. Le cas échéant, un nouveau compte de stockage est créé. S'il est omis, un nouveau compte de stockage est créé avec un nom basé sur le paramètre :vm\_name.

* **:cloud\_service\_name** - Nom du service cloud à utiliser pour l'hébergement de la machine virtuelle. Le cas échéant, un nouveau service cloud est créé. S'il est omis, un nouveau service cloud est créé avec un nom basé sur le paramètre :vm\_name.

* **:deployment\_name** - Nom du déploiement à utiliser lors du déploiement de la configuration de machine virtuelle

* **:tcp\_endpoints** - Ports TCP à exposer publiquement pour cette machine virtuelle. Le point de terminaison SSH (pour les machines virtuelles basées sur Linux) et le point de terminaison WinRM (pour les machines virtuelles basées sur Windows) ne doivent pas être spécifiés et seront créés automatiquement. Plusieurs ports peuvent être spécifiés, en les séparant par des virgules. Pour associer un port interne à un port public au moyen d'un numéro de port différent, utilisez le format **public port:internal port**. Par exemple, 80:8080 expose le port interne 8080 en tant que port public 80.

* **:service\_location** - Emplacement de stockage du certificat cible sur la machine virtuelle. S'applique uniquement aux machines virtuelles basées sur Windows.

* **:ssh\_private\_key\_file** - Fichier contenant la clé privée, qui sera utilisée pour sécuriser l'accès SSH à la machine virtuelle basée sur Linux. Il est également utilisé pour spécifier le certificat utilisé pour sécuriser WinRM si le transport HTTPS est sélectionné. Si **: ssh\_private\_key\_file** et **: ssh\_certificate\_file** sont omis, SSH utilisera uniquement l'authentification par mot de passe

* **:ssh\_certificate\_file** - Fichier contenant le fichier de certificat, qui sera utilisé pour sécuriser l'accès SSH à la machine virtuelle basée sur Linux. Il est également utilisé pour spécifier le certificat utilisé pour sécuriser WinRM si le transport HTTPS est sélectionné. Si **: ssh\_private\_key\_file** et **: ssh\_certificate\_file** sont omis, SSH utilisera uniquement l'authentification par mot de passe

* **:ssh\_port** - Port public qui sera utilisé pour la communication SSH. Si aucune valeur n'est spécifiée, le port SSH 22 est utilisé par défaut.

* **:vm\_size** - Taille de la machine virtuelle. Ceci détermine la taille de la mémoire, le nombre de cœurs, la bande passante et d'autres caractéristiques physiques de la machine virtuelle. Consultez la rubrique [Tailles de machines virtuelles et services cloud pour Microsoft Azure](http://msdn.microsoft.com/library/windowsazure/dn197896.aspx) pour connaître les tailles et caractéristiques physiques disponibles.

* **:winrm_transport** - Tableau des transports disponibles pour une utilisation avec WinRM. Les transports valides sont 'http' et 'https'. Si  'https' est spécifié comme transport, vous devez également utiliser **:ssh\_private\_key\_file** et **:ssh\_certificate\_file** pour spécifier le certificat permettant de sécuriser les communications HTTPS.

Voici un exemple de création de machine virtuelle qui utilise une petite instance de calcul, expose publiquement les ports pour le trafic HTTP (port local 8080, port public 80) et HTTPS (443), et active l'authentification par certificat pour les sessions SSH au moyen des fichiers de certificats spécifiés :

	vm_params = {
	  :vm_name => 'myvm',
	  :vm_user => 'myuser',
	  :password => 'mypassword',
	  :image => 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20130824-fr-fr-30GB',
	  :location = 'East US'
	}

	vm_opts = {
      :tcp_endpoints => '80:8080,443',
      :vm_size => 'Small',
      :ssh_private_key_file => '../sshkey/mykey.key',
      :ssh_certificate_file => '../sshkey/mykey.pem'
	}

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.create_virtual_machine(vm_params, vm_opts)

###Procédure : Affichage de la liste des machines virtuelles

Pour afficher la liste des machines virtuelles pour votre abonnement Azure, utilisez la méthode **list\_virtual\_machines**. Celle-ci renvoie un tableau d'objets **Azure::VirtualMachineManagement::VirtualMachine** :

	vm_mgr = Azure::VirtualMachineService.new
	virtual_machines = vm_mgr.list_virtual_machines

###Procédure : Obtention d'informations sur une machine virtuelle

Pour obtenir une instance de **Azure::VirtualMachineManagement::VirtualMachine** pour une machine virtuelle spécifique, utilisez la méthode **get\_virtual\_machine** et fournissez le nom de la machine virtuelle et du service cloud :

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.get_virtual_machine('myvm', 'mycloudservice')

###Procédure : Suppression d'une machine virtuelle

Pour supprimer une machine virtuelle, utilisez la méthode **delete\_virtual\_machine** et fournissez le nom de la machine virtuelle et du service cloud :

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.delete_virtual_machine('myvm', 'mycloudservice')

> [AZURE.WARNING] La méthode **delete_virtual_machine** supprime le service cloud et tous les disques associés à la machine virtuelle.

###Procédure : Arrêt d'une machine virtuelle

Pour arrêter une machine virtuelle, utilisez la méthode **shutdown\_virtual\_machine** et fournissez le nom de la machine virtuelle et du service cloud :

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.shutdown_virtual_machine('myvm', 'mycloudservice')

###Procédure : Démarrage d'une machine virtuelle

Pour démarrer une machine virtuelle, utilisez la méthode **start\_virtual\_machine** et fournissez le nom de la machine virtuelle et du service cloud :

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.start_virtual_machine('myvm', 'mycloudservice')

##<a name="vm-images"> </a>Procédure : Utilisation d'images et de disques de machine virtuelle

Les opérations sur les images de machine virtuelle sont effectuées au moyen de la classe **Azure::VirtualMachineImageService**. Les opérations sur les disques sont effectuées au moyen de la classe **Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService**.

###Procédure : Affichage de la liste des images de machine virtuelle

Pour afficher la liste des images de machine virtuelle, utilisez la méthode **list\_virtual\_machine\_images**. Cette méthode renvoie un tableau d'objets **Azure::VirtualMachineImageService**.

	image_mgr = Azure::VirtualMachineImageService.new
	images = image_mgr.list_virtual_machine_images

###Procédure : Affichage de la liste de disques

Pour afficher la liste des disques de votre abonnement Azure, utilisez la méthode **list\_virtual\_machine\_disks**. Celle-ci renvoie un tableau d'objets **Azure::VirtualMachineImageManagement::VirtualMachineDisk**.

	disk_mgr = Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService.new
	disks = disk_mgr.list_virtual_machine_disks

###Procédure : Suppression d'un disque

Pour supprimer un disque, utilisez la méthode **delete\_virtual\_machine\_disk** et spécifiez le nom du disque à supprimer :

	disk_mgr = Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService.new
	disk_mgr.delete_virtual_machine_disk

##<a name="cloud-services"> </a>Procédure : Utilisation de services cloud

Les opérations de gestion d'Azure Cloud Services sont effectuées au moyen de la classe **Azure::CloudService**.

###Procédure : d'un service cloud

Pour créer un service cloud, utilisez la méthode **create\_cloud\_service** et fournissez un nom et un hachage d'options. Les options valides sont les suivantes :

* **:location** - *Obligatoire*. Région dans laquelle le service cloud sera créé.

* **:description** - Description du service cloud.

Le code suivant crée un service cloud dans la région East US :

	cs_mgr = Azure::CloudService.new
	cs_mgr.create_cloud_service('mycloudservice', { :location => 'East US' })

###Procédure : Affichage de la liste de services cloud

Pour afficher la liste des services cloud pour votre abonnement Azure, utilisez la méthode **list\_cloud\_services**. Celle-ci renvoie un tableau d'objets **Azure::CloudServiceManagement::CloudService** :

	cs_mgr = Azure::CloudService.new
	cloud_services = cs_mgr.list_cloud_services

###Procédure : Détermination de l'existence ou non d'un service cloud

Pour vérifier si un service cloud spécifique existe déjà, utilisez la méthode **get\_cloud\_service** et fournissez le nom du service cloud. Renvoie **true** si un service cloud portant le nom spécifié existe ; sinon, renvoie **false**.

	cs_mgr = Azure::CloudService.new
	cs_exists = cs_mgr.get_cloud_service('mycloudservice')

###Procédure : Suppression d'un service cloud

Pour supprimer un service cloud, utilisez la méthode **delete\_cloud\_service** et fournissez le nom du service cloud :

	cs_mgr = Azure::CloudService.new
	cs_mgr.delete_cloud_service('mycloudservice')

###Procédure : Suppression d'un déploiement

Pour supprimer un déploiement de service cloud, utilisez la méthode **delete\_cloud\_service\_deployment** et fournissez le nom du service cloud :

	cs_mgr = Azure::CloudService.new
	cs_mgr.delete_cloud_service_deployment('mycloudservice')

##<a name="storage-services"> </a>Procédure : Utilisation de services de stockage

Les opérations de gestion d'Azure Cloud Services sont effectuées au moyen de la classe **Azure::StorageService**.

###Procédure : Création d'un compte de stockage

Pour créer un compte de stockage, utilisez la méthode **create\_storage\_account** et fournissez un nom et un hachage d'options. Les options valides sont les suivantes :

* **:location** - *Obligatoire*. Région dans laquelle le compte de stockage sera créé.

* **:description** - Description du compte de stockage.

Le code suivant crée un compte de stockage dans la région " East US " :

	storage_mgr = Azure::StorageService.new
	storage_mgr.create_storage_account('mystorage', { :location => 'East US' })

###Procédure : Affichage de la liste de comptes de stockage

Pour obtenir une liste des comptes de stockage pour votre abonnement Azure, utilisez la méthode **list\_storage\_accounts**. Celle-ci renvoie un tableau d'objets **Azure::StorageManagement::StorageAccount** :

	storage_mgr = Azure::StorageService.new
	accounts = storage_mgr.list_storage_accounts

###Procédure : Détermination de l'existence d'un compte de stockage

Pour déterminer si un compte de stockage spécifique existe, utilisez la méthode **get\_storage\_account** et spécifiez le nom du compte de stockage. Renvoie **true** si le compte de stockage existe ; sinon, renvoie **false**.

	storage_mgr = Azure::StorageService.new
	store_exists = storage_mgr.get_storage_account('mystorage')

###Procédure : Suppression d'un compte de stockage

Pour supprimer un compte de stockage, utilisez la méthode **delete\_storage\_account** et spécifiez le nom du compte de stockage :

	storage_mgr = Azure::StorageService.new
	storage_mgr.delete_storage_account('mystorage')

##<a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez appris les principes de base de la création par programme de machines virtuelles Azure, suivez les liens ci-dessous pour découvrir d'autres tâches avec les machines virtuelles.

* Visitez la page [Machines virtuelles](http://azure.microsoft.com/documentation/services/virtual-machines/).
*  Consultez la référence MSDN suivante : [Machines virtuelles](http://msdn.microsoft.com/library/windowsazure/jj156003.aspx)
* Découvrez comment héberger une [Application Ruby on Rails sur une machine virtuelle](http://azure.microsoft.com/develop/ruby/tutorials/web-app-with-linux-vm/)



<!--HONumber=42-->
