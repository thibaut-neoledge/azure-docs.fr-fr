<properties linkid="manage-services-how-to-use-appdynamics" urlDisplayName="Monitor with AppDynamics" pageTitle="How to use AppDynamics with Azure" metaKeywords="" description="Learn how to use AppDynamics for Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How To Use AppDynamics for Azure" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi"></tags>

# Utilisation d'AppDynamics pour Azure

Cette rubrique montre comment prendre en main AppDynamics pour Azure.

## Sommaire

-   [Présentation d'AppDynamics](#what)
-   [Conditions préalables](#prereq)
-   [Inscription pour l'obtention d'un compte AppDynamics](#register)
-   [Téléchargement de l'agent .NET à partir d'AppDynamics](#download)
-   [Ajout de l'agent .NET aux rôles Azure et modification du démarrage](#addagent)
-   [Publication de l'application instrumentée avec AppDynamics sur Azure](#publish)
-   [Surveillance de votre application](#monitor)

## <span id="what"></span></a>Présentation d'AppDynamics

AppDynamics est une solution de surveillance des performances des applications qui vous permet d'effectuer les actions suivantes :

-   identifier des problèmes, tels que des demandes d'utilisateur lentes et figées et les erreurs, dans un environnement de production ;

-   résoudre et dépanner la cause racine de ces problèmes.

AppDynamics contient deux composants :

-   Agent de serveur d'applications : l'agent .NET de serveur d'applications collecte des données à partir de vos serveurs. Vous pouvez exécuter un agent distinct sur chaque instance de rôle que vous souhaitez surveiller. Vous pouvez télécharger l'agent à partir du portail AppDynamics.

-   Contrôleur AppDynamics : l'agent envoie ses informations à un service hébergé sur le contrôleur AppDynamics sur Azure. À l'aide d'une console basée sur navigateur web, connectez-vous au contrôleur pour surveiller, analyser et dépanner votre application.

    ![Schéma AppDynamics](./media/cloud-services-how-to-appdynamics/addiagram.png)

## <span id="prereq"></span></a>Conditions préalables

-   Visual Studio 2010 ou ultérieur
-   Une solution Visual Studio à surveiller
-   Kit de développement logiciel (SDK) Azure
-   Compte Azure

## <span id="register"></span></a>Inscription pour l'obtention d'un compte AppDynamics

Pour vous inscrire à AppDynamics et obtenir un compte Azure :

1.  Cliquez sur **Try Free** ou **S'inscrire** pour AppDynamics sur Azure Marketplace à l'adresse [https://datamarket.azure.com/browse/Applications](https://datamarket.azure.com/browse/Applications).

    Si vous choisissez **S'inscrire**, vous recevrez une version gratuite d'AppDynamics Pro for Azure avec des fonctionnalités complètes. Après 30 jours, elle deviendra une version gratuite d'AppDynamics Lite pour Azure offrant des fonctionnalités limitées. Les informations de votre carte de crédit ne vous seront pas demandées pour cette option. Vous pouvez effectuer une mise à niveau vers AppDynamics Pro pour Azure à tout moment.

    Si vous choisissez l'option **Try Free**, vous recevrez une version gratuite d'AppDynamics Pro pour Azure offrant des fonctionnalités complètes. Les informations de votre carte de crédit vous seront demandées pour cette option. Après 30 jours, votre compte client sera débité pour permettre une utilisation continue d'AppDynamics Pro pour Azure, sauf si vous annulez votre abonnement.

    Vous devez avoir une licence d'agent pour chaque instance de rôle que vous souhaitez surveiller. Par exemple, un site exécutant 2 instances de rôle web et 2 instances de rôle de travail nécessite 4 licences d'agent.

2.  Sur la page d'inscription, indiquez vos informations d'utilisateur, un mot de passe, une adresse électronique, un nom de société et le nom de l'application que vous surveillerez pendant sa publication avec Azure.

3.  Cliquez sur **Register Now**.

    Vous recevrez vos données d'identification AppDynamics et l'URL de contrôleur AppDynamics (hôte et port) attribués à votre compte par courrier électronique, à l'adresse que vous avez indiquée sur la page d'enregistrement. Enregistrez ces informations.

    Si vous possédez déjà des données d'identification AppDynamics provenant d'un autre produit, vous pouvez vous connecter grâce à elles.

    Vous recevrez également une page d'accueil pour le compte AppDynamics.

    Vous accéderez à votre page d'accueil de compte AppDynamics.

    Votre page d'accueil de compte AppDynamics comprend les éléments suivants :

    -   URL de contrôleur : pour vous connecter à votre compte sur le service hébergé par le contrôleur AppDynamics

    -   Données d'identification AppDynamics : nom de compte et clé d'accès

    -   Lien vers le site de téléchargement AppDynamics : pour télécharger l'agent .NET AppDynamics

    -   Nombre de jours restants pour votre essai Pro

    -   Liens vers les vidéos et la documentation d'intégration AppDynamics

    Pour accéder à tout moment à la page d'accueil de votre compte AppDynamics, entrez son URL dans un navigateur web et connectez-vous grâce à vos données d'identification AppDynamics.

## <span id="download"></span></a>Téléchargement de l'agent .NET à partir d'AppDynamics

1.  Accédez au site de téléchargement AppDynamics. L'URL se trouve dans votre courrier électronique de bienvenue et sur la page d'accueil de votre compte AppDynamics.

2.  Connectez-vous avec votre nom de compte et votre clé d'accès AppDynamics.

3.  Téléchargez le fichier nommé AppDynamicsdotNetAgentSetup64.msi. Ne l'exécutez pas.

## <span id="addagent"></span></a>Ajout de l'agent .NET aux rôles Azure et modification du démarrage

Cette étape met en œuvre les rôles dans votre solution Visual Studio pour la surveillance par AppDynamics. Aucune procédure d'installation traditionnelle de type Assistant Windows n'est requise pour utiliser AppDynamics pour Azure.

1.  Créez un projet Azure dans Visual Studio ou ouvrez un projet Azure existant.

2.  Si vous avez créé un projet, ajoutez les projets de rôle web et/ou de travail à la solution.

3.  Pour chaque projet de rôle web et de travail que vous souhaitez surveiller, ajoutez le fichier .msi de l'agent .NET téléchargé.

    Notez que bien que chaque *projet de rôle* soit lié à un seul fichier .msi d'agent .NET, chaque *instance de rôle* du projet requiert une licence d'agent .NET distincte.

4.  Pour chaque projet de rôle web et de travail que vous souhaitez surveiller, ajoutez un fichier texte nommé startup.cmd et collez-y les lignes ci-dessous :

        if defined COR_PROFILER GOTO END 
        SETLOCAL EnableExtensions 
        REM Run the agent installer 
        AppDynamicsdotNetAgentSetup64.msi AD_Agent_Environment=Azure AD_Agent_ControllerHost=%1 AD_Agent_ControllerPort=%2 AD_Agent_AccountName=%3 AD_Agent_AccessKey=%4 AD_Agent_ControllerApplication=%5 /quiet /log d:\adInstall.log  
        SHUTDOWN /r /c "Rebooting the instance after the installation of AppDynamics Monitoring Agent" /t 0 
        GOTO END   
        :END

5.  Pour chaque rôle web et de travail que vous souhaitez surveiller, définissez la propriété **Copier dans le répertoire de sortie** pour le fichier .msi de l'agent AppDynamics et pour le fichier startup.cmd sur **Toujours copier**.

    ![Toujours copier](./media/cloud-services-how-to-appdynamics/adcopyalways.png)

6.  Dans le fichier ServiceDefinition.csdef pour le projet Azure, ajoutez un élément Startup Task qui appelle startup.cmd avec les paramètres de chaque élément WorkerRole et WebRole.

    Ajoutez les lignes suivantes :

        <Startup>
        <Task commandLine="startup.cmd [your_controller_host] [your_controller_port] [your_account_name] [your_access_key] [your_application_name]" executionContext="elevated" taskType="simple"/>
        </Startup>

    où :

    -   *your controller host* et *your controller port* sont l'hôte et le port contrôleur attribués à votre compte, et *your account name* et *your access key* sont les données d'identification qui vous sont attribuées par AppDynamics. Ces informations sont fournies dans le courrier électronique envoyé lorsque vous vous enregistrez auprès d'AppDynamics, mais aussi sur votre page d'accueil AppDynamics. Consultez la rubrique [Inscription pour l'obtention d'un compte AppDynamics](#register).

    -   *your application name* est le nom que vous choisissez pour l'application. Ce nom identifiera l'application dans l'interface AppDynamics Controller.

    Votre fichier ServiceDefinition.csdef ressemblera à ceci :

    ![Définition de service](./media/cloud-services-how-to-appdynamics/adscreen.png)

## <a name="publish"></a>Publication de l'application instrumentée avec AppDynamics sur Azure

Pour chaque rôle instrumenté par AppDynamics :

1.  Dans Visual Studio, sélectionnez le projet de rôle.

2.  Sélectionnez Publish to Azure.

## <a name="monitor"></a>Surveillance de votre application

1.  Connectez-vous au contrôleur AppDynamics avec l'URL indiquée dans votre courrier électronique de bienvenue et sur la page d'accueil de votre compte AppDynamics.

2.  Envoyez des requêtes à votre application afin de créer du trafic à surveiller et attendez quelques minutes.

3.  Dans le contrôleur AppDynamics, sélectionnez votre application.

4.  Surveillez votre application.

## <a name="learn"></a>En savoir plus

Consultez la page d'accueil de votre compte AppDynamics pour obtenir des liens vers de la documentation et des vidéos.

Les mises à jour les plus récentes pour ce document sont disponibles dans sa version wiki à l'adresse [http://docs.appdynamics.com/display/ADAZ/How+To+Use+AppDynamics+for+Windows+Azure](http://docs.appdynamics.com/display/ADAZ/How+To+Use+AppDynamics+for+Windows+Azure).


