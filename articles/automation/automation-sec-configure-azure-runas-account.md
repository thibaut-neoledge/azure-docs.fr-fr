<properties
    pageTitle="Configurer un compte d’identification Azure | Microsoft Azure"
    description="Ce didacticiel vous guide tout au long des procédures de création et de test d’une authentification de principal de sécurité dans Azure Automation. Il est complété par un exemple d’utilisation."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
	keywords="nom du principal du service, setspn, authentification azure"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/29/2016"
    ms.author="magoedte"/>

# Authentifier des Runbooks avec un compte d’identification Azure

Cette rubrique vous décrit la configuration d’un compte Azure Automation à partir du portail Azure à l’aide de la fonctionnalité Compte d’identification qui permet d’authentifier les Runbooks gérant les ressources dans Azure Resource Manager ou dans la gestion des services Azure.

Lorsque vous créez un compte Automation dans le portail Azure, les actions suivantes s’effectuent également automatiquement :

- Création d’un compte d’identification qui :
    - Crée un nouveau principal de service dans Azure Active Directory.
    - Crée un certificat.
    - Affecte le rôle RBAC (contrôle d’accès en fonction du rôle) de contributeur qui sera utilisé pour gérer les ressources Resource Manager à l’aide de Runbooks.
- Crée un compte d’identification Classic en chargeant un certificat de gestion, qui sera utilisé pour gérer les ressources de gestion des services Azure ou les ressources classiques à l’aide de Runbooks.

Le processus s’en trouve ainsi simplifié, et vous êtes en mesure de commencer rapidement à générer et déployer des Runbooks pour répondre à vos besoins d’automatisation.

Lorsque vous utilisez un compte d’identification standard ou Classic, vous pouvez :

- Bénéficier d’une méthode d’authentification Azure standardisée lorsque vous gérez des ressources Azure Resource Manager ou des ressources des services de gestion Azure à partir de Runbooks dans le portail Azure.
- Automatiser l’utilisation de Runbooks globaux configurés dans les alertes Azure.


>[AZURE.NOTE] La [fonctionnalité d’intégration d’alertes](../azure-portal/insights-receive-alert-notifications.md) Azure associée aux Runbooks globaux requiert un compte Automation configuré avec un compte d’identification standard ou Classic. Vous pouvez sélectionner un compte Automation pour lequel un compte d’identification standard ou Classic est déjà défini ou choisir d’en créer un.

Nous allons vous montrer comment créer un compte Automation à partir du portail Azure, le mettre à jour à l’aide de PowerShell et vous authentifier dans vos Runbooks.

Avant de poursuivre, voici quelques points que vous devez comprendre et prendre en considération :

- Cela n’affecte pas les comptes Automation existants déjà créés dans le modèle de déploiement classique ou Resource Manager.
- Cela fonctionne uniquement pour les comptes Automation créés via le portail Azure. Si vous tentez de créer un compte à partir du portail Azure Classic, la configuration du compte d’identification ne sera pas répliquée.
- Vous disposez peut-être de Runbooks et de ressources (par exemple, des planifications et des variables) qui avaient été créés pour gérer des ressources classiques, et vous voulez peut-être que ces Runbooks utilisent l’authentification avec le nouveau compte d’identification Classic. Dans ce cas, vous devez migrer les Runbooks vers le nouveau compte Automation ou mettre à jour votre compte existant à l’aide du script PowerShell situé plus loin dans cet article.
- Pour vous authentifier à l’aide du nouveau compte d’identification ou du compte Automation d’identification Classic, vous devez modifier les Runbooks existants avec l’exemple de code indiqué plus loin dans cet article.

    >[AZURE.NOTE] Le compte d’identification sert à l’authentification des ressources Resource Manager à l’aide du principal de service basé sur les certificats. Le compte d’identification Classic sert à l’authentification des ressources de gestion des services à l’aide du certificat de gestion.


## Créer un compte Automation à partir du portail Azure

Dans cette section, vous allez créer un compte Azure Automation à l’aide du portail Azure. Cette procédure permet de créer aussi bien un compte d’identification standard qu’un compte d’identification Classic.

>[AZURE.NOTE] Pour effectuer cette procédure, vous *devez* être membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement, ce qui permet d’accéder à l’abonnement de l’utilisateur. Vous devez également ajouter l’utilisateur en tant qu’Utilisateur dans le service Azure Active Directory (Azure AD) par défaut de cet abonnement. Il n’est pas nécessaire d’attribuer au compte un rôle doté de privilèges.

1. Connectez-vous au portail Azure avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.
2. Sélectionnez **Comptes Automation**.
3. Dans le panneau Comptes Automation, cliquez sur **Ajouter**.<br>![Ajouter un compte Automation](media/automation-sec-configure-azure-runas-account/create-automation-account-properties.png)

    >[AZURE.NOTE] Si l’avertissement suivant s’affiche dans le panneau **Ajouter un compte Automation**, cela signifie que votre compte n’est pas membre du rôle Administrateurs des abonnements ni coadministrateur de l’abonnement.<br>![Avertissement Ajouter un compte Automation](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)

4. Dans le panneau **Ajouter un compte Automation**, entrez le nom de votre nouveau compte Automation dans la zone **Nom**.
5. Si vous disposez de plusieurs abonnements, spécifiez-en un pour le nouveau compte, ainsi qu’un **groupe de ressources** nouveau ou existant et un **emplacement** de centre de données Azure.
6. Vérifiez que l’option **Créer un compte d’identification Azure** est bien définie sur **Oui**, puis cliquez sur le bouton **Créer**.

    >[AZURE.NOTE] Si vous choisissez de ne pas créer de compte d’identification en sélectionnant l’option **Non**, un message d’avertissement s’affiche dans le panneau **Ajouter un compte Automation**. Même si le compte est créé dans le portail Azure, il n’aura pas d’identité d’authentification correspondante au sein de votre service de répertoire des abonnements classique ou Resource Manager. Par conséquent, le compte n’aura pas accès aux ressources de votre abonnement. Cela empêche tous les Runbooks faisant référence à ce compte de pouvoir s’authentifier et d’effectuer des tâches sur les ressources de ces modèles de déploiement.

    >![Avertissement Ajouter un compte Automation](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)<br> Si le principal du service n’est pas créé, le rôle Collaborateur n’est pas attribué.


7. Pour suivre la progression de la création du compte Automation, accédez à l’onglet **Notifications** du menu.

### Ressources incluses

Une fois le compte Automation créé, plusieurs ressources vous sont automatiquement créées. Le tableau ci-dessous récapitule les ressources du compte d’identification.<br>

Ressource|Description
----|----
Runbook AzureAutomationTutorial|Exemple de Runbook PowerShell qui illustre l’authentification à l’aide du compte d’identification et l’accès à l’ensemble des ressources Resource Manager.
Runbook AzureAutomationTutorialScript|Exemple de Runbook PowerShell qui illustre l’authentification à l’aide du compte d’identification et l’accès à l’ensemble des ressources Resource Manager.
AzureRunAsCertificate|Ressource de certificat créée automatiquement lors de la création du compte Automation ou à l’aide du script PowerShell figurant plus loin dans cet article pour un compte existant. Ce certificat vous permet de vous authentifier auprès d’Azure afin de pouvoir gérer les ressources Azure Resource Manager des Runbooks. Ce certificat a une durée de vie d’un an.
AzureRunAsConnection|Ressource de connexion créée automatiquement lors de la création du compte Automation ou à l’aide du script PowerShell figurant plus loin dans cet article pour un compte existant.

Le tableau ci-dessous récapitule les ressources du compte d’identification Classic.<br>

Ressource|Description
----|----
Runbook AzureClassicAutomationTutorial|Exemple de Runbook qui accède à toutes les machines virtuelles Classic d’un abonnement à l’aide du compte d’identification Classic (certificat), puis indique leur nom et état.
Runbook AzureClassicAutomationTutorialScript|Exemple de Runbook qui accède à toutes les machines virtuelles Classic d’un abonnement à l’aide du compte d’identification Classic (certificat), puis indique leur nom et état.
AzureClassicRunAsCertificate|Ressource de certificat créée automatiquement et utilisée pour l’authentification auprès d’Azure afin que vous puissiez gérer les ressources Azure Classic des Runbooks. Ce certificat a une durée de vie d’un an.
AzureClassicRunAsConnection|Ressource de connexion créée automatiquement et utilisée pour l’authentification auprès d’Azure afin que vous puissiez gérer les ressources Azure Classic des Runbooks.  

## Vérifier l’authentification d’identification

Nous allons à présent effectuer un test rapide afin de vérifier que vous êtes en mesure de vous authentifier à l’aide du nouveau compte d’identification.

1. Dans le portail Azure, ouvrez le compte Automation que vous avez créé.
2. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des runbooks.
3. Sélectionnez le Runbook **AzureAutomationTutorialScript**, puis cliquez sur **Démarrer** pour le lancer. Vous allez recevoir une invite de confirmation de démarrage du Runbook.
4. Un [travail du Runbook](automation-runbook-execution.md) est créé. Le panneau **Travail** affiche l’état du travail dans la mosaïque **Résumé du travail**. L’état initial du travail est *Mis en file d’attente* pour indiquer qu’il attend la disponibilité d’un Runbook Worker dans le cloud. L’état passe ensuite à *Démarrage en cours* lorsqu’un Worker sélectionne le travail, puis à *En cours d’exécution* lorsque le Runbook commence à s’exécuter. Une fois le travail du Runbook terminé, l’état devient **Terminé**.<br> ![Test de Runbook du principal de sécurité](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)<br>
5. Pour afficher les résultats détaillés du Runbook, cliquez sur la mosaïque **Sortie**.<br> Le panneau **Sortie** doit indiquer que le compte est authentifié et a retourné la liste de toutes les ressources disponibles dans le groupe de ressources.
6. Fermez le panneau **Sortie** pour revenir au panneau **Résumé du travail**.
7. Fermez le panneau **Résumé du travail** et le panneau du Runbook **AzureAutomationTutorialScript** correspondant.

## Vérifier l’authentification d’identification Classic

Nous allons à présent effectuer un test rapide afin de vérifier que vous êtes en mesure de vous authentifier à l’aide du nouveau compte d’identification Classic.

1. Dans le portail Azure, ouvrez le compte Automation que vous avez créé.
2. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des runbooks.
3. Sélectionnez le Runbook **AzureClassicAutomationTutorialScript**, puis cliquez sur **Démarrer** pour le lancer. Vous allez recevoir une invite de confirmation de démarrage du Runbook.
4. Un [travail du Runbook](automation-runbook-execution.md) est créé. Le panneau Travail s’ouvre et l’état du travail est affiché dans la mosaïque **Résumé du travail**. L’état initial du travail est *Mis en file d’attente* pour indiquer qu’il attend la disponibilité d’un Runbook Worker dans le cloud. Il passe ensuite à *Démarrage en cours* lorsqu’un Worker sélectionne le travail, puis à *En cours d’exécution* lorsque le Runbook commence à s’exécuter. À l’issue du travail du Runbook, l’état **Terminé** doit s’afficher.<br> ![Test de Runbook du principal de sécurité](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
5. Pour afficher les résultats détaillés du Runbook, cliquez sur la mosaïque **Sortie**.<br> Le panneau **Sortie** doit indiquer que le compte est authentifié et a retourné la liste de toutes les machines virtuelles Classic de l’abonnement.
6. Fermez le panneau **Sortie** pour revenir au panneau **Résumé du travail**.
7. Fermez le panneau **Résumé du travail** et le panneau du Runbook **AzureClassicAutomationTutorialScript** correspondant.

## Mise à jour d’un compte Automation à l’aide de PowerShell

Nous vous permettons ici d’utiliser PowerShell pour mettre à jour votre compte Automation existant si :

- Vous avez créé un compte Automation sans compte d’identification.
- Vous possédez déjà un compte Automation pour gérer les ressources Resource Manager et vous voulez le mettre à jour pour inclure le compte d’identification pour l’authentification du Runbook.
- Vous possédez déjà un compte Automation pour gérer les ressources classiques et souhaitez le mettre à jour pour utiliser le compte d’identification Classic au lieu de créer un compte et d’y migrer vos Runbooks et ressources.

Avant de continuer, vérifiez que :

- Vous avez téléchargé et installé [Windows Management Framework 4.0 (WMF)](https://www.microsoft.com/download/details.aspx?id=40855) si vous utilisez Windows 7. Si vous utilisez Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows 8.1 ou Windows 7 SP1, [Windows Management Framework 5.0](https://www.microsoft.com/download/details.aspx?id=50395) peut être installé.
- Vous avez installé Azure PowerShell 1.0. Pour plus d’informations sur cette version et sur la méthode d’installation, voir l’article [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).
- Vous avez créé un compte Automation. Ce compte sera référencé en tant que valeur des paramètres *–AutomationAccountName* et *–ApplicationDisplayName* dans les deux scripts des sections ci-dessous.

Pour obtenir les valeurs des paramètres *SubscriptionID*, *ResourceGroup* et *AutomationAccountName*, qui sont requis pour les scripts, dans le portail Azure, sélectionnez votre compte Automation dans le panneau **Compte Automation**, puis sélectionnez **Tous les paramètres**. Dans le panneau **Tous les paramètres**, sous **Paramètres du compte**, sélectionnez **Propriétés**. Dans le panneau **Propriétés**, vous pouvez observer ces valeurs.<br> ![Propriétés du compte Automation](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)

### Créer le script PowerShell d’un compte d’identification

Le script PowerShell suivant configurera :

- Une application Azure AD authentifiée avec un certificat auto-signé, la création d’un compte de principal du service pour cette application dans Azure AD et l’affectation du rôle de contributeur pour ce compte dans votre abonnement actuel. Vous pouvez remplacer le rôle de contributeur par un rôle de propriétaire ou par tout autre rôle. Pour plus d’informations, consultez l’article [Contrôle d’accès en fonction du rôle dans Azure Automation](../automation/automation-role-based-access-control.md).
- Une ressource de certificat Automation dans le compte Automation spécifié nommée AzureRunAsCertificate. Cette ressource contient le certificat utilisé dans le principal du service.
- Une ressource de connexion Automation dans le compte Automation spécifié nommée AzureRunAsConnection. La ressource contient l’ID de l’application, l’ID du client, l’ID de l’abonnement et l’empreinte du certificat.

Les étapes suivantes vous guident tout au long de l’exécution du script.

1. Enregistrez le script suivant sur votre ordinateur. Dans cet exemple, enregistrez-le sous le nom de fichier **New-AzureServicePrincipal.ps1**.

        #Requires -RunAsAdministrator
        Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [String] $CertPlainPassword,

        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12
        )

        Login-AzureRmAccount
        Import-Module AzureRM.Resources
        Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")

        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose

        $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @($CertPath, $CertPlainPassword)
        $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= $EndDate
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.Type = "AsymmetricX509Cert"
        $KeyCredential.Usage = "Verify"
        $KeyCredential.Value = $KeyValue

        # Use Key credentials
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ApplicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential

        New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
        Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose

        $NewRole = $null
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
           Sleep 5
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           Sleep 10
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }

        # Get the tenant id for this subscription
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1

        # Create the automation resources
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        $ConnectionAssetName = "AzureRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues

2. Sur votre ordinateur, démarrez **Windows PowerShell** dans l’écran **Démarrer** avec des droits de l’utilisateur élevés.
3. À partir de l’interface de ligne de commande PowerShell avec élévation de privilèges, accédez au dossier contenant le script que vous avez créé à l’étape 1, puis exécutez-le. Modifiez les valeurs des paramètres *–ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* et *-CertPlainPassword*.<br>

    >[AZURE.NOTE] Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure. Vous devez vous connecter avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.

        .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName>
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>"  
<br>

Une fois le script exécuté, reportez-vous à [l’exemple de code](#sample-code-to-authenticate-with-resource-manager-resources) pour vous authentifier auprès des ressources Resource Manager et valider la configuration des informations d’identification.

### Créer le script PowerShell d’un compte d’identification Classic

Le script PowerShell suivant configurera :

- Une ressource de certificat Automation dans le compte Automation spécifié nommée **AzureClassicRunAsCertificate**. La ressource contient le certificat utilisé pour authentifier vos Runbooks.
- Une ressource de connexion Automation dans le compte Automation spécifié nommée **AzureClassicRunAsConnection**. La ressource contient le nom de l’abonnement, l’ID de l’abonnement et le nom de la ressource de certificat.

Le script crée un certificat de gestion auto-signé et l’enregistre dans le dossier de fichiers temporaires sur votre ordinateur, sous le profil utilisateur utilisé pour exécuter la session PowerShell : *%USERPROFILE%\\AppData\\Local\\Temp*. Après l’exécution du script, vous devez charger le certificat de gestion Azure dans le magasin de gestion de l’abonnement dans lequel le compte Automation a été créé. Les étapes suivantes vous guident tout au long de l’exécution du script et du chargement du certificat.

1. Enregistrez le script suivant sur votre ordinateur. Dans cet exemple, enregistrez-le sous le nom de fichier **New-AzureClassicRunAsAccount.ps1**.

        #Requires -RunAsAdministrator
        Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [String] $CertPlainPassword,

        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12
        )

        Login-AzureRmAccount
        Import-Module AzureRM.Resources
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId
        $SubscriptionName = $subscription.Subscription.SubscriptionName

        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")
        $CertPathCer = Join-Path $env:TEMP ($ApplicationDisplayName + ".cer")

        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPathCer -Type CERT | Write-Verbose

        # Create the automation resources
        $ClassicCertificateAssetName = "AzureClassicRunAsCertificate"
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name $ClassicCertificateAssetName  -Password $CertPassword -Exportable | write-verbose

        # Create an Automation connection asset named AzureClassicRunAsConnection in the Automation account. This connection uses ClassicCertificateAssetName.
        $ConnectionAssetName = "AzureClassicRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicCertificateAssetName}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureClassicCertificate -ConnectionFieldValues $ConnectionFieldValues

        Write-Host -ForegroundColor red "Please upload the cert $CertPathCer to the Management store by following the steps below."
        Write-Host -ForegroundColor red "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates."
        Write-Host -ForegroundColor red "Then click Upload and upload the certificate $CertPathCer"

2. Sur votre ordinateur, démarrez **Windows PowerShell** dans l’écran **Démarrer** avec des droits de l’utilisateur élevés.
3. À partir de l’interface de ligne de commande PowerShell avec élévation de privilèges, accédez au dossier contenant le script que vous avez créé à l’étape 1, puis exécutez-le. Modifiez les valeurs des paramètres *–ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* et *-CertPlainPassword*.<br>

    >[AZURE.NOTE] Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure. Vous devez vous connecter avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.

        .\New-AzureClassicRunAsAccount.ps1 -ResourceGroup <ResourceGroupName>
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>"

Une fois le script exécuté, vous devez copier le certificat créé dans le dossier **Temp** de votre profil utilisateur. Suivez les étapes pour [charger un certificat d’API de gestion](../azure-api-management-certs.md) vers le portail Azure Classic, puis reportez-vous à [l’exemple de code](#sample-code-to-authenticate-with-service-management-resources) pour valider la configuration des informations d’identification avec les ressources de gestion des services.

## Exemple de code pour l’authentification avec des ressources Azure Resource Manager

Vous pouvez utiliser l’exemple de code mis à jour suivant, extrait de l’exemple de Runbook **AzureAutomationTutorialScript**, pour procéder à une authentification avec le compte d’identification pour gérer les ressources Azure Resource Manager avec vos Runbooks.

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

       "Logging in to Azure..."
       Add-AzureRmAccount `
         -ServicePrincipal `
         -TenantId $servicePrincipalConnection.TenantId `
         -ApplicationId $servicePrincipalConnection.ApplicationId `
         -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
	   "Setting context to a specific subscription"	 
	   Set-AzureRmContext -SubscriptionId $SubId	 		 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
           $ErrorMessage = "Connection $connectionName not found."
           throw $ErrorMessage
         } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
         }
    }


Le script inclut deux lignes de code supplémentaires pour prendre en charge le référencement d’un contexte d’abonnement ; le travail entre plusieurs abonnements est ainsi facilité. La ressource variable *SubscriptionId* contient l’ID de l’abonnement. Suite à l’instruction d’applet de commande Add-AzureRmAccount, l’[applet de commande Set-AzureRmContext](https://msdn.microsoft.com/library/mt619263.aspx) est indiquée avec le jeu de paramètres *-SubscriptionId*. Si le nom de la variable est trop générique, vous pouvez modifier le nom de la variable afin que celui-ci comprenne un préfixe ou une autre convention d’affectation de noms pour faciliter son identification. Vous pouvez également utiliser le jeu de paramètres *-SubscriptionName* au lieu de *-SubscriptionId* et de la ressource variable correspondante.

Notez que l’applet de commande utilisée pour l’authentification (**Add-AzureRmAccount**) dans le Runbook utilise le jeu de paramètres *ServicePrincipalCertificate*. Elle effectue l’authentification à l’aide du certificat du principal du service, et non des informations d’identification.

## Exemple de code pour l’authentification avec les ressources de gestion des services

Vous pouvez utiliser l’exemple de code mis à jour suivant, extrait de l’exemple de Runbook **AzureClassicAutomationTutorialScript**, pour procéder à une authentification avec le compte d’identification Classic pour gérer les ressources classiques avec vos Runbooks.

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID


## Étapes suivantes

- Pour plus d’informations sur les principaux de service, consultez l’article [Objets Principal du service et Application](../active-directory/active-directory-application-objects.md).
- Pour plus d’informations sur le contrôle d’accès en fonction du rôle dans Azure Automation, reportez-vous à l’article [Contrôle d’accès en fonction du rôle dans Azure Automation](../automation/automation-role-Based-access-control.md).
- Pour plus d’informations sur les certificats et les services Azure, consultez la page [Vue d’ensemble des certificats pour Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).

<!---HONumber=AcomDC_0810_2016-->