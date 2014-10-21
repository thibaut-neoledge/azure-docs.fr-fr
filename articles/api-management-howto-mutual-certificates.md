<properties pageTitle="How to secure back-end services using mutual certificate authentication in Azure API Management" metaKeywords="" description="Learn how to secure back-end services using mutual certificate authentication in Azure API Management." metaCanonical="" services="api-management" documentationCenter="API Management" title="How to secure back-end services using mutual certificate authentication in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Comment sécuriser les services principaux à l'aide d'une authentification de certificat mutuel dans la Gestion des API Azure

La Gestion des API permet de sécuriser l'accès au service principal d'une API en utilisant des certificats mutuels. Ce guide explique comment gérer les certificats dans la console de Gestion des API et comment configurer une API pour utiliser un certificat et accéder à son service principal.

> Pour plus d'informations sur la gestion des certificats à l'aide de l'API REST de Gestion des API, voir la section [Entité de certificat API REST de Gestion des API Azure][].

## Dans cette rubrique

-   [Configuration requise][]
-   [Chargement d'un certificat client][]
-   [Suppression d'un certificat client][]
-   [Configuration d'une API afin d'utiliser l'authentification mutuelle des certificats pour proxy][]

## <a name="prerequisites"> </a>Conditions préalables

Ce guide explique comment configurer votre instance de service Gestion des API afin d'utiliser l'authentification mutuelle des certificats pour accéder au service principal d'une API. Avant de passer à la procédure de cette rubrique, vous devez configurer votre service principal pour l'authentification mutuelle des certificats, et avoir accès au certificat et au mot de passe associé afin de les charger dans la console de Gestion des API.

## <a name="step1"> </a>Chargement d'un certificat client

Pour commencer, cliquez sur **Console de gestion** dans le portail Azure pour votre service Gestion des API. Vous accéderez au portail d'administration Gestion des API.

![API Management console][]

> Si vous n'avez pas encore créé une instance de service Gestion des API, consultez la page [Création d'une instance de service Gestion des API][] dans le didacticiel [Prise en main de Gestion des API Azure][].

Cliquez sur **Sécurité** dans le menu **Gestion des API** de gauche, puis sur **Certificats clients**.

![Certificats clients][]

Pour charger un nouveau certificat, cliquez sur **Charger un certificat**.

![Chargement d'un certificat][]

Accédez à votre certificat, puis entrez le mot de passe pour le certificat.

> Le certificat doit être au format **.pfx**. Les certificats auto-signés sont autorisés.

![Chargement d'un certificat][1]

Cliquez sur **Charger** pour charger le certificat.

> Le mot de passe du certificat est validé à ce moment. S'il est incorrect, un message d'erreur s'affiche.

![Certificat chargé][]

Lorsque le certificat est chargé, il s'affiche dans l'onglet **Certificats clients**. Si vous avez plusieurs certificats, créez une note à ce sujet ou indiquant les quatre derniers caractères de l'empreinte numérique, utilisés pour sélectionner le certificat lors de la configuration d'une API pour l'utilisation de certificats, tel qu'indiqué dans la section suivante [Configuration d'une API afin d'utiliser l'authentification mutuelle des certificats pour proxy][].

## <a name="step1a"> </a>Suppression d'un certificat client

Pour supprimer un certificat, cliquez sur **Supprimer** à côté du certificat en question.

![Suppression d'un certificat][]

Cliquez sur **Oui, le supprimer** pour confirmer la suppression.

![Confirmation de suppression][]

Si le certificat est en cours d'utilisation par une API, un écran d'avertissement s'affiche. Pour supprimer le certificat, vous devez d'abord le supprimer de toutes les API configurées pour l'utiliser.

![Confirmation de suppression][2]

## <a name="step2"> </a>Configuration d'une API afin d'utiliser l'authentification mutuelle des certificats pour proxy

Cliquez sur **API** dans le menu **Gestion des API** de gauche, cliquez sur le nom de l'API désirée, puis cliquez sur l'onglet **Sécurité**.

![Sécurité API][]

Sélectionnez **Certificats mutuels** dans la liste déroulante **Avec informations d'identification**.

![Certificats mutuels][]

Sélectionnez le certificat désiré dans la liste déroulante **Certificat client**. Si plusieurs certificats s'affichent, vous pouvez consulter le sujet ou les quatre derniers caractères de l'empreinte numérique (notés dans la section précédente) afin d'identifier le certificat correct.

![Sélection de certificat][]

Cliquez sur **Enregistrer** pour enregistrer la modification de configuration de l'API.

> Cette modification s'applique immédiatement, et les appels d'opérations de cette API utiliseront désormais ce certificat pour s'authentifier sur le serveur principal.

![Enregistrement des modifications d'API][]

> Lorsqu'un certificat est spécifié pour l'authentification proxy d'un service principal d'une API, il est intégré à la stratégie de cette API et peut être affiché dans l'éditeur de stratégies.

![Stratégie de certificat][]

  [Entité de certificat API REST de Gestion des API Azure]: http://msdn.microsoft.com/library/azure/dn783483.aspx
  [Configuration requise]: #prerequisites
  [Chargement d'un certificat client]: #step1
  [Suppression d'un certificat client]: #step1a
  [Configuration d'une API afin d'utiliser l'authentification mutuelle des certificats pour proxy]: #step2
  [API Management console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
  [Création d'une instance de service Gestion des API]: ../api-management-get-started/#create-service-instance
  [Prise en main de Gestion des API Azure]: ../api-management-get-started
  [Certificats clients]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
  [Chargement d'un certificat]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
  [1]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
  [Certificat chargé]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
  [Suppression d'un certificat]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
  [Confirmation de suppression]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
  [2]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png
  [Sécurité API]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
  [Certificats mutuels]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
  [Sélection de certificat]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
  [Enregistrement des modifications d'API]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
  [Stratégie de certificat]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
