<properties 
	pageTitle="Création et déploiement d'un service cloud - Azure" 
	description="Découvrez comment créer et déployer un service cloud à l'aide de la méthode Création rapide dans Azure." 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2014" 
	ms.author="adegeo"/>





#Création et déploiement d'un service cloud

Le portail de gestion Azure vous offre deux manières de créer et de déployer un service cloud : **Création rapide** et **Création personnalisée**. 

Cette rubrique explique comment utiliser la méthode Création rapide pour créer un service cloud et comment utiliser ensuite **Télécharger** pour charger et déployer un package de service cloud dans Azure. Lorsque vous utilisez cette méthode, le portail de gestion Azure met à votre disposition tous les liens nécessaires pour compléter les éléments au fur et à mesure. Si vous êtes prêt à déployer votre service cloud lorsque vous le créez, vous pouvez effectuer ces deux opérations en même temps à l'aide de **Création personnalisée**. 

**Remarque**   Si vous prévoyez de publier votre service cloud depuis Windows Team Foundation Services (TFS), utilisez Création rapide, puis configurez la publication TFS dans **Création rapide** ou dans le tableau de bord. Pour plus d'informations, consultez la page [Livraison continue sur Azure au moyen de Visual Studio Online][TFSTutorialForCloudService] ou consultez l'aide sur la page de **Création rapide**.

##Sommaire##

* [Concepts](#concepts)
* [Préparation de votre application](#prepare)
* [Avant de commencer](#begin)
* [Création Création d'un service cloud avec Quick Create](#quick)
* [Procédure : téléchargement d'un certificat pour un service cloud](#uploadcertificate)
* [Procédure : déploiement d'un service cloud](#deploy)


<h2><a id="concepts"></a>Concepts</h2>
Trois composants sont nécessaires pour déployer une application en tant que service cloud dans Azure :

>- **fichier de définition de service (.csdef)**   celui-ci définit le modèle du service, avec le nombre de rôles.

>- **fichier de configuration de service (.cscfg)**   celui-ci contient des paramètres de configuration pour le service cloud et les différents rôles, y compris le nombre d'instances de rôle.

>- **package de service (.cspkg)**   celui-ci contient le code de l'application et le fichier de définition de service.

<h2><a id="prepare"></a>Préparation de votre application</h2>
Avant de déployer un service cloud, vous devez créer le package de service cloud (.cspkg) à partir du code de l'application, ainsi que le fichier de configuration de service cloud (.cscfg). Chaque package de service cloud contient des fichiers d'application et des configurations. Le fichier de configuration de service contient les paramètres de configuration.

Le Kit de développement logiciel (SDK) Azure fournit les outils nécessaires à la préparation des fichiers de déploiement. Vous pouvez installer le Kit de développement logiciel (SDK) depuis la page des [téléchargements Azure](http://azure.microsoft.com/develop/downloads/), dans le langage souhaité pour le développement de votre code.

Trois fonctions du service cloud nécessitent une configuration spécifique avant d'exporter le package de service :

- Si vous souhaitez déployer un service cloud qui utilise le chiffrement de données SSL (Secure Sockets Layer), configurez votre application pour SSL. Pour plus d'informations, consultez la page [Configuration d'un certificat SSL sur un point de terminaison HTTPS](http://msdn.microsoft.com/library/windowsazure/ff795779.aspx).

- Si vous voulez configurer les connexions Bureau à distance aux instances de rôle, configurez les rôles pour le Bureau à distance. Pour plus d'informations sur la préparation du fichier de définition du service pour l'accès à distance, consultez la page [Configuration d'une connexion Bureau à distance pour un rôle dans Azure](http://msdn.microsoft.com/library/hh124107.aspx).

- Si vous voulez configurer la surveillance détaillée pour votre service cloud, activez Azure Diagnostics pour le service cloud. *Minimal monitoring* (niveau de surveillance par défaut) utilise des compteurs de performances récupérés sur le système d'exploitation hôte des instances de rôle (machine virtuelle). La surveillance détaillée recueille des mesures supplémentaires sur les données de performances dans les instances de rôle, afin de permettre une analyse plus fine des problèmes qui surviennent au cours du traitement de l'application. Pour savoir comment activer Azure Diagnostics, consultez la page [Activation des diagnostics dans Azure](http://azure.microsoft.com/develop/net/common-tasks/diagnostics/).

- Pour créer un service cloud avec des déploiements de rôles Web ou de rôles de travail, vous devez créer le package de service. Plus d'informations sur les fichiers du package, consultez la page [Configurer un service cloud pour Azure](http://msdn.microsoft.com/library/hh124108.aspx). Pour créer le package, consultez la page [Création d'un package d'application Microsoft Azure](http://msdn.microsoft.com/library/hh403979.aspx). Si vous utilisez Visual Studio pour développer votre application, consultez la page [Publication d'un service cloud en utilisant les outils Azure](http://msdn.microsoft.com/library/ff683672.aspx).

<h2><a id="begin"></a>Avant de commencer</h2>

- Si vous n'avez pas installé le Kit de développement logiciel (SDK), cliquez sur **Installer Azure SDK** pour ouvrir la [page des téléchargements Azure](http://azure.microsoft.com/develop/downloads/), puis téléchargez le Kit de développement logiciel dans le langage souhaité pour le développement de votre code. (Vous pourrez le faire ultérieurement.)

- Si des instances de rôle nécessitent des certificats, créez ces certificats. Les services cloud requièrent un fichier .pfx avec une clé privée. Vous pouvez télécharger les certificats sur Azure lorsque vous créez et déployez le service cloud. Pour plus d'informations sur les certificats Azure, consultez la page [Gérer les certificats](http://msdn.microsoft.com/library/gg981929.aspx).

- Si vous prévoyez de déployer le service cloud dans un groupe d'affinités, créez le groupe d'affinités. Vous pouvez utiliser un groupe d'affinités pour déployer votre service cloud et d'autres services Azure dans le même emplacement dans une région. Vous pouvez créer le groupe d'affinités dans la zone **Réseaux** du portail de gestion, sur la page **Groupes d'affinités**. Pour plus d'informations consultez la page [Créer un groupe d'affinités dans le portail de gestion](http://msdn.microsoft.com/library/jj156209.aspx).


<h2><a id="quick"></a>Procédure : création d'un service cloud avec Création rapide</h2>

1. Dans le [portail de gestion](http://manage.windowsazure.com/), cliquez sur **Nouveau**>**Calculer**>**Service de cloud computing**>**Création rapide**.

	![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2. Dans **URL**, entrez un nom de sous-domaine à utiliser dans l'URL publique pour accéder au service cloud dans les déploiements de production. Le format de l'URL des déploiements de production est le suivant :: http://*myURL*.cloudapp.net.

3. Dans **Région ou groupe d'affinités**, sélectionnez la zone géographique ou le groupe d'affinités où déployer le service cloud. Sélectionnez un groupe d'affinités si vous voulez déployer votre service cloud dans le même emplacement que d'autres services Azure dans une région.

4. Cliquez sur **Créer un service de cloud computing**.

	![CloudServices_Region](./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png)

	Vous pouvez surveiller l'état du processus dans la zone de message en bas de la fenêtre.

	La zone **Cloud Services** s'affiche, avec le nouveau service cloud. Lorsque l'état passe à Created, la création du service cloud s'est effectuée correctement.

	![CloudServices_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)


<h2><a id="uploadcertificate"></a>Procédure : téléchargement d'un certificat pour un service cloud</h2>

1. Dans le [portail de gestion](http://manage.windowsazure.com/), cliquez sur **Cloud Services**, cliquez sur le nom du service cloud, puis cliquez sur **Certificats**.

	![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)


2. Cliquez sur **Télécharger un certificat** ou sur **Télécharger**.

3. Dans **Fichier**, utilisez **Parcourir** pour sélectionner le certificat (fichier .pfx).

4. Dans **Mot de passe**, entrez la clé privée du certificat.

5. Cliquez sur **OK** (coche).

	![CloudServices_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

	Vous pouvez consulter l'état du téléchargement dans la zone de message, comme illustré ci-dessous. Une fois le téléchargement terminé, le certificat est ajouté à la table. Dans la zone de message, cliquez sur OK pour fermer le message.

	![CloudServices_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

<h2><a id="deploy"></a>Procédure : déploiement d'un service cloud</h2>

1. Dans le [portail de gestion](http://manage.windowsazure.com/), cliquez sur **Cloud Services**, cliquez sur le nom du service cloud, puis cliquez sur **Tableau de bord**.

	Le tableau de bord s'ouvre dans l'environnement de production. Vous pouvez alors sélectionner Intermédiaire pour déployer votre application dans l'environnement intermédiaire. Pour plus d'informations, consultez la page [Gestion des déploiements dans Azure](http://msdn.microsoft.com/library/gg433027.aspx).

	 
2. Cliquez sur **Télécharger un nouveau déploiement de production** ou sur **Télécharger**.

3. Dans **Étiquette du déploiement**, entrez le nom du nouveau déploiement, par exemple MyCloudServicev4.

3. Dans **Package**, utilisez **Parcourir** pour sélectionner le fichier de package du service (.cspkg) à utiliser.

4. Dans **Configuration**, utilisez **Parcourir** pour sélectionner le fichier de configuration du service (.cscfg) à utiliser.

5. Si le service cloud doit inclure des rôles avec une seule instance, activez la case à cocher **Déployez même si un ou plusieurs rôles contiennent une seule instance** afin de permettre au déploiement de continuer.

 Azure ne peut garantir un pourcentage d'accès au service cloud que de 99,95 % pendant la maintenance et la mise à jour du service si tous les rôles disposent d'au moins deux instances Si besoin, vous pouvez ajouter des instances de rôle supplémentaires dans la page **Mettre à l'échelle** une fois le service cloud déployé. Pour plus d'informations, consultez [Contrats de niveau de service](http://azure.microsoft.com/support/legal/sla/).

6. Cliquez sur **OK** (coche) pour lancer le déploiement du service cloud.

	![CloudServices_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)
 

	Vous pouvez surveiller l'état du déploiement dans la zone de message. Cliquez sur OK pour masquer le message.

	![CloudServices_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

###Pour vérifier la réussite du déploiement###

1. Cliquez sur **Tableau de bord**.

	L'état présente le service comme étant **En cours d'exécution**.

2. Sous **Aperçu rapide**, cliquez sur l'URL du site pour ouvrir le service cloud dans un navigateur Web.

[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409

	![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)


<!--HONumber=45--> 
