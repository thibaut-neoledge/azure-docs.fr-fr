---
title: "Format de fichier CSV pour l’aperçu de la collaboration B2B d’Azure Active Directory | Microsoft Docs"
description: "Azure Active Directory B2B prend en charge les relations interentreprises en permettant aux partenaires commerciaux d’accéder de façon sélective à vos applications d’entreprise"
services: active-directory
documentationcenter: 
author: viv-liu
manager: cliffdi
editor: 
tags: 
ms.assetid: bd2c5364-4164-407d-ac25-34088c175c4a
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/09/2016
ms.author: viviali
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f972fa0f8322ee33e1d198738f35d800564b4ddf


---
# <a name="azure-ad-b2b-collaboration-preview-csv-file-format"></a>Version préliminaire d’Azure AD B2B Collaboration : Format de fichier CSV
La version préliminaire de la collaboration B2B d'Azure AD nécessite un fichier CSV spécifiant les informations de l'utilisateur partenaire à télécharger par le biais du portail Azure AD. Le fichier CSV doit contenir les étiquettes requises ci-dessous et les champs facultatifs, en fonction des besoins. Modifiez l'exemple de fichier CSV (ci-dessous) sans modifier l'orthographe des étiquettes dans la première ligne.

> [!NOTE]
> La première ligne d'étiquettes (par exemple, Email, DisplayName, etc.) est nécessaire pour que le fichier CSV puisse être analysé correctement. L'orthographe doit correspondre aux champs spécifiés ci-dessous. Ces étiquettes identifient le contenu en dessous. Pour les champs facultatifs qui ne sont pas nécessaires, les étiquettes peuvent être supprimées du fichier CSV. La colonne entière peut être vide.
> 
> 

## <a name="required-fields-br"></a>Champs obligatoires :  <br/>
**Email :** adresse e-mail de l'utilisateur invité. <br/>
**DisplayName :** nom d’affichage de l’utilisateur invité (en général, prénom et nom).<br/>

## <a name="optional-fields-br"></a>Champs facultatifs :  <br/>
**InvitationText :** personnaliser le texte du courrier électronique d’invitation après la personnalisation de l’application et avant le lien d’échange.<br/>
**InvitedToApplications :** ID d’application des applications d’entreprise pour affecter des utilisateurs. Les ID d'application sont récupérables dans PowerShell en appelant `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/>
**InvitedToGroups :** ID d’objet pour les groupes auxquels ajouter des utilisateurs. Les ID d'objet sont récupérables dans PowerShell en appelant `Get-MsolGroup | fl DisplayName, ObjectId`<br/>
**InviteRedirectURL :** URL pour diriger un utilisateur invité après acceptation de l’invitation. Il doit s’agir d’une URL spécifique à la société (par exemple, [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)). Si ce champ facultatif n'est pas spécifié, l'utilisateur invité est dirigé vers le volet d'accès à l'application où il peut naviguer jusqu'aux applications d'entreprise souhaitées. L’URL du panneau d’accès à l’application a la forme `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`.<br/>
**CcEmailAddress** : adresse e-mail de copie de l’invitation envoyée par e-mail. Si le champ CcEmailAddress est utilisé, cette invitation ne peut pas servir à créer un utilisateur ou un locataire vérifié par e-mail.<br/>
**Langue :** langue de l’e-mail d’invitation et de l’expérience d’échange, « en » (anglais) par défaut quand cela n’est pas spécifié. Les codes des 10 autres langues prises en charge sont les suivants :<br/>

1. de : allemand<br/>
2. es : espagnol<br/>
3. fr : français<br/>
4. it : italien<br/>
5. ja : japonais<br/>
6. ko : coréen<br/>
7. pt-BR : portugais (Brésil)<br/>
8. ru : russe<br/>
9. zh-HANS : chinois simplifié<br/>
10. zh-HANT : chinois traditionnel<br/>

## <a name="sample-csv-file"></a>Exemple de fichier CSV
Voici un exemple de fichier CSV que vous pouvez modifier.

> [!NOTE]
> Copiez et collez-le dans le bloc-notes et enregistrez-le avec une extension de fichier « .csv ». Puis modifiez-le dans Excel. Il sera structuré sous forme de table avec des étiquettes dans la première ligne.
> 
> Ajoutez des champs facultatifs dans Excel en spécifiant l'étiquette et en remplissant la colonne en dessous.
> 
> 

```
Email,DisplayName,InvitationText,InviteRedirectUrl,InvitedToApplications,InvitedToGroups,CcEmailAddress,Language
wharp@contoso.com,Walter Harp,Hi Walter! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
jsmith@contoso.com,Jeff Smith,Hi Jeff! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
bsmith@contoso.com,Ben Smith,Hi Ben! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en

```

## <a name="related-articles"></a>Articles connexes
Consultez les autres articles sur la collaboration B2B d’Azure AD :

* [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Fonctionnement](active-directory-b2b-how-it-works.md)
* [Procédure détaillée](active-directory-b2b-detailed-walkthrough.md)
* [Format du jeton utilisateur externe](active-directory-b2b-references-external-user-token-format.md)
* [Modifications de l’attribut d’objet utilisateur externe](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [Limites actuelles de la version préliminaire](active-directory-b2b-current-preview-limitations.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)




<!--HONumber=Dec16_HO4-->


