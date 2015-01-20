<properties pageTitle="Prise en main d'Azure API Management" metaKeywords="" description="Découvrez comment créer des API, des opérations et comment prendre en main la gestion des API." metaCanonical="" services="api-management" documentationCenter="API Management" title="Get started with Azure API Management" authors="sdanie" solutions="" manager="dwrede" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/18/2014" ms.author="sdanie" />

# Prise en main de Gestion des API Azure

Ce guide décrit la prise en main rapide de Gestion des API et la création de votre premier appel d'API.

> Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite Azure][].

La première étape de travail avec Gestion des API consiste à créer une instance de service. Connectez-vous au [Portail de gestion][] et cliquez sur **Nouveau**, **Services d'application**, **API Management**, **Créer**.

![API Management new instance][api-management-create-instance-menu]

Pour **l'URL**, spécifiez un nom de sous-domaine unique à utiliser pour l'URL du service.

Choisissez l'**abonnement** et la **région** souhaités pour votre instance de service. Une fois vos sélections effectuées, cliquez sur le bouton Suivant.

![New API Management service][api-management-create-instance-step1]

Entrez **Contoso Ltd.** pour le **nom de l'organisation**, ainsi que votre adresse de messagerie dans le champ correspondant pour l'administrateur.

>Cette adresse de messagerie est utilisée pour les notifications provenant du système Gestion des API. Pour plus d'informations, consultez [Configuration de notifications][].

![New API Management service][api-management-create-instance-step2]

Les instances du service API Management sont disponibles selon deux niveaux : Développeur et Standard. Par défaut, les instances du service API Management sont créées à l'aide du niveau Développeur. Pour sélectionner le niveau Standard, cochez la case **Paramètres avancés** et sélectionnez le niveau Standard dans l'écran suivant.

>Microsoft Azure offre deux niveaux auxquels vous pouvez exécuter votre service API Management : Développeur et Standard. Le niveau développeur est réservé aux programmes d'API de développement, de test et pilote pour lesquels la haute disponibilité n'est pas un problème. Au niveau Standard, vous pouvez étendre le nombre d'unités réservées pour gérer davantage de trafic. Le niveau Standard permet à votre service API Management de disposer de la puissance de traitement et des performances maximales. Ce didacticiel peut être effectué à l'aide du niveau de votre choix. Pour plus d'informations sur les niveaux du service API Management, consultez la page relative à la [tarification du service API Management][].

Cliquez sur la coche pour créer votre instance de service.

![New API Management service][api-management-instance-created]

Une fois l'instance de service créée, l'étape suivante consiste à créer une API.

## <a name="create-api"> </a>Création d'une API

Une API se compose d'un ensemble d'opérations pouvant être appelées à partir d'une application cliente. Les opérations de l'API sont transmises par proxy aux services web existants.

Chaque instance de service Gestion des API est préconfigurée avec un exemple d'API Echo sur laquelle vous pouvez appeler n'importe quel verbe HTTP. La valeur de retour sera identique à celle des en-têtes et du corps que vous avez envoyés. Ce didacticiel utilise le service web principal pour que l'API Echo crée une API dans le service API Management, appelée **Mon service d'écho**.

Les API sont créées et configurées à partir de la console Gestion des API, accessible via le portail de gestion Azure. Pour accéder à la console API Management, cliquez sur **Console de gestion** dans le portail Azure de votre service API Management.

![New API Management console][api-management-management-console]

Pour créer **Mon API d'écho**, cliquez sur **API** dans le menu **API Management** à gauche, puis sur **Ajouter des API**.

![Create API][api-management-create-api]

![Add new API][api-management-add-new-api]

Les trois champs suivants permettent de configurer la nouvelle API.

-	Tapez **Mon API d'écho** dans la zone **Titre d'API web**. **Titre d'API web** fournit un nom descriptif unique pour l'API. Il s'affiche dans les portails des développeurs et de gestion.
-	Tapez **http://echoapi.cloudapp.net/api** dans l'**URL du service web**. **L'URL du service web** fait référence au service HTTP qui implémente l'API. La gestion des API transmet les demandes à cette adresse.
-	Tapez **monécho** dans le **suffixe de l'URL d'API web**. **Le suffixe de l'URL d'API web** est ajouté à l'URL de base du service API Management. Vos API partageront une URL de base commune et se distingueront par un suffixe unique ajouté après la base.
-	Le **schéma d'URL d'API web** détermine quels sont les protocoles qui peuvent être utilisés pour accéder à l'API. Le protocole HTTPS est spécifié par défaut.

Cliquez sur **Enregistrer** pour créer l'API. Une fois l'API créée, la page Résumé de l'API s'affiche dans le portail de gestion.

![API summary][api-management-new-api-summary]


>L'exemple d'API Echo n'utilise pas d'authentification, mais pour plus d'informations sur la configuration de l'authentification, consultez [Configuration des paramètres de l'API][].


## <a name="add-operation"> </a>Ajout d'une opération

Cliquez sur **Opérations** pour afficher le volet des opérations de l'API. Les définitions des opérations sont utilisées pour valider les demandes entrantes et générer automatiquement la documentation. Comme nous n'avons pas encore ajouté d'opérations, aucune ne s'affiche.

![Operations][api-management-myecho-operations]

Cliquez sur **Ajouter une opération** pour ajouter une nouvelle opération. La fenêtre **Nouvelle opération** s'affiche et l'onglet **Signature** est sélectionné par défaut.

![Operation signature][api-management-operation-signature]

Dans cet exemple, nous spécifions une opération GET sur le service d'écho. Entrez les valeurs suivantes dans les champs de l'onglet **Signature**.

-	Tapez **GET** dans la zone de texte **Verbe HTTP**. Dès que vous commencez à taper, vous pouvez sélectionner **GET** dans la liste des verbes http affichée.
-	Tapez **/ressource** dans la zone de texte **Modèle d'URL**.
-	Tapez **ressource GET** dans la zone de texte **Nom d'affichage**.
-	Tapez **Démonstration d'un appel GET sur un exemple de ressource. Il est traité par un " écho " principal qui renvoie une réponse égale à la requête (les en-têtes et le corps fournis sont renvoyés dès réception)** dans la zone de texte **Description**. Cette description permet de générer la documentation de cette opération lorsque les développeurs utilisent cette API.

Cliquez sur **Paramètre**s pour configurer les paramètres de la chaîne de requête de cette opération. Pour ajouter un paramètre de requête, cliquez sur **Ajouter des paramètres de requête** et spécifiez les valeurs suivantes.

-	Tapez **param1** dans la zone de texte **Nom**.
-	Tapez **Un exemple de paramètre qui est obligatoire.** dans la zone de texte **Description**.
-	Cliquez sur le champ **Type**, puis choisissez **chaîne** dans la liste. Les types pris en charge sont **chaîne**, **nombre**, **booléen** et **DateHeure**.
-	Cliquez sur le champ **Valeurs**, tapez **exemple** dans la zone de texte, puis cliquez sur le signe plus pour ajouter le texte de la valeur par défaut au paramètre. Après l'ajout du texte par défaut, cliquez n'importe où hors du champ **Valeurs** pour fermer la fenêtre d'ajout de valeurs.
-	Cochez la case **Obligatoire**.

Cliquez sur **Enregistrer** pour ajouter l'opération nouvellement configurée dans l'API.


## <a name="add-api-to-product"> </a>Ajout de la nouvelle API à un produit

Les développeurs doivent commencer par s'abonner à un produit avant de pouvoir créer des appels d'API. Un produit fournit un accès à une ou plusieurs API et peut contenir des restrictions d'accès telles que les quotas d'utilisation et les limites de débit. Au cours de cette étape du didacticiel, vous allez ajouter Mon API d'écho à un produit existant.

Cliquez sur **Produits** dans le menu **API Management** à gauche pour afficher et configurer les produits disponibles dans cette instance d'API.

![Products][api-management-list-products]

Par défaut, chaque instance Gestion des API est fournie avec deux exemples de produits :

-	**Starter**
-	**Illimité**

Dans ce didacticiel, vous allez utiliser le produit **Starter**. Cliquez sur **Starter** pour afficher les paramètres, y compris les API associées à ce produit.

![Add API][api-management-add-api-to-product]

Cliquez sur **Ajouter l'API au produit**.

![Add API][api-management-add-myechoapi-to-product]

Cochez la case **Mon API d'écho**, puis cliquez sur **Enregistrer**.

![API added][api-management-api-added-to-product]

À présent que **Mon API d'écho** est associée à un produit, les développeurs peuvent s'y abonner et commencer à utiliser l'API.

>Cette étape du didacticiel utilise le produit **Starter**, lequel est fourni préconfiguré et prêt à l'utilisation. Pour obtenir un guide pas à pas sur la création et la publication d'un nouveau produit, consultez [Création et publication d'un produit][].

L'utilisateur Administrateur est automatiquement abonné à tous les produits et peut accéder aux API auxquelles il offre l'accès. Il n'est donc pas nécessaire de s'abonner manuellement au produit qui vient d'être créé avant de pouvoir lancer un appel.

## <a name="call-operation"> </a>Appel d'une opération à partir du portail des développeurs

Les opérations peuvent être directement appelées depuis le portail des développeurs, ce qui permet d'afficher et de tester les opérations d'une API. Dans l'étape de ce didacticiel, vous allez appeler la méthode Get qui a été ajoutée à **Mon API d'écho**. Cliquez sur **Portail des développeurs** dans le menu en haut à droite du Portail de gestion.

![Developer portal][api-management-developer-portal-menu]

Cliquez sur **API** dans le menu en haut, puis sur **Mon API d'écho** pour afficher les opérations disponibles.

![Developer portal][api-management-developer-portal-myecho-api]

Notez que la description et les paramètres ajoutés lors de la création de l'opération s'affichent, fournissant la documentation pour les développeurs qui utilisent cette opération.

Cliquez sur **ressource GET**, puis sur **Ouvrir la console**. 

![Operation console][api-management-developer-portal-myecho-api-console]

Entrez des valeurs pour les paramètres, spécifiez votre clé de développeur, puis cliquez sur **HTTP Get**.

€

Après l'appel d'une opération, le portail des développeurs affiche l'**URL demandée** à partir du service principal, l'**état de la réponse**, les **en-têtes de la réponse**, et tout **contenu de la réponse**. 

![Response][api-management-invoke-get-response]



## <a name="next-steps"> </a>Étapes suivantes

-   Configuration des stratégies
-   Personnalisation du portail des développeurs
-   Suivi des appels avec l'inspecteur d'API

[Version d'évaluation gratuite Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/

[Création d'une instance du service API Management]: #create-service-instance
[Création d'une API]: #create-api
[Ajout d'une opération]: #add-operation
[Ajout de la nouvelle API à un produit]: #add-api-to-product
[Abonnement au produit contenant l'API]: #subscribe
[Appel d'une opération à partir du portail des développeurs]: #call-operation
[Affichage des analyses]: #view-analytics
[Étapes suivantes]: #next-steps

[Configuration des paramètres de l'API]: ../api-management-howto-create-apis/#configure-api-settings
[Configurer les notifications]: ../api-management-howto-configure-notifications
[Réponses]: ../api-management-howto-add-operations/#responses
[Création et publication d'un produit]: ../api-management-howto-add-products
[Prise en main de la configuration avancée des API]: ../api-management-get-started-advanced
[Tarification d'API Management]: http://azure.microsoft.com/pricing/details/api-management/
[Portail de gestion]: https://manage.windowsazure.com/

[Configuration des stratégies]: ../api-management-howto-policies
[Personnalisation du portail des développeurs]: ../api-management-customize-portal
[Suivi des appels avec l'inspecteur d'API]: ../api-management-howto-api-inspector

[api-management-management-console]: ./media/api-management-get-started-b/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started-b/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started-b/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started-b/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started-b/api-management-instance-created.png
[api-management-create-api]: ./media/api-management-get-started-b/api-management-create-api.png
[api-management-add-new-api]: ./media/api-management-get-started-b/api-management-add-new-api.png
[api-management-new-api-summary]: ./media/api-management-get-started-b/api-management-new-api-summary.png
[api-management-myecho-operations]: ./media/api-management-get-started-b/api-management-myecho-operations.png
[api-management-operation-signature]: ./media/api-management-get-started-b/api-management-operation-signature.png
[api-management-list-products]: ./media/api-management-get-started-b/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started-b/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started-b/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started-b/api-management-api-added-to-product.png



[api-management-developer-portal-menu]: ./media/api-management-get-started-b/api-management-developer-portal-menu.png
[api-management-developer-portal-myecho-api]: ./media/api-management-get-started-b/api-management-developer-portal-myecho-api.png
[api-management-developer-portal-myecho-api-console]: ./media/api-management-get-started-b/api-management-developer-portal-myecho-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started-b/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started-b/api-management-invoke-get-response.png

<!--HONumber=35.2-->
