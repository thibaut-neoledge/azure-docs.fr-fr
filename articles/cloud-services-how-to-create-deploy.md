<properties  linkid="manage-services-how-to-create-and-deploy-a-cloud-service" urlDisplayName="How to create and deploy" pageTitle="How to create and deploy a cloud service - Azure" metaKeywords="Azure creating cloud service, deleting cloud service" description="Learn how to create and deploy a cloud service using the Quick Create method in Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Create and Deploy a Cloud Service" authors="ryanwi" solutions="" manager="" editor="" />

# Création et déploiement d'un service cloud

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

Le portail de gestion Azure vous offre deux manières de créer et de déployer un service cloud : **Quick Create** et **Custom Create**.

Cette rubrique explique comment utiliser la méthode Quick Create pour créer un service cloud et comment utiliser ensuite **Upload** pour télécharger et déployer un package de service cloud dans Azure. Lorsque vous utilisez cette méthode, le portail de gestion Azure met à votre disposition tous les liens nécessaires pour compléter les éléments au fur et à mesure. Si vous êtes prêt à déployer votre service cloud lorsque vous le créez, vous pouvez effectuer ces deux opérations en même temps à l'aide de **Custom Create**.

**Remarque** Si vous prévoyez de publier votre service cloud depuis
Windows Team Foundation Services (TFS), utilisez Quick Create, puis configurez la publication TFS dans **Quick Start** ou dans le tableau de bord. Pour plus d'informations, consultez la page [Remise continue sur Azure avec Team Foundation Service Preview][1] ou **Quick Start**.

## Sommaire

* [Concepts](#concepts)
* [Préparation de votre application](#prepare)
* [Avant de commencer](#begin)
* [Création d'un service cloud avec Quick Create](#quick)
* [Téléchargement d'un certificat pour un service cloud](#uploadcertificate)
* [Déploiement d'un service cloud](#deploy)

<h2><a  id="concepts" ></a>Concepts</h2>


Trois composants sont nécessaires pour déployer une application en tant que service cloud dans Azure :

> * **fichier de définition du service** Le fichier de définition du   service cloud (.csdef) définit le modèle du service, avec le nombre de rôles.

> * **fichier de configuration de service** Le fichier de configuration du service cloud (.cscfg) contient des paramètres de configuration pour le service cloud et les différents rôles, y compris le nombre d'instances de rôle.

> * **package de service** Le package de service (.cspkg) contient le code de l'application et le fichier de définition de service.

<h2><a  id="prepare" ></a>Préparation de votre application</h2>


Avant de déployer un service cloud, vous devez créer le package de service cloud (.cspkg) à partir du code de l'application, ainsi que le fichier de configuration de service cloud (.cscfg). Chaque package de service cloud contient des fichiers d'application et des configurations. Le fichier de configuration de service contient les paramètres de configuration.

Le Kit de développement logiciel (SDK) Azure fournit les outils nécessaires à la préparation des fichiers de déploiement. Vous pouvez l'installer depuis la page des [téléchargements Azure][2], dans le langage souhaité pour le développement de votre code.

Si vous découvrez les services cloud, vous pouvez télécharger un exemple de package de services cloud (.cspkg) et de fichier de configuration de service (.cscfg) dans les [exemples de code Azure][3].

Trois fonctions du service cloud nécessitent une configuration spécifique avant d'exporter le package de service :

* Si vous souhaitez déployer un service cloud qui utilise le chiffrement de données SSL (Secure Sockets Layer), configurez votre application pour SSL. Pour plus d'informations, consultez la page [Configuration d'un certificat SSL sur un point de terminaison HTTPS][4].

* Si vous voulez configurer les connexions Bureau à distance aux instances de rôle, configurez les rôles pour le Bureau à distance. Pour plus d'informations sur la préparation du fichier de définition du service pour l'accès à distance, consultez la page [Présentation de la configuration d'une connexion Bureau à distance pour un rôle][5].

* Si vous voulez configurer la surveillance détaillée pour votre service cloud, activez Azure Diagnostics pour le service cloud. *Surveillance minimale* (niveau de surveillance par défaut) utilise des compteurs de performances récupérés sur le système d'exploitation hôte des instances de rôle (machine virtuelle). La surveillance détaillée recueille des mesures supplémentaires sur les données de performances dans les instances de rôle, afin de permettre une analyse plus fine des problèmes qui surviennent au cours du traitement de l'application. Pour savoir comment activer Azure Diagnostics, consultez la page [Activation des diagnostics dans Azure][6].

<h2><a  id="begin" ></a>Avant de commencer</h2>


* Si vous n'avez pas installé le Kit de développement logiciel (SDK), cliquez sur **Install Azure SDK** pour ouvrir la page des [téléchargements Azure][2], puis téléchargez le Kit de développement logiciel dans le langage souhaité pour le développement de votre code. (Vous pourrez le faire ultérieurement.)

* Si des instances de rôle nécessitent des certificats, créez ces certificats. Les services cloud requièrent un fichier .pfx avec une clé privée. Vous pouvez télécharger les certificats sur Azure lorsque vous créez et déployez le service cloud. Pour plus d'informations sur la création de certificats, consultez la page [Configuration d'un certificat SSL sur un point de terminaison HTTPS][4].

* Si vous prévoyez de déployer le service cloud dans un groupe d'affinités, créez le groupe d'affinités. Vous pouvez utiliser un groupe d'affinités pour déployer votre service cloud et d'autres services Azure dans le même emplacement dans une région. Vous pouvez créer le groupe d'affinités dans la zone **Networks** du portail de gestion, sur la page **Affinity Groups**. Pour plus d'informations, consultez la page d'aide sur les **groupes d'affinités**.

<h2><a  id="quick" ></a>Création d'un service cloud avec Quick Create</h2>


1.  Dans le [portail de gestion][7], cliquez sur **New**, cliquez sur **Cloud Service**, puis sur **Quick Create**.
    
    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2.  Dans **URL**, entrez un nom de sous-domaine à utiliser dans l'URL publique pour accéder au service cloud dans les déploiements de production. Le format de l'URL des déploiements de production est le suivant : http://*monURL*.cloudapp.net.

3.  Dans **Region/Affinity Group**, sélectionnez la zone géographique ou le groupe d'affinités où déployer le service cloud. Sélectionnez un groupe d'affinités si vous voulez déployer votre service cloud dans le même emplacement que d'autres services Azure dans une région.
    
    > [WACOM.NOTE] 
    > Pour créer un groupe d'affinités, ouvrez la zone **Networks** du portail de gestion, cliquez sur **Affinity Groups**, puis cliquez sur **Create a new affinity group** ou sur **Create**. Vous pouvez utiliser les groupes d'affinités que vous avez créés précédemment dans le portail de gestion Azure. Vous pouvez également créer des groupes d'affinités et les gérer grâce à l'API Azure Service Management. Pour plus d'informations, consultez la page [Opérations sur les groupes d'affinités][8].

4.  Cliquez sur **Create Cloud Service**.
    
    Vous pouvez surveiller l'état du processus dans la zone de message en bas de la fenêtre.
    
    La zone **Cloud Services** s'affiche, avec le nouveau service cloud. Lorsque l'état passe à Created, la création du service cloud s'est effectuée correctement.
    
    ![CloudServices_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)
    
    Si un des rôles du service cloud nécessite un certificat pour le chiffrement de données SSL et que le certificat n'a pas été téléchargé sur Azure, vous devez télécharger ce certificat avant de déployer le service cloud. Une fois le certificat téléchargé, les applications Windows qui s'exécutent dans les instances de rôles
    peuvent y accéder.

<h2><a  id="uploadcertificate" ></a>Téléchargement d'un certificat pour un service cloud</h2>


1.  Dans le [portail de gestion][7], cliquez sur **Cloud Services**. Cliquez ensuite sur le nom du service cloud pour ouvrir le tableau de bord.
    
    ![CloudServices_EmptyDashboard](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)

2.  Cliquez sur **Certificates** pour ouvrir la page **Certificates** présentée ci-dessous.
    
    ![CloudServices_CertificatesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CertificatesPage.png)

3.  Cliquez sur **Add new certificate** ou sur **Upload**. **Add a Certificate** s'affiche.
    
    ![CloudServices_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

4.  Dans **Certificate file**, utilisez **Browse** pour sélectionner le certificat (fichier .pfx) à utiliser.

5.  Dans **Password**, entrez la clé privée du certificat.

6.  Cliquez sur OK (coche).
    
    Vous pouvez consulter l'état du téléchargement dans la zone de message, comme illustré ci-dessous. Une fois le téléchargement terminé, le certificat est ajouté à la table. Dans la zone de message, cliquez sur la flèche vers le bas pour fermer le message ou cliquez sur X pour le supprimer.
    
    ![CloudServices_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)
    
    Vous pouvez déployer votre service cloud à partir du tableau de bord ou de **Quick Start**.

<h2><a  id="deploy" ></a>Déploiement d'un service cloud</h2>


1.  Dans le [portail de gestion][7], cliquez sur **Cloud Services**. Cliquez ensuite sur le nom du service cloud pour ouvrir le tableau de bord.

2.  Cliquez sur **Quick Start** (icône à gauche de **Dashboard**) pour ouvrir la page **Quick Start**, présentée ci-dessous. (Vous pouvez également déployer votre service cloud à l'aide de la fonction **Upload** du tableau de bord.)
    
    ![CloudServices_QuickStartPage](./media/cloud-services-how-to-create-deploy/CloudServices_QuickStartPage.png)

3.  Si vous n'avez pas installé le Kit de développement logiciel (SDK), cliquez sur **Install Azure SDK** pour ouvrir la page des [téléchargements Azure][2], puis téléchargez le Kit de développement logiciel dans le langage souhaité pour le développement de votre code.
    
    Dans la page des téléchargements, vous pouvez également installer des bibliothèques clientes et du code source pour développer des applications Web Node.js, Java, PHP ou autres, que vous pouvez déployer comme services cloud Azure extensibles.
    
    > [WACOM.NOTE] 
    > Pour les services cloud créés précédemment (appelés *services hébergés*), vous devez vous assurer que les systèmes
    > d'exploitation hôtes sur les machines virtuelles (instances de rôle) sont compatibles avec la version du Kit de développement logiciel (SDK) Azure que vous installez. Pour plus d'informations, consultez les [notes de publication du Kit de développement logiciel (SDK) Azure][9].

4.  Cliquez sur **New Production Deployment** ou **New Staging Deployment**.
    
    Si vous souhaitez tester votre service cloud Azure avant de le déployer en production, vous pouvez effectuer un déploiement intermédiaire. Dans l'environnement intermédiaire, le GUID du service cloud identifie le service cloud dans les URL (*GUID*.cloudapp.net). Dans l'environnement de production, le préfixe DNS convivial que vous affectez est utilisé (par exemple, *myservice*.cloudapp.net). Lorsque vous êtes prêt à passer votre service cloud intermédiaire en production, utilisez **Swap** pour rediriger les demandes des clients vers ce déploiement.
    
    Lorsque vous sélectionnez un environnement de déploiement, **Upload a Package** s'affiche.
    
    ![CloudServices_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)

5.  Dans **Deployment name**, entrez le nom du nouveau déploiement, par exemple MyCloudServicev1.

6.  Dans **Package**, utilisez **Browse** pour sélectionner le fichier de package du service (.cspkg) à utiliser.

7.  Dans **Configuration**, utilisez **Browse** pour sélectionner le fichier de configuration du service (.cscfg) à utiliser.

8.  Si le service cloud doit inclure des rôles avec une seule instance, activez la case à cocher **Deploy even if one or more roles contain a single instance** afin de permettre au déploiement de continuer.

	Azure ne peut garantir un pourcentage d'accès au service cloud que de 99,95 % pendant la maintenance et la mise à jour du service si tous les rôles disposent d'au moins deux instances Si besoin, vous pouvez ajouter des instances de rôle supplémentaires dans la page **Échelle** une fois le service cloud déployé. Pour plus d'informations, consultez la page [Contrats de niveau de service][10].

1.  Cliquez sur OK (coche) pour lancer le déploiement du service cloud.
    
    Vous pouvez surveiller l'état du déploiement dans la zone de message. Cliquez sur la flèche vers le bas pour masquer le message.
    
    ![CloudServices_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

### Pour vérifier la réussite du déploiement

1.  Cliquez sur **Dashboard**.

2.  Sous **quick glance**, cliquez sur l'URL du site pour ouvrir le service cloud dans un navigateur Web.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)



[1]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409
[2]: http://www.windowsazure.com/en-us/develop/downloads/
[3]: http://code.msdn.microsoft.com/windowsazure/
[4]: http://msdn.microsoft.com/en-us/library/windowsazure/ff795779.aspx
[5]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433010.aspx
[6]: http://www.windowsazure.com/en-us/develop/net/common-tasks/diagnostics/
[7]: http://manage.windowsazure.com/
[8]: http://msdn.microsoft.com/en-us/library/windowsazure/ee460798.aspx
[9]: http://msdn.microsoft.com/en-us/library/windowsazure/hh552718.aspx
[10]: http://www.windowsazure.com/en-us/support/legal/sla/