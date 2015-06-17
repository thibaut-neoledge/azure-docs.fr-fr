<properties 
	pageTitle="Prise en main de Gestion des API Azure" 
	description="Découvrez comment créer des API et des opérations et comment prendre en main Gestion des API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="sdanie"/>

# Prise en main de Gestion des API Azure

Ce guide décrit la prise en main rapide de Gestion des API et la création de votre premier appel d'API.

## Dans cette rubrique

-   [Création d'une instance Gestion des API][Création d'une instance Gestion des API]
-   [Création d'une API][Création d'une API]
-   [Ajout d'une opération][Ajout d'une opération]
-   [Ajout de la nouvelle API à un produit][Ajout de la nouvelle API à un produit]
-   [Abonnement au produit contenant l'API][Abonnement au produit contenant l'API]
-   [Appel d'une opération à partir du portail des développeurs][Appel d'une opération à partir du portail des développeurs]
-   [Affichage des analyses][Affichage des analyses]
-   [Étapes suivantes][Étapes suivantes]

## <a name="create-service-instance"> </a>Création d'une instance Gestion des API

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

Chaque instance Gestion des API est préconfigurée avec un exemple d'API Echo renvoyant l'entrée qui lui a été envoyée. Pour l'utiliser, vous pouvez appeler un verbe HTTP, et la valeur renvoyée sera égale aux en-têtes et au corps que vous avez envoyés.

Ce didacticiel utilise le service web http://echoapi.cloudapp.net/api pour créer une API dans Gestion des API appelée **Mon service d'écho**.

Les API sont créées et configurées à partir de la console Gestion des API, accessible via le portail de gestion Azure. Pour accéder à la console Gestion des API, cliquez sur **Console de gestion** dans le portail Azure de votre service Gestion des API.

![New API Management console][New API Management console]

Pour créer **Mon API d'écho**, cliquez sur **API** dans le menu **Gestion des API** à gauche, puis sur **Ajouter des API**.

![Create API][Create API]

![Add new API][Add new API]

Les trois champs suivants permettent de configurer la nouvelle API.

-   Tapez **Mon API d'écho** dans la zone **Titre d'API web**. **Titre d'API web** fournit un nom descriptif unique pour l'API. Il s'affiche dans les portails des développeurs et de gestion.
-   Tapez **http://echoapi.cloudapp.net/api** dans l'**URL du service web**. L'**URL du service web** indique le service HTTP implémentant l'API. La gestion des API transmet les demandes à cette adresse.
-   Tapez **monécho** dans le **suffixe de l'URL d'API web**. Le **suffixe de l'URL d'API web** est ajouté à l'URL de base du service Gestion des API. Vos API partageront une URL de base commune et se distingueront par un suffixe unique ajouté après la base.

Cliquez sur **Enregistrer** pour créer l'API. Une fois l'API créée, la page Résumé de l'API s'affiche dans le portail de gestion.

![API summary][API summary]

La section API comporte quatre onglets. L'onglet **Résumé** affiche les mesures de base et les informations concernant l'API. L'onglet **Paramètres** permet d'afficher et de modifier la configuration d'une API, y compris les informations d'identification d'authentification pour le service principal. L'onglet **Opérations** permet de gérer les opérations de l'API et est utilisé dans l'étape suivante du didacticiel. L'onglet **Problèmes** permet d'afficher les problèmes signalés par les développeurs utilisant vos API.

> L'exemple d'API Echo n'utilise pas d'authentification, mais pour plus d'informations sur la configuration de l'authentification, consultez la rubrique [Configuration des paramètres de l'API][Configuration des paramètres de l'API].

Une fois l'API créée et les paramètres configurés, l'étape suivante consiste à ajouter les opérations à l'API. Les définitions des opérations sont utilisées pour valider les demandes entrantes et générer automatiquement la documentation.

## <a name="add-operation"> </a>Ajout d'une opération

Cliquez sur **Opérations** pour afficher le volet des opérations de l'API. Comme nous n'avons pas encore ajouté d'opérations, aucune ne s'affiche.

![Operations][Operations]

Cliquez sur **Ajouter une opération** pour ajouter une nouvelle opération. La fenêtre **Nouvelle opération** s'affiche et l'onglet **Signature** est sélectionné par défaut.

![Operation signature][Operation signature]

Dans cet exemple, nous spécifions une opération GET sur le service d'écho. Entrez les valeurs suivantes dans les champs de l'onglet **Signature**.

-   Tapez **GET** dans la zone de texte **Verbe HTTP**. Dès que vous commencez à taper, vous pouvez sélectionner **GET** dans la liste des verbes http affichée.
-   Tapez **/ressource** dans la zone de texte **Modèle d'URL**.
-   Tapez **ressource GET** dans la zone de texte de nom **Afficher**.
-   Tapez **Démonstration d'un appel GET sur un exemple de ressource. Il est traité par un "écho" principal qui renvoie une réponse égale à la demande (les en-têtes et le corps fournis sont renvoyés dès réception).** dans la zone de texte **Description**. Cette description permet de générer la documentation de cette opération lorsque les développeurs utilisent cette API.

Cliquez sur **Paramètres** pour configurer les paramètres de la chaîne de requête de cette opération. Cet exemple comporte deux paramètres de chaîne de requête. Pour ajouter un paramètre de requête, cliquez sur **Ajouter des paramètres de requête** et spécifiez les valeurs suivantes.

Pour le premier paramètre de requête, configurez les valeurs suivantes.

-   Tapez **param1** dans la zone de texte **Nom**.
-   Tapez **Un exemple de paramètre qui est obligatoire.** dans la zone de texte **Description**.
-   Cliquez sur le champ **Type**, puis choisissez **chaîne** dans la liste. Les types pris en charge sont **chaîne**, **nombre**, **booléen** et **DateHeure**.
-   Cliquez sur le champ **Valeurs**, tapez **exemple** dans la zone de texte et cliquez sur le signe plus pour ajouter le texte de la valeur par défaut au paramètre. Après l'ajout du texte par défaut, cliquez n'importe où hors du champ **Valeurs** pour fermer la fenêtre d'ajout de valeurs.
-   Activez la case à cocher **Obligatoire**.

Pour le second paramètre de requête, entrez les valeurs suivantes.

-   **Nom** : **param2**
-   **Description** : **Un autre exemple de paramètre, défini comme non obligatoire.**
-   **Type** : **nombre**

Il est conseillé de fournir des exemples de réponses pour tous les codes d'état que l'opération peut produire. Chaque code d'état peut avoir plusieurs exemples de corps de réponse, un pour chacun des types de contenu pris en charge. Dans ce didacticiel, nous ajoutons un code de réponse **200 OK**.

Cliquez sur **Ajouter** dans la section Réponses, commencez par taper **200** dans la zone de texte, puis sélectionnez **200 OK** dans la liste déroulante.

![Add response][Add response]

Une fois **200 OK** sélectionné, un nouveau code de réponse est ajouté à l'opération et la fenêtre de réponse s'affiche. Tapez **Renvoyé dans tous les cas.** dans la zone de texte **Description**.

![Add response][3]

> **Ajouter des représentations** permet de configurer des réponses dans plusieurs représentations. Pour plus d’informations, consultez la rubrique [Réponses][Réponses].

Cliquez sur **Enregistrer** pour ajouter l'opération nouvellement configurée dans l'API.

## <a name="add-api-to-product"> </a>Ajout de la nouvelle API à un produit

Les développeurs doivent commencer par s'abonner à un produit avant de pouvoir créer des appels d'API. Un produit fournit un accès à une ou plusieurs API et peut contenir des restrictions d'accès telles que les quotas d'utilisation et les limites de débit. Au cours de cette étape du didacticiel, vous allez ajouter Mon API d'écho à un produit existant.

Cliquez sur **Produits** dans le menu **Gestion des API** à gauche pour afficher et configurer les produits disponibles dans cette instance d'API.

![Products][Products]

Par défaut, chaque instance Gestion des API est fournie avec deux exemples de produits :

-   **Starter**
-   **Illimité**

Dans ce didacticiel, vous allez utiliser le produit **Starter**. Cliquez sur **Starter** pour afficher les paramètres, y compris les API associées à ce produit.

![Add API][Add API]

Cliquez sur **Ajouter l'API au produit**.

![Add API][4]

Activez la case à cocher pour **Mon API d'écho** et cliquez sur **Enregistrer**.

![API added][API added]

Maintenant que **Mon API d'écho** est associée à un produit, les développeurs peuvent s'y abonner et commencer à utiliser l'API.

> Cette étape du didacticiel utilise le produit Starter, lequel est fourni préconfiguré et prêt à l'utilisation. Pour un guide détaillé sur la création et la publication d'un nouveau produit, consultez le guide [Création et publication d'un produit][Création et publication d'un produit].

## <a name="subscribe"> </a>Abonnement au produit contenant l'API

Pour créer des appels à une API, les développeurs doivent commencer par s'abonner à un produit qui leur permet d'y accéder. Les développeurs peuvent s'abonner aux produits dans le portail des développeurs, et les administrateurs dans la console de gestion. Vous êtes un administrateur par défaut puisque vous avez créé l'instance Gestion des API lors des étapes précédentes de ce didacticiel. Vous allez donc abonner un compte au produit **Starter**.

Cliquez sur **Développeurs** dans le menu **Gestion des API** à gauche pour afficher et configurer les développeurs dans cette instance de service.

![Developers][Developers]

Cliquez sur le nom du développeur pour configurer les paramètres de l'utilisateur, y compris les abonnements.

> Dans cet exemple, nous abonnons un développeur nommé Clayton Gragg. Si vous n'avez pas de comptes de développeur créés, vous pouvez abonner le compte d'administrateur. Pour plus d’informations sur la création de comptes de développeurs, consultez la page [Gestion des comptes de développeurs dans Gestion des API Azure][Gestion des comptes de développeurs dans Gestion des API Azure].

![Add subscription][Add subscription]

Cliquez sur **Ajouter un abonnement**.

![Add subscription][5]

Activez la case à cocher pour **Starter** et cliquez sur **S'abonner**.

![Subscription added][Subscription added]

Une fois votre compte de développeur abonné, vous pouvez appeler les API de ce produit.

## <a name="call-operation"> </a>Appel d'une opération à partir du portail des développeurs

Les opérations peuvent être directement appelées depuis le portail des développeurs, ce qui permet d'afficher et de tester les opérations d'une API. Dans l'étape de ce didacticiel, vous allez appeler la méthode Get qui a été ajoutée à **Mon API d'écho**. Cliquez sur **Portail des développeurs** dans le menu en haut à droite du portail de gestion.

![Developer portal][Developer portal]

Cliquez sur **API** dans le menu en haut, puis sur **Mon API d'écho** pour afficher les opérations disponibles.

![Developer portal][6]

Notez que la description et les paramètres ajoutés lors de la création de l'opération s'affichent, fournissant la documentation pour les développeurs qui utilisent cette opération.

Cliquez sur **ressource GET**, puis sur **Ouvrir la console**.

![Operation console][Operation console]

Entrez des valeurs pour les paramètres, spécifiez votre clé de développeur et cliquez sur **HTTP Get**.

![HTTP Get][HTTP Get]

Après l'appel d'une opération, le portail des développeurs affiche l'**URL requise** à partir du service principal, l'**état de la réponse**, les **en-têtes de la réponse** et tout **contenu de la réponse**.

![Response][Response]

## <a name="view-analytics"> </a>Affichage des analyses

Pour afficher les analyses de **Mon API d'écho**, revenez au portail d'administration en sélectionnant **Gérer** dans le menu utilisateur en haut à droite du portail des développeurs.

![Gérer][Gérer]

La vue par défaut pour le portail d'administration correspond au tableau de bord, lequel offre un aperçu de votre instance Gestion des API.

![Tableau de bord][Tableau de bord]

Passez la souris sur le graphique de Mon API d'écho pour afficher les mesures spécifiques de l'utilisation de l'API à une période donnée.

> Si vous ne voyez aucune ligne sur votre graphique, revenez au portail des développeurs et créez des appels dans l'API, patientez et revenez au tableau de bord.

![Analytics][Analytics]

Cliquez sur **Afficher les détails** pour afficher la page Résumé de l'API, incluant une version plus importante des mesures affichées.

![Résumé][Résumé]

Pour des mesures et des rapports détaillés, cliquez sur **Analyse** dans le menu **Gestion des API** à gauche.

![Vue d'ensemble][Vue d'ensemble]

La section **Analyse** comporte les quatre onglets suivants.

-   **Aperçu rapide** présente l'utilisation générale et les mesures d'intégrité ainsi que les développeurs, les produits, les API et les principales opérations.
-   **Utilisation** fournit une présentation détaillée des appels d'API et de la bande passante, y compris une représentation géographique.
-   **Intégrité** se concentre sur les codes d'état, les taux de réussite en cache, les temps de réponse et les temps de réponse d'API et de service.
-   **Activité** fournit des rapports qui présentent une analyse des activités spécifiques par développeur, produit, API et opération.

## <a name="next-steps"> </a>Étapes suivantes

-   Consultez les autres rubriques du didacticiel [Prise en main de la configuration avancée des API][Prise en main de la configuration avancée des API].

  [Création d'une instance Gestion des API]: #create-service-instance
  [Création d'une API]: #create-api
  [Ajout d'une opération]: #add-operation
  [Ajout de la nouvelle API à un produit]: #add-api-to-product
  [Abonnement au produit contenant l'API]: #subscribe
  [Appel d'une opération à partir du portail des développeurs]: #call-operation
  [Affichage des analyses]: #view-analytics
  [Étapes suivantes]: #next-steps
  [Version d'évaluation gratuite d'Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a
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
  [Add response]: ./media/api-management-get-started/api-management-add-response.png
  [3]: ./media/api-management-get-started/api-management-add-response-window.png
  [Réponses]: ../api-management-howto-add-operations/#responses
  [Products]: ./media/api-management-get-started/api-management-list-products.png
  [Add API]: ./media/api-management-get-started/api-management-add-api-to-product.png
  [4]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
  [API added]: ./media/api-management-get-started/api-management-api-added-to-product.png
  [Création et publication d'un produit]: ../api-management-howto-add-products
  [Developers]: ./media/api-management-get-started/api-management-developers.png
  [Gestion des comptes de développeurs dans Gestion des API Azure]: ../api-management-howto-create-or-invite-developers/
  [Add subscription]: ./media/api-management-get-started/api-management-add-subscription.png
  [5]: ./media/api-management-get-started/api-management-add-subscription-window.png
  [Subscription added]: ./media/api-management-get-started/api-management-subscription-added.png
  [Developer portal]: ./media/api-management-get-started/api-management-developer-portal-menu.png
  [6]: ./media/api-management-get-started/api-management-developer-portal-myecho-api.png
  [Operation console]: ./media/api-management-get-started/api-management-developer-portal-myecho-api-console.png
  [HTTP Get]: ./media/api-management-get-started/api-management-invoke-get.png
  [Response]: ./media/api-management-get-started/api-management-invoke-get-response.png
  [Gérer]: ./media/api-management-get-started/api-management-manage-menu.png
  [Tableau de bord]: ./media/api-management-get-started/api-management-dashboard.png
  [Analytics]: ./media/api-management-get-started/api-management-mouse-over.png
  [Résumé]: ./media/api-management-get-started/api-management-api-summary-metrics.png
  [Vue d'ensemble]: ./media/api-management-get-started/api-management-analytics-overview.png
  [Prise en main de la configuration avancée des API]: ../api-management-get-started-advanced

<!--HONumber=46--> 
 