---
title: "Azure Active Directory B2C : personnalisation de l’interface utilisateur Azure AD B2C de façon dynamique avec les stratégies personnalisées"
description: "Comment prendre en charge plusieurs expériences de personnalisation avec du contenu HTML/CSS qui change de façon dynamique lors de l’exécution"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/20/2017
ms.author: yoelh
ms.openlocfilehash: cbce9b72c25c90dde526ff229f77a85ce9478efa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-using-custom-policies"></a>Azure Active Directory B2C : configuration de l’interface utilisateur avec du contenu dynamique à l’aide de stratégies personnalisées
Les stratégies personnalisées Azure AD B2C vous permettent de transmettre un paramètre dans une chaîne de requête. Ce paramètre est transmis à votre point de terminaison HTML et peut changer le contenu de la page de façon dynamique. Par exemple, vous pouvez modifier l’image d’arrière-plan de connexion ou d’inscription B2C en fonction d’un paramètre que vous transmettez depuis votre application web/mobile. 

## <a name="prerequisites"></a>Composants requis
Cet article se concentre sur la façon de personnaliser l’interface utilisateur Azure AD B2C avec du **contenu dynamique** à l’aide de stratégies personnalisées. Pour commencer avec la personnalisation de l’interface utilisateur à l’aide de stratégies personnalisées, consultez l’article [Azure Active Directory B2C : Configurer la personnalisation de l’interface utilisateur dans une stratégie personnalisée](active-directory-b2c-ui-customization-custom.md). 

>[!NOTE]
>
>  L’article [Azure Active Directory B2C : Configurer la personnalisation de l’interface utilisateur dans une stratégie personnalisée](active-directory-b2c-ui-customization-custom.md) vous explique les bases de la personnalisation de l’interface utilisateur Azure AD B2C avec une stratégie personnalisée :
> * La fonctionnalité de personnalisation de l’interface utilisateur de page.
> * Un outil qui vous permet de tester la fonctionnalité de personnalisation d’interface utilisateur de page à l’aide de notre exemple de contenu.
> * Les éléments d’interface utilisateur de base dans chaque type de page.
> * Meilleures pratiques lors de l’exercice de cette fonctionnalité.

## <a name="adding-a-link-to-html5css-templates-to-your-user-journey"></a>Ajout d’un lien aux modèles HTML5/CSS pour votre parcours utilisateur

Dans une stratégie personnalisée, une définition de contenu définit l’URI de page HTML5 qui est utilisée pour une étape d’interface utilisateur donnée. Par exemple, pour les pages de connexion ou d’inscription. La stratégie de base définit l’apparence par défaut en pointant vers un URI de fichiers HTML5 (fait référence à ses fichiers CSS). Dans la stratégie d’extension, vous pouvez modifier l’apparence en substituant le LoadUri de ce fichier HTML5. Par conséquent, les définitions de contenu comprennent des URL vers du contenu externe qui est défini en créant des fichiers HTML5/CSS, si nécessaire. 

La section `ContentDefinitions` contient une série d’éléments XML `ContentDefinition`. L’attribut d’ID de l’élément `ContentDefinition` spécifie le type des pages qui se rapporte à la définition de contenu. Par conséquent, le contexte dans lequel un modèle HTML5/CSS personnalisé se trouve est utilisé. Le tableau suivant décrit l’ensemble d’ID de définition de contenu reconnus par le moteur d’infrastructure d’expérience d’identité et le type des pages associé.

| ID de définition du contenu | Modèle HTML5 par défaut| Description | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Page d’erreur**. Cette page s’affiche lorsqu’une exception ou une erreur est rencontrée. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Page de sélection du fournisseur d’identité**. Cette page contient une liste de fournisseurs d’identité parmi lesquels l’utilisateur peut faire son choix lors de la connexion. Il s’agit généralement de fournisseurs d’identité d’entreprise, de fournisseurs d’identité de réseaux sociaux tels que Facebook et Google + ou de comptes locaux. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Sélection du fournisseur d’identité pour l’inscription**. Cette page contient une liste de fournisseurs d’identité parmi lesquels l’utilisateur peut faire son choix lors de l’inscription. Il s’agit de fournisseurs d’identité d’entreprise, de fournisseurs d’identité de réseaux sociaux tels que Facebook et Google + ou de comptes locaux. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Page de mot de passe oublié**. Cette page contient un formulaire que l’utilisateur doit remplir pour lancer une réinitialisation de mot de passe.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Page de connexion à un compte local**. Cette page contient un formulaire de connexion que l’utilisateur doit renseigner lors de la connexion à un compte local basé sur une adresse e-mail ou un nom d’utilisateur. Le formulaire peut contenir une zone de saisie de texte et une zone de saisie de mot de passe. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Page d’inscription à un compte local**. Cette page contient un formulaire d’inscription que l’utilisateur doit renseigner lors de la connexion à un compte local basé sur une adresse e-mail ou un nom d’utilisateur. Le formulaire peut contenir différentes commandes de saisie telles qu’une zone de saisie de texte, une zone de saisie de mot de passe, un bouton radio, des zones de liste déroulante à sélection unique ou des cases à sélection multiples. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Page d’authentification multi-facteur**. Cette page permet aux utilisateurs de vérifier leurs numéros de téléphone (par voie textuelle ou vocale) au cours de l’inscription ou de la connexion. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Page d’inscription à un compte de réseau social**. Cette page contient un formulaire d’inscription que l’utilisateur doit remplir lors de l’inscription à l’aide d’un compte existant proposé par un fournisseur d’identité de réseaux sociaux. Cette page est similaire à la page d’inscription au compte de réseau social ci-dessus, à l’exception des champs de saisie de mot de passe. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Page de mise à jour de profil**. Cette page contient un formulaire dont l’utilisateur peut se servir pour mettre à jour son profil. Cette page est similaire à la page d’inscription au compte de réseau social, à l’exception des champs de saisie de mot de passe. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Page de connexion ou d’inscription unifiée**. Cette page gère à la fois l’inscription et la connexion des utilisateurs. Les utilisateurs peuvent utiliser des fournisseurs d’identité d’entreprise, des fournisseurs d’identité de réseaux sociaux tels que Facebook et Google + ou des comptes locaux.  |

## <a name="serving-dynamic-content"></a>Utilisation de contenu dynamique
Dans l’article précédent [Azure Active Directory B2C : Configurer la personnalisation de l’interface utilisateur dans une stratégie personnalisée](active-directory-b2c-ui-customization-custom.md), vous avez chargé les fichiers HTML5 vers le stockage Blob Azure. Ces fichiers HTML5 sont statiques et affichent le même contenu HTML pour chaque requête. Dans cet article, nous utilisons une application web ASP.Net, qui peut accepter des paramètres de chaîne de requête et réagir en conséquence. Lors de cette procédure pas à pas, vous allez effectuer les opérations suivantes :
* Créer une application web ASP.NET Core qui héberge votre modèle HTML5 
* Ajouter le modèle personnalisé HTML5 _unified.cshtml_ 
* Publier votre application web dans Azure App Service 
* Définir CORS pour votre application web
* Remplacer les éléments `LoadUri` pour pointer vers votre fichier HTML5

## <a name="step-1-create-an-aspnet-web-app"></a>Étape 1 : Créer une application web ASP.NET

1.  Dans Visual Studio, créez un projet en sélectionnant **Fichier > Nouveau > Projet**.
2.  Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Visual C# > Web > Application web ASP.NET Core (.NET Core)**.
3.  Nommez l’application, par exemple Contoso.AADB2C.UI, puis sélectionnez **OK**.

    ![Créer un projet Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4.  Sélectionnez le modèle **Application web**.
5.  Vérifiez que l’authentification est définie sur **Aucune authentification**.

    ![Sélectionner le modèle d’API web](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6.  Cliquez sur **OK** pour créer le projet.

## <a name="step-2-create-mvc-view"></a>Étape 2 : Créer une vue MVC
### <a name="step-21-download-b2c-built-in-html5-template"></a>Étape 2.1 Télécharger le modèle HTML5 intégré à B2C
Votre modèle HTML5 personnalisé est basé sur le modèle HTML5 intégré à B2C. Vous pouvez télécharger le modèle [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) ou en télécharger un à partir du [pack de démarrage](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Vous utilisez ce fichier HTML5 pour une page de connexion ou d’inscription unifiée.

### <a name="step-22-add-mvc-view"></a>Étape 2.2 Ajouter une vue MVC
1. Cliquez avec le bouton droit sur le dossier Vues/Accueil, puis sur **Ajouter > Nouvel élément**.
 ![Ajouter un élément MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)
2. Dans la boîte de dialogue **Ajouter un nouvel élément - Contoso.AADB2C.UI**, sélectionnez **Web > ASP.NET**.
3. Cliquez sur **Page de vue MVC**.
4. Dans le champ **Nom**, remplacez le nom par _unified.cshtml_.
5. Cliquer sur **Ajouter**
 ![Ajouter une vue MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)
6.  Si ce fichier n’est pas déjà ouvert, double-cliquez dessus pour l’ouvrir. Effacez le contenu de votre fichier _unified.cshtml_.
7. Pour la démonstration, nous supprimons la référence à la page de disposition. Ajoutez l’extrait de code suivant au fichier _unified.cshtml_.

    ```C#
    @{
        Layout = null;
    }
    ```

8. Ouvrez le fichier _unified.cshtml_ que vous avez téléchargé à partir du modèle HTML5 intégré à AAD B2C.
9. Recherchez les signes `@` et remplacez-les par `@@`.
10. Copiez le contenu du fichier et collez-le sous la définition de la disposition. Votre code doit ressembler à ce qui suit : ![fichier unified.cshtml après avoir ajouté le modèle HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)
### <a name="step-23-change-the-background-image"></a>Étape 2.3 Modifier l’image d’arrière-plan
10. Recherchez l’élément `<img>` avec l’ID `background_background_image` et remplacez `src` par _https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1_ ou n’importe quelle image d’arrière-plan de votre choix.
![Modifier l’arrière-plan de la page](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)
### <a name="step-24-add-you-view-to-mvc-controller"></a>Étape 2.4 Ajouter votre vue au contrôleur MVC
Ouvrez **Contrôleurs\HomeController.cs** et ajouter la méthode suivante. 
```C
public IActionResult unified()
{
    return View();
}
```
Ce code spécifie que la méthode doit utiliser un fichier de modèle de vue pour afficher une réponse dans le navigateur. Étant donné que nous ne spécifions pas de manière explicite le nom du fichier de modèle de vue, MVC est par défaut défini pour utiliser le fichier de vue _unified.cshtml_ dans le dossier /Vues/Accueil.

Après avoir ajouté la méthode _unified_, votre code doit ressembler à ce qui suit : ![Modification du contrôleur pour afficher la vue](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

Déboguez votre application web et assurez-vous que la page _unified_ est accessible. Par exemple, `http://localhost:<Port number>/Home/unified`

### <a name="step-25-publish-to-azure"></a>Étape 2.5 : Publier dans Azure
1.  Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **Contoso.AADB2C.UI**, puis sélectionnez **Publier**.

    ![Publier dans Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2.  Assurez-vous que **Microsoft Azure App Service** est sélectionné, puis sélectionnez **Publier**.

    ![Créer un Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    La boîte de dialogue **Créer App Service** vous permet de créer toutes les ressources Azure nécessaires pour exécuter l’application web ASP.NET dans Azure.

> [!NOTE]
>
>Pour plus d’informations sur la publication, consultez : [Création d’une application web ASP.NET dans Azure](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3.  Sous **Nom de l’application web**, tapez un nom d’application unique (les caractères autorisés sont `a-z`, `0-9` et `-`). L’URL de l’application web est `http://<app_name>.azurewebsites.NET`, où `<app_name>` correspond au nom de votre application web. Vous pouvez accepter le nom généré automatiquement, qui est unique.

    Sélectionnez **Créer** pour commencer à créer les ressources Azure.

    ![Fournir les propriétés App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

4.  Une fois que l’Assistant a terminé, il publie l’application web ASP.NET dans Azure, puis lance l’application dans le navigateur par défaut. Copiez l’URL de la page _unified_, par exemple, _https://<app_name>.azurewebsites.net/home/unified_.

## <a name="step-3-configure-cors-in-azure-app-service"></a>Étape 3 : Configurer CORS dans Azure App Service
1. Dans un navigateur, accédez au [portail Azure](https://portal.azure.com/).

2. Cliquez sur **App Services**, puis sur le nom de votre application API.

    ![Sélectionnez l’application API dans le portail](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

3. Dans la section **Paramètres**, faites défiler jusqu’à la section **API**, puis cliquez sur **CORS**.

    ![Sélectionner les paramètres CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

4. Dans la zone de texte, saisissez la ou les URL depuis laquelle/lesquelles vous souhaitez autoriser les appels JavaScript.
Ou saisissez un astérisque (*) pour indiquer que tous les domaines d’origine sont acceptés.

5. Cliquez sur **Enregistrer**.

    ![Cliquez sur Enregistrer.](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

Lorsque vous cliquez sur Enregistrer, l’application API accepte les appels JavaScript provenant des URL spécifiées. 

## <a name="step-4-html5-template-validation"></a>Étape 4 : Validation du modèle HTML5
Votre modèle HTML5 est prêt à être utilisé.  Toutefois, il n’est disponible dans aucune des `ContentDefinition`. Avant d’ajouter la `ContentDefinition` à votre stratégie personnalisée, vous devez :
* Vérifier que votre contenu est accessible et conforme à HTML5
* Vérifier que CORS est activé sur votre serveur de contenu

>[!NOTE]
>
>Pour vérifier que CORS est activé sur le site sur lequel votre contenu est hébergé et pour tester vos demandes CORS, vous pouvez utiliser le site http://test-cors.org/. 

* Le contenu utilisé est sécurisé via **HTTPS**.
* Utiliser des **URL absolues** comme https://yourdomain/content pour tous les liens, ainsi que les images et le contenu CSS.

## <a name="step-5-configure-your-content-definition"></a>Étape 5 : Configurer votre définition de contenu
Pour configurer la `ContentDefinition`
1.  Ouvrez le fichier de base de votre stratégie (par exemple, TrustFrameworkBase.xml).
2.  Recherchez l’élément `<ContentDefinitions>` et copiez la totalité du contenu du nœud `<ContentDefinitions>`.
3.  Ouvrez le fichier d’extension (par exemple, TrustFrameworkExtensions.xml), puis recherchez l’élément `<BuildingBlocks>`. Si l’élément n’existe pas, ajoutez-en un.
4.  Collez l’intégralité du contenu du nœud `<ContentDefinitions>` que vous avez copié en tant qu’enfant de l’élément `<BuildingBlocks>`. 
5.  Recherchez le nœud `<ContentDefinition>` incluant `Id="api.signuporsignin"` dans le XML que vous avez copié.
6.  Modifiez la valeur de `LoadUri` en remplaçant _~/tenant/default/unified_ par _https://app_name.azurewebsites.net/home/unified_. Votre stratégie personnalisée doit ressembler à ce qui suit : ![Votre définition de contenu](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Étape 6 : Charger la stratégie sur votre locataire
1.  Dans le [portail Azure](https://portal.azure.com), passez au [contexte de votre locataire Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) et ouvrez **Azure AD B2C**.
2.  Sélectionnez **Infrastructure d’expérience d’identité**.
3.  Ouvrez **Toutes les stratégies**.
4.  Sélectionnez **Charger la stratégie**.
5.  Cochez la case **Remplacer la stratégie si elle existe**.
6.  **Téléchargez** TrustFrameworkExtensions.xml et vérifiez que sa validation n’échoue pas

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Étape 7 : Tester la stratégie personnalisée en utilisant Exécuter maintenant
1.  Ouvrez **Paramètres Azure AD B2C** et accédez à **Infrastructure d’expérience d’identité**.

    >[!NOTE]
    >
    >    **Exécuter maintenant** nécessite la préinscription d’au moins une application sur le locataire. 
    >    Pour découvrir comment inscrire des applications, consultez les articles sur Azure AD B2C [Bien démarrer](active-directory-b2c-get-started.md) et [Inscription des applications](active-directory-b2c-app-registration.md).
    >

2.  Ouvrez **B2C_1A_signup_signin**, la stratégie personnalisée de partie de confiance que vous avez chargée. Sélectionnez **Exécuter maintenant**.
3.  Vous devez être en mesure de voir votre HTML5 personnalisé avec l’arrière-plan que vous avez créé précédemment. ![Votre stratégie d’inscription ou de connexion](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-adding-dynamic-content"></a>Étape 8 : Ajout de contenu dynamique
Dans cette section, nous modifions l’arrière-plan en fonction d’un paramètre de chaîne de requête nommé _campaignId_. Votre application par partie de confiance (applications web/mobiles) envoie le paramètre à AAD B2C. Votre stratégie lit le paramètre et envoie sa valeur à votre modèle HTML5. 


### <a name="step-81-adding-content-definition-parameter"></a>Étape 8.1 Ajout de paramètre de définition de contenu

Pour ajouter l’élément `ContentDefinitionParameters` :
1.  Ouvrez le fichier SignUpOrSignin de votre stratégie (par exemple, SignUpOrSignin.xml).
2.  Recherchez le nœud `<UserJourneyBehaviors>`. 
4.  Ajoutez l’extrait de code XML suivant dans `<UserJourneyBehaviors>` 

    ```XML
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    </ContentDefinitionParameters>
    ```

### <a name="step-82-change-your-code-to-accept-query-string-parameter-and-replace-the-background-image-accordingly"></a>Étape 8.2 Modifier votre code pour accepter le paramètre de chaîne de requête et remplacer l’image d’arrière-plan en conséquence
Dans cette section, nous modifions la méthode _unified_ HomeController pour accepter le paramètre campaignId. La méthode vérifie sa valeur et définit la variable `ViewData["background"]` en conséquence.

1. Ouvrez **Contrôleurs\HomeController.cs** et modifiez la méthode `unified` avec l’extrait de code suivant :

    ```C#
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. Recherchez l’élément `<img>` avec l’ID `background_background_image` et remplacez `src` par `@ViewData["background"]`.

    ![Modifier l’arrière-plan de la page](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8.3 Charger les modifications et publier votre stratégie
1. Publiez votre projet Visual Studio sur Azure App Service.
2. Chargez la stratégie SignUpOrSignin.xml sur AAD B2C.
3.  Ouvrez **B2C_1A_signup_signin**, la stratégie personnalisée de partie de confiance que vous avez chargée. Sélectionnez **Exécuter maintenant**. Vous devez voir la même image d’arrière-plan qu’auparavant.
4. Copiez l’URL de la barre d’adresses du navigateur.
5. Ajoutez le paramètre de chaîne de requête _campaignId_ à l’URI. Par exemple, ajoutez `&campaignId=hawaii`, comme illustré dans l’image suivante. ![Modifier l’arrière-plan de la page](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)
6. Appuyez sur **Entrée** ; votre page affiche maintenant l’arrière-plan Hawaï. ![Modifier l’arrière-plan de la page](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)
7. À présent, remplacez la valeur par *Tokyo* et appuyez sur **Entrée**. La page affiche l’arrière-plan Tokyo. ![Modifier l’arrière-plan de la page](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Étape 9 : Modifier le reste du parcours utilisateur
Si vous accédez à la page de connexion et si vous cliquez sur le lien **Inscrivez-vous dès maintenant**, vous voyez l’arrière-plan par défaut, pas celui que vous avez défini. Cela est dû au fait que vous avez uniquement modifié la page de connexion ou d’inscription ; vous devez également modifier le reste des définitions de contenu Self-Asserted. Pour ce faire, effectuez les étapes ci-après :
* À l’étape 2 :
    * Téléchargez le fichier **selfasserted**.
    * Copiez le contenu du fichier.
    * Créez une vue **selfasserted**.
    * Ajoutez **selfasserted** au contrôleur **Accueil**.
* À l’étape 4 : 
    * Dans votre stratégie d’extension, recherchez le nœud `<ContentDefinition>` qui comprend `Id="api.selfasserted"`, `Id="api.localaccountsignup"` et `Id="api.localaccountpasswordreset"`.
    *  Définissez l’attribut `LoadUri` sur votre URI selfasserted.
* À l’étape 8.2, modifiez votre code pour accepter les paramètres de chaîne de requête. 
* Chargez la stratégie TrustFrameworkExtensions.xml et vérifiez que sa validation n’échoue pas.
* Exécutez le test de la stratégie, puis cliquez sur **Inscrivez-vous dès maintenant** pour voir le résultat.

## <a name="optional-download-the-complete-policy-files-and-code"></a>[Facultatif] Télécharger les fichiers de stratégie et le code complets
* Nous vous recommandons de créer votre scénario avec vos propres fichiers de stratégie personnalisée après avoir suivi la procédure pas à pas Bien démarrer avec les stratégies personnalisées, au lieu d’utiliser ces exemples de fichiers.  [Exemples de fichiers de stratégie de référence](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)
* Vous pouvez télécharger le code complet ici [Exemple de solution Visual Studio pour référence](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).




