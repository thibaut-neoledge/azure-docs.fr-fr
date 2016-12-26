---
title: "Configuration de l’inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine | Microsoft Docs"
description: "Configurez vos appareils Windows joints à un domaine pour les inscrire automatiquement et en mode silencieux auprès d’Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: f26ca6e30e3070b2d2ee1861b88c52013158ba03
ms.openlocfilehash: 45f52d4e02896d38726552adbe81261551151683


---
# <a name="how-to-configure-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Configuration de l’inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine

Pour utiliser [l’accès conditionnel en fonction de l’appareil à Azure Active Directory](active-directory-conditional-access.md), vos ordinateurs doivent être inscrits auprès d’Azure Active Directory (Azure AD). Cet article vous présente les étapes de configuration de l’inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine dans votre organisation.

> [!NOTE]
> La mise à jour Windows 10 de novembre offre certaines des expériences utilisateur améliorées dans Azure AD, mais la mise à jour anniversaire Windows 10 prend entièrement en charge l’accès conditionnel en fonction de l’appareil. Pour plus d’informations sur l’accès conditionnel, consultez [Accès conditionnel Azure Active Directory](active-directory-conditional-access.md). Pour plus d’informations sur les appareils Windows 10 dans l’espace de travail et sur la façon dont un utilisateur inscrit un appareil Windows 10 auprès d’Azure AD, consultez [Windows 10 pour l’entreprise : plusieurs manières d’utiliser des appareils professionnels](active-directory-azureadjoin-windows10-devices-overview.md).
> 
> 

Pour les appareils exécutant Windows, vous pouvez inscrire certaines versions antérieures de Windows, notamment :

- Windows 8.1
- Windows 7

Pour les appareils exécutant Windows Server, vous pouvez inscrire les plateformes suivantes :

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2



## <a name="prerequisites"></a>Conditions préalables

La principale exigence pour l’inscription automatique des appareils joints à un domaine auprès d’Azure AD est d’avoir une version à jour d’Azure Active Directory Connect (Azure AD Connect).

Selon la façon dont vous avez déployé Azure AD Connect, et si vous avez choisi l’installation expresse ou personnalisée ou une mise à niveau sur place, les conditions préalables suivantes peuvent avoir été configurées automatiquement :

- **Le point de connexion de service dans Active Directory en local** : pour la découverte d’informations de client Azure AD par les ordinateurs s’inscrivant auprès d’Azure AD.
 
- **Émission de règles de transformation Active Directory Federation Services (AD FS)** : pour l’authentification d’ordinateur lors de l’inscription (applicable aux configurations fédérées).

Si certains appareils dans vos organisations ne sont pas des appareils Windows 10 joints à un domaine, exécutez les étapes suivantes :

* Définir une stratégie dans Azure AD pour permettre aux utilisateurs d’inscrire des appareils
* Définir l’authentification intégrée Windows comme une alternative valide à l’authentification multifacteur dans AD FS

## <a name="step-1-configure-service-connection-point"></a>Étape 1 : Configuration du point de connexion de service 

Un objet de point de connexion de service (SCP) doit exister dans la partition de contexte de dénomination de configuration du domaine de l’ordinateur. Le point de connexion de service conserve des informations de détection sur le client Azure AD sur lequel les ordinateurs s’inscrivent. Dans une configuration Active Directory de forêts multiples, le point de connexion de service doit exister dans toutes les forêts qui ont des ordinateurs joints à un domaine.

Le SCP se situe sous :  

**CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Contexte de dénomination de votre configuration]**

Pour une forêt avec un nom de domaine Active Directory *example.com*, le contexte de dénomination de configuration est :  

**CN=Configuration,DC=example,DC=com**

Avec le script Windows PowerShell suivant, vous pouvez vérifier l’existence de l’objet et récupérer les valeurs de la découverte : 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com";

    $scp.Keywords;

La sortie de **$scp.Keywords** présente les informations de client Azure AD, par exemple :

azureADName:microsoft.com  
azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Si le point de connexion de service n’existe pas, vous pouvez le créer en exécutant le script PowerShell suivant sur votre serveur Azure AD Connect :

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;



**Remarques :**

- Lorsque vous exécutez **$aadAdminCred = Get-Credential**, vous devez entrer un nom d’utilisateur. Pour le nom d’utilisateur, utilisez le format suivant : **user@example.com** 


- Lorsque vous exécutez l’applet de commande **Initialize-ADSyncDomainJoinedComputerSync**, remplacez [*nom de compte de connecteur*] par le compte de domaine utilisé comme compte de connecteur Active Directory.
  
- L’applet de commande utilise le module Active Directory PowerShell qui s’appuie sur les services Web Active Directory (ADWS) dans un contrôleur de domaine. Les services Web Active Directory sont pris en charge dans les contrôleurs de domaine dans Windows Server 2008 R2 et les versions ultérieures. Pour les contrôleurs de domaine dans Windows Server 2008 ou les versions antérieures, utilisez l’API System.DirectoryServices via PowerShell pour créer le point de connexion de service, puis affectez les valeurs de mots-clés.
 
 



##<a name="step-2-register-your-devices"></a>Étape 2 : Inscription de vos appareils

Les étapes d’inscription de votre appareil dépendent du fait que votre organisation est fédérée ou non. 


### <a name="device-registration-in-non-federated-organizations"></a>Inscription d’appareil dans les organisations non fédérées

L’inscription d’appareil dans une organisation non fédérée n’est possible que si les conditions suivantes sont réunies :

- Vous exécutez Windows 10 et Windows Server 2016 sur votre appareil
- Vos appareils sont joints à un domaine
- La synchronisation du mot de passe à l’aide d’Azure AD Connect est activée

Si toutes ces exigences sont satisfaites, vous n’avez rien à faire pour inscrire vos appareils.  


### <a name="device-registration-in-federated-organizations"></a>Inscription d’appareil dans les organisations fédérées

Dans une configuration Azure AD fédérée, les appareils s’appuient sur AD FS (ou sur le serveur de fédération local) pour s’authentifier auprès d’Azure AD. Ils s’inscrivent auprès du service Azure Active Directory Device Registration.

Pour les ordinateurs Windows 10 et Windows Server 2016, Azure AD Connect associe l’objet d’appareil dans Azure AD à l’objet de compte d’ordinateur local. Les revendications suivantes doivent être remplies lors de l’authentification auprès du service Azure AD Device Registration pour terminer l’inscription et créer l’objet d’appareil :

- **http://schemas.microsoft.com/ws/2012/01/accounttype** contient la valeur DJ, qui identifie l’authentificateur principal comme un ordinateur joint au domaine.

- **http://schemas.microsoft.com/identity/claims/onpremobjectguid** contient la valeur de l’attribut **objectGUID** du compte d’ordinateur local.
 
- **http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid** contient l’ID de sécurité principal de l’ordinateur, qui correspond à la valeur d’attribut **objectSid** du compte d’ordinateur local.

- **http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid** contient la valeur qu’utilise Azure AD pour approuver le jeton émis à partir d’AD FS ou à partir du service d’émission de jeton de sécurité (STS). Ceci est important si vous avez plusieurs domaines vérifiés dans Azure AD. Dans le cas d’AD FS, utilisez **http://\<*domain-name*\>/adfs/services/trust/**, où **\<domain-name\>** correspond au nom de domaine vérifié dans Azure AD.

Pour plus d’informations sur la vérification du domaine, consultez [Ajouter un nom de domaine personnalisé à Azure Active Directory](active-directory-add-domain.md).  
Pour obtenir une liste de vos domaines d’entreprise vérifiés, vous pouvez utiliser l’applet de commande [Get-MsolDomain](https://docs.microsoft.com/powershell/msonline/v1/get-msoldomain). 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)


Les ordinateurs Windows 10 et Windows Server 2016 joints à un domaine s’authentifient à l’aide de l’authentification intégrée de Windows vers un point de terminaison WS-Trust actif hébergé par AD FS. Vérifiez que ce point de terminaison est activé. Si vous utilisez le proxy d’authentification web, vérifiez également que ce point de terminaison est publié via le proxy. Le point de terminaison est **adfs/services/trust/13/windowstransport**. 

Il doit être activé dans la console de gestion AD FS sous **Service > Points de terminaison**. Si vous ne disposez pas d’AD FS en tant que serveur de fédération local, suivez les instructions de votre fournisseur pour vérifier que le point de terminaison correspondant est activé. 



> [!NOTE]
> Si vous n’utilisez pas AD FS pour votre serveur de fédération local, suivez les instructions de votre fournisseur pour créer les règles qui émettent ces revendications.
> 
> 




**Pour créer les règles manuellement, dans AD FS :**

- Sélectionnez l’un des scripts Windows PowerShell suivants. 
- Exécutez le script Windows PowerShell dans une session connectée à votre serveur. 
- Remplacez la première ligne par le nom de domaine validé de votre organisation dans Azure AD.




#### <a name="setting-ad-fs-rules-in-a-single-domain-environment"></a>Configuration des règles AD FS dans un environnement de domaine unique

Utilisez le script suivant pour ajouter les règles AD FS si vous n’avez **qu’un domaine vérifié** :


    <#----------------------------------------------------------------------
    |   Modify the Azure AD Relying Party to include the claims needed
    |   for DomainJoin++. The rules include:
    |   -ObjectGuid
    |   -AccountType
    |   -ObjectSid
    +---------------------------------------------------------------------#>

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

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 


#### <a name="setting-ad-fs-rules-in-a-multi-domain-environment"></a>Configuration des règles AD FS dans un environnement à plusieurs domaines

Si vous avez plusieurs domaines vérifiés, procédez comme suit :

1. Supprimer la règle **IssuerID** existante créée par Azure AD Connect.  
Voici un exemple de cette règle : c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 


2. Exécutez ce script : 

        <#----------------------------------------------------------------------  
        |   Modify the Azure AD Relying Party to include the claims needed  
        |   for DomainJoin++. The rules include:
        |   -ObjectGuid
        |   -AccountType
        |   -ObjectSid
        +---------------------------------------------------------------------#>

        $VerifiedDomain = 'example.com'      # Replace example.com with one of your verified domains

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

        $rule4 = '@RuleName = "Issue AccountType with the value User when its not a computer account" 

        NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]) 

        => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");' 

        $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID" 

        c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));' 

        $rule6 = '@RuleName = "Update issuer for DJ computer auth" 

        c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$VerifiedDomain+'/adfs/services/trust/");' 

        $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

        $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4+ $rule5+  $rule6 

        $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

        Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 



## <a name="step-3-setup-ad-fs-for-authentication-of-device-registration"></a>Étape 3 : Configuration d’AD FS pour l’authentification de l’inscription de l’appareil

Assurez-vous que l’authentification intégrée Windows est définie comme une alternative valide à l’authentification multifacteur pour l’inscription de l’appareil dans AD FS. Pour cela, vous devez disposer d’une règle de transformation d’émission qui transmet la méthode d’authentification.

1. Dans la console de gestion d’AD FS, accédez à **AD FS** > **Relations d’approbation** > **Approbations de partie de confiance**.
2. Cliquez avec le bouton droit sur l’objet d’approbation de partie de confiance de la plateforme d’identité Microsoft Office 365 et sélectionnez **Modifier les règles de revendication**.
3. Sous l’onglet **Règles de transformation d’émission**, sélectionnez **Ajouter une règle**.
4. Sélectionnez **Envoyer les revendications en utilisant une règle personnalisée** dans la liste de modèles **Règle de revendication**.
5. Sélectionnez **Suivant**.
6. Dans la zone **Nom de la règle de revendication**, tapez **Règle de revendication de méthode d’authentification**.
7. Dans la zone **Règle de revendication**, tapez cette règle :  
**c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);**
8. Sur votre serveur de fédération, tapez la commande PowerShell suivante :
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

**\<RPObjectName\>** est le nom de l’objet de partie de confiance de votre objet d’approbation de partie de confiance Azure AD. Cet objet est généralement nommé **plateforme d’identité Microsoft Office 365**.



##<a name="step-4-deployment-and-rollout"></a>Étape 4 : Déploiement

Lorsque les ordinateurs joints à un domaine respectent les conditions préalables, ils sont prêts à s’inscrire auprès d’Azure AD.

Les ordinateurs Mise à jour anniversaire Windows 10 et Windows Server 2016 joints à un domaine s’inscrivent automatiquement auprès d’Azure AD lors du prochain redémarrage de l’appareil ou de la prochaine connexion utilisateur à Windows. Les nouveaux ordinateurs qui sont joints au domaine s’inscrivent auprès d’Azure AD lors du redémarrage de l’appareil après l’opération de jonction au domaine.

> [!NOTE]
> Les ordinateurs Windows 10 joints à un domaine s’inscrivent automatiquement auprès d’Azure AD uniquement si l’objet de stratégie de groupe de déploiement est défini.
> 
> 

Vous pouvez utiliser un objet de stratégie de groupe pour contrôler le déploiement de l’inscription automatique des ordinateurs Windows 10 et Windows Server 2016 joints à un domaine. 

Pour le déploiement de l’inscription automatique des ordinateurs autres que Windows 10 joints à un domaine, vous pouvez déployer un package Windows Installer sur les ordinateurs que vous sélectionnez.

> [!NOTE]
> La stratégie de groupe pour le contrôle du déploiement déclenche également l’inscription des ordinateurs Windows 8.1 joints à un domaine. Vous pouvez utiliser la stratégie pour l’inscription des ordinateurs Windows 8.1 joints à un domaine. Ou bien, si vous avez un mélange de versions de Windows, notamment les versions Windows 7 ou Windows Server, vous pouvez inscrire tous vos ordinateurs autres que Windows 10 et Windows Server 2016 à l’aide d’un package Windows Installer.
> 
> 

### <a name="create-a-group-policy-object-to-control-the-rollout-of-automatic-registration"></a>Créer un objet de stratégie de groupe pour contrôler le déploiement de l’inscription automatique

Pour contrôler le déploiement de l’inscription automatique des ordinateurs joints à un domaine auprès d’Azure AD, vous pouvez déployer la stratégie de groupe **d’inscription des ordinateurs joints à un domaine comme appareils** sur les ordinateurs que vous souhaitez inscrire. Par exemple, vous pouvez déployer la stratégie sur un groupe de sécurité ou sur une unité organisationnelle.

**Pour configurer la stratégie :**

1. Ouvrez le Gestionnaire de serveur et accédez à **Outils** > **Gestion des stratégies de groupe**.
2. Accédez au nœud de domaine qui correspond au domaine dans lequel vous souhaitez activer l’inscription automatique d’ordinateurs Windows 10 ou Windows Server 2016.
3. Cliquez avec le bouton droit sur **Objets de stratégie de groupe**, puis sélectionnez **Nouveau**.
4. Entrez un nom pour votre objet de stratégie de groupe. Par exemple, *Inscription automatique dans Azure AD*. Sélectionnez **OK**.
5. Cliquez avec le bouton droit sur votre nouvel objet de stratégie de groupe, puis sélectionnez **Modifier**.
6. Accédez à **Configuration ordinateur** > **Stratégies** > **Modèles d’administration** > **Composants Windows** > **Enregistrement d’appareil**. Cliquez avec le bouton droit sur **Inscrire les ordinateurs joints du domaine en tant qu’appareils**, puis sélectionnez **Modifier**.
   
   > [!NOTE]
   > Ce modèle de stratégie de groupe a été renommé par rapport aux versions précédentes de la Console de gestion des stratégies de groupe. Si vous utilisez une version antérieure de la console, accédez à **Configuration ordinateur** > **Stratégies** > **Modèles d’administration** > **Composants Windows** > **Jonction d’espace de travail** > **Joindre automatiquement les ordinateurs clients à l’espace de travail**.
   > 
   > 
7. Sélectionnez **Activé**, puis **Appliquer**.
8. Sélectionnez **OK**.
9. Liez l’objet de stratégie de groupe à un emplacement de votre choix. Par exemple, vous pouvez le lier à une unité organisationnelle spécifique. Vous pouvez également le lier à un groupe de sécurité spécifique d’ordinateurs qui s’inscrivent automatiquement auprès d’Azure AD. Pour définir cette stratégie pour tous les ordinateurs Windows 10 ou Windows Server 2016 joints à un domaine de votre organisation, liez l’objet de stratégie de groupe au domaine.

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>Packages Windows Installer pour les ordinateurs autres que Windows 10
Pour inscrire les ordinateurs joints à un domaine exécutant Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 dans un environnement fédéré, vous pouvez télécharger et installer les fichiers de package Windows Installer (.msi) suivants :

* [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
* [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

Déployez le package à l’aide d’un système de distribution de logiciels comme System Center Configuration Manager. Le package prend en charge les options d’installation en mode silencieux standard avec le paramètre *quiet*. System Center Configuration Manager 2016 offre des avantages supplémentaires issus des versions précédentes, comme la possibilité de suivre les inscriptions terminées. Pour plus d’informations, consultez [System Center 2016](https://www.microsoft.com/en-us/cloud-platform/system-center).

Le programme d’installation crée une tâche planifiée sur le système, qui s’exécute dans le contexte de l’utilisateur. La tâche est déclenchée lorsque l’utilisateur se connecte à Windows. La tâche inscrit l’appareil en mode silencieux auprès d’Azure AD avec les informations d’identification de l’utilisateur après l’avoir authentifié à l’aide de l’authentification intégrée Windows. Pour afficher la tâche planifiée, accédez à **Microsoft** > **Jonction d’espace de travail**, puis accédez à la bibliothèque du Planificateur de tâches.

## <a name="next-steps"></a>Étapes suivantes
* [Accès conditionnel Azure Active Directory](active-directory-conditional-access.md)




<!--HONumber=Nov16_HO4-->


