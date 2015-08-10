<properties
	pageTitle="Azure Active Directory Connect Sync : attributs synchronisés avec Azure Active Directory"
	description="Répertorie les attributs qui sont synchronisés avec Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure Active Directory Connect Sync : attributs synchronisés avec Azure Active Directory

Cette rubrique répertorie les attributs synchronisés par Azure AD Connect Sync.<br> Les attributs sont regroupés par les applications Azure AD.
 




## Office 365 ProPlus

| Nom de l'attribut| Utilisateur| Commentaire |
| --- | :-: | --- |
| accountEnabled| X| Dérivé de userAccountControl|
| cn| X| |
| displayName| X| |
| objectSID| X| |
| pwdLastSet| X| |
| sourceAnchor| X| L’attribut utilisé pour les utilisateurs est configuré dans le guide d’installation.|
| usageLocation| X| msExchUsageLocation dans AD DS|
| userPrincipalName| X| |


## Exchange Online

| Nom de l'attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Dérivé de userAccountControl|
| assistant| X| X| | |
| authOrig| X| X| X| |
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| société| X| X| | |
| countryCode| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| dLMemRejectPerms| X| X| X| |
| dLMemSubmitPerms| X| X| X| |
| extensionAttribute1| X| X| X| |
| extensionAttribute10| X| X| X| |
| extensionAttribute11| X| X| X| |
| extensionAttribute12| X| X| X| |
| extensionAttribute13| X| X| X| |
| extensionAttribute14| X| X| X| |
| extensionAttribute15| X| X| X| |
| extensionAttribute2| X| X| X| |
| extensionAttribute3| X| X| X| |
| extensionAttribute4| X| X| X| |
| extensionAttribute5| X| X| X| |
| extensionAttribute6| X| X| X| |
| extensionAttribute7| X| X| X| |
| extensionAttribute8| X| X| X| |
| extensionAttribute9| X| X| X| |
| facsimileTelephoneNumber| X| X| | |
| givenName| X| X| | |
| homePhone| X| X| | |
| info| X| X| X| |
| Initials| X| X| | |
| l| X| X| | |
| legacyExchangeDN| X| X| X| |
| mailNickName| X| X| X| |
| mangedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| msDS-HABSeniorityIndex| X| X| X| |
| msDS-PhoneticDisplayName| X| X| X| |
| msExchArchiveGUID| X| | | |
| msExchArchiveName| X| | | |
| msExchAssistantName| X| X| | |
| msExchAuditAdmin| X| | | |
| msExchAuditDelegate| X| | | |
| msExchAuditDelegateAdmin| X| | | |
| msExchAuditOwner| X| | | |
| msExchBlockedSendersHash| X| X| | |
| msExchBypassAudit| X| | | |
| msExchCoManagedByLink| | | X| |
| msExchDelegateListLink| X| | | |
| msExchELCExpirySuspensionEnd| X| | | |
| msExchELCExpirySuspensionStart| X| | | |
| msExchELCMailboxFlags| X| | | |
| msExchEnableModeration| X| | X| |
| msExchExtensionCustomAttribute1| X| X| X| |
| msExchExtensionCustomAttribute2| X| X| X| |
| msExchExtensionCustomAttribute3| X| X| X| |
| msExchExtensionCustomAttribute4| X| X| X| |
| msExchExtensionCustomAttribute5| X| X| X| |
| msExchHideFromAddressLists| X| X| X| |
| msExchImmutableID| X| | | |
| msExchLitigationHoldDate| X| X| X| |
| msExchLitigationHoldOwner| X| X| X| |
| msExchMailboxAuditEnable| X| | | |
| msExchMailboxAuditLogAgeLimit| X| | | |
| msExchMailboxGuid| X| | | |
| msExchModeratedByLink| X| X| X| |
| msExchModerationFlags| X| X| X| |
| msExchRecipientDisplayType| X| X| X| |
| msExchRecipientTypeDetails| X| X| X| |
| msExchRemoteRecipientType| X| | | |
| msExchRequireAuthToSendTo| X| X| X| |
| msExchResourceCapacity| X| | | |
| msExchResourceDisplay| X| | | |
| msExchResourceMetaData| X| | | |
| msExchResourceSearchProperties| X| | | |
| msExchRetentionComment| X| X| X| |
| msExchRetentionURL| X| X| X| |
| msExchSafeRecipientsHash| X| X| | |
| msExchSafeSenderHash| X| X| | |
| msExchSenderHintTranslations| X| X| X| |
| msExchTeamMailboxExpiration| X| | | |
| msExchTeamMailboxOwners| X| | | |
| msExchTeamMailboxSharePointUrl| X| | | |
| msExchUserHoldPolicies| X| | | |
| msOrg-IsOrganizational| | | X| |
| objectSID| X| | X| |
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| proxyAddresses| X| X| X| |
| publicDelegates| X| X| X| |
| pwdLastSet| X| | | |
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Dérivé de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| L’attribut utilisé pour les utilisateurs est configuré dans le guide d’installation.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailPhoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| usageLocation| X| | | msExchUsageLocation dans AD DS|
| userCertificate| X| X| | |
| userPrincipalName| X| | | |
| userSMIMECertificates| X| X| | |
| wWWHomePage| X| X| | |


## SharePoint Online

| Nom de l'attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Dérivé de userAccountControl|
| authOrig| X| X| X| |
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| société| X| X| | |
| countryCode| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| dLMemRejectPerms| X| X| X| |
| dLMemSubmitPerms| X| X| X| |
| extensionAttribute1| X| X| X| |
| extensionAttribute10| X| X| X| |
| extensionAttribute11| X| X| X| |
| extensionAttribute12| X| X| X| |
| extensionAttribute13| X| X| X| |
| extensionAttribute14| X| X| X| |
| extensionAttribute15| X| X| X| |
| extensionAttribute2| X| X| X| |
| extensionAttribute3| X| X| X| |
| extensionAttribute4| X| X| X| |
| extensionAttribute5| X| X| X| |
| extensionAttribute6| X| X| X| |
| extensionAttribute7| X| X| X| |
| extensionAttribute8| X| X| X| |
| extensionAttribute9| X| X| X| |
| facsimileTelephoneNumber| X| X| | |
| givenName| X| X| | |
| hideDLMembership| | | X| |
| homephone| X| X| | |
| info| X| X| X| |
| initials| X| X| | |
| ipPhone| X| X| | |
| l| X| X| | |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| middleName| X| X| | |
| mobile| X| X| | |
| msExchTeamMailboxExpiration| X| | | |
| msExchTeamMailboxOwners| X| | | |
| msExchTeamMailboxSharePointLinkedBy| X| | | |
| msExchTeamMailboxSharePointUrl| X| | | |
| objectSID| X| | X| |
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherIpPhone| X| X| | |
| otherMobile| X| X| | |
| otherPager| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| postOfficeBox| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | |
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Dérivé de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| L’attribut utilisé pour les utilisateurs est configuré dans le guide d’installation.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailPhoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| url| X| X| | |
| usageLocation| X| | | msExchUsageLocation dans AD DS|
| userPrincipalName| X| | | |
| wWWHomePage| X| X| | |

## Lync Online

| Nom de l'attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Dérivé de userAccountControl|
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| société| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| facsimileTelephoneNumber| X| X| X| |
| givenName| X| X| | |
| homephone| X| X| | |
| ipPhone| X| X| | |
| l| X| X| | |
| mail| X| X| X| |
| mailNickName| X| X| X| |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| msExchHideFromAddressLists| X| X| X| |
| msRTCSIP-ApplicationOptions| X| | | |
| msRTCSIP-DeploymentLocator| X| X| | |
| msRTCSIP-Line| X| X| | |
| msRTCSIP-OptionFlags| X| X| | |
| msRTCSIP-OwnerUrn| X| | | |
| msRTCSIP-PrimaryUserAddress| X| X| | |
| msRTCSIP-UserEnabled| X| X| | |
| objectSID| X| | X| |
| otherTelephone| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | |
| securityEnabled| | | X| Dérivé de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| L’attribut utilisé pour les utilisateurs est configuré dans le guide d’installation.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailPhoto| X| X| | |
| title| X| X| | |
| usageLocation| X| | | msExchUsageLocation dans AD DS|
| userPrincipalName| X| | | |
| wWWHomePage| X| X| | |


## Azure RMS

| Nom de l'attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Détermine si un compte est activé.|
| cn| X| | X| Nom commun ou alias. Le plus souvent, il s’agit du préfixe de valeur [mail].|
| displayName| X| X| X| Chaîne qui représente le nom affiché souvent comme nom convivial (prénom nom).|
| mail| X| X| X| Adresse de messagerie complète.|
| member| | | X| |
| objectSID| X| | X| propriété mécanique. Identificateur d’utilisateur Active Directory utilisé pour maintenir la synchronisation entre Azure AD et Active Directory.|
| proxyAddresses| X| X| X| propriété mécanique. Utilisé par Azure AD. Contient toutes les adresses de messagerie secondaires pour l’utilisateur.|
| pwdLastSet| X| | | propriété mécanique. Permet de savoir quand invalider les jetons déjà émis.|
| securityEnabled| | | X| Dérivé de groupType.|
| sourceAnchor| X| X| X| propriété mécanique. Identificateur immuable pour maintenir la relation entre ADDS et Azure AD.|
| usageLocation| X| | | propriété mécanique. Pays de l’utilisateur. Utilisé pour l’attribution de licence.|
| userPrincipalName| X| | | Ce nom UPN est l’ID de connexion de l’utilisateur. Le plus souvent, identique à la valeur [mail].|


## Intune

| Nom de l'attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Dérivé de userAccountControl|
| c| X| X| | |
| cn| X| | X| |
| description| X| X| X| |
| displayName| X| X| X| |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| member| | | X| |
| objectSID| X| | X| |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | |
| securityEnabled| | | X| Dérivé de groupType|
| sourceAnchor| X| X| X| L’attribut utilisé pour les utilisateurs est configuré dans le guide d’installation.|
| usageLocation| X| | | msExchUsageLocation dans AD DS|
| userPrincipalName| X| | | |


## Dynamics CRM

| Nom de l'attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Dérivé de userAccountControl|
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| société| X| X| | |
| countryCode| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| facsimileTelephoneNumber| X| X| | |
| givenName| X| X| | |
| l| X| X| | |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| objectSID| X| | X| |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| pwdLastSet| X| | | |
| securityEnabled| | | X| Dérivé de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| L’attribut utilisé pour les utilisateurs est configuré dans le guide d’installation.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| title| X| X| | |
| usageLocation| X| | | msExchUsageLocation dans AD DS|
| userPrincipalName| X| | | |


## Ressources supplémentaires

* [Azure AD Connect Sync : personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
 
<!--Image references-->

<!---HONumber=July15_HO5-->