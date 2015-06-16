<properties 
	pageTitle="Utiliser un connecteur SaaS à partir du code" 
	description="Apprenez à configurer un connecteur SaaS que vous installez dans votre abonnement Azure à partir d’Azure Marketplace." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="tdykstra"/>

# Utiliser un connecteur SaaS à partir du code

## Vue d'ensemble

Ce didacticiel montre l’installation, la configuration et le test d’un [connecteur SaaS (Software-as-a-Service)](../app-service-logic-what-are-bizTalk-api-apps.md) dans [Azure App Service](/documentation/services/app-service/) pour l’appeler par programme, par exemple à partir d’une application mobile. Un connecteur SaaS est une [application API](app-service-api-apps-why-best-platform.md) qui simplifie l’interaction avec les plateformes SaaS, comme Office 365, Salesforce, Facebook et Dropbox.

Par exemple, si vous souhaitez coder les demandes HTTP pour qu’elles lisent et écrivent des fichiers dans votre compte Dropbox, le processus d’authentification pour travailler directement avec Dropbox est complexe. Le connecteur Dropbox s’occupe de la partie complexe de l’authentification afin que vous puissiez vous concentrer sur l’écriture de votre code.

> [AZURE.WARNING]Les instructions fournies ici **ne doivent *pas* être suivies si vous souhaitez utiliser un connecteur SaaS à partir d’une application logique**. Consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md) pour plus d’informations sur l’utilisation de connecteurs SaaS dans les applications logiques. Cet article est propre à l’utilisation du *code* pour appeler votre connecteur.
 
Ce didacticiel utilise un connecteur DropBox à titre d’exemple et vous guide dans les étapes suivantes :

* Installez le connecteur Dropbox dans un [groupe de ressources](../resource-group-overview.md) dans votre abonnement Azure. 
* Configurez le connecteur Dropbox afin qu’il puisse se connecter au service Dropbox. (Pour cette étape, vous devez disposer d’un compte Dropbox.)
* Configurez le groupe de ressources afin que seuls les utilisateurs authentifiés puissent accéder aux applications API qui font partie du groupe de ressources.
* Faites des tests pour vérifier que l’authentification des utilisateurs et l’authentification Dropbox fonctionnent.

## Installer le connecteur Dropbox

1. Accédez à la page d’accueil du [portail Azure en version préliminaire] et cliquez sur **Marketplace**.

	![Marketplace dans le portail Azure en version préliminaire](./media/app-service-api-connnect-your-app-to-saas-connector/marketplace.png)

2. Recherchez Dropbox, puis cliquez sur l’icône **Connecteur Dropbox**.

	![Cliquez sur Connecteur Dropbox](./media/app-service-api-connnect-your-app-to-saas-connector/searchdb.png)
 
3. Cliquez sur **Create**.

	![Click Create](./media/app-service-api-connnect-your-app-to-saas-connector/clickcreate.png)
 
5. Dans le panneau **Connecteur Dropbox**, sous **Plan App Service**, cliquez sur **Créer nouveau**, puis, dans la boîte de dialogue **Créer un plan App Service**, entrez DropBoxPlan.

	Pour plus d’informations sur les plans Azure App Service, consultez la rubrique [Présentation détaillée des plans d’Azure App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Sous **Groupe de ressources**, cliquez sur **Créer**, puis, dans la boîte de dialogue **Créer un groupe de ressources**, entrez DropboxRG.

	Pour plus d’informations sur les groupes de ressources, consultez la rubrique [Utilisation des groupes de ressources pour gérer vos ressources Azure](../resource-group-overview.md).

7. Sélectionnez le **Niveau de tarification** Gratuit. (Si vous ne le voyez dans la liste, cliquez sur **Afficher tout**. Après avoir cliqué sur **F1 Gratuit**, cliquez sur le bouton **Sélectionner**.)

	Vous pouvez utiliser un niveau de tarification payant, mais il n’est pas nécessaire pour ce didacticiel.
 
11. Choisissez un **emplacement** proche de vous.

9. <a id="gateway"></a>Conservez la valeur par défaut « DropboxConnector » comme **nom** du connecteur, puis cliquez sur **Créer**.

	![Cliquez sur créer](./media/app-service-api-connnect-your-app-to-saas-connector/createdropbox.png)

	Azure App Service crée un groupe de ressources dans lequel il crée une application API de connecteur Dropbox et une application web *passerelle*. La fonction de cette passerelle est de gérer l’accès à toutes les applications API dans le groupe de ressources.

	Vous pouvez vérifier la progression de la création de la ressource en cliquant sur **Notifications** sur la page d’accueil du portail Azure en version préliminaire.

3. Après la création du connecteur par Azure, cliquez sur **Parcourir > Groupes de ressources > DropboxRG**.
 
	Le panneau **Groupe de ressources** de DropboxRG montre le connecteur et la passerelle dans le groupe de ressources.

	![Schéma du groupe de ressources](./media/app-service-api-connnect-your-app-to-saas-connector/rgdiagram.png)

## Configurer votre compte Dropbox et le connecteur Dropbox

Pour activer l’accès API à votre compte Dropbox, vous devez créer une application Dropbox sur le site des développeurs Dropbox. Copiez ensuite l’ID client et les valeurs de clé secrète client de cette application Dropbox vers votre connecteur Dropbox, puis configurez le connecteur pour accepter uniquement les demandes authentifiées.

### Créer une application Dropbox

Les étapes suivantes montrent le processus de création d’une application Dropbox à l’aide du site Dropbox.com. Comme le site Dropbox.com peut être modifié sans préavis, il est possible que vous constatiez des différences dans l’interface utilisateur par rapport à ce qui est présenté ici.

1. Accédez au [portail des développeurs Dropbox](https://www.dropbox.com/developers/apps), cliquez sur **App Console**, puis sur **Create App**.

	![Créer une application Dropbox](./media/app-service-api-connnect-your-app-to-saas-connector/dbappcreate.png)

2. Choisissez **Dropbox API app** et configurez les autres paramètres.
 
	Les options d’accès aux fichiers indiquées dans la capture d’écran ci-dessous vous permettent de tester l’accès à votre compte Dropbox avec une simple demande HTTP Get, si vous avez des fichiers dans votre compte.

	Le nom de l’application API Dropbox peut être n’importe quel nom accepté par le site Dropbox.

3. Cliquez sur **Create app**.

	![Créer une application Dropbox](./media/app-service-api-connnect-your-app-to-saas-connector/dbapiapp.png)

	La page suivante affiche les paramètres de clé et de clé secrète d’application (ID client et clé secrète client dans Azure) que vous utiliserez pour la configuration de votre connecteur Dropbox Azure.

	Elle contient également un champ dans lequel vous pouvez entrer un URI de redirection, dont vous obtenez la valeur dans la section suivante.

	![Créer une application Dropbox](./media/app-service-api-connnect-your-app-to-saas-connector/dbappsettings.png)

### Copier les paramètres d’application Dropbox dans le connecteur Dropbox Azure et vice-versa 

4. Dans une autre fenêtre de navigateur ou dans un autre onglet, accédez au [portail Azure en version préliminaire].

3. Accédez au panneau **Application API** de votre connecteur Dropbox. (Si vous êtes toujours dans le panneau **Groupe de ressources**, cliquez simplement sur le connecteur Dropbox dans le diagramme.)

4. Cliquez sur **Paramètres**, et dans le panneau **Paramètres**, cliquez sur **Authentification**.

	![Cliquez sur Paramètres](./media/app-service-api-connnect-your-app-to-saas-connector/clicksettings.png)

	![Cliquez sur Authentification](./media/app-service-api-connnect-your-app-to-saas-connector/clickauth.png)

5. Dans le panneau d’authentification, entrez l’ID client et la clé secrète client du site Dropbox, puis cliquez sur **Enregistrer**.

	![Entrez les paramètres et cliquez sur Enregistrer](./media/app-service-api-connnect-your-app-to-saas-connector/authblade.png)

3. Copiez l’**URI de redirection** (la zone grise au-dessus de la clé secrète client et de l’ID client) et ajoutez cette valeur à la page que vous avez laissée ouverte à l’étape précédente.

	L’URI de redirection suit ce modèle :

		[gatewayurl]/api/consent/redirect/[connectorname]

	Par exemple :

		https://dropboxrgaeb4ae60b7.azurewebsites.net/api/consent/redirect/DropboxConnector

	![Obtenir un URI de redirection](./media/app-service-api-connnect-your-app-to-saas-connector/redirecturi.png)

	![Créer une application Dropbox](./media/app-service-api-connnect-your-app-to-saas-connector/dbappsettings2.png)

### Définir le connecteur Dropbox pour qu’il demande un accès authentifié

Par défaut, le **niveau d’accès** du connecteur est défini sur **Interne**, ce qui signifie qu’il ne peut être appelé que par d’autres applications API et applications web du même groupe de ressources. Mais Dropbox autorise uniquement les utilisateurs authentifiés à accéder à leur compte Dropbox. Vous devez donc modifier le niveau d’accès pour demander l’authentification utilisateur.

1. Allez dans le panneau **Paramètres**, puis cliquez sur **Paramètres de l’application**.

2. Dans le panneau **Paramètres de l’application**, définissez le **Niveau d’accès** sur **Public (authentifié)**, puis cliquez sur **Enregistrer**.
	
	![Définir la valeur Public (authentifié)](./media/app-service-api-connnect-your-app-to-saas-connector/pubauth.png)

Vous avez configuré le connecteur Dropbox afin que les appels sortants puissent accéder à votre compte Dropbox et que les appels entrants proviennent d’utilisateurs authentifiés. Dans la section suivante, vous spécifiez le fournisseur d’authentification à utiliser pour authentifier les utilisateurs.

## Configurer la passerelle

Comme expliqué [précédemment](#gateway), la passerelle est une application web spéciale qui gère l’accès à toutes les applications API dans un groupe de ressources. Pour configurer la passerelle pour authentifier les utilisateurs, vous choisissez un fournisseur d’authentification, comme Azure Active Directory, Google ou Twitter. Les utilisateurs devront ensuite s’authentifier auprès du fournisseur choisi avant de pouvoir appeler correctement le connecteur Dropbox.

- Pour effectuer cette étape, allez dans la section [Configurer la passerelle](app-service-api-dotnet-add-authentication.md#configure-the-gateway) du didacticiel [Protéger une application API](app-service-api-dotnet-add-authentication.md) et suivez les instructions fournies pour configurer la passerelle dans votre groupe de ressources DropboxRG.

## Tester l’authentification de l’utilisateur et de Dropbox

Une fois que vous avez configuré la passerelle dans votre groupe de ressources DropboxRG pour qu’elle utilise un fournisseur d’authentification, vous pouvez tester votre connecteur Dropbox.

La plupart du temps, vous allez utiliser un connecteur en l’appelant à partir du code. Nous écrivons également des didacticiels qui vous montrent comment faire. Mais parfois, vous voulez vérifier que le connecteur fonctionne avant d’associer d’autres parties de l’application. Ce didacticiel montre comment utiliser un navigateur et un simple outil client REST pour vérifier que vous pouvez interagir avec le service Dropbox via le connecteur Dropbox que vous venez d’installer et de configurer.

Les instructions suivantes indiquent comment effectuer ces étapes à l’aide des outils de développement du navigateur Chrome et de l’outil client Postman REST. Il s’agit juste d’un exemple et vous pouvez faire la même chose avec d’autres navigateurs et d’autres outils.

### Connectez-vous en tant qu’un utilisateur final

Effectuez les étapes suivantes dans une nouvelle fenêtre du navigateur. En fonction du fournisseur d’authentification que vous utilisez, vous constaterez que vous devez utiliser une fenêtre privée ou incognito.

2. Accédez à l’URL de connexion de la passerelle et du fournisseur d’authentification que vous avez configurés. L’URL suit ce modèle : 

    	http://[gatewayurl]/login/[providername]

	Vous pouvez obtenir l’URL de la passerelle dans le panneau **Passerelle** du [portail Azure en version préliminaire]. (Pour accéder au panneau **Passerelle**, cliquez sur la passerelle dans le diagramme illustré dans le panneau **Groupe de ressources**.)

	![URL de la passerelle](./media/app-service-api-connnect-your-app-to-saas-connector/gatewayurl.png)

	La valeur de [providername] valeur est « facebook » pour Facebook, « twitter » pour Twitter, « aad » pour Azure Active directory, etc.

	Voici un exemple d’URL de connexion pour Azure Active Directory :

		https://dropboxrgaeb4ae60b7cb4f3d966dfa43.azurewebsites.net/login/aad/

3. Entrez vos informations d'identification quand le navigateur affiche une page de connexion.
 
	Si vous avez configuré la connexion Azure Active Directory, employez l’un des utilisateurs répertoriés sous l’onglet **Utilisateurs** pour l’application que vous avez créée sous l’onglet Azure Active Directory du [portail Azure], comme admin@contoso.onmicrosoft.com.

	Lorsque la connexion est réussie, vous obtenez une page « Connexion terminée ».

	![](./media/app-service-api-connnect-your-app-to-saas-connector/logindone.png)

### Fournir l’identité de l’utilisateur à Dropbox

Pour obtenir l’autorisation par Dropbox d’utiliser l’API Dropbox, vous devez fournir les informations d’identification de l’utilisateur à Dropbox. Azure effectue cette opération pour vous, mais pour déclencher le processus, vous devez accéder à une URL spécifique dans le navigateur. Pour obtenir cette URL, vous devez envoyer une demande HTTP Post à la passerelle.

Celle-ci doit comprendre le jeton d’authentification Azure fourni lors de la connexion. Pour les demandes de navigateur, le jeton est automatique, car il est stocké dans un cookie, mais pour une demande HTTP Post à l’aide d’un outil client REST, vous devez obtenir le jeton du cookie et le placer dans l’en-tête de la demande HTTP Post.

1. Dans la fenêtre du navigateur qui contient le message « Connexion terminée », accédez aux outils de développement du navigateur et recherchez le cookie `x-zumo-auth`. Laissez cette fenêtre ouverte afin de pouvoir copier la valeur du cookie à l’étape suivante.
 
	Pour obtenir la valeur du cookie dans Chrome, procédez comme suit :

	- Appuyez sur F12 pour ouvrir les outils de développement.
	- Accédez à l’onglet **Ressources**.
	- Recherchez les cookies de votre site de passerelle et cliquez trois fois sur la **valeur** du cookie `x-zumo-auth` pour le sélectionner. (Assurez-vous que vous copiez bien l’intégralité de la valeur du cookie. Si vous double-cliquez, vous risquez de ne récupérer que la première partie.)  

	![Copiez le jeton](./media/app-service-api-connnect-your-app-to-saas-connector/copytoken.png)

4. Dans une nouvelle fenêtre ou un nouvel onglet du navigateur, créez et envoyez une demande HTTP Post à la passerelle pour demander une URL de consentement. Ajoutez le jeton `x-zumo-auth` comme en-tête HTTP.

	L’URL suit ce modèle :

		[gatewayurl]/api/consent/list?api-version=2015-01-14&redirecturl=[a URL you want the browser to go to after you authenticate]

	Par exemple :

		https://dropboxrgaeb4ae60b7cb4f3d966dfa43.azurewebsites.net/api/consent/list?api-version=2015-01-14&redirecturl=https://portal.azure.com

	Pour envoyer la demande Postman dans Chrome, procédez comme suit :

	- Entrez l’**URL de la demande** décrite ci-dessus.
	- Définissez la méthode **Post**.
	- Ajoutez un en-tête `x-zumo-auth`.
	- Dans le champ **Valeur** de l’en-tête, collez la valeur copiée dans le cookie `x-zumo-auth`.
	- Cliquez sur **Envoyer**.
	 
	L’illustration suivante présente l’outil Postman dans Chrome :

	![Envoyez l’URL de consentement](./media/app-service-api-connnect-your-app-to-saas-connector/sendforconsent.png)

	La réponse comprend une URL à utiliser pour authentifier l’utilisateur auprès de Dropbox. (Si vous obtenez une erreur qui indique que la méthode Get n’est pas prise en charge, même si la méthode est définie sur **Post** dans la liste déroulante, vous devrez peut-être utiliser un autre outil client REST. « Advanced REST Client » est un autre complément Chrome que vous pouvez utiliser.)

	![URL de consentement](./media/app-service-api-connnect-your-app-to-saas-connector/getconsenturl.png)

2. Accédez à l’URL que vous avez reçue en réponse à la demande HTTP Post.

	Dropbox associe votre identité utilisateur à votre application API Dropbox, puis redirige le navigateur vers l’URL de redirection que vous avez spécifiée (par exemple, le portail Azure en version préliminaire si vous avez suivi l’exemple et utilisé https://portal.azure.com).

	Comme votre application Dropbox est en mode développeur, vous risquez d’obtenir une page de connexion Dropbox avant que le navigateur ne passe à l’URL de redirection. Après vous être connecté à l’aide de vos informations d’identification Dropbox, l’identité utilisateur que vous avez utilisée pour vous connecter à la passerelle est associée à votre application Dropbox. À l’avenir, cette étape de connexion Dropbox n’est plus requise pour cette identité.

3. Ne fermez pas cette fenêtre du navigateur, vous allez l’utiliser dans la section suivante.

### Appeler l’API du connecteur Dropbox 

Azure gère désormais trois jetons d’authentification pour vous :

- Un pour le fournisseur d’authentification que vous avez configuré pour la passerelle, par exemple Azure Active Directory.
- Un pour Dropbox.
- Un créé par Azure (le jeton « zumo »).

Le seul que vous devez utiliser dans vos demandes HTTP pour travailler avec Dropbox est le jeton zumo. En coulisse, Azure associe ce jeton aux deux autres et le connecteur fournit les deux autres jetons en votre nom lorsqu’il effectue des appels à Dropbox.

Dans les étapes suivantes, vous allez envoyer une demande Get au connecteur Dropbox pour inspecter les fichiers de votre compte Dropbox. Étant donné que vous pouvez utiliser une fenêtre de navigateur pour une demande Get et que la fenêtre du navigateur a déjà le jeton zumo dans un cookie, tout ce que vous avez à faire est d’accéder à une URL et de récupérer les données Dropbox.

1. Dans la fenêtre de navigateur qui a ouvert le portail Azure en version préliminaire, revenez au panneau **Application API** de votre connecteur Dropbox. 

2. Copiez l’URL de l’application API.
 
4. Cliquez sur l’icône **Définition de l’API** pour afficher les méthodes d’API disponibles dans le connecteur.

	![Panneau Application API](./media/app-service-api-connnect-your-app-to-saas-connector/apiappblade.png)

	![Définition de l’API](./media/app-service-api-connnect-your-app-to-saas-connector/apidef.png)

2. Dans la fenêtre du navigateur que vous avez utilisée pour vous authentifier auprès de la passerelle, appelez la méthode GET, qui récupère les noms de fichiers à partir d’un dossier. Voici le modèle d’URL :

		[connectorurl]/folder/[foldername]
   
3. Par exemple, si l’URL de votre connecteur est https://dropboxrg784237ad3e7.azurewebsites.net et que vous souhaitez voir les fichiers du dossier de votre blog, l’URL est :

		https://dropboxrg784237ad3e7.azurewebsites.net/folder/blog

	Les différents navigateurs répondent aux appels d’API de façon différente. Si vous utilisez Chrome, vous verrez une réponse similaire à celle de l’exemple suivant :

	![Obtenir la réponse à une demande de dossier](./media/app-service-api-connnect-your-app-to-saas-connector/dbresponse.png)

## Étapes suivantes

Vous avez vu comment installer, configurer et tester un connecteur SaaS. Pour plus d’informations, consultez la page [Utilisation de connecteurs](../app-service-logic/app-service-logic-use-biztalk-connectors.md).

[portail Azure en version préliminaire]: https://portal.azure.com/
[portail Azure]: https://manage.windowsazure.com/
<!--HONumber=54--> 