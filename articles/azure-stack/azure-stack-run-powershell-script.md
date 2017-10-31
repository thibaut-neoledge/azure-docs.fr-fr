---
title: "Déployer le Kit de développement Azure Stack | Microsoft Docs"
description: "Découvrez comment préparer le Kit de développement Azure Stack et exécuter le script PowerShell pour le déployer."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/17/2017
ms.author: erikje
ms.openlocfilehash: b67cabf0ecdb48f137bfcfbce95eee568a1c298d
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-the-azure-stack-development-kit"></a>Déployer le Kit de développement Azure Stack

*S’applique à : Kit de développement Azure Stack*

Pour déployer le [kit de développement Azure Stack](azure-stack-poc.md), vous devez effectuer les étapes suivantes :

1. [Téléchargez le package de déploiement](https://azure.microsoft.com/overview/azure-stack/try/?v=try) pour obtenir le fichier Cloudbuilder.vhdx.
2. [Préparez le fichier cloudbuilder.vhdx](#prepare-the-development-kit-host) en exécutant le script asdk-installer.ps1 pour configurer l’ordinateur (l’hôte du Kit de développement) sur lequel vous voulez installer le Kit de développement. Après cette étape, l’hôte du Kit de développement démarre à partir du fichier Cloudbuilder.vhdx.
3. [Déployez le Kit de développement](#deploy-the-development-kit) sur l’hôte du Kit de développement.

> [!NOTE]
> Pour un résultat optimal, même si vous voulez utiliser un environnement Azure Stack déconnecté, il est préférable de déployer en étant connecté à Internet. De cette façon, la version d’évaluation de Windows Server 2016 peut être activée au moment du déploiement.
> 
> 

## <a name="download-and-extract-the-development-kit"></a>Télécharger et extraire le Kit de développement
1. Avant de commencer le téléchargement, vérifiez que votre ordinateur répond aux prérequis suivants :

   * L’ordinateur doit avoir au moins 60 Go d’espace disque libre.
   * [.NET Framework 4.6 (ou ultérieur)](https://aka.ms/r6mkiy) doit être installé.

2. [Accédez à la page de démarrage](https://azure.microsoft.com/overview/azure-stack/try/?v=try), spécifiez vos informations, puis cliquez sur **Envoyer**.
3. Sous **Télécharger le logiciel**, cliquez sur **Kit de développement Azure Stack**.
4. Exécutez le fichier AzureStackDownloader.exe téléchargé.
5. Dans la fenêtre **Téléchargeur du Kit de développement Azure Stack**, suivez les étapes 1 à 5.
6. Une fois le téléchargement terminé, cliquez sur **Exécuter** pour lancer le programme MicrosoftAzureStackPOC.exe.
7. Passez en revue l’écran Contrat de licence et les informations de l’Assistant Auto-extracteur, puis cliquez sur **Suivant**.
8. Passez en revue l’écran Déclaration de confidentialité et les informations de l’Assistant Auto-extracteur, puis cliquez sur **Suivant**.
9. Sélectionnez la destination pour les fichiers à extraire et cliquez sur **Suivant**.
   * La destination par défaut est : <drive letter>:\<dossier actif>\Microsoft Azure Stack
10. Passez en revue l’écran Emplacement de destination et les informations de l’Assistant Auto-extracteur, puis cliquez sur **Extraire** pour extraire les fichiers CloudBuilder.vhdx (environ 25 Go) et ThirdPartyLicenses.rtf. Ce processus prend un certain temps.

> [!NOTE]
> Après avoir extrait les fichiers, vous pouvez supprimer les fichiers .exe et .bin pour récupérer de l’espace sur la machine. Vous pouvez aussi déplacer ces fichiers à un autre emplacement : ainsi, si vous devez redéployer, vous n’avez pas besoin de retélécharger les fichiers.
> 
> 

## <a name="prepare-the-development-kit-host"></a>Préparer l’hôte du Kit de développement
1. Vérifiez que vous pouvez vous connecter physiquement à l’hôte du Kit de développement ou avoir accès à une console physique (par exemple KVM). Vous devez disposer de ce type d’accès après le redémarrage de l’hôte du Kit de développement à l’étape 13 ci-dessous.
2. Vérifiez que l’hôte du Kit de développement répond à la [configuration minimale requise](azure-stack-deploy.md). Vous pouvez utiliser le [vérificateur de déploiement pour Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) afin de vérifier la configuration requise.
3. Connectez-vous en tant qu’administrateur local à votre hôte du Kit de développement.
4. Copiez ou déplacez le fichier CloudBuilder.vhdx à la racine du lecteur C:\ (C:\CloudBuilder.vhdx).
5. Exécutez le script suivant pour télécharger le fichier du programme d’installation du Kit de développement (asdk-installer.ps1) dans le dossier c:\AzureStack_Installer sur votre hôte du Kit de développement.
    ```powershell
    # Variables
    $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
    $LocalPath = 'c:\AzureStack_Installer'

    # Create folder
    New-Item $LocalPath -Type directory

    # Download file
    Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
    ```
6. Ouvrez une console PowerShell avec élévation de privilèges > Exécutez le script C:\AzureStack_Installer\asdk-installer.ps1 > Cliquez sur **Préparer environnement**.
7. Dans la page **Sélectionner le fichier vhdx Cloudbuilder** du programme d’installation, recherchez et sélectionnez le fichier cloudbuilder.vhdx que vous avez téléchargé dans les étapes précédentes.
8. Facultatif : Cochez la case **Ajouter des pilotes** pour spécifier un dossier contenant les pilotes supplémentaires que vous voulez sur l’hôte.
9. Dans la page **Paramètres facultatifs**, spécifiez un compte d’administrateur local pour l’hôte du Kit de développement. Si vous ne spécifiez pas ces informations d’identification, KVM doit accéder à l’hôte pendant le processus d’installation ci-dessous.
10. Dans la page **Paramètres facultatifs**, vous pouvez aussi définir les paramètres suivants :
    - **Nom de l’ordinateur** : cette option définit le nom de l’hôte du Kit de développement. Le nom doit respecter les spécifications des noms de domaine complets et ne pas dépasser 15 caractères. La valeur par défaut est un nom d’ordinateur aléatoire généré par Windows.
    - **Fuseau horaire** : définit le fuseau horaire pour l’hôte du Kit de développement. La valeur par défaut est (UTC-8:00) Heure du Pacifique (États-Unis et Canada).
    - **Configuration IP statique** : indique que votre déploiement doit utiliser une adresse IP statique. Dans le cas contraire, quand le programme d’installation redémarre dans cloudbuilder.vhx, les interfaces réseau sont configurées avec DHCP.
11. Cliquez sur **Suivant**.
12. Si vous avez choisi une configuration IP statique à l’étape précédente, vous devez maintenant :
    - Sélectionner une carte réseau. Vérifiez que vous pouvez vous connecter à la carte avant de cliquer sur **Suivant**.
    - Vérifiez que les valeurs pour **Adresse IP**, **Passerelle** et **DNS** sont correctes, puis cliquez sur **Suivant**.
13. Cliquez sur **Suivant** pour démarrer le processus de préparation.
14. Quand la préparation indique **Terminé**, cliquez sur **Suivant**.
15. Cliquez sur **Redémarrer maintenant** pour démarrer dans cloudbuilder.vhdx et continuer le processus de déploiement.

## <a name="deploy-the-development-kit"></a>Déployer le Kit de développement
1. Connectez-vous en tant qu’administrateur local à l’hôte du Kit de développement. Utilisez les informations d’identification spécifiées dans les étapes précédentes.

    > [!IMPORTANT]
    > Pour les déploiements Azure Active Directory, Azure Stack nécessite l’accès à Internet, directement ou via un proxy transparent. Le déploiement prend en charge une seule carte réseau pour la mise en réseau. Si vous avez plusieurs cartes réseau, vérifiez qu’une seule d’entre elles est activée (et que toutes les autres sont désactivées) avant d’exécuter le script de déploiement dans la section suivante.
    
2. Ouvrez une console PowerShell avec élévation de privilèges > Exécutez le script \AzureStack_Installer\asdk-installer.ps1 (qui peut être sur un lecteur différent dans Cloudbuilder.vhdx) > Cliquez sur **Installer**.
3. Dans la zone **Type**, sélectionnez **Cloud Azure** ou **ADFS**.
    - **Cloud Azure** : Azure Active Directory est le fournisseur d’identité. Utilisez ce paramètre pour spécifier un annuaire spécifique où le compte AAD a des autorisations d’administrateur général. Nom complet d’un locataire de répertoire AAD. Par exemple,. onmicrosoft.com. 
    - **ADFS**: le service d’annuaire de marquage par défaut est le fournisseur d’identité, le compte par défaut avec lequel se connecter est azurestackadmin@azurestack.local et le mot de passe à utiliser est celui que vous avez fourni dans le cadre de l’installation.
4. Sous **Mot de passe de l’administrateur local**, dans la zone **Mot de passe**, tapez le mot de passe de l’administrateur local (qui doit correspondre au mot de passe de l’administrateur local actuellement configuré), puis cliquez sur **Suivant**.
5. Sélectionnez une carte réseau à utiliser pour le Kit de développement, puis cliquez sur **Suivant**.
6. Sélectionnez une configuration réseau DHCP ou statique pour la machine virtuelle BGPNAT01.
    - **DHCP** (par défaut) : la machine virtuelle obtient la configuration réseau IP auprès du serveur DHCP.
    - **Statique** : utilisez cette option seulement si DHCP ne peut pas affecter une adresse IP valide pour l’accès Internet d’Azure Stack. Une adresse IP statique doit être spécifiée avec la longueur du masque de sous-réseau (par exemple 10.0.0.5/24).
7. Si vous le souhaitez, vous pouvez aussi définir les valeurs suivantes :
    - **ID de VLAN** : définit l’ID du réseau local virtuel. Utilisez cette option seulement si l’hôte et AzS-BGPNAT01 doivent configurer l’ID du réseau local virtuel pour accéder au réseau physique (et à Internet). 
    - **Redirecteur DNS** : un serveur DNS est créé dans le cadre du déploiement d’Azure Stack. Pour permettre aux ordinateurs de la solution de résoudre les noms en dehors du marquage, spécifiez le serveur DNS de votre infrastructure existante. Le serveur DNS couvert par le marquage transfère les demandes de résolution de noms inconnus à ce serveur.
    - **Serveur de temps** : ce champ requis définit le serveur de temps et doit être une adresse IP. Pour rechercher l’adresse IP d’un serveur de temps, visitez [pool.ntp.org](http:\\pool.ntp.org) ou effectuez un test ping time.windows.com. 
8. Cliquez sur **Suivant**. 
9. Dans la page **Vérification des propriétés de la carte d’interface réseau**, vous voyez une barre de progression. 
    - Si elle indique **Impossible de télécharger une mise à jour**, suivez les instructions de la page.
    - Quand elle indique **Terminé**, cliquez sur **Suivant**.
10. Dans la page **Récapitulatif**, cliquez sur **Déployer**.
11. Si vous utilisez un déploiement Azure Active Directory, vous êtes invité à entrer vos informations d’identification du compte d’administrateur général Azure Active Directory.
12. Le processus de déploiement peut prendre quelques heures, au cours desquelles le système ne redémarre automatiquement qu’une seule fois.
   
   > [!IMPORTANT]
   > Si vous voulez surveiller la progression du déploiement, connectez-vous en tant que azurestack\AzureStackAdmin. Si vous vous connectez en tant qu’administrateur local une fois que la machine est jointe au domaine, vous ne voyez pas la progression du déploiement. Ne réexécutez pas le déploiement : au lieu de cela, connectez-vous en tant que azurestack\AzureStackAdmin pour vérifier qu’il est en cours d’exécution.
   > 
   > 
   
    Quand le déploiement est terminé, la console PowerShell affiche : **TERMINÉ : Action « Déploiement »**.
   
Si le déploiement échoue, vous pouvez utiliser le script de réexécution PowerShell suivant à partir de la même fenêtre PowerShell avec élévation de privilèges :

```powershell
cd c:\CloudDeployment\Setup
.\InstallAzureStackPOC.ps1 -Rerun
```

Ce script va redémarrer le déploiement à partir de la dernière étape qui a réussi.

Vous pouvez aussi [redéployer](azure-stack-redeploy.md) à partir de zéro.


## <a name="reset-the-password-expiration-to-180-days"></a>Réinitialiser l’expiration du mot de passe à 180 jours

Pour faire en sorte que le mot de passe de l’hôte du Kit de développement n’expire pas trop tôt, suivez ces étapes après avoir déployé :

Pour modifier la stratégie d’expiration de mot de passe à partir de Powershell :
1. Dans la fenêtre Powershell, exécutez la commande ; Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local

Pour modifier la stratégie d’expiration de mot de passe manuellement :
1. Sur l’hôte du Kit de développement, ouvrez **Gestion des stratégies de groupe** et accédez à **Gestion des stratégies de groupe** – **Forêt : azurestack.local** – **Domaines** – **azurestack.local**.
2. Cliquez avec le bouton droit sur **Stratégie de domaine par défaut**, puis cliquez sur **Modifier**.
3. Dans l’Éditeur de gestion de stratégie de groupe, accédez à **Configuration de l’ordinateur** – **Stratégies** – **Paramètres Windows** – **Paramètres de sécurité**– **Stratégies de comptes** – **Stratégie de mot de passe**.
4. Dans le volet droit, double-cliquez sur **Antériorité maximale du mot de passe**.
5. Dans la boîte de dialogue **Antériorité maximale du mot de passe - Propriétés**, remplacez la valeur de **Le mot de passe expirera dans** par 180, puis cliquez sur **OK**.


## <a name="next-steps"></a>Étapes suivantes

[Installer PowerShell](azure-stack-powershell-configure-quickstart.md)

[Inscrire Azure Stack auprès de votre abonnement Azure](azure-stack-register.md)

[Se connecter à Azure Stack](azure-stack-connect-azure-stack.md)

