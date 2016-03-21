<properties 
	pageTitle="Comment sécuriser les services principaux à l'aide d'une authentification par certificat client dans la Gestion des API Azure" 
	description="Découvrez comment sécuriser des services principaux à l'aide d'une authentification par certificat client dans la Gestion des API Azure" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="sdanie"/>

# Comment sécuriser les services principaux à l'aide d'une authentification par certificat client dans la Gestion des API Azure

La Gestion des API permet de sécuriser l'accès au service principal d'une API en utilisant des certificats client. Ce guide explique comment gérer les certificats dans le portail des éditeurs de l’API et comment configurer une API pour utiliser un certificat et accéder à son service principal.

Pour en savoir plus sur la gestion des certificats à l’aide de l’API REST de gestion des API, consultez [Entité de certificat API REST de gestion des API Azure][].

## <a name="prerequisites"> </a>Configuration requise

Ce guide explique comment configurer votre instance de service de gestion des API afin d'utiliser l'authentification par certificat lient pour accéder au service principal d'une API. Avant de suivre la procédure présentée dans cette rubrique, vous devez configurer votre service principal pour l'authentification avec certificat client ([pour configurer l’authentification avec certificat dans Azure WebSites, consultez cet article][]) et avoir accès au certificat et au mot de passe associés afin de les charger dans le portail de publication de la gestion des API.

## <a name="step1"> </a>Chargement d’un certificat client

Pour commencer, cliquez sur **Gérer** dans le portail Azure Classic de votre service Gestion des API. Vous accédez au portail des éditeurs Gestion des API.

![Portail des éditeurs d’API][api-management-management-console]

>Si vous n'avez pas encore créé une instance de service Gestion des API, consultez la page [Création d'une instance de service Gestion des API][] dans le didacticiel [Prise en main de Gestion des API Azure][].

Cliquez sur **Sécurité** dans le menu **Gestion des API** de gauche, puis sur **Certificats clients**.

![Certificats clients][api-management-security-client-certificates]

Pour charger un nouveau certificat, cliquez sur **Charger un certificat**.

![Téléchargement d’un certificat][api-management-upload-certificate]

Accédez à votre certificat, puis entrez le mot de passe pour le certificat.

>Le certificat doit être au format **.pfx**. Les certificats auto-signés sont autorisés.

![Téléchargement d’un certificat][api-management-upload-certificate-form]

Cliquez sur **Charger** pour charger le certificat.

>Le mot de passe du certificat est validé à ce moment. S'il est incorrect, un message d'erreur s'affiche.

![Certificat chargé][api-management-certificate-uploaded]

Lorsque le certificat est chargé, il s'affiche dans l'onglet **Certificats clients**. Si vous avez plusieurs certificats, faites une note indiquant les quatre derniers caractères de l'empreinte numérique, qui sont utilisés pour sélectionner le certificat lors de la configuration d'une API pour l'utilisation de certificats, tel qu'indiqué dans la section suivante [Configuration d'une API afin d'utiliser un certificat pour l'authentification de passerelle][].

## <a name="step1a"> </a>Suppression d’un certificat client

Pour supprimer un certificat, cliquez sur **Supprimer** à côté du certificat en question.

![Suppression d'un certificat][api-management-certificate-delete]

Cliquez sur **Oui, le supprimer** pour confirmer la suppression.

![Confirmation de suppression][api-management-confirm-delete]

Si le certificat est en cours d'utilisation par une API, un écran d'avertissement s'affiche. Pour supprimer le certificat, vous devez d'abord le supprimer de toutes les API configurées pour l'utiliser.

![Confirmation de suppression][api-management-confirm-delete-policy]

## <a name="step2"> </a>Configuration d'une API afin d'utiliser un certificat pour l'authentification de passerelle

Cliquez sur **API** dans le menu **Gestion des API** de gauche, cliquez sur le nom de l'API désirée, puis cliquez sur l'onglet **Sécurité**.

![Sécurité API][api-management-api-security]

Sélectionnez **Certificats client** dans la liste déroulante **Avec informations d'identification**.

![Certificats clients][api-management-mutual-certificates]

Sélectionnez le certificat désiré dans la liste déroulante **Certificat client**. Si plusieurs certificats s'affichent, vous pouvez consulter le sujet ou les quatre derniers caractères de l'empreinte numérique (notés dans la section précédente) afin d'identifier le certificat correct.

![Sélection de certificat][api-management-select-certificate]

Cliquez sur **Enregistrer** pour enregistrer la modification de configuration de l'API.

>Cette modification s'applique immédiatement, et les appels d'opérations de cette API utiliseront désormais ce certificat pour s'authentifier sur le serveur principal.

![Enregistrement des modifications d'API][api-management-save-api]

>Lorsqu’un certificat est spécifié pour l’authentification passerelle d’un service principal d’une API, il est intégré à la stratégie de cette API et peut être affiché dans l’éditeur de stratégies.

![Stratégie de certificat][api-management-certificate-policy]

## Étapes suivantes

Pour plus d'informations sur les autres méthodes de sécurisation de votre service principal, telles que l’authentification HTTP de base ou partagée, regardez la vidéo suivante

> [AZURE.VIDEO last-mile-security]

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Prise en main de Gestion des API Azure]: api-management-get-started.md
[Get started with advanced API configuration]: api-management-get-started-advanced.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Création d'une instance de service Gestion des API]: api-management-get-started.md#create-service-instance

[Entité de certificat API REST de gestion des API Azure]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[pour configurer l’authentification avec certificat dans Azure WebSites, consultez cet article]: https://azure.microsoft.com/fr-FR/documentation/articles/app-service-web-configure-tls-mutual-auth/

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configuration d'une API afin d'utiliser un certificat pour l'authentification de passerelle]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps


 

<!---HONumber=AcomDC_0309_2016-->