<properties 
	pageTitle="Utilisation de Access Control (Java) - Guide des fonctionnalités Azure" 
	description="Apprenez à développer et à utiliser le contrôle d'accès avec Java dans Azure." 
	services="active-directory" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="robmcm"/>

# Authentification des utilisateurs web auprès d'Azure Access Control Service à l'aide d'Eclipse

Ce guide vous indique comment utiliser Azure Access Control Service (ou ACS) au sein du plug-in Azure pour Eclipse avec Java (par Microsoft Open Technologies). Pour plus d'informations sur ACS, consultez la rubrique [Étapes suivantes](#next_steps) .

> [AZURE.NOTE]
> Le filtre ACS Azure (par Microsoft Open Technologies) est une version préliminaire de la technologie destinée à la communauté. En tant que logiciel préliminaire, il n'est pas officiellement pris en charge par Microsoft Open Technologies, Inc. ou Microsoft.

## Sommaire

-   [Qu'est-ce qu'ACS ?][]
-   [Concepts][]
-   [Conditions préalables][]
-   [Création d'un espace de noms ACS][]
-   [Ajout de fournisseurs d'identité][]
-   [Ajout d'une application par partie de confiance][]
-   [Création de règles][]
-   [Téléchargement d'un certificat vers votre espace de noms ACS][]
-   [Vérification de la page d'intégration d'application][]
-   [Création d'une application web Java][]
-   [Ajout de la bibliothèque de filtres ACS à votre application][]
-   [Déploiement de l'émulateur de calcul][]
-   [Déploiement sur Azure][]
-   [Étapes suivantes][]

## <a name="what-is"></a>Qu'est-ce qu'ACS ?

La plupart des développeurs ne sont pas des experts en matière d'identité et ne souhaitent généralement pas passer du temps à développer des mécanismes d'authentification et d'autorisation pour leurs applications et services. ACS est un service Azure qui vous permet d'authentifier rapidement les utilisateurs lorsqu'ils accèdent à vos applications et services Web sans ajouter de logique d'authentification complexe à votre code.

Les fonctionnalités suivantes sont disponibles dans ACS :

-   Intégration avec Windows Identity Foundation (WIF).
-   Prise en charge des fournisseurs d'identité web reconnus, notamment Windows Live ID, Google, Yahoo! et Facebook.
-   Prise en charge d'Active Directory Federation Services (AD FS) 2.0.
-   Service de gestion OData (Open Data Protocol) qui fournit un accès par programme aux paramètres ACS.
-   Portail de gestion qui autorise l'accès administratif aux paramètres ACS.

Pour plus d'informations sur ACS, consultez la page [Access Control Service 2.0][].

## <a name="concepts"></a>Concepts

Azure ACS repose sur les principes de l'identité basée sur des demandes, une approche cohérente permettant de créer des mécanismes d'authentification pour les applications exécutées localement ou dans le cloud. Cette approche permet aux applications et aux services d'obtenir les informations d'identité dont ils ont besoin pour les utilisateurs internes ou externes à leur organisation et sur Internet.

Pour réaliser les tâches présentées dans ce guide, vous devez comprendre les éléments suivants :

**Client** - Dans le contexte de ce guide, il s'agit d'un navigateur qui tente d'accéder à votre application Web.

**Application par partie de confiance** - Il s'agit d'un site Web ou d'un service qui sous-traite l'authentification à une autorité externe. Dans le jargon technique, nous disons que la partie de confiance fait confiance à cette autorité. Ce guide décrit la configuration de votre application de manière à ce qu'elle approuve ACS.

**Jeton** - Un jeton est un ensemble de données de sécurité généralement émis après l'authentification d'un utilisateur. Il contient un jeu de  *demandes* qui sont les attributs de l'utilisateur authentifié. Une demande peut représenter le nom d'un utilisateur, son âge, l'identifiant pour un rôle qui lui est attribué, etc. Un jeton bénéficie le plus souvent d'une signature numérique, ce qui permet de remonter jusqu'à son émetteur et de le protéger contre toute tentative de modification. Un utilisateur obtient l'accès à une application par partie de confiance en présentant un jeton valide émis par une autorité approuvée par celle-ci.

**Fournisseur d'identité** - Un fournisseur d'identité est une autorité qui authentifie des identités d'utilisateur et émet des jetons de sécurité. L'activité d'émission des jetons est implémentée via un service spécial nommé " service d'émission de jeton de sécurité " (STS, Security Token Service). En voici quelques exemples classiques : Windows Live ID, Facebook, référentiels d'utilisateurs professionnels (tels qu'Active Directory) et ainsi de suite. Lorsqu'ACS est configuré de manière à approuver un fournisseur d'identité, le système accepte et valide les jetons émis par ce fournisseur. ACS peut approuver plusieurs fournisseurs d'identité simultanément. Donc lorsque votre application approuve ACS, vous pouvez fournir aux utilisateurs la possibilité d'être authentifiés par un fournisseur d'identité qu'ACS approuve en votre nom.

**Fournisseur de fédération** - Les fournisseurs d'identité ont une connaissance directe des utilisateurs, authentifient ces derniers à l'aide de leurs informations d'identification et émettent des demandes au sujet des utilisateurs. Un fournisseur de fédération est un autre type d'autorité :  au lieu d'authentifier directement les utilisateurs, il " répartit " l'authentification. Il agit comme intermédiaire entre l'application par partie de confiance et un ou plusieurs fournisseurs d'identité. fournisseurs d'identité. Les fournisseurs d'identité comme les fournisseurs de fédération émettent des jetons de sécurité, ils utilisent donc tous les deux le service STS. ACS est un fournisseur de fédération.

**Moteur de règles ACS** - Il s'agit de la logique utilisée pour transformer les jetons entrants, provenant de fournisseurs d'identité approuvés, en jeton destinés à être utilisés par la partie de confiance. Cette logique est codée sous forme de règles de transformation de demandes simples. ACS comprend un moteur de règles qui est chargé d'appliquer la logique de transformation que vous avez indiquée pour votre partie de confiance.

**Espace de noms de contrôle d'accès** - L'espace de noms constitue la partition la plus élevée d'ACS que vous utilisez pour organiser vos paramètres. Un espace de noms contient une liste de fournisseurs d'identité auxquels vous faites confiance, les applications par partie de confiance que vous souhaitez servir, les règles que le moteur de règles doit appliquer aux jetons entrants, etc. Il expose plusieurs points de terminaison qui seront utilisés par le développeur et l'application afin qu'ACS assure les fonctions demandées.

La figure suivante présente le fonctionnement de l'authentification ACS avec une application Web :

![ACS flow diagram][acs_flow]

1.  Le client (dans le cas présent, un navigateur) demande une page à partir de la partie de confiance.
2.  Puisque la demande n'a pas encore été authentifiée, la partie de confiance redirige l'utilisateur vers l'autorité approuvée, c'est-à-dire ACS. ACS propose à l'utilisateur de choisir parmi les fournisseurs d'identité spécifiés pour cette partie de confiance. L'utilisateur sélectionne le fournisseur d'identité approprié.
3.  Le client accède à la page d'authentification du fournisseur d'identité et invite l'utilisateur à se connecter.
4.  Une fois le client authentifié (par exemple, lorsque les informations d'identification de l'identité ont été entrées), le fournisseur d'identité émet un jeton de sécurité.
5.  À la suite de cela, il redirige le client vers ACS et le client envoie le jeton de sécurité (émis par le fournisseur d'identité) à ACS.
6.  ACS valide le jeton de sécurité émis par le fournisseur d'identité, inclut la demande d'identité de ce jeton dans le moteur de règles ACS, calcule la demande d'identité de sortie et émet un nouveau jeton de sécurité contenant ces demandes de sortie.
7.  ACS redirige le client vers la partie de confiance. Le client envoie le nouveau jeton de sécurité émis par ACS à la partie de confiance. La partie de confiance valide la signature du jeton de sécurité émis par ACS, valide les demandes du jeton et renvoie la page demandée à l'origine.

## <a name="pre"></a>Conditions préalables

Pour réaliser les tâches présentées dans ce guide, vous avez besoin des éléments suivants :

- Kit de développement logiciel (SDK) Java version 1.6 ou ultérieure
- IDE (environnement de développement intégré) Eclipse pour développeurs Java EE, Indigo ou ultérieur, Vous pouvez le télécharger à partir de <http://www.eclipse.org/downloads/>. 
- Une distribution d'un serveur web ou d'un serveur d'applications basé sur Java, tel que Apache Tomcat, GlassFish, JBoss Application Server ou Jetty.
- Un abonnement à Azure, pouvant être souscrit à l'adresse suivante : <http://www.microsoft.com/windowsazure/offers/>.
- Le plug-in Azure pour Eclipse avec Java (par Microsoft Open Technologies), version avril 2014. Pour plus d'informations, consultez la page [Installation du plug-in Azure pour Eclipse avec Java (par Microsoft Open Technologies)](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
- Un certificat X509 à utiliser avec votre application. Vous avez besoin du certificat public (.cer) et de celui au format Personal Information Exchange (.PFX) (les instructions de création de ce certificat sont indiquées plus loin dans ce didacticiel).
- Vous devez connaître l'émulateur de calcul Azure ainsi que les techniques de déploiement présentées dans la page [Création d'une application Hello World pour Azure dans Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx).

## <a name="create-namespace"></a>Création d'un espace de noms ACS

Pour commencer à utiliser ACS dans Azure, vous devez créer un espace de noms ACS. L'espace de noms fournit une étendue unique pour l'adressage des ressources ACS au sein de votre application.

1. Connectez-vous au [Portail de gestion Azure][].
2. Cliquez sur **Active Directory**. 
3. Pour créer un espace de noms ACS, cliquez sur **Nouveau**, sur **Services d'application**, sur **Access Control**, puis sur **Création rapide**. 
4. Entrez le nom de l'espace de noms. Azure vérifie que le nom est unique.
5. Sélectionnez la région dans laquelle l'espace de noms sera utilisé. Pour obtenir des performances optimales, sélectionnez la zone géographique dans laquelle vous déployez l'application.
6. Si vous avez plusieurs abonnements, sélectionnez celui que vous souhaitez utiliser pour l'espace de noms ACS.
7. Cliquez sur **Créer**.

Azure crée et active l'espace de noms. Patientez jusqu'à ce que l'état du nouvel espace de noms soit **Actif** avant de continuer. 

## <a name="add-IP"></a>Ajout de fournisseurs d'identité

Cette tâche vous permet d'ajouter des fournisseurs d'identité à utiliser avec votre application par partie de confiance pour réaliser les tâches d'authentification. Pour l'exemple, cette tâche vous indique comment ajouter Windows Live comme fournisseur d'identité, mais il est possible d'utiliser tous les fournisseurs répertoriés dans le portail de gestion ACS
.


1.  Dans le [Portail de gestion Azure][], cliquez sur **Active Directory**, sélectionnez un espace de noms Access Control, puis cliquez sur **Gérer**. Le portail de gestion ACS s'ouvre.
2.  Dans le volet de navigation gauche du portail de gestion ACS, cliquez sur **Fournisseurs d'identité**.
3.  Windows Live ID est activé par défaut. Il n'est pas possible de le supprimer. Dans le cadre de ce didacticiel, seul Windows Live ID est utilisé. C'est toutefois sur cet écran qu'il est possible d'ajouter d'autres fournisseurs d'identité, en cliquant sur le bouton **Ajouter**.

Windows Live ID est maintenant activé comme fournisseur d'identité pour votre espace de noms ACS. Ensuite, spécifiez votre application Web Java (qui sera créée ultérieurement) comme partie de confiance.

## <a name="add-RP"></a>Ajout d'une application par partie de confiance

Cette tâche vise à configurer ACS afin que votre application Web Java soit reconnue comme application par partie de confiance valide.

1.  Dans le Portail de gestion ACS, cliquez sur **Applications par partie de confiance**.
2.  Sur la page **Applications par partie de confiance**, cliquez sur **Ajouter**.
3.  Effectuez les actions suivantes sur la page **Ajouter une application par partie de confiance** :
    1.  Dans **Nom**, entrez le nom de la partie de confiance. Pour suivre l'exemple de ce didacticiel, entrez **Azure Web App**.
    2.  Dans **Mode**, sélectionnez **Saisir manuellement les paramètres**.
    3.  Sous **Domaine**, entrez l'URI auquel s'applique le jeton de sécurité émis par ACS. Pour suivre notre exemple, entrez **http://localhost:8080/**.
        ![Relying party realm for use in compute emulator][relying_party_realm_emulator]
    4.  Sous **URL de renvoi**, entrez l'adresse URL vers laquelle ACS renvoie le jeton de sécurité. Pour suivre notre exemple, entrez **http://localhost:8080/MyACSHelloWorld/index.jsp**
        ![Relying party return URL for use in compute emulator][relying_party_return_url_emulator]
    5.  Acceptez les valeurs par défaut dans les autres champs.

4.  Cliquez sur **Enregistrer**.

Votre application web Java est maintenant correctement configurée pour fonctionner avec l'émulateur de calcul Azure (à l'adresse http://localhost:8080/). Elle est considérée comme une partie de confiance dans votre espace de noms ACS. Il vous faut ensuite créer les règles utilisées par ACS pour traiter les demandes pour la partie de confiance.

## <a name="create-rules"></a>Création de règles

Au cours de cette tâche, vous allez définir les règles qui définissent comment les demandes sont transmises de vos fournisseurs d'identité à votre partie de confiance. Dans l'exemple suivi pour ce didacticiel, nous allons simplement configurer ACS en vue de copier les types et valeurs de demandes entrantes directement dans le jeton de sortie, sans filtrage ou modification de ceux-ci.

1.  Sur la page principale du Portail de gestion ACS, cliquez sur **Groupes de règles**.
2.  Sur la page **Groupes de règles**, cliquez sur **Groupe de règles par défaut pour Azure Web App**.
3.  Sur la page **Modifier le groupe de règles**, cliquez sur **Générer**.
4.  Sur la page **Generate Rules: Groupe de règles par défaut pour Azure Web App**, vérifiez que Windows Live ID est sélectionné, puis cliquez sur **Générer**.	
5.  Sur la page **Modifier le groupe de règles**, cliquez sur **Enregistrer**.

## <a name="upload-certificate"></a>Téléchargement d'un certificat vers votre espace de noms ACS

Au cours de cette tâche, vous allez télécharger un certificat .PFX qui sera utilisé pour signer les demandes de jetons créées par l'espace de noms ACS.

1.  Sur la page principale du portail de gestion ACS, cliquez sur **Certificats et clés**.
2.  Sur la page **Certificats et clés**, cliquez sur **Ajouter** au-dessus de **Signature de jetons**.
3.  Sur la page **Ajouter un certificat ou une clé de signature de jetons** :
    1. Dans la section **Utilisé pour**, cliquez sur **Application par partie de confiance**, puis sélectionnez **Azure Web App** (que vous avez précédemment défini comme nom de votre application par partie de confiance).
    2. Dans la section **Type**, sélectionnez **Certificat X.509**.
    3. Dans la section **Certificat**, cliquez sur le bouton Parcourir, puis accédez au fichier de certificat X.509 que vous souhaitez utiliser. Il s'agit d'un fichier .PFX. Sélectionnez le fichier, cliquez sur **Ouvrir**, puis saisissez le mot de passe du certificat dans la zone **Mot de passe**. Dans le cadre d'un test, vous pouvez utiliser un certificat auto-signé. Pour créer un certificat auto-signé, dans la boîte de dialogue **Bibliothèque de filtres ACS** (qui sera décrite ultérieurement), appuyez sur le bouton **Nouveau** ou servez-vous de l'utilitaire **encutil.exe** présent sur le [site web du projet][] du kit de démarrage Azure pour Java (par Microsoft Open Technologies).
    4. Vérifiez que l'option **Convertir en principal** est sélectionnée. Votre page **Ajouter un certificat ou une clé de signature de jetons** doit être similaire à celle-ci.
        ![Add token-signing certificate][add_token_signing_cert]
    5. Cliquez sur **Enregistrer** pour enregistrer vos paramètres et fermer la page **Ajouter un certificat ou une clé de signature de jetons**.

Vérifiez ensuite les informations contenues dans la page d'intégration de l'application, puis copiez l'URI. Il vous permettra de configurer votre application Web Java afin qu'elle utilise ACS.

## <a name="review-app-int"></a>Vérification de la page d'intégration d'application

Vous pouvez trouver toutes les informations et les codes nécessaires à la configuration de votre application Web Java (application par partie de confiance) afin qu'elle fonctionne avec ACS sur la page d'intégration d'application du portail de gestion ACS. Ces informations sont nécessaires pour la configuration de votre application Web Java et l'authentification fédérée.

1.  Dans le portail de gestion ACS, cliquez sur **Intégration d'application**.  
2.  Sur la page **Intégration d'application**, cliquez sur **Pages de connexion**.
3.  Sur la page **Intégration de la page de connexion**, cliquez sur **Azure Web App**.

Sur la page **Login Page Integration: Azure Web App**, l'URL répertoriée sous **Option 1: Link to an ACS-hosted login page** sera utilisée dans votre application web Java. Cette valeur doit être indiquée lors de l'ajout de la bibliothèque de filtres ACS Azure à votre application Java.

## <a name="create-java-app"></a>Création d'une application Web Java
1. Dans le menu d'Eclipse, cliquez sur **File**, sur **New**, puis sur **Dynamic Web Project**. (Si vous ne voyez pas **Dynamic Web Project** répertorié en tant que projet disponible après avoir cliqué sur **Fichier** et **Nouveau**, procédez comme suit : cliquez sur **Fichier**, sur **Nouveau** et sur **Projet**, développez **Web**, puis cliquez sur **Projet Web dynamique**, et sur **Suivant**.) Pour l'exemple de ce didacticiel, nommez votre projet **MyACSHelloWorld**. Assurez-vous de bien utiliser ce nom, les étapes suivantes du didacticiel partent du principe que votre fichier WAR se nomme MyACSHelloWorld. Votre écran se présente comme suit :

    ![Create a Hello World project for ACS exampple][create_acs_hello_world]

    Cliquez sur **Finish**.
2. Dans la vue de l'Explorateur de projets Eclipse, développez **MyACSHelloWorld**. Cliquez avec le bouton droit sur **WebContent**, puis cliquez sur **New**, puis sur **JSP File**.
3. Dans la boîte de dialogue **New JSP File**, nommez le fichier **index.jsp**. Conservez MyACSHelloWorld/WebContent comme dossier parent, comme illustré ci-dessous :

    ![Add a JSP file for ACS example][add_jsp_file_acs]

    Cliquez sur **Suivant**.

4. Dans la boîte de dialogue **Sélectionner le modèle JSP**, sélectionnez **Nouveau fichier JSP (html)**, puis cliquez sur **Terminer**.
5. Lorsque le fichier index.jsp s'ouvre dans Eclipse, ajoutez du texte pour afficher **Hello ACS World!** au sein de l'élément `<body>` existant. Votre contenu `<body>` mis à jour doit apparaître comme suit :

        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
    
    Enregistrez index.jsp.
  
## <a name="add_acs_filter_library"></a>Ajout de la bibliothèque de filtres ACS à votre application

1. Dans l'Explorateur de projets Eclipse, cliquez avec le bouton droit sur **MyACSHelloWorld**, cliquez sur **Chemin d'accès de build**, puis sur **Configurer le chemin d'accès à la build**.
2. Dans la boîte de dialogue **Chemin d'accès de la génération Java**, cliquez sur l'onglet **Bibliothèques**.
3. Cliquez sur **Ajouter une bibliothèque**.
4. Cliquez sur **Filtres ACS Azure (by MS Open Tech)**, puis sur **Suivant**. La boîte de dialogue **Filtres ACS Azure** s'affiche.  (Un chemin différent peut être indiqué pour le champ **Emplacement**, en fonction de l'emplacement d'installation d'Eclipse. Le numéro de version peut également être différent en fonction des mises à jour logicielles.)

    ![Add ACS Filter library][add_acs_filter_lib]

5. En utilisant un navigateur ouvert sur la page **Intégration de la page de connexion** du portail de gestion, copiez l'URL indiquée dans le champ **Option 1 : Lien vers une page de connexion hébergée par ACS**, puis collez-la dans le champ **Point de terminaison de l'authentification ACS** de la boîte de dialogue Eclipse.
6. En utilisant un navigateur ouvert sur la page **Modifier l'application par partie de confiance** du portail de gestion, copiez l'URL indiquée dans le champ **Domaine**, puis collez-la dans le champ **Domaine par partie de confiance** de la boîte de dialogue Eclipse.
7. Dans la section **Sécurité** de la boîte de dialogue Eclipse, si vous souhaitez utiliser un certificat existant, cliquez sur **Parcourir**, accédez au certificat souhaité, sélectionnez-le, puis cliquez sur **Ouvrir**. Si vous préférez créer un certificat, cliquez sur **Nouveau** pour afficher la boîte de dialogue **Nouveau certificat**, puis indiquez le mot de passe, le nom du fichier.cer et celui du fichier .pfx pour le nouveau certificat.
8. Activez la case à cocher **Incorporer le certificat dans le fichier WAR**. Intégrer le certificat de cette façon permet de l'inclure dans votre déploiement sans avoir besoin de l'ajouter manuellement comme composant. (Si vous devez stocker votre certificat en externe depuis votre fichier WAR, vous pouvez ajouter le certificat en tant que rôle et désactiver la case à cocher **Incorporer le certificat dans le fichier WAR**.)
9. [Facultatif] Laissez la case à cocher **Utiliser des connexions HTTPS**. Si vous activez cette option, vous devrez accéder à votre application via le protocole HTTPS. Si vous ne souhaitez pas utiliser de connexions HTTPS, désélectionnez cette option.
10. Pour un déploiement vers l'émulateur de calcul, vos paramètres **Filtre ACS Azure** doivent être similaires à l'exemple ci-dessous.

    ![Azure ACS Filter settings for a deployment to the compute emulator][add_acs_filter_lib_emulator]

11. Cliquez sur **Finish**.
12. Cliquez sur **Oui** dans la boîte de dialogue indiquant qu'un fichier web.xml va être créé.
13. Cliquez sur **OK** pour fermer la boîte de dialogue **Chemin d'accès de la génération Java**.

## <a name="deploy_compute_emulator"></a>Déploiement de l'émulateur de calcul

1. Dans l'Explorateur de projets Eclipse, cliquez avec le bouton droit sur **MyACSHelloWorld**, cliquez sur **Azure**, puis sur **Package pour Azure**.
2. Dans **Nom du projet**, entrez **MyAzureACSProject**, puis cliquez sur **Suivant**.
3. Sélectionnez un JDK et un serveur d'applications. (Ces étapes sont détaillées dans le didacticiel [Création d'une application Hello World pour Azure dans Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx).)
4. Cliquez sur **Finish**.
5. Cliquez sur le bouton **Exécuter dans l'émulateur Azure**.
6. Lorsque votre application web Java est lancée dans l'émulateur de calcul, fermez toutes les instances de votre navigateur (afin qu'aucune session de navigateur ne puisse perturber votre test de connexion à ACS).
7. Exécutez votre application en ouvrant <http://localhost:8080/MyACSHelloWorld/> dans votre navigateur (ou <https://localhost:8080/MyACSHelloWorld/> si vous avez activé la case **Utiliser des connexions HTTPS**). Un identifiant Windows Live ID vous est normalement demandé, puis vous êtes transféré vers l'URL de renvoi spécifiée dans votre application par partie de confiance.
99.  Lorsque vous avez terminé d'afficher votre application, cliquez sur le bouton **Réinitialiser l'émulateur Azure**.

## <a name="deploy_azure"></a>Déploiement sur Azure

Pour effectuer un déploiement sur Azure, vous devez changer le domaine de partie de confiance et l'URL de renvoi de votre espace de noms.

1. Dans le portail de gestion Azure, sur la page **Modifier l'application par partie de confiance**, modifiez **Domaine** et entrez l'URL de votre site déployé. Remplacez **example** par le nom DNS spécifié lors du déploiement.

    ![Relying party realm for use in production][relying_party_realm_production]

2. Modifiez la valeur **URL de renvoi** en entrant l'URL de votre application. Remplacez **example** par le nom DNS spécifié lors du déploiement.

    ![Relying party return URL for use in production][relying_party_return_url_production]

3. Cliquez sur **Enregistrer** pour enregistrer les modifications apportées au domaine de partie de confiance et à l'URL de renvoi.
4. Laissez la page **Intégration de la page de connexion** ouverte dans votre navigateur, car vous allez avoir besoin de copier certaines données qui s'y trouvent.
5. Dans l'Explorateur de projets Eclipse, cliquez avec le bouton droit sur **MyACSHelloWorld**, cliquez sur **Chemin d'accès de build**, puis sur **Configurer le chemin d'accès à la build**.
6. Cliquez sur l'onglet **Bibliothèques**, sur **Filtres ACS Azure**, puis sur **Modifier**.
7. En utilisant un navigateur ouvert sur la page **Intégration de la page de connexion** du portail de gestion, copiez l'URL indiquée dans le champ **Option 1 : Lien vers une page de connexion hébergée par ACS**, puis collez-la dans le champ **Point de terminaison de l'authentification ACS** de la boîte de dialogue Eclipse.
8. En utilisant un navigateur ouvert sur la page **Modifier l'application par partie de confiance** du portail de gestion, copiez l'URL indiquée dans le champ **Domaine**, puis collez-la dans le champ **Domaine par partie de confiance** de la boîte de dialogue Eclipse.
9. Dans la section **Sécurité** de la boîte de dialogue Eclipse, si vous souhaitez utiliser un certificat existant, cliquez sur **Parcourir**, accédez au certificat souhaité, sélectionnez-le, puis cliquez sur **Ouvrir**. Si vous préférez créer un certificat, cliquez sur **Nouveau** pour afficher la boîte de dialogue **Nouveau certificat**, puis indiquez le mot de passe, le nom du fichier.cer et celui du fichier .pfx pour le nouveau certificat.
10. Laissez l'option **Incorporer le certificat dans le fichier WAR** activée si vous souhaitez incorporer le certificat dans le fichier WAR.
11. [Facultatif] Laissez la case à cocher **Utiliser des connexions HTTPS**. Si vous activez cette option, vous devrez accéder à votre application via le protocole HTTPS. Si vous ne souhaitez pas utiliser de connexions HTTPS, désélectionnez cette option.
12. Pour un déploiement vers Azure, vos paramètres de filtre ACS Azure doivent être similaires à l'exemple ci-dessous.

    ![Azure ACS Filter settings for a production deployment][add_acs_filter_lib_production]

13. Cliquez sur **Terminer** pour fermer la boîte de dialogue **Modifier la bibliothèque**.
14. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés de MyACSHelloWorld**.
15. Dans Eclipse, cliquez sur le bouton **Publier sur le cloud Azure**. Répondez aux invites, comme expliqué dans la section **Déploiement de votre application dans Azure** de la rubrique [Création d'une application Hello World pour Azure dans Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx). 

Une fois le déploiement de votre application web terminé, fermez les sessions de navigateur en cours, exécutez votre application web. Votre identifiant Windows Live ID doit vous être demandé. Vous êtes ensuite transféré vers l'URL de renvoi de votre application par partie de confiance.

Lorsque vous avez terminé d'utiliser votre application ACS Hello World, n'oubliez pas de supprimer le déploiement (la rubrique [Création d'une application Hello World pour Azure dans Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) explique comment procéder).


## <a name="next_steps"></a>Étapes suivantes

Pour étudier les données SAML (Security Assertion Markup Language) renvoyées par ACS vers votre application, consultez la rubrique [Affichage des données SAML renvoyées par Azure ACS][]. Pour en savoir plus sur les fonctionnalités ACS et découvrir des scénarios plus complexes, consultez la page [Access Control Service 2.0][].

Cet exemple utilisait l'option **Incorporer le certificat dans le fichier WAR** qui simplifie le déploiement du certificat. Si vous préférez maintenir le certificat de signature séparé du fichier WAR, vous pouvez utiliser la technique suivante :

1. Dans la section **Sécurité** de la boîte de dialogue **Filtres Azure ACS**, entrez **${env.JAVA_HOME}/mycert.cer**, puis désactivez l'option **Incorporer le certificat dans le fichier WAR**. Modifiez mycert.cer si le nom de fichier du certificat est différent. Cliquez sur **Terminer** pour fermer la boîte de dialogue.
2. Copiez le certificat comme élément de votre déploiement : Dans l'Explorateur de projets Eclipse, développez **MyAzureACSProject**, cliquez avec le bouton droit sur **WorkerRole1**, cliquez sur **Propriétés**, développez **Rôle Azure** et cliquez sur **Composants**.
3. Cliquez sur **Ajouter**.
4. Dans la boîte de dialogue **Ajouter un composant** :
    1. Dans la section **Importer** :
        1. Utilisez le bouton **Fichier** pour accéder au certificat que vous souhaitez utiliser. 
        2. Sous **Méthode**, sélectionnez **Copier**.
    2. Dans la section **Comme nom**, cliquez sur la zone de texte et acceptez le nom par défaut.
    3. Dans la section **Déploiement** :
        1. Sous **Méthode**, sélectionnez **Copier**.
        2. Dans la zone **Annuaire de destination**, entrez **%JAVA_HOME%**.
    4. La boîte de dialogue **Ajouter un composant** doit être similaire à la suivante.

        ![Add certificate component][add_cert_component]

    5. Cliquez sur **OK**.

Votre certificat doit maintenant être inclus dans votre déploiement. Que le certificat soit intégré au fichier WAR ou ajouté en tant que composant au déploiement, vous devez télécharger le certificat dans votre espace de noms en suivant les instructions de la section [Téléchargement d'un certificat vers votre espace de noms ACS][].

[Qu'est-ce qu'ACS ?]: #what-is
[Concepts]: #concepts
[Conditions préalables]: #pre
[Création d'une application web Java]: #create-java-app
[Création d'un espace de noms ACS]: #create-namespace
[Ajout de fournisseurs d'identité]: #add-IP
[Ajout d'une application par partie de confiance]: #add-RP
[Création de règles]: #create-rules
[Téléchargement d'un certificat vers votre espace de noms ACS]: #upload-certificate
[Vérification de la page d'intégration d'application]: #review-app-int
[Configuration de l'approbation entre ACS et les applications web ASP.NET]: #config-trust
[Ajout de la bibliothèque de filtres ACS à votre application]: #add_acs_filter_library
[Déploiement de l'émulateur de calcul]: #deploy_compute_emulator
[Déploiement sur Azure]: #deploy_azure
[Étapes suivantes]: #next_steps
[Site web de projet]: http://wastarterkit4java.codeplex.com/releases/view/61026
[Affichage des données SAML renvoyées par Azure ACS]: /develop/java/how-to-guides/view-saml-returned-by-acs/
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Kit de développement logiciel Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Portail de gestion Azure]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png

<!--HONumber=47-->
