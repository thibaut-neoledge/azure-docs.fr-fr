---
title: "Création d’un environnement Azure App Service Environment à l’aide d’un modèle Resource Manager"
description: "Explique comment créer un environnement Azure App Service Environment externe ou ILB à l’aide d’un modèle Resource Manager"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 370ab7a32634f04b4776a2a9a84ecaacf11ea617
ms.contentlocale: fr-fr
ms.lasthandoff: 06/26/2017

---
# <a name="how-to-create-an-ase-using-azure-resource-manager-templates"></a>Création d’un environnement ASE à l’aide des modèles Azure Resource Manager

## <a name="overview"></a>Vue d'ensemble
Les environnements ASE (ASE, App Service Environment) peuvent être créés avec un point de terminaison accessible via Internet ou un point de terminaison sur une adresse interne du réseau virtuel Azure (VNet).  S’il est créé avec un point de terminaison interne, ce point de terminaison est fourni par un composant Azure appelé équilibreur de charge interne (ILB, Internal Load Balancer).  L’environnement ASE pourvu d’un point de terminaison public est appelé environnement ASE externe tandis que l’environnement ASE sur une adresse IP interne est appelé environnement ASE ILB.  

Un environnement ASE peut être créé à l’aide du portail Azure ou des modèles Azure Resource Manager.  Cet article décrit les étapes et la syntaxe nécessaires à la création d’un environnement ASE externe ou ILB à l’aide des modèles Azure Resource Manager.  Pour en savoir plus sur la création d’un environnement ASE dans le portail, vous pouvez commencer par [Making an External ASE (Création d’un environnement ASE externe)][MakeExternalASE] ou [Créer et utiliser un équilibreur de charge interne avec un environnement Azure App Service Environment][MakeILBASE].

Lorsque vous créez un environnement ASE dans le portail, vous pouvez choisir de créer votre réseau virtuel en même temps ou de sélectionner un réseau virtuel préexistant pour y effectuer le déploiement.  Si vous procédez à la création à partir d’un modèle, vous devez commencer par : 
* Un réseau virtuel Resource Manager.
* Un sous-réseau de ce réseau virtuel.  Il est recommandé d’attribuer au sous-réseau ASE la taille /25 avec 128 adresses afin qu’il puisse prendre en charge toute croissance à venir.  Cette valeur ne peut pas être modifiée une fois l’environnement ASE créé.
* L’ID de ressource de votre réseau virtuel.  Vous pouvez l’obtenir à partir du portail Azure sous vos propriétés de réseau virtuel Azure.
* L’abonnement dans lequel vous souhaitez procéder au déploiement.
* L’emplacement dans lequel vous souhaitez procéder au déploiement.

Pour automatiser la création de votre environnement ASE :

1. Créez l’environnement ASE à partir d’un modèle.  Si vous créez un environnement ASE externe, vous en avez terminé avec cette étape unique.  Si vous créez un environnement ASE ILB, vous devez effectuer quelques étapes supplémentaires.
2. Une fois votre environnement ASE ILB créé, un certificat SSL correspondant à votre domaine ASE ILB est chargé.
3. Le certificat SSL chargé est affecté à l’environnement ASE ILB en tant que certificat SSL « par défaut ».  Ce certificat SSL servira à des applications de l’ILB ASE pour le trafic SSL lorsque celles-ci sont gérées à l’aide d’un domaine racine commun affecté à l’ASE (par exemple, https://someapp.mycustomrootcomain.com).


## <a name="creating-the-ase"></a>Création de l’environnement ASE
Un exemple de modèle Azure Resource Manager permettant de créer un environnement ASE, et son fichier de paramètres associé, sont disponibles sur GitHub [ici][quickstartasev2create].

Pour créer un environnement ASE ILB à ce moment-là, vous devez alors utiliser les modèles Resource Manager [ici][quickstartilbasecreate].  Ils sont destinés à ce cas d’usage.  La plupart des paramètres du fichier *azuredeploy.parameters.json* sont communs à la création des environnements ASE ILB et des environnements ASE externes.  La liste ci-dessous répertorie les paramètres spécifiques, ou qui sont uniques, lors de la création d’un ILB ASE :

* *interalLoadBalancingMode* : dans la plupart des cas, définissez ce paramètre sur 3, ce qui signifie que le trafic HTTP/HTTPS sur les ports 80/443 ainsi que les ports de canaux de contrôle/données écoutés par le service FTP sur l’ASE seront liés à une adresse interne du réseau virtuel allouée à l’ILB.  Si cette propriété a la valeur 2, seuls les ports relatifs au service FTP (canaux de contrôle et de données) seront liés à une adresse d’ILB, tandis que le trafic HTTP/HTTPS restera sur l’adresse IP virtuelle publique.
* *dnsSuffix* : ce paramètre définit le domaine racine par défaut qui sera affecté à l’ASE.  Dans la version publique d’Azure App Service, le domaine racine par défaut pour toutes les applications web est *azurewebsites.net*.  Cependant, étant donné qu’un ILB ASE est interne au réseau virtuel d’un client, il n’est pas pertinent d’utiliser le domaine racine par défaut du service public.  Au lieu de cela, un ILB ASE doit avoir un domaine racine par défaut approprié pour une utilisation au sein du réseau virtuel interne d’une société.  Par exemple, une société fictive Contoso Corporation peut utiliser le domaine racine par défaut *internal-contoso.com* pour les applications qui sont destinées à être résolues et accessibles uniquement au sein du réseau virtuel de Contoso. 
* *ipSslAddressCount* : ce paramètre est automatiquement défini par défaut sur la valeur 0 dans le fichier *azuredeploy.json*, car les ILB ASE disposent d’une seule adresse d’ILB.  Il n’existe aucune adresse IP SSL explicite pour un ILB ASE. Par conséquent, le pool d’adresses IP SSL pour un ILB ASE doit être défini sur zéro, sinon une erreur d’approvisionnement se produit. 

Une fois le fichier *azuredeploy.parameters.json* renseigné, l’environnement ASE peut être créé à l’aide de l’extrait de code PowerShell indiqué ci-après.  Modifiez les chemins d’accès de fichiers pour qu’ils correspondent aux emplacements où se trouvent les fichiers de modèle Azure Resource Manager sur votre ordinateur.  Pensez également à indiquer vos propres valeurs pour le nom de déploiement d’Azure Resource Manager et le nom de groupe de ressources.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Une fois le modèle Azure Resource Manager soumis, la création de l’environnement ASE prend environ une heure.  À l’issue de la création, l’environnement ASE s’affiche dans le portail, dans la liste des environnements ASE correspondant à l’abonnement qui a déclenché le déploiement.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Téléchargement et configuration du certificat SSL « par défaut »
Une fois l’ILB ASE créé, un certificat SSL doit être associé à l’ASE en tant que certificat SSL « par défaut » utilisé pour établir des connexions SSL aux applications.  Poursuivons avec l’exemple de la société fictive Contoso Corporation. Si le suffixe DNS par défaut de l’ASE est *internal-contoso.com*, une connexion à *https://some-random-app.internal-contoso.com* nécessite un certificat SSL valide pour **.internal-contoso.com*. 

Il existe plusieurs façons d’obtenir un certificat SSL valide, parmi lesquelles les autorités de certification internes, l’achat d’un certificat auprès d’un émetteur externe et l’utilisation d’un certificat auto-signé.  Quelle que soit la source du certificat SSL, les attributs de certificat suivants doivent être configurés correctement :

* *Objet* : cet attribut doit être défini sur **.votre-domaine-racine-ici.com*
* *Autre nom de l’objet* : cet attribut doit inclure à la fois **.votre-domaine-racine-ici.com* et **.scm.votre-domaine-racine-ici.com*.  La deuxième entrée est nécessaire, car les connexions SSL sur le site SCM/Kudu associé à chaque application seront établies à l’aide d’une adresse sous la forme *votre-nom-application.scm.votre-domaine-racine-ici.com*.

Une fois le certificat SSL valide obtenu, deux étapes préparatoires supplémentaires sont nécessaires.  Le certificat SSL doit être converti/enregistré au format de fichier .pfx.  N’oubliez pas que le fichier .pfx doit inclure tous les certificats intermédiaires et racine et être sécurisé avec un mot de passe.

Le fichier .pfx obtenu doit ensuite être converti en une chaîne au format Base64, car le certificat SSL sera téléchargé à l’aide d’un modèle Azure Resource Manager.  Étant donné que les modèles Azure Resource Manager sont des fichiers texte, le fichier .pfx doit être converti en une chaîne au format Base64 afin d’être inclus en tant que paramètre du modèle.

L’extrait de code Powershell ci-dessous montre un exemple de génération d’un certificat auto-signé, d’exportation du certificat en tant que fichier .pfx, de conversion du fichier .pfx au format Base64 de la chaîne codée, puis d’enregistrement de la chaîne codée en Base64 dans un fichier distinct.  Le code PowerShell pour l’encodage en Base64 est une adaptation issue du [Powershell Scripts Blog (Blog relatif aux scripts PowerShell)][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Une fois le certificat SSL généré et converti en chaîne encodée en Base64, vous pouvez utiliser l’exemple de modèle Azure Resource Manager sur GitHub pour [configurer le certificat SSL par défaut][quickstartconfiguressl].

Les paramètres du fichier *azuredeploy.parameters.json* sont répertoriés ci-dessous :

* *appServiceEnvironmentName* : nom de l’ILB ASE configuré.
* *existingAseLocation* : chaîne de texte contenant la région Azure où l’ILB ASE a été déployé.  Par exemple, « South Central US ».
* *pfxBlobString* : représentation sous forme de chaîne codée en Base64 du fichier .pfx.  À l’aide de l’extrait de code ci-dessus, copiez la chaîne contenue dans « exportedcert.pfx.b64 » et collez-la en tant que valeur de l’attribut *pfxBlobString* .
* *password* : mot de passe utilisé pour sécuriser le fichier .pfx.
* *certificateThumbprint* : empreinte numérique du certificat.  Si vous récupérez cette valeur à partir de Powershell (par exemple, *$certificate.Thumbprint* dans l’extrait de code précédent), vous pouvez utiliser la valeur telle quelle.  Toutefois, si vous copiez la valeur à partir de la boîte de dialogue de certificat Windows, n’oubliez pas de retirer les espaces superflus.  La valeur *certificateThumbprint* doit se présenter sous la forme suivante : AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName* : identificateur de chaîne convivial de votre choix permettant d’identifier le certificat.  Ce nom fait partie de l’identificateur Azure Resource Manager unique pour l’entité *Microsoft.Web/certificates* qui représente le certificat SSL.  Le nom **doit** se terminer par le suffixe suivant : \_nomdevotreASE_InternalLoadBalancingASE.  Ce suffixe permet au portail d’indiquer que le certificat est utilisé pour sécuriser un ASE avec équilibrage de charge interne.

Un exemple abrégé de fichier *azuredeploy.parameters.json* est présenté ci-dessous :

    {
         "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

Une fois le fichier *azuredeploy.parameters.json* renseigné, le certificat SSL par défaut peut être configuré à l’aide de l’extrait de code Powershell suivant.  Modifiez les chemins d’accès de fichiers pour qu’ils correspondent aux emplacements où se trouvent les fichiers de modèle Azure Resource Manager sur votre ordinateur.  Pensez également à indiquer vos propres valeurs pour le nom de déploiement d’Azure Resource Manager et le nom de groupe de ressources.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Une fois le modèle Azure Resource Manager soumis, cela prendra environ quarante minutes par serveur frontal de l’ASE pour que la modification soit appliquée.  Par exemple, avec un ASE par défaut utilisant deux serveurs frontaux, l’application du modèle prendra environ une heure et vingt minutes.  Lorsque le modèle est en cours d’exécution, l’ASE ne peut pas être mis à l’échelle.  

Une fois le modèle terminé, les applications sur l’ILB ASE sont accessibles via le protocole HTTPS et les connexions sont sécurisées à l’aide du certificat SSL par défaut.  Le certificat SSL par défaut est utilisé lorsque les applications sur l’ILB ASE sont gérées à l’aide d’une combinaison du nom de l’application et du nom d’hôte par défaut.  Par exemple, *https://mycustomapp.internal-contoso.com* utiliserait le certificat SSL par défaut pour **.internal-contoso.com*.

Toutefois, tout comme les applications qui s’exécutent sur le service public mutualisé, les développeurs peuvent également configurer des noms d’hôte personnalisés pour les applications individuelles, puis configurer des liaisons de certificat SNI SSL uniques pour ces applications individuelles.  

## <a name="asev1"></a>ASEv1 ##
L’environnement ASE présente deux versions : ASEv1 et ASEv2. Les informations précédentes concernent la version ASEv2. Cette section vous indique les différences existant entre les versions ASEv1 et ASEv2.

Dans un environnement ASEv1, vous devez gérer toutes les ressources manuellement, notamment les serveurs frontaux, les rôles de travail et les adresses IP utilisées pour le protocole SSL basé sur IP. Avant de pouvoir augmenter la taille des instances de votre plan App Service, vous devez commencer par effectuer cette procédure dans le pool de travail dans lequel vous voulez héberger le plan.

Les versions ASEv1 et ASEv2 utilisent un modèle de tarification différent. Dans ASEv1, vous devez payer pour chaque cœur alloué. Cela inclut les cœurs utilisés pour les serveurs frontaux ou les rôles de travail qui n’hébergent pas de charges de travail. Dans la version ASEv1, la taille d’échelle maximale par défaut d’un environnement ASE correspond à un total de 55 hôtes, dont les rôles de travail et les serveurs frontaux. L’un des avantages d’un environnement ASEv1 est qu’il peut être déployé dans un réseau virtuel classique et dans un réseau virtuel Resource Manager. Pour plus d’informations sur la version ASEv1, voir [Présentation de l’environnement App Service v1][ASEv1Intro].

Si vous souhaitez créer un environnement ASEv1 à l’aide d’un modèle Resource Manager, vous pouvez commencer par lire l’article [Comment créer un environnement ASE ILB v1 à l’aide des modèles Azure Resource Manager][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: ../../app-service-web/app-service-app-service-environment-create-ilb-ase-resourcemanager.md

