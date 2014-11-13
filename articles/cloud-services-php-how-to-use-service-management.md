<properties urlDisplayName="Service Management" pageTitle="Utilisation des API de gestion des services Azure (PHP)" metaKeywords="" description="D&eacute;couvrez comment utiliser les API de gestion des services PHP Azure pour g&eacute;rer les services cloud et d'autres applications Azure." metaCanonical="" services="" documentationCenter="PHP" title="Utilisation de la gestion des services &agrave; partir de PHP" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Utilisation de la gestion des services à partir de PHP

Ce guide vous explique comment effectuer des tâches courantes de gestion des services par programme à partir de PHP. La classe [ServiceManagementRestProxy][ServiceManagementRestProxy] du [Kit de développement logiciel (SDK) Azure pour PHP][Kit de développement logiciel (SDK) Azure pour PHP] prend en charge l'accès par programme à une grande partie des fonctionnalités liées à la gestion des services disponibles sur le [portail de gestion][portail de gestion] (telles que **la création, la mise à jour et la suppression de services cloud, les déploiements, les services de stockage et les groupes d'affinités**). Ces fonctionnalités peuvent être utiles pour la création d'applications nécessitant un accès par programme à la gestion des services.

## Sommaire

-   [Présentation de la gestion des services][Présentation de la gestion des services]
-   [Concepts][Concepts]
-   [Création d'une application PHP][Création d'une application PHP]
-   [Obtention des bibliothèques clientes Azure][Obtention des bibliothèques clientes Azure]
-   [Connexion à la gestion des services][Connexion à la gestion des services]
-   [Affichage de la liste des emplacements disponibles][Affichage de la liste des emplacements disponibles]
-   [Création d'un service cloud][Création d'un service cloud]
-   [Suppression d'un service cloud][Suppression d'un service cloud]
-   [Création d'un déploiement][Création d'un déploiement]
-   [Mise à jour d'un déploiement][Mise à jour d'un déploiement]
-   [Transfert des déploiements entre les environnements intermédiaire et de production][Transfert des déploiements entre les environnements intermédiaire et de production]
-   [Suppression d'un déploiement][Suppression d'un déploiement]
-   [Création d’un service de stockage][Création d’un service de stockage]
-   [Suppression d'un service de stockage][Suppression d'un service de stockage]
-   [Création d'un groupe d'affinités][Création d'un groupe d'affinités]
-   [Suppression d'un groupe d'affinités][Suppression d'un groupe d'affinités]

## <span id="WhatIs"></span></a>Présentation de la gestion des services

L'API de gestion des services fournit un accès par programme aux fonctionnalités de gestion des services disponibles par le biais du [portail de gestion][portail de gestion]. Le Kit de développement logiciel (SDK) Azure pour PHP vous permet de gérer vos services cloud, comptes de stockage et groupes d'affinités.

Pour utiliser l'API de gestion des services, vous devez [créer un compte Azure][créer un compte Azure].

## <span id="Concepts"></span></a>Concepts

Le Kit de développement logiciel (SDK) Azure pour PHP inclut l'[API de gestion des services Azure][API de gestion des services Azure], qui est une API REST. Toutes les opérations de l'API sont effectuées au moyen du protocole SSL et sont mutuellement authentifiées au moyen de certificats X.509 v3. La gestion des services est accessible à partir d'un service s'exécutant dans Azure, ou directement sur Internet à partir de toute application pouvant envoyer une demande HTTPS et recevoir une réponse HTTPS.

## <span id="CreateApplication"></span></a>Création d'une application PHP

La référence de classes dans le Kit de développement logiciel (SDK) Azure pour PHP depuis votre code constitue la seule exigence pour créer une application PHP qui utilise la gestion des services Azure. Vous pouvez utiliser tous les outils de développement pour créer votre application, y compris Bloc-notes.

Dans ce guide, vous allez utiliser des fonctionnalités de service qui peuvent être appelées dans une application PHP localement, ou dans du code qui s'exécute dans un rôle web, un rôle de travail ou un site web Azure.

## <span id="GetClientLibraries"></span></a>Obtention des bibliothèques clientes Azure

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

## <span id="Connect"></span></a> Connexion à la gestion des services

Pour vous connecter au point de terminaison de la gestion de services, vous avez besoin de votre ID d'abonnement Azure et du chemin d'accès vers un certificat de gestion valide. Vous pouvez obtenir votre ID d'abonnement par le biais du [portail de gestion][portail de gestion] et créer des certificats de gestion de différentes manières. Dans ce guide, nous avons utilisé [OpenSSL][OpenSSL], que vous pouvez [télécharger pour Windows][télécharger pour Windows] et exécuter sur une console.

En fait, vous devez créer deux certificats, un pour le serveur (un fichier `.cer`) et un pour le client (un fichier `.pem`). Pour créer le fichier `.pem`, exécutez le code suivant :

    `openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Pour créer le certificat `.cer`, exécutez le code suivant :

    `openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Pour plus d'informations sur les certificats Azure, consultez la rubrique [Vue d'ensemble des certificats dans Azure][Vue d'ensemble des certificats dans Azure]. Pour une description complète des paramètres OpenSSL, consultez la documentation disponible sur <http://www.openssl.org/docs/apps/openssl.html>.

Si vous avez téléchargé et importé le fichier de paramètres de publication au moyen des [outils en ligne de commande Azure][outils en ligne de commande Azure], vous pouvez utiliser le fichier `.pem` créé par les outils au lieu de créer le vôtre. Les outils créent un fichier `.cer` pour vous, le téléchargent sur Azure et placent le fichier `.pem` correspondant dans le répertoire `.azure` de votre ordinateur (dans votre répertoire utilisateur).

Une fois que vous avez créé ces fichiers, vous devez télécharger le fichier `.cer` sur Azure par le biais du [portail de gestion][portail de gestion]. Prenez note également de l'endroit où vous avez enregistré le fichier `.pem`.

Une fois que vous avez obtenu votre ID d'abonnement, créé un certificat et téléchargé le fichier `.cer` sur Azure, vous pouvez vous connecter au point de terminaison de gestion Azure en créant une chaîne de connexion et en la transmettant à la méthode **createServiceManagementService** sur la classe **ServicesBuilder** :

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $conn_string = "SubscriptionID=<your_subscription_id>;CertificatePath=<path_to_.pem_certificate>";

    $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

Dans l'exemple ci-dessus, `$serviceManagementRestProxy` est un objet [ServiceManagementRestProxy][ServiceManagementRestProxy]. La classe **ServiceManagementRestProxy** est la classe principale utilisée pour gérer les services Azure.

## <span id="ListAvailableLocations"></span></a> Affichage de la liste des emplacements disponibles

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
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460801
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

> [WACOM.NOTE]
> Dans les exemples de code suivants, les emplacements sont transmis aux méthodes sous forme de chaînes. Cependant, vous avez également la possibilité de les transmettre sous forme d'énumérations au moyen de la classe `WindowsAzure\ServiceManagement\Models\Locations`. Par exemple, au lieu de transmettre « West US » (Ouest des États-Unis) à une méthode acceptant un emplacement, vous pouvez transmettre `Locations::WEST_US`.

## <span id="CreateCloudService"></span></a> Création d'un service cloud

Lorsque vous créez une application et que vous l'exécutez dans Azure, l'ensemble constitué du code et de la configuration est appelé [service cloud Azure][service cloud Azure] (également connu sous le nom de *service hébergé* dans les versions antérieures d'Azure). La méthode **createHostedServices** vous permet de créer un service hébergé en fournissant un nom de service hébergé (qui doit être unique dans Azure), une étiquette (le nom du service hébergé automatiquement codé en base64) et un objet **CreateServiceOptions**. L'objet [CreateServiceOptions][CreateServiceOptions] vous permet de configurer un emplacement *ou* le groupe d'affinités pour votre service.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myhostedservice";
        $label = base64_encode($name);
        $options = new CreateServiceOptions();
        $options->setLocation('West US');
        // Instead of setLocation, you can use setAffinityGroup
        // to set an affinity group.

        $result = $serviceManagementRestProxy->createHostedService($name, $label, $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Vous pouvez afficher la liste de tous les services hébergés pour votre abonnement au moyen de la méthode **listHostedServices**, qui renvoie un objet [ListHostedServicesResult][ListHostedServicesResult]. En appelant la méthode **getHostedServices**, vous pouvez ensuite créer une boucle sur un tableau d'objets [HostedServices] et extraire les propriétés des services :

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

Une fois que vous avez créé un service cloud, vous pouvez déployer votre code sur le service avec la méthode [createDeployment][Création d'un déploiement].

## <span id="DeleteCloudService"></span></a> Suppression d'un service cloud

Vous pouvez supprimer un service cloud en transmettant son nom à la méthode **deleteHostedService** :

    $serviceManagementRestProxy->deleteHostedService("myhostedservice");

Notez qu'avant de supprimer un service, vous devez supprimer tous les déploiements associés Pour plus d'informations, consultez la rubrique [Suppression d'un déploiement][Suppression d'un déploiement].

## <span id="CreateDeployment"></span></a> Création d'un déploiement

La méthode **createDeployment** télécharge un nouveau [package de service][package de service] et crée un déploiement dans l'environnement intermédiaire ou de production. Les paramètres de cette méthode sont les suivants :

-   **$name** : nom du service hébergé.
-   **$deploymentName** : nom du déploiement.
-   **$slot** : énumération indiquant l'emplacement intermédiaire ou de production.
-   **$packageUrl** : URL du package de déploiement (fichier .cspgk). Le fichier de package doit être stocké dans un compte de stockage d'objets blob Azure sous le même abonnement que le service hébergé sur lequel le package est téléchargé. Vous pouvez créer un package de déploiement au moyen des [cmdlets Azure PowerShell][cmdlets Azure PowerShell] ou de l'[outil en ligne de commande cspack][outil en ligne de commande cspack].
-   **$configuration** : fichier de configuration de service (fichier .cscfg).
-   **$label** : nom du service hébergé codé en base64.

L'exemple suivant crée un déploiement dans l'emplacement de production d'un service hébergé nommé `myhostedservice` :

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
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
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Dans l'exemple ci-dessus, notez que l'état de l'opération **createDeployment** peut être extrait en transmettant le résultat renvoyé par **createDeployment** à la méthode **getOperationStatus**.

Vous pouvez accéder aux propriétés de déploiement à l'aide de la méthode **getDeployment**. L'exemple suivant permet d'extraire un déploiement en spécifiant son emplacement dans l'objet [GetDeploymentOptions][ListHostedServicesResult], mais vous pourriez tout aussi bien spécifier le nom du déploiement. Il effectue également une itération dans toutes les instances du déploiement :

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

## <span id="UpdateDeployment"></span></a> Mise à jour d'un déploiement

Vous pouvez mettre un déploiement à jour à l'aide de la méthode **changeDeploymentConfiguration** ou **updateDeploymentStatus**.

La méthode **changeDeploymentConfiguration** vous permet de télécharger un nouveau fichier de configuration de service (`.cscfg`), qui changera n'importe quel paramètre de service (y compris le nombre d'instances d'un déploiement). Pour plus d'informations, consultez la rubrique [Schéma de configuration de service Azure (.cscfg)][Schéma de configuration de service Azure (.cscfg)]. L'exemple suivant indique comment télécharger un nouveau fichier de configuration de service :

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\ChangeDeploymentConfigurationOptions;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
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
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460801
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
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $options = new GetDeploymentOptions();
        $options->setSlot(DeploymentSlot::PRODUCTION);
        
        $result = $serviceManagementRestProxy->updateDeploymentStatus("myhostedservice", DeploymentStatus::RUNNING, $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="MoveDeployments"></span></a> Transfert des déploiements entre les environnements intermédiaire et de production

Azure fournit deux environnements de déploiement : intermédiaire et de production. Généralement, un service est déployé dans l'environnement intermédiaire pour être testé avant son déploiement dans l'environnement de production. Lorsqu'il est temps de promouvoir le service de l'environnement intermédiaire à l'environnement de production, vous pouvez le faire sans redéployer le service. Pour cela, intervertissez les déploiements Pour plus d'informations sur l'intervertissement de déploiements, consultez la rubrique [Vue d'ensemble de la gestion des déploiements dans Azure][Vue d'ensemble de la gestion des déploiements dans Azure].

L'exemple suivant indique comment utiliser la méthode **swapDeployment** pour intervertir deux déploiements (avec les noms `v1` et `v2`). Dans l'exemple, avant l'appel de **swapDeployment**, le déploiement `v1` se trouve dans l'emplacement de production, et le déploiement `v2` dans l'emplacement intermédiaire. Après avoir appelé **swapDeployment**, `v2` se trouve dans l'emplacement de production, et `v1` dans l'emplacement intermédiaire.

    require_once 'vendor\autoload.php'; 

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $result = $serviceManagementRestProxy->swapDeployment("myhostedservice", "v2", "v1");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="DeleteDeployment"></span></a> Suppression d'un déploiement

Pour supprimer un déploiement, utilisez la méthode **deleteDeployment**. L'exemple suivant indique comment supprimer un déploiement dans l'environnement intermédiaire à l'aide de la méthode **setSlot** sur un objet [GetDeploymentOptions][ListHostedServicesResult], puis en le transmettant à **deleteDeployment**. Au lieu de spécifier un déploiement par emplacement, vous avez la possibilité d'utiliser la méthode **setName** sur la classe [GetDeploymentOptions) pour spécifier un déploiement par nom de déploiement.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $options = new GetDeploymentOptions();
        $options->setSlot(DeploymentSlot::STAGING);
        
        $result = $serviceManagementRestProxy->deleteDeployment("myhostedservice", $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="CreateStorageService"></span></a> Création d'un service de stockage

Un [service de stockage][GetDeploymentOptions) pour spécifier un déploiement par nom de déploiement.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $options = new GetDeploymentOptions();
        $options->setSlot(DeploymentSlot::STAGING);
        
        $result = $serviceManagementRestProxy->deleteDeployment("myhostedservice", $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="CreateStorageService"></span></a> Création d'un service de stockage

Un [service de stockage] vous donne accès aux [objets blob][objets blob], [tables][tables] et [files d'attente][files d'attente] Azure. Pour créer un service de stockage, vous avez besoin d'un nom pour le service (comprenant entre 3 et 24 lettres minuscules et unique au sein d'Azure), d'une étiquette (nom de service automatiquement codé en base64 comprenant jusqu'à 100 caractères) et d'un emplacement ou d'un groupe d'affinités. Il n'est pas obligatoire de fournir une description. L'emplacement, le groupe d'affinités et la description se définissent dans un objet [CreateServiceOptions][CreateServiceOptions], qui est transmis à la méthode **createStorageService**. L'exemple suivant indique comment créer un service de stockage en spécifiant un emplacement. Si vous voulez utiliser un groupe d'affinités, vous devez au préalable créer un groupe d'affinités (consultez la rubrique [Création d'un groupe d'affinités][Création d'un groupe d'affinités]) et le configurer à l'aide de la méthode **CreateServiceOptions-\>setAffinityGroup**.

    require_once 'vendor\autoload.php';
     
    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
    use WindowsAzure\Common\ServiceException;
     
     
    try{
        // Create REST proxy.
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
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Dans l'exemple ci-dessus, notez que l'état de l'opération **createStorageService** peut être extrait en transmettant le résultat renvoyé par **createStorageService** à la méthode **getOperationStatus**.

Vous pouvez afficher la liste de vos comptes de stockage et leurs propriétés avec la méthode **listStorageServices** :

    // Create REST proxy.
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

## <span id="DeleteStorageService"></span></a> Suppression d'un service de stockage

Vous pouvez supprimer un service de stockage en transmettant son nom à la méthode **deleteStorageService**. la suppression d'un service de stockage supprime toutes les données qui y sont stockées (objets blob, tables et files d'attente).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $serviceManagementRestProxy->deleteStorageService("mystorageservice");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="CreateAffinityGroup"></span></a> Création d'un groupe d'affinités

Un groupe d'affinités est un groupement logique de services Azure indiquant à Azure de localiser les services en vue de performances optimisées. Par exemple, vous pouvez créer un groupe d'affinités à l'emplacement « West US » (Ouest des États-Unis), puis créer un [service cloud][Création d'un service cloud] dans ce groupe d'affinités. Si vous créez ensuite un service de stockage dans le même groupe d'affinités, Azure sait qu'il doit le placer dans l'emplacement « West US » et l'optimiser au sein du centre de données pour des performances optimales avec les services cloud dans le même groupe d'affinités.

Pour créer un groupe d'affinités, vous avez besoin d'un nom, d'une étiquette (automatiquement codée en base64) et d'un emplacement. Vous avez également la possibilité de fournir une description :

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateAffinityGroupOptions;
    use WindowsAzure\Common\ServiceException;
     
    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myAffinityGroup";
        $label = base64_encode($name);
        $location = "West US";

        $options = new CreateAffinityGroupOptions();
        $options->setDescription = "My affinity group description.";
        
        $serviceManagementRestProxy->createAffinityGroup($name, $label, $location, $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Une fois que vous avez créé un groupe d'affinités, vous pouvez spécifier le groupe (au lieu d'un emplacement) lors de la [création d'un service de stockage][Création d’un service de stockage].

Vous pouvez afficher la liste des groupes d'affinités et inspecter leurs propriétés en appelant la méthode **listAffinityGroups**, puis les méthodes appropriées sur la classe [AffinityGroup][AffinityGroup] :

    $result = $serviceManagementRestProxy->listAffinityGroups();

    $groups = $result->getAffinityGroups();

    foreach($groups as $group){
        echo "Name: ".$group->getName()."<br />";
        echo "Description: ".$group->getDescription()."<br />";
        echo "Location: ".$group->getLocation()."<br />";
        echo "------<br />";
    }

## <span id="DeleteAffinityGroup"></span></a> Suppression d'un groupe d'affinités

Vous pouvez supprimer un groupe d'affinités en transmettant son nom à la méthode **deleteAffinityGroup**. Notez qu'avant de pouvoir supprimer un groupe d'affinités, ce dernier doit être dissocié de tout service (ou les services utilisant le groupe d'affinités doivent être supprimés).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        // An affinity group must be disassociated from all services 
        // before it can be deleted.
        $serviceManagementRestProxy->deleteAffinityGroup("myAffinityGroup");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

  [ServiceManagementRestProxy]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/ServiceManagementRestProxy.php
  [Kit de développement logiciel (SDK) Azure pour PHP]: ../php-download-sdk/
  [portail de gestion]: https://manage.windowsazure.com/
  [Présentation de la gestion des services]: #WhatIs
  [Concepts]: #Concepts
  [Création d'une application PHP]: #CreateApplication
  [Obtention des bibliothèques clientes Azure]: #GetClientLibraries
  [Connexion à la gestion des services]: #Connect
  [Affichage de la liste des emplacements disponibles]: #ListAvailableLocations
  [Création d'un service cloud]: #CreateCloudService
  [Suppression d'un service cloud]: #DeleteCloudService
  [Création d'un déploiement]: #CreateDeployment
  [Mise à jour d'un déploiement]: #UpdateDeployment
  [Transfert des déploiements entre les environnements intermédiaire et de production]: #MoveDeployments
  [Suppression d'un déploiement]: #DeleteDeployment
  [Création d’un service de stockage]: #CreateStorageService
  [Suppression d'un service de stockage]: #DeleteStorageService
  [Création d'un groupe d'affinités]: #CreateAffinityGroup
  [Suppression d'un groupe d'affinités]: #DeleteAffinityGroup
  [créer un compte Azure]: /fr-fr/pricing/free-trial/
  [API de gestion des services Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460799.aspx
  [OpenSSL]: http://www.openssl.org/
  [télécharger pour Windows]: http://www.openssl.org/related/binaries.html
  [Vue d'ensemble des certificats dans Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg981935.aspx
  [outils en ligne de commande Azure]: ../command-line-tools/
  [service cloud Azure]: ../cloud-services-what-is/
  [CreateServiceOptions]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/CreateServiceOptions.php
  [ListHostedServicesResult]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php
  [package de service]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433093
  [cmdlets Azure PowerShell]: ../install-configure-powershell/
  [outil en ligne de commande cspack]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg432988.aspx
  [Schéma de configuration de service Azure (.cscfg)]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee758710.aspx
  [Vue d'ensemble de la gestion des déploiements dans Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh386336.aspx
  [service de stockage]: ../storage-whatis-account/
  [objets blob]: ../storage-php-how-to-use-blobs/
  [tables]: ../storage-php-how-to-use-table-storage/
  [files d'attente]: ../storage-php-how-to-use-queues/
  [AffinityGroup]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/AffinityGroup.php
