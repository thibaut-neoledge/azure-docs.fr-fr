<properties urlDisplayName="How to create and deploy" pageTitle="Cr&eacute;ation et d&eacute;ploiement d'un service cloud - Azure" metaKeywords="Azure creating cloud service, deleting cloud service" description="D&eacute;couvrez comment cr&eacute;er et d&eacute;ployer un service cloud &agrave; l'aide de la m&eacute;thode Cr&eacute;ation rapide dans Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="Cr&eacute;ation et d&eacute;ploiement d'un service cloud" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="ryanwi" />

# Création et déploiement d'un service cloud

Le portail de gestion Azure vous offre deux manières de créer et de déployer un service cloud : **Création rapide** et **Création personnalisée**.

Cette rubrique explique comment utiliser la méthode Création rapide pour créer un service cloud et comment utiliser ensuite **Télécharger** pour charger et déployer un package de service cloud dans Azure. Lorsque vous utilisez cette méthode, le portail de gestion Azure met à votre disposition tous les liens nécessaires pour compléter les éléments au fur et à mesure. Si vous êtes prêt à déployer votre service cloud lorsque vous le créez, vous pouvez effectuer ces deux opérations en même temps à l'aide de **Création personnalisée**.

**Remarque** Si vous prévoyez de publier votre service cloud depuis Windows Team Foundation Services (TFS), utilisez Création rapide, puis configurez la publication TFS dans **Création rapide** ou dans le tableau de bord. Pour plus d'informations, consultez la page [Livraison continue sur Azure au moyen de Visual Studio Online][Livraison continue sur Azure au moyen de Visual Studio Online] ou consultez l'aide sur la page de **démarrage rapide**.

## Sommaire

-   [Concepts][Concepts]
-   [Préparation de votre application][Préparation de votre application]
-   [Avant de commencer][Avant de commencer]
-   [Création d'un service cloud avec Quick Create][Création d'un service cloud avec Quick Create]
-   [Téléchargement d'un certificat pour un service cloud][Téléchargement d'un certificat pour un service cloud]
-   [Déploiement d'un service cloud][Déploiement d'un service cloud]

## <span id="concepts"></span></a>Concepts

trois composants sont nécessaires pour déployer une application en tant que service cloud dans Azure :

> -   **fichier de définition du service** Le fichier de définition du service cloud (.csdef) définit le modèle du service, avec le nombre de rôles.

> -   **fichier de configuration de service** Le fichier de configuration de service (.cscfg) contient des paramètres de configuration pour le service cloud et les différents rôles, y compris le nombre d'instances de rôle.

> -   **package de service** Le package de service (.cspkg) contient le code de l'application et le fichier de définition de service.

## <span id="prepare"></span></a>Préparation de votre application

Avant de déployer un service cloud, vous devez créer le package de service cloud (.cspkg) à partir du code de l'application, ainsi que le fichier de configuration de service cloud (.cscfg). Chaque package de service cloud contient des fichiers d'application et des configurations. Le fichier de configuration de service contient les paramètres de configuration.

Le Kit de développement logiciel (SDK) Azure fournit les outils nécessaires à la préparation des fichiers de déploiement. Vous pouvez installer le Kit de développement logiciel (SDK) depuis la page des [téléchargements Azure][téléchargements Azure], dans le langage souhaité pour le développement de votre code.

Trois fonctions du service cloud nécessitent une configuration spécifique avant d'exporter le package de service :

-   Si vous souhaitez déployer un service cloud qui utilise le chiffrement de données SSL (Secure Sockets Layer), configurez votre application pour SSL. Pour plus d'informations, consultez la page [Configuration d'un certificat SSL sur un point de terminaison HTTPS][Configuration d'un certificat SSL sur un point de terminaison HTTPS].

-   Si vous voulez configurer les connexions Bureau à distance aux instances de rôle, configurez les rôles pour le Bureau à distance. Pour plus d'informations sur la préparation du fichier de définition du service pour l'accès à distance, consultez la page [Configuration d'une connexion Bureau à distance pour un rôle dans Azure][Configuration d'une connexion Bureau à distance pour un rôle dans Azure].

-   Si vous voulez configurer la surveillance détaillée pour votre service cloud, activez Azure Diagnostics pour le service cloud. *Surveillance minimale* (niveau de surveillance par défaut) utilise des compteurs de performances récupérés sur le système d'exploitation hôte des instances de rôle (machine virtuelle). La surveillance détaillée recueille des mesures supplémentaires sur les données de performances dans les instances de rôle, afin de permettre une analyse plus fine des problèmes qui surviennent au cours du traitement de l'application. Pour savoir comment activer Azure Diagnostics, consultez la page [Activation des diagnostics dans Azure][Activation des diagnostics dans Azure].

-   Pour créer un service cloud avec des déploiements de rôles web ou de rôles de travail, vous devez créer le package de service. Plus d'informations sur les fichiers du package, consultez la page [Configurer un service cloud pour Azure][Configurer un service cloud pour Azure]. Pour créer le package, consultez la page [Création d'un package d'application Microsoft Azure][Création d'un package d'application Microsoft Azure]. Si vous utilisez Visual Studio pour développer votre application, consultez la page [Publication d'un service cloud en utilisant les outils Azure][Publication d'un service cloud en utilisant les outils Azure].

## <span id="begin"></span></a>Avant de commencer

-   Si vous n'avez pas installé le Kit de développement logiciel (SDK), cliquez sur **Install Azure SDK** pour ouvrir la page des [téléchargements Azure][téléchargements Azure], puis téléchargez le Kit de développement logiciel dans le langage souhaité pour le développement de votre code. (Vous pourrez le faire ultérieurement.)

-   Si des instances de rôle nécessitent des certificats, créez ces certificats. Les services cloud requièrent un fichier .pfx avec une clé privée. Vous pouvez télécharger les certificats sur Azure lorsque vous créez et déployez le service cloud. Pour plus d'informations sur les certificats Azure, consultez la page [Gérer les certificats][Gérer les certificats].

-   Si vous prévoyez de déployer le service cloud dans un groupe d'affinités, créez le groupe d'affinités. Vous pouvez utiliser un groupe d'affinités pour déployer votre service cloud et d'autres services Azure dans le même emplacement dans une région. Vous pouvez créer le groupe d'affinités dans la zone **Networks** du portail de gestion, sur la page **Affinity Groups**. Pour plus d'informations consultez la page [Créer un groupe d'affinités dans le portail de gestion][Créer un groupe d'affinités dans le portail de gestion].

## <span id="quick"></span></a> Création d'un service cloud avec Création rapide

1.  Dans le [portail de gestion][portail de gestion], cliquez sur **Nouveau**\>**Calculer**\>**Service de cloud computing**\>**Création rapide**.

    ![CloudServices\_QuickCreate][CloudServices\_QuickCreate]

2.  Dans **URL**, entrez un nom de sous-domaine à utiliser dans l'URL publique pour accéder au service cloud dans les déploiements de production. Le format de l'URL des déploiements de production est le suivant : http://*monURL*.cloudapp.net.

3.  Dans **Région ou groupe d'affinités**, sélectionnez la zone géographique ou le groupe d'affinités où déployer le service cloud. Sélectionnez un groupe d'affinités si vous voulez déployer votre service cloud dans le même emplacement que d'autres services Azure dans une région.

4.  Cliquez sur **Create Cloud Service**.

    ![CloudServices\_Region][CloudServices\_Region]

    Vous pouvez surveiller l'état du processus dans la zone de message en bas de la fenêtre.

    La zone **Cloud Services** s'affiche, avec le nouveau service cloud. Lorsque l'état passe à Created, la création du service cloud s'est effectuée correctement.

    ![CloudServices\_CloudServicesPage][CloudServices\_CloudServicesPage]

## <span id="uploadcertificate"></span></a> Téléchargement d'un certificat pour un service cloud

1.  Dans le [portail de gestion][portail de gestion], cliquez sur **Cloud Services**, cliquez sur le nom du service cloud, puis cliquez sur **Certificats**.

    ![CloudServices\_QuickCreate][1]

2.  Cliquez sur **Télécharger un certificat** ou sur **Télécharger**.

3.  Dans **Fichier**, utilisez **Parcourir** pour sélectionner le certificat (fichier .pfx).

4.  Dans **Password**, entrez la clé privée du certificat.

5.  Cliquez sur **OK** (coche).

    ![CloudServices\_AddaCertificate][CloudServices\_AddaCertificate]

    Vous pouvez consulter l'état du téléchargement dans la zone de message, comme illustré ci-dessous. Une fois le téléchargement terminé, le certificat est ajouté à la table. Dans la zone de message, cliquez sur OK pour fermer le message.

    ![CloudServices\_CertificateProgress][CloudServices\_CertificateProgress]

## <span id="deploy"></span></a> Déploiement d'un service cloud

1.  Dans le [portail de gestion][portail de gestion], cliquez sur **Cloud Services**, cliquez sur le nom du service cloud, puis cliquez sur **Tableau de bord**.

    Le tableau de bord s'ouvre dans l'environnement de production. Vous pouvez alors sélectionner Intermédiaire pour déployer votre application dans l'environnement intermédiaire. Pour plus d'informations, consultez la page [Gestion des déploiements dans Azure][Gestion des déploiements dans Azure].

2.  Cliquez sur **Télécharger un nouveau déploiement de production** ou sur **Télécharger**.

3.  Dans **Étiquette du déploiement**, entrez le nom du nouveau déploiement, par exemple MyCloudServicev4.

4.  Dans **Package**, utilisez **Browse** pour sélectionner le fichier de package du service (.cspkg) à utiliser.

5.  Dans **Configuration**, utilisez **Browse** pour sélectionner le fichier de configuration du service (.cscfg) à utiliser.

6.  Si le service cloud doit inclure des rôles avec une seule instance, activez la case à cocher **Deploy even if one or more roles contain a single instance** afin de permettre au déploiement de continuer.

Azure ne peut garantir un pourcentage d'accès au service cloud que de 99,95 % pendant la maintenance et la mise à jour du service si tous les rôles disposent d'au moins deux instances Si besoin, vous pouvez ajouter des instances de rôle supplémentaires dans la page **Mettre à l'échelle** une fois le service cloud déployé. Pour plus d'informations, consultez la page [Contrats de niveau de service][Contrats de niveau de service].

1.  Cliquez sur **OK** (coche) pour lancer le déploiement du service cloud.

    ![CloudServices\_UploadaPackage][CloudServices\_UploadaPackage]

    Vous pouvez surveiller l'état du déploiement dans la zone de message. Cliquez sur OK pour masquer le message.

    ![CloudServices\_UploadProgress][CloudServices\_UploadProgress]

### Pour vérifier la réussite du déploiement

1.  Cliquez sur **Tableau de bord**.

    L'état présente le service comme étant **En cours d'exécution**.

2.  Sous **Aperçu rapide**, cliquez sur l'URL du site pour ouvrir le service cloud dans un navigateur web.

<!-- -->

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)

  [Livraison continue sur Azure au moyen de Visual Studio Online]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409
  [Concepts]: #concepts
  [Préparation de votre application]: #prepare
  [Avant de commencer]: #begin
  [Création d'un service cloud avec Quick Create]: #quick
  [Téléchargement d'un certificat pour un service cloud]: #uploadcertificate
  [Déploiement d'un service cloud]: #deploy
  [téléchargements Azure]: http://www.windowsazure.com/fr-fr/develop/downloads/
  [Configuration d'un certificat SSL sur un point de terminaison HTTPS]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ff795779.aspx
  [Configuration d'une connexion Bureau à distance pour un rôle dans Azure]: http://msdn.microsoft.com/fr-fr/library/hh124107.aspx
  [Activation des diagnostics dans Azure]: http://www.windowsazure.com/fr-fr/develop/net/common-tasks/diagnostics/
  [Configurer un service cloud pour Azure]: http://msdn.microsoft.com/fr-fr/library/hh124108.aspx
  [Création d'un package d'application Microsoft Azure]: http://msdn.microsoft.com/fr-fr/library/hh403979.aspx
  [Publication d'un service cloud en utilisant les outils Azure]: http://msdn.microsoft.com/fr-fr/library/ff683672.aspx
  [Gérer les certificats]: http://msdn.microsoft.com/fr-fr/library/gg981929.aspx
  [Créer un groupe d'affinités dans le portail de gestion]: http://msdn.microsoft.com/fr-fr/library/jj156209.aspx
  [portail de gestion]: http://manage.windowsazure.com/
  [CloudServices\_QuickCreate]: ./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png
  [CloudServices\_Region]: ./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png
  [CloudServices\_CloudServicesPage]: ./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png
  [1]: ./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png
  [CloudServices\_AddaCertificate]: ./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png
  [CloudServices\_CertificateProgress]: ./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png
  [Gestion des déploiements dans Azure]: http://msdn.microsoft.com/fr-fr/library/gg433027.aspx
  [Contrats de niveau de service]: http://www.windowsazure.com/fr-fr/support/legal/sla/
  [CloudServices\_UploadaPackage]: ./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png
  [CloudServices\_UploadProgress]: ./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png
