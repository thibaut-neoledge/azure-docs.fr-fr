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
	ms.topic="hero-article"
	ms.date="06/30/2015"
	ms.author="adegeo"/>




# Création et déploiement d'un service cloud

> [AZURE.SELECTOR]
- [Azure Portal](cloud-services-how-to-create-deploy.md)
- [Azure Preview Portal](cloud-services-how-to-create-deploy-portal.md)

Le portail Azure vous permet de créer et de déployer un service cloud de deux manières : **Création rapide** et **Création personnalisée**.

Cette rubrique explique comment utiliser la méthode Quick Create pour créer un service cloud et comment utiliser ensuite **Upload** pour télécharger et déployer un package de service cloud dans Azure. Si vous utilisez cette méthode, le portail Azure met à votre disposition tous les liens nécessaires pour remplir les conditions requises au fur et à mesure. Si vous êtes prêt à déployer votre service cloud lorsque vous le créez, vous pouvez effectuer ces deux opérations en même temps à l'aide de **Custom Create**.

> [AZURE.NOTE]Si vous prévoyez de publier votre service cloud depuis Visual Studio Online (VSO), utilisez Création rapide, puis configurez la publication VSO dans **Création rapide** ou dans le tableau de bord. Pour plus d'informations, consultez la page [Livraison continue sur Azure au moyen de Visual Studio Online][TFSTutorialForCloudService] ou **Quick Start**.

## Concepts
trois composants sont nécessaires pour déployer une application en tant que service cloud dans Azure :

- **Définition de service** Le fichier de définition de service cloud (.csdef) définit le modèle de service, notamment le nombre de rôles.

- **Configuration de service** Le fichier de configuration de service cloud (.cscfg) contient les paramètres de configuration du service cloud et des différents rôles, notamment le nombre d’instances de rôle.

- **Package de service** Le package de service (.cspkg) contient le code d’application, les configurations et le fichier de définition de service.

Pour plus d’informations sur ces composants et sur la création d’un package, cliquez [ici](cloud-services-model-and-package.md).

## Préparation de votre application
Avant de déployer un service cloud, vous devez créer le package de service cloud (.cspkg) à partir du code de l'application, ainsi que le fichier de configuration de service cloud (.cscfg). Le Kit de développement logiciel (SDK) Azure fournit les outils nécessaires à la préparation des fichiers de déploiement. Vous pouvez installer le Kit de développement logiciel (SDK) depuis la page des [téléchargements Azure](http://azure.microsoft.com/downloads/), dans le langage souhaité pour le développement de votre code.

Trois fonctions du service cloud nécessitent une configuration spécifique avant d'exporter le package de service :

- Si vous souhaitez déployer un service cloud qui utilise le chiffrement de données SSL (Secure Sockets Layer), configurez votre application pour SSL. Pour plus d'informations, consultez la page [Configuration d'un certificat SSL sur un point de terminaison HTTPS](http://msdn.microsoft.com/library/azure/ff795779.aspx).

- Si vous voulez configurer les connexions Bureau à distance aux instances de rôle, configurez les rôles pour le Bureau à distance. Pour plus d'informations sur la préparation du fichier de définition du service pour l'accès à distance, consultez la page [Configuration d'une connexion Bureau à distance pour un rôle dans Azure](http://msdn.microsoft.com/library/hh124107.aspx).

- Si vous voulez configurer la surveillance détaillée pour votre service cloud, activez Azure Diagnostics pour le service cloud. *Surveillance minimale* (niveau de surveillance par défaut) utilise des compteurs de performances récupérés sur le système d'exploitation hôte des instances de rôle (machine virtuelle). La surveillance détaillée recueille des mesures supplémentaires sur les données de performances dans les instances de rôle, afin de permettre une analyse plus fine des problèmes qui surviennent au cours du traitement de l'application. Pour savoir comment activer Azure Diagnostics, consultez la page [Activation des diagnostics dans Azure](cloud-services-dotnet-diagnostics.md).

- Pour créer un service cloud avec des déploiements de rôles web ou de rôles de travail, vous devez créer le package de service. Plus d'informations sur les fichiers du package, consultez la page [Configurer un service cloud pour Azure](http://msdn.microsoft.com/library/hh124108.aspx). Pour créer le fichier de package, consultez la page [Création d’un package d’application Azure](http://msdn.microsoft.com/library/hh403979.aspx). Si vous utilisez Visual Studio pour développer votre application, consultez la page [Publication d'un service cloud en utilisant les outils Azure](http://msdn.microsoft.com/library/ff683672.aspx).

## Avant de commencer

- Si vous n'avez pas installé le Kit de développement logiciel (SDK), cliquez sur **Install Azure SDK** pour ouvrir la page des [téléchargements Azure](http://azure.microsoft.com/downloads/), puis téléchargez le Kit de développement logiciel dans le langage souhaité pour le développement de votre code. (Vous pourrez le faire ultérieurement.)

- Si des instances de rôle nécessitent des certificats, créez ces certificats. Les services cloud requièrent un fichier .pfx avec une clé privée. Vous pouvez télécharger les certificats sur Azure lorsque vous créez et déployez le service cloud. Pour plus d'informations sur les certificats Azure, consultez la page [Gérer les certificats](http://msdn.microsoft.com/library/gg981929.aspx).

- Si vous prévoyez de déployer le service cloud dans un groupe d'affinités, créez le groupe d'affinités. Vous pouvez utiliser un groupe d'affinités pour déployer votre service cloud et d'autres services Azure dans le même emplacement dans une région. Vous pouvez créer le groupe d'affinités dans la zone **Networks** du portail de gestion, sur la page **Affinity Groups**. Pour plus d'informations consultez la page [Créer un groupe d'affinités dans le portail de gestion](http://msdn.microsoft.com/library/jj156209.aspx).


## Étape 3 : création d’un service cloud et chargement du package de déploiement

1. Connectez-vous au [portail Azure en version préliminaire][]. 
2. Cliquez sur **Nouveau** et sur **Compute**, faites défiler la page vers le bas, puis cliquez sur **Service cloud**.

    ![Publier votre service cloud](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)

3. Dans le nouveau panneau **Service cloud**, entrez une valeur pour le **nom DNS**.
4. Créez un **groupe de ressources** ou sélectionnez-en un.
5. Sélectionnez un **emplacement**.
6. Sélectionnez **Package**, puis, dans le panneau **Télécharger un package**, renseignez les champs obligatoires.  
      
     Si l’un de vos rôles contient une seule et même instance, assurez-vous que la case **Déployer même si un ou plusieurs rôles ne contiennent qu’une seule et même instance** est cochée.

7. Vérifiez que la case **Démarrer le déploiement** est *cochée*.
8. Cliquez sur **OK**. 

    ![Publier votre service cloud](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## Téléchargement d'un certificat

Si votre package de déploiement a été [configuré pour utiliser des certificats](cloud-services-configure-ssl-certificate-portal.md#modify), vous pouvez charger le certificat maintenant.

9. Sélectionnez **Certificats**, puis, dans le panneau **Ajouter des certificats**, sélectionnez le fichier .pfx du certificat SSL et indiquez le **Mot de passe** pour le certificat. 
10. Cliquez sur **Joindre un certificat**, puis sur **OK** dans le panneau **Ajouter des certificats**.
11. Cliquez sur **Créer** dans le panneau **Service cloud**. Lorsque le déploiement atteint l'état **Ready**, vous pouvez passer aux étapes suivantes.

    ![Publier votre service cloud](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)


## Vérifier la réussite du déploiement

1. Cliquez sur l’instance de service cloud.

	L'état présente le service comme étant **En cours d'exécution**.

2. Sous **Bases**, cliquez sur l’**URL du site** pour ouvrir le service cloud dans un navigateur web.

    ![CloudServices\_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)


[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409
 

<!---HONumber=August15_HO6-->