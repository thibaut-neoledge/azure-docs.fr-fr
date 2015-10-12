<properties
	pageTitle="Utilisation de l'API de gestion des services (Python) - Guide des fonctionnalités"
	description="Découvrez comment effectuer des tâches courantes de gestion des services par programme à partir de Python."
	services="cloud-services"
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="03/11/2015"
	ms.author="huvalo"/>

# Utilisation de la gestion des services à partir de Python

Ce guide vous explique comment effectuer des tâches courantes de gestion des services par programme à partir de Python. La classe **ServiceManagementService** du [Kit de développement logiciel (SDK) Azure pour Python][download-SDK-Python] prend en charge l’accès par programme à une grande partie des fonctionnalités liées à la gestion des services disponibles dans le [portail de gestion][management-portal] (telles que **la création, la mise à jour et la suppression de services cloud, les déploiements, les services de gestion des données et les machines virtuelles**). Ces fonctionnalités peuvent être utiles pour la création d'applications nécessitant un accès par programme à la gestion des services.

## <a name="WhatIs"> </a>Présentation de la gestion des services
L'API de gestion des services fournit un accès par programme aux fonctionnalités de gestion des services disponibles par le biais du [portail de gestion][management-portal]. Le Kit de développement logiciel (SDK) Azure pour Python vous permet de gérer vos services cloud et vos comptes de stockage.

Pour utiliser l'API de gestion des services, vous devez [créer un compte Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"> </a>Concepts
Le Kit de développement logiciel (SDK) Azure pour Python inclut l'[API de gestion des services Azure][svc-mgmt-rest-api], qui est une API REST. Toutes les opérations de l'API sont effectuées au moyen du protocole SSL et sont mutuellement authentifiées au moyen de certificats X.509 v3. La gestion des services est accessible à partir d'un service s'exécutant dans Azure, ou directement sur Internet à partir de toute application pouvant envoyer une demande HTTPS et recevoir une réponse HTTPS.

## <a name="Connect"> </a>Connexion à la gestion des services
Pour vous connecter au point de terminaison de la gestion de services, vous avez besoin de votre ID d'abonnement Azure et d'un certificat de gestion valide. Vous pouvez obtenir votre ID d'abonnement dans le [portail de gestion][management-portal].

> [AZURE.NOTE]À partir du Kit de développement logiciel (SDK) Azure pour Python v0.8.0, il est maintenant possible d'utiliser des certificats créés avec OpenSSL sous Windows. Ceci nécessite Python 2.7.4 ou version ultérieure. Nous recommandons aux utilisateurs d’utiliser OpenSSL au lieu de .pfx, car la prise en charge des certificats .pfx risque de disparaître à l’avenir.

### Certificats de gestion sur Windows/Mac/Linux (OpenSSL)
Vous pouvez utiliser [OpenSSL](http://www.openssl.org/) pour créer votre certificat de gestion. En fait, vous devez créer deux certificats, un pour le serveur (un fichier `.cer`) et un pour le client (un fichier `.pem`). Pour créer le fichier `.pem`, exécutez le code suivant :

	`openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Pour créer le certificat `.cer`, exécutez le code suivant :

	`openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Pour plus d'informations sur les certificats Azure, consultez la rubrique [Gestion des certificats dans Azure](http://msdn.microsoft.com/library/windowsazure/gg981929.aspx). Pour une description complète des paramètres OpenSSL, consultez la documentation disponible sur [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Une fois ces fichiers créés, vous devez télécharger le fichier`.cer` sur Azure au moyen de l’action Télécharger de l’onglet Paramètres dans le [portail de gestion][management-portal]. Pensez également à noter l’endroit où vous avez enregistré le fichier `.pem`.

Une fois que vous avez obtenu votre ID d’abonnement, créé un certificat et téléchargé le fichier `.cer` sur Azure, vous pouvez vous connecter au point de terminaison de gestion Azure en transmettant l’ID d’abonnement et le chemin du fichier `.pem` vers **ServiceManagementService** :

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = '<path_to_.pem_certificate>'

	sms = ServiceManagementService(subscription_id, certificate_path)

Dans l’exemple ci-dessus, `sms` est un objet **ServiceManagementService**. La classe **ServiceManagementService** est la classe principale utilisée pour gérer les services Azure.

### Certificats de gestion sur Windows (MakeCert)

Vous pouvez créer un certificat de gestion auto-signé sur votre machine au moyen de `makecert.exe`. Ouvrez une **invite de commandes Visual Studio** en tant qu'**administrateur** et utilisez la commande suivante, en remplaçant *AzureCertificate* par le nom du certificat que vous voulez utiliser.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

La commande va créer le fichier `.cer` et l’installer dans le magasin de certificats **Personnel**. Pour plus d'informations, consultez la rubrique [Créer et télécharger un certificat de gestion pour Windows Azure](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx).

Une fois le certificat créé, vous devez télécharger le fichier `.cer` sur Azure via l’action Télécharger de l’onglet Paramètres dans le [portail de gestion][management-portal].

Une fois que vous avez obtenu votre ID d’abonnement, créé un certificat et téléchargé le fichier `.cer` sur Azure, vous pouvez vous connecter au point de terminaison de gestion Azure en transmettant l’ID d’abonnement et l’emplacement du certificat dans votre magasin de certificats **Personnel** vers **ServiceManagementService** (à nouveau, remplacez *AzureCertificate* par le nom de votre certificat) :

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

	sms = ServiceManagementService(subscription_id, certificate_path)

Dans l’exemple ci-dessus, `sms` est un objet **ServiceManagementService**. La classe **ServiceManagementService** est la classe principale utilisée pour gérer les services Azure.

## <a name="ListAvailableLocations"> </a>Affichage de la liste des emplacements disponibles

Pour afficher la liste des emplacements disponibles pour les services d'hébergement, utilisez la méthode **list_locations** :

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_locations()
	for location in result:
		print(location.name)

Quand vous créez un service cloud ou un service de stockage, vous devez fournir un emplacement valide. La méthode **list_locations** renvoie toujours une liste à jour des emplacements disponibles actuellement. Lors de la rédaction de cet article, les emplacements disponibles étaient les suivants :

- Europe de l'Ouest
- Europe du Nord
- Asie du Sud-Est
- Asie de l'Est
- Centre des États-Unis
- États-Unis - partie centrale septentrionale
- Centre-Sud des États-Unis
- Ouest des États-Unis
- Est des États-Unis
- Est du Japon
- Ouest du Japon
- Sud du Brésil
- Est de l’Australie
- Sud-est de l’Australie

## <a name="CreateCloudService"> </a>Création d’un service cloud

Lorsque vous créez une application et que vous l'exécutez dans Azure, l'ensemble constitué du code et de la configuration est appelé [service cloud] Azure (également connu sous le nom de *service hébergé* dans les versions antérieures d'Azure). La méthode **create_hosted_service** vous permet de créer un service hébergé en fournissant un nom de service hébergé (qui doit être unique dans Azure), une étiquette (automatiquement codée en base64), une description et un emplacement.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	label = 'myhostedservice'
	desc = 'my hosted service'
	location = 'West US'

	sms.create_hosted_service(name, label, desc, location)

Vous pouvez afficher la liste de tous les services hébergés pour votre abonnement au moyen de la méthode **list_hosted_services** :

	result = sms.list_hosted_services()

	for hosted_service in result:
		print('Service name: ' + hosted_service.service_name)
		print('Management URL: ' + hosted_service.url)
		print('Location: ' + hosted_service.hosted_service_properties.location)
		print('')

Si vous souhaitez obtenir des informations sur un service hébergé particulier, transmettez son nom à la méthode **get_hosted_service_properties** :

	hosted_service = sms.get_hosted_service_properties('myhostedservice')

	print('Service name: ' + hosted_service.service_name)
	print('Management URL: ' + hosted_service.url)
	print('Location: ' + hosted_service.hosted_service_properties.location)

Une fois que vous avez créé un service cloud, vous pouvez déployer votre code sur le service avec la méthode **create_deployment**.

## <a name="DeleteCloudService"> </a>Suppression d’un service cloud

Vous pouvez supprimer un service cloud en transmettant son nom à la méthode **delete_hosted_service** :

	sms.delete_hosted_service('myhostedservice')

Notez qu'avant de supprimer un service, vous devez supprimer tous les déploiements associés (consultez la section [Suppression d'un déploiement](#DeleteDeployment) pour plus d'informations).

## <a name="DeleteDeployment"> </a>Suppression d’un déploiement

Pour supprimer un déploiement, utilisez la méthode **delete_deployment**. L’exemple suivant indique comment supprimer un déploiement nommé `v1`.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Création d’un service de stockage

Un [service de stockage] vous donne accès aux [objets blob][azure-blobs], [tables][azure-tables] et [files d'attente][azure-queues] Azure. Pour créer un service de stockage, vous avez besoin d’un nom pour le service (comprenant entre 3 et 24 lettres minuscules et unique au sein d’Azure), une description, une étiquette (jusqu’à 100 caractères, automatiquement codés en base64) et un emplacement. L'exemple suivant indique comment créer un service de stockage en spécifiant un emplacement.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'mystorageaccount'
	label = 'mystorageaccount'
	location = 'West US'
	desc = 'My storage account description.'

	result = sms.create_storage_account(name, desc, label, location=location)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

Dans l'exemple ci-dessus, notez que l'état de l'opération **create_storage_account** peut être extrait en transmettant le résultat renvoyé par **create_storage_account** à la méthode **get_operation_status**.

Vous pouvez afficher la liste de vos comptes de stockage et leurs propriétés avec la méthode **list_storage_accounts** :

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_storage_accounts()
	for account in result:
		print('Service name: ' + account.service_name)
		print('Location: ' + account.storage_service_properties.location)
		print('')

## <a name="DeleteStorageService"> </a>Suppression d’un service de stockage

Vous pouvez supprimer un service de stockage en transmettant son nom à la méthode **delete_storage_account** : La suppression d'un service de stockage supprime toutes les données qui y sont stockées (objets blob, tables et files d'attente).

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>Affichage de la liste des systèmes d’exploitation disponibles

Pour afficher la liste des systèmes d'exploitation disponibles pour les services d'hébergement, utilisez la méthode **list_operating_systems** :

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_operating_systems()

	for os in result:
		print('OS: ' + os.label)
		print('Family: ' + os.family_label)
		print('Active: ' + str(os.is_active))

Vous pouvez également utiliser la méthode **list_operating_system_families**, qui regroupe les systèmes d'exploitation par famille :

	result = sms.list_operating_system_families()

	for family in result:
		print('Family: ' + family.label)
		for os in family.operating_systems:
			if os.is_active:
				print('OS: ' + os.label)
				print('Version: ' + os.version)
		print('')

## <a name="CreateVMImage"> </a>Création d’une image du système d’exploitation

Pour ajouter une image du système d'exploitation au référentiel d'images, utilisez la méthode **add_os_image** :

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'mycentos'
	label = 'mycentos'
	os = 'Linux' # Linux or Windows
	media_link = 'url_to_storage_blob_for_source_image_vhd'

	result = sms.add_os_image(label, media_link, name, os)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

Pour afficher la liste des images de système d'exploitation qui sont disponibles, utilisez la méthode **list_os_images**. Cela inclut toutes les images de plateforme et les images utilisateur :

	result = sms.list_os_images()

	for image in result:
		print('Name: ' + image.name)
		print('Label: ' + image.label)
		print('OS: ' + image.os)
		print('Category: ' + image.category)
		print('Description: ' + image.description)
		print('Location: ' + image.location)
		print('Media link: ' + image.media_link)
		print('')

## <a name="DeleteVMImage"> </a>Suppression d’une image du système d’exploitation

Pour supprimer une image utilisateur, utilisez la méthode **delete_os_image** :

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.delete_os_image('mycentos')

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Création d’une machine virtuelle

Pour créer une machine virtuelle, vous devez d'abord créer un [service cloud](#CreateCloudService). Ensuite, créez le déploiement de machine virtuelle en utilisant la méthode **create_virtual_machine_deployment** :

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myvm'
	location = 'West US'

	#Set the location
	sms.create_hosted_service(service_name=name,
		label=name,
		location=location)

	# Name of an os image as returned by list_os_images
	image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-fr-FR-30GB.vhd'

	# Destination storage account container/blob where the VM disk
	# will be created
	media_link = 'url_to_target_storage_blob_for_vm_hd'

	# Linux VM configuration, you can use WindowsConfigurationSet
	# for a Windows VM instead
	linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

	os_hd = OSVirtualHardDisk(image_name, media_link)

	sms.create_virtual_machine_deployment(service_name=name,
		deployment_name=name,
		deployment_slot='production',
		label=name,
		role_name=name,
		system_config=linux_config,
		os_virtual_hard_disk=os_hd,
		role_size='Small')

## <a name="DeleteVM"> </a>Suppression d’une machine virtuelle

Pour supprimer une machine virtuelle, vous devez d'abord supprimer le déploiement au moyen de la méthode **delete_deployment** :

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment(service_name='myvm',
		deployment_name='myvm')

Le service cloud peut ensuite être supprimé au moyen de la méthode **delete_hosted_service** :

	sms.delete_hosted_service(service_name='myvm')

##Création d’une machine virtuelle à partir d’une image de machine virtuelle capturée

Pour capturer une image de machine virtuelle, appelez d’abord la méthode **capture_vm_image** :

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	# replace the below three parameters with actual values
	hosted_service_name = 'hs1'
	deployment_name = 'dep1'
	vm_name = 'vm1'

	image_name = vm_name + 'image'
	image = CaptureRoleAsVMImage	('Specialized',
		image_name,
		image_name + 'label',
		image_name + 'description',
		'english',
		'mygroup')

	result = sms.capture_vm_image(
			hosted_service_name,
			deployment_name,
			vm_name,
			image
		)

Ensuite, pour être sûr d’avoir correctement capturé l’image, utilisez l’API **list_vm_images** et vérifiez que votre image est affichée dans les résultats :

	images = sms.list_vm_images()

Pour créer enfin la machine virtuelle à l’aide de l’image capturée, utilisez la méthode **create_virtual_machine_deployment** comme avant, mais cette fois en passant la méthode vm_image_name à la place.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myvm'
	location = 'West US'

	#Set the location
	sms.create_hosted_service(service_name=name,
		label=name,
		location=location)

	sms.create_virtual_machine_deployment(service_name=name,
		deployment_name=name,
		deployment_slot='production',
		label=name,
		role_name=name,
		system_config=linux_config,
		os_virtual_hard_disk=None,
		role_size='Small',
		vm_image_name = image_name)

Pour en savoir plus sur la capture d’une machine virtuelle Linux, consultez la page [Capture d’une machine virtuelle Linux](../virtual-machines-linux-capture-image.md).

Pour en savoir plus sur la capture d’une machine virtuelle Windows, consultez la page [Capture d’une machine virtuelle Windows](../virtual-machines-capture-image-windows-server.md).

## <a name="What's Next"> </a>Étapes suivantes

Vous connaissez désormais les principes de base de la gestion des services. Vous pouvez maintenant accéder à la [documentation complète de référence sur l’API du Kit de développement logiciel (SDK) Azure pour Python](http://azure-sdk-for-python.readthedocs.org/en/documentation/index.html) et effectuer facilement des tâches complexes pour gérer votre application python.

Pour plus d’informations, consultez le [Centre pour développeurs Python](/develop/python/).

[What is Service Management]: #WhatIs
[Concepts]: #Concepts
[How to: Connect to service management]: #Connect
[How to: List available locations]: #ListAvailableLocations
[How to: Create a cloud service]: #CreateCloudService
[How to: Delete a cloud service]: #DeleteCloudService
[How to: Create a deployment]: #CreateDeployment
[How to: Update a deployment]: #UpdateDeployment
[How to: Move deployments between staging and production]: #MoveDeployments
[How to: Delete a deployment]: #DeleteDeployment
[How to: Create a storage service]: #CreateStorageService
[How to: Delete a storage service]: #DeleteStorageService
[How to: List available operating systems]: #ListOperatingSystems
[How to: Create an operating system image]: #CreateVMImage
[How to: Delete an operating system image]: #DeleteVMImage
[How to: Create a virtual machine]: #CreateVM
[How to: Delete a virtual machine]: #DeleteVM
[Next Steps]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[download-SDK-Python]: https://www.windowsazure.com/develop/python/common-tasks/install-python/
[service cloud]: http://windowsazure.com/documentation/articles/cloud-services-what-is
[service package]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Azure PowerShell cmdlets]: https://www.windowsazure.com/develop/php/how-to-guides/powershell-cmdlets/
[cspack commandline tool]: http://msdn.microsoft.com/library/windowsazure/gg432988.aspx
[Deploying an Azure Service]: http://msdn.microsoft.com/library/windowsazure/gg433027.aspx
[service de stockage]: https://www.windowsazure.com/manage/services/storage/what-is-a-storage-account/
[azure-blobs]: https://www.windowsazure.com/develop/python/how-to-guides/blob-service/
[azure-tables]: https://www.windowsazure.com/develop/python/how-to-guides/table-service/
[azure-queues]: https://www.windowsazure.com/develop/python/how-to-guides/queue-service/
[Azure Service Configuration Schema (.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[Cloud Services]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Virtual Machines]: http://msdn.microsoft.com/library/windowsazure/jj156003.aspx
 

<!---HONumber=Oct15_HO1-->