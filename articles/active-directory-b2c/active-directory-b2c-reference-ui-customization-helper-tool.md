<properties
	pageTitle="Version préliminaire d’Azure Active Directory B2C : outil d’assistance de personnalisation d’interface de page | Microsoft Azure"
	description="Un outil d’assistance est utilisé pour illustrer la fonctionnalité de personnalisation de la Page Interface utilisateur dans Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="swkrish"/>

# Version préliminaire d’Azure Active Directory B2C : un outil d’assistance d’utilisé pour illustrer la fonctionnalité de personnalisation de la Page utilisateur Interface (UI)

Cet article complète le [principal article sur la personnalisation de l'interface utilisateur](active-directory-b2c-reference-ui-customization.md) dans Azure Active Directory B2C. Les étapes ci-dessous décrivent l’utilisation de la fonctionnalité de personnalisation de l'interface utilisateur à l'aide de l’exemple de contenu HTML et CSS que nous vous avons fourni.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Obtention d’un client Azure AD B2C

Avant de pouvoir personnaliser quoi que ce soit, vous devrez [obtenir un client Azure AD B2C](active-directory-b2c-get-started.md), si ce n'est déjà fait.

## Création d’une stratégie d’inscription

L'exemple de contenu que nous vous avons fourni personnalise deux pages d’une [stratégie d'inscription](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy) : la [page de sélection IDP](active-directory-b2c-reference-ui-customization.md#identity-provider-selection-page) et la [page d'abonnement à un compte local](active-directory-b2c-reference-ui-customization.md#local-account-sign-up-page). Lors de la [création de votre stratégie d'inscription](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy), ajoutez le compte local (adresse de messagerie), Facebook et Google+ en tant que **fournisseurs d'identité**. Il s'agit des seuls IDP acceptés par notre exemple de contenu HTML.

## Inscription d'une application

Vous devrez [inscrire une application](active-directory-b2c-app-registration.md) dans votre client B2C qui permettra d'exécuter votre stratégie. Après avoir inscrit votre application, vous disposez de quelques options pour exécuter votre stratégie d'inscription :

- Générez l’une des applications de démarrage rapide AD B2C répertoriées [ici](active-directory-b2c-overview.md#getting-started).
- Utilisez l’application [Azure AD B2C Playground](https://aadb2cplayground.azurewebsites.net) préconfigurée. Si vous choisissez d'utiliser l'application Playground, vous devez inscrire une application dans votre client B2C en utilisant l'**URI de redirection** `https://aadb2cplayground.azurewebsites.net/`
- Utilisez le bouton **Exécuter maintenant** sur votre stratégie dans le [portail Azure en version préliminaire](https://portal.azure.com).

## Personnalisation de votre stratégie

Pour personnaliser l'apparence de vos stratégies, vous devez d'abord créer les fichiers HTML et CSS en suivant les conventions spécifiques d'Azure AD B2C. Vous pouvez ensuite charger votre contenu statique vers un emplacement accessible au public afin qu’Azure AD B2C puisse y accéder. Il peut s’agir de votre propre serveur web dédié, d’un stockage d’objets blob Azure, d’Azure CDN, ou n'importe quel autre fournisseur d'hébergement de ressources statiques. Les seules conditions requises exigent que votre contenu soit disponible via le protocole HTTPS et accessible à l'aide de CORS. Après avoir publié votre contenu statique sur le web, vous pouvez modifier votre stratégie pour la pointer vers cet emplacement et présenter ce contenu à vos utilisateurs finaux. Le [principal article sur la personnalisation de l'interface utilisateur](active-directory-b2c-reference-ui-customization.md) décrit en détail la fonctionnalité de personnalisation Azure AD B2C.

Dans le cadre de ce didacticiel, nous avons déjà créé quelques exemples de contenu et les avons hébergés sur un stockage d’objets blob Azure. L'exemple de contenu est une personnalisation très basique dans le thème de notre entreprise fictive, « Contoso B2C ». Pour le tester dans votre propre stratégie, procédez comme suit :

1. Connectez-vous à votre client dans le [portail Azure en version préliminaire](https://portal.azure.com) et accédez au panneau de fonctionnalités B2C.
2. Cliquez sur **Stratégies d’inscription**, puis cliquez sur votre stratégie d’inscription (par exemple, « b2c\_1\_sign\_up »).
3. Cliquez sur **Personnalisation d’interface de page**, puis **page de sélection du fournisseur d’identité**.
4. Placez le commutateur **Utiliser un modèle personnalisé** sur **Oui**. Dans le champ **URI de la page personnalisée**, entrez `https://contosob2c.blob.core.windows.net/static/Index.html`. Cliquez sur **OK**.
5. Cliquez sur **page d’inscription à un compte Local**. Placez le commutateur **Utiliser un modèle personnalisé** sur **Oui**. Dans le champ **URI de la page personnalisée**, entrez `https://contosob2c.blob.core.windows.net/static/EmailVerification.html`. Cliquez deux fois sur **OK** pour fermer les panneaux de personnalisation de l'interface utilisateur.
6. Cliquez sur **Enregistrer**.

Vous pouvez maintenant tester votre stratégie personnalisée. Vous pouvez utiliser votre propre application ou l’application AAD B2C Playground si vous le souhaitez, mais vous avez aussi la possibilité de cliquer sur la commande **Exécuter maintenant** dans le panneau de la stratégie. Sélectionnez votre application dans la liste déroulante ainsi que l'URI de redirection approprié. Cliquez sur le bouton **Exécuter maintenant**. Un nouvel onglet de navigateur devrait s’ouvrir et vous pouvez vivre l’expérience utilisateur consistant à inscrire votre application avec le nouveau contenu en place !

## Chargement de l'exemple de contenu sur un stockage d’objets blob Azure

Si vous souhaitez utiliser le stockage d'objets blob Azure pour héberger le contenu de votre page, vous pouvez créer votre propre compte de stockage et utiliser notre outil d'assistance B2C pour charger vos fichiers.

#### Créer un compte de stockage

1. Connectez-vous au [portail Azure en version préliminaire](https://portal.azure.com/).
2. Cliquez sur **+ Nouveau** -> **données + stockage** -> **compte de stockage**. Vous avez besoin d’un abonnement Azure pour créer un compte de stockage d’objets Blob Azure. Vous pouvez vous inscrire pour un essai gratuit [ici](https://azure.microsoft.com/fr-FR/pricing/free-trial/).
3. Fournissez un **Nom** pour le compte de stockage (par exemple, « contoso ») et de sélectionner les options appropriées pour **Niveau de tarification**, **Groupe de ressources** et **Abonnement**. Assurez-vous que l’option **Épingler au tableau d’accueil** est activée. Cliquez sur **Create**.
4. Revenez en arrière vers le tableau d’accueil et cliquez sur le compte de stockage que vous venez de créer.
5. Sous le **Résumé**, cliquez sur **Conteneurs**, puis sur **+ Ajouter**.
6. Fournissez un **Nom** pour le conteneur (par exemple, « b2c ») et sélectionnez **Blob** comme le **type d’accès**. Cliquez sur **OK**.
7. Le conteneur que vous avez créé apparaît dans la liste sur le panneau **Objets Blob**. Notez l’URL du conteneur ; il se présente par exemple comme `https://contoso.blob.core.windows.net/b2c`. Fermez le panneau **Objets Blob**.
8. Sur le panneau de compte de stockage, cliquez sur **Clés** et notez les valeurs des champs **Nom de compte de stockage** et **Clé d’accès primaire** champs.

> [AZURE.NOTE]La **Clé d’accès primaire** est une information de sécurité importante.

#### Téléchargement de l’outil d’assistance et des fichiers exemples

Vous pouvez télécharger ici l'[outil d'assistance du stockage d'objets blob Azure et les fichiers exemples sous forme d'une archive .zip](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) ou effectuer un clonage à partir de GitHub :

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Ce référentiel contient un répertoire `sample_templates\contoso` qui regroupe un exemple de code HTML, CSS et des images. Pour que ces modèles puissent faire référence à votre propre compte de stockage d'objets blob Azure, vous devez modifier les fichiers HTML. Ouvrez `Index.htnml` et `EmailValidation.html` puis remplacez toutes les instances de `https://localhost` par l'URL de votre propre conteneur que vous avez copiée dans les étapes ci-dessus. Il est nécessaire d'utiliser le chemin absolu des fichiers HTML car, dans ce cas, le code HTML sera fourni par Azure AD, sous le domaine `https://login.microsoftonline.com`.

#### Chargement des fichiers exemples

Dans le même référentiel, décompressez `B2CAzureStorageClient.zip` et exécutez le fichier `B2CAzureStorageClient.exe` qu’il contient. Ce programme chargera simplement tous les fichiers dans le répertoire que vous spécifiez pour votre compte de stockage, et activera l'accès CORS pour ces fichiers. Si vous avez suivi les étapes ci-dessus, les fichiers HTML et CSS pointeront désormais vers votre compte de stockage. Notez que le nom de votre compte de stockage est la partie qui précède `blob.core.windows.net`, par exemple, `contoso`. Vous pouvez vérifier que le contenu a été téléchargé correctement en essayant d'accéder à `https://{storage-account-name}.blob.core.windows.net/{container-name}/Index.html` sur un navigateur. Utilisez également [http://test-cors.org/](http://test-cors.org/) pour vous assurer que le contenu est maintenant compatible CORS (recherchez l'état XHR : 200 dans le résultat).

#### Nouvelle personnalisation de votre stratégie

Maintenant que vous avez chargé l'exemple de contenu sur votre propre compte de stockage, vous devez modifier votre stratégie d'inscription pour le référencer. Répétez les étapes à partir de la section [« Personnalisation de votre stratégie »](#customize-your-policy) ci-dessus, en utilisant cette fois l'URL de votre propre compte de stockage. Par exemple, l'emplacement de votre fichier `Index.html` serait `<url-of-your-container>/Index.html`.
        
Vous pouvez à présent utiliser le bouton **Exécuter maintenant** ou votre propre application pour réexécuter votre stratégie. Le résultat devrait être quasiment identique puisque vous avez utilisé les mêmes exemples de codes HTML et CSS dans les deux cas. Cependant, vos stratégies référencent maintenant votre propre instance de stockage d'objets blob Azure, et vous êtes libre de modifier et de recharger les fichiers. Pour plus d'informations sur la personnalisation du code HTML et CSS, reportez-vous au [principal article sur la personnalisation de l'interface utilisateur](active-directory-b2c-reference-ui-customization.md).

<!---HONumber=Oct15_HO3-->