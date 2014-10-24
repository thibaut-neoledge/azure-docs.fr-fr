<properties pageTitle="How to create and use groups to manage developer accounts in Azure API Management" metaKeywords="" description="Learn how to manage developer accounts using groups in Azure API Management" metaCanonical="" services="" documentationCenter="API Management" title="How to create and use groups to manage developer accounts in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Création et utilisation de groupes pour gérer les comptes de développeurs dans Gestion des API Azure

Dans Gestion des API (version préliminaire), les groupes permettent de gérer la visibilité des produits pour les développeurs. Les produits sont d'abord visibles pour les groupes. Les développeurs de ces groupes peuvent afficher les produits associés aux groupes et s'y abonner.

Le service Gestion des API possède les groupes intégrés suivants.

-   **Administrateurs** - Les administrateurs gèrent les instances du service Gestion des API, créant les API, opérations et produits qui sont utilisés par les développeurs.
-   **Développeurs** - Les développeurs sont les clients qui génèrent des applications grâce à vos API. Les développeurs bénéficient d'un accès au portail des développeurs et génèrent des applications qui appellent les opérations d'une API.
-   **Invités** - Les utilisateurs non authentifiés, tels que les prospects, qui consultent le portail des développeurs d'une instance Gestion des API appartiennent à ce groupe. Ils peuvent recevoir certains accès en lecture seule, comme la possibilité d'afficher les API, mais pas de les appeler.

En plus de ces groupes intégrés, les administrateurs peuvent créer des groupes personnalisés. Ces derniers possèdent les mêmes privilèges que le groupe de développeurs intégré, et peuvent être utilisés pour gérer plusieurs groupes de développeurs. Par exemple, vous pouvez créer un groupe personnalisé pour les développeurs qui utiliseront les API à partir d'un produit, et un autre groupe de développeurs qui utiliseront les API à partir d'un autre produit.

Ce guide explique comment les administrateurs de l'instance Gestion des API peuvent ajouter de nouveaux groupes et les associer à des produits et des développeurs.

## Dans cette rubrique

-   [Création d'un groupe][]
-   [Association d'un groupe à un produit][]
-   [Association des groupes aux développeurs][]
-   [Étapes suivantes][]

## <a name="create-group"> </a>Création d'un groupe

Pour créer un groupe, cliquez sur **Console de gestion** dans le portail Azure de votre service Gestion des API. Vous accéderez au portail d'administration Gestion des API.

> Si vous n'avez pas encore créé une instance de service Gestion des API, consultez la page [Création d'une instance de service Gestion des API][] dans le didacticiel [Prise en main de Gestion des API Azure][].

![API Management console][]

Cliquez sur **Groupes** dans le menu **Gestion des API** à gauche, puis sur **Ajouter un groupe**.

![Add new group][]

Entrez un nom unique pour le groupe, et éventuellement une description, puis cliquez sur **Enregistrer**.

![Add new group][1]

Le nouveau groupe s'affiche dans l'onglet des groupes. Pour modifier le **nom** ou la **description** du groupe, cliquez sur le nom du groupe dans la liste. Pour supprimer le groupe, cliquez sur **supprimer**.

![Group added][]

Maintenant que le groupe est créé, il peut être associé à des produits et des développeurs.

## <a name="associate-group-product"> </a>Association d'un groupe à un produit

Pour associer un groupe à un produit, cliquez sur **Produits** dans le menu **Gestion des API** sur la gauche, puis cliquez sur le nom du produit souhaité.

![Set visibility][]

Utilisez l'onglet **Visibilité** pour ajouter et supprimer des groupes et pour voir les groupes actuellement associés au produit. Pour ajouter ou supprimer des groupes, activez ou désactivez les cases à cocher des groupes voulus, puis cliquez sur **Enregistrer**.

![Set visibility][2]

> Pour configurer les groupes depuis l'onglet **Visibilité** d'un produit, cliquez sur **Gérer des groupes**.

Une fois le produit associé à un groupe, les développeurs de ce groupe peuvent le voir et s'y abonner.

## <a name="associate-group-developer"> </a>Association des groupes aux développeurs

Pour associer des groupes aux développeurs, cliquez sur **Développeurs** dans le menu **Gestion des API** sur la gauche, puis activez la case à cocher à côté des développeurs à associer au groupe.

![Add developer to group][]

Une fois que les développeurs souhaités sont sélectionnés, cliquez sur le groupe dans la liste déroulante **Ajouter au groupe**. Les développeurs peuvent être retirés des groupes via la liste déroulante **Supprimer du groupe**.

![Développeurs][]

Une fois l'association entre le développeur et le groupe ajoutée, vous pouvez la consulter dans l'onglet **Développeurs**.

## <a name="next-steps"> </a>Étapes suivantes

Une fois le développeur ajouté à un groupe, il peut voir tous les produits associés à ce groupe et s'y abonner. Pour plus d'informations, consultez la page [Création et publication d'un produit dans Gestion des API Azure][].

  [Création d'un groupe]: #create-group
  [Association d'un groupe à un produit]: #associate-group-product
  [Association des groupes aux développeurs]: #associate-group-developer
  [Étapes suivantes]: #next-steps
  [Création d'une instance de service Gestion des API]: ../api-management-get-started/#create-service-instance
  [Prise en main de Gestion des API Azure]: ../api-management-get-started
  [API Management console]: ./media/api-management-howto-create-groups/api-management-management-console.png
  [Add new group]: ./media/api-management-howto-create-groups/api-management-add-group.png
  [1]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
  [Group added]: ./media/api-management-howto-create-groups/api-management-new-group.png
  [Set visibility]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
  [2]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
  [Add developer to group]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
  [Développeurs]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png
  [Création et publication d'un produit dans Gestion des API Azure]: ../api-management-howto-add-products
