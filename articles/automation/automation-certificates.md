---
title: Ressources de certificats dans Azure Automation | Microsoft Docs
description: "Les certificats peuvent être stockés en toute sécurité dans Azure Automation afin d’y accéder par des Runbooks ou configurations DSC pour s’authentifier auprès des ressources Azure et tierces.  Cet article présente les certificats et leur utilisation dans la création textuelle et graphique."
services: automation
documentationcenter: 
author: mgoedtel
manager: stevenka
editor: tysonn
ms.assetid: ac9c22ae-501f-42b9-9543-ac841cf2cc36
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 1973a3523e121414dfbebf4d00cd2d4fe2005d2f


---
# <a name="certificate-assets-in-azure-automation"></a>Ressources de certificats dans Azure Automation
Les certificats peuvent être stockés en toute sécurité dans Azure Automation afin d’être accessibles par les Runbooks ou configurations DSC à l’aide de l’activité **Get-AutomationCertificate** . Cette méthode vous permet de créer des Runbooks et des configurations DSC qui utilisent des certificats pour l’authentification ou les ajoute aux ressources Azure ou tierces.

> [!NOTE]
> Les ressources sécurisées dans Azure Automation incluent les informations d'identification, les certificats, les connexions et les variables chiffrées. Ces ressources sont chiffrées et stockées dans Azure Automation à l'aide d'une clé unique, générée pour chaque compte Automation. Cette clé est chiffrée par un certificat principal et stockée dans Azure Automation. Avant de stocker une ressource sécurisée, la clé pour le compte Automation est déchiffrée à l’aide du certificat principal, puis utilisée pour chiffrer la ressource.
> 
> 

## <a name="windows-powershell-cmdlets"></a>Applets de commande Windows PowerShell
Les applets de commande du tableau suivant sont utilisées pour créer et gérer les ressources de certificats Automation avec Windows PowerShell. Elles sont fournies dans le cadre du [module Azure PowerShell](../powershell-install-configure.md) , utilisable dans les Runbooks Automation et les configurations DSC.

| Applets de commande | Description |
|:--- |:--- |
| [Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx) |Récupère des informations sur un certificat. Vous pouvez uniquement récupérer le certificat lui-même à partir de l’activité Get-AutomationCertificate. |
| [New- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913764.aspx) |Importe un nouveau certificat dans Azure Automation. |
| [Remove- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913773.aspx) |Supprime un certificat dans Azure Automation. |
| [Set- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913763.aspx) |Définit les propriétés d’un certificat existant, y compris le téléchargement du fichier de certificat et le mot de passe d’un fichier .pfx. |

## <a name="activities-to-access-certificates"></a>Activités pour accéder aux certificats
Les activités du tableau suivant sont utilisées pour accéder aux certificats dans un Runbook ou dans une configuration DSC.

| Activités | Description |
|:--- |:--- |
| Get-AutomationCertificate |Obtient un certificat à utiliser dans un Runbook ou dans une configuration DSC. |

> [!NOTE]
> Évitez d’utiliser des variables dans le paramètre –Name de GetAutomationCertificate, car cela complique la découverte des dépendances entre les configurations de Runbooks ou DSC et les ressources de certificats au moment de la conception.
> 
> 

## <a name="creating-a-new-certificate"></a>Création d’un certificat
Lorsque vous créez un certificat, vous téléchargez un fichier .cer ou .pfx dans Azure Automation. Si vous marquez le certificat comme exportable, vous pouvez également le transférer du magasin de certificats Azure Automation. S’il n’est pas exportable, il peut uniquement être utilisé pour la signature dans le Runbook ou la configuration DSC.

### <a name="to-create-a-new-certificate-with-the-azure-classic-portal"></a>Pour créer un certificat avec le portail Azure Classic
1. À partir de votre compte Automation, cliquez sur **Ressources** en haut de la fenêtre.
2. En bas de la fenêtre, cliquez sur **Ajouter un paramètre**.
3. Cliquez sur **Ajouter les informations d’identification**.
4. Dans la liste déroulante **Type d’informations d’identification**, sélectionnez **Certificat**.
5. Tapez un nom pour le certificat dans la zone **Nom** , puis cliquez sur la flèche droite.
6. Recherchez un fichier .cer ou .pfx.  Si vous sélectionnez un fichier .pfx, spécifiez un mot de passe et s’il est autorisé à être exporté.
7. Cliquez sur la coche pour télécharger le fichier de certificat et enregistrer la nouvelle ressource de certificat.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Pour créer un certificat avec le portail Azure
1. À partir de votre compte Automation, cliquez sur la partie **Ressources** pour ouvrir le panneau **Ressources**.
2. Cliquez sur la partie **Certificats** pour ouvrir le panneau **Certificats**.
3. Cliquez sur **Ajouter un certificat** en haut du panneau.
4. Tapez un nom pour le certificat dans la zone **Nom** .
5. Cliquez sur **Sélectionner un fichier** sous **Charger un fichier de certificat** pour rechercher un fichier .cer ou .pfx.  Si vous sélectionnez un fichier .pfx, spécifiez un mot de passe et s’il est autorisé à être exporté.
6. Cliquez sur **Créer** pour enregistrer la nouvelle ressource de certificat.

### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Pour créer un certificat avec Windows PowerShell
Les exemples de commandes suivants montrent comment créer un certificat Automation et le marquer comme exportable. Cette opération importe un fichier pfx existant.

    $certName = 'MyCertificate'
    $certPath = '.\MyCert.pfx'
    $certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force

    New-AzureAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable

## <a name="using-a-certificate"></a>Utilisation d’un certificat
Vous devez utiliser l’activité **Get-AutomationCertificate** pour utiliser un certificat. Vous ne pouvez pas utiliser l’applet de commande [Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx) dans la mesure où elle retourne des informations sur la ressource de certificat, mais pas le certificat lui-même.

### <a name="textual-runbook-sample"></a>Exemple de Runbook textuel
L’exemple de code suivant montre comment ajouter un certificat à un service cloud dans un Runbook. Dans cet exemple, le mot de passe est récupéré à partir d’une variable Automation chiffrée.

    $serviceName = 'MyCloudService'
    $cert = Get-AutomationCertificate -Name 'MyCertificate'
    $certPwd = Get-AutomationVariable –Name 'MyCertPassword'
    Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### <a name="graphical-runbook-sample"></a>Exemple de Runbook graphique
Pour ajouter une commande **Get-AutomationCerticificate** à un Runbook graphique, vous devez cliquer avec le bouton droit sur le certificat dans le volet Bibliothèque de l’éditeur graphique et sélectionner l’option **Ajouter à la zone de dessin**.

![](media/automation-certificates/certificate-add-canvas.png)

L’image suivante montre un exemple d’utilisation d’un certificat dans un Runbook graphique.  Il s’agit du même exemple que celui affiché plus haut pour ajouter un certificat à un service cloud à partir d’un Runbook textuel.  

Cet exemple utilise le paramètre **UseConnectionObject** défini pour l’activité **Send-TwilioSMS**, qui utilise un objet de connexion pour l’authentification auprès du service.  Un [lien pipeline](automation-graphical-authoring-intro.md#links-and-workflow) doit être utilisé ici, car un lien de séquence retournera une collection contenant un unique objet au sein d’un paramètre que le paramètre Connexion n’attend pas.

![](media/automation-certificates/add-certificate.png)

## <a name="see-also"></a>Voir aussi
* [Liens de création graphique](automation-graphical-authoring-intro.md#links-and-workflow) 




<!--HONumber=Nov16_HO3-->


