---
title: Format de jeton utilisateur externe pour la version préliminaire d’Azure Active Directory B2B Collaboration | Microsoft Docs
description: Azure Active Directory B2B prend en charge les relations interentreprises en permettant aux partenaires commerciaux d’accéder de façon sélective à vos applications d’entreprise
services: active-directory
documentationcenter: ''
author: viv-liu
manager: cliffdi
editor: ''
tags: ''

ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/09/2016
ms.author: viviali

---
# Version préliminaire d’Azure AD B2B Collaboration : Format de jeton utilisateur externe
Les revendications pour un jeton Azure AD standard sont décrites dans l’article [Types de jeton et de revendication pris en charge](active-directory-token-and-claims.md) sur azure.microsoft.com.

Les revendications qui sont différentes pour un utilisateur externe authentifié de collaboration B2B sont les suivantes :<br/>

* **OID :** l’ID d’objet du client de la ressource<br/>
* **TID :** l’ID de client du client de la ressource<br/>
* **Émetteur** : le client de la ressource<br/>
* **IDP**: le client de base de l’utilisateur<br/>
* **AltSecId** : l’autre ID de sécurité, qui vous est caché<br/>

## Articles connexes
Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu'est-ce que la collaboration B2B d'Azure AD ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Fonctionnement](active-directory-b2b-how-it-works.md)
* [Procédure pas à pas](active-directory-b2b-detailed-walkthrough.md)
* [Référence du format de fichier CSV](active-directory-b2b-references-csv-file-format.md)
* [Modifications de l’attribut d’objet utilisateur externe](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [Limites actuelles de la version préliminaire](active-directory-b2b-current-preview-limitations.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0511_2016-->