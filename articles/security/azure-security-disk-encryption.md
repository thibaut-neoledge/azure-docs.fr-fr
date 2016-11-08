---
title: Chiffrement de disque Azure pour des machines virtuelles Windows et Linux IaaS| Microsoft Docs
description: Le document offre une vue d’ensemble du chiffrement de disque Microsoft Azure pour les machines virtuelles IaaS Windows et Linux.
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh

ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2016
ms.author: krkhan

---
# Chiffrement de disque Azure pour des machines virtuelles Windows et Linux IaaS
Microsoft Azure s’engage fermement à préserver la confidentialité, la souveraineté de vos données et vous permet de contrôler vos données Azure hébergées via une suite de technologies servant à chiffrer, contrôler et gérer les clés de chiffrement, le contrôle et l’audit de l’accès aux données. Les clients Azure ont ainsi la possibilité de choisir la solution qui répond le mieux à leurs besoins professionnels. Dans ce document, nous allons vous présenter une nouvelle solution technologique « Azure Disk Encryption for Windows and Linux IaaS VM’s » pour protéger et sauvegarder vos données afin de répondre aux engagements de votre sécurité en matière d’organisation et les exigences de conformité. Cet article fournit des instructions détaillées sur la façon d’utiliser les fonctionnalités de cryptage de disque Azure, notamment sur les scénarios pris en charge et sur les expériences utilisateur.

**Remarque** : Certaines recommandations contenues dans cet article peuvent entraîner une augmentation des taux d’utilisation des données, des réseaux ou des ressources de calcul débouchant sur des coûts de licence ou abonnement supplémentaires.

## Vue d’ensemble
Azure Disk Encryption est une nouvelle fonctionnalité qui vous permet de chiffrer vos disques de machine virtuelle Windows et Linux IaaS. Azure Disk Encryption s’appuie sur la fonctionnalité standard [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) Windows et la fonctionnalité [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux pour fournir le chiffrement de volume du système d’exploitation et des disques de données. La solution est intégrée au coffre de [clés Azure](https://azure.microsoft.com/documentation/services/key-vault/) pour vous aider à contrôler et à gérer les clés de chiffrement de disque et les secrets de votre abonnement au coffre de clés, tout en vous assurant que toutes les données des disques virtuels sont chiffrées au repos dans le stockage Azure.

Azure disk encryption pour les machines virtuelles Iaas Windows et Linux est maintenant **disponible** dans toutes les régions publiques Azure pour les machines virtuelles standard et les machines virtuelles avec stockage premium.

### Scénarios de chiffrement
Azure Disk Encryption prend en charge les scénarios client suivants :

* Activation du chiffrement sur de nouvelles machines virtuelles IaaS créées à partir de disques durs virtuels déjà chiffrés et de clés de chiffrement
* Activation du chiffrement sur de nouvelles machines virtuelles IaaS créées à partir d’images de la galerie Azure
* Activation du chiffrement sur des machines virtuelles IaaS existantes et fonctionnant dans Azure
* Désactivation du chiffrement sur les machines virtuelles IaaS Windows.
* Désactivation du chiffrement sur les lecteurs de données pour les machines virtuelles IaaS Linux

La solution prend en charge les éléments de machines virtuelles IaaS suivants lorsqu’elle est activée dans Microsoft Azure :

* Prise en main d’Azure Key Vault
* Machines virtuelles de niveau standard - [Machines virtuelles Iaas des séries A, D, DS, G, GS etc.](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Activation du chiffrement pour les machines virtuelles IaaS Windows et Linux.
* Désactivation du chiffrement sur les systèmes d’exploitation et les lecteurs de données pour les machines virtuelles IaaS Windows
* Désactivation du chiffrement sur les lecteurs de données pour les machines virtuelles IaaS Linux
* Activation du chiffrement sur les machines virtuelles IaaS exécutant le système d’exploitation du client Windows.
* Activation du chiffrement sur les volumes avec chemins d’accès de montage.
* Activation du chiffrement sur les machines virtuelles Linux configurées avec le système RAID basé sur le logiciel
* Activation du chiffrement sur les machines virtuelles Windows configurées avec des espaces de stockage
* Toutes les régions publiques Azure sont prises en charge

La solution ne prend pas en charge les scénarios, fonctionnalités et technologies suivants dans la version :

* Machines virtuelles IaaS de niveau de base
* Désactivation du chiffrement sur le lecteur du système d’exploitation pour les machines virtuelles IaaS Linux
* Machines virtuelles IaaS créées en utilisant la méthode de création de machines virtuelles
* Intégration à votre service de gestion de clés local
* Windows Server 2016 Technical Preview n’est pas pris en charge dans cette version
* Azure Files (partage de fichiers Azure), NFS (Network File System), volumes dynamiques et machines virtuelles Windows configurées avec des systèmes RAID basés sur des logiciels

### Fonctionnalités de chiffrement
Lorsque vous activez et déployez Azure Disk Encryption pour les machines virtuelles IaaS d’Azure, les fonctionnalités suivantes sont activées en fonction de la configuration fournie :

* Chiffrement du volume du système d’exploitation pour protéger le volume de démarrage au repos dans le stockage client
* Chiffrement du volume de données pour protéger le volume de démarrage au repos dans le stockage client
* Désactivation du chiffrement sur les systèmes d’exploitation et les lecteurs de données pour les machines virtuelles IaaS Windows
* Désactivation du chiffrement sur les lecteurs de données pour les machines virtuelles IaaS Linux
* La sauvegarde des clés et secrets de chiffrement dans l’abonnement de coffre de clés Azure client
* Rapports d’état du chiffrement des machines virtuelles IaaS chiffrées
* Suppression des paramètres de configuration de chiffrement de disque à partir de la machine virtuelle IaaS

La solution Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux inclut l’extension de chiffrement de disque pour Windows, l’extension de chiffrement de disque pour Linux, les applets de commande CLI de chiffrement de disque et les modèles Azure Resource Manager de chiffrement de disque. Azure Disk Encryption est pris en charge sur les machines virtuelles IaaS exécutant les systèmes d’exploitation Windows ou Linux. Pour plus d’informations sur les systèmes d’exploitation pris en charge, consultez la section Conditions préalables requises ci-dessous.

**REMARQUE :** le chiffrement des disques de machine virtuelle avec Azure Disk Encryption est gratuit.

### Proposition de valeur
La solution de gestion de chiffrement de disque Azure satisfait les besoins des entreprises du cloud suivants :

* Les machines virtuelles IaaS sont sécurisées au repos par le biais d’une technologie de chiffrement standard permettant de répondre aux exigences de sécurité organisationnelle et de conformité.
* Les machines virtuelles IaaS démarrent par le biais de clés contrôlées et de politiques clients, et elles peuvent auditer leur utilisation dans le coffre de clés.

### Workflow de chiffrement
Les étapes de haut niveau nécessaires à l’activation du chiffrement de disque pour les machines virtuelles Windows et Linux sont :

1. le client choisit un scénario de chiffrement parmi les scénarios de chiffrement qui précèdent
2. Le client opte pour l’activation du chiffrement de disque via le modèle Resource Manager de chiffrement de disque Azure ou les applets de commande PS ou les commandes CLI et spécifie la configuration de chiffrement
   
   * Pour le scénario de disque dur virtuel client chiffré, le client télécharge le disque dur virtuel chiffré sur le compte de stockage et la documentation de clé de chiffrement dans le coffre de clés et fournit la configuration permettant le chiffrement sur une machine virtuelle IaaS
   * Pour le nouvel ordinateur virtuel créé à partir de la galerie Azure et l’ordinateur virtuel existant en cours d’exécution dans Azure, le client fournit la configuration de chiffrement pour activer le chiffrement sur la machine virtuelle IaaS
3. Le client accorde l’accès à la plateforme Azure pour lire la documentation relative à la clé de chiffrement (systèmes de clés de chiffrement BitLocker pour les systèmes Windows et phrase secrète pour Linux) du coffre de clés et activer le chiffrement sur la machine virtuelle IaaS
4. Le client fournit une identité d’application Azure AD pour inscrire la documentation de clé de chiffrement dans le coffre de clés et activer le chiffrement sur les machines virtuelles IaaS pour les scénarios mentionnés à la section 2 ci-dessus
5. Azure met à jour le modèle de service de machine virtuelle avec chiffrement et la configuration de coffre de clés et des machines virtuelles chiffrées pour le client

![Microsoft Antimalware dans Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### Workflow de déchiffrement
Les étapes de haut niveau nécessaires à la désactivation du chiffrement de disque pour les machines virtuelles IaaS sont les suivantes :

1. Le client choisit de désactiver le chiffrement (déchiffrement) sur une machine virtuelle IaaS en cours d’exécution dans Azure via le modèle Resource Manager Azure Disk Encryption ou via les applets de commande PS et spécifie la configuration de déchiffrement.
2. L’étape de désactivation du chiffrement désactive le chiffrement du volume de système d’exploitation ou du volume de données (ou les deux) sur la machine virtuelle IaaS Windows en cours d’exécution. Toutefois la désactivation du chiffrement du disque de système d’exploitation pour Linux n’est pas pris en charge comme indiqué dans la documentation ci-dessus. L’étape de désactivation est autorisée uniquement pour les lecteurs de données sur les machines virtuelles Linux.
3. Azure met à jour le modèle de service de machine virtuelle et la machine virtuelle Iaas est marquée comme déchiffrée. Le contenu de la machine virtuelle n’est plus chiffré au repos.
4. La désactivation du chiffrement ne supprime ni le coffre de clés ni le matériel de clé de chiffrement du client (clés de chiffrement BitLocker pour Windows ou phrase secrète pour Linux).

## Composants requis
Voici les conditions requises pour activer le chiffrement de disque de machines virtuelles IaaS Azure pour les scénarios pris en charge dans la section vue d’ensemble

* L’utilisateur doit disposer d’un abonnement Azure actif valide pour créer des ressources dans Azure dans les régions prises en charge
* Azure Disk Encryption est pris en charge sur les versions Windows Server suivantes : Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2. Windows Server 2016 Technical Preview n’est pas pris en charge dans cette version.
* Azure Disk Encryption est pris en charge sur les SKU clients Windows suivants : client Windows 8 et client Windows 10.

**Remarque** : Pour Windows Server 2008 R2, .Net Framework 4.5 DOIT être installé avant l’activation du chiffrement dans Azure. Vous pouvez l’installer à partir de Windows Update en installant la mise à jour facultative « Microsoft .NET Framework 4.5.2 pour systèmes Windows Server 2008 R2 x64 ([KB2901983](https://support.microsoft.com/kb/2901983)) »

* Azure Disk Encryption est pris en charge sur les versions serveur Linux suivantes : Ubuntu, CentOS, SUSE, SUSE Linux Enterprise Server (SLES) et Red Hat Enterprise Linux.

**Remarque** : le chiffrement du disque de système d’exploitation Linux est actuellement pris en charge sur les distributions Linux suivantes : RHEL 7.2, CentOS 7.2, Ubuntu 16.04

* Toutes les ressources (par exemple, le coffre de clés, le compte de stockage, la machine virtuelle, etc.) doivent appartenir à la même région et au même abonnement Azure.

**Remarque** : le chiffrement de disque Azure nécessite que le coffre de clés et les machines virtuelles se trouvent dans la même région Azure. Le fait de les configurer dans des régions distinctes provoque l’échec de l’activation de la fonctionnalité de chiffrement de disque Azure.

* Pour installer et configurer Azure Key Vault en vue d’utiliser le chiffrement de disque Azure, consultez la section **Définition et configuration d’Azure Key Vault pour l’utilisation de chiffrement de disque Azure.** dans la rubrique *Composants requis* de cet article.
* Pour installer et configurer l’application Azure AD dans Azure Active Directory en vue d’utiliser le chiffrement de disque Azure, consultez la section **Configurez votre application auprès d’Azure Active Directory dans Azure Active Directory** dans la rubrique *Composants requis* de cet article.
* Pour installer et configurer la stratégie d’accès au coffre de clés pour l’application Azure AD, consultez la section **Définition de la stratégie d’accès de coffre de clés pour l’application Azure AD** dans la rubrique *Composants requis* de cet article.
* Pour préparer un disque dur virtuel Windows déjà chiffré, consultez la section **Préparation d’un disque dur virtuel Windows déjà chiffré** dans l’annexe de cet article.
* Pour préparer un disque dur virtuel Linux déjà chiffré, consultez la section **Préparation d’un disque dur virtuel Linux déjà chiffré** dans l’annexe de cet article.
* La plateforme Azure doit avoir accès aux clés ou aux secrets de chiffrement dans l’Azure Key Vault du client afin de les mettre à disposition de la machine virtuelle pour lancer et déchiffrer son volume de système d’exploitation. Pour accorder à la plateforme Azure les autorisations nécessaires pour accéder au coffre de clés du client, la propriété **enabledForDiskEncryption** doit être définie sur le coffre de clés pour cette exigence. Pour plus de détails, consultez la section **Définition et configuration d’Azure Key Vault pour l’utilisation de chiffrement de disque Azure** dans l’annexe de cet article.
* Les URL de secret de coffre de clés et de clé de chiffrement à clé (KEK) doivent être des versions gérées. Azure met en vigueur cette restriction de gestion de version. Voir ci-dessous des exemples de secret valide et d’URL KEK :
  * Exemple d’URL secrète valide : *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Exemple de KEK KRK valide : *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Le chiffrement de disque Azure ne prend pas en charge les numéros de port spécifié dans le cadre du secret de coffre de clés et de secrets et des URL KEK. Voir les exemples ci-dessous pour l’URL de coffre de clés pris en charge :
  * URL de coffre de clés non acceptée *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
    * URL du coffre de clés accepté : *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Pour activer la fonction de chiffrement de disque Azure, les machines virtuelles IaaS doivent répondre aux exigences de configuration du point de terminaison de réseau suivantes :
  * La machine virtuelle IaaS doit être en mesure de se connecter au point de terminaison Active Directory Azure [Login.windows.net] pour obtenir un jeton afin de se connecter à un coffre de clé Azure
  * La machine virtuelle IaaS doit être en mesure de se connecter au point de terminaison Azure Key Vault pour écrire les clés de chiffrement dans le coffre clés client
  * La machine virtuelle IaaS doit être en mesure de se connecter au point de terminaison de stockage Azure qui héberge le référentiel d’extensions Azure et au compte de stockage Azure qui héberge les fichiers de disque dur virtuel

**Remarque :** Si votre stratégie de sécurité limite l’accès à Internet à partir des machines virtuelles Azure, vous pouvez résoudre l’URI ci-dessus auquel vous devez vous connecter, et configurer une règle spécifique pour autoriser les connexions sortantes vers les adresses IP.

* Utilisez la dernière version du kit de développement logiciel (SDK) Azure PowerShell pour configurer Azure Disk Encryption. Télécharger la dernière version [d’Azure PowerShell](https://github.com/Azure/azure-powershell/releases)

**Remarque :** Azure Disk Encryption n’est pas pris en charge dans le [Kit de développement logiciel (SDK) Azure PowerShell version 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016). Si vous recevez une erreur liée à l’utilisation d’Azure PowerShell 1.1.0, consultez l’article [Azure Disk Encryption Error Related to Azure PowerShell 1.1.0](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx) (Erreur Azure Disk Encryption liée à Azure PowerShell 1.1.0).

* Pour exécuter les commandes de l’interface de ligne de commande Azure et les associer à votre abonnement Azure, vous devez d’abord installer la version de l’interface de ligne de commande Azure :
  * Pour installer l’interface de ligne de commande Azure et l’associer à votre abonnement Azure, consultez [Installation et configuration de l’interface de ligne de commande Azure](../xplat-cli-install.md).
  * Pour plus d’informations sur l’utilisation de l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager, rendez-vous [ici](../virtual-machines/azure-cli-arm-commands.md).
* Azure Disk Encryption utilise le protecteur de clé externe BitLocker pour les machines virtuelles IaaS Windows. Si vos machines virtuelles sont jointes au domaine, ne distribuez pas les politiques de groupe qui appliquent les protecteurs de module de plateforme sécurisée. Pour plus d’informations sur la stratégie de groupe pour « Autoriser BitLocker sans module de plateforme sécurisée compatible », consultez [cet article](https://technet.microsoft.com/library/ee706521).
* Le script PowerShell prérequis pour le chiffrement de disque Azure pour créer l’application Azure AD, créer un coffre de clés ou en configurer un existant et activer le chiffrement se trouve [ici](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).

#### Configurez votre application auprès d’Azure Active Directory dans Azure Active Directory
Lorsque le chiffrement doit être activé sur une machine virtuelle en cours d’exécution dans Azure, le chiffrement des disques Azure génère et écrit les clés de chiffrement dans votre coffre de clés. La gestion des clés de chiffrement dans le coffre de clés nécessite l’authentification Azure AD.

Une application Azure AD doit donc être créée à cet effet. Vous trouverez la procédure détaillée d’inscription d’une application dans la section « Obtenir une identité d’application » dans ce [billet de blog](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Cet article contient également plusieurs exemples utiles sur l’approvisionnement et la configuration de votre coffre de clés. Pour l’authentification, il est possible d’utiliser soit l’authentification par clé secrète de client, soit l’authentification AD Azure client par certificat.

##### Authentification par clé secrète pour Azure AD
Les sections qui suivent présentent les étapes nécessaires à la configuration une authentification par clé secrète client pour Azure AD.

##### Créer une nouvelle application Azure AD à l’aide d’Azure PowerShell
Utiliser l’applet de commande PowerShell ci-dessous pour créer une nouvelle application Azure AD :

    $aadClientSecret = “yourSecret”
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

**Remarque :** $azureAdApplication.ApplicationId est l’ID de client Azure AD et $aadClientSecret est la clé secrète que vous devez utiliser ultérieurement pour activer ADE. Vous devez sauvegarder la clé secrète du client Azure AD correctement.

##### Approvisionnement de l’ID de client Azure AD et du secret à partir du portail de modèle de déploiement Azure Classic
Vous pouvez aussi configurer l’ID du client et du secret Azure AD à l’aide du portail de modèle de déploiement Azure Classic à l’adresse https://manage.windowsazure.com. Pour effectuer cette tâche, suivez les étapes ci-dessous :

1\. Cliquez sur l’onglet Active Directory comme indiqué dans la figure ci-dessous :

![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig3.png)

2\. Cliquez sur Ajouter une application et saisissez le nom de l’application, comme indiqué ci-dessous :

![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig4.png)

3\. Cliquez sur le bouton fléché et configurez les propriétés de l’application, comme indiqué ci-dessous :

![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig5.png)

4\. Cliquez sur la coche dans le coin inférieur gauche pour terminer. La page de configuration de l’application s’affiche. Notez que l’ID du Client Active Directory d’Azure se trouve au bas de la page, comme indiqué ci-dessous.

![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig6.png)

5 Enregistrer la clé secrète du client Azure AD par un clic sur le bouton Enregistrer. Cliquez sur le bouton Enregistrer et notez la clé secrète mentionnée dans la zone de texte de clé. Il s’agit de la clé secrète du client Azure AD. Vous devez sauvegarder la clé secrète du client Azure AD correctement.

![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig7.png)

**Remarque :** le flux indiqué ci-dessus n’est pas pris en charge dans le portail.

##### Utiliser une application existante
Pour exécuter les commandes ci-dessous, vous avez besoin du module Azure AD PowerShell, que vous pouvez obtenir [ici](https://technet.microsoft.com/library/jj151815.aspx).

**Remarque :** Vous devez exécuter les commandes ci-dessous à partir d’une nouvelle fenêtre PowerShell. N’utilisez PAS Azure PowerShell ou la fenêtre Azure Resource Manager pour exécuter ces commandes. La raison de cette recommandation est que ces applets de commande se trouvent dans le module MSOnline ou PowerShell Azure AD.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### Authentification par certificat pour Azure AD
> [!NOTE]
> L’authentification par certificat AAD n’est actuellement pas prise en charge sur les machines virtuelles Linux.
> 
> 

Les sections qui suivent présentent les étapes nécessaires pour configurer une authentification par certificat pour Azure AD.

##### Créer une nouvelle application Azure AD
Exécuter les applets de commande PowerShell ci-dessous pour créer une nouvelle application Azure AD :

**Remarque :** remplacez la chaîne `yourpassword` ci-dessous par votre mot de passe sécurisé et sauvegardez-le.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Une fois que vous avez terminé cette étape, téléchargez un fichier .pfx dans le coffre de clés et activez la stratégie d’accès nécessaire pour déployer ce certificat sur un ordinateur virtuel.

##### Utilisez une application Azure AD existante
Si vous configurez l’authentification par certificat pour une application existante, utilisez les applets de commande PowerShell ci-dessous. Veillez à les exécuter à partir de la fenêtre PowerShell :

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

Une fois que vous avez terminé cette étape, téléchargez un fichier .pfx dans le coffre de clés et activez la stratégie d’accès nécessaire pour déployer ce certificat sur un ordinateur virtuel.

##### Télécharger un fichier PFX au coffre de clés
Vous pouvez lire ce [billet de blog](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx) pour obtenir des explications détaillées sur ce processus. Toutefois, les applets de commande PowerShell ci-dessous sont tout ce dont vous avez besoin pour cette tâche. Veillez à les exécuter à partir de la console Azure PowerShell :

**Remarque :** remplacez la chaîne `yourpassword` ci-dessous par votre mot de passe sécurisé et sauvegardez-le.

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### Déployez un certificat dans le coffre de clés sur une machine virtuelle existante
Une fois que vous avez terminé le téléchargement du PFX, exécutez les opérations ci-dessous pour déployer un certificat dans le coffre de clés sur un ordinateur virtuel existant :

    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName


#### Définition de la stratégie d’accès de coffre de clés pour l’application Azure AD
Votre application Azure AD a besoin d’autorisations d’accès aux clés ou aux clés secrètes dans le coffre. Utilisez l’applet de commande [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607.aspx) pour accorder des autorisations à l’application, à l’aide de l’ID client (qui a été généré quand l’application a été enregistrée) comme valeur du paramètre ServicePrincipalName. Vous pouvez lire [ce billet de blog](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx) pour obtenir des exemples à ce sujet. Vous trouverez ci-dessous un exemple de la façon dont vous exécutez cette tâche via PowerShell :

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

**REMARQUE** : Azure Disk Encryption requiert de configurer les stratégies d’accès suivantes sur votre application cliente AAD : autorisations WrapKey et Set

## Terminologie
Utilisez le tableau de terminologie comme référence pour comprendre certains des termes couramment utilisés par cette technologie :

| Terminologie | Définition |
| --- | --- |
| Azure AD |Azure AD est [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Le compte Azure est requis pour l’authentification, le stockage et l’extraction des secrets du coffre de clés. |
| Azure Key Vault [AKV] |Azure Key Vault est un service de gestion de clés de chiffrement basé sur des modules de sécurité matériels FIPS pour sauvegarder vos clés de chiffrement et les secrets sensibles en toute sécurité. Pour plus de détails, consultez la documentation sur le [coffre de clés](https://azure.microsoft.com/services/key-vault/). |
| ARM |Azure Resource Manager |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) est une technologie de chiffrement de volume Windows qui permet d’activer le chiffrement de disque sur des machines virtuelles IaaS Windows. |
| BEK |Les clés de chiffrement BitLocker servent à chiffrer le volume de démarrage du système d’exploitation et les volumes de données. Les clés BitLocker sont sauvegardées dans le coffre de clés Azure du client en tant que secrets. |
| Interface de ligne de commande |[Interface de ligne de commande Azure](../xplat-cli-install.md) |
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) est le sous-système de chiffrement de disque transparent Linux utilisé pour activer le chiffrement de disque sur les machines virtuelles IaaS Linux. |
| KEK |La clé de chiffrement à clé est la clé asymétrique (RSA 2048) utilisée pour protéger ou encapsuler le secret si vous le souhaitez. Vous pouvez fournir une clé protégée par le module HSM ou la clé protégée par le logiciel. Pour plus d’informations, consultez la documentation relative à [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| Applets de commande PS |[applets de commande Azure PowerShell](../powershell-install-configure.md) |

### Définition et configuration d’Azure Key Vault pour l’utilisation de chiffrement de disque Azure.
Azure Disk Encryption protège les clés et les secrets de chiffrement dans Azure Key Vault. Suivez les étapes de chacune des sections ci-dessous pour configurer le coffres de clés pour l’utilisation du chiffrement de disque Azure.

#### Création d’un coffre de clés
Pour créer un coffre de clés, utilisez une des options répertoriées ci-dessous :

* Utilisez le modèle Resource Manager « 101-Create-KeyVault » qui se trouve [ici](https://github.com/Azure/azure-quickstart-templates/blob/master/101-create-key-vault/azuredeploy.json).
* Utilisez les [applets de commande de coffre de clés](https://msdn.microsoft.com/library/dn868052.aspx) Azure PowerShell.
* Utilisez le portail Azure Resource Manager.

**Remarque :** Si vous disposez déjà d’une configuration de coffre de clés pour votre abonnement, passez à la section suivante.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### Approvisionnement d’une clé de chiffrement par clé (facultatif)
Si vous souhaitez utiliser une clé de chiffrement de par clé (KEK) pour établir un nouveau niveau de sécurité englobant les clés de chiffrement BitLocker, vous devez ajouter une KEK à votre coffre de clés à utiliser au moment du processus de déploiement. Utilisez l’applet de commande [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868048.aspx) pour créer une clé de chiffrement de clé dans le coffre de clés. Vous pouvez également importer des clés de chiffrement par clé (KEK) à partir de votre module de sécurité matériel de gestion des clés locales. Pour plus d’informations, consultez la [Documentation relative au coffre de clés](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

La clé de chiffrement par clé peut être ajoutée à partir du portail Azure Resource Manager et à l’aide de l’expérience utilisateur d’Azure Key Vault.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### Définir des autorisations de coffre de clés pour autoriser l’accès à la plateforme Azure pour trouver les clés et les secrets
La plateforme Azure doit avoir accès aux clés et aux clés secrètes de chiffrement dans le coffre de clés Azure afin de les mettre à disposition de la machine virtuelle pour lancer et déchiffrer le volume du système d’exploitation de machine virtuelle. Pour accorder à la plateforme Microsoft Azure les autorisations nécessaires pour accéder au coffre de clés, la propriété *enabledForDiskEncryption* doit être définie sur le coffre de clés. Vous pouvez définir la propriété enabledForDiskEncryption de votre coffre de clés à l’aide de l’applet de commande PS du coffre de clés :

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

Vous pouvez également définir la propriété *enabledForDiskEncryption* en vous rendant sur https://resources.azure.com. Vous devez définir la propriété *enabledForDiskEncryption* de votre coffre de clés comme indiqué précédemment. Si ce n'est pas le cas, le déploiement échoue.

Vous pouvez configurer des stratégies d’accès pour votre application AAD à partir de l’expérience utilisateur Key Vault :

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

Assurez-vous que Key Vault est activé pour le chiffrement de disque dans « Stratégies d’accès avancées » :

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## Scénarios de déploiement de chiffrement de disque et expériences utilisateur
Il existe de nombreux scénarios permettant d’activer le chiffrement de disque et les étapes peuvent varier selon le scénario. Les sections qui suivent couvrent ces scénarios de manière plus approfondie.

### activation du chiffrement sur de nouvelles machines virtuelles IaaS créées à partir de la galerie Azure
Vous pouvez activer le chiffrement de disque sur de nouvelles machines virtuelles IaaS Windows à partir de la galerie Azure dans Azure à l’aide du modèle Resource Manager publié [ici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image). Cliquez sur le bouton « Déployer sur Azure » dans le modèle de démarrage rapide Azure, saisissez le modèle de chiffrement d’entrée dans le panneau Paramètres, puis cliquez sur OK. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement du groupe de ressources, les conditions juridiques et les accords, puis cliquez sur le bouton de création pour activer le chiffrement sur une nouvelle machine virtuelle IaaS.

**Remarque :** Ce modèle crée une machine virtuelle Windows chiffrée à l’aide de l’image de la galerie Windows Server 2012.

Le chiffrement de disque peut être activé sur une nouvelle machine virtuelle IaaS RedHat Linux 7.2 avec une baie RAID-0 de 200 Go à l’aide de [ce](https://aka.ms/fde-rhel) modèle Resource Manager. Une fois que le modèle est déployé, vérifiez l’état du chiffrement de la machine virtuelle à l’aide de l’`Get-AzureRmVmDiskEncryptionStatus`applet de commande comme décrit dans la section « [Chiffrement du lecteur du système d’exploitation sur une machine virtuelle Linux en cours d’exécution](#encrypting-os-drive-on-a-running-linux-vm) ». Lorsque la machine renvoie l’état `VMRestartPending`, redémarrez la machine virtuelle.

Vous pouvez voir les détails de paramètres de modèle Resource Manager pour le nouveau scénario de galerie Azure à l’aide de l’ID Client Azure AD dans le tableau ci-dessous :

| Paramètre | Description |
| --- | --- |
| adminUsername |Spécifiez un nom d’utilisateur pour la machine virtuelle. |
| adminPassword |Spécifiez un mot de passe utilisateur Admin pour la machine virtuelle. |
| newStorageAccountName |Nom du compte de stockage pour stocker les disques durs virtuels du système d’exploitation et des données |
| vmSize |Taille de la machine virtuelle. Actuellement, seules les séries A, D et G standard sont pris en charge |
| virtualNetworkName |Nom du réseau virtuel auquel la carte d’interface réseau de machine virtuelle va appartenir. |
| subnetName |Nom du sous-réseau du réseau virtuel auquel la carte d’interface réseau de machine virtuelle appartient. |
| AADClientID |ID de client de l’application Azure AD qui dispose des autorisations pour écrire des secrets dans le coffre de clés |
| AADClientSecret |Clé secrète client de l’application Azure AD qui dispose des autorisations nécessaires pour inscrire des secrets dans le coffre de clés |
| keyVaultURL |URL du coffre de clés dans laquelle la clé BitLocker clé doit être téléchargée. Vous pouvez l’obtenir à l’aide de l’applet de commande : (Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI |
| keyEncryptionKeyURL |URL de la clé de chiffrement à clé utilisée pour chiffrer la clé BitLocker générée. Cette étape est facultative. |
| keyVaultResourceGroup |Groupe de ressources du coffre de clés |
| vmName |Nom de la machine virtuelle sur laquelle l’opération de chiffrement doit être effectuée |

**Remarque :** KeyEncryptionKeyURL est un paramètre facultatif. Vous pouvez apporter vos propres clés de chiffrement à clé (KEK) pour renforcer votre clé de chiffrement des données (clé secrète de chiffrement) dans le coffre de clés.

### activation du chiffrement sur de nouvelles machines virtuelles IaaS créées à partir de disques durs virtuels chiffrés clients et des clés de chiffrement
Dans ce scénario, vous pouvez activer le chiffrement à l’aide du modèle Resource Manager, des applets de commande PowerShell ou des commandes d’interface CLI. Les sections ci-dessous décriront de façon plus détaillée le modèle Resource Manager et les commandes CLI.

Suivez les instructions d’une de ces sections pour la préparation d’images déjà chiffrées qui peuvent être utilisées dans Azure. Une fois l’image créée, la procédure dans la section suivante peut être utilisée pour la création d’une machine virtuelle Azure chiffrée.

* [Préparation d’un disque dur virtuel Windows déjà chiffré](#preparing-a-pre-encrypted-windows-vhd)
* [Préparation d’un disque dur virtuel Linux déjà chiffré](#preparing-a-pre-encrypted-linux-vhd)

#### Utilisation du modèle Resource Manager
Vous pouvez activer le chiffrement de disque sur le disque dur virtuel chiffré du client à l’aide du modèle Resource Manager publié [ici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm). Cliquez sur le bouton « Déployer sur Azure » dans le modèle de démarrage rapide Azure, saisissez le modèle de chiffrement d’entrée dans le panneau Paramètres, puis cliquez sur OK. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement du groupe de ressources, les conditions juridiques et les accords, et cliquez sur le bouton de création pour activer le chiffrement sur une nouvelle machine virtuelle IaaS.

Les détails des paramètres de modèle Resource Manager du scénario de disque dur virtuel chiffré client sont décrits dans le tableau ci-dessous :

| Paramètre | Description |
| --- | --- |
| newStorageAccountName |Nom du compte de stockage pour stocker le disque dur virtuel du système d’exploitation chiffré. Ce compte de stockage doit avoir déjà été créé dans le même groupe de ressources et le même emplacement que la machine virtuelle |
| osVhdUri |Disque dur virtuel de l’URI du système d’exploitation à partir du compte de stockage |
| osType |Type de produit du système d’exploitation (Windows/Linux) |
| virtualNetworkName |Nom du réseau virtuel auquel la carte d’interface réseau de machine virtuelle va appartenir. Il doit avoir déjà été créé dans le même groupe de ressources et le même emplacement que la machine virtuelle |
| subnetName |Nom du sous-réseau du réseau virtuel auquel la carte d’interface réseau de machine virtuelle appartient. |
| vmSize |Taille de la machine virtuelle. Actuellement, seules les séries A, D et G standard sont pris en charge |
| keyVaultResourceID |ResourceID identifiant la ressource de coffre de clés dans ARM. Vous pouvez l’obtenir à l’aide de l’applet de commande PowerShell : (Get-AzureRmKeyVault - VaultName &lt; yourKeyVaultName &gt; - ResourceGroupName &lt; yourResourceGroupName &gt;).ResourceId |
| keyVaultSecretUrl |​URL de la clé de chiffrement de disque configurée dans le coffre de clés |
| keyVaultKekUrl |L’URL de la clé de cryptage consiste à chiffrer la clé de chiffrement de disque généré |
| ​vmName |​Nom de la machine virtuelle IaaS |

#### Utilisation d’applets de commande PowerShell
Vous pouvez activer le chiffrement de disque sur le disque dur virtuel chiffré du client à l’aide des applets de commande PS publiées [ici](https://msdn.microsoft.com/library/azure/mt603746.aspx).

#### Utilisation des commandes CLI
Suivez les étapes ci-dessous pour activer le chiffrement de disque de ce scénario à l’aide des commandes CLI :

1. Définir des stratégies d’accès sur le coffre de clés :
   * Définir l’indicateur EnabledForDiskEncryption : `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Définir des autorisations pour une application Azure AD pour écrire des secrets dans le coffre de clés : `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]`
2. Pour activer le chiffrement sur une machine virtuelle existante ou en cours d’exécution, tapez : *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. Obtenir l’état du chiffrement : *« azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json »*
4. Pour activer le chiffrement sur une nouvelle machine virtuelle à partir de disques durs virtuels clients chiffrés, utilisez les paramètres avec la commande « azure vm create » ci-dessous :
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>

### Activer le chiffrement sur des machines virtuelles IaaS Windows existantes/en cours de fonctionnement dans Azure
Dans ce scénario, vous pouvez activer le chiffrement à l’aide du modèle Resource Manager, des applets de commande PowerShell ou des commandes CLI. Les sections ci-dessous expliquent de façon plus détaillée comment l’activer à l’aide du modèle Resource Manager et de commandes CLI.

#### Utilisation du modèle Resource Manager
Vous pouvez activer le chiffrement de disque sur une machine virtuelle Windows IaaS existante ou en cours d’utilisation à l’aide du modèle Resource Manager publié [ici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm). Cliquez sur le bouton « Déployer sur Azure » dans le modèle de démarrage rapide Azure, saisissez le modèle de chiffrement d’entrée dans le panneau Paramètres, puis cliquez sur OK. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement du groupe de ressources, les conditions juridiques et les accords, puis cliquez sur le bouton de création pour activer le chiffrement sur une machine virtuelle IaaS existante/en cours d’exécution.

Les détails de paramètres de modèle Resource Manager pour le nouveau scénario de galerie Azure à l’aide de l’ID Client Azure AD sont disponibles dans le tableau ci-dessous :

| Paramètre | Description |
| --- | --- |
| ​AADClientID |​ID de client de l’application Azure AD qui dispose des autorisations pour écrire des secrets dans le coffre de clés |
| AADClientSecret |​Clé secrète client de l’application Azure AD qui dispose des autorisations nécessaires pour inscrire des secrets dans le coffre de clés |
| keyVaultName |Nom du coffre de clés vers lequel la clé BitLocker doit être chargée. Vous pouvez l’obtenir à l’aide de l’applet de commande : (Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname |
| ​ keyEncryptionKeyURL |URL de la clé de chiffrement à clé utilisée pour chiffrer la clé BitLocker générée. Celle-ci est facultative si vous sélectionnez `nokek` dans la liste déroulante UseExistingKek. Si vous sélectionnez `kek` dans la liste déroulante UseExistingKek, vous devez entrer la valeur keyEncryptionKeyURL. |
| ​volumeType |​Type de volume sur laquelle l’opération de chiffrement est effectuée Les valeurs valides sont « Système d’exploitation », « Données », « Tous » |
| sequenceVersion |Version de séquence de l’opération BitLocker. Incrémenter ce numéro de version à chaque fois qu’une opération de chiffrement de disque est exécutée sur la même machine virtuelle |
| ​vmName |​Nom de la machine virtuelle sur laquelle l’opération de chiffrement doit être effectuée |

**Remarque :** KeyEncryptionKeyURL est un paramètre facultatif. Vous pouvez apporter vos propres clés de chiffrement à clé (KEK) (clé secrète de chiffrement BitLocker) dans le coffre de clés.

#### Utilisation d’applets de commande PowerShell
Pour plus d’informations sur la façon d’activer le chiffrement à l’aide d’Azure Disk Encryption avec des applets de commande PS, consultez la [première partie](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) et la [deuxième partie](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) du billet de blog **Explore Azure disk encryption with Azure PowerShell**.

#### Utilisation des commandes CLI
Activer le chiffrement sur des machines virtuelles IaaS Windows existantes/en cours de fonctionnement dans Azure

1. Définir des stratégies d’accès sur le coffre de clés :
   * Définir l’indicateur « EnabledForDiskEncryption » : « azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true »
   * Définir des autorisations d’application Azure AD pour écrire des clés secrètes dans KeyVault : « azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"] »
2. Pour activer le chiffrement sur une machine virtuelle existante ou en cours d’exécution, tapez : *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. Obtenir l’état du chiffrement : *« azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json »*
4. Pour activer le chiffrement sur une nouvelle machine virtuelle à partir de disques durs virtuels clients chiffrés, utilisez les paramètres avec la commande « azure vm create » ci-dessous :
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>

### Activer le chiffrement sur des machines virtuelles IaaS Linux existantes/en cours de fonctionnement dans Azure
Vous pouvez activer le chiffrement de disque sur une machine virtuelle Linux IaaS existante ou en cours d’utilisation à l’aide du modèle Resource Manager publié [ici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm). Cliquez sur le bouton « Déployer sur Azure » dans le modèle de démarrage rapide Azure, saisissez le modèle de chiffrement d’entrée dans le panneau Paramètres, puis cliquez sur OK. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement du groupe de ressources, les conditions juridiques et les accords, puis cliquez sur le bouton de création pour activer le chiffrement sur une machine virtuelle IaaS existante/en cours d’exécution.

Les détails de paramètres de modèle Resource Manager pour le nouveau scénario de galerie Azure à l’aide de l’ID Client Azure AD sont décrits dans le tableau ci-dessous :

| Paramètre | Description |
| --- | --- |
| ​AADClientID |​ID de client de l’application Azure AD qui dispose des autorisations pour écrire des secrets dans le coffre de clés |
| AADClientSecret |​Clé secrète client de l’application Azure AD qui dispose des autorisations nécessaires pour inscrire des secrets dans le coffre de clés |
| keyVaultName |Nom du coffre de clés vers lequel la clé BitLocker doit être chargée. Vous pouvez l’obtenir à l’aide de l’applet de commande : (Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname |
| ​ keyEncryptionKeyURL |URL de la clé de chiffrement à clé utilisée pour chiffrer la clé BitLocker générée. Celle-ci est facultative si vous sélectionnez « nokek » dans la liste déroulante UseExistingKek. Si vous sélectionnez « kek » dans la liste déroulante UseExistingKek, vous devez entrer la valeur keyEncryptionKeyURL. |
| ​volumeType |​Type de volume sur laquelle l’opération de chiffrement est effectuée Les valeurs valides prises en charge sont « OS »/« All » (pour RHEL 7.2, CentOS 7.2 & Ubuntu 16.04) et « Data » pour toutes les autres distributions. |
| sequenceVersion |Version de séquence de l’opération BitLocker. Incrémenter ce numéro de version à chaque fois qu’une opération de chiffrement de disque est exécutée sur la même machine virtuelle |
| ​vmName |​Nom de la machine virtuelle sur laquelle l’opération de chiffrement doit être effectuée |
| passPhrase |Tapez une phrase secrète forte en tant que clé de chiffrement de données |

**Remarque :** KeyEncryptionKeyURL est un paramètre facultatif. Vous pouvez apporter vos propres clés de chiffrement à clé (KEK) pour renforcer votre clé de chiffrement des données (clé secrète de chiffrement) dans le coffre de clés.

#### Commandes CLI
Vous pouvez activer le chiffrement de disque sur le disque dur virtuel chiffré du client à l’aide de la commande CLI installée à partir de [cet emplacement](../xplat-cli-install.md). Activer le chiffrement sur des machines virtuelles IaaS Linux existantes/en cours de fonctionnement dans Azure :

1. Définir des stratégies d’accès sur le coffre de clés :
   * Définir l’indicateur « EnabledForDiskEncryption » : « azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true »
   * Définir des autorisations d’application Azure AD pour écrire des clés secrètes dans KeyVault : « azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"] »
2. Pour activer le chiffrement sur une machine virtuelle existante ou en cours d’exécution, tapez : *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. Obtenir l’état du chiffrement : « azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json »
4. Pour activer le chiffrement sur une nouvelle machine virtuelle à partir d’un disque dur virtuel client chiffré, utilisez les paramètres avec la commande « azure vm create » ci-dessous :
   * *disk-encryption-key-vault-id <disk-encryption-key-vault-id>*
   * *disk-encryption-key-url <disk-encryption-key-url>*
   * *key-encryption-key-vault-id <key-encryption-key-vault-id>*
   * *key-encryption-key-url <key-encryption-key-url>*

### Obtenir l’état du chiffrement d’une machine virtuelle IaaS chiffrée
Vous pouvez obtenir l’état de chiffrement en utilisant le portail Azure Resource Manager, des [applets de commande PowerShell](https://msdn.microsoft.com/library/azure/mt622700.aspx) ou des commandes CLI. Les sections ci-dessous expliquent comment utiliser le portail Azure et les commandes de l’interface de ligne de commande pour obtenir l’état du chiffrement.

#### Obtenir l’état de chiffrement d’une machine virtuelle Windows chiffrée à l’aide du portail Azure Resource Manager
Vous pouvez obtenir l’état de chiffrement de la machine virtuelle IaaS à partir du portail Azure Resource Manager. Ouvrez une session sur le portail Azure à la page https://portal.azure.com/ et cliquez sur le lien Machines virtuelles dans le menu de gauche pour afficher le récapitulatif des machines virtuelles de votre abonnement. Vous pouvez filtrer la vue des machines virtuelles en sélectionnant le nom d’abonnement dans la liste déroulante d’abonnement. Cliquez sur des colonnes situées en haut du menu de page de machines virtuelles. Sélectionnez la colonne de chiffrement de disque dans le panneau de choix de colonne, et cliquez sur la mise à jour. La colonne de chiffrement de disque présente l’état de chiffrement « Activé » ou « Non activé » pour chaque machine virtuelle, comme indiqué dans la figure ci-dessous.

![Microsoft Antimalware dans Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### Obtenir l’état de chiffrement d’une machine virtuelle IaaS (Windows/Linux) chiffrée à l’aide de l’applet de commande PS de chiffrement de disque
Vous pouvez obtenir l’état de chiffrement de la machine virtuelle IaaS d’applet de commande de disque de chiffrement PS « Get-AzureRmVMDiskEncryptionStatus ». Pour obtenir les paramètres de chiffrement de votre machine virtuelle, saisissez votre session PowerShell Azure :

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

La sortie de Get-AzureRmVMDiskEncryptionStatus peut être examinée à la recherche d’URL de clés de chiffrement.

    C:\> $status = Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMNam
    e $VMName -ExtensionName $ExtensionName
    C:\> $status.OsVolumeEncryptionSettings

    DiskEncryptionKey                                                 KeyEncryptionKey                                               Enabled
    -----------------                                                 ----------------                                               -------
    Microsoft.Azure.Management.Compute.Models.KeyVaultSecretReference Microsoft.Azure.Management.Compute.Models.KeyVaultKeyReference    True


    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey.SecretUrl
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a
    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey

    SecretUrl                                                                                                               SourceVault
    ---------                                                                                                               -----------
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a Microsoft.Azure.Management....

Les valeurs des paramètres OSVolumeEncrypted et DataVolumesEncrypted sont définies sur « Encrypted », indiquant que les deux volumes sont chiffrés avec Azure Disk Encryption. Pour plus d’informations sur la façon d’activer le chiffrement à l’aide d’Azure Disk Encryption avec des applets de commande PS, consultez la [première partie](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) et la [deuxième partie](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) du billet de blog **Explore Azure disk encryption with Azure PowerShell**.

**REMARQUE** : sur les machines virtuelles Linux, l’applet de commande `Get-AzureRmVMDiskEncryptionStatus` prend 3-4 minutes pour signaler l’état de chiffrement.

#### Obtenir l’état de chiffrement de la machine virtuelle IaaS à partir de la commande CLI de chiffrement.
Vous pouvez obtenir l’état du chiffrement de la machine virtuelle IaaS à partir de la commande CLI de chiffrement de disque *azure vm show-disk-encryption-status*. Pour obtenir les paramètres de chiffrement de votre machine virtuelle, saisissez votre session CLI Azure :

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### Désactivation du chiffrement sur la machine virtuelle IaaS Windows en cours d’exécution
Vous pouvez désactiver le chiffrement sur une machine virtuelle IaaS Windows ou Linux en cours d’exécution via le modèle Resource Manager Azure Disk Encryption ou via les applets de commande PS et spécifier la configuration de déchiffrement.

##### Machine virtuelle Windows
L’étape de désactivation du chiffrement désactive le chiffrement du volume de système d’exploitation ou du volume de données (ou les deux) sur la machine virtuelle IaaS Windows en cours d’exécution. Vous ne pouvez pas désactiver le volume de système d’exploitation et laisser le volume de données chiffré. Une fois l’étape de désactivation du chiffrement effectuée,le modèle de déploiement Azure Classic met à jour le modèle de service de machine virtuelle et la machine virtuelle IaaS Windows est marquée comme déchiffrée. Le contenu de la machine virtuelle n’est plus chiffré au repos. La désactivation du chiffrement ne supprime ni le coffre de clés ni le matériel de clé de chiffrement du client (clés de chiffrement BitLocker pour Windows et phrase secrète pour Linux).

##### Machine virtuelle Linux
L’étape de désactivation du chiffrement désactive le chiffrement du volume de données sur la machine virtuelle IaaS Linux en cours d’exécution.

**REMARQUE** : la désactivation du chiffrement sur le lecteur du système d’exploitation n’est pas autorisée sur les machines virtuelles Linux.

##### Désactiver le chiffrement sur des machines virtuelles IaaS existantes/en cours d’exécution dans Azure à l’aide du modèle Resource Manager
Vous pouvez désactiver le chiffrement de disque sur la machine virtuelle IaaS Windows en cours d’exécution à l’aide du modèle Resource Manager publié [ici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm). Cliquez sur le bouton Déployer sur Azure dans le modèle de démarrage rapide Azure, saisissez la configuration de déchiffrement dans le panneau Paramètres, puis cliquez sur OK. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement du groupe de ressources, les conditions juridiques et les accords, puis cliquez sur le bouton de création pour activer le chiffrement sur une nouvelle machine virtuelle IaaS.

Pour la machine virtuelle Linux, [ce](https://aka.ms/decrypt-linuxvm) modèle peut être utilisé pour désactiver le chiffrement.

Voici les détails des paramètres du modèle Resource Manager pour la désactivation du chiffrement sur une machine virtuelle IaaS en cours d’exécution :

| ​vmName | ​Nom de la machine virtuelle sur laquelle l’opération de chiffrement doit être effectuée |
| --- | --- |
| ​volumeType |​Type de volume sur laquelle l’opération de déchiffrement est effectuée Les valeurs valides sont « Système d’exploitation », « Données » et « Tous ». **Remarque :** vous ne pouvez pas désactiver le chiffrement sur un volume de démarrage/système d’exploitation d’une machine virtuelle IaaS Windows en cours d’exécution sans désactiver le chiffrement sur le volume « Données ». **Remarque** : la désactivation du chiffrement sur le lecteur du système d’exploitation n’est pas autorisée sur les machines virtuelles Linux. |
| sequenceVersion |Version de séquence de l’opération BitLocker. Incrémenter ce numéro de version à chaque fois qu’une opération de déchiffrement de disque est exécutée sur la même machine virtuelle |

##### Désactiver le chiffrement sur des machines virtuelles IaaS existantes/en cours d’exécution dans Azure à l’aide de l’applet de commande PS
Pour désactiver à l’aide de l’applet de commande PS, l’applet de commande [Disable-AzureRmVMDiskEncryption](https://msdn.microsoft.com/library/azure/mt715776.aspx) désactive le chiffrement sur une machine virtuelle Infrastructure as a Service (IaaS). Cette applet de commande prend en charge les machines virtuelles Linux et Windows. Cette applet de commande installe une extension sur la machine virtuelle permettant de désactiver le chiffrement. Si le paramètre Name n’est pas spécifié, une extension avec le nom par défaut « AzureDiskEncryption pour les machines virtuelles Windows » est créée.

Sur les machines virtuelles Linux, l’extension «AzureDiskEncryptionForLinux » est utilisée.

**Remarque** : cette applet de commande redémarre la machine virtuelle.

## Annexe
### Connexion à votre abonnement
Passez en revue la section *Composants requis* de ce document avant de continuer. Après avoir vérifié que toutes les conditions préalables sont remplies, suivez les étapes ci-dessous pour vous connecter à votre abonnement :

1\. Démarrez une session Azure PowerShell et connectez-vous à votre compte Azure avec la commande suivante :

    Login-AzureRmAccount

2\. Si vous disposez de plusieurs abonnements et souhaitez spécifier un abonnement spécifique à utiliser, saisissez ce qui suit pour voir les abonnements de votre compte :

    Get-AzureRmSubscription

3\. Pour spécifier l’abonnement que vous souhaitez utiliser, saisissez ce qui suit :

    Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>

4\. Pour vérifier que l’abonnement configuré est correct, saisissez :

    Get-AzureRmSubscription

5\. Pour confirmer que les applets de commande de chiffrement de disque Azure sont installés, saisissez :

    Get-command *diskencryption*

6\. Vous devriez voir la sortie ci-dessous qui confirme l’installation Powershell de chiffrement de disque :

    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     

### Préparation d’un disque dur virtuel Windows déjà chiffré
Les sections qui suivent sont nécessaires à la préparation d’un disque dur virtuel Windows déjà chiffré pour le déploiement en tant que disque dur virtuel dans IaaS Azure. Les étapes sont utilisées pour préparer et démarrer une nouvelle machine virtuelle (vhd) Windows sur Hyper-V ou Azure.

#### Mettre à jour la stratégie de groupe pour permettre la protection autre que par module de plateforme sécurisée pour la protection du système d’exploitation
Vous devez configurer le paramètre de stratégie de groupe BitLocker appelé chiffrement de lecteur BitLocker, situé sous Stratégie de l’ordinateur local \\Configuration ordinateur\\Modèles d’administration\\Composants Windows. Remplacez ce paramètre par : *Lecteurs du système d’exploitation - Exiger une authentification supplémentaire au démarrage - Autoriser BitLocker sans un module de plateforme sécurisée compatible* comme indiqué dans la figure ci-dessous :

![Microsoft Antimalware dans Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### Installer les composants de fonctionnalité BitLocker
Pour Windows Server 2012, utilisez la commande ci-dessous :

    dism /online /Enable-Feature /all /FeatureName:Bitlocker /quiet /norestart

Pour Windows Server 2008 R2, utilisez la commande ci-dessous :

    ServerManagerCmd -install BitLockers

#### Préparer le volume du système d’exploitation pour BitLocker à l’aide de `bdehdcfg`
Exécutez la commande ci-dessous pour compresser la partition du système d’exploitation et de préparer la machine pour BitLocker.

    bdehdcfg -target c: shrink -quiet

#### Utilisation de BitLocker pour protéger le volume du système d’exploitation
Utilisez la commande [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) pour activer le chiffrement sur le volume de démarrage à l’aide d’un protecteur de clé externe et placer la clé externe (fichier .bek) sur le disque ou le volume externe. Le chiffrement sera activé sur le volume système, d’amorçage au prochain redémarrage.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

**Remarque :** Vous devez préparer la machine virtuelle avec un disque dur virtuel de données/de ressources distinct pour obtenir la clé externe à l’aide de BitLocker.

#### Chiffrement du lecteur du système d’exploitation sur une machine virtuelle Linux en cours d’exécution
Le chiffrement du lecteur du système d’exploitation sur une machine virtuelle Linux en cours d’exécution est pris en charge sur les distributions suivantes :

* RHEL 7.2
* CentOS 7.2
* Ubuntu 16.04

Configuration requise pour le chiffrement du lecteur du système d’exploitation :

* La machine virtuelle doit être créée à partir de l’image de la galerie Azure dans le portail Azure Resource Manager.
* Machine virtuelle Azure au moins 4 Go de RAM (la taille recommandée est de 7 Go).
* (Pour RHEL et CentOS) SELinux doit être [désactivé](https://access.redhat.com/documentation/fr-FR/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) sur la machine virtuelle. La machine virtuelle doit être redémarrée au moins une fois après la désactivation de SELinux.

##### Étapes
1\. Créez une machine virtuelle à l’aide d’une des distributions spécifiées ci-dessus.

Pour CentOS 7.2, le chiffrement du lecteur du système d’exploitation est pris en charge via une image spécifique. Pour utiliser cette image, spécifiez « 7.2n » en tant que référence SKU lors de la création de la machine virtuelle :

    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"

2\. Configurez la machine virtuelle selon vos besoins. Si vous souhaitez chiffrer tous les lecteurs (de données et du système d’exploitation) les lecteurs de données doivent être spécifiés et montables à partir de /etc/fstab.

> [!NOTE]
> Vous devez utiliser UUID =... pour définir les lecteurs de données dans/etc/fstab au lieu de spécifier le nom du périphérique de traitement par blocs, par exemple, /dev/sdb1. L’ordre des lecteurs sur la machine virtuelle est modifié au cours du chiffrement. Si votre machine virtuelle s’appuie sur un ordre spécifique de périphériques de traitement par blocs leur montage échouera après le chiffrement.
> 
> 

3\. Déconnectez-vous des sessions SSH.

4\. Pour chiffrer le système d’exploitation, définissez volumeType sur « All » ou « OS » lors de l’[activation du chiffrement](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure).

> [!NOTE]
> Tous les processus d’espace utilisateur qui ne s’exécutent pas en tant que services `systemd` doivent être arrêtés avec un `SIGKILL`. La machine virtuelle doit être redémarrée. Prévoyez un arrêt de la machine virtuelle lors de l’activation du chiffrement du lecteur du système d’exploitation sur une machine virtuelle en cours d’exécution.
> 
> 

5\. Surveillez régulièrement la progression du chiffrement à l’aide des instructions fournies dans la [section suivante](#monitoring-os-encryption-progress).

6\. Dès que Get-AzureRmVmDiskEncryptionStatus indique « VMRestartPending », redémarrez votre machine virtuelle en vous y connectant ou via le portail, PowerShell ou CLI.

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, please reboot the VM

Il est recommandé d’enregistrer le [diagnostic de démarrage](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) de la machine virtuelle *avant* le redémarrage.

#### Surveillance de la progression du chiffrement du système d’exploitation
Il existe trois façons de surveiller la progression du chiffrement du système d’exploitation.

1\. Utilisez l’applet de commande Get-AzureRmVmDiskEncryptionStatus et examinez le champ ProgressMessage :

    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started

Dès que le chiffrement du lecteur du système d’exploitation est démarré sur la machine virtuelle, 40 à 50 minutes environ seront nécessaires sur une machine virtuelle enregistrée sur un stockage Premium.

En raison de l’[erreur #388](https://github.com/Azure/WALinuxAgent/issues/388) dans WALinuxAgent, `OsVolumeEncrypted` et `DataVolumesEncrypted` apparaissent comme `Unknown` dans certaines distributions. Ce problème est résolu automatiquement dans WALinuxAgent version 2.1.5 et les versions ultérieures. Si `Unknown` s’affiche dans la sortie, vous pouvez vérifier l’état du chiffrement du disque en utilisant Azure Resource Viewer.

Accédez à [Azure Resource Viewer](https://resources.azure.com/), puis développez cette hiérarchie dans le panneau de sélection de gauche :

~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

Faites défiler InstanceView pour afficher l’état de chiffrement de vos lecteurs.

![Vue d’instance de machine virtuelle](./media/azure-security-disk-encryption/vm-instanceview.png)

2\. Recherchez les [diagnostics de démarrage](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Les messages de l’extension ADE doivent être précédés du préfixe `[AzureDiskEncryption]`.

3 Ouvrez une session sur la machine virtuelle via SSH et obtenez le journal des extensions à partir de

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

Il n’est pas recommandé de se connecter à la machine virtuelle lorsque le chiffrement du système d’exploitation est en cours. Par conséquent, les journaux doivent être copiés uniquement lorsque les deux autres méthodes ont échoué.

#### Préparation d’un disque dur virtuel Linux déjà chiffré
##### Ubuntu 16
###### Configurer le chiffrement lors de l’installation de la distribution
1\. Sélectionnez « Configure encrypted volumes » (Configurer les volumes chiffrés) lors du partitionnement des disques.

![Configuration d’Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2\. Créez un lecteur de démarrage séparé qui ne doit pas être chiffré. Chiffrez votre lecteur racine.

![Configuration d’Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3\. Indiquez une phrase secrète. Il s’agit de la phrase secrète que vous allez charger dans KeyVault.

![Configuration d’Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4\. Terminez le partitionnement.

![Configuration d’Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5\. Lors du démarrage de la machine virtuelle, vous êtes invité à entrer une phrase secrète. Utilisez la phrase secrète fournie à l’étape 3.

![Configuration d’Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6\. Préparez une machine virtuelle pour le chargement dans Azure à l’aide de [ces instructions](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). N’exécutez pas encore la dernière étape (annulation de l’approvisionnement de la machine virtuelle).

###### Configurer le chiffrement pour travailler avec Azure
1\. Créez un fichier sous /usr/local/sbin/azure\_crypt\_key.sh, avec le contenu du script ci-dessous. Prêtez une attention particulière à KeyFileName, car il s’agit du nom de fichier de phrase secrète déterminé par Azure.

    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do
        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi


2\. Modifiez la configuration du chiffrement dans */etc/crypttab*. Il doit se présenter comme suit :

    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh

3\. Si vous modifiez *azure\_crypt\_key.sh* dans Windows et que vous l’avez copié sur Linux, n’oubliez pas d’exécuter *dos2unix /usr/local/sbin/azure\_crypt\_key.sh*.

4\. Ajoutez des autorisations exécutables au script :

    chmod +x /usr/local/sbin/azure_crypt_key.sh

4\. Éditez */etc/initramfs-tools/modules* en ajoutant des lignes :

    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1

5\. Exécutez `update-initramfs -u -k all` pour mettre à jour l’initramfs afin de mettre en vigueur le `keyscript`. 6. Vous pouvez maintenant annuler l’approvisionnement de la machine virtuelle.

![Configuration d’Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

7\. Passez à l’étape suivante et [chargez votre disque dur virtuel](#upload-encrypted-vhd-to-an-azure-storage-account) dans Azure.

##### openSUSE 13.2
###### Configurer le chiffrement lors de l’installation de la distribution
1\. Sélectionnez « Encrypt Volume Group » (Chiffrer le groupe de volumes) lors du partitionnement des disques. Indiquez une phrase secrète. Il s’agit de la phrase secrète que vous allez charger dans KeyVault.

![Configuration d’openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2\. Démarrez la machine virtuelle à l’aide de votre mot phrase secrète.

![Configuration d’openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3\. Préparez la machine virtuelle au chargement dans Azure à l’aide de [ces instructions](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). N’exécutez pas encore la dernière étape (annulation de l’approvisionnement de la machine virtuelle).

###### Configurer le chiffrement pour travailler avec Azure
1\. Modifiez le fichier /etc/dracut.conf et ajoutez la ligne suivante :

    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"

2\. Mettez ces lignes en commentaire à la fin du fichier “/usr/lib/dracut/modules.d/90crypt/module-setup.sh” :

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3\. Ajoutez la ligne suivante au début du fichier « /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh »

    DRACUT_SYSTEMD=0

et remplacez toutes les occurrences de

    if [ -z "$DRACUT_SYSTEMD" ]; then

to

    if [ 1 ]; then

4 Modifiez /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh et l’ajouter après “# Open LUKS device”

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done


5\. Exécutez « / usr/sbin/dracut - f - v » pour mettre à jour initrd.

6\. Maintenant vous pouvez annuler l’approvisionnement de la machine virtuelle et [charger votre disque dur virtuel](#upload-encrypted-vhd-to-an-azure-storage-account) dans Azure.

##### CentOS 7
###### Configurer le chiffrement lors de l’installation de la distribution
1\. Sélectionnez « Encrypt my data » (Chiffrer mes données) lors du partitionnement des disques.

![Configuration de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2\. Vérifiez que « Chiffrer » est sélectionné pour la partition racine.

![Configuration de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3\. Indiquez une phrase secrète. Il s’agit de la phrase secrète que vous allez charger dans KeyVault.

![Configuration de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4\. Lors du démarrage de la machine virtuelle, vous êtes invité à entrer une phrase secrète. Utilisez la phrase secrète fournie à l’étape 3.

![Configuration de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5\. Préparez la machine virtuelle au chargement dans Azure à l’aide de [ces instructions](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). N’exécutez pas encore la dernière étape (annulation de l’approvisionnement de la machine virtuelle).

6\. Maintenant vous pouvez annuler l’approvisionnement de la machine virtuelle et [charger votre disque dur virtuel](#upload-encrypted-vhd-to-an-azure-storage-account) dans Azure.

###### Configurer le chiffrement pour travailler avec Azure
1\. Modifiez le fichier /etc/dracut.conf et ajoutez la ligne suivante :

    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"

2\. Mettez ces lignes en commentaire à la fin du fichier “/usr/lib/dracut/modules.d/90crypt/module-setup.sh” :

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3\. Ajoutez la ligne suivante au début du fichier « /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh »

    DRACUT_SYSTEMD=0

et remplacez toutes les occurrences de

    if [ -z "$DRACUT_SYSTEMD" ]; then

to

    if [ 1 ]; then

4 Modifiez /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh et l’ajouter après “# Open LUKS device”

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done


5\. Exécutez « / usr/sbin/dracut - f - v » pour mettre à jour initrd.

![Configuration de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### Télécharger des disques durs virtuels cryptés dans un compte de stockage Azure
Une fois le chiffrement BitLocker activé, le disque dur virtuel chiffré local doit être chargé vers votre compte de stockage.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### Télécharger la clé secrète de chiffrement de disque pour la machine virtuelle pré-chiffrée dans le coffre de clés
La clé secrète de chiffrement de disque obtenue précédemment doit être téléchargée en tant que clé secrète dans le coffre de clés. Les autorisations du coffre de clés doivent être activées pour votre client AAD, ainsi que le chiffrement de disque.

    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $KeyVault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### La clé secrète de chiffrement de disque non chiffré avec une clé de chiffrement à clé KEK
Utilisez [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) pour approvisionner la clé secrète dans le coffre de clés. Dans le cas d’une machine virtuelle Windows, le fichier bek est codé sous forme de chaîne en base64, puis téléchargé dans le coffre de clés en utilisant l’applet de commande Set-AzureKeyVaultSecret. Pour Linux, la phrase clé est codée sous forme de chaîne en base64, puis téléchargé dans le coffre de clés. En outre, assurez-vous que les balises suivantes sont définies lors de la création de la clé secrète dans le coffre de clés.

    # This is the passphrase that was provided for encryption during distro install
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

`$secretUrl` doit être utilisé à l’étape suivante pour [attacher le lecteur du système d’exploitation sans utiliser de clé de chiffrement à clé KEK](#without-using-a-kek).

#### Disque chiffré avec une clé secrète de chiffrement de disque à clé KEK
La clé secrète peut éventuellement être chiffrée avec une clé de chiffrement à clé avant de la télécharger dans le coffre de clés. Utilisez l’[API](https://msdn.microsoft.com/library/azure/dn878066.aspx) de retour à la ligne pour d’abord chiffrer la clé secrète à l’aide de la clé de chiffrement de clé. La sortie de cette opération de retour est une chaîne d’URL encodée en base64 qui est ensuite chargée comme clé secrète à l’aide de l’applet de commande [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx).

    # This is the passphrase that was provided for encryption during distro install
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id

`$KeyEncryptionKey` et `$secretUrl` doivent être utilisés à l’étape suivante pour [attacher le lecteur du système d’exploitation à l’aide de la clé de chiffrement à clé KEK](#using-a-kek).

### Spécifiez une URL secrète lors de l’attachement du lecteur du système d’exploitation
#### Sans utiliser de clé de chiffrement à clé KEK
Lors de l’attachement du lecteur du système d’exploitation, `$secretUrl` doit être transmis. L’URL a été générée dans la section [« Le secret de chiffrement de disque non chiffré avec une clé de chiffrement à clé KEK »](#disk-encryption-secret-not-encrypted-with-a-kek).

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### À l’aide d’une clé de chiffrement à clé KEK
Lors de l’attachement du lecteur du système d’exploitation, `$KeyEncryptionKey` et `$secretUrl` doivent être transmis. L’URL a été générée dans la section [« Le secret de chiffrement de disque chiffré avec une clé de chiffrement à clé KEK »](#disk-encryption-secret-encrypted-with-a-kek).

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id

## Télécharger ce guide
Vous pouvez télécharger ce guide à partir de la [Galerie TechNet](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).

## Pour plus d’informations
[Explorer Azure Disk Encryption avec Azure PowerShell](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)

[Explorer Azure Disk Encryption avec Azure PowerShell - partie 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)

<!---HONumber=AcomDC_0928_2016-->