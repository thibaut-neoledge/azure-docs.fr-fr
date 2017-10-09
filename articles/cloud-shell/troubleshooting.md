---
title: "Dépannage d’Azure Cloud Shell (version préliminaire) | Microsoft Docs"
description: "Dépannage d’Azure Cloud Shell"
services: azure
documentationcenter: 
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: damaerte
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 32d4d4d10e5d8986e2dfe94430f52db8f038e245
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="troubleshooting-azure-cloud-shell"></a>Dépannage d’Azure Cloud Shell
Les solutions connues aux problèmes d’Azure Cloud Shell sont les suivantes :

## <a name="error-400-disallowedoperation"></a>Erreur : 400 DisallowedOperation
 - **Détails** : lorsque j’utilise un abonnement Azure Active Directory, je ne peux pas créer de stockage.
 - **Résolution** : pour résoudre ce problème, utilisez un abonnement Azure habilité à créer des ressources de stockage. Les abonnements Azure AD ne peuvent pas créer de ressources Azure.

## <a name="powershell-resolutions"></a>Résolutions PowerShell

### <a name="no-home-directory-persistence"></a>Aucune persistance du répertoire $Home
  - **Détails** : les applications (telles que : git, vim, etc.) qui écrivent des données dans $Home ne seront pas conservées entre les sessions PowerShell.
  - **Résolution** : dans votre profil PowerShell, créez un lien symbolique vers le dossier spécifique de l’application spécifique dans `clouddrive` sur $Home.

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>La combinaison de touches CTRL + C ne permet pas de sortir d’une invite d’applet de commande
 - **Détails** : lorsque vous tentez de quitter une invite d’applet de commande, `Ctrl+C` ne quitte pas l’invite.
 - **Résolution** : pour quitter l’invite, appuyez sur `Ctrl+C` puis sur `Enter`.

### <a name="gui-applications-are-not-supported"></a>Les applications de l’interface graphique utilisateur ne sont pas prises en charge.
  - **Détails** : si un utilisateur lance une application d’interface graphique utilisateur, l’invite ne renvoie rien.  Par exemple, lorsqu’un utilisateur utilise `git` pour fermer un référentiel privé pour lequel l’authentification à deux facteurs est activée, une boîte de dialogue relative au code d’authentification à deux facteurs s’affiche.
  - **Résolution** : `Ctrl+C` pour quitter la commande.

### <a name="get-help--online-does-not-open-the-help-page"></a>Get-Help - online n’ouvre pas la page d’aide
 - **Détails** : si un utilisateur tape `Get-Help Find-Module -online`, un message d’erreur semblable au suivant s’affiche  :`Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
 - **Résolution** : copiez l’url et ouvrez-la manuellement sur votre navigateur.
 
### <a name="troubleshooting-remote-management-of-azure-vms"></a>Résolution des problèmes de gestion à distance des machines virtuelles Azure
 - **Détails** : en raison des paramètres de pare-feu Windows par défaut pour WinRM, l’erreur suivante peut s’afficher :`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
 - **Résolution** : assurez-vous que votre machine virtuelle est en cours d’exécution. Vous pouvez exécuter `Get-AzureRmVM -Status` pour déterminer l’état de la machine virtuelle.  Ensuite, ajoutez une nouvelle règle de pare-feu sur la machine virtuelle à distance pour autoriser les connexions WinRM à partir de n’importe quel sous-réseau, par exemple,

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 Vous pouvez utiliser [extension de script personnalisé] [customex] pour éviter la connexion à votre machine virtuelle à distance pour l’ajout de la nouvelle règle de pare-feu.
 Vous pouvez enregistrer le script ci-dessus dans un fichier, par exemple `addfirerule.ps1`, et le charger sur votre conteneur de stockage Azure.
 Essayez ensuite la commande suivante :

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```
