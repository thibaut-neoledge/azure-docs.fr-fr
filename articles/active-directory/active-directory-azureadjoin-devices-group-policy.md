<properties
	pageTitle="Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10 | Microsoft Azure"
	description="Explique comment les administrateurs peuvent configurer une stratégie de groupe pour permettre à des appareils d’être joints à un domaine du réseau d’entreprise."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""
	tags="azure-classic-portal"/>

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article"

	ms.date="02/26/2016"

	ms.author="femila"/>

# Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10

La jonction de domaine est la méthode que les entreprises utilisent traditionnellement depuis une quinzaine d’années ou plus pour connecter leurs appareils professionnels. Elle permet aux utilisateurs de se connecter à leurs appareils avec leur compte professionnel ou scolaire Windows Server Active Directory et au service informatique de gérer intégralement ces appareils. En règle générale, les entreprises s'appuient sur des méthodes de création d'images pour mettre des appareils à la disposition des utilisateurs et utilisent System Center Configuration Manager (SCCM) ou une stratégie de groupe pour les gérer.

La jonction de domaine dans Windows 10 offrira les avantages suivants après la connexion des appareils à Azure Active Directory (Azure AD) :

- authentification unique (SSO) aux ressources Azure AD depuis n’importe où ;
- accès au Windows Store pour entreprises à l’aide de comptes professionnels ou scolaires (aucun compte Microsoft requis) ;
- itinérance des paramètres utilisateur compatible pour l’entreprise sur tous les appareils avec un compte professionnel ou scolaire (aucun compte Microsoft requis) ;
- authentification forte et connexion pratique pour les comptes professionnels ou scolaires avec Microsoft Passport et Windows Hello ;
- possibilité de restreindre l’accès aux appareils conformes aux paramètres de stratégie de groupe des appareils de l’organisation.

## Configuration requise

La jonction de domaine demeure utile. Toutefois, pour bénéficier des avantages d’Azure AD pour l’authentification unique, de l’itinérance des paramètres avec un compte professionnel ou scolaire et de l’accès au Windows Store avec un compte professionnel ou scolaire, vous aurez besoin des éléments suivants :

- Abonnement Azure AD
- Azure AD Connect pour étendre l’annuaire local à Azure AD
- Stratégie définie pour connecter des appareils joints au domaine à Azure AD
- Version de Windows 10 (version 10551 ou ultérieure) pour les appareils

Pour activer Microsoft Passport for Work et Windows Hello, vous aurez également besoin des éléments suivants :

- Infrastructure de clé publique (PKI) pour l’émission de certificats utilisateur.
- Version 1509 de System Center Configuration Manager pour l'évaluation technique. Pour plus d’informations, consultez [Évaluation technique de Microsoft System Center Configuration Manager](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) et [Blog de l’équipe System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx). C'est nécessaire pour déployer des certificats d'utilisateur en fonction des clés de Microsoft Passport.

En alternative à l'exigence de déploiement d'infrastructure à clé publique, vous pouvez procéder comme suit :

- Avoir quelques contrôleurs de domaine avec les services de domaine Active Directory Windows Server 2016.

Pour permettre un accès conditionnel, vous pouvez créer des paramètres de stratégie de groupe qui autorisent l’accès aux appareils joints au domaine sans déploiements supplémentaires. Pour gérer le contrôle d’accès basé sur la conformité de l’appareil, vous aurez besoin des éléments suivants :

- Version 1509 de System Center Configuration Manager pour l’évaluation technique de scénarios Passport

## Instructions de déploiement



### Étape 1 : déploiement d'Azure Active Directory Connect

Azure AD Connect vous permettra de configurer les ordinateurs locaux en tant qu’objets Appareil dans le cloud. Pour déployer Azure AD Connect, consultez « Installer Azure AD Connect » dans l’article [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect/#install-azure-ad-connect).

 - Si vous avez effectué une [installation personnalisée pour Azure AD Connect](active-directory-aadconnect-get-started-custom.md) (pas l’installation Express), suivez la procédure **Créer un point de connexion de service dans Active Directory local**, plus loin dans cette étape.
 - Si vous avez une configuration fédérée avec Azure AD avant l’installation d’Azure AD Connect (par exemple, si vous avez préalablement déployé des services de fédération Active Directory (AD FS)), suivez la procédure **Configurer les règles de revendication AD FS**, plus loin dans cette étape.

#### Créer un point de connexion de service dans Active Directory local

Les appareils joints au domaine utiliseront le point de connexion de service pour découvrir des informations de locataire Azure AD au moment de l’inscription automatique auprès du service d’inscription pour appareils Azure.

Sur le serveur Azure AD Connect, exécutez les commandes PowerShell suivantes :

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


Quand vous exécutez l’applet de commande $aadAdminCred = Get-Credential, utilisez le format **user@example.com* pour le nom d’utilisateur des informations d’identification entrées lorsque la fenêtre contextuelle Get-Credential s’affiche.

Quand vous exécutez l’applet de commande Initialize-ADSyncDomainJoinedComputerSync..., remplacez [*nom de compte de connecteur*] par le compte de domaine utilisé comme compte de connecteur Active Directory.

#### Configurer les règles de revendication AD FS
La configuration des règles de revendication AD FS permet l’inscription instantanée d’un ordinateur auprès du service d’inscription pour appareils Azure en permettant aux ordinateurs de procéder à l’authentification avec Kerberos/NTLM via AD FS. Sans cette étape, les ordinateurs atteignent Azure AD de manière différée (en fonction des heures de synchronisation d’Azure AD Connect).

>[AZURE.NOTE]
Si vous n’avez pas AD FS en tant que serveur de fédération local, suivez les instructions de votre fournisseur pour créer les règles de revendication.

Sur le serveur AD FS (ou sur une session connectée au serveur AD FS), exécutez les commandes PowerShell suivantes :

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed
     |   for DomainJoin++. The rules include:
     |   -ObjectGuid
     |   -AccountType
     |   -ObjectSid
     +---------------------------------------------------------------------#>

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $rule1 = '@RuleName = "Issue object GUID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain joined computers"
          c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

    $rule3 = '@RuleName = "Pass through primary SID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(claim = c2);'

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

>[AZURE.NOTE]
Les ordinateurs Windows 10 procèdent à l’authentification à l’aide de l’authentification intégrée de Windows vers un point de terminaison WS-Trust actif hébergé par AD FS. Vérifiez que ce point de terminaison est activé. Si vous utilisez le proxy d’authentification web, vérifiez également que ce point de terminaison est publié via le proxy. Pour ce faire, vérifiez que adfs/services/trust/13/windowstransport est affiché comme activé dans la console de gestion AD FS sous **Service** > **Points de terminaison**.


### Étape 2 : configuration de l'inscription automatique des appareils via la stratégie de groupe dans Active Directory

Vous pouvez utiliser une stratégie de groupe dans Active Directory pour configurer vos appareils Windows 10 joints au domaine de manière qu’ils s’inscrivent automatiquement auprès d’Azure AD. Pour cela, utilisez les instructions pas à pas suivantes :

1. 	Ouvrez le Gestionnaire de serveur et accédez à **Outils** > **Gestion des stratégies de groupe**.
2.	Dans Gestion des stratégies de groupe, accédez au nœud du domaine qui correspond au domaine où vous voulez activer Rejoindre Azure AD.
3.	Cliquez avec le bouton droit sur **Objets de stratégie de groupe**, puis sélectionnez **Nouveau**. Nommez votre objet de stratégie de groupe, par exemple Jonction automatique d'Azure AD. Cliquez sur **OK**.
4.	Cliquez avec le bouton droit sur votre nouvel objet de stratégie de groupe, puis sélectionnez **Modifier**.
5.	Accédez à **Configuration ordinateur** > **Stratégies** > **Modèles d'administration** > **Composants Windows** > **Jonction d'espace de travail**.
6.	Cliquez avec le bouton droit sur **Joindre automatiquement les ordinateurs clients à l’espace de travail**, puis sélectionnez **Modifier**.
7.	Sélectionnez la case d’option **Activé**, puis cliquez sur **Appliquer**. Cliquez sur **OK**.
8.	Liez l’objet de stratégie de groupe à un emplacement de votre choix. Pour activer cette stratégie pour tous les appareils Windows 10 joints au domaine de votre organisation, liez l’objet de stratégie de groupe au domaine. Par exemple :
 - Une unité d’organisation spécifique dans Active Directory où les ordinateurs Windows 10 joints au domaine seront situés
 - Un groupe de sécurité spécifique contenant des ordinateurs Windows 10 joints au domaine, qui sera inscrit automatiquement auprès d’Azure AD

>[AZURE.NOTE]
Ce modèle de stratégie de groupe a été renommé dans Windows 10. Si vous exécutez l’outil de stratégie de groupe à partir d’un ordinateur Windows 10, la stratégie s’affiche comme suit : <br> **Enregistrer les ordinateurs appartenant au domaine en tant qu’appareils**<br>. Elle se trouve à l’emplacement suivant :<br> ***Configuration ordinateur/Stratégies/Modèles d’administration/Composants Windows/Inscription d’appareil***


## Informations supplémentaires
* [Windows 10 pour l’entreprise : plusieurs manières d’utiliser des appareils professionnels](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [En savoir plus sur les scénarios d’utilisation pour Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0302_2016-->