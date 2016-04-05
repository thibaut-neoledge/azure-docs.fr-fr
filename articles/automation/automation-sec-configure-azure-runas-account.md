<properties
    pageTitle="Configurer un compte d’authentification Azure | Microsoft Azure"
    description="Ce didacticiel vous guide tout au long des procédures de création et de test d’une authentification de principal de sécurité dans Azure Automation. Il est complété par un exemple d’utilisation."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/30/2016"
    ms.author="magoedte"/>

# Authentifier des Runbooks avec un compte d’identification Azure
Cette rubrique vous explique la manière de configurer un compte Automation à partir du portail Azure à l’aide de la nouvelle fonctionnalité de compte d’identification (également appelée « principal du service ») afin de vous permettre d’accéder aux ressources Azure Resource Manager (ARM) dans votre abonnement avec les Runbooks Automation. Lorsque vous créez un compte Automation dans le portail Azure, un nouveau principal du service est automatiquement créé et affecté par défaut au rôle RBAC (contrôle d’accès en fonction du rôle) de Contributeur dans l’abonnement. Le processus s’en trouve ainsi simplifié, et vous êtes en mesure de commencer rapidement à générer et déployer des Runbooks pour répondre à vos besoins d’automatisation.

Avantages d’un principal du service :

* Bénéficier d’une méthode d’authentification Azure standardisée lorsque vous gérez des ressources Azure ARM à l’aide de Runbooks
* Automatiser l’utilisation de Runbooks globaux configurés dans les alertes Azure


>[AZURE.NOTE] La [fonctionnalité d’intégration d’alertes](../azure-portal/insights-receive-alert-notifications.md) Azure associée aux Runbooks globaux requiert un compte Automation configuré avec un principal du service. Vous pouvez sélectionner un compte Automation déjà associé à un utilisateur principal du service ou choisir d’en créer un nouveau.

Nous allons vous montrer comment créer un compte Automation à partir du portail Azure et d’Azure PowerShell, et vous expliquer comment vous authentifier auprès de ce principal du service dans vos Runbooks.

## Créer un compte Automation à partir du portail Azure
Dans cette section, vous allez suivre une procédure qui vous permettra de créer un compte Azure Automation et un principal du service à partir du portail Azure.

>[AZURE.NOTE] L’utilisateur qui exécute ces étapes *doit* être membre du rôle Administrateurs d’abonnement.

1. Connectez-vous au portail Azure en tant qu’administrateur de service pour l’abonnement Azure que vous souhaitez gérer.
2. Sélectionnez **Comptes Automation**.
3. Dans le panneau Comptes Automation, cliquez sur **Ajouter**.<br>![Ajouter un compte Automation](media/automation-sec-configure-azure-runas-account/add-automation-account-properties.png)
4. Dans le panneau **Ajouter un compte Automation**, entrez le nom de votre nouveau compte Automation dans la zone **Nom**.
5. Si vous disposez de plusieurs abonnements, spécifiez celui du nouveau compte, ainsi qu’un **Groupe de ressources** nouveau ou existant et un **emplacement** de centre de données Azure.
6. Vérifiez que l’option **Créer un compte d’authentification Azure** est bien définie sur la valeur **Oui**, puis cliquez sur le bouton **Créer**.  
7. Lorsqu’Azure crée le compte Automation, vous pouvez en suivre la progression sous l’onglet **Notifications** du menu.

Une fois la procédure terminée, le compte Automation est créé avec une ressource de certificat nommée **AzureRunAsCertificate** valable un an, complétée par une ressource de connexion nommée **AzureRunAsConnection**.

## Mise à jour d’un compte Automation à l’aide de PowerShell
La procédure suivante permet de mettre à jour un compte Automation existant et de créer le principal du service à l’aide de PowerShell. Vous devez suivre cette procédure si vous avez créé un compte sans créer de compte d’identification.

Avant de continuer, vérifiez que :

1. Vous avez téléchargé et installé le [module Azure Active Directory pour Windows PowerShell (version 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297).
2. Vous avez créé un compte Automation. Ce compte sera référencé en tant que valeur des paramètres –AutomationAccountName et –ApplicationDisplayName dans le script ci-dessous.

Le script PowerShell configurera les éléments suivants :

* Une application Azure AD pouvant être authentifiée avec un certificat auto-signé, un compte de principal du service pour cette application dans Azure AD et l’affectation du rôle de contributeur (que vous pouvez remplacer par un rôle de propriétaire ou par tout autre rôle) pour ce compte dans votre abonnement actuel. Pour plus d’informations, veuillez consulter l’article [Contrôle d’accès en fonction du rôle dans Azure Automation](../automation/automation-role-based-access-control.md).  
* Une ressource de certificat Automation dans le compte Automation spécifié nommée **AzureRunAsCertificate**, qui contient le certificat utilisé dans le principal du service.
* Une ressource de connexion Automation dans le nom du compte Automation spécifié nommée **AzureRunAsConnection**, qui contient l’ID d’application, l’ID de locataire, l’ID d’abonnement et l’empreinte numérique du certificat.<br>

### Exécution du script PowerShell

1. Enregistrez le script suivant sur votre ordinateur. Dans cet exemple, enregistrez-le sous le nom de fichier **New-AzureServicePrincipal.ps1**.  

 ```
    #Requires - RunAsAdministrator
    Param (
    [Parameter(Mandatory=$true)]
    [String] $ResourceGroup,

    [Parameter(Mandatory=$true)]
    [String] $AutomationAccountName,
   
    [Parameter(Mandatory=$true)]
    [String] $ApplicationDisplayName,
   
    [Parameter(Mandatory=$true)]
    [String] $CertPlainPassword,
   
    [Parameter(Mandatory=$false)]
    [int] $NoOfMonthsUntilExpired = 12
    )
   
    Login-AzureRmAccount
    Import-Module AzureRM.Resources
		
    $CurrentDate = Get-Date
    $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
    $KeyId = (New-Guid).Guid
    $CertPath = Join-Path $env:TEMP ($ServicePrincipalDisplayName + ".pfx")

    $Cert = New-SelfSignedCertificate -DnsName $ServicePrincipalDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

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
    $Application = New-AzureRmADApplication -DisplayName $ServicePrincipalDisplayName -HomePage ("http://" + $ServicePrincipalDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential

    New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
    Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose

    $NewRole = $null
    $Retries = 0;
    While ($NewRole -eq $null -and $Retries -le 2)
    {
      # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
      Sleep 5
      New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose
      Sleep 5
      $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
      $Retries++;
    }

    # Get the tenant id for this subscription
    $SubscriptionInfo = Get-AzureRmSubscription
    $TenantID = $SubscriptionInfo | Select TenantId -First 1

    # Create the automation resources
    Remove-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name ServicePrincipalApplicationID -Force -ErrorAction SilentlyContinue
    New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name ServicePrincipalApplicationID -Value $Application.ApplicationId -Encrypted $false | write-verbose
    Remove-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name ServicePrincipalTenantID -Force -ErrorAction SilentlyContinue
    New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name ServicePrincipalTenantID -Value $TenantID.TenantId -Encrypted $false | write-verbose
    Remove-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name AzureRunAsCertificate -Force -ErrorAction SilentlyContinue | write-verbose
    New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose

    # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    $ConnectionAssetName = "AzureRunAsConnection"
    $SubscriptionId = $SubscriptionInfo | Select SubscriptionId -First 1
    Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
    $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId.SubscriptionId}
    New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
 ```


2. Sur votre ordinateur, démarrez **Windows PowerShell** à partir de l’écran **Démarrer** avec des droits utilisateur élevés.
3. À partir de l’interface de ligne de commande PowerShell avec élévation de privilèges, accédez au dossier contenant le script créé à l’étape 1 et exécutez le script en modifiant les valeurs des paramètres *–ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName* et *-CertPlainPassword*.<br> ```.\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -ApplicationDisplayName <DisplayNameofAutomationAccount> -CertPlainPassword "<StrongPassword>"``` <br>

    >[AZURE.NOTE] Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure. Vous *devez* ouvrir une session avec un compte associé à un administrateur de service dans l’abonnement. <br>

4. Une fois le script terminé, passez à la section suivante pour tester et vérifier la nouvelle configuration des informations d’identification.

### Vérification de l’authentification en tant que principal du service
Nous allons ensuite effectuer un test rapide afin de vérifier que vous êtes en mesure de vous authentifier à l’aide du nouveau principal du service. Si vous ne parvenez pas à vous authentifier, revenez à l’étape 1 et confirmez de nouveau chacune des étapes précédentes.

1. Dans le portail Azure, ouvrez le compte Automation que vous avez créé.  
2. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des runbooks.
3. Créez un Runbook en cliquant sur le bouton **Ajouter un Runbook**, puis sur **Créer un Runbook** dans le panneau **Ajouter un Runbook**.
4. Nommez le runbook *Test-SecPrin-Runbook* et sélectionnez PowerShell comme **Type de Runbook**. Cliquez sur **Créer** pour créer le Runbook.
5. Dans le panneau **Modifier le Runbook PowerShell**, collez le code suivant dans le canevas :<br>
       ```
       $Conn = Get-AutomationConnection -Name AzureRunAsConnection
       Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
       ```
6. Enregistrez le Runbook en cliquant sur **Enregistrer**.
7. Cliquez sur **Panneau de test** pour ouvrir le panneau **Test**.
8. Cliquez sur **Démarrer** pour démarrer le test.
9. Une [tâche de Runbook](automation-runbook-execution.md) est créée et son état apparaît dans le volet. L’état initial de la tâche est *Mis en file d’attente* pour indiquer que la tâche attend qu’un Runbook Worker du cloud devienne disponible. La tâche prend ensuite l’état *Démarrage en cours* lorsqu’un Worker sélectionne la tâche, puis l’état *En cours d’exécution* lorsque le Runbook commence à s’exécuter.  
10. Lorsque la tâche du Runbook est terminée, sa sortie s'affiche. Dans notre cas, nous devrions obtenir l’état **Terminé**.<br> ![Test de Runbook du principal de sécurité](media/automation-sec-configure-azure-runas-account/runbook-test-results.png)<br>
11. Fermez le panneau **Test** pour revenir au canevas.
12. Fermez le panneau **Modifier le Runbook PowerShell**.
13. Fermez le panneau **Test-SecPrin-Runbook**.

Le code ci-dessus vous permet de vérifier si le nouveau compte est correctement configuré. Vous allez utiliser ce code dans tous vos Runbooks PowerShell pour vous authentifier dans Azure Automation afin de gérer les ressources ARM. Bien évidemment, vous pouvez toujours vous authentifier avec le compte Automation que vous utilisez actuellement.

## Étapes suivantes
- Pour plus d’informations sur les principaux de service, voir [Objets principal du service et application](../active-directory/active-directory-application-objects.md).

<!---HONumber=AcomDC_0330_2016-->