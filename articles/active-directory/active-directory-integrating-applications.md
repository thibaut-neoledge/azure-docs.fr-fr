<properties
   pageTitle="Intégration d'applications dans Azure Active Directory | Microsoft Azure"
	description="Comment ajouter, mettre à jour ou supprimer une application dans Azure Active Directory (Azure AD)."
	services="active-directory"
	documentationCenter=""
	authors="msmbaldwin"
	manager="mbaldwin"
	editor="mbaldwin"/>
<tags
   ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="08/25/2015"
	ms.author="mbaldwin"/>

# Intégration d'applications dans Azure Active Directory
Les développeurs et les fournisseurs de logiciels en tant que service (SaaS) peuvent développer des services cloud commerciaux ou des applications métier qui peuvent être intégrés à Azure Active Directory (Azure AD) pour fournir un processus de connexion et d’autorisation sécurisé à leurs services. Pour intégrer une application ou un service à Azure AD, le développeur doit d'abord enregistrer les détails relatifs à son application avec Azure AD à l'aide du portail de gestion Azure.

Cet article vous montre comment ajouter, mettre à jour ou supprimer une application dans Azure AD. Vous découvrirez les différents types d'applications qui peuvent être intégrées à Azure AD, comment configurer vos applications pour accéder aux autres ressources telles que les API web, et bien plus encore.

Pour plus d'informations sur les propriétés de l'application, consultez la section [Objets application et principal du service](active-directory-application-objects.md) ; pour connaître les directives de personnalisation à utiliser lors du développement d'applications avec Azure Active Directory, consultez la section [Directives de personnalisation pour les applications intégrées](active-directory-branding-guidelines.md).

## Ajout d'une application

Toute application qui souhaite utiliser les fonctionnalités d'Azure AD doit d'abord être enregistrée dans un répertoire. Le processus d'enregistrement implique de fournir à Azure AD des informations sur votre application, notamment l'URL où elle est située, l'URL à laquelle envoyer une réponse après avoir authentifié un utilisateur, l'URI qui identifie l'application, et ainsi de suite.

Si vous créez une application web qui a juste besoin de l’authentification des utilisateurs dans Azure AD, il vous suffit de suivre les instructions ci-dessous. Si votre application doit accéder à une API web, que vous créez une application native qui doit accéder à une API web ou que vous souhaitez rendre votre application mutualisée, vous devrez poursuivre votre lecture dans la section [Mise à jour d'une application](#updating-an-application) pour continuer la configuration de votre application.

Si vous souhaitez rendre votre application disponible à d'autres organisations, vous devez également activer l'accès externe une fois l'application ajoutée.

### Pour enregistrer une application dans le portail de gestion Azure

1. Connectez-vous au portail de gestion Azure.

1. Cliquez sur l'icône d'Active Directory dans le menu de gauche, puis cliquez sur le répertoire souhaité.

1. Dans le menu supérieur, cliquez sur Applications. Si aucune application n’a été ajoutée à votre répertoire, cette page affiche uniquement le lien Ajouter une application. Cliquez sur le lien. Vous pouvez également cliquer sur le bouton Ajouter dans la barre de commandes.

1. Sur la page Que voulez-vous faire, cliquez sur le lien vers Ajouter une application développée par mon organisation.

1. Sur la page Parlez-nous de votre application, vous devez spécifier un nom pour votre application et indiquer le type d'application que vous enregistrez avec Azure AD. Vous pouvez choisir entre une application web et/ou une API web (par défaut) et une application cliente native qui représente une application installée sur un périphérique tel qu'un téléphone ou un ordinateur. Une fois que vous avez terminé, cliquez sur l'icône en forme de flèche dans l'angle inférieur droit de la page.

1. Sur la page des propriétés de l’application, fournissez l'URL d'authentification et l'URI ID d'application de votre application web (ou simplement l'URI de redirection pour une application cliente native), puis cliquez sur la case à cocher dans le coin inférieur droit de la page.

1. Votre application a été ajoutée, et vous allez être redirigé vers la page de démarrage rapide pour votre application. Selon que votre application est une application native ou web, vous verrez des options différentes d'ajout de fonctionnalités supplémentaires à votre application. Une fois votre application ajoutée, vous pouvez commencer la mise à jour de votre application pour permettre aux utilisateurs de se connecter, accéder à des API web dans d'autres applications ou configurer l'application mutualisée (ce qui permet à d'autres organisations d’accéder à votre application).

>[AZURE.NOTE]Par défaut, l'inscription de l'application nouvellement créée est configurée pour autoriser les utilisateurs de votre répertoire à se connecter à votre application.

## Mise à jour d'une application

Une fois votre application inscrite avec Azure AD, il est possible qu’elle doive être mise à jour pour fournir un accès aux API web, être mise à la disposition d'autres organisations, et plus encore. Cette section décrit comment aller plus loin dans la configuration de votre application. Pour plus d'informations sur le fonctionnement de l'authentification dans Azure AD, consultez la section [Scénarios d'authentification pour Azure AD](active-directory-authentication-scenarios.md).

### Vue d’ensemble de l’infrastructure de consentement

La nouvelle infrastructure de consentement d'Azure AD facilite le développement d’applications natives et web qui doivent accéder à des API web sécurisées par Azure AD. Ces API web incluent l'API Graph, Office 365 et d’autres services Microsoft, en plus de vos propres API web. L'infrastructure est basée sur le consentement d’un utilisateur ou d’un administrateur à l’inscription d’une application dans son répertoire, ce qui peut impliquer l'accès aux données du répertoire. Par exemple, si une application web doit appeler l’API web Office 365 pour lire les informations de calendrier de l'utilisateur, cet utilisateur devra donner son consentement à l'application. Une fois le consentement donné, l'application web sera en mesure d'appeler l'API web Office 365 au nom de l'utilisateur et d’utiliser les informations de calendrier en fonction des besoins.

L'infrastructure de consentement est conçue sur OAuth 2.0 et ses différents flux, notamment l’octroi d’un code d’autorisation et d’informations d'identification du client, à l'aide de clients publics ou confidentiels. En utilisant OAuth 2.0, Azure AD permet de créer de nombreux types d'applications clientes, sur téléphone, tablette, serveur ou web, et d'accéder aux ressources requises.

Pour plus d'informations sur l'infrastructure de consentement, consultez la section [OAuth 2.0 dans Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx), [Scénarios d'authentification pour Azure AD](active-directory-authentication-scenarios.md), et la rubrique sur Office 365 [Authentification et autorisation à l'aide de la structure commune de consentement](https://msdn.microsoft.com/library/office/dn605895(v=office.15).aspx).

#### Exemple d’expérience de consentement

Les étapes suivantes vous montrent comment l’expérience de consentement fonctionne à la fois pour le développeur d'applications et pour l'utilisateur.

1. Sur la page de configuration de votre application, dans le portail de gestion Azure, définissez les autorisations demandées par votre application en utilisant les menus déroulants dans la commande Autorisations pour d’autres applications.

    ![Autorisations pour d'autres applications](./media/active-directory-integrating-applications/permissions.png)

1. Considérons que les autorisations de votre application ont été mises à jour, que l'application s'exécute et qu’un utilisateur s'apprête à l’utiliser pour la première fois. Si l'application n'a pas encore acquis de jeton d’accès ou d’actualisation, l'application a besoin d’accéder au point de terminaison de l'autorisation d'Azure AD pour obtenir un code d'autorisation qui peut servir à acquérir un nouveau jeton d’accès et d’actualisation.

1. Si l'utilisateur n'est pas déjà authentifié, il sera invité à se connecter à Azure AD.

    ![Connexion d’un utilisateur ou d’un administrateur à Azure AD](./media/active-directory-integrating-applications/useradminsignin.png)

1. Une fois l'utilisateur connecté, Azure AD déterminera s’il est nécessaire d'afficher une page de consentement pour l’utilisateur. La détermination est différente selon que l’utilisateur (ou l’administrateur de son organisation), a déjà accordé ou non son consentement à l'application. Si l'autorisation n'a pas encore été accordée, Azure AD invitera l'utilisateur à fournir son consentement et affichera les autorisations dont il a besoin pour fonctionner. Le jeu d'autorisations qui s'affiche dans la boîte de dialogue du consentement est identique à ce qui a été sélectionné dans la commande Autorisations pour d’autres applications dans le portail de gestion Azure.

    ![Expérience de consentement de l'utilisateur](./media/active-directory-integrating-applications/userconsent.png)

1. Une fois que l'utilisateur a donné son consentement, un code d'autorisation est retourné à votre application, qui peut être utilisé pour acquérir un jeton d'accès et un jeton d'actualisation. Pour plus d’informations sur ce flux, consultez la section [Application web vers API web](active-directory-authentication-scenarios.md#web-application-to-web-api) dans [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md).

### Accès aux API web dans d'autres applications

À l'aide de l'infrastructure de consentement décrite ci-dessus, vous pouvez configurer votre application afin qu’elle exige des autorisations d'accès aux données exposées par les API web dans votre répertoire. Par défaut, toutes les applications peuvent choisir des autorisations d'Azure Active Directory (API Graph) et de l’API Azure Service Management, avec l'autorisation « Activer l'authentification et lire le profil de l’utilisateur » d’Azure AD déjà sélectionnée par défaut. Si votre répertoire comporte également un abonnement à Office 365, les API web et les autorisations pour SharePoint et Exchange Online pourront également être sélectionnées. Vous pouvez sélectionner deux types d'autorisations dans les listes déroulantes en regard de l'API web souhaitée :

- Autorisations de l'application : votre application doit accéder à l'API web directement en tant que telle (aucun contexte utilisateur). Ce type d'autorisation requiert le consentement de l'administrateur et n'est également pas disponible pour les applications clientes natives.

- Autorisations de délégation : votre application doit accéder à l'API web en tant qu'utilisateur connecté, mais avec un accès limité par l'autorisation sélectionnée. Ce type d'autorisation peut être accordé par un utilisateur, à moins que l'autorisation ne soit configurée comme nécessitant le consentement de l'administrateur.

#### Pour ajouter l’accès aux API web dans d’autres applications

1. Connectez-vous au portail de gestion Azure.

1. Cliquez sur l'icône d'Active Directory dans le menu de gauche, puis cliquez sur le répertoire souhaité.

1. Dans le menu supérieur, cliquez sur Applications, puis cliquez sur l'application que vous souhaitez configurer. La page de démarrage rapide s'affiche avec l'authentification unique et d'autres informations de configuration.

1. Développez Accéder aux API web dans la section Autres applications du démarrage rapide, puis cliquez sur le lien Configurer maintenant sous la section Sélectionner les autorisations. La page des propriétés de l'application s'affiche.

1. Faites défiler jusqu’à la section Autorisations pour d'autres applications. La première colonne vous permet de sélectionner les applications disponibles dans le répertoire qui expose une API web. Une fois la sélection faite, vous pouvez sélectionner les autorisations d'application et de délégation qu’expose l'API web.

1. Une fois la sélection faite, cliquez sur le bouton Enregistrer dans la barre de commandes.

>[AZURE.NOTE]Une fois que vous avez cliqué sur le bouton Enregistrer, les autorisations pour votre application dans votre répertoire sont automatiquement basées sur les Autorisations pour les autres applications que vous avez configurées. Vous pouvez afficher ces autorisations de l'application dans l'onglet des propriétés de l'application.

### Exposition d’API web vers d'autres applications

Vous pouvez développer une API web et la mettre à disposition d'autres organisations en exposant l’étendue de vos autorisations à d'autres développeurs d'applications. Une API web correctement configurée peut être mise à disposition tout comme les autres API web Microsoft, notamment l'API Graph et les API Office 365. Votre API web est mise à disposition en configurant un manifeste d'application, qui consiste en un fichier JSON représentant la configuration de l'identité de votre application. Vous pouvez exposer l’étendue de vos autorisations en accédant à votre application dans le portail de gestion Azure, puis en cliquant sur le bouton du manifeste d'application dans la barre de commandes.

#### Pour exposer une API web à d’autres applications

1. Connectez-vous au portail de gestion Azure.

1. Cliquez sur l'icône d'Active Directory dans le menu de gauche, puis cliquez sur le répertoire souhaité.

1. Dans le menu supérieur, cliquez sur Applications, puis cliquez sur l'application que vous souhaitez configurer. La page de démarrage rapide s'affiche avec l'authentification unique et d'autres informations de configuration.

1. Cliquez sur le bouton Gérer le manifeste dans la barre de commandes, puis sélectionnez Télécharger le manifeste.

1. Ouvrez le fichier du manifeste de l'application JSON et remplacez le nœud « oauth2Permissions » par l'extrait de code JSON suivant. Cet extrait de code est un exemple de façon d’exposer une portée d'autorisation appelée emprunt d'identité de l'utilisateur. Veillez à modifier le texte et les valeurs en fonction de votre propre application :

		"oauth2Permissions": [
		{
			"adminConsentDescription": "Allow the application full access to the Todo List service on behalf of the signed-in 	user",
			"adminConsentDisplayName": "Have full access to the Todo List service",
			"id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
			"isEnabled": true,
			“origin”: “Application”
			"type": "User",
			"userConsentDescription": "Allow the application full access to the todo service on your behalf",
			"userConsentDisplayName": "Have full access to the todo service",
			"value": "user_impersonation"
			}
		],

    La valeur d'ID doit être un nouveau GUID, créé à l'aide d'un outil de génération de GUID ou d’un programme. Elle représente un identificateur unique pour l'autorisation exposée par l'API web. Une fois votre client correctement configuré pour demander l'accès à votre API web et pour appeler l'API web, il présentera un jeton OAuth 2.0 JWT qui a la revendication d’étendue définie à la valeur ci-dessus, dans ce cas user\_impersonation.

	>[AZURE.NOTE]Vous pouvez exposer des portées d’autorisations supplémentaires ultérieurement si nécessaire. Considérez que votre API web peut exposer plusieurs autorisations associées à un éventail de fonctions différentes. Vous pouvez maintenant contrôler l'accès à l'API web à l'aide de la revendication d'étendue dans le jeton OAuth 2.0 JWT reçu.

1. Enregistrez le fichier JSON mis à jour et téléchargez-le en cliquant sur le bouton Gérer le manifeste dans la barre de commandes, en sélectionnant Télécharger le manifeste, en accédant à votre fichier de manifeste mis à jour puis en le sélectionnant. Une fois le fichier téléchargé, votre API web est configurée pour être utilisée par d'autres applications dans votre répertoire.

#### Pour vérifier que l’API web est exposée à d’autres applications dans votre répertoire

1. Dans le menu supérieur, cliquez sur Applications, sélectionnez l'application dont vous souhaitez configurer l'accès à l'API web, puis cliquez sur Configurer.

1. Faites défiler jusqu’à la section Autorisations pour d'autres applications. Cliquez sur la liste déroulante Sélectionner l'application : vous pourrez sélectionner l'API web pour laquelle vous avez exposé une autorisation. Dans la liste déroulante Autorisations de délégation, sélectionnez la nouvelle autorisation.

![Les autorisations de la liste des tâches sont affichées.](./media/active-directory-integrating-applications/listpermissions.png)

#### Schéma AppPermissions pour le fichier JSON du manifeste d’application

Le tableau suivant répertorie les valeurs possibles pour la partie oauth2Permissions du fichier JSON du manifeste d'application.

|Élément|Description|
|---|---|
|adminConsentDescription|La description de l'aide dans la zone de pointage de la boîte de dialogue de consentement de l'administrateur et sur la page des propriétés de l'application faisant l’objet du consentement.|
|adminConsentDisplayName|Le nom convivial de l'autorisation affiché dans les listes déroulantes d’étendue des autorisations d’application et/ou de délégation, ainsi qu’aux administrateurs lors du consentement et sur la page des propriétés de l'application.|
|id|Représente un identificateur unique interne pour l'étendue de l'autorisation. Il doit être unique sur l’ensemble des autorisations de l'application, et il doit également être un GUID.|
|isEnabled|Lors de la création ou de la mise à jour d'une autorisation OAuth2, définissez-la toujours sur true. Si vous souhaitez supprimer une autorisation, vous devez tout d'abord définir cette valeur sur false et télécharger le manifeste. Vous pouvez ensuite supprimer l'autorisation lors d’un téléchargement de manifeste ultérieur.|
|origin|Réservé pour une utilisation ultérieure. Toujours défini sur « application ».|
|type|Peut prendre l'une des valeurs suivantes : « Utilisateur » : consentement possible par les utilisateurs finaux « Admin »: consentement obligatoire par un administrateur d'entreprise|
|userConsentDescription|La description de l'aide dans la zone de pointage dans la boîte de dialogue de consentement de l'utilisateur.|
|userConsentDisplayName|Le nom convivial pour l'autorisation affiché aux utilisateurs finaux lors du consentement, et dans la page des propriétés de l'application dans le volet d'accès à l’application.|
|value|Cette valeur est placée dans la revendication d’étendue du jeton d'accès OAuth 2.0 si cette autorisation particulière a été consentie par l'utilisateur. L'API web peut utiliser cette valeur pour définir l'étendue du niveau d'accès dont l'application dispose en empruntant l'identité de l'utilisateur. Elle ne doit pas contenir d’espaces et doit être unique parmi toutes les autorisations de l'application.|

### Accès à l'API Graph

Cette section décrit comment mettre à jour votre application pour accéder à l'API Graph, appelée « Azure Active Directory » dans la liste des Autorisations pour d'autres applications. Elle est disponible par défaut pour toutes les applications inscrites avec Azure AD. Vous pouvez choisir parmi les autorisations suivantes avec l'API Graph :

|Nom de l’autorisation|Description|Type|
|---|---|---|
|Activer l'authentification et lire les profils utilisateur|Autoriser les utilisateurs à se connecter à l'application avec leur compte professionnel et laisser l'application lire les profils des utilisateurs connectés, notamment leur adresse de messagerie et leurs informations de contact.|Autorisation de délégation uniquement. Peut être acceptée par les utilisateurs.|
|Accéder au répertoire de votre organisation|Autoriser l'application à accéder au répertoire de votre organisation au nom de l'utilisateur connecté.|Autorisation de délégation uniquement. Peut être acceptée par les utilisateurs dans un client natif et uniquement par un administrateur pour les applications web.|
|Lire les données du répertoire|Autoriser l'application à lire les données du répertoire de votre organisation, notamment les utilisateurs, les groupes et les applications.|Autorisation de délégation et d’application. Doit être acceptée par un administrateur.|
|Lire et écrire les données du répertoire|Autoriser l'application à lire et à écrire les données du répertoire de votre organisation, notamment les utilisateurs et les groupes.|Autorisation de délégation et d’application. Doit être acceptée par un administrateur.|

Pour les utilisateurs existants du portail de gestion Azure, il est équivalent de définir les autorisations de l’application Lire les données du répertoire et Lire et écrire les données du répertoire avec la nouvelle commande Autorisations pour d’autres applications par rapport à l’assistant précédent de gestion des accès. Pour afficher l’étendue des autorisations exposées par Office 365, consultez la rubrique [Authentification et autorisation à l’aide de l’infrastructure commune de consentement](https://msdn.microsoft.com/office/office365/howto/common-app-authentication-tasks).

>[AZURE.NOTE]En raison d'une limitation actuelle, les applications clientes natives ne peuvent appeler l'API Graph Azure AD que si elles utilisent l'autorisation « Accéder au répertoire de votre organisation ». Cette restriction ne s'applique pas aux applications web.

### Configuration d'applications mutualisées

Lorsque vous ajoutez une application à Azure AD, vous pouvez choisir de rendre votre application accessible uniquement aux utilisateurs de votre organisation. Vous pouvez également rendre votre application accessible aux utilisateurs d'organisations externes. Ces deux types d’applications sont appelés applications à client unique et applications mutualisées. Vous pouvez modifier la configuration d'une application à client unique pour en faire une application mutualisée, ce que cette section décrit ci-dessous.

Il est important de noter les différences entre une application à client unique et une application mutualisée. Une application à client unique est prévue pour une utilisation dans une seule organisation. Il s’agit généralement d’une application métier écrite par un développeur de l’entreprise. Une application à client unique doit être accessible uniquement aux utilisateurs d’un annuaire et, en conséquence, ne doit être approvisionnée que dans un seul annuaire. Une application mutualisée est prévue pour une utilisation dans plusieurs organisations. Il s’agit généralement d’une application SaaS (software-as-a-service) écrite par un éditeur de logiciels indépendant. Les applications mutualisées doivent être approvisionnées dans chaque annuaire dans lequel elles sont utilisées, ce qui suppose le consentement d’un utilisateur ou d’un administrateur pour les inscrire.

#### Permettre aux utilisateurs externes d’accorder l’accès à l’application

Si vous écrivez une application que vous souhaitez proposer à vos clients ou à des partenaires externes à votre organisation, vous devez mettre à jour la définition de l'application dans le portail de gestion Azure.

>[AZURE.NOTE]Lorsque vous activez l'accès externe, vous devez vous assurer que l’URI ID de votre application appartient à un domaine vérifié. En outre, l'URL de renvoi doit commencer par https://. Pour plus d’informations, consultez [Objets application et principal du service](active-directory-application-objects.md).

##### Pour autoriser les utilisateurs externes à accéder à votre application

1. Connectez-vous au portail de gestion Azure.

1. Cliquez sur l'icône d'Active Directory dans le menu de gauche, puis cliquez sur le répertoire souhaité.

1. Dans le menu supérieur, cliquez sur Applications, puis cliquez sur l'application que vous souhaitez configurer. La page de démarrage rapide s'affiche avec l'authentification unique et d'autres informations de configuration.

1. Développez la section Configurer une application mutualisée de la zone de démarrage rapide, puis cliquez sur le lien Configurer maintenant dans la section Activer l'accès. La page des propriétés de l'application s'affiche.

1. Cliquez sur le bouton Oui en regard de L’application est mutualisée, puis cliquez sur le bouton Enregistrer dans la barre de commandes.

Une fois que vous avez apporté les modifications ci-dessus, les utilisateurs et les administrateurs d’autres organisations pourront accorder à votre application l'accès à leur répertoire et à d'autres données.

### Octroi de l’accès à l'aide de l'infrastructure de consentement

Pour accorder l’accès à l'aide de l'infrastructure de consentement, l'application cliente doit demander l'autorisation avec d'OAuth 2.0. Des [exemples de code](https://github.com/AzureADSamples) sont disponibles pour vous montrer comment une application web, une application native ou une application démon/serveur demande des codes d’autorisation et des jetons d’accès pour appeler des API web.

Votre application web peut peut-être offrir une expérience d'inscription aux utilisateurs. Si vous offrez une expérience d'inscription, l'utilisateur devra cliquer sur un bouton d’inscription (ou de connexion) qui redirigera le navigateur vers le point de terminaison d’autorisation AD Azure OAuth2.0 ou un point de terminaison userinfo OpenID Connect. Ces points de terminaison permettent à l'application d’obtenir des informations sur le nouvel utilisateur en inspectant l'id\_token.

Sinon, votre application web peut également offrir une expérience permettant aux administrateurs d’« inscrire ma société ». Cette expérience redirige également l'utilisateur vers le point de terminaison d’autorisation AD OAuth Azure 2.0. Dans ce cas, vous pouvez également transmettre un paramètre prompt=admin\_consent pour déclencher l'expérience de consentement de l’administrateur, où l'administrateur accorde son consentement pour le compte de son organisation. En cas de consentement réussi, la réponse contiendra admin\_consent=true. Lors de l’échange d’un jeton d'accès, vous recevrez également un id\_token qui fournira des informations sur l'organisation et sur l'administrateur qui s’est inscrit à votre application.

#### Activation de l’accord implicite OAuth 2.0 pour les applications à page unique

Les applications à page unique (SPA) se composent généralement d’une partie frontale reposant largement sur JavaScript qui s’exécute dans le navigateur. Celle-ci appelle l’API web principale de l’application pour effectuer sa logique métier. Pour les applications à page unique hébergées dans Azure AD, l’accord implicite OAuth 2.0 permet d’authentifier l’utilisateur auprès d’Azure AD et d’obtenir un jeton qui peut servir à sécuriser les appels du client JavaScript de l’application à son API web principale. Une fois que l’utilisateur a donné son consentement, ce même protocole d’authentification peut servir à obtenir des jetons pour sécuriser les appels entre le client et d’autres ressources API web configurées pour l’application. Par défaut, l’accord implicite OAuth 2.0 est désactivé pour les applications. Vous pouvez activer l’accord implicite OAuth 2.0 pour votre application en définissant la valeur `oauth2AllowImplicitFlow`” dans son manifeste d’application (fichier JSON représentant la configuration de l’identité de votre application).

##### Pour activer l’accord implicite OAuth 2.0

1. Connectez-vous au portail de gestion Azure.
1. Cliquez sur l’icône **Active Directory** dans le menu de gauche, puis cliquez sur l’annuaire souhaité.
1. Dans le menu supérieur, cliquez sur **Applications**, puis sur l’application à configurer. La page de démarrage rapide s'affiche avec l'authentification unique et d'autres informations de configuration.
1. Cliquez sur le bouton **Gérer le manifeste** dans la barre de commandes, puis sélectionnez **Télécharger le manifeste**. Ouvrez le fichier manifeste d’application JSON et affectez à « oauth2AllowImplicitFlow » la valeur « true ». La valeur par défaut est « false ».

       "oauth2AllowImplicitFlow": true,

1. Enregistrez le fichier JSON mis à jour et téléchargez-le. Pour cela, cliquez sur le bouton **Gérer le manifeste** dans la barre de commandes, sélectionnez **Télécharger le manifeste**, accédez à votre fichier de manifeste mis à jour, puis sélectionnez-le. Une fois le fichier téléchargé, votre API web est configurée pour utiliser l’accord implicite OAuth 2.0 pour authentifier les utilisateurs.


### Expériences héritées pour accorder l'accès

Cette section décrit l'expérience de consentement héritée avant le 12 mars 2014. Cette expérience est toujours prise en charge et est décrite ci-dessous. Avant la nouvelle fonctionnalité, vous pouviez accorder uniquement les autorisations suivantes :

- Authentifier les utilisateurs à partir de leur organisation

- Authentifier les utilisateurs et lire les données du répertoire de leur organisation (comme l'application uniquement)

- Authentifier les utilisateurs et lire et écrire les données du répertoire de leur organisation (comme l'application uniquement)

Vous pouvez suivre les étapes décrites dans la section [Développement d’applications web mutualisées avec Azure AD](https://msdn.microsoft.com/library/azure/dn151789.aspx) pour octroyer l’accès à de nouvelles applications inscrites dans Azure AD. Il est important de noter que la nouvelle infrastructure de consentement prend en charge des applications beaucoup plus puissantes et permet également aux utilisateurs de donner leur consentement à ces applications, et non simplement aux administrateurs.

#### Création du lien d’octroi de l’accès pour les utilisateurs externes (procédure héritée)

Afin de permettre à des utilisateurs externes de s’inscrire à votre application avec leur compte professionnel, vous devez mettre à jour votre application de façon à ce qu’elle affiche un bouton établissant un lien vers la page d’Azure AD qui leur permet d'accorder l'accès. Des conseils de personnalisation de ce bouton d’inscription sont abordés dans la rubrique [Instructions de personnalisation pour les applications intégrées](active-directory-branding-guidelines.md). Une fois que l'utilisateur a accordé ou refusé l'accès, la page d'octroi de l’accès d’Azure AD redirige le navigateur vers votre application avec une réponse. Pour plus d’informations sur les propriétés de l’application, consultez [Objets application et principal du service](active-directory-application-objects.md).

La page d’octroi de l'accès est créée par Azure AD, et vous trouverez un lien vers celle-ci sur la page de configuration de votre application dans le portail de gestion. Pour accéder à la page de configuration, cliquez sur le lien Applications dans le menu supérieur de votre locataire Azure AD, cliquez sur l'application que vous souhaitez configurer, puis cliquez sur Configurer dans le menu supérieur de la page de démarrage rapide.

Le lien de votre application ressemble à ceci : `http://account.activedirectory.windowsazure.com/Consent.aspx?ClientID=058eb9b2-4f49-4850-9b78-469e3176e247&RequestedPermissions=DirectoryReaders&ConsentReturnURL=https%3A%2F%2Fadatum.com%2FExpenseReport.aspx%3FContextId%3D123456`. Le tableau suivant décrit les éléments du lien :

|Paramètre|Description|
|---|---|
|ClientId|Obligatoire. L'ID de client que vous avez obtenu dans le cadre de l'ajout de votre application.|
|RequestedPermissions|facultatif. Niveau d'accès que demande votre application, qui sera affiché à l'utilisateur accordant l'accès à votre application. S’il n’est pas spécifié, le niveau d'accès demandé sera par défaut l'authentification unique. Les autres options sont DirectoryReaders et DirectoryWriters. Pour plus d'informations sur ces niveaux d'accès, reportez-vous à la section Niveaux d'accès aux applications.|
|ConsentReturnUrl|facultatif. L'URL à laquelle vous souhaitez que la réponse d’octroi de l’accès soit renvoyée. Cette valeur doit être codée URL et doit être sous le même domaine que l'URL de réponse configurée dans votre définition d'application. Si elle n’est pas fournie, la réponse d'octroi de l’accès sera redirigée vers l'URL de réponse que vous avez configurée.|

Si vous spécifiez une ConsentReturnUrl distincte de l'URL de réponse, votre application pourra implémenter une logique distincte qui pourra traiter la réponse sur une URL distincte de l'URL de réponse (qui traite normalement les jetons SAML pour l’authentification). Vous pouvez également spécifier des paramètres supplémentaires dans l’URL codée ConsentReturnURL ; ils seront passés comme paramètres de la chaîne de requête à votre application lors de la redirection. Ce mécanisme peut servir à conserver des informations supplémentaires ou à lier la demande d’octroi d’accès de votre application à la réponse d'Azure AD.

#### Expérience utilisateur et réponse liées à l’octroi de l’accès (procédure héritée)

Les images suivantes illustrent ce à quoi l'utilisateur est confronté lorsqu’une application redirige vers le lien d’octroi d’accès.

Si l'utilisateur n'est pas déjà connecté, il est invité à le faire :

![Connectez-vous à AAD](./media/active-directory-integrating-applications/signintoaad.png)

Une fois l'utilisateur authentifié, Azure AD redirige l'utilisateur vers la page d’octroi d'accès :

![Accorder l'accès](./media/active-directory-integrating-applications/grantaccess.png)

>[AZURE.NOTE]Seul l'administrateur d'entreprise de l'organisation externe peut accorder l'accès à votre application. Si l'utilisateur n'est pas un administrateur d'entreprise, il aura la possibilité d’envoyer un courrier à l'administrateur de la société pour demander l’accès à cette application.

Une fois que le client a accordé l'accès à votre application en cliquant sur Accorder l'accès, ou qu’il a refusé l’accès en cliquant sur Annuler, Azure AD envoie une réponse à la ConsentReturnUrl ou à l’URL de réponse que vous avez configurée. Cette réponse contient les paramètres suivants :

|Paramètre|Description|
|---|---|
|TenantId|L’ID unique de l'organisation dans Azure AD qui a accordé l'accès à votre application. Ce paramètre est spécifié uniquement si le client a accordé l’accès.|
|Consentement|La valeur est définie comme Accordé si l’accès a été accordé à l'application ou comme Refusé si la demande a été rejetée.|

Des paramètres supplémentaires seront renvoyés à l'application s’ils ont été définis en tant que parties de l’URL codée ConsentReturnUrl. Voici un exemple de réponse à une demande d’octroi d’accès indiquant que l’application a été autorisée et incluant un ContextID qui a été fourni dans la demande d’octroi d’accès : `https://adatum.com/ExpenseReport.aspx?ContextID=123456&Consent=Granted&TenantId=f03dcba3-d693-47ad-9983-011650e64134`.

>[AZURE.NOTE]La réponse d'octroi de l'accès ne contiendra pas de jeton de sécurité pour l'utilisateur ; l'application doit connecter l'utilisateur séparément.

Voici un exemple de réponse à une demande d’octroi d’accès refusée : `https://adatum.com/ExpenseReport.aspx?ContextID=123456&Consent=Denied`

#### Déploiement de clés d’application pour un accès ininterrompu à l’API graphique (procédure héritée)

Pendant la durée de vie de votre application, vous devrez peut-être modifier les clés que vous utilisez lorsque vous faites appel à Azure AD pour acquérir un jeton d'accès afin d’appeler l'API Graph. En général, la modification de clés se divise en deux catégories : substitution d'urgence là où votre clé a été compromise ou substitution lorsque votre clé actuelle est sur le point d'expirer. La procédure suivante doit être suivie pour fournir à votre application un accès ininterrompu pendant que vous actualisez vos clés (principalement dans le deuxième cas).

1. Dans le portail de gestion Azure, cliquez sur votre locataire de répertoire, cliquez sur Applications dans le menu supérieur, puis cliquez sur l'application que vous souhaitez configurer. La page de démarrage rapide s'affiche avec l'authentification unique et d'autres informations de configuration.

1. Cliquez sur Configurer dans le menu supérieur pour afficher la liste des propriétés de votre application, et vous verrez la liste de vos clés.

1. Sous Clés, cliquez sur la liste déroulante qui indique Sélectionnez une durée et choisissez 1 ou 2 ans, puis cliquez sur Enregistrer dans la barre de commandes. Cela génère une nouvelle clé de mot de passe pour votre application. Copiez cette nouvelle clé de mot de passe. À ce stade, l’ancienne et la nouvelle clés sont utilisables par votre application pour obtenir un jeton d'accès d'Azure AD.

1. Revenez à votre application et mettez à jour la configuration pour commencer à utiliser la nouvelle clé de mot de passe. Consultez la section [Utilisation de l’API Graph pour interroger Azure AD](https://msdn.microsoft.com/library/azure/dn151791.aspx) pour obtenir un exemple de cas où cette mise à jour doit se produire.

1. Vous devez maintenant déployer cette modification dans votre environnement de production, en commençant par la vérifier sur un nœud de service, avant de la déployer sur le reste.

1. Une fois la mise à jour terminée dans votre déploiement de production, vous pouvez revenir au portail de gestion Azure et supprimer l'ancienne clé.

#### Modification des propriétés de l’application après l’activation de l’accès (procédure héritée)

Une fois que vous avez activé l'accès des utilisateurs externes à votre application, vous pouvez toujours apporter des modifications aux propriétés de votre application dans le portail de gestion Azure. Toutefois, les clients qui ont déjà accordé l'accès à votre application avant que vous ayez apporté des modifications à l’application ne verront pas ces modifications répercutées lors de l'affichage des détails sur votre application dans le portail de gestion Azure. Une fois l'application accessible aux clients, vous devez être très prudent lorsque vous apportez certaines modifications. Par exemple, si vous mettez à jour l'URI ID de l'application, vos clients existants qui ont accordé l'accès avant cette modification ne pourront pas se connecter à votre application à l'aide de leur compte professionnel ou scolaire.

Si vous apportez une modification aux RequestedPermissions pour demander un niveau d'accès supérieur, les clients existants utilisant la fonctionnalité de l'application qui tire maintenant parti des nouveaux appels de l'API avec ce niveau d'accès supérieur risquent d’obtenir une réponse Accès refusé de l'API Graph. Votre application doit gérer ces situations et demander au client d'accorder l'accès à votre application avec la demande d’un niveau d'accès supérieur.

## Suppression d'une application

Cette section décrit comment supprimer une application de votre répertoire, à la fois pour les applications à client unique et les applications mutualisées.

### Pour supprimer une application à client unique de votre répertoire

1. Connectez-vous au portail de gestion Azure.

1. Cliquez sur l'icône d'Active Directory dans le menu de gauche, puis cliquez sur le répertoire souhaité.

1. Dans le menu supérieur, cliquez sur Applications, puis cliquez sur l'application que vous souhaitez configurer. La page de démarrage rapide s'affiche avec l'authentification unique et d'autres informations de configuration.

1. Cliquez sur le bouton Supprimer dans la barre de commandes.

1. Cliquez sur Oui dans le message de confirmation.

### Pour supprimer une application mutualisée de votre répertoire

1. Connectez-vous au portail de gestion Azure.

1. Cliquez sur l'icône d'Active Directory dans le menu de gauche, puis cliquez sur le répertoire souhaité.

1. Dans le menu supérieur, cliquez sur Applications, puis cliquez sur l'application que vous souhaitez configurer. La page de démarrage rapide s'affiche avec l'authentification unique et d'autres informations de configuration.

1. Dans la section L'application est mutualisée, cliquez sur Non. Cela convertit votre application en une application à client unique, mais l'application reste toujours dans les organisations qui ont déjà donné leur consentement.

1. Cliquez sur le bouton Supprimer dans la barre de commandes.

1. Cliquez sur Oui dans le message de confirmation.

Afin de pouvoir supprimer l'accès d'une application à son répertoire (après avoir donné son consentement), l'administrateur de l'entreprise doit avoir un abonnement Azure pour supprimer l'accès via le portail de gestion Azure. L’administrateur de l’entreprise peut également utiliser les [applets de commande Azure AD PowerShell](http://go.microsoft.com/fwlink/?LinkId=294151) pour supprimer l’accès.

## Étapes suivantes

- Consulter les [Instructions de personnalisation pour les applications intégrées](active-directory-branding-guidelines.md)

- En savoir plus sur les [Objets application et principal du service](active-directory-application-objects.md)

- Consulter le [Guide du développeur Active Directory](active-directory-developer’s guide.md)

<!---HONumber=September15_HO1-->