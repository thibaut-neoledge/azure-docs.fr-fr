<properties linkid="develop-python-service-management" urlDisplayName="Service Management" pageTitle="How to use the service management API (Python) - feature guide" metaKeywords="" description="Learn how to programmatically perform common service management tasks from Python." metaCanonical="" services="cloud-services" documentationCenter="Python" title="How to use Service Management from Python" authors="" solutions="" manager="" editor="" />

Utilisation de la gestion des services à partir de Python
=========================================================

Ce guide vous explique comment effectuer des tâches courantes de gestion des services par programme à partir de Python. La classe **ServiceManagementService** du [Kit de développement logiciel (SDK) Azure pour Python](https://www.windowsazure.com/en-us/develop/python/common-tasks/install-python/) prend en charge l'accès par programme à une grande partie des fonctionnalités liées à la gestion des services disponibles dans le [portail de gestion](https://manage.windowsazure.com/) (telles que **la création, la mise à jour et la suppression de services cloud, les déploiements, les services de gestion des données, les machines virtuelles et les groupes d'affinités**). Ces fonctionnalités peuvent être utiles pour la création d'applications nécessitant un accès par programme à la gestion des services.

Sommaire
--------

-   [Présentation de la gestion des services](#WhatIs)
-   [Concepts](#Concepts)
-   [Connexion à la gestion des services](#Connect)
-   [Affichage de la liste des emplacements disponibles](#ListAvailableLocations)
-   [Création d'un service cloud](#CreateCloudService)
-   [Suppression d'un service cloud](#DeleteCloudService)
-   [Création d'un déploiement](#CreateDeployment)
-   [Mise à jour d'un déploiement](#UpdateDeployment)
-   [Transfert des déploiements entre les environnements intermédiaire et de production](#MoveDeployments)
-   [Suppression d'un déploiement](#DeleteDeployment)
-   [Création d'un service de stockage](#CreateStorageService)
-   [Suppression d'un service de stockage](#DeleteStorageService)
-   [Création d'un groupe d'affinités](#CreateAffinityGroup)
-   [Suppression d'un groupe d'affinités](#DeleteAffinityGroup)
-   [Affichage de la liste des systèmes d'exploitation disponibles](#ListOperatingSystems)
-   [Création d'une image du système d'exploitation](#CreateVMImage)
-   [Suppression d'une image du système d'exploitation](#DeleteVMImage)
-   [Création d'une machine virtuelle](#CreateVM)
-   [Suppression d'une machine virtuelle](#DeleteVM)
-   [Étapes suivantes](#NextSteps)

Présentation de la gestion des services
---------------------------------------

L'API de gestion des services fournit un accès par programme aux fonctionnalités de gestion des services disponibles par le biais du [portail de gestion](https://manage.windowsazure.com/). Le Kit de développement logiciel (SDK) Azure pour Python vous permet de gérer vos services cloud, comptes de stockage et groupes d'affinités.

Pour utiliser l'API de gestion des services, vous devez [créer un compte Azure](http://www.windowsazure.com/en-us/pricing/free-trial/).

Concepts
--------

Le Kit de développement logiciel (SDK) Azure pour Python inclut l'[API de gestion des services Azure](http://msdn.microsoft.com/en-us/library/windowsazure/ee460799.aspx), qui est une API REST. Toutes les opérations de l'API sont effectuées au moyen du protocole SSL et sont mutuellement authentifiées au moyen de certificats X.509 v3. La gestion des services est accessible à partir d'un service s'exécutant dans Azure, ou directement sur Internet à partir de toute application pouvant envoyer une demande HTTPS et recevoir une réponse HTTPS.

Connexion à la gestion des services
-----------------------------------

Pour vous connecter au point de terminaison de la gestion de services, vous avez besoin de votre ID d'abonnement Azure et d'un certificat de gestion valide. Vous pouvez obtenir votre ID d'abonnement dans le [portail de gestion](https://manage.windowsazure.com/).

### Certificats de gestion sur Windows

Vous pouvez créer un certificat de gestion auto-signé sur votre machine au moyen de `makecert.exe`. Ouvrez une **invite de commandes Visual Studio** en tant qu'**administrateur** et utilisez la commande suivante, en remplaçant *AzureCertificate* par le nom du certificat que vous voulez utiliser.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

La commande va créer le fichier `.cer` et l'installer dans le magasin de certificats **Personnel**. Pour plus d'informations, consultez la rubrique [Créer et télécharger un certificat de gestion pour Windows Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg551722.aspx).

Une fois que vous avez créé le certificat, vous devez télécharger le fichier `.cer` sur Azure via l'action Télécharger de l'onglet Paramètres dans le [portail de gestion](https://manage.windowsazure.com/).

Une fois que vous avez obtenu votre ID d'abonnement, créé un certificat et téléchargé le fichier `.cer` sur Azure, vous pouvez vous connecter au point de terminaison de gestion Azure en transmettant l'ID d'abonnement et l'emplacement du certificat dans votre magasin de certificats **Personnel** vers **ServiceManagementService** (à nouveau, remplacez *AzureCertificate* par le nom de votre certificat) :

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\my\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

Dans l'exemple ci-dessus, `sms` est un objet **ServiceManagementService**. La classe **ServiceManagementService** est la classe principale utilisée pour gérer les services Azure.

### Certificats de gestion sur Mac/Linux

Vous pouvez utiliser [OpenSSL](http://www.openssl.org/) pour créer votre certificat de gestion. En fait, vous devez créer deux certificats, un pour le serveur (un fichier `.cer`) et un pour le client (un fichier `.pem`). Pour créer le fichier `.pem`, exécutez le code suivant :

    `openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Pour créer le certificat `.cer`, exécutez le code suivant :

    `openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Pour plus d'informations sur les certificats Azure, consultez la rubrique [Gestion des certificats dans Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg981929.aspx). Pour une description complète des paramètres OpenSSL, consultez la documentation disponible sur <http://www.openssl.org/docs/apps/openssl.html>.

Une fois que vous avez créé ces fichiers, vous devez télécharger le fichier `.cer` sur Azure au moyen de l'action Télécharger de l'onglet Paramètres dans le [portail de gestion](https://manage.windowsazure.com/). Prenez note également de l'endroit où vous avez enregistré le fichier `.pem`.

Une fois que vous avez obtenu votre ID d'abonnement, créé un certificat et téléchargé le fichier `.cer` sur Azure, vous pouvez vous connecter au point de terminaison de gestion Azure en transmettant l'ID d'abonnement et le chemin du fichier `.pem` vers **ServiceManagementService** :

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

Dans l'exemple ci-dessus, `sms` est un objet **ServiceManagementService**. La classe **ServiceManagementService** est la classe principale utilisée pour gérer les services Azure.

Affichage de la liste des emplacements disponibles
--------------------------------------------------

Pour afficher la liste des emplacements disponibles pour les services d'hébergement, utilisez la méthode **list\_locations** :

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Lorsque vous créez un service cloud, un service de stockage ou un groupe d'affinités, vous devez fournir un emplacement valide. La méthode **list\_locations** renvoie toujours une liste à jour des emplacements disponibles actuellement. Lors de la rédaction de cet article, les emplacements disponibles étaient les suivants :

-   Europe de l'Ouest
-   Asie du Sud-Est
-   Est de l'Asie
-   États-Unis - partie centrale septentrionale
-   Europe du Nord
-   États-Unis - partie centrale méridionale
-   Ouest des États-Unis
-   Est des États-Unis

Création d'un service cloud
---------------------------

Lorsque vous créez une application et que vous l'exécutez dans Azure, l'ensemble constitué du code et de la configuration est appelé [service cloud](http://windowsazure.com/en-us/documentation/articles/cloud-services-what-is) Azure (également connu sous le nom de *service hébergé* dans les versions antérieures d'Azure). La méthode **create\_hosted\_service** vous permet de créer un service hébergé en fournissant un nom de service hébergé (qui doit être unique dans Azure), une étiquette (automatiquement codée en base64), une description et un emplacement. Vous pouvez spécifier un groupe d'affinités au lieu d'un emplacement pour votre service.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    # Vous pouvez définir l'emplacement ou un groupe d'affinités
    sms.create_hosted_service(name, label, desc, location)

Vous pouvez afficher la liste de tous les services hébergés pour votre abonnement au moyen de la méthode **list\_hosted\_services** :

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Affinity group: ' + hosted_service.hosted_service_properties.affinity_group)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Si vous souhaitez obtenir des informations sur un service hébergé particulier, transmettez son nom à la méthode **get\_hosted\_service\_properties** :

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Affinity group: ' + hosted_service.hosted_service_properties.affinity_group)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Une fois que vous avez créé un service cloud, vous pouvez déployer votre code sur le service avec la méthode **create\_deployment**.

Suppression d'un service cloud
------------------------------

Vous pouvez supprimer un service cloud en transmettant son nom à la méthode **delete\_hosted\_service** :

    sms.delete_hosted_service('myhostedservice')

Notez qu'avant de supprimer un service, vous devez supprimer tous les déploiements associés (consultez la section [Suppression d'un déploiement](#DeleteDeployment) pour plus d'informations).

Création d'un déploiement
-------------------------

La méthode **create\_deployment** télécharge un nouveau [package de service](http://msdn.microsoft.com/en-us/library/windowsazure/jj155995.aspx) et crée un déploiement dans l'environnement intermédiaire ou de production. Les paramètres de cette méthode sont les suivants :

-   **name** : nom du service hébergé.
-   **deployment\_name** : nom du déploiement.
-   **slot** : chaîne indiquant l'emplacement `staging` ou `production`.
-   **package\_url** : URL du package de déploiement (fichier .cspgk). Le fichier de package doit être stocké dans un compte de stockage d'objets blob Azure sous le même abonnement que le service hébergé sur lequel le package est téléchargé. Vous pouvez créer un package de déploiement au moyen des [cmdlets Azure PowerShell](https://www.windowsazure.com/en-us/develop/php/how-to-guides/powershell-cmdlets/) ou de l'[outil en ligne de commande cspack](http://msdn.microsoft.com/en-us/library/windowsazure/gg432988.aspx).
-   **configuration** : fichier de configuration de service (fichier .cscfg) codé en base64.
-   **label** : étiquette du nom de service hébergé (automatiquement codé en base64).

L'exemple suivant crée un déploiement `v1` pour un service hébergé nommé `myhostedservice` :

    from azure import *
    from azure.servicemanagement import *
    import base64

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    deployment_name = 'v1'
    slot = 'production'
    package_url = 'URL_for_.cspkg_file'
    configuration = base64.b64encode(open('path_to_cscfg', 'rb'))
    label = 'myhostedservice'

    result = sms.create_deployment(name, slot, deployment_name, package_url, label, configuration)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Dans l'exemple ci-dessus, notez que l'état de l'opération **create\_deployment** peut être extrait en transmettant le résultat renvoyé par **create\_deployment** à la méthode **get\_operation\_status**.

Vous pouvez accéder aux propriétés de déploiement avec la méthode **get\_deployment\_by\_slot** ou **get\_deployment\_by\_name**. L'exemple suivant extrait un déploiement en spécifiant son emplacement. Il effectue également une itération dans toutes les instances du déploiement :

    result = sms.get_deployment_by_slot('myhostedservice', 'production')

    print('Name: ' + result.name)
    print('Slot: ' + result.deployment_slot)
    print('Private ID: ' + result.private_id)
    print('Status: ' + result.status)
    print('Instances:')
    for instance in result.role_instance_list:
        print('Instance name: ' + instance.instance_name)
        print('Instance status: ' + instance.instance_status)
        print('Instance size: ' + instance.instance_size)

Mise à jour d'un déploiement
----------------------------

Un déploiement peut être mis à jour au moyen de la méthode **change\_deployment\_configuration** ou **update\_deployment\_status**.

La méthode **change\_deployment\_configuration** vous permet de télécharger un nouveau fichier de configuration de service (`.cscfg`), qui changera n'importe quel paramètre de service (y compris le nombre d'instances d'un déploiement). Pour plus d'informations, consultez la rubrique [Schéma de configuration de service Azure (.cscfg)](http://msdn.microsoft.com/en-us/library/windowsazure/ee758710.aspx). L'exemple suivant indique comment télécharger un nouveau fichier de configuration de service :

    from azure import *
    from azure.servicemanagement import *
    import base64

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    deployment_name = 'v1'
    configuration = base64.b64encode(open('path_to_cscfg', 'rb'))

    result = sms.change_deployment_configuration(name, deployment_name, configuration)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Dans l'exemple ci-dessus, notez que l'état de l'opération **change\_deployment\_configuration** peut être extrait en transmettant le résultat renvoyé par **change\_deployment\_configuration** à la méthode **get\_operation\_status**.

La méthode **update\_deployment\_status** vous permet de définir un état de déploiement sur RUNNING ou SUSPENDED. L'exemple suivant explique comment définir l'état sur RUNNING pour un déploiement nommé `v1` d'un service hébergé appelé `myhostedservice` :

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    deployment_name = 'v1'

    result = update_deployment_status(name, deployment_name, 'Running')

Transfert des déploiements entre les environnements intermédiaire et de production
----------------------------------------------------------------------------------

Azure fournit deux environnements de déploiement : intermédiaire et de production. Généralement, un service est déployé dans l'environnement intermédiaire pour être testé avant son déploiement dans l'environnement de production. Lorsqu'il est temps de promouvoir le service de l'environnement intermédiaire à l'environnement de production, vous pouvez le faire sans redéployer le service. Pour cela, intervertissez les déploiements (pour plus d'informations sur l'intervertissement de déploiements, consultez la rubrique [Déploiement d'un service Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433027.aspx)).

L'exemple suivant indique comment utiliser la méthode **swap\_deployment** pour intervertir deux déploiements (avec les noms `v1` et `v2`). Dans l'exemple, avant d'appeler **swap\_deployment**, le déploiement `v1` se trouve dans l'emplacement de production, et le déploiement `v2` dans l'emplacement intermédiaire. Après avoir appelé **swap\_deployment**, `v2` se trouve dans l'emplacement de production, et `v1` dans l'emplacement intermédiaire.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.swap_deployment('myhostedservice', 'v1', 'v2')

Suppression d'un déploiement
----------------------------

Pour supprimer un déploiement, utilisez la méthode **delete\_deployment**. L'exemple suivant indique comment supprimer un déploiement nommé `v1`.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

Création d'un service de stockage
---------------------------------

Un [service de stockage](https://www.windowsazure.com/en-us/manage/services/storage/what-is-a-storage-account/) vous donne accès aux [objets blob](https://www.windowsazure.com/en-us/develop/python/how-to-guides/blob-service/), [tables](https://www.windowsazure.com/en-us/develop/python/how-to-guides/table-service/) et [files d'attente](https://www.windowsazure.com/en-us/develop/python/how-to-guides/queue-service/) Azure. Pour créer un service de stockage, vous avez besoin d'un nom pour le service (comprenant entre 3 et 24 lettres minuscules et unique au sein d'Azure), une description, une étiquette (jusqu'à 100 caractères, automatiquement codés en base64) et un emplacement ou un groupe d'affinités. L'exemple suivant indique comment créer un service de stockage en spécifiant un emplacement. Si vous voulez utiliser un groupe d'affinités, vous devez au préalable créer ce dernier (consultez la section [Création d'un groupe d'affinités](#CreateAffinityGroup)) et le configurer avec le paramètre **affinity\_group**.

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

Dans l'exemple ci-dessus, notez que l'état de l'opération **create\_storage\_account** peut être extrait en transmettant le résultat renvoyé par **create\_storage\_account** à la méthode **get\_operation\_status**.

Vous pouvez afficher la liste de vos comptes de stockage et leurs propriétés avec la méthode **list\_storage\_accounts** :

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Affinity group: ' + account.storage_service_properties.affinity_group)
        print('Location: ' + account.storage_service_properties.location)
        print('')

Suppression d'un service de stockage
------------------------------------

Vous pouvez supprimer un service de stockage en transmettant son nom à la méthode **delete\_storage\_account** : la suppression d'un service de stockage supprime toutes les données qui y sont stockées (objets blob, tables et files d'attente).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

Création d'un groupe d'affinités
--------------------------------

Un groupe d'affinités est un groupement logique de services Azure indiquant à Azure de localiser les services en vue de performances optimisées. Par exemple, vous pouvez créer un groupe d'affinités à l'emplacement « West US » (Ouest des États-Unis), puis créer un [service cloud](#CreateCloudService) dans ce groupe d'affinités. Si vous créez ensuite un service de stockage dans le même groupe d'affinités, Azure sait qu'il doit le placer dans l'emplacement « West US » et l'optimiser au sein du centre de données pour des performances optimales avec les services cloud dans le même groupe d'affinités.

Pour créer un groupe d'affinités, vous avez besoin d'un nom, d'une étiquette (automatiquement codée en base64) et d'un emplacement. Vous avez également la possibilité de fournir une description :

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myAffinityGroup'
    label = 'myAffinityGroup'
    location = 'West US'
    desc = 'my affinity group'

    sms.create_affinity_group(name, label, location, desc)

Une fois que vous avez créé un groupe d'affinités, vous pouvez spécifier le groupe (au lieu d'un emplacement) lors de la [création d'un service de stockage](#CreateStorageService).

Vous pouvez afficher la liste des groupes d'affinités et inspecter leurs propriétés en appelant la méthode **list\_affinity\_groups** :

    result = sms.list_affinity_groups()

    for group in result:
        print('Name: ' + group.name)
        print('Description: ' + group.description)
        print('Location: ' + group.location)
        print('')

Suppression d'un groupe d'affinités
-----------------------------------

Vous pouvez supprimer un groupe d'affinités en transmettant son nom à la méthode **delete\_affinity\_group**. Notez qu'avant de pouvoir supprimer un groupe d'affinités, ce dernier doit être dissocié de tout service (ou les services utilisant le groupe d'affinités doivent être supprimés).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_affinity_group('myAffinityGroup')

Affichage de la liste des systèmes d'exploitation disponibles
-------------------------------------------------------------

Pour afficher la liste des systèmes d'exploitation disponibles pour les services d'hébergement, utilisez la méthode **list\_operating\_systems** :

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Vous pouvez également utiliser la méthode **list\_operating\_system\_families**, qui regroupe les systèmes d'exploitation par famille :

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

Création d'une image du système d'exploitation
----------------------------------------------

Pour ajouter une image du système d'exploitation au référentiel d'images, utilisez la méthode **add\_os\_image** :

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mycentos'
    label = 'mycentos'
    os = 'Linux' # Linux ou Windows
    media_link = 'url_to_storage_blob_for_source_image_vhd'

    result = sms.add_os_image(label, media_link, name, os)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Pour afficher la liste des images de système d'exploitation qui sont disponibles, utilisez la méthode **list\_os\_images**. Cela inclut toutes les images de plateforme et les images utilisateur :

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Affinity group: ' + image.affinity_group)
        print('Media link: ' + image.media_link)
        print('')

Suppression d'une image du système d'exploitation
-------------------------------------------------

Pour supprimer une image utilisateur, utilisez la méthode **delete\_os\_image** :

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Création d'une machine virtuelle
--------------------------------

Pour créer une machine virtuelle, vous devez d'abord créer un [service cloud](#CreateCloudService). Ensuite, créez le déploiement de machine virtuelle en utilisant la méthode **create\_virtual\_machine\_deployment** :

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    # Vous pouvez définir l'emplacement ou un groupe d'affinités
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Nom d'une image de système d'exploitation telle que renvoyée par list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

    # Blob/conteneur de compte de stockage de destination où le disque de machine virtuelle
    # sera créé
    media_link = 'url_to_target_storage_blob_for_vm_hd'

    # Configuration de machine virtuelle Linux, vous pouvez utiliser WindowsConfigurationSet
    # pour une machine virtuelle Windows à la place
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

Suppression d'une machine virtuelle
-----------------------------------

Pour supprimer une machine virtuelle, vous devez d'abord supprimer le déploiement au moyen de la méthode **delete\_deployment** :

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

Le service cloud peut ensuite être supprimé au moyen de la méthode **delete\_hosted\_service** :

    sms.delete_hosted_service(service_name='myvm')

Étapes suivantes
----------------

Maintenant que vous avez appris les bases de la gestion des services, suivez ces liens pour effectuer des tâches plus complexes.

-   Consultez la référence MSDN suivante : [Cloud Services](http://msdn.microsoft.com/en-us/library/windowsazure/jj155995.aspx)
-   Consultez la référence MSDN suivante : [Azure Virtual Machines](http://msdn.microsoft.com/en-us/library/windowsazure/jj156003.aspx)

