<properties
   pageTitle="Format de jeton utilisateur externe pour la version préliminaire d’Azure Active Directory B2B Collaboration | Microsoft Azure"
   description="Azure Active Directory B2B prend en charge les relations interentreprises en permettant aux partenaires commerciaux d’accéder de façon sélective à vos applications d’entreprise"
   services="active-directory"
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/27/2015"
   ms.author="viviali"/>

# Format de jeton utilisateur externe pour la version préliminaire d’Azure Active Directory (Azure AD) B2B Collaboration
Les revendications pour un jeton Azure AD standard sont décrites dans l’article [Types de jeton et de revendication pris en charge](active-directory-token-and-claims.md) sur azure.microsoft.com.

Les revendications différentes pour un utilisateur externe authentifié de collaboration B2B sont les suivantes :<br/> - **OID :** ID d’objet du locataire ressource<br/> - **TID** : ID de locataire à partir du client de la ressource<br/> - **Issuer** : client de la ressource<br/> - **IDP** : client de base de l’utilisateur<br/> - **AltSecId** : autre ID de sécurité, qui vous est caché<br/>

## Articles connexes
Consultez les autres articles sur Azure B2B Collaboration :

- [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Fonctionnement](active-directory-b2b-how-it-works.md)
- [Procédure pas à pas](active-directory-b2b-detailed-walkthrough.md)
- [Référence du format de fichier CSV](active-directory-b2b-references-csv-file-format.md)
- [Modifications de l’attribut d’objet utilisateur externe](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limites actuelles de la version préliminaire](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=Nov15_HO1-->