<properties
	pageTitle="Inscription automatique d’appareils auprès d’Azure Active Directory pour les appareils joints à un domaine Windows| Microsoft Azure"
	description="Les administrateurs peuvent choisir de faire inscrire automatiquement et en mode silencieux les appareils joints à un domaine Windows auprès d’Azure Active Directory (Azure AD)."
	services="active-directory"
	documentationCenter=""
	authors="Markvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="femila"/>

# Inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine

En tant qu’administrateur, vous pouvez choisir d’inscrire automatiquement et en mode silencieux des appareils joints à votre domaine Windows auprès d’Azure Active Directory (Azure AD). Ceci peut être utile si vous avez configuré des stratégies d’accès conditionnel basées sur les appareils à des applications Office 365 ou à des applications gérées localement par AD FS. Vous pouvez en savoir plus sur les scénarios d’inscription d’appareils en lisant la [Présentation de l’inscription d’appareils auprès d’Azure Active Directory](active-directory-conditional-access-device-registration-overview.md).

>AZURE.NOTE Pour les dernières informations sur la configuration de l’inscription automatique des appareils, consultez [Configuration de l’inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine](active-directory-conditional-access-automatic-device-registration-setup.md).

L’inscription automatique d’appareils auprès d’Azure Active Directory est disponible pour les ordinateurs Windows 7 et Windows 8.1 qui ont été joints à un domaine Active Directory. Il s’agit en général d’appareils détenus par des entreprises et qui ont été fournis à des travailleurs de l’information.

Pour commencer l’inscription des appareils joints à votre domaine Windows auprès d’Azure AD, assurez-vous de respecter les conditions requises ci-dessous. Quand vous avez rempli les conditions requises, configurez l’inscription automatique des appareils pour les appareils joints à votre domaine Windows.

## Conditions requises pour l’inscription automatique d’appareils Windows joints à un domaine auprès d’Azure Active Directory

Déployez AD FS et connectez-vous à Azure Active Directory à l’aide d’Azure Active Directory Connect.
----------------------------------------------------------------------------------------------
1. Utilisez Azure Active Directory Connect pour déployer Active Directory Federation Services (AD FS) avec Windows Server 2012 R2 et configurez une relation de fédération avec Azure Active Directory.
2. Configurez une règle de revendication d’approbation de partie de confiance Azure Active Directory supplémentaire.
3. Ouvrez la console de gestion d’AD FS et accédez à **AD FS**>**Relations d’approbation > Approbations de partie de confiance **. Cliquez avec le bouton droit sur l’objet d’approbation de partie de confiance de la plateforme d’identité Microsoft Office 365 et sélectionnez **Modifier les règles de revendication...**
4. Sous l’onglet **Règles de transformation d’émission**, sélectionnez **Ajouter une règle**.
5. Sélectionnez **Envoyer les revendications en utilisant une règle personnalisée** dans la zone de liste déroulante **Règle de revendication**. Sélectionnez **Suivant**.
6. Tapez *Règle de revendication de méthode d’authentification* dans la zone de texte **Nom de la règle de revendication :**.
7. Tapez la règle de revendication suivante dans la zone de texte **Règle de revendication** :

        c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
        => issue(claim = c);

8. Cliquez sur **OK** à deux reprises pour fermer la boîte de dialogue.

Configurez une classe de référence d’authentification d’approbation de partie de confiance Azure Active Directory supplémentaire.
-----------------------------------------------------------------------------------------------------
Sur votre serveur de fédération, ouvrez une fenêtre de commande Windows PowerShell et tapez :


  `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

Où <RPObjectName> est le nom de l’objet de partie de confiance de votre objet d’approbation de partie de confiance Azure Active Directory. Cet objet est généralement nommé plateforme d’identité Microsoft Office 365.

Stratégie d’authentification globale d’AD FS
-----------------------------------------------------------------------------
Configurez la stratégie d’authentification principale globale d’AD FS pour permettre l’authentification Windows intégrée pour l’intranet (c’est la valeur par défaut).


Configuration d’Internet Explorer
------------------------------------------------------------------------------
Configurez les paramètres suivants sur Internet Explorer sur vos appareils Windows pour la zone de sécurité Intranet local :

- Ne pas demander la sélection d'un certificat client lorsqu'il n'existe qu'un seul certificat : **Activer**
- Autoriser les scripts : **Activer**
- Connexion automatique uniquement dans la zone Intranet : **Activé**

Ce sont les paramètres par défaut pour la zone de sécurité intranet local d’Internet Explorer. Vous pouvez afficher ou gérer ces paramètres dans Internet Explorer en accédant à **Options Internet** > **Sécurité** > Intranet Local > Personnaliser le niveau. Vous pouvez également configurer ces paramètres à l’aide de la stratégie de groupe Active Directory.

Connectivité réseau
-------------------------------------------------------------
Les appareils joints à un domaine Windows doivent être connectés à AD FS et à un contrôleur de domaine Active Directory pour s’inscrire automatiquement auprès d’Azure AD. Cela signifie généralement que l’ordinateur doit être connecté au réseau d’entreprise. Ceci peut inclure une connexion câblée, une connexion Wi-Fi, DirectAccess ou VPN.

## Configurer la détection du service Azure Active Directory Device Registration
Les appareils Windows 7 et Windows 8.1 détecteront le serveur d’inscription d’appareils en associant le nom de compte de l’utilisateur à un nom de serveur d’inscription d’appareils connu. Vous devez créer un enregistrement DNS CNAME qui pointe vers l’enregistrement A associé à votre service Azure Active Directory Device Registration. L'enregistrement CNAME doit utiliser le préfixe **enterpriseregistration** connu suivi du suffixe UPN utilisé par les comptes d'utilisateurs au sein de votre organisation. Si votre organisation utilise plusieurs suffixes UPN, plusieurs enregistrements CNAME doivent être créés dans le DNS.

Par exemple, si vous utilisez deux suffixes UPN dans votre organisation nommés @contoso.com et @region.contoso.com, vous devez créer les enregistrements DNS suivants :

| Entrée | Type | Adresse |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com | CNAME | enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com | CNAME | enterpriseregistration.windows.net |

##Configurer l’inscription automatique des appareils pour les appareils joints à des domaines Windows 7 et Windows 8.1

Configurez l’inscription automatique des appareils pour les appareils joints à vos domaines Windows 7 et Windows 8.1 en utilisant les liens suivants. Assurez-vous d’avoir satisfait aux conditions requises avant de continuer.

* [Configurer l’inscription automatique des appareils pour les appareils joints à un domaine Windows 8.1](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)

* [Configurer l’inscription automatique des appareils pour les appareils joints à un domaine Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md)

* [Inscription automatique auprès d’Azure Active Directory d’appareils Windows 10 joints à un domaine](active-directory-azureadjoin-devices-group-policy.md)

Remarques supplémentaires
--------------------------------------------------------------------

L’inscription des appareils auprès d’Azure AD fournit l’ensemble le plus étendu de fonctionnalités des appareils. Avec l’inscription d’appareils Azure AD, vous pouvez inscrire les appareils mobiles personnels (BYOD) et les appareils d’entreprise joints à un domaine. Les appareils peuvent être utilisés avec des services hébergés, comme Office 365, et avec des services gérés localement avec AD FS.

Les entreprises qui utilisent des appareils mobiles et des appareils traditionnels, ou qui utilisent Office 365, Azure AD ou d’autres services Microsoft, doivent inscrire les appareils dans Azure AD en utilisant le service d’inscription d’appareils Azure AD. Si votre entreprise n’utilise pas les appareils mobiles et n’utilise aucun service Microsoft, comme Office 365, Azure AD ou Microsoft Intune, mais qui au lieu de cela héberge seulement des applications locales, vous pouvez choisir d’inscrire les appareils dans Active Directory en utilisant AD FS.

Pour en savoir plus sur le déploiement de l'inscription d'appareils avec AD FS, cliquez [ici](https://technet.microsoft.com/library/dn486831.aspx).

## Rubriques supplémentaires

- [Vue d’ensemble du service Azure Active Directory Device Registration](active-directory-conditional-access-device-registration-overview.md)
- [Configurer l’inscription automatique des appareils pour les appareils joints à un domaine Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md)
- [Configurer l’inscription automatique des appareils pour les appareils joints à un domaine Windows 8.1.](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [Inscription automatique auprès d’Azure Active Directory d’appareils Windows 10 joints à un domaine](active-directory-azureadjoin-devices-group-policy.md)

<!---HONumber=AcomDC_0928_2016-->