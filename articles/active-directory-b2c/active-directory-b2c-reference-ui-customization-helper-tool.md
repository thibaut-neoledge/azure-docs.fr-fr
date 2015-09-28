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
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Version préliminaire d’Azure Active Directory B2C : un outil d’assistance d’utilisé pour illustrer la fonctionnalité de personnalisation de la Page utilisateur Interface (UI)

Cet article est associé à l’[article principal](active-directory-b2c-reference-ui-customization) sur la fonctionnalité de personnalisation de la page d’interface utilisateur dans Azure Active Directory (AD) B2C.

L’exécution des étapes ci-dessous permet de tester la fonctionnalité de personnalisation de l’interface utilisateur de page à l’aide de notre exemple de contenu. Pour les besoins de la démonstration, notre exemple de contenu est au format HTML5 **statique**.

1. Téléchargez notre exemple de contenu dans [stockage d’objets Blob Azure](http://azure.microsoft.com/documentation/services/storage/), pour le rendre accessible publiquement via HTTPS et activer CORS (partage des ressources cross-origin) sur ces URL.
2. Modifiez les paramètres de personnalisation de l’interface utilisateur de page dans votre stratégie d’inscription existante et spécifiez l’URL ci-dessus.

Avant de commencer :

- Générez l’une des applications de démarrage rapide AD B2C répertoriées [ici](active-directory-b2c-overview.md). Dans le cadre de cette opération, vous allez créer une stratégie d’inscription que vous allez modifier ici.
- Télécharger l’outil d’assistance [ici](https://github.com/AzureADSamples/B2C-AzureBlobStorage-Client).

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

### Étape 1 : Télécharger notre exemple de contenu dans les emplacements adéquats

1. Connectez-vous à la [version préliminaire du portail Azure](https://portal.azure.com/).
2. Cliquez sur **+ Nouveau** -> **données + stockage** -> **compte de stockage**. Vous avez besoin d’un abonnement Azure pour créer un compte de stockage d’objets Blob Azure. Vous pouvez vous inscrire pour un [ici](https://azure.microsoft.com/fr-FR/pricing/free-trial/).
3. Fournir un **Nom** pour le compte de stockage (par exemple, « contoso.core.windows.net ») et de sélectionner les options appropriées pour **Niveau de tarification**, **Groupe de ressources** et **Abonnement**. Assurez-vous que l’option **Épingler au tableau d’accueil**est activée. Cliquez sur **Create**.
4. Revenez en arrière vers le tableau d’accueil et cliquez sur le compte de stockage que vous venez de créer.
5. Sous le **Résumé**, cliquez sur **Conteneurs**, puis sur **+ Ajouter**.
6. Fournir un **Nom** pour le conteneur (par exemple, « b2c ») et sélectionnez **Blob** comme le **type d’accès**. Cliquez sur **OK**.
7. Le conteneur que vous avez créé apparaît dans la liste sur le panneau **Objets Blob**. Notez l’URL du conteneur ; il se présente par exemple comme `https://contoso.blob.core.windows.net/b2c`. Fermer le panneau **Objets Blob**.
8. Sur le panneau de compte de stockage, cliquez sur **Clés** et notez les valeurs des champs **Nom de compte de stockage** et **Clé d’accès primaire** champs.

    > [AZURE.NOTE]La **Clé d’accès primaire** est une information de sécurité importante.

9. Exécutez l’outil d’assistance et fournissez-lui les valeurs **Nom de compte de stockage** et **Clé d’accès primaire** copiées à l’étape précédente. Cette opération téléchargera notre contenu exemple dans votre compte de stockage.
10. Vérifiez que le contenu a été téléchargé correctement en essayant d’accéder à `https://contoso.blob.core.windows.net/b2c/idp.html` sur un navigateur. Utilisez également [http://test-cors.org/](http://test-cors.org/) pour vous assurer que le contenu est maintenant compatible CORS (recherchez `XHR status: 200` dans le résultat).

### Étape 2 : Modifier les options de personnalisation de l’interface utilisateur de page dans votre stratégie d’inscription

1. Connectez-vous à votre annuaire dans la [version préliminaire du portail Azure](https://portal.azure.com) et accédez au panneau de fonctionnalités B2C.
2. Cliquez sur **Stratégies d’inscription**, puis cliquez sur votre stratégie d’inscription (par exemple, « B2C\_1\_SiIn »).
3. Cliquez sur **Personnalisation d’interface de page**, puis **page de sélection du fournisseur d’identité**.
4. Placez le commutateur **Utiliser un modèle personnalisé** sur **Oui**. Dans le champ **URI de la page personnalisée**, saisissez l’URL appropriée correspondant au contenu téléchargé sur votre compte de stockage. Par exemple, `https://contoso.blob.core.windows.net/b2c/idp.html`. Cliquez sur **OK**.
5. Cliquez sur **page d’inscription à un compte Local**. Placez le commutateur **Utiliser un modèle personnalisé** sur **Oui**. Dans le champ **URI de la page personnalisée**, saisissez l’URL appropriée au contenu téléchargé vers votre compte de stockage. Par exemple : `https://contoso.blob.core.windows.net/b2c/su.html`. Cliquez sur **OK** deux fois.
6. Cliquez sur **Enregistrer**.
7. Cliquez sur la commande **Exécuter maintenant** en haut du panneau. Sélectionnez « B2C app » dans le menu déroulant **Applications** et `https://localhost:44321/` dans la liste déroulante **URL de réponse/URI de redirection**. Cliquez sur le bouton **Exécuter maintenant**.
8. Un nouvel onglet de navigateur s’ouvre et vous pouvez vivre l’expérience utilisateur consistant à inscrire votre application avec le nouveau contenu en place !

> [AZURE.NOTE]Notez qu’il faut une minute pour que les modifications de stratégie prennent effet.

<!---HONumber=Sept15_HO3-->