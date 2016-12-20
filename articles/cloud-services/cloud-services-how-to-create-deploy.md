---
title: "Création et déploiement d’un service cloud | Microsoft Docs"
description: "Découvrez comment créer et déployer un service cloud à l&quot;aide de la méthode Création rapide dans Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 0ea78ccc-5e7d-40f8-bdb6-478c0eb0e265
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6a3437ce502711a56f7201a87c84e8336b7a3e05


---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Création et déploiement d'un service cloud
> [!div class="op_single_selector"]
> * [Portail Azure](cloud-services-how-to-create-deploy-portal.md)
> * [portail Azure Classic](cloud-services-how-to-create-deploy.md)
> 
> 

Le portail Azure Classic vous permet de créer et de déployer un service cloud de deux manières : **Création rapide** et **Création personnalisée**.

Cette rubrique explique comment utiliser la méthode Création rapide pour créer un service cloud et comment utiliser ensuite **Télécharger** pour télécharger et déployer un package de service cloud dans Azure. Si vous utilisez cette méthode, le portail Azure Classic met à votre disposition tous les liens nécessaires pour remplir les conditions requises au fur et à mesure. Si vous êtes prêt à déployer votre service cloud lorsque vous le créez, vous pouvez effectuer ces deux opérations en même temps à l'aide de **Custom Create**.

> [!NOTE]
> Si vous prévoyez de publier votre service cloud depuis Visual Studio Team Services (VSTS), utilisez Création rapide, puis configurez la publication VSTS dans **Création rapide** ou dans le tableau de bord. Pour plus d’informations, consultez la page [Livraison continue sur Azure au moyen de Visual Studio Team Services][TFSTutorialForCloudService] ou consultez l’aide sur la page de **démarrage rapide**.
> 
> 

## <a name="concepts"></a>Concepts
trois composants sont nécessaires pour déployer une application en tant que service cloud dans Azure :

* **Définition de service**  
   Le fichier de définition de service cloud (.csdef) définit le modèle de service, notamment le nombre de rôles.
* **Configuration de service**  
   Le fichier de configuration de service cloud (.cscfg) contient les paramètres de configuration du service cloud et des différents rôles, notamment le nombre d’instances de rôle.
* **Package de service**  
   Le package de service (.cspkg) contient le code d’application, les configurations et le fichier de définition de service.

Pour plus d’informations sur ces composants et sur la création d’un package, cliquez [ici](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Préparation de votre application
Avant de déployer un service cloud, vous devez créer le package de service cloud (.cspkg) à partir du code de l'application, ainsi que le fichier de configuration de service cloud (.cscfg). Le Kit de développement logiciel (SDK) Azure fournit les outils nécessaires à la préparation des fichiers de déploiement. Vous pouvez installer le Kit de développement logiciel (SDK) depuis la page des [téléchargements Azure](https://azure.microsoft.com/downloads/) , dans le langage souhaité pour le développement de votre code.

Trois fonctions du service cloud nécessitent une configuration spécifique avant d'exporter le package de service :

* Si vous souhaitez déployer un service cloud qui utilise le chiffrement de données SSL (Secure Sockets Layer), [configurez votre application](cloud-services-configure-ssl-certificate.md#step-2-modify-the-service-definition-and-configuration-files) pour SSL.
* Si vous voulez configurer les connexions Bureau à distance aux instances de rôle, [configurez les rôles](cloud-services-role-enable-remote-desktop.md) pour le Bureau à distance.
* Si vous voulez configurer la surveillance détaillée pour votre service cloud, activez Azure Diagnostics pour le service cloud. *Surveillance minimale* (niveau de surveillance par défaut) utilise des compteurs de performances récupérés sur le système d'exploitation hôte des instances de rôle (machine virtuelle). La surveillance détaillée recueille des mesures supplémentaires sur les données de performances dans les instances de rôle, afin de permettre une analyse plus fine des problèmes qui surviennent au cours du traitement de l'application. Pour savoir comment activer Azure Diagnostics, consultez la page [Activation des diagnostics dans Azure](cloud-services-dotnet-diagnostics.md).

Pour créer un service cloud avec des déploiements de rôles web ou de rôles de travail, vous devez [créer le package de service](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Avant de commencer
* Si vous n'avez pas installé le Kit de développement logiciel (SDK), cliquez sur **Install Azure SDK** pour ouvrir la page des [téléchargements Azure](https://azure.microsoft.com/downloads/), puis téléchargez le Kit de développement logiciel dans le langage souhaité pour le développement de votre code. (Vous pourrez le faire ultérieurement.)
* Si des instances de rôle nécessitent des certificats, créez ces certificats. Les services cloud requièrent un fichier .pfx avec une clé privée. Vous pouvez [charger les certificats sur Azure](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) quand vous créez et déployez le service cloud.
* Si vous prévoyez de déployer le service cloud dans un groupe d'affinités, créez le groupe d'affinités. Vous pouvez utiliser un groupe d'affinités pour déployer votre service cloud et d'autres services Azure dans le même emplacement dans une région. Vous pouvez créer le groupe d’affinités dans la zone **Réseaux** du portail Azure Classic, dans la page **Groupes d’affinités**.

## <a name="how-to-create-a-cloud-service-using-quick-create"></a>Création d’un service cloud avec Création rapide
1. Dans le [portail Azure Classic](http://manage.windowsazure.com/), cliquez sur **Nouveau**>**Calculer**>**Service cloud**>**Création rapide**.
   
    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)
2. Dans **URL**, entrez un nom de sous-domaine à utiliser dans l'URL publique pour accéder au service cloud dans les déploiements de production. Le format de l’URL des déploiements de production est le suivant : http://*myURL*.cloudapp.net.
3. Dans **Région ou groupe d'affinités**, sélectionnez la zone géographique ou le groupe d'affinités où déployer le service cloud. Sélectionnez un groupe d'affinités si vous voulez déployer votre service cloud dans le même emplacement que d'autres services Azure dans une région.
4. Cliquez sur **Create Cloud Service**.
   
    ![CloudServices_Region](./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png)
   
    Vous pouvez surveiller l'état du processus dans la zone de message en bas de la fenêtre.
   
    La zone **Cloud Services** s'affiche, avec le nouveau service cloud. Lorsque l'état passe à Created, la création du service cloud s'est effectuée correctement.
   
    ![CloudServices_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)

## <a name="how-to-upload-a-certificate-for-a-cloud-service"></a>Téléchargement d’un certificat pour un service cloud
1. Dans le [portail Azure Classic](http://manage.windowsazure.com/), cliquez sur **Services cloud**, sur le nom du service cloud, puis sur **Certificats**.
   
    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)
2. Cliquez sur **Télécharger un certificat** ou sur **Télécharger**.
3. Dans **Fichier**, utilisez **Parcourir** pour sélectionner le certificat (fichier .pfx).
4. Dans **Password**, entrez la clé privée du certificat.
5. Cliquez sur **OK** (coche).
   
    ![CloudServices_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)
   
    Vous pouvez consulter l'état du téléchargement dans la zone de message, comme illustré ci-dessous. Une fois le téléchargement terminé, le certificat est ajouté à la table. Dans la zone de message, cliquez sur OK pour fermer le message.
   
    ![CloudServices_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

## <a name="how-to-deploy-a-cloud-service"></a>Déploiement d’un service cloud
1. Dans le [portail Azure Classic](http://manage.windowsazure.com/), cliquez sur **Services cloud**, sur le nom du service cloud, puis sur **Tableau de bord**.
2. Cliquez sur **Télécharger un nouveau déploiement de production** ou sur **Télécharger**.
3. Dans **Étiquette du déploiement**, entrez le nom du nouveau déploiement, par exemple MyCloudServicev4.
4. Dans **Package**, utilisez **Parcourir** pour sélectionner le fichier de package du service (.cspkg) à utiliser.
5. Dans **Configuration**, utilisez **Parcourir** pour sélectionner le fichier de configuration du service (.cscfg) à utiliser.
6. Si le service cloud doit inclure des rôles avec une seule instance, activez la case à cocher **Deploy even if one or more roles contain a single instance** afin de permettre au déploiement de continuer.
   
    Azure ne peut garantir un pourcentage d'accès au service cloud que de 99,95 % pendant la maintenance et la mise à jour du service si tous les rôles disposent d'au moins deux instances Si besoin, vous pouvez ajouter des instances de rôle supplémentaires dans la page **Échelle** une fois le service cloud déployé. Pour plus d'informations, consultez la page [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/).
7. Cliquez sur **OK** (coche) pour lancer le déploiement du service cloud.
   
    ![CloudServices_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)
   
    Vous pouvez surveiller l'état du déploiement dans la zone de message. Cliquez sur OK pour masquer le message.
   
    ![CloudServices_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

## <a name="verify-your-deployment-completed-successfully"></a>Vérifier la réussite du déploiement
1. Cliquez sur **Dashboard**.
   
    L'état présente le service comme étant **En cours d'exécution**.
2. Sous **quick glance**, cliquez sur l'URL du site pour ouvrir le service cloud dans un navigateur Web.
   
    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)

[TFSTutorialForCloudService]: cloud-services-continuous-delivery-use-vso.md

## <a name="next-steps"></a>Étapes suivantes
* [Configuration générale de votre service cloud](cloud-services-how-to-configure.md).
* Configurez un [nom de domaine personnalisé](cloud-services-custom-domain-name.md).
* [Gérez votre service cloud](cloud-services-how-to-manage.md).
* Configurez des [certificats SSL](cloud-services-configure-ssl-certificate.md).




<!--HONumber=Nov16_HO3-->


