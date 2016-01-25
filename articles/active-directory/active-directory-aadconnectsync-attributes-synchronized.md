<properties
	pageTitle="Azure Active Directory Connect Sync : attributs synchronisés avec Azure Active Directory | Microsoft Azure"
	description="Répertorie les attributs qui sont synchronisés avec Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016"
	ms.author="markusvi;andkjell"/>


# Azure Active Directory Connect Sync : attributs synchronisés avec Azure Active Directory

Cette rubrique répertorie les attributs synchronisés par Azure AD Connect Sync.<br> Les attributs sont regroupés selon l’application Azure AD associée.


## Office 365 ProPlus

| Nom de l’attribut| Utilisateur| Commentaire |
| --- | :-: | --- |
| accountEnabled| X| Détermine si un compte est activé.|
| cn| X| |
| displayName| X| |
| objectSID| X| propriété mécanique. Identificateur d’utilisateur Active Directory utilisé pour maintenir la synchronisation entre Azure AD et Active Directory.|
| pwdLastSet| X| propriété mécanique. Permet de savoir quand invalider les jetons déjà émis. Utilisé par la synchronisation de mot de passe et par la fédération.|
| sourceAnchor| X| propriété mécanique. Identificateur immuable pour maintenir la relation entre ADDS et Azure AD.|
| usageLocation| X| propriété mécanique. Pays de l’utilisateur. Utilisé pour l’attribution de licence.|
| userPrincipalName| X| Le nom UPN est l’ID de connexion de l’utilisateur. Le plus souvent, identique à la valeur [mail].|


## Exchange Online

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Détermine si un compte est activé.|
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
| info| X| X| X| Cet attribut n'est actuellement pas utilisé pour les groupes.|
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
| msExchExtensionCustomAttribute1| X| X| X| Cet attribut n'est actuellement pas utilisé par Exchange Online. |
| msExchExtensionCustomAttribute2| X| X| X| Cet attribut n'est actuellement pas utilisé par Exchange Online. |
| msExchExtensionCustomAttribute3| X| X| X| Cet attribut n'est actuellement pas utilisé par Exchange Online. |
| msExchExtensionCustomAttribute4| X| X| X| Cet attribut n'est actuellement pas utilisé par Exchange Online. |
| msExchExtensionCustomAttribute5| X| X| X| Cet attribut n'est actuellement pas utilisé par Exchange Online. |
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
| objectSID| X| | X| propriété mécanique. Identificateur d’utilisateur Active Directory utilisé pour maintenir la synchronisation entre Azure AD et Active Directory.|
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| proxyAddresses| X| X| X| |
| publicDelegates| X| X| X| |
| pwdLastSet| X| | | propriété mécanique. Permet de savoir quand invalider les jetons déjà émis. Utilisé par la synchronisation de mot de passe et par la fédération.|
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Dérivé de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| propriété mécanique. Identificateur immuable pour maintenir la relation entre ADDS et Azure AD.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailPhoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| usageLocation| X| | | propriété mécanique. Pays de l’utilisateur. Utilisé pour l’attribution de licence.|
| userCertificate| X| X| | |
| userPrincipalName| X| | | Le nom UPN est l’ID de connexion de l’utilisateur. Le plus souvent, identique à la valeur [mail].|
| userSMIMECertificates| X| X| | |
| wWWHomePage| X| X| | |



## SharePoint Online

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Détermine si un compte est activé.|
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
| objectSID| X| | X| propriété mécanique. Identificateur d’utilisateur Active Directory utilisé pour maintenir la synchronisation entre Azure AD et Active Directory.|
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
| pwdLastSet| X| | | propriété mécanique. Permet de savoir quand invalider les jetons déjà émis. Utilisé par la synchronisation de mot de passe et par la fédération.|
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Dérivé de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| propriété mécanique. Identificateur immuable pour maintenir la relation entre ADDS et Azure AD.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailPhoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| url| X| X| | |
| usageLocation| X| | | propriété mécanique. Pays de l’utilisateur. Utilisé pour l’attribution de licence.|
| userPrincipalName| X| | | Le nom UPN est l’ID de connexion de l’utilisateur. Le plus souvent, identique à la valeur [mail].|
| wWWHomePage| X| X| | |

## Lync Online

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Détermine si un compte est activé.|
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
| objectSID| X| | X| propriété mécanique. Identificateur d’utilisateur Active Directory utilisé pour maintenir la synchronisation entre Azure AD et Active Directory.|
| otherTelephone| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | propriété mécanique. Permet de savoir quand invalider les jetons déjà émis. Utilisé par la synchronisation de mot de passe et par la fédération.|
| securityEnabled| | | X| Dérivé de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| propriété mécanique. Identificateur immuable pour maintenir la relation entre ADDS et Azure AD.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailPhoto| X| X| | |
| title| X| X| | |
| usageLocation| X| | | propriété mécanique. Pays de l’utilisateur. Utilisé pour l’attribution de licence.|
| userPrincipalName| X| | | Le nom UPN est l’ID de connexion de l’utilisateur. Le plus souvent, identique à la valeur [mail].|
| wWWHomePage| X| X| | |


## Azure RMS

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
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

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Détermine si un compte est activé.|
| c| X| X| | |
| cn| X| | X| |
| description| X| X| X| |
| displayName| X| X| X| |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| member| | | X| |
| objectSID| X| | X| propriété mécanique. Identificateur d’utilisateur Active Directory utilisé pour maintenir la synchronisation entre Azure AD et Active Directory.|
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | propriété mécanique. Permet de savoir quand invalider les jetons déjà émis. Utilisé par la synchronisation de mot de passe et par la fédération.|
| securityEnabled| | | X| Dérivé de groupType|
| sourceAnchor| X| X| X| propriété mécanique. Identificateur immuable pour maintenir la relation entre ADDS et Azure AD.|
| usageLocation| X| | | propriété mécanique. Pays de l’utilisateur. Utilisé pour l’attribution de licence.|
| userPrincipalName| X| | | Le nom UPN est l’ID de connexion de l’utilisateur. Le plus souvent, identique à la valeur [mail].|



## Dynamics CRM

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Détermine si un compte est activé.|
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
| objectSID| X| | X| propriété mécanique. Identificateur d’utilisateur Active Directory utilisé pour maintenir la synchronisation entre Azure AD et Active Directory.|
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| pwdLastSet| X| | | propriété mécanique. Permet de savoir quand invalider les jetons déjà émis. Utilisé par la synchronisation de mot de passe et par la fédération.|
| securityEnabled| | | X| Dérivé de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| propriété mécanique. Identificateur immuable pour maintenir la relation entre ADDS et Azure AD.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| title| X| X| | |
| usageLocation| X| | | propriété mécanique. Pays de l’utilisateur. Utilisé pour l’attribution de licence.|
| userPrincipalName| X| | | Le nom UPN est l’ID de connexion de l’utilisateur. Le plus souvent, identique à la valeur [mail].|

## Applications tierces
Il s’agit d’un ensemble d’attributs qui peut être utilisé si l’annuaire Azure AD n’est pas utilisé pour prendre en charge Office 365, Dynamics ou Intune. Il comporte un petit ensemble d’attributs de base.

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Détermine si un compte est activé.|
| cn| X| | X| |
| displayName| X| X| X| |
| givenName| X| X| | |
| mail| X| | X| |
| managedBy| | | X| |
| mailNickName| X| X| X| |
| member| | | X| |
| objectSID| X| | | propriété mécanique. Identificateur d’utilisateur Active Directory utilisé pour maintenir la synchronisation entre Azure AD et Active Directory.|
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | propriété mécanique. Permet de savoir quand invalider les jetons déjà émis. Utilisé par la synchronisation de mot de passe et par la fédération.|
| sn| X| X| | |
| sourceAnchor| X| X| X| propriété mécanique. Identificateur immuable pour maintenir la relation entre ADDS et Azure AD.|
| usageLocation| X| | | propriété mécanique. Pays de l’utilisateur. Utilisé pour l’attribution de licence.|
| userPrincipalName| X| | | Le nom UPN est l’ID de connexion de l’utilisateur. Le plus souvent, identique à la valeur [mail].|

## Windows 10
Les ordinateurs (appareils devices) du domaine Windows 10 synchroniseront certains attributs sur Azure AD. Pour plus d'informations sur les scénarios, consultez [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md). Ces attributs sont toujours synchronisés et Windows 10 n'apparaît pas comme une application que vous pouvez désélectionner. Un ordinateur appartenant au domaine Windows 10 est identifié par l'attribut userCertificate.

| Nom de l'attribut| Appareil| Commentaire |
| --- | :-: | --- |
| accountEnabled| X| |
| deviceTrustType| X| Valeur codée en dur pour les ordinateurs appartenant au domaine. |
| displayName | X| |
| ms-DS-CreatorSID | X| Également appelé registeredOwnerReference.|
| objectGUID | X| Également appelé deviceID.|
| objectSID | X| Également appelé onPremisesSecurityIdentifier.|
| operatingSystem | X| Également appelé deviceOSType.|
| operatingSystemVersion | X| Également appelé deviceOSVersion.|
| userCertificate | X| |

Ces attributs pour l'utilisateur s'ajoutent aux autres applications que vous avez sélectionnées.

| Nom de l'attribut| Utilisateur| Commentaire |
| --- | :-: | --- |
| domainFQDN| X| Également appelé dnsDomainName. Par exemple, contoso.com.|
| domainNetBios| X| Également appelé netBiosName. Par exemple, CONTOSO.|

## Écriture différée d’Exchange hybride
Ces attributs sont écrits en différé depuis Azure AD vers Active Directory local quand vous choisissez d’activer Exchange hybride. Selon votre version d’Exchange, il est possible que moins d’attributs soient synchronisés.

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| msDS-ExternalDirectoryObjectID| X| | | Dérivé de cloudAnchor dans Azure AD. Nouveauté d’Exchange 2016.|
| msExchArchiveStatus| X| | | Archive en ligne : permet aux clients d’archiver le courrier.|
| msExchBlockedSendersHash| X| | | Filtrage : écrit en différé le filtrage local, les données sécurisées en ligne et les données des expéditeurs bloqués provenant des clients.|
| msExchSafeRecipientsHash| X| | | Filtrage : écrit en différé le filtrage local, les données sécurisées en ligne et les données des expéditeurs bloqués provenant des clients.|
| msExchSafeSenderHash| X| | | Filtrage : écrit en différé le filtrage local, les données sécurisées en ligne et les données des expéditeurs bloqués provenant des clients.|
| msExchUCVoiceMailSettings| X| | | Activer la messagerie unifiée (MU) - messagerie vocale en ligne : utilisée par l’intégration de Microsoft Lync Server pour indiquer à Lync Server local que l’utilisateur dispose de la messagerie vocale dans les services en ligne.|
| msExchUserHoldPolicies| X| | | Conservation pour litige : permet aux services cloud de déterminer quels utilisateurs sont sous conservation pour litige.|
| proxyAddresses| X| X| X| Seule l’adresse x500 d’Exchange Online est insérée.|

## Remarques sur les attributs
- Quand vous utilisez un ID secondaire, l’attribut local userPrincipalName est synchronisé avec l’attribut Azure AD onPremisesUserPrincipalName. L’attribut de l’ID secondaire, par exemple mail, sera synchronisé avec l’attribut Azure AD userPrincipalName.


## Étapes suivantes
En savoir plus sur la configuration de la [synchronisation Azure AD Connect](active-directory-aadconnectsync-whatis.md).

En savoir plus sur l'[intégration de vos identités locales dans Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0114_2016-->