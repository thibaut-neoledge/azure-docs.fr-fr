<properties pageTitle="How to delegate user registration and product subscription" metaKeywords="" description="Learn how to delegate user registration and product subscription to a third party in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to delegate user registration and product subscription in Azure API Management" authors="antonba" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="antonba"></tags>

# Délégation de l'inscription des utilisateurs et des abonnements aux produits

La délégation vous permet d'utiliser votre site web existant pour gérer les connexions/inscriptions des développeurs et l'abonnement aux produits au lieu de faire appel aux fonctionnalités intégrées du portail des développeurs. Ceci permet à votre site web de conserver les données utilisateur et de valider ces étapes de façon personnalisée.

## Dans cette rubrique

-   [Délégation de la connexion et de l'inscription des développeurs][]
-   [Délégation de l'abonnement aux produits][]

## <a name="delegate-signin-up"> </a>Délégation de la connexion et de l'inscription des développeurs

Pour déléguer les connexions et inscriptions des développeurs à votre site web existant, vous devez créer un point de terminaison de délégation spécifique sur votre site qui agit en tant que point d'entrée pour toutes les demandes de ce type émanant du portail des développeurs Gestion des API.

Le processus final se présente comme suit :

1.  Le développeur clique sur le lien de connexion ou d'inscription sur le portail des développeurs Gestion des API.
2.  Le navigateur est redirigé vers le point de terminaison de délégation.
3.  À son tour, le point de terminaison de délégation présente une interface demandant à l'utilisateur de se connecter ou de s'inscrire, ou bien le redirige vers cette interface.
4.  Si l'authentification est réussie, l'utilisateur revient à la page du portail des développeurs Gestion des API d'où il est parti.

Pour commencer, configurons Gestion des API pour que les demandes soient acheminées via votre point de terminaison de délégation. Dans le portail des éditeurs Gestion des API, sous l'en-tête **Portail des développeurs** dans le menu à gauche, cliquez sur **Délégation**, puis sur **Déléguer la connexion et l'inscription**.

![Delegation page][]

-   Décidez de l'URL de votre point de terminaison de délégation spécial, puis entrez-la dans le champ **URL du point de terminaison de délégation**.

-   Dans le champ **Clé d'authentification de la délégation**, entrez le secret utilisé pour calculer une signature qui vous sera fournie pour vérification afin de vous assurer que la demande provient bien de Gestion des API Azure.

﻿À présent, vous devez créer le **point de terminaison de délégation**. Il doit effectuer certaines actions :

1.  Recevoir une demande au format suivant :

    > *<http://www.yourwebsite.com/apimdelegation?operation=SignIn&redirectUrl>={URL de la page source}&salt={string}&sid={string}*

    Paramètres de requête pour le cas connexion/inscription :

    -   **operation** : identifie le type de demande de délégation. Il ne peut s'agir ici que de « SignIn ».
    -   **redirectUrl** : l'URL de la page dans laquelle l'utilisateur a cliqué sur un lien de connexion ou d'inscription.
    -   **salt** : chaîne salt spéciale utilisée pour calculer un code de hachage de sécurité.
    -   **sig** : code de hachage de sécurité calculé à comparer avec votre propre code de hachage.

2.  Vérifiez si la demande émane bien de Gestion des API Azure (facultatif, mais fortement recommandé pour assurer la sécurité).

    -   Calculez un code de hachage HMAC-SHA512 d'une chaîne basée sur les paramètres **redirectUrl** et **salt** de la requête :

        > HMAC(**redirectUrl** + '\\n' + **salt**)

    -   Comparez le code de hachage calculé plus haut avec la valeur du paramètre de requête **sig**. Si les deux codes de hachage correspondent, passez à l'étape suivante. Sinon, rejetez la demande.

3.  Vérifiez que vous recevez une demande pour une connexion/déconnexion : le paramètre de requête **operation** est défini sur **SignIn**.

4.  Présentez à l'utilisateur l'interface de connexion ou d'inscription.

5.  Si l'utilisateur s'inscrit, vous devez créer un compte dans Gestion des API. [Créez un utilisateur][] avec l'API REST de gestion de Gestion des API. Lors de cette opération, assurez-vous de bien définir l'identifiant utilisateur afin qu'il soit identique à celui de votre magasin utilisateur ou à un identifiant que vous pouvez suivre.

6.  Lorsque l'utilisateur est bien authentifié :

    -   [Demandez un jeton d'authentification unique (SSO)][] via l'API REST Gestion des API.

    -   Ajoutez un paramètre de requête returnUrl à l'URL SSO reçue de l'appel d'API ci-dessus :

        > Par exemple, <https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url>

    -   Redirigez l'utilisateur vers l'URL générée.

## <a name="delegate-product-subscription"> </a>Délégation de l'abonnement aux produits

La délégation de l'abonnement aux produits fonctionne de la même manière que la délégation de la connexion/inscription. Le processus final se présente comme suit :

1.  Le développeur sélectionne un produit dans le portail des développeurs Gestion des API, puis clique sur le bouton d'abonnement.
2.  Le navigateur est redirigé vers le point de terminaison de délégation.
3.  Le point de terminaison de délégation effectue les étapes nécessaires pour l'abonnement au produit. Il est de votre responsabilité d'éventuellement rediriger l'utilisateur vers une autre page pour obtenir des informations de facturation, demander des informations supplémentaires ou simplement stocker les informations sans aucune action de l'utilisateur.
4.  Si l'authentification est réussie, l'utilisateur revient à la page du portail des développeurs Gestion des API d'où il est parti.

Pour activer la fonctionnalité, dans la page **Délégation**, cliquez sur **Déléguer l'abonnement au produit**.

Assurez-vous ensuite que le point de terminaison de délégation effectue bien les actions suivantes :

1.  Recevoir une demande au format suivant :

    > *<http://www.yourwebsite.com/apimdelegation?operation>={opération}&productId={produit auquel s'abonner}&userId={utilisateur faisant la demande}&salt={string}&sid={string}*

    Paramètres de requête pour le cas abonnement à un produit :

    -   **operation** : identifie le type de demande de délégation. Pour l'abonnement à un produit, les options valides sont les suivantes :

        -   « Subscribe » : une demande d'abonnement de l'utilisateur à un produit donné avec l'identifiant fourni (voir ci-dessous).
        -   « Unsubscribe » : une demande de désabonnement de l'utilisateur pour un produit.
        -   « Renew » : une demande de renouvellement d'abonnement (par exemple en cas d'expiration).
    -   **productId** : l'ID du produit auquel l'utilisateur demande l'abonnement
    -   **userId** : l'ID de l'utilisateur pour qui la demande est envoyée
    -   **salt** : chaîne salt spéciale utilisée pour calculer un code de hachage de sécurité.
    -   **sig** : code de hachage de sécurité calculé à comparer avec votre propre code de hachage.

2.  Vérifiez si la demande émane bien de Gestion des API Azure (facultatif, mais fortement recommandé pour assurer la sécurité).

    -   Calculez un code de hachage HMAC-SHA512 d'une chaîne basée sur les paramètres de requête **ProductId**, **userId** et **salt** :

        > HMAC(**productId** + '\\n' + **userId** + '\\n' + **salt**)

    -   Comparez le code de hachage calculé plus haut avec la valeur du paramètre de requête **sig**. Si les deux codes de hachage correspondent, passez à l'étape suivante. Sinon, rejetez la demande.

3.  Effectuez le traitement de l'abonnement au produit en fonction du type de l'opération demandée dans **operation**, par exemple facturation, autres questions, etc.

4.  Après avoir correctement abonné l'utilisateur au produit de votre côté, abonnez l'utilisateur au produit Gestion des API en [appelant l'API REST pour l'abonnement au produit][].

5.  Redirigez l'utilisateur vers l'URL **redirectUrl** fournie lorsque vous avez reçu la demande.

  [Délégation de la connexion et de l'inscription des développeurs]: #delegate-signin-up
  [Délégation de l'abonnement aux produits]: #delegate-product-subscription
  [Delegation page]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
  [Créez un utilisateur]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
  [Demandez un jeton d'authentification unique (SSO)]: http://go.microsoft.com/fwlink/?LinkId=507409
  [appelant l'API REST pour l'abonnement au produit]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
