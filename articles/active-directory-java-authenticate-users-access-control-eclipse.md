<properties linkid="develop-java-how-to-guides-access-control" urlDisplayName="Access Control" pageTitle="How to use Access Control (Java) - Azure feature guide" metaKeywords="" description="Learn how to develop and use Access Control with Java in Azure." metaCanonical="" services="active-directory" documentationCenter="Java" title="How to Authenticate Web Users with Azure Access Control Service Using Eclipse" videoId="" scriptId="" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Authentification des utilisateurs web auprès d'Azure Access Control Service à l'aide d'Eclipse

Ce guide vous indique comment utiliser Azure Access Control Service (ou ACS) au sein du plug-in Azure pour Eclipse avec Java (par Microsoft Open Technologies). Pour plus d'informations sur ACS, consultez la section [Étapes suivantes][Étapes suivantes].

> [WACOM.NOTE]
> Le filtre ACS Azure (par Microsoft Open Technologies) est une version préliminaire de la technologie destinée à la communauté. En tant que logiciel préliminaire, il n'est pas officiellement pris en charge par Microsoft Open Technologies, Inc. ou Microsoft.

## Sommaire

-   [Qu'est-ce qu'ACS ?][Qu'est-ce qu'ACS ?]
-   [Concepts][Concepts]
-   [Conditions préalables][Conditions préalables]
-   [Création d'un espace de noms ACS][Création d'un espace de noms ACS]
-   [Ajout de fournisseurs d'identité][Ajout de fournisseurs d'identité]
-   [Ajout d'une application par partie de confiance][Ajout d'une application par partie de confiance]
-   [Création de règles][Création de règles]
-   [Téléchargement d'un certificat vers votre espace de noms ACS][Téléchargement d'un certificat vers votre espace de noms ACS]
-   [Vérification de la page d'intégration d'application][Vérification de la page d'intégration d'application]
-   [Création d'une application web Java][Création d'une application web Java]
-   [Ajout de la bibliothèque de filtres ACS à votre application][Ajout de la bibliothèque de filtres ACS à votre application]
-   [Déploiement de l'émulateur de calcul][Déploiement de l'émulateur de calcul]
-   [Déploiement sur Azure][Déploiement sur Azure]
-   [Étapes suivantes][Étapes suivantes]

## <a name="what-is"></a>Qu'est-ce qu'ACS ?

La plupart des développeurs ne sont pas des experts en matière d'identité et ne souhaitent
généralement pas passer du temps à développer des mécanismes d'authentification
et d'autorisation pour leurs applications et services. ACS est un service Azure qui
vous permet d'authentifier rapidement les utilisateurs lorsqu'ils accèdent à
vos applications et services web sans ajouter de logique d'authentification
complexe à votre code.

Les fonctionnalités suivantes sont disponibles dans ACS :

-   Intégration avec Windows Identity Foundation (WIF).
-   Prise en charge des fournisseurs d'identité web reconnus, notamment Windows Live ID, Google, Yahoo! et Facebook.
-   Prise en charge d'Active Directory Federation Services (AD FS) 2.0.
-   Service de gestion OData (Open Data Protocol) qui fournit un accès par programme aux paramètres ACS.
-   Portail de gestion qui autorise l'accès administratif aux paramètres ACS.

Pour plus d'informations sur ACS, consultez la page [Access Control Service 2.0][Access Control Service 2.0].

## <a name="concepts"></a>Concepts

Azure ACS repose sur les principes de l'identité basée sur des
demandes, une approche cohérente permettant de créer des mécanismes d'authentification
 pour les applications exécutées localement ou dans le cloud. Cette approche permet
aux applications et aux services d'obtenir les informations d'identité
dont ils ont besoin pour les utilisateurs internes ou externes à leur
organisation et sur Internet.

Pour réaliser les tâches présentées dans ce guide, vous devez comprendre
les éléments suivants :

**Client** : dans le contexte de ce guide, il s'agit d'un navigateur qui
tente d'accéder à votre application web.

**Application par partie de confiance** : il s'agit d'un site web ou d'un
service qui sous-traite l'authentification à une autorité externe. Dans
le jargon technique, nous disons que la partie de confiance fait confiance à cette autorité. Ce guide
décrit la configuration de votre application de manière à ce qu'elle approuve ACS.

**Jeton** : un jeton est un ensemble de données de sécurité
généralement émis après identification d'un utilisateur. Il contient un jeu de *demandes* qui sont des attributs de l'utilisateur authentifié. Une demande peut représenter
le nom d'un utilisateur, son âge, l'identifiant pour un rôle qui lui
est attribué, etc. Un jeton bénéficie le plus souvent d'une signature
numérique, ce qui permet de remonter jusqu'à son émetteur et de le protéger
contre toute tentative de modification. Un utilisateur obtient l'accès à une application par partie de
confiance en présentant un jeton valide émis par une autorité approuvée par celle-ci.

**Fournisseur d'identité** : un fournisseur d'identité est une autorité
qui authentifie des identités d'utilisateur et émet des jetons de sécurité. L'activité d'émission des jetons
est implémentée via un service spécial nommé « service d'émission
de jeton de sécurité » (STS, Security Token Service). Les fournisseurs d'identité les plus connus sont
Windows Live ID, Facebook, les référentiels d'utilisateurs professionnels (tels qu'Active Directory), etc.
Lorsqu'ACS est configuré de manière à approuver un fournisseur
d'identité, le système accepte et valide les jetons émis par ce fournisseur. ACS peut approuver plusieurs
fournisseurs d'identité simultanément. Donc lorsque votre application
approuve ACS, vous pouvez fournir aux utilisateurs la possibilité d'être
authentifiés par un fournisseur d'identité qu'ACS approuve en votre nom.

**Fournisseur de fédération** : les fournisseurs d'identité connaissent
directement les utilisateurs et les authentifient grâce à leurs informations
d'identification. Ils émettent des demandes en fonction des informations utilisateur connues. Un fournisseur de fédération est un autre type
d'autorité : plutôt que d'authentifier directement les utilisateurs, il
agit comme intermédiaire entre l'application par partie de confiance et un ou
plusieurs fournisseurs d'identité. Les fournisseurs d'identité comme les fournisseurs de
fédération émettent des jetons de sécurité, ils utilisent donc tous les deux le service STS. ACS est un fournisseur de fédération.

**Moteur de règles ACS** : il s'agit de la logique utilisée pour transformer
les jetons entrants, provenant de fournisseurs d'identité approuvés, en jeton destinés
à être utilisés par la partie de confiance. Cette logique est codée sous forme de règles de transformation de demandes simples. ACS comprend un moteur de
règles qui est chargé d'appliquer la logique de transformation que vous
avez indiquée pour votre partie de confiance.

**Espace de noms ACS** : l'espace de noms constitue la partition la plus élevée
d'ACS que vous utilisez pour organiser vos paramètres. Un espace de noms contient une liste
de fournisseurs d'identité auxquels vous faites confiance, les applications
par partie de confiance que vous souhaitez servir, les règles que le moteur de règles doit appliquer aux jetons entrants, etc. Il expose
plusieurs points de terminaison qui seront utilisés par le développeur et
l'application afin qu'ACS assure les fonctions demandées.

La figure suivante présente le fonctionnement de l'authentification
ACS avec une application web :

![Diagramme de flux ACS][Diagramme de flux ACS]

1.  Le client (dans le cas présent, un navigateur) demande une page à partir de la partie de confiance.
2.  Puisque la demande n'a pas encore été authentifiée, la partie de confiance redirige l'utilisateur vers l'autorité approuvée, c'est-à-dire ACS. ACS propose à l'utilisateur de choisir parmi les fournisseurs d'identité spécifiés pour cette partie de confiance. L'utilisateur sélectionne le fournisseur d'identité approprié.
3.  Le client accède à la page d'authentification du fournisseur d'identité et invite l'utilisateur à se connecter.
4.  Une fois le client authentifié (par exemple, lorsque les informations d'identification de l'identité ont été entrées), le fournisseur d'identité émet un jeton de sécurité.
5.  À la suite de cela, il redirige le client vers ACS et le client envoie le jeton de sécurité (émis par le fournisseur d'identité) à ACS.
6.  ACS valide le jeton de sécurité émis par le fournisseur d'identité, inclut la demande d'identité de ce jeton dans le moteur de règles ACS, calcule la demande d'identité de sortie et émet un nouveau jeton de sécurité contenant ces demandes de sortie.
7.  ACS redirige le client vers la partie de confiance. Le client envoie le nouveau jeton de sécurité émis par ACS à la partie de confiance. La partie de confiance valide la signature du jeton de sécurité émis par ACS, valide les demandes du jeton et renvoie la page demandée à l'origine.

## <a name="pre"></a>Conditions préalables

Pour réaliser les tâches présentées dans ce guide, vous avez besoin des éléments suivants :

-   Kit de développement logiciel (SDK) Java version 1.6 ou ultérieure
-   IDE (environnement de développement intégré) Eclipse pour développeurs Java EE, Indigo ou ultérieur, disponible en téléchargement à l'adresse suivante : <http://www.eclipse.org/downloads/>.
-   Une distribution d'un serveur web ou d'un serveur d'applications basé sur Java, tel que Apache Tomcat, GlassFish, JBoss Application Server ou Jetty.
-   Un abonnement à Azure, pouvant être souscrit à l'adresse suivante : <http://www.microsoft.com/windowsazure/offers/>.
-   Le plug-in Azure pour Eclipse avec Java (par Microsoft Open Technologies), version avril 2014. Pour plus d'informations, consultez la page [Installation du plug-in Azure pour Eclipse avec Java (par Microsoft Open Technologies)][Installation du plug-in Azure pour Eclipse avec Java (par Microsoft Open Technologies)].
-   Un certificat X509 à utiliser avec votre application. Vous avez besoin du certificat public (.cer) et de celui au format Personal Information Exchange (.PFX) (les instructions de création de ce certificat sont indiquées plus loin dans ce didacticiel).
-   Vous devez connaître l'émulateur de calcul Azure ainsi que les techniques de déploiement présentées dans la page [Création d'une application Hello World pour Azure dans Eclipse][Création d'une application Hello World pour Azure dans Eclipse].

## <a name="create-namespace"></a>Création d'un espace de noms ACS

Pour commencer à utiliser ACS dans Azure, vous devez créer
un espace de noms ACS. L'espace de noms fournit une étendue unique pour
l'adressage des ressources ACS au sein de votre application.

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Cliquez sur **Active Directory**.
3.  Pour créer un espace de noms ACS, cliquez sur **Nouveau**, sur **Services d'application**, sur **Access Control**, puis sur **Création rapide**.
4.  Entrez le nom de l'espace de noms. Azure vérifie que le nom est unique.
5.  Sélectionnez la région dans laquelle l'espace de noms sera utilisé. Pour obtenir des performances optimales, sélectionnez la zone géographique dans laquelle vous déployez l'application.
6.  Si vous avez plusieurs abonnements, sélectionnez celui que vous souhaitez utiliser pour l'espace de noms ACS.
7.  Cliquez sur **Create**.

Azure crée et active l'espace de noms. Patientez jusqu’à ce que l’état du nouvel espace de noms soit **Active** avant de continuer.

## <a name="add-IP"></a>Ajout de fournisseurs d'identité

Cette tâche vous permet d'ajouter des fournisseurs d'identité
à utiliser avec votre application par partie de confiance pour réaliser les tâches d'authentification. Pour l'exemple, cette tâche vous indique comment ajouter
Windows Live comme fournisseur d'identité, mais il est possible d'utiliser
tous les fournisseurs répertoriés dans le portail de gestion ACS.

1.  Dans le [portail de gestion Azure][portail de gestion Azure], cliquez **Active Directory**, sélectionnez un espace de noms Access Control, puis cliquez sur **Gérer**. Le portail de gestion ACS s'ouvre.
2.  Dans le volet de navigation gauche du portail de gestion ACS, cliquez sur **Identity providers**.
3.  Windows Live ID est activé par défaut. Il n'est pas possible de le supprimer. Dans le cadre de ce didacticiel, seul Windows Live ID est utilisé. C'est toutefois sur cet écran qu'il est possible d'ajouter d'autres fournisseurs d'identité, en cliquant sur le bouton **Add**.

Windows Live ID est maintenant activé comme fournisseur d'identité pour votre espace de noms ACS. Ensuite,
spécifiez votre application web Java (qui sera créée ultérieurement) comme partie de confiance.

## <a name="add-RP"></a>Ajout d'une application par partie de confiance

Cette tâche vise à configurer ACS afin que votre application
web Java soit reconnue comme application par partie de confiance valide.

1.  Dans le portail de gestion ACS, cliquez sur **Relying party applications**.
2.  Sur la page **Relying Party Applications**, cliquez sur **Add**.
3.  Effectuez les actions suivantes sur la page **Add Relying Party Application** :

    1.  Dans **Name**, entrez le nom de la partie de confiance. Pour suivre l'exemple de ce didacticiel, entrez
        **Azure Web App**.
    2.  Dans **Mode**, sélectionnez **Enter settings manually**.
    3.  Sous **Realm**, entrez l'URI auquel s'applique le jeton de sécurité émis par ACS. Dans le cas présent, entrez **http://localhost:8080/**.
        ![Domaine de partie de confiance à utiliser dans l'émulateur de calcul][Domaine de partie de confiance à utiliser dans l'émulateur de calcul]
    4.  Sous **Return URL**, entrez l'adresse URL vers laquelle ACS renvoie le jeton de sécurité. Dans le cas présent, entrez **http://localhost:8080/MyACSHelloWorld/index.jsp**  
        ![URL de retour vers partie de confiance pour utilisation dans l'émulateur de calcul][URL de retour vers partie de confiance pour utilisation dans l'émulateur de calcul]
    5.  Acceptez les valeurs par défaut dans les autres champs.

4.  Cliquez sur **Enregistrer**.

Votre application web Java est maintenant correctement configurée pour fonctionner avec l'émulateur de calcul Azure (à l'adresse
http://localhost:8080/). Elle est considérée comme une partie de confiance dans votre espace de noms ACS. Il vous faut
ensuite créer les règles utilisées par ACS pour traiter les demandes pour la partie de confiance.

## <a name="create-rules"></a>Création de règles

Au cours de cette tâche, vous allez définir les règles qui définissent
comment les demandes sont transmises de vos fournisseurs d'identité à votre partie de confiance. Dans l'exemple suivi pour ce didacticiel, nous allons
simplement configurer une règle pour indiquer à ACS de copier les types
et valeurs de demandes entrantes directement dans le jeton de sortie, sans effectuer de filtrage ni apporter de modifications.

1.  Sur la page principale du portail de gestion ACS, cliquez sur **Rule groups**.
2.  Sur la page **Rule Groups**, cliquez sur **Default Rule Group for Azure Web App**.
3.  Sur la page **Edit Rule Group**, cliquez sur **Generate**.
4.  Sur la page **Generate Rules: Default Rule Group for Azure Web App**, vérifiez que Windows Live ID est sélectionné, puis cliquez sur **Generate**.
5.  Sur la page **Edit Rule Group**, cliquez sur **Save**.

## <a name="upload-certificate"></a>Téléchargement d'un certificat vers votre espace de noms ACS

Au cours de cette tâche, vous allez télécharger un certificat .PFX qui sera utilisé pour signer les demandes de jetons créées par l'espace de noms ACS.

1.  Sur la page principale du portail de gestion ACS, cliquez sur **Certificates and keys**.
2.  Sur la page **Certificates and Keys**, cliquez sur **Add** au-dessus de **Token Signing**.
3.  Sur la page **Add Token-Signing Certificate or Key** :
    1.  Dans la section **Used for**, cliquez sur **Relying Party Application**, puis sélectionnez **Azure Web App** (que vous avez précédemment défini comme nom de votre application de partie de confiance).
    2.  Dans la section **Type**, sélectionnez **X.509 Certificate**.
    3.  Dans la section **Certificate**, cliquez sur le bouton Parcourir, puis accédez au fichier de certificat X.509 que vous souhaitez utiliser. Il s'agit d'un fichier .PFX. Sélectionnez le fichier, cliquez sur **Open**, puis saisissez le mot de passe du certificat dans la zone **Password**. Dans le cadre d'un test, vous pouvez utiliser un certificat auto-signé. Pour créer un certificat auto-signé, dans la boîte de dialogue **ACS Filter Library** (qui sera décrite ultérieurement), appuyez sur le bouton **New** ou servez-vous de l'utilitaire **encutil.exe** présent sur le [site web du projet][site web du projet] de kit de démarrage Azure pour Java (par Microsoft Open Technologies).
    4.  Vérifiez que **Make Primary** est sélectionné. Votre page **Add Token-Signing Certificate or Key** doit être similaire à celle-ci.
        ![Ajouter un certificat de signature de jeton][Ajouter un certificat de signature de jeton]
    5.  Cliquez sur **Save** pour enregistrer vos paramètres et fermer la page **Add Token-Signing Certificate or Key**.

Vérifiez ensuite les informations contenues dans la page d'intégration
de l'application, puis copiez l'URI. Il vous permettra de
configurer votre application web Java afin qu'elle utilise ACS.

## <a name="review-app-int"></a>Vérification de la page d'intégration d'application

Vous pouvez trouver toutes les informations et les codes nécessaires
à la configuration de votre application web Java (application
par partie de confiance) afin qu'elle fonctionne avec ACS sur la page d'intégration d'application du portail de gestion ACS. Ces
informations sont nécessaires pour la configuration de votre application
web Java et l'authentification fédérée.

1.  Dans le portail de gestion ACS, cliquez sur **Application integration**.
2.  Sur la page **Application Integration**, cliquez sur **Login Pages**.
3.  Sur la page **Login Page Integration**, cliquez sur **Azure Web App**.

Sur la page **Login Page Integration: Azure Web App**, l'URL répertoriée sous **Option 1: Link to an ACS-hosted login page** sera utilisée dans votre application web Java. Cette valeur doit être indiquée lors de l'ajout de la bibliothèque de filtres ACS Azure à votre application Java.

## <a name="create-java-app"></a>Création d'une application web Java

1.  Dans le menu d'Eclipse, cliquez sur **File**, sur **New**, puis sur **Dynamic Web Project**. Si **Dynamic Web Project** ne figure pas dans la liste des projets disponibles après avoir cliqué sur **File**, puis **New**, procédez comme suit : cliquez sur **File**, **New**, **Project**, développez le nœud **Web**, cliquez sur **Dynamic Web Project**, puis sur **Suivant**. Pour l'exemple de ce didacticiel, nommez votre projet **MyACSHelloWorld**. Assurez-vous de bien utiliser ce nom, les étapes suivantes du didacticiel partent du principe que votre fichier WAR se nomme MyACSHelloWorld. Votre écran se présente comme suit :

    ![Créer un projet Hello World pour l'exemple ACS][Créer un projet Hello World pour l'exemple ACS]

    Cliquez sur **Terminer**.

2.  Dans la vue de l'Explorateur de projets Eclipse, développez **MyACSHelloWorld**. Cliquez avec le bouton droit sur **WebContent**, cliquez sur **New**, puis sur **JSP File**.
3.  Dans la boîte de dialogue **New JSP File**, nommez le fichier **index.jsp**. Conservez MyACSHelloWorld/WebContent comme dossier parent, comme illustré ci-dessous :

    ![Ajouter un fichier JSP pour l'exemple ACS][Ajouter un fichier JSP pour l'exemple ACS]

    Cliquez sur **Suivant**.

4.  Dans la boîte de dialogue **Select JSP Template**, sélectionnez **New JSP File (html)**, puis cliquez sur **Terminer**.
5.  Lorsque le fichier index.jsp s'ouvre dans Eclipse, ajoutez du texte pour afficher **Hello ACS World!** au sein de l'élément `<body>` existant. Votre contenu `<body>` mis à jour doit apparaître comme suit :

        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>

    Enregistrez index.jsp.

## <a name="add_acs_filter_library"></a>Ajout de la bibliothèque de filtres ACS à votre application

1.  Dans l'Explorateur de projets Eclipse, cliquez avec le bouton droit sur **MyACSHelloWorld**, cliquez sur **Build Path**, puis sur **Configure Build Path**.
2.  Dans la boîte de dialogue **Java Build Path**, cliquez sur l'onglet **Libraries**.
3.  Cliquez sur **Add Library**.
4.  Cliquez sur **Azure Access Control Services Filter (by MS Open Tech)**, puis sur **Suivant**. La boîte de dialogue **Azure Access Control Services Filter** s'affiche. Un chemin différent peut être indiqué pour le champ **Location**, en fonction de l'emplacement d'installation d'Eclipse. Le numéro de version peut également être différent en fonction des mises à jour logicielles.

    ![Ajouter la bibliothèque de filtres ACS][Ajouter la bibliothèque de filtres ACS]

5.  En utilisant un navigateur ouvert sur la page **Login Page Integration** du portail de gestion, copiez l'URL indiquée dans le champ **Option 1: Link to an ACS-hosted login page**, puis collez-la dans le champ **ACS Authentication Endpoint** de la boîte de dialogue Eclipse.
6.  En utilisant un navigateur ouvert sur la page **Edit Relying Party Application** du portail de gestion, copiez l'URL indiquée dans le champ **Realm**, puis collez-la dans le champ **Relying Party Realm** de la boîte de dialogue Eclipse.
7.  Dans la section **Security** de la boîte de dialogue Eclipse, si vous souhaitez utiliser un certificat existant, cliquez sur **Browse**, accédez au certificat souhaité, sélectionnez-le, puis cliquez sur **Open**. Si vous préférez créer un certificat, cliquez sur **New** pour afficher la boîte de dialogue **New Certificate**, puis indiquez le mot de passe, le nom du fichier.cer et celui du fichier .pfx pour le nouveau certificat.
8.  Activez la case à cocher **Embed the certificate in the WAR file**. Intégrer le certificat de cette façon permet de l'inclure dans votre déploiement sans avoir besoin de l'ajouter manuellement comme composant. Si vous devez stocker votre certificat en externe depuis votre fichier WAR, vous pouvez ajouter le certificat en tant que rôle et désactiver la case à cocher **Embed the certificate in the WAR file**.
9.  [Facultatif] Laissez la case à cocher **Require HTTPS connections** activée. Si vous activez cette option, vous devrez accéder à votre application via le protocole HTTPS. Si vous ne souhaitez pas utiliser de connexions HTTPS, désélectionnez cette option.
10. Pour un déploiement vers l'émulateur de calcul, vos paramètres **Azure ACS Filter** doivent être similaires à l'exemple ci-dessous.

    ![Paramètres de filtre ACS Azure pour un déploiement sur l'émulateur de calcul][Paramètres de filtre ACS Azure pour un déploiement sur l'émulateur de calcul]

11. Cliquez sur **Terminer**.
12. Cliquez sur **Yes** dans la boîte de dialogue indiquant qu'un fichier web.xml va être créé.
13. Cliquez sur **OK** pour fermer la boîte de dialogue **Java Build Path**.

## <a name="deploy_compute_emulator"></a>Déploiement de l'émulateur de calcul

1.  Dans l'Explorateur de projets Eclipse, cliquez avec le bouton droit sur **MyACSHelloWorld**, cliquez sur **Azure**, puis sur **Package for Azure**.
2.  Dans **Project name**, entrez **MyAzureACSProject**, puis cliquez sur **Suivant**.
3.  Sélectionnez un JDK et un serveur d'applications. Ces étapes sont détaillées dans le didacticiel [Création d'une application Hello World pour Azure dans Eclipse][Création d'une application Hello World pour Azure dans Eclipse].
4.  Cliquez sur **Terminer**.
5.  Cliquez sur le bouton **Run in Azure Emulator**.
6.  Lorsque votre application web Java est lancée dans l'émulateur de calcul, fermez toutes les instances de votre navigateur (afin qu'aucune session de navigateur ne puisse perturber votre test de connexion à ACS).
7.  Exécutez votre application en ouvrant <http://localhost:8080/MyACSHelloWorld/> dans votre navigateur (ou <https://localhost:8080/MyACSHelloWorld/> si vous avez activé la case à cocher **Require HTTPS connections**). Un identifiant Windows Live ID vous est normalement demandé, puis vous êtes transféré vers l'URL de renvoi spécifiée dans votre application par partie de confiance.
8.  Lorsque vous avez terminé d'afficher votre application, cliquez sur le bouton **Reset Azure Emulator**.

## <a name="deploy_azure"></a>Déploiement sur Azure

Pour effectuer un déploiement sur Azure, vous devez changer le domaine de partie de confiance et l'URL de renvoi de votre espace de noms.

1.  Dans le portail de gestion Azure, sur la page **Edit Relying Party Application**, modifiez **Realm** et entrez l'URL de votre site déployé. Remplacez **example** par le nom DNS spécifié lors du déploiement.

    ![Domaine de partie de confiance à utiliser en production][Domaine de partie de confiance à utiliser en production]

2.  Modifiez la valeur **Return URL** et entrez l'URL de votre application. Remplacez **example** par le nom DNS spécifié lors du déploiement.

    ![URL de renvoi de partie de confiance à utiliser en production][URL de renvoi de partie de confiance à utiliser en production]

3.  Cliquez sur **Save** pour enregistrer les modifications apportées au domaine de partie de confiance et à l'URL de renvoi.
4.  Laissez la page **Login Page Integration** ouverte dans votre navigateur, car vous allez avoir besoin de copier certaines données qui s'y trouvent.
5.  Dans l'Explorateur de projets Eclipse, cliquez avec le bouton droit sur **MyACSHelloWorld**, cliquez sur **Build Path**, puis sur **Configure Build Path**.
6.  Cliquez sur l'onglet **Libraries**, sur **Azure Access Control Services Filter**, puis sur **Edit**.
7.  En utilisant un navigateur ouvert sur la page **Login Page Integration** du portail de gestion, copiez l'URL indiquée dans le champ **Option 1: Link to an ACS-hosted login page**, puis collez-la dans le champ **ACS Authentication Endpoint** de la boîte de dialogue Eclipse.
8.  En utilisant un navigateur ouvert sur la page **Edit Relying Party Application** du portail de gestion, copiez l'URL indiquée dans le champ **Realm**, puis collez-la dans le champ **Relying Party Realm** de la boîte de dialogue Eclipse.
9.  Dans la section **Security** de la boîte de dialogue Eclipse, si vous souhaitez utiliser un certificat existant, cliquez sur **Browse**, accédez au certificat souhaité, sélectionnez-le, puis cliquez sur **Open**. Si vous préférez créer un certificat, cliquez sur **New** pour afficher la boîte de dialogue **New Certificate**, puis indiquez le mot de passe, le nom du fichier.cer et celui du fichier .pfx pour le nouveau certificat.
10. Laissez l'option **Embed the certificate in the WAR file** activée si vous souhaitez intégrer le certificat dans le fichier WAR.
11. [Facultatif] Laissez la case à cocher **Require HTTPS connections** activée. Si vous activez cette option, vous devrez accéder à votre application via le protocole HTTPS. Si vous ne souhaitez pas utiliser de connexions HTTPS, désélectionnez cette option.
12. Pour un déploiement vers Azure, vos paramètres de filtre ACS Azure doivent être similaires à l'exemple ci-dessous.

    ![Paramètres de filtre ACS Azure pour un déploiement de production][Paramètres de filtre ACS Azure pour un déploiement de production]

13. Cliquez sur **Terminer** pour fermer la boîte de dialogue **Edit Library**.
14. Cliquez sur **OK** pour fermer la boîte de dialogue **Properties for MyACSHelloWorld**.
15. Dans Eclipse, cliquez sur le bouton **Publish to Azure Cloud**. Répondez aux invites, comme expliqué dans la section **Déploiement de votre application dans Azure** de la rubrique [Création d'une application Hello World pour Azure dans Eclipse][Création d'une application Hello World pour Azure dans Eclipse].

Une fois le déploiement de votre application web terminé, fermez les sessions de navigateur en cours, exécutez votre application web. Votre identifiant Windows Live ID doit vous être demandé. Vous êtes ensuite transféré vers l'URL de renvoi de votre application par partie de confiance.

Lorsque vous avez terminé d'utiliser votre application ACS Hello World, n'oubliez pas de supprimer le déploiement (la rubrique [Création d'une application Hello World pour Azure dans Eclipse][Création d'une application Hello World pour Azure dans Eclipse] explique comment procéder).

## <a name="next_steps"></a>Étapes suivantes

Pour étudier les données SAML (Security Assertion Markup Language) renvoyées par ACS vers votre application, consultez la rubrique [Affichage des données SAML renvoyées par ACS][Affichage des données SAML renvoyées par ACS]. Pour en savoir plus sur les fonctionnalités ACS et découvrir des scénarios plus complexes, consultez la page [Access Control Service 2.0][Access Control Service 2.0].

Cet exemple utilisait l'option **Embed the certificate in the WAR file** qui simplifie le déploiement du certificat. Si vous préférez maintenir le certificat de signature séparé du fichier WAR, vous pouvez utiliser la technique suivante :

1.  Dans la section **Security** de la boîte de dialogue **Azure Access Control Services Filter**, entrez **${env.JAVA\_HOME}/mycert.cer**, puis désactivez l'option **Embed the certificate in the WAR file** Mmodifiez mycert.cer si le nom de fichier du certificat est différent. Cliquez sur **Terminer** pour fermer la boîte de dialogue.
2.  Copiez le certificat comme élément de votre déploiement : Dans l'Explorateur de projets Eclipse, développez **MyAzureACSProject**, cliquez avec le bouton droit sur **WorkerRole1**, cliquez sur **Properties**, développez **Azure Role** et cliquez sur **Components**.
3.  Cliquez sur **Add**.
4.  Dans la boîte de dialogue **Add Component** :
    1.  Dans la section **Import** :
        1.  Utilisez le bouton **File** pour accéder au certificat que vous souhaitez utiliser.
        2.  Sous **Method**, sélectionnez **copy**.
    2.  Dans la section **As Name**, cliquez sur la zone de texte et acceptez le nom par défaut.
    3.  Dans la section **Deploy** :
        1.  Sous **Method**, sélectionnez **copy**.
        2.  Dans la zone **To directory**, entrez **%JAVA\_HOME%**.
    4.  La boîte de dialogue **Add Component** doit être similaire à la suivante :

        ![Ajouter un composant de certificat][Ajouter un composant de certificat]

    5.  Cliquez sur **OK**.

Votre certificat doit maintenant être inclus dans votre déploiement. Que le certificat soit intégré au fichier WAR ou ajouté en tant que composant au déploiement, vous devez télécharger le certificat dans votre espace de noms en suivant les instructions de la section [Téléchargement d'un certificat vers votre espace de noms ACS][Téléchargement d'un certificat vers votre espace de noms ACS].

<!-- Eclipse-specific -->

  [Étapes suivantes]: #next_steps
  [Concepts]: #concepts
  [Conditions préalables]: #pre
  [Création d'un espace de noms ACS]: #create-namespace
  [Ajout de fournisseurs d'identité]: #add-IP
  [Ajout d'une application par partie de confiance]: #add-RP
  [Création de règles]: #create-rules
  [Téléchargement d'un certificat vers votre espace de noms ACS]: #upload-certificate
  [Vérification de la page d'intégration d'application]: #review-app-int
  [Création d'une application web Java]: #create-java-app
  [Ajout de la bibliothèque de filtres ACS à votre application]: #add_acs_filter_library
  [Déploiement de l'émulateur de calcul]: #deploy_compute_emulator
  [Déploiement sur Azure]: #deploy_azure
  [Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
  [Diagramme de flux ACS]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png
  [Création d'une application Hello World pour Azure dans Eclipse]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh690944.aspx
  [portail de gestion Azure]: https://manage.windowsazure.com
  [Domaine de partie de confiance à utiliser dans l'émulateur de calcul]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
  [URL de retour vers partie de confiance pour utilisation dans l'émulateur de calcul]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
  [site web du projet]: http://wastarterkit4java.codeplex.com/releases/view/61026
  [Ajouter un certificat de signature de jeton]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png
  [Créer un projet Hello World pour l'exemple ACS]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
  [Ajouter un fichier JSP pour l'exemple ACS]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
  [Ajouter la bibliothèque de filtres ACS]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
  [Paramètres de filtre ACS Azure pour un déploiement sur l'émulateur de calcul]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
  [Domaine de partie de confiance à utiliser en production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
  [URL de renvoi de partie de confiance à utiliser en production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
  [Paramètres de filtre ACS Azure pour un déploiement de production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png
  [Affichage des données SAML renvoyées par ACS]: /fr-fr/develop/java/how-to-guides/view-saml-returned-by-acs/
  [Ajouter un composant de certificat]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
