<properties linkid="develop-php-how-to-guides-service-management" urlDisplayName="Service Management" pageTitle="How to use Azure service management APIs (PHP)" metaKeywords="" description="Learn how to use the Azure PHP Service Management APIs to manage cloud services and other Azure applications." metaCanonical="" services="" documentationCenter="PHP" title="How to use Service Management from PHP" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" videoId="" scriptId="" />

Utilisation de la gestion des services à partir de PHP
======================================================

Ce guide vous explique comment effectuer des tâches courantes de gestion des services par programme à partir de PHP. La classe [ServiceManagementRestProxy](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/ServiceManagementRestProxy.php) du [Kit de développement logiciel (SDK) Azure pour PHP](../php-download-sdk/) prend en charge l'accès par programme à une grande partie des fonctionnalités liées à la gestion des services disponibles sur le [portail de gestion](https://manage.windowsazure.com/) (telles que **la création, la mise à jour et la suppression de services cloud, les déploiements, les services de stockage et les groupes d'affinités**). Ces fonctionnalités peuvent être utiles pour la création d'applications nécessitant un accès par programme à la gestion des services.

Sommaire
--------

-   [Présentation de la gestion des services](#WhatIs)
-   [Concepts](#Concepts)
-   [Création d'une application PHP](#CreateApplication)
-   [Obtention des bibliothèques clientes Azure](#GetClientLibraries)
-   [Connexion à la gestion des services](#Connect)
-   [Affichage de la liste des emplacements disponibles](#ListAvailableLocations)
-   [Création d'un service cloud](#CreateCloudService)
-   [Suppression d'un service cloud](#DeleteCloudService)
-   [Création d'un déploiement](#CreateDeployment)
-   [Mise à jour d'un déploiement](#UpdateDeployment)
-   [Transfert des déploiements entre les environnements intermédiaire et de production](#MoveDeployments)
-   [Suppression d'un déploiement](#DeleteDeployment)
-   [Création d’un service de stockage](#CreateStorageService)
-   [Suppression d'un service de stockage](#DeleteStorageService)
-   [Création d'un groupe d'affinités](#CreateAffinityGroup)
-   [Suppression d'un groupe d'affinités](#DeleteAffinityGroup)

Présentation de la gestion des services
---------------------------------------

L'API de gestion des services fournit un accès par programme aux fonctionnalités de gestion des services disponibles par le biais du [portail de gestion](https://manage.windowsazure.com/). Le Kit de développement logiciel (SDK) Azure pour PHP vous permet de gérer vos services cloud, comptes de stockage et groupes d'affinités.

Pour utiliser l'API de gestion des services, vous devez [créer un compte Azure](/en-us/pricing/free-trial/).

Concepts
--------

Le Kit de développement logiciel (SDK) Azure pour PHP inclut l'[API de gestion des services Azure](http://msdn.microsoft.com/en-us/library/windowsazure/ee460799.aspx), qui est une API REST. Toutes les opérations de l'API sont effectuées au moyen du protocole SSL et sont mutuellement authentifiées au moyen de certificats X.509 v3. La gestion des services est accessible à partir d'un service s'exécutant dans Azure, ou directement sur Internet à partir de toute application pouvant envoyer une demande HTTPS et recevoir une réponse HTTPS.

Création d'une application PHP
------------------------------

La référence de classes dans le Kit de développement logiciel (SDK) Azure pour PHP depuis votre code constitue la seule exigence pour créer une application PHP qui utilise la gestion des services Azure. Vous pouvez utiliser tous les outils de développement pour créer votre application, y compris Bloc-notes.

Dans ce guide, vous allez utiliser des fonctionnalités de service qui peuvent être appelées dans une application PHP en local, ou dans le code s'exécutant dans un rôle Web, un rôle de travail ou un site Web Azure.

Obtention des bibliothèques clientes Azure
------------------------------------------

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

Connexion à la gestion des services
-----------------------------------

Pour vous connecter au point de terminaison de la gestion de services, vous avez besoin de votre ID d'abonnement Azure et du chemin d'accès vers un certificat de gestion valide. Vous pouvez obtenir votre ID d'abonnement par le biais du [portail de gestion](https://manage.windowsazure.com/) et créer des certificats de gestion de différentes manières. Dans ce guide, nous avons utilisé [OpenSSL](http://www.openssl.org/), que vous pouvez [télécharger pour Windows](http://www.openssl.org/related/binaries.html) et exécuter sur une console.

En fait, vous devez créer deux certificats, un pour le serveur (un fichier `.cer`) et un pour le client (un fichier `.pem`). Pour créer le fichier `.pem`, exécutez le code suivant :

    `openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Pour créer le certificat `.cer`, exécutez le code suivant :

    `openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Pour plus d'informations sur les certificats Azure, consultez la rubrique [Vue d'ensemble des certificats dans Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg981935.aspx). Pour une description complète des paramètres OpenSSL, consultez la documentation disponible sur <http://www.openssl.org/docs/apps/openssl.html>.

Si vous avez téchargé et importé le fichier de paramètres de publication au moyen des [outils en ligne de commande Azure](../command-line-tools/), vous pouvez utiliser le fichier `.pem` créé par les outils au lieu de créer le vôtre. Les outils créent un fichier `.cer` pour vous, le téléchargent sur Azure et placent le fichier `.pem` correspondant dans le répertoire `.azure` de votre ordinateur (dans votre répertoire utilisateur).

Une fois que vous avez créé ces fichiers, vous devez télécharger le fichier `.cer` sur Azure par le biais du [portail de gestion](https://manage.windowsazure.com/). Prenez note également de l'endroit où vous avez enregistré le fichier `.pem`.

Une fois que vous avez obtenu votre ID d'abonnement, créé un certificat et téléchargé le fichier `.cer` sur Azure, vous pouvez vous connecter au point de terminaison de gestion Azure en créant une chaîne de connexion et en la transmettant à la méthode **createServiceManagementService** sur la classe **ServicesBuilder** :

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $conn_string = "SubscriptionID=<your_subscription_id>;CertificatePath=<path_to_.pem_certificate>";

    $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

Dans l'exemple ci-dessus, `$serviceManagementRestProxy` est un objet [ServiceManagementRestProxy](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/ServiceManagementRestProxy.php). La classe **ServiceManagementRestProxy** est la classe principale utilisée pour gérer les services Azure.

Affichage de la liste des emplacements disponibles
--------------------------------------------------

Pour afficher la liste des emplacements disponibles pour les services d'hébergement, utilisez la méthode **ServiceManagementRestProxy-\>listLocations** :

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

        $result = $serviceManagementRestProxy->listLocations();

        $locations = $result->getLocations();

        foreach($locations as $location){
              echo $location->getName()."<br />";
        }
    }
    catch(ServiceException $e){
        // Gérez une exception en fonction des codes et des messages d'erreur.
        // Les codes et messages d'erreur se trouvent ici : 
        // http://msdn.microsoft.com/en-us/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Lorsque vous créez un service cloud, un service de stockage ou un groupe d'affinités, vous devez fournir un emplacement valide. La méthode **listLocations** renvoie toujours une liste à jour des emplacements disponibles actuellement. Lors de la rédaction de cet article, les emplacements disponibles étaient les suivants :

-   États-Unis
-   Europe
-   Europe de l'Ouest
-   Asie
-   Asie du Sud-Est
-   Est de l'Asie
-   États-Unis - partie centrale septentrionale
-   Europe du Nord
-   États-Unis - partie centrale méridionale
-   Ouest des États-Unis
-   Est des États-Unis

> [WACOM.NOTE] Dans les exemples de code suivants, les emplacements sont transmis aux méthodes sous forme de chaînes. Cependant, vous avez également la possibilité de les transmettre sous forme d'énumérations au moyen de la classe `WindowsAzure\ServiceManagement\Models\Locations`. Par exemple, au lieu de transmettre « West US » (Ouest des États-Unis) à une méthode acceptant un emplacement, vous pouvez transmettre `Locations::WEST_US`.

Création d'un service cloud
---------------------------

Lorsque vous créez une application et que vous l'exécutez dans Azure, l'ensemble constitué du code et de la configuration est appelé [service cloud](../cloud-services-what-is/) Azure (également connu sous le nom de *service hébergé* dans les versions antérieures d'Azure). La méthode **createHostedServices** vous permet de créer un service hébergé en fournissant un nom de service hébergé (qui doit être unique dans Azure), une étiquette (le nom du service hébergé automatiquement codé en base64) et un objet **CreateServiceOptions**. L'objet [CreateServiceOptions](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/CreateServiceOptions.php) vous permet de configurer un emplacement *ou* le groupe d'affinités pour votre service.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
    use WindowsAzure\Common\ServiceException;

    try{
        // Créez le proxy REST.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myhostedservice";
        $label = base64_encode($name);
        $options = new CreateServiceOptions();
        $options->setLocation('West US');
        // Vous pouvez utiliser setAffinityGroup au lieu de setLocation
        // pour définir un groupe d'affinités.

        $result = $serviceManagementRestProxy->createHostedService($name, $label, $options);
    }
    catch(ServiceException $e){
        // Gérez une exception en fonction des codes et des messages d'erreur.
        // Les codes et messages d'erreur se trouvent ici : 
        // http://msdn.microsoft.com/en-us/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Vous pouvez afficher la liste de tous les services hébergés pour votre abonnement au moyen de la méthode **listHostedServices**, qui renvoie un objet [ListHostedServicesResult](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php). En appelant la méthode **getHostedServices**, vous pouvez ensuite créer une boucle sur un tableau d'objets [HostedServices] et extraire les propriétés des services :

    $listHostedServicesResult = $serviceManagementRestProxy->listHostedServices();

    $hosted_services = $listHostedServicesResult->getHostedServices();

    foreach($hosted_services as $hosted_service){
        echo "Service name: ".$hosted_service->getName()."<br />";
        echo "Management URL: ".$hosted_service->getUrl()."<br />";
        echo "Affinity group: ".$hosted_service->getAffinityGroup()."<br />";
        echo "Location: ".$hosted_service->getLocation()."<br />";
        echo "------<br />";
        }

Si vous souhaitez obtenir des informations sur un service hébergé particulier, transmettez son nom à la méthode **getHostedServiceProperties** :

    $getHostedServicePropertiesResult = $serviceManagementRestProxy->getHostedServiceProperties("myhostedservice");
        
    $hosted_service = $getHostedServicePropertiesResult->getHostedService();
        
    echo "Service name: ".$hosted_service->getName()."<br />";
    echo "Management URL: ".$hosted_service->getUrl()."<br />";
    echo "Affinity group: ".$hosted_service->getAffinityGroup()."<br />";
    echo "Location: ".$hosted_service->getLocation()."<br />";

Une fois que vous avez créé un service cloud, vous pouvez déployer votre code sur le service avec la méthode [createDeployment](#CreateDeployment).

Suppression d'un service cloud
------------------------------

Vous pouvez supprimer un service cloud en transmettant son nom à la méthode **deleteHostedService** :

    $serviceManagementRestProxy->deleteHostedService("myhostedservice");

Notez qu'avant de supprimer un service, vous devez supprimer tous les déploiements associés Pour plus d'informations, consultez la rubrique [Suppression d'un déploiement](#DeleteDeployment).

Création d'un déploiement
-------------------------

La méthode **createDeployment** télécharge un nouveau [package de service](http://msdn.microsoft.com/en-us/library/windowsazure/gg433093) et crée un déploiement dans l'environnement intermédiaire ou de production. Les paramètres de cette méthode sont les suivants :

-   **$name** : nom du service hébergé.
-   **$deploymentName** : nom du déploiement.
-   **$slot** : énumération indiquant l'emplacement intermédiaire ou de production.
-   **$packageUrl** : URL du package de déploiement (fichier .cspgk). Le fichier de package doit être stocké dans un compte de stockage d'objets blob Azure sous le même abonnement que le service hébergé sur lequel le package est téléchargé. Vous pouvez créer un package de déploiement au moyen des [cmdlets Azure PowerShell](../install-configure-powershell/) ou de l'[outil en ligne de commande cspack](http://msdn.microsoft.com/en-us/library/windowsazure/gg432988.aspx).
-   **$configuration** : fichier de configuration de service (fichier .cscfg).
-   **$label** : nom du service hébergé codé en base64.

L'exemple suivant crée un déploiement dans l'emplacement de production d'un service hébergé nommé `myhostedservice` :

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // Créez le proxy REST.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myhostedservice";
        $deploymentName = "v1";
        $slot = DeploymentSlot::PRODUCTION;
        $packageUrl = "URL_for_.cspkg_file";
        $configuration = base64_encode(file_get_contents('path_to_.cscfg_file'));
        $label = base64_encode($name);

        $result = $serviceManagementRestProxy->createDeployment($name,
                                                         $deploymentName,
                                                         $slot,
                                                         $packageUrl,
                                                         $configuration,
                                                         $label);
        
        $status = $serviceManagementRestProxy->getOperationStatus($result);
        echo "Operation status: ".$status->getStatus()."<br />";
    }
    catch(ServiceException $e){
        // Gérez une exception en fonction des codes et des messages d'erreur.
        // Les codes et messages d'erreur se trouvent ici : 
        // http://msdn.microsoft.com/en-us/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Dans l'exemple ci-dessus, notez que l'état de l'opération **createDeployment** peut être extrait en transmettant le résultat renvoyé par **createDeployment** à la méthode **getOperationStatus**.

Vous pouvez accéder aux propriétés de déploiement à l'aide de la méthode **getDeployment**. L'exemple suivant permet d'extraire un déploiement en spécifiant son emplacement dans l'objet [GetDeploymentOptions](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php), mais vous pourriez tout aussi bien spécifier le nom du déploiement. L'exemple effectue également une itération dans toutes les instances du déploiement :

    $options = new GetDeploymentOptions();
    $options->setSlot(DeploymentSlot::PRODUCTION);
        
    $getDeploymentResult = $serviceManagementRestProxy->getDeployment("myhostedservice", $options);
    $deployment = $getDeploymentResult->getDeployment();

    echo "Name: ".$deployment->getName()."<br />";
    echo "Slot: ".$deployment->getSlot()."<br />";
    echo "Private ID: ".$deployment->getPrivateId()."<br />";
    echo "Status: ".$deployment->getStatus()."<br />";
    echo "Instances: <br />";
    foreach($deployment->getroleInstanceList() as $roleInstance){
        echo "Instance name: ".$roleInstance->getInstanceName()."<br />";
        echo "Instance status: ".$roleInstance->getInstanceStatus()."<br />";
        echo "Instance size: ".$roleInstance->getInstanceSize()."<br />";
    }
    echo "------<br />";

Mise à jour d'un déploiement
----------------------------

Vous pouvez mettre un déploiement à jour à l'aide de la méthode **changeDeploymentConfiguration** ou **updateDeploymentStatus**.

La méthode **changeDeploymentConfiguration** vous permet de télécharger un nouveau fichier de configuration de service (`.cscfg`), qui changera n'importe quel paramètre de service (y compris le nombre d'instances dans un déploiement). Pour plus d'informations, consultez la rubrique [Schéma de configuration de service Azure (.cscfg)](http://msdn.microsoft.com/en-us/library/windowsazure/ee758710.aspx). L'exemple suivant indique comment télécharger un nouveau fichier de configuration de service :

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\ChangeDeploymentConfigurationOptions;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // Créez le proxy REST.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myhostedservice";
        $configuration = base64_encode(file_get_contents('path to .cscfg file'));
        $options = new ChangeDeploymentConfigurationOptions();
        $options->setSlot(DeploymentSlot::PRODUCTION);

        $result = $serviceManagementRestProxy->changeDeploymentConfiguration($name, $configuration, $options);
        
        $status = $serviceManagementRestProxy->getOperationStatus($result);
        echo "Operation status: ".$status->getStatus()."<br />";
    }
    catch(ServiceException $e){
        // Gérez une exception en fonction des codes et des messages d'erreur.
        // Les codes et messages d'erreur se trouvent ici : 
        // http://msdn.microsoft.com/en-us/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Dans l'exemple ci-dessus, notez que l'état de l'opération **changeDeploymentConfiguration** peut être extrait en transmettant le résultat renvoyé par **changeDeploymentConfiguration** à la méthode **getOperationStatus**.

La méthode **updateDeploymentStatus** vous permet de définir un état de déploiement sur RUNNING ou SUSPENDED. L'exemple suivant explique comment définir l'état sur RUNNING pour un déploiement de l'emplacement de production d'un service hébergé appelé `myhostedservice` :

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\DeploymentStatus;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
    use WindowsAzure\Common\ServiceException;

    try{
        // Créez le proxy REST.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $options = new GetDeploymentOptions();
        $options->setSlot(DeploymentSlot::PRODUCTION);
        
        $result = $serviceManagementRestProxy->updateDeploymentStatus("myhostedservice", DeploymentStatus::RUNNING, $options);
    }
    catch(ServiceException $e){
        // Gérez une exception en fonction des codes et des messages d'erreur.
        // Les codes et messages d'erreur se trouvent ici : 
        // http://msdn.microsoft.com/en-us/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Transfert des déploiements entre les environnements intermédiaire et de production
----------------------------------------------------------------------------------

Azure fournit deux environnements de déploiement : intermédiaire et de production. Généralement, un service est déployé dans l'environnement intermédiaire pour être testé avant son déploiement dans l'environnement de production. Lorsqu'il est temps de promouvoir le service de l'environnement intermédiaire à l'environnement de production, vous pouvez le faire sans redéployer le service. Pour cela, intervertissez les déploiements Pour plus d'informations sur l'intervertissement de déploiements, consultez la rubrique [Vue d'ensemble de la gestion des déploiements dans Azure](http://msdn.microsoft.com/en-us/library/windowsazure/hh386336.aspx).

L'exemple suivant indique comment utiliser la méthode **swapDeployment** pour intervertir deux déploiements (avec les noms `v1` et `v2`). Dans l'exemple, avant l'appel de **swapDeployment**, le déploiement `v1` se trouve dans l'emplacement de production, et le déploiement `v2` dans l'emplacement intermédiaire. Une fois **swapDeployment** appelé, `v2` se trouve dans l'emplacement de production, et `v1` dans l'emplacement intermédiaire.

    require_once 'vendor\autoload.php';  

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // Créez le proxy REST.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $result = $serviceManagementRestProxy->swapDeployment("myhostedservice", "v2", "v1");
    }
    catch(ServiceException $e){
        // Gérez une exception en fonction des codes et des messages d'erreur.
        // Les codes et messages d'erreur se trouvent ici : 
        // http://msdn.microsoft.com/en-us/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Suppression d'un déploiement
----------------------------

Pour supprimer un déploiement, utilisez la méthode **deleteDeployment**. L'exemple suivant indique comment supprimer un déploiement dans l'environnement intermédiaire à l'aide de la méthode **setSlot** sur un objet [GetDeploymentOptions](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php), puis en le transmettant à **deleteDeployment**. Au lieu de spécifier un déploiement par emplacement, vous avez la possibilité d'utiliser la méthode **setName** sur la classe [GetDeploymentOptions) pour spécifier un déploiement par nom de déploiement.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // Créez le proxy REST.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $options = new GetDeploymentOptions();
        $options->setSlot(DeploymentSlot::STAGING);
        
        $result = $serviceManagementRestProxy->deleteDeployment("myhostedservice", $options);
    }
    catch(ServiceException $e){
        // Gérez une exception en fonction des codes et des messages d'erreur.
        // Les codes et messages d'erreur se trouvent ici : 
        // http://msdn.microsoft.com/en-us/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Création d’un service de stockage
---------------------------------

Un [service de stockage](../storage-whatis-account/) vous donne accès aux [objets blob](../storage-php-how-to-use-blobs/), [tables](../storage-php-how-to-use-table-storage/) et [files d'attente](../storage-php-how-to-use-queues/) Azure. Pour créer un service de stockage, vous avez besoin d'un nom pour le service (comprenant entre 3 et 24 lettres minuscules et unique au sein d'Azure), d'une étiquette (nom de service automatiquement codé en base64 comprenant jusqu'à 100 caractères) et d'un emplacement ou d'un groupe d'affinités. Il n'est pas obligatoire de fournir une description. L'emplacement, le groupe d'affinités et la description se définissent dans un objet [CreateServiceOptions](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/CreateServiceOptions.php), qui est transmis à la méthode **createStorageService**. L'exemple suivant indique comment créer un service de stockage en spécifiant un emplacement. Si vous voulez utiliser un groupe d'affinités, vous devez au préalable créer un groupe d'affinités (consultez la rubrique [Création d'un groupe d'affinités](#CreateAffinityGroup)) et le configurer à l'aide de la méthode **CreateServiceOptions-\>setAffinityGroup**.

    require_once 'vendor\autoload.php';
     
    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
    use WindowsAzure\Common\ServiceException;
     
     
    try{
        // Créez le proxy REST.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "mystorageaccount";
        $label = base64_encode($name);
        $options = new CreateServiceOptions();
        $options->setLocation('West US');
        $options->setDescription("My storage account description.");

        $result = $serviceManagementRestProxy->createStorageService($name, $label, $options);

        $status = $serviceManagementRestProxy->getOperationStatus($result);
        echo "Operation status: ".$status->getStatus()."<br />";
    }
    catch(ServiceException $e){
        // Gérez une exception en fonction des codes et des messages d'erreur.
        // Les codes et messages d'erreur se trouvent ici : 
        // http://msdn.microsoft.com/en-us/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Dans l'exemple ci-dessus, notez que l'état de l'opération **createStorageService** peut être extrait en transmettant le résultat renvoyé par **createStorageService** à la méthode **getOperationStatus**.

Vous pouvez afficher la liste de vos comptes de stockage et leurs propriétés avec la méthode **listStorageServices** :

    // Créez le proxy REST.
    $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
    $getStorageServicesResult = $serviceManagementRestProxy->listStorageServices();
    $storageServices = $getStorageServicesResult->getStorageServices();

    foreach($storageServices as $storageService){
        echo "Service name: ".$storageService->getName()."<br />";
        echo "Service URL: ".$storageService->getUrl()."<br />";
        echo "Affinity group: ".$storageService->getAffinityGroup()."<br />";
        echo "Location: ".$storageService->getLocation()."<br />";
        echo "------<br />";
    }

Suppression d'un service de stockage
------------------------------------

Vous pouvez supprimer un service de stockage en transmettant son nom à la méthode **deleteStorageService**. la suppression d'un service de stockage supprime toutes les données qui y sont stockées (objets blob, tables et files d'attente).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // Créez le proxy REST.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $serviceManagementRestProxy->deleteStorageService("mystorageservice");
    }
    catch(ServiceException $e){
        // Gérez une exception en fonction des codes et des messages d'erreur.
        // Les codes et messages d'erreur se trouvent ici : 
        // http://msdn.microsoft.com/en-us/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Création d'un groupe d'affinités
--------------------------------

Un groupe d'affinités est un groupement logique de services Azure indiquant à Azure de localiser les services en vue de performances optimisées. Par exemple, vous pouvez créer un groupe d'affinités à l'emplacement « West US » (Ouest des États-Unis), puis créer un [service cloud](#CreateCloudService) dans ce groupe d'affinités. Si vous créez ensuite un service de stockage dans le même groupe d'affinités, Azure sait qu'il doit le placer dans l'emplacement « West US » et l'optimiser au sein du centre de données pour des performances optimales avec les services cloud dans le même groupe d'affinités.

Pour créer un groupe d'affinités, vous avez besoin d'un nom, d'une étiquette (automatiquement codée en base64) et d'un emplacement. Vous avez également la possibilité de fournir une description :

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateAffinityGroupOptions;
    use WindowsAzure\Common\ServiceException;
     
    try{
        // Créez le proxy REST.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myAffinityGroup";
        $label = base64_encode($name);
        $location = "West US";

        $options = new CreateAffinityGroupOptions();
        $options->setDescription = "My affinity group description.";
        
        $serviceManagementRestProxy->createAffinityGroup($name, $label, $location, $options);
    }
    catch(ServiceException $e){
        // Gérez une exception en fonction des codes et des messages d'erreur.
        // Les codes et messages d'erreur se trouvent ici : 
        // http://msdn.microsoft.com/en-us/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Une fois que vous avez créé un groupe d'affinités, vous pouvez spécifier le groupe (au lieu d'un emplacement) lors de la [création d'un service de stockage](#CreateStorageService).

Vous pouvez afficher la liste des groupes d'affinités et inspecter leurs propriétés en appelant la méthode **listAffinityGroups**, puis les méthodes appropriées sur la classe [AffinityGroup](https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/AffinityGroup.php) :

    $result = $serviceManagementRestProxy->listAffinityGroups();

    $groups = $result->getAffinityGroups();

    foreach($groups as $group){
        echo "Name: ".$group->getName()."<br />";
        echo "Description: ".$group->getDescription()."<br />";
        echo "Location: ".$group->getLocation()."<br />";
        echo "------<br />";
    }

Suppression d'un groupe d'affinités
-----------------------------------

Vous pouvez supprimer un groupe d'affinités en transmettant son nom à la méthode **deleteAffinityGroup**. Notez qu'avant de pouvoir supprimer un groupe d'affinités, ce dernier doit être dissocié de tout service (ou les services utilisant le groupe d'affinités doivent être supprimés).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // Créez le proxy REST.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        // Un groupe d'affinités doit être dissocié de tous les services 
        // avant de pouvoir être supprimé.
        $serviceManagementRestProxy->deleteAffinityGroup("myAffinityGroup");
    }
    catch(ServiceException $e){
        // Gérez une exception en fonction des codes et des messages d'erreur.
        // Les codes et messages d'erreur se trouvent ici : 
        // http://msdn.microsoft.com/en-us/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }
