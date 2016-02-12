<properties
   pageTitle="Format de fichier CSV pour l'aperçu de la collaboration B2B d'Azure Active Directory | Microsoft Azure"
   description="Azure Active Directory B2B prend en charge les relations interentreprises en permettant aux partenaires commerciaux d'accéder de façon sélective à vos applications d'entreprise"
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
   ms.workload="identity"
   ms.date="02/03/2016"
   ms.author="viviali"/>

# Version préliminaire d’Azure AD B2B Collaboration : Format de fichier CSV

La version préliminaire de la collaboration B2B d'Azure AD nécessite un fichier CSV spécifiant les informations de l'utilisateur partenaire à télécharger par le biais du portail Azure AD. Le fichier CSV doit contenir les étiquettes requises ci-dessous et les champs facultatifs, en fonction des besoins. Modifiez l'exemple de fichier CSV (ci-dessous) sans modifier l'orthographe des étiquettes dans la première ligne et sans réorganiser les colonnes.

>[AZURE.NOTE] La première ligne d'étiquettes (par exemple, Email, DisplayName...) est nécessaire pour que le fichier CSV puisse être analysé correctement. L'orthographe doit correspondre aux champs spécifiés ci-dessous. Ces étiquettes identifient le contenu en dessous. Pour les champs facultatifs qui ne sont pas nécessaires, les étiquettes peuvent être supprimées du fichier CSV. La colonne entière peut être vide.

## Champs obligatoires : <br/>
**Email :** adresse e-mail de l'utilisateur invité. <br/> **DisplayName :** nom d'affichage de l'utilisateur invité (en général, prénom et nom).<br/> **InviteContactUsUrl :** URL à inclure dans les invitations par e-mail au cas où l'utilisateur invité souhaiterait contacter votre organisation.<br/>

## Champs facultatifs : <br/>
**InviteAppID :** ID de l'application à utiliser pour personnaliser l'invitation par e-mail et les pages d'acceptation.<br/> **InviteAppResources :** ID d'application des applications d'entreprise pour affecter des utilisateurs. Les ID d'application sont récupérables dans PowerShell en appelant `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/> **InviteGroupResources :** ID d'objet pour les groupes auxquels ajouter des utilisateurs. Les ID d'objet sont récupérables dans PowerShell en appelant `Get-MsolGroup | fl DisplayName, ObjectId`<br/> **InviteReplyURL :** URL pour diriger un utilisateur invité après acceptation de l'invitation. Il doit s'agir d'une URL spécifique à la société (par exemple, [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)). Si ce champ facultatif n'est pas spécifié, l'utilisateur invité est dirigé vers le volet d'accès à l'application où il peut naviguer jusqu'aux applications d'entreprise souhaitées. L'URL du panneau d'accès à l'application a la forme `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`.<br/> **Langue :** langue de l'e-mail d'invitation et de l'expérience d'échange, en anglais par défaut lorsque cela n'est pas spécifié. Les 10 autres codes de langue pris en charge sont :<br/> 1. de : allemand<br/> 2. es : espagnol<br/> 3. fr : français<br/> 4. it : italien<br/> 5. ja : japonais<br/> 6. ko : coréen<br/> 7. pt-BR : portugais (Brésil)<br/> 8. ru : russe<br/> 9. zh-HANS : chinois simplifié<br/> 10. zh-HANT : chinois traditionnel<br/>

## Exemple de fichier CSV
Voici un exemple de fichier CSV que vous pouvez modifier.

>[AZURE.NOTE] Copiez et collez-le dans le bloc-notes et enregistrez-le avec une extension de fichier « .csv ». Puis modifiez-le dans Excel. Il sera structuré sous forme de table avec des étiquettes dans la première ligne.

>[AZURE.NOTE] Ajoutez des champs facultatifs dans Excel en spécifiant l'étiquette et en remplissant la colonne en dessous.

```
Email,DisplayName,InviteAppID,InviteReplyUrl,InviteAppResources,InviteGroupResources,InviteContactUsUrl
wharp@contoso.com,Walter Harp,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
jsmith@contoso.com,Jeff Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
bsmith@contoso.com,Ben Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
```

## Articles connexes
Consultez les autres articles sur Azure B2B Collaboration

- [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Fonctionnement](active-directory-b2b-how-it-works.md)
- [Procédure détaillée](active-directory-b2b-detailed-walkthrough.md)
- [Format du jeton utilisateur externe](active-directory-b2b-references-external-user-token-format.md)
- [Modifications de l’attribut d’objet utilisateur externe](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limites actuelles de l'aperçu](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=AcomDC_0204_2016-->