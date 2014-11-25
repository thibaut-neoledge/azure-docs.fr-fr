<properties pageTitle="Get started with Azure API Management" metaKeywords="" description="Learn how to create APIs, operations, and get started with API Management." metaCanonical="" services="" documentationCenter="API Management" title="Get started with Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

# Prise en main de Gestion des API Azure

Ce guide décrit la prise en main rapide de Gestion des API et la création de votre premier appel d'API.

> Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][Version d'évaluation gratuite d'Azure].

La première étape de travail avec Gestion des API consiste à créer une instance de service. Connectez-vous au [portail de gestion][portail de gestion] et cliquez sur **Nouveau**, **Services d'application**, **Gestion des API**, **Créer**.

![API Management new instance][API Management new instance]

Pour l'**URL**, spécifiez un nom de sous-domaine unique à utiliser pour l'URL du service.

Choisissez l'**option de tarification**, l'**abonnement** et la **région** souhaités pour votre instance de service. Toutes les options de tarification peuvent être utilisées dans ce didacticiel. Une fois vos sélections effectuées, cliquez sur le bouton Suivant.

![New API Management service][New API Management service]

Entrez **Contoso Ltd.** pour le **nom de l'organisation**, ainsi que votre adresse de messagerie dans le champ correspondant pour l'administrateur.

> Cette adresse de messagerie est utilisée pour les notifications provenant du système Gestion des API. Pour plus d'informations, consultez la page [Configuration de notifications][Configuration de notifications].

Cliquez sur la coche pour créer votre instance de service.

![New API Management service][1]

![New API Management service][2]

Une fois l'instance de service créée, l'étape suivante consiste à créer une API.

## <a name="create-api"> </a>Création d'une API

Une API se compose d'un ensemble d'opérations pouvant être appelées à partir d'une application cliente. Les opérations de l'API sont transmises par proxy aux services web existants.

Chaque instance de service Gestion des API est préconfigurée avec un exemple d'API Echo sur laquelle vous pouvez appeler n'importe quel verbe HTTP. La valeur de retour sera identique à celle des en-têtes et du corps que vous avez envoyés. Ce didacticiel utilise le service web principal pour que l'API Echo crée une API dans Gestion des API, appelée **Mon service d'écho**.

Les API sont créées et configurées à partir de la console Gestion des API, accessible via le portail de gestion Azure. Pour accéder à la console Gestion des API, cliquez sur **Console de gestion** dans le portail Azure de votre service Gestion des API.

![New API Management console][New API Management console]

Pour créer **Mon API d'écho**, cliquez sur **API** dans le menu **Gestion des API** à gauche, puis sur **Ajouter des API**.

![Create API][Create API]

![Add new API][Add new API]

Les trois champs suivants permettent de configurer la nouvelle API.

-   Tapez **Mon API d'écho** dans la zone **Titre d'API web**. **Titre d'API web** fournit un nom descriptif unique pour l'API. Il s'affiche dans les portails des développeurs et de gestion.
-   Tapez **<http://echoapi.cloudapp.net/api>** dans l'**URL du service web**. L'**URL du service web** indique le service HTTP implémentant l'API. La gestion des API transmet les demandes à cette adresse.
-   Tapez **monécho** dans le **suffixe de l'URL d'API web**. Le **suffixe de l'URL d'API web** est ajouté à l'URL de base du service Gestion des API. Vos API partageront une URL de base commune et se distingueront par un suffixe unique ajouté après la base.

Cliquez sur **Enregistrer** pour créer l'API. Une fois l'API créée, la page Résumé de l'API s'affiche dans le portail de gestion.

![API summary][API summary]

> L'exemple d'API Echo n'utilise pas d'authentification, mais pour plus d'informations sur la configuration de l'authentification, consultez la rubrique [Configuration des paramètres de l'API][Configuration des paramètres de l'API].

## <a name="add-operation"> </a>Ajout d'une opération

Cliquez sur **Opérations** pour afficher le volet des opérations de l'API. Les définitions des opérations sont utilisées pour valider les demandes entrantes et générer automatiquement la documentation. Comme nous n'avons pas encore ajouté d'opérations, aucune ne s'affiche.

![Operations][Operations]

Cliquez sur **Ajouter une opération** pour ajouter une nouvelle opération. La fenêtre **Nouvelle opération** s'affiche et l'onglet **Signature** est sélectionné par défaut.

![Operation signature][Operation signature]

Dans cet exemple, nous spécifions une opération GET sur le service d'écho. Entrez les valeurs suivantes dans les champs de l'onglet **Signature**.

-   Tapez **GET** dans la zone de texte **Verbe HTTP**. Dès que vous commencez à taper, vous pouvez sélectionner **GET** dans la liste des verbes http affichée.
-   Tapez **/ressource** dans la zone de texte **Modèle d'URL**.
-   Tapez **ressource GET** dans la zone de texte de nom **Afficher**.
-   Tapez **Démonstration d'un appel GET sur un exemple de ressource. Il est traité par un "écho" principal qui renvoie une réponse égale à la demande (les en-têtes et le corps fournis sont renvoyés dès réception).** dans la zone de texte **Description**. Cette description permet de générer la documentation de cette opération lorsque les développeurs utilisent cette API.

Cliquez sur **Paramètres** pour configurer les paramètres de la chaîne de requête de cette opération. Pour ajouter un paramètre de requête, cliquez sur **Ajouter des paramètres de requête** et spécifiez les valeurs suivantes.

-   Tapez **param1** dans la zone de texte **Nom**.
-   Tapez **Un exemple de paramètre qui est obligatoire.** dans la zone de texte **Description**.
-   Cliquez sur le champ **Type**, puis choisissez **chaîne** dans la liste. Les types pris en charge sont **chaîne**, **nombre**, **booléen** et **DateHeure**.
-   Cliquez sur le champ **Valeurs**, tapez **exemple** dans la zone de texte et cliquez sur le signe plus pour ajouter le texte de la valeur par défaut au paramètre. Après l'ajout du texte par défaut, cliquez n'importe où hors du champ **Valeurs** pour fermer la fenêtre d'ajout de valeurs.
-   Activez la case à cocher **Obligatoire**.

Cliquez sur **Enregistrer** pour ajouter l'opération nouvellement configurée dans l'API.

## <a name="add-api-to-product"> </a>Ajout de la nouvelle API à un produit

Les développeurs doivent commencer par s'abonner à un produit avant de pouvoir créer des appels d'API. Un produit fournit un accès à une ou plusieurs API et peut contenir des restrictions d'accès telles que les quotas d'utilisation et les limites de débit. Au cours de cette étape du didacticiel, vous allez ajouter Mon API d'écho à un produit existant.

Cliquez sur **Produits** dans le menu **Gestion des API** à gauche pour afficher et configurer les produits disponibles dans cette instance d'API.

![Produits][Produits]

Par défaut, chaque instance Gestion des API est fournie avec deux exemples de produits :

-   **Starter**
-   **Illimité**

Dans ce didacticiel, vous allez utiliser le produit **Starter**. Cliquez sur **Starter** pour afficher les paramètres, y compris les API associées à ce produit.

![Add API][Add API]

Cliquez sur **Ajouter l'API au produit**.

![Add API][3]

Activez la case à cocher pour **Mon API d'écho** et cliquez sur **Enregistrer**.

![API added][API added]

Maintenant que **Mon API d'écho** est associée à un produit, les développeurs peuvent s'y abonner et commencer à utiliser l'API.

> Cette étape du didacticiel utilise le produit **Starter**, lequel est fourni préconfiguré et prêt à l'utilisation. Pour un guide détaillé sur la création et la publication d'un nouveau produit, consultez le guide [Création et publication d'un produit][Création et publication d'un produit].

L'utilisateur Administrateur est automatiquement abonné à tous les produits et peut accéder aux API auxquelles il offre l'accès. Il n'est donc pas nécessaire de s'abonner manuellement au produit qui vient d'être créé avant de pouvoir lancer un appel.

## <a name="call-operation"> </a>Appel d'une opération à partir du portail des développeurs

Les opérations peuvent être directement appelées depuis le portail des développeurs, ce qui permet d'afficher et de tester les opérations d'une API. Dans l'étape de ce didacticiel, vous allez appeler la méthode Get qui a été ajoutée à **Mon API d'écho**. Cliquez sur **Portail des développeurs** dans le menu en haut à droite du portail de gestion.

![Portail des développeurs][Portail des développeurs]

Cliquez sur **API** dans le menu en haut, puis sur **Mon API d'écho** pour afficher les opérations disponibles.

![Portail des développeurs][4]

Notez que la description et les paramètres ajoutés lors de la création de l'opération s'affichent, fournissant la documentation pour les développeurs qui utilisent cette opération.

Cliquez sur **ressource GET**, puis sur **Ouvrir la console**.

![Operation console][Operation console]

Entrez des valeurs pour les paramètres, spécifiez votre clé de développeur et cliquez sur **HTTP Get**.

![HTTP Get][HTTP Get]

Après l'appel d'une opération, le portail des développeurs affiche l'**URL requise** à partir du service principal, l'**état de la réponse**, les **en-têtes de la réponse** et tout **contenu de la réponse**.

![Response][Response]

## <a name="next-steps"> </a>Étapes suivantes

-   Configuration des stratégies
-   Personnalisation du portail des développeurs
-   Suivi des appels avec l'inspecteur d'API

  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/
  [portail de gestion]: https://manage.windowsazure.com/
  [API Management new instance]: ./media/api-management-get-started/api-management-create-instance-menu.png
  [New API Management service]: ./media/api-management-get-started/api-management-create-instance-step1.png
  [Configuration de notifications]: ../api-management-howto-configure-notifications
  [1]: ./media/api-management-get-started/api-management-create-instance-step2.png
  [2]: ./media/api-management-get-started/api-management-instance-created.png
  [New API Management console]: ./media/api-management-get-started/api-management-management-console.png
  [Create API]: ./media/api-management-get-started/api-management-create-api.png
  [Add new API]: ./media/api-management-get-started/api-management-add-new-api.png
  [API summary]: ./media/api-management-get-started/api-management-new-api-summary.png
  [Configuration des paramètres de l'API]: ../api-management-howto-create-apis/#configure-api-settings
  [Operations]: ./media/api-management-get-started/api-management-myecho-operations.png
  [Operation signature]: ./media/api-management-get-started/api-management-operation-signature.png
  [Produits]: ./media/api-management-get-started/api-management-list-products.png
  [Add API]: ./media/api-management-get-started/api-management-add-api-to-product.png
  [3]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
  [API added]: ./media/api-management-get-started/api-management-api-added-to-product.png
  [Création et publication d'un produit]: ../api-management-howto-add-products
  [Portail des développeurs]: ./media/api-management-get-started/api-management-developer-portal-menu.png
  [4]: ./media/api-management-get-started/api-management-developer-portal-myecho-api.png
  [Operation console]: ./media/api-management-get-started/api-management-developer-portal-myecho-api-console.png
  [HTTP Get]: ./media/api-management-get-started/api-management-invoke-get.png
  [Response]: ./media/api-management-get-started/api-management-invoke-get-response.png
