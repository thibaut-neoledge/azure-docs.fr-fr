<properties pageTitle="How to create APIs in Azure API Management" metaKeywords="" description="Learn how to create and configure APIs in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to create APIs in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Création d'API dans Gestion des API Azure

Une API du service Gestion des API (version préliminaire) est un ensemble d'opérations qui peuvent être appelées par des applications clientes. Les nouvelles API sont créées dans la console de gestion. Les opérations souhaitées sont ensuite ajoutées. Une fois les opérations ajoutées, l'API est ajoutée à un produit et peut être publiée. Une fois l'API publiée, les développeurs peuvent s'y abonner et l'utiliser.

Ce guide présente la première étape du processus : créer et configurer une nouvelle API dans Gestion des API. Pour plus d'informations sur l'ajout d'opérations et la publication d'un produit, consultez les rubriques [Ajout d'opérations à une API][] et [Création et publication d'un produit][].

## Dans cette rubrique

-   [Création d'une API][]
-   [Configuration des paramètres de l'API][]
-   [Étapes suivantes][]

## <a name="create-new-api"> </a>Création d'une API

Pour créer et configurer des API, cliquez sur **Console de gestion** dans le portail Azure de votre instance de service Gestion des API. Vous accéderez au portail d'administration Gestion des API.

> Si vous n'avez pas encore créé une instance de service Gestion des API, consultez la page [Création d'une instance de service Gestion des API][] dans le didacticiel [Prise en main de Gestion des API Azure][].

![Management console][]

Cliquez sur **API** dans le menu **Gestion des API** à gauche, puis sur **Ajouter des API**.

![Create API][]

Utilisez la fenêtre **Ajouter une nouvelle API** pour configurer la nouvelle API.

![Add new API][]

Les trois champs suivants permettent de configurer la nouvelle API.

-   **Titre d'API web** fournit un nom descriptif unique pour l'API. Il s'affiche dans les portails des développeurs et de gestion.
-   L'**URL du service web** indique le service HTTP implémentant l'API. La gestion des API transmet les demandes à cette adresse.
-   Le **suffixe de l'URL d'API web** est ajouté à l'URL de base du service Gestion des API. L'URL de base est commune à toutes les API hébergées par une instance de service Gestion des API. Gestion des API distingue les API selon leur suffixe. Celui-ci doit donc être unique pour chaque API d'un éditeur donné.

Une fois les trois valeurs configurées, cliquez sur **Enregistrer**. Une fois l'API créée, la page Résumé de l'API s'affiche dans le portail de gestion.

![API summary][]

## <a name="configure-api-settings"> </a>Configuration des paramètres de l'API

L'onglet **Paramètres** permet de vérifier et de modifier la configuration d'une API. Les options **Titre d'API web**, **URL du service web** et **Suffixe de l'URL d'API web** sont définis initialement lors de la création de l'API. Vous pouvez les modifier ici. **Description** fournit une description facultative. **Avec informations d'identification** permet de configurer l'authentification HTTP de base.

![API settings][]

Pour configurer l'authentification HTTP de base pour le service web qui met en place l'API, sélectionnez **De base** dans la liste déroulante **Avec informations d'identification**, puis entrez les informations d'identification souhaitées.

![Basic authentication settings][]

Cliquez sur **Enregistrer** pour enregistrer les modifications apportées aux paramètres de l'API.

## <a name="next-steps"> </a>Étapes suivantes

Une fois l'API créée et les paramètres configurés, l'étape suivante consiste à ajouter les opérations à l'API, à ajouter l'API à un produit et à la publier pour la mettre à disposition des développeurs. Pour plus d'informations, consultez les guides suivants :

-   [Ajout d'opérations à une API][]
-   [Création et publication d'un produit][]

  [Ajout d'opérations à une API]: ../api-management-howto-add-operations
  [Création et publication d'un produit]: ../api-management-howto-add-products
  [Création d'une API]: #create-new-api
  [Configuration des paramètres de l'API]: #configure-api-settings
  [Étapes suivantes]: #next-steps
  [Création d'une instance de service Gestion des API]: ../api-management-get-started/#create-service-instance
  [Prise en main de Gestion des API Azure]: ../api-management-get-started
  [Management console]: ./media/api-management-howto-create-apis/api-management-management-console.png
  [Create API]: ./media/api-management-howto-create-apis/api-management-create-api.png
  [Add new API]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
  [API summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
  [API settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
  [Basic authentication settings]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
