---
title: Configuration de l’inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine | Microsoft Docs
description: Les administrateurs peuvent choisir de faire inscrire automatiquement et en mode silencieux les appareils joints à un domaine Windows auprès d’Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2016
ms.author: markvi

---
# <a name="how-to-setup-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Configuration de l’inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine
L’inscription d’ordinateurs Windows joints à un domaine auprès d’Azure AD est obligatoire pour activer [l’accès conditionnel en fonction de l’appareil à Azure Active Directory](active-directory-conditional-access.md).

Les mises à jour, telles que l’autorisation d’utilisation d’un compte professionnel ou scolaire pour bénéficier d’une expérience SSO pour les applications Azure AD, l’itinérance d’entreprise des paramètres entre les appareils et l’utilisation du Windows Store pour entreprise, bénéficient d’une authentification renforcée et d’une connexion pratique grâce à Windows Hello. 

> La mise à jour [AZURE.NOTE] Windows 10 de novembre 2015 prend en charge certaines des expériences utilisateur améliorées. Toutefois, seule la mise à jour anniversaire prend entièrement en charge l’accès conditionnel en fonction de l’appareil.  
> Pour plus d’informations sur l’accès conditionnel, consultez [Accès conditionnel en fonction de l’appareil à Azure Active Directory](active-directory-conditional-access.md).  
> Pour plus d’informations sur les appareils Windows 10 dans l’espace de travail et les expériences utilisateur après inscription auprès d’Azure AD, consultez [Windows 10 pour l’entreprise : plusieurs manières d’utiliser des appareils professionnels](active-directory-azureadjoin-windows10-devices-overview.md). 
> 
> 

L’inscription est prise en charge dans les versions précédentes de Windows, notamment : 

* Windows 8.1 
* Windows 7 

Pour le cas d’utilisation d’ordinateurs Windows Server faisant office d’ordinateurs de bureau (par exemple, un développeur utilisant Windows Server en tant qu’ordinateur principal), les plateformes suivantes peuvent être inscrites : 

* Windows Server 2016 
* Windows Server 2012 R2 
* Windows Server 2012 
* Windows Server 2008 R2 

Ci-dessous, vous découvrirez les opérations à effectuer dans votre environnement pour activer l’inscription d’appareils Windows joints à un domaine auprès d’Azure AD dans votre organisation. Voici ce que vous verrez : 

1. Composants requis 
2. Déploiement 
3. Résolution de problèmes 
4. Forum Aux Questions 

## <a name="prerequisites"></a>Composants requis
La principale exigence pour activer l’inscription automatique des appareils joints à un domaine auprès d’Azure AD est une version à jour d’Azure AD Connect. 

Selon la façon dont vous avez déployé Azure AD Connect, si vous avez choisi l’installation Express ou personnalisée ou une mise à niveau sur place, les conditions préalables suivantes peuvent avoir été configurées automatiquement : 

1. Le point de connexion de service (SCP) dans Active Directory en local pour la découverte d’informations de client Azure AD par les ordinateurs s’inscrivant auprès d’Azure AD. 
2. L’émission de règles de transformation AD FS pour l’authentification d’ordinateur lors de l’enregistrement (applicable aux configurations fédérées). 

Si vous disposez d’ordinateurs joints à un domaine non Windows 10 dans votre organisation, vous devez disposer des éléments suivants : 

1. Vérifiez que la stratégie permettant aux utilisateurs d’inscrire des appareils est activée dans Azure AD. 
2. Dans AD FS, vérifiez que l’authentification intégrée Windows (WIA) est définie comme une alternative valide à l’authentification multifacteur (MFA). 

## <a name="service-connection-point-for-discovery-of-azure-ad-tenant"></a>Point de connexion de service pour la détection de client Azure AD
Un objet SCP qui conserve des informations de découverte sur le client Azure AD dans lequel les ordinateurs s’inscrivent doit exister dans la partition de contexte de dénomination de configuration de la forêt du domaine auquel les ordinateurs sont joints. Dans une configuration à forêts multiples Active Directory, le SCP doit exister dans toutes les forêts auxquelles les ordinateurs sont joints. 

Le SCP doit se trouver à l’emplacement suivant dans Active Directory : 

    CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Configuration Naming Context] 

> [!NOTE]
> Pour une forêt avec un nom de domaine Active Directory example.com, le contexte de dénomination de configuration est CN=Configuration,DC=example,DC=com 
> 
> 

Vous pouvez vérifier l’existence de l’objet et les valeurs pour la découverte du client Azure AD en utilisant le script PowerShell suivant (remplacez le contexte de dénomination de configuration dans l’exemple avec le vôtre) : 

    $scp = New-Object System.DirectoryServices.DirectoryEntry; 

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com"; 

    $scp.Keywords; 

La sortie de $scp.Keywords présente les informations de client Azure AD comme suit : 

    azureADName:microsoft.com 

    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47 

Si le SCP n’existe pas, vous pouvez le créer en exécutant le script PowerShell suivant sur le serveur Azure AD Connect : 

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1"; 

    $aadAdminCred = Get-Credential; 

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred; 


> [!NOTE]
> Quand vous exécutez $aadAdminCred = Get-Credential,,utilisez le format user@example.com pour le nom d’utilisateur des informations d’identification entrées lorsque la fenêtre contextuelle Get-Credential s’affiche.  
> Quand vous exécutez l’applet de commande Initialize-ADSyncDomainJoinedComputerSync, remplacez [nom de compte de connecteur] par le compte de domaine utilisé comme compte de connecteur Active Directory.  
> L’applet de commande ci-dessus utilise le module Active Directory PowerShell qui s’appuie sur Active Directory Web Services (ADWS) dans les contrôleurs de domaine (DC). ADWS est pris en charge dans Windows Server 2008 R2 ou dans les contrôleurs de domaine de version supérieure. Si vous disposez uniquement de contrôleurs de domaine Windows Server 2008 ou version inférieure, vous pouvez utiliser les API System.DirectoryServices via PowerShell pour créer le SCP et affecter les valeurs de mots clés appropriées. 
> 
> 

## <a name="ad-fs-rules-for-instant-computer-registration-(federated-orgs)"></a>Règles AD FS pour l’inscription instantanée d’ordinateur (organisations fédérées)
Dans une configuration fédérée d’Azure AD, les ordinateurs reposent sur AD FS (ou sur le serveur de fédération local) pour s’authentifier auprès d’Azure AD pour l’inscription à Azure Device Registration Service (Azure DRS). 

> [!NOTE]
> Dans une configuration non fédéré ( le hachage de mot de passe de l’utilisateur est synchronisé à Azure AD), les ordinateurs joints à un domaine Windows 10 et Windows Server 2016 s’authentifient auprès d’Azure DRS à l’aide des informations d’identification écrites dans les comptes d’ordinateur locaux prises en charge dans Azure AD via Azure AD Connect. Pour les ordinateurs non Windows 10/Windows Server 2016 sur une configuration non fédérée, consultez la section Package Windows Installer pour l’inscription d’ordinateurs joints à un domaine non Windows 10/Windows Server 2016 sous Déploiement dans ce document pour découvrir les options qui s’offrent à vous en matière d’activation des stratégies d’accès conditionnel en fonction de l’appareil au sein de votre organisation. 
> 
> 

Pour les ordinateurs Windows 10 et Windows Server 2016, Azure AD Connect associe l’objet d’appareil dans Azure AD à l’objet de compte d’ordinateur local. Pour ce faire, les revendications suivantes doivent être remplies lors de l’authentification auprès d’Azure DRS pour terminer l’inscription et créer l’objet d’appareil en premier lieu : 

* `http://schemas.microsoft.com/ws/2012/01/accounttype` - contenant la valeur de « DJ » qui identifie l’authentification principale en tant qu’ordinateur joint au domaine. 
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid` - contenant la valeur de l’attribut objectGUID du compte d’ordinateur local. 
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` - contenant le SID principal de l’ordinateur, correspondant à la valeur de l’attribut objectSid du compte d’ordinateur local. 
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` - contenant la valeur appropriée qui permet à Azure AD d’approuver le jeton émis par AD FS ou par le service STS local. Cela est important dans une configuration Active Directory à forêts multiples dans laquelle les ordinateurs peuvent être joints à une forêt autre que celle qui se connecte à Azure AD et dans laquelle se trouve AD FS ou le service STS local. Dans le cas d’AD FS, la valeur doit être `http://<domain-name>/adfs/services/trust/` où \<domain-name\> est le nom de domaine validé dans Azure AD. 

Pour créer manuellement ces règles dans AD FS, vous pouvez utiliser le script PowerShell suivant sur les sessions connectées à votre serveur. La première ligne doit être remplacée par le nom de domaine validé dans Azure AD pour votre organisation. 

> [!NOTE]
> Si vous ne disposez pas d’AD FS en tant que serveur de fédération local, suivez les instructions de votre fournisseur pour créer les règles appropriées de revendication. 
> 
> 

    $validatedDomain = "example.com"      # Replace example.com with your validated domain name in Azure AD 

    $rule1 = '@RuleName = "Issue object GUID" 

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);' 

    $rule2 = '@RuleName = "Issue account type for domain joined computers" 

      c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");' 

    $rule3 = '@RuleName = "Pass through primary SID" 

      c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

      c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

      => issue(claim = c2);' 

    $rule4 = '@RuleName = "Issue AccountType with the value User when it’s not a computer account" 

      NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]) 

      => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");' 

    $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID" 

      c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] && 

      c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));' 

    $rule6 = '@RuleName = "Update issuer for DJ computer auth" 

      c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$validatedDomain+'/adfs/services/trust/");' 

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5 + $rule6 

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

> [!NOTE]
> Les ordinateurs Windows 10 et Windows Server 2016 joints à un domaine authentifieront à l’aide de l’authentification intégrée de Windows vers un point de terminaison WS-Trust actif hébergé par AD FS. Vérifiez que ce point de terminaison est activé. Si vous utilisez le proxy d’authentification web, vérifiez également que ce point de terminaison est publié via le proxy. Le point de terminaison est adfs/services/trust/13/windowstransport. Il doit être affiché comme activé dans la console de gestion AD FS sous Service > Points de terminaison. Si vous ne disposez pas d’AD FS en tant que serveur de fédération local, suivez les instructions de votre fournisseur pour vérifier que le point de terminaison correspondant est activé. 
> 
> 

## <a name="ensure-ad-fs-is-set-up-to-support-authentication-of-device-for-registration"></a>Vérifiez qu’AD FS est configuré pour prendre en charge l’authentification de l’appareil pour l’inscription
Dans AD FS, vérifiez que l’authentification intégrée Windows (WIA) est définie comme une alternative valide à l’authentification multifacteur (MFA) pour l’inscription des appareils.

Pour cela, vous devez disposer d’une émission de règles de transformation qui transmet la méthode d’authentification.

1. Ouvrez la console de gestion d’AD FS et accédez à **AD FS>Relations d’approbation > Approbations de partie de confiance**. 
2. Cliquez avec le bouton droit sur l’objet d’approbation de partie de confiance de la plateforme d’identité Microsoft Office 365 et sélectionnez **Modifier les règles de revendication**.
3. Sous l’onglet **Règles de transformation d’émission**, sélectionnez **Ajouter une règle**.
4. Sélectionnez **Envoyer les revendications en utilisant une règle personnalisée** dans la liste de modèles **Règle de revendication**. 
5. Sélectionnez **Suivant**.
6. Dans la zone de texte **Nom de la règle de revendication**, tapez **Règle de revendication de méthode d’authentification**.
7. Dans la zone de texte **Règle de revendication **, tapez `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
   => issue(claim = c);`
8. Sur votre serveur de fédération, ouvrez Windows PowerShell.
9. Tapez la commande suivante : 
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

Où **\<RPObjectName\** est le nom de l’objet de partie de confiance de votre objet d’approbation de partie de confiance Azure Active Directory. Cet objet est généralement nommé plateforme d’identité Microsoft Office 365.

## <a name="deployment-and-roll-out"></a>Déploiement
Une fois les conditions préalables remplies, les ordinateurs joints à un domaine sont prêts à s’inscrire auprès d’Azure AD. 

Les ordinateurs Mise à jour anniversaire Windows 10 et Windows Server 2016 joints à un domaine s’inscriront automatiquement auprès d’Azure AD lors du prochain redémarrage ou de la prochaine connexion utilisateur à Windows. Les nouveaux ordinateurs qui sont joints au domaine s’inscriront auprès d’Azure AD lors du redémarrage suivant l’opération de jonction de domaine. 

> [!NOTE]
> Les ordinateurs Windows 10 joints à un domaine s’inscriront automatiquement auprès d’Azure AD uniquement si l’objet de stratégie de groupe de déploiement est défini. Pour plus d’informations, consultez la section suivante. 
> 
> 

Pour contrôler le déploiement de l’enregistrement automatique des ordinateurs Windows 10/Windows Server 2016 joints à un domaine, vous pouvez utiliser un objet de stratégie de groupe à cet effet. Pour le déploiement de l’inscription automatique des ordinateurs non Windows 10 joints à un domaine, un package Windows Installer peut être déployé sur les ordinateurs sélectionnés. 

> [!NOTE]
> La stratégie de groupe pour le contrôle du déploiement déclenche également l’inscription des ordinateurs Windows 8.1 joints à un domaine. Vous pouvez choisir d’utiliser la stratégie pour l’inscription des ordinateurs Windows 8.1 joints à un domaine ou si vous utilisez plusieurs versions de Windows (notamment Windows 7 ou Windows Server), vous pouvez choisir d’activer l’enregistrement de tous vos ordinateurs non Windows 10/Windows Server 2016 à l’aide du package Windows Installer. 
> 
> 

### <a name="group-policy-object-to-control-roll-out-of-automatic-registration"></a>Objet de stratégie de groupe pour contrôler le déploiement de l’inscription automatique
Pour contrôler le déploiement de l’enregistrement automatique des ordinateurs joints au domaine auprès d’Azure AD, vous pouvez déployer les ordinateurs joints au domaine de registre de stratégie de groupe comme appareils sur les ordinateurs que vous souhaitez enregistrer. Par exemple, vous pouvez déployer la stratégie basée sur un groupe de sécurité ou sur une unité d’organisation (UO). 

Pour définir la stratégie, exécutez les opérations suivantes : 

1. Ouvrez le Gestionnaire de serveur et accédez à **Outils > Gestion des stratégies de groupe**. 
2. Dans Gestion des stratégies de groupe, accédez au nœud du domaine qui correspond au domaine où vous voulez activer l’inscription automatique des ordinateurs Windows 10 ou Windows Server 2016. 
3. Cliquez avec le bouton droit sur **Objets de stratégie de groupe**, puis sélectionnez **Nouveau**. 
4. Nommez votre objet de stratégie de groupe, par exemple *Inscription automatique auprès d’Azure AD*. Cliquez sur OK. 
5. Cliquez avec le bouton droit sur votre nouvel objet de stratégie de groupe, puis sélectionnez **Modifier**. 
6. Accédez à **Configuration ordinateur > Stratégies > Modèles d’administration > Composants Windows > Inscription d’appareil**, cliquez avec le bouton droit sur **Enregistrer les ordinateurs appartenant au domaine en tant qu’appareils**, puis sélectionnez **Modifier**. 
   
   > [!NOTE]
   > Ce modèle de stratégie de groupe a été renommé par rapport aux versions précédentes de la Console de gestion des stratégies de groupe. Si vous exécutez une version précédente de la console, la stratégie sera sous Configuration ordinateur > Stratégies > Modèles d’administration > Composants Windows > Jonction d’espace de travail avec le nom Joindre automatiquement les ordinateurs clients à l’espace de travail. 
   > 
   > 
7. Sélectionnez l’option **Activé**, puis cliquez sur **Appliquer**. 
8. Cliquez sur **OK**. 
9. Liez l’objet de stratégie de groupe à un emplacement de votre choix. Par exemple, une unité d’organisation (UO) spécifique où se trouvent les ordinateurs ou un groupe de sécurité spécifique contenant les ordinateurs qui s’inscriront automatiquement auprès d’Azure AD. Pour activer cette stratégie pour tous les ordinateurs Windows10 ou Windows Server 2016 joints au domaine de votre organisation, liez l’objet de stratégie de groupe au domaine. 

## <a name="msi-package-for-non-windows-10-computers"></a>Package MSI pour les ordinateurs non Windows 10
Pour enregistrer les ordinateurs joints à un domaine exécutant Windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 ou Windows Server 2012 R2, un package Windows Installer (.msi) est disponible au téléchargement :

* [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
* [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

Vous devez déployer ce package à l’aide d’un système de distribution de logiciels comme System Center Configuration Manager. Le package prend en charge les options d’installation en mode silencieux standard avec le paramètre /quiet. Si vous utilisez System Center Configuration Manager 2016, vous pourrez profiter d’avantages supplémentaires, comme la possibilité de suivre les enregistrements terminés. Pour plus d’informations, consultez [System Center 2016](https://www.microsoft.com/cloud-platform/system-center-2016). 

Le programme d’installation crée une tâche planifiée sur le système, qui s’exécute dans le contexte de l’utilisateur et qui est déclenchée lors de la connexion de l’utilisateur à Windows. La tâche inscrit l’appareil en mode silencieux auprès d’Azure AD avec les informations d’identification de l’utilisateur après l’avoir authentifié à l’aide de l’authentification intégrée Windows. La tâche planifiée se trouve dans la bibliothèque du Planificateur de tâches, sous **Microsoft > Jonction d’espace de travail**. 

## <a name="additional-topics"></a>Rubriques supplémentaires
* [Accès conditionnel Azure Active Directory](active-directory-conditional-access.md)

<!--HONumber=Oct16_HO2-->


