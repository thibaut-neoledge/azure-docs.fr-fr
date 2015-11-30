<properties 
	pageTitle="Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10 | Microsoft Azure" 
	description="Une rubrique qui explique comment les administrateurs peuvent configurer des stratégies de groupe pour activer les appareils à joindre au domaine du réseau d'entreprise." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="femila"/>

# Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10

La jonction de domaine est la méthode que les entreprises utilisent traditionnellement depuis une quinzaine d'années ou plus pour connecter leurs appareils professionnels. Elle permet aux utilisateurs de se connecter à leurs appareils avec leur compte professionnel AD et au département informatique de gérer intégralement ces appareils. En règle générale, les entreprises s'appuient sur des méthodes de création d'images pour mettre des appareils à la disposition des utilisateurs et utilisent System Center Configuration Manager (SCCM) ou une stratégie de groupe pour les gérer.

La gestion de domaine dans Windows 10 offrira les avantages suivants après la connexion à Azure AD :

- Authentification unique (SSO) aux ressources Azure AD depuis n'importe où.
- Accès au Windows Store pour entreprises à l'aide de comptes professionnels (aucun compte Microsoft requis).
- Itinérance des paramètres utilisateur compatible pour l’entreprise sur tous les appareils avec un compte professionnel (aucun compte Microsoft requis)
- Authentification forte et connexion pratique pour les comptes professionnels avec Microsoft Passport et Windows Hello.
- Possibilité de restreindre l'accès aux appareils conformes aux stratégies de l'entreprise concernant les appareils.

##Configuration requise

La jonction de domaine continue à fonctionner. Toutefois, pour bénéficier des avantages d'Azure AD pour l'authentification unique, de l'itinérance des paramètres avec un compte professionnel et de l'accès au Windows Store avec un compte professionnel, vous aurez besoin des éléments suivants :

- Abonnement Azure AD.
- Azure AD Connect pour étendre l'annuaire local à Azure AD.
- Définition d'une stratégie pour connecter des appareils joints au domaine à Azure AD.
- Version de Windows 10 (version 10551 ou ultérieure) pour les appareils.

Pour activer Microsoft Passport for Work et Windows Hello, vous aurez également besoin des éléments suivants :

- Infrastructure PKI pour l'émission de certificats utilisateur.
- Version 1509 de System Center Configuration Manager pour l'évaluation technique. Pour plus d'informations, consultez [Evaluation technique de Microsoft System Center Configuration Manager](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) et [Blog de l'équipe System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx). C'est nécessaire pour déployer des certificats d'utilisateur en fonction des clés de Microsoft Passport.

En alternative à l'exigence de déploiement d'infrastructure à clé publique, vous pouvez procéder comme suit :

- Avoir quelques contrôleurs de domaine avec les services de domaine Active Directory Windows Server 2016.

Pour permettre un accès conditionnel, vous pouvez créer une stratégie qui autorise l'accès aux appareils joints au domaine sans déploiements supplémentaires. Pour gérer le contrôle d'accès basé sur la conformité de l’appareil, vous aurez besoin des éléments suivants :

- Version 1509 de System Center Configuration Manager pour l'évaluation technique de scénarios Passport.

## Instructions de déploiement

1. Déployer Azure Connect Active Directory Connect : Azure AD Connect permettra aux ordinateurs locaux d'être configurés en tant qu'objets d’appareil dans le cloud. Pour déployer Azure AD Connect, consultez Activation de votre annuaire pour la gestion hybride avec Azure AD Connect.


Si vous avez effectué une [installation personnalisée pour Azure AD Connect](https://azure.microsoft.com/fr-FR/documentation/articles/active-directory-aadconnect-get-started-custom/) (pas l'installation Express), vous devez suivre la procédure, **Créer un point de connexion de service (SCP) dans Active Directory sur site** décrite ci-dessous.

Si vous avez une configuration fédérée avec Azure AD avant l'installation d'Azure AD Connect (par exemple, si vous avez préalablement déployé les services de fédération Active Directory (AD FS)), vous devrez suivre la procédure **Configurer les règles de revendication AD FS** ci-dessous.

### Créer un point de connexion de service (SCP) dans Active Directory local
Les appareils joints au domaine utiliseront cet objet pour découvrir des informations de locataire Azure AD au moment de l'enregistrement automatique avec le service d'enregistrement d'appareils Azure. Sur le serveur Azure AD Connect, exécutez les commandes PowerShell suivantes :

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

>[AZURE.NOTE]Remplacez [*nom de compte de connecteur*] par le compte de domaine utilisé comme compte de connecteur AD.

>[AZURE.NOTE]Le nom d'utilisateur des informations d'identification entrées lorsque la fenêtre contextuelle de Get-Credential s'affiche doit être au format **user@example.com*

### Configurer les règles de revendication AD FS
Cela permet l'inscription instantanée d'un ordinateur avec Azure DRS en permettant aux ordinateurs de procéder à l'authentification avec Kerberos/NTLM via AD FS. Sans cette étape, les ordinateurs atteindront Azure AD de manière différée (en fonction des heures de synchronisation de Connect de Windows Azure AD).

>[AZURE.NOTE]Si vous n'avez pas AD FS en tant que serveur de fédération local, suivez les instructions de votre fournisseur pour créer les règles de revendication.

Sur le serveur AD FS (ou sur une session connectée au serveur AD FS), exécutez les commandes PowerShell suivantes :

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed 
     |   for DomainJoin++.  The rules include:
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

>[AZURE.NOTE]Les ordinateurs Windows 10 authentifieront à l'aide de l'authentification intégrée de Windows vers un point de terminaison WS-Trust actif hébergé par AD FS. Vous devez vérifier que ce point de terminaison est activé. Si vous utilisez le proxy de l'authentification Web, vous devez également vérifier que ce point de terminaison est publié via le proxy. Pour ce faire, vous devez vérifier que adfs/services/trust/13/windowstransport est affiché comme activé dans la console de gestion AD FS sous Service > Points de terminaison.

## Configurer l'enregistrement automatique des appareils via la stratégie de groupe dans Active Directory
Vous pouvez utiliser une stratégie de groupe Active Directory pour configurer vos appareils Windows 10 joints au domaine de manière qu’ils s’inscrivent automatiquement auprès d’Azure AD. Pour cela, consultez les instructions pas à pas suivantes :

1. 	Ouvrez le Gestionnaire de serveur et accédez à **Outils** > **Gestion des stratégies de groupe**.
2.	Dans Gestion des stratégies de groupe, accédez au nœud du domaine qui correspond au domaine où vous voulez activer Joindre Azure AD.
3.	Cliquez avec le bouton droit sur **Objets de stratégie de groupe** et sélectionnez **Nouveau**. Nommez votre objet de stratégie de groupe, par exemple Jonction automatique d'Azure AD. Cliquez sur **OK**.
4.	Cliquez avec le bouton droit sur votre nouvel objet de stratégie de groupe, puis sélectionnez **Modifier**.
5.	Accédez à **Configuration ordinateur** > **Stratégies** > **Modèles d’administration** > **Composants Windows** > **Jonction d’espace de travail**.
6.	Cliquez avec le bouton droit sur **Joindre automatiquement les ordinateurs clients à l’espace de travail**, puis sélectionnez **Modifier**.
7.	Sélectionnez la case d’option **Activé**, puis cliquez sur **Appliquer**. Cliquez sur **OK**.
8.	Vous pouvez maintenant lier l’objet de stratégie de groupe à un emplacement de votre choix. Pour activer cette stratégie pour tous les appareils Windows 10 joints au domaine de votre organisation, liez la stratégie de groupe au domaine. Par exemple :
 - Une unité d'organisation spécifique (UO) dans AD où les ordinateurs Windows 10 joints au domaine seront situés.
 - Un groupe de sécurité spécifique contenant des ordinateurs Windows 10 joints au domaine, qui sera enregistré automatiquement avec Azure AD.
 
>[AZURE.NOTE]Ce modèle de stratégie de groupe a été renommé dans Windows 10. Si vous exécutez l'outil de stratégie de groupe à partir d'un ordinateur Windows 10, la stratégie s'affichera comme suit : <br> **Enregistrer les ordinateurs appartenant au domaine en tant qu'appareils** et la stratégie se trouvera sous l'emplacement suivant :<br> ***Configuration ordinateur/Stratégies/Modèles d'administration/Windows/Inscription d'appareil***

 
## Informations supplémentaires
* [Windows 10 pour l’entreprise : manières d’utiliser des appareils professionnels](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [En savoir plus sur les scénarios d’utilisation pour Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=Nov15_HO4-->