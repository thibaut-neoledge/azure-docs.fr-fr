<properties linkid="manage-linux-howto-service-management-api" urlDisplayName="Service Management API" pageTitle="How to use the service management API for VMs - Azure" metaKeywords="" description="Learn how to use the Azure Service Management API for a Linux virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Use the Service Management API" authors="" solutions="" manager="" editor="" />

Utilisation de l'API de gestion des services
============================================

Initialisation
--------------

Pour appeler l'API de gestion des services IaaS Azure à partir de NodeJS, le module `azure` est utilisé.

    var azure = require('azure');

Créez tout d'abord une instance de ServiceManagementService. Tous les appels à l'API utilisent cet objet. L'ID d'abonnement, les informations d'identification et d'autres options de connexion sont définis à ce moment. Pour gérer plusieurs abonnements, créez plusieurs objets ServiceManagementService.

    var iaasClient = azure.createServiceManagementService(subscriptionid, auth, options);

-   Subscriptionid est une chaîne obligatoire. Il doit s'agir de l'ID d'abonnement du compte auquel vous accédez.
-   Auth est un objet facultatif spécifiant la clé privée et le certificat public à utiliser avec ce compte.
    -   keyfile : chemin d'accès au fichier .pem qui a une clé privée. Ignoré si keyvalue est spécifiée.
    -   keyvalue : valeur réelle d'une clé privée telle qu'elle est stockée dans un fichier .pem.
    -   certfile : chemin d'accès au fichier .pem qui a un certificat public. Ignoré si certvalue est spécifiée.
    -   certvalue : valeur réelle d'un certificat public comme elle est stockée dans un fichier .pem.
    -   Si les valeurs ci-dessus ne sont pas spécifiées, les valeurs des variables d'environnement du processus `CLIENT_AUTH_KEYFILE` et `CLIENT_AUTH_CERTFILE` sont lues et utilisées. Si elles ne sont pas définies, les valeurs par défaut des fichiers sont tentées : priv.pem et pub.pem.
    -   S'il n'est pas possible de charger la clé privée et le certificat public, une erreur est générée.
-   Options est un objet facultatif pouvant être utilisé pour contrôler les propriétés utilisées par le client.
    -   host : nom d'hôte du serveur de gestion Azure. S'il n'est pas spécifié, l'hôte par défaut est utilisé.
    -   apiversion : chaîne de version à utiliser dans l'en-tête HTTP. Si elle n'est pas spécifiée, la version par défaut est utilisée.
    -   serializetype : peut être XML ou JSON. S'il n'est pas spécifié, la sérialisation par défaut est utilisée.

Un proxy de tunnel peut également être utilisé pour permettre à la requête HTTPS de passer par un proxy. Une fois IaasClient créé, la variable d'environnement `HTTPS_PROXY` est traitée. Si elle est définie sur une URL valide, le nom d'hôte et le port sont analysés à partir de l'URL et utilisés lors des requêtes suivantes afin d'identifier le proxy.

     iaasClient.SetProxyUrl(proxyurl)

SetProxyUrl peut être appelé pour définir explicitement l'hôte et le port du proxy. Il a le même effet que lorsque la variable d'environnement `HTTPS_PROXY` est définie et il remplace le paramètre d'environnement.

Rappels
-------

Toutes les API ont un argument de rappel requis. L'achèvement de la requête est signalé en appelant la fonction transmise dans le rappel.

    callback(rsp)

-   Le rappel a uniquement un paramètre : l'objet response.
-   isSuccessful : true ou false
-   statusCode : code d'état HTTP de la réponse
-   response : réponse analysée dans un objet javascript. Elle est définie si isSuccessful a la valeur true.
-   error : objet javascript contenant les informations sur l'erreur, défini si isSuccessful a la valeur false.
-   body : corps réel de la réponse en tant que chaîne
-   headers : les en-têtes HTTP réels de la réponse
-   reqopts : options de requête HTTP Node utilisées pour effectuer la requête.

Remarquez que, dans certains cas, l'achèvement peut uniquement indiquer que la requête a été acceptée. Dans ce cas, utilisez **GetOperationStatus** pour obtenir l'état final.

API
---

**iaasClient.GetOperationStatus(requested, callback)**

-   De nombreux appels de gestion reviennent avant la fin de l'opération. Ils renvoient une valeur 202 Accepté et placent un requested dans l'en-tête HTTP ms-request-id. Pour effectuer une interrogation afin de connaître l'état d'avancement de la requête, utilisez cette API et transmettez la valeur demandée.
-   callback est requis.

**iaasClient.GetOSImage(imagename, callback)**

-   imagename est un nom de chaîne requis de l'image.
-   callback est requis.
-   L'objet response contient les propriétés de l'image nommée en cas de succès.

**iaasClient.ListOSImages(callback)**

-   callback est requis.
-   L'objet response contient un tableau d'objets image en cas de succès.

**iaasClient.CreateOSImage(imageName, mediaLink, imageOptions, callback)**

-   imageName est un nom de chaîne requis de l'image.
-   mediaLink est un nom de chaîne requis du mediaLink à utiliser.
-   imageOptions est un objet facultatif. Il peut contenir les propriétés suivantes :
    -   Catégorie
    -   Label : imageName par défaut si non défini.
    -   Emplacement
    -   RoleSize
-   callback est requis. (Si imageOptions n'est pas défini, il peut être le troisième paramètre.)
-   L'objet response contient les propriétés de l'image créée en cas de succès.

**iaasClient.ListHostedServices(callback)**

-   callback est requis.
-   L'objet response contient un tableau d'objets de service hébergés en cas de succès.

**iaasClient.GetHostedService(serviceName, callback)**

-   serviceName est un nom de chaîne requis du service hébergé.
-   callback est requis.
-   L'objet response contient les propriétés du service hébergé en cas de succès.

**iaasClient.CreateHostedService(serviceName, serviceOptions, callback)**

-   serviceName est un nom de chaîne requis du service hébergé.
-   serviceOptions est un objet facultatif. Il peut contenir les propriétés suivantes :
    -   Description : « Service host » par défaut
    -   Label : serviceName par défaut si non défini.
    -   Location : « Azure Preview » par défaut, TODO à modifier lors de la publication.
-   callback est requis.

**iaasClient.GetStorageAccountKeys(serviceName, callback)**

-   serviceName est un nom de chaîne requis du service hébergé.
-   callback est requis.
-   L'objet response contient les clés d'accès de stockage en cas de succès.

**iaasClient.GetDeployment(serviceName, deploymentName, callback)**

-   serviceName est un nom de chaîne requis du service hébergé.
-   deploymentName est un nom de chaîne requis du déploiement.
-   callback est requis.
-   L'objet response contient les propriétés du déploiement nommé en cas de succès.

**iaasClient.GetDeploymentBySlot(serviceName, deploymentSlot, callback)**

-   serviceName est un nom de chaîne requis du service hébergé.
-   deploymentSlot est un nom de chaîne requis de l'emplacement (Staged ou Production).
-   callback est requis.
-   L'objet response contient les propriétés des déploiements dans l'emplacement en cas de succès.

**iaasClient.CreateDeployment(serviceName, deploymentName, VMRole, deployOptions, callback)**

-   serviceName est un nom de chaîne requis du service hébergé.
-   deploymentName est un nom de chaîne requis du déploiement.
-   VMRole est un objet requis ayant les propriétés du Rôle à créer pour le déploiement.
-   deployOptions est un objet facultatif. Il peut contenir les propriétés suivantes :
    -   DeploymentSlot : « Production » par défaut
    -   Label : deploymentName par défaut si non défini.
    -   UpgradeDomainCount : pas de valeur par défaut
-   callback est requis.

**iaasClient.GetRole(serviceName, deploymentName, roleName, callback)**

-   serviceName est un nom de chaîne requis du service hébergé.
-   deploymentName est un nom de chaîne requis du déploiement.
-   roleName est un nom de chaîne requis du rôle.
-   callback est requis.
-   L'objet response contient les propriétés du rôle nommé en cas de succès.

**iaasClient.AddRole(serviceName, deploymentName, VMRole, callback)**

-   serviceName est un nom de chaîne requis du service hébergé.
-   deploymentName est un nom de chaîne requis du déploiement.
-   VMRole est un objet requis ayant les propriétés du Rôle à ajouter au déploiement.
-   callback est requis.

**iaasClient.ModifyRole(serviceName, deploymentName, roleName, VMRole, callback)**

-   serviceName est un nom de chaîne requis du service hébergé.
-   deploymentName est un nom de chaîne requis du déploiement.
-   roleName est un nom de chaîne requis du rôle.
-   VMRole est un objet requis ayant les propriétés à modifier dans le rôle.
-   callback est requis.

**iaasClient.DeleteRole(serviceName, deploymentName, roleName, callback)**

-   serviceName est un nom de chaîne requis du service hébergé.
-   deploymentName est un nom de chaîne requis du déploiement.
-   roleName est un nom de chaîne requis du rôle.
-   callback est requis.

**iaasClient.AddDataDisk(serviceName, deploymentName, roleName, datadisk, callback)**

-   serviceName est un nom de chaîne requis du service hébergé.
-   deploymentName est un nom de chaîne requis du déploiement.
-   roleName est un nom de chaîne requis du rôle.
-   Datadisk est un objet requis utilisé pour spécifier la manière dont le disque de données est créé.
-   callback est requis.

**iaasClient.ModifyDataDisk(serviceName, deploymentName, roleName, LUN, datadisk, callback)**

-   serviceName est un nom de chaîne requis du service hébergé.
-   deploymentName est un nom de chaîne requis du déploiement.
-   roleName est un nom de chaîne requis du rôle.
-   LUN est le numéro qui identifie le disque de données.
-   Datadisk est un objet requis utilisé pour spécifier la manière dont le disque de données est modifié.
-   callback est requis.

**iaasClient.RemoveDataDisk(serviceName, deploymentName, roleName, LUN, callback)**

-   serviceName est un nom de chaîne requis du service hébergé.
-   deploymentName est un nom de chaîne requis du déploiement.
-   roleName est un nom de chaîne requis du rôle.
-   LUN est le numéro qui identifie le disque de données.
-   callback est requis.

**iaasClient.ShutdownRoleInstance(serviceName, deploymentName, roleInstance, callback)**

-   serviceName est un nom de chaîne requis du service hébergé.
-   deploymentName est un nom de chaîne requis du déploiement.
-   roleInstance est un nom de chaîne requis de l'instance de rôle.
-   callback est requis.

**iaasClient.RestartRoleInstance(serviceName, deploymentName, roleInstance, callback)**

-   serviceName est un nom de chaîne requis du service hébergé.
-   deploymentName est un nom de chaîne requis du déploiement.
-   roleInstance est un nom de chaîne requis de l'instance de rôle.
-   callback est requis.

**iaasClient.CaptureRoleInstance(serviceName, deploymentName, roleInstance, captOptions, callback)**

-   serviceName est un nom de chaîne requis du service hébergé.
-   deploymentName est un nom de chaîne requis du déploiement.
-   roleInstance est un nom de chaîne requis de l'instance de rôle.
-   captOptions est un objet requis qui définit les actions de capture :
    -   PostCaptureActions
    -   ProvisioningConfiguration
    -   SupportsStatelessDeployment
    -   TargetImageLabel
    -   TargetImageName
-   callback est requis.

Objets de données
-----------------

Les API considèrent les objets comme des entrées lors de la création ou de la modification d'un déploiement, d'un rôle ou d'un disque. D'autres API renvoient des objets similaires à une opération Get ou List. Cette section décrit les propriétés de l'objet. Deployment

-   Name : chaîne
-   DeploymentSlot : « Staging » ou « Production »
-   Status : chaîne, sortie seulement
-   PrivateID : chaîne, sortie seulement
-   Label : chaîne
-   UpgradeDomainCount : nombre
-   SdkVersion : chaîne
-   Locked : true ou false
-   RollbackAllowed : true ou false
-   RoleInstance : objet, sortie seulement
-   Role : objet VMRole
-   InputEndpointList : tableau d'InputEndpoint

**VMRole**

-   RoleName : chaîne. Requis pour la création.
-   RoleSize : chaîne. Facultatif pour la création.
-   RoleType : chaîne. « PersistentVMRole » par défaut si non spécifié lors de la création.
-   OSDisk : objet. Requis pour la création.
-   DataDisks : tableau d'objets. Facultatif pour la création.
-   ConfigurationSets : tableau d'objets Configuration.

**RoleInstance**

-   RoleName : chaîne
-   InstanceName : chaîne
-   InstanceStatus : chaîne
-   InstanceUpgradeDomain : nombre
-   InstanceFaultDomain : nombre
-   InstanceSize : chaîne
-   IpAddress : chaîne

**OSDisk**

-   SourceImageName : chaîne. Requis pour la création.
-   DisableWriteCache : true ou false
-   DiskName : chaîne
-   MediaLink : chaîne

**DataDisk**

-   DisableReadCache : true ou false
-   EnableWriteCache : true ou false
-   DiskName : chaîne
-   MediaLink : chaîne
-   LUN : numéro (0-15)
-   LogicalDiskSizeInGB : nombre
-   SourceMediaLink : chaîne
-   Il y a 3 manières de spécifier le disque pendant la création : par nom, par média ou par taille. Les options spécifiées déterminent la façon dont il fonctionne. Pour plus d'informations, consultez le document API RDFE.

**ProvisioningConfiguration**

-   ConfigurationSetType : « ProvisioningConfiguration »
-   AdminPassword : chaîne
-   MachineName : chaîne
-   ResetPasswordOnFirstLogon : true ou false

**NetworkConfiguration**

-   ConfigurationSetType : « NetworkConfiguration »
-   InputEndpoints : tableau d'ExternalEndpoints

**InputEndpoint**

-   RoleName
-   Vip
-   Port

**ExternalEndpoint**

-   LocalPort
-   Nom
-   Port
-   Protocole

Exemple de code
---------------

Voici un exemple de code javascript qui crée un service hébergé et un déploiement, puis qui interroge pour connaître l'état d'avancement du déploiement.

    var azure = require('azure');

    // spécifiez où sont situés les fichiers de certificat
    var auth = {
      keyfile : '../certs/priv.pem',
      certfile : '../certs/pub.pem'
    }

    // noms et ID pour l'abonnement, le service et le déploiement
    var subscriptionId = '167a0c69-cb6f-4522-ba3e-d3bdc9c504e1';
    var serviceName = 'sampleService2';
    var deploymentName = 'sampleDeployment';

    // interrogation pour connaître l'état de l'avancement toutes les 10 secondes
    var pollPeriod = 10000;

    // données utilisées pour définir le déploiement et le rôle

    var deploymentOptions = {
      DeploymentSlot: 'Staging',
      Label: 'Deployment Label'
    }

    var osDisk = {
      SourceImageName : 'Win2K8SP1.110809-2000.201108-01.en.us.30GB.vhd',
    };

    var dataDisk1 = {
      LogicalDiskSizeInGB : 10,
      LUN : '0'
    };

    var provisioningConfigurationSet = {
      ConfigurationSetType: 'ProvisioningConfiguration',
      AdminPassword: 'myAdminPwd1',
      MachineName: 'sampleMach1',
      ResetPasswordOnFirstLogon: false
    };

    var externalEndpoint1 = {
      Name: 'endpname1',
      Protocol: 'tcp',
      Port: '59919',
      LocalPort: '3395'
    };

    var networkConfigurationSet = {
      ConfigurationSetType: 'NetworkConfiguration',
      InputEndpoints: [externalEndpoint1]
    };

    var VMRole = {
      RoleName: 'sampleRole',
      RoleSize: 'Small',
      OSDisk: osDisk,
      DataDisks: [dataDisk1],
      ConfigurationSets: [provisioningConfigurationSet, networkConfigurationSet]
    }


    // fonction pour afficher les messages d'erreur en cas d'échec
    function showErrorResponse(rsp) {
      console.log('There was an error response from the service');
      console.log('status code=' + rsp.statusCode);
      console.log('Error Code=' + rsp.error.Code);
      console.log('Error Message=' + rsp.error.Message);
    }

    // interrogation pour connaître l'état d'avancement
    function PollComplete(reqid) {
      iaasCli.GetOperationStatus(reqid, function(rspobj) {
        if (rspobj.isSuccessful && rspobj.response) {
          var rsp = rspobj.response;
          if (rsp.Status == 'InProgress') {
            setTimeout(PollComplete(reqid), pollPeriod);
            process.stdout.write('.');
          } else {
            console.log(rsp.Status);
            if (rsp.HttpStatusCode) console.log('HTTP Status: ' + rsp.HttpStatusCode);
            if (rsp.Error) {      
              console.log('Error code: ' + rsp.Error.Code);
              console.log('Error Message: ' + rsp.Error.Message);
            }
          }
        } else {
          showErrorResponse(rspobj);
        }
      });
    }


    // créez l'objet client
    var iaasCli = azure.createServiceManagementService(subscriptionId, auth);

    // créez un service hébergé.
    // en cas de réussite, créez un déploiement
    // en cas d'acceptation, interrogez pour connaître l'état d'avancement
    iaasCli.CreateHostedService(serviceName, function(rspobj) {
      if (rspobj.isSuccessful) {
        iaasCli.CreateDeployment(serviceName, 
                                 deploymentName,
                                 VMRole, deploymentOptions,
                                  function(rspobj) {
          if (rspobj.isSuccessful) {
          // obtenez l'ID de la requête et commencez à interroger pour connaître l'état d'avancement
            var reqid = rspobj.headers['x-ms-request-id'];
            process.stdout.write('Polling');
            setTimeout(PollComplete(reqid), pollPeriod);
          } else {
            showErrorResponse(rspobj);
          }
        });
      } else {
        showErrorResponse(rspobj);
      }
    });
