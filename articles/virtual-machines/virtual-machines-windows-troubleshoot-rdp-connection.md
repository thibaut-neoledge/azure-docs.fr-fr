<properties
	pageTitle="Résolution de problèmes de connexion Bureau à distance vers une machine virtuelle Azure | Microsoft Azure"
	description="Résolvez des erreurs concernant la connexion Bureau à distance pour une machine virtuelle Windows. Accédez à une procédure de prévention rapide, à une aide par message d'erreur et à un dépannage réseau détaillé."
	keywords="erreur bureau à distance,erreur de connexion bureau à distance,impossible se connecter à la machine virtuelle,résolution des problèmes de connexion bureau à distance"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/25/2016"
	ms.author="dkshir"/>

# Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure exécutant Windows

La connexion Bureau à distance à votre machine virtuelle Azure Windows peut échouer pour diverses raisons. Le problème peut être lié au service Bureau à distance sur la machine virtuelle, à la connexion réseau ou encore au client Bureau à distance sur votre ordinateur hôte. Cet article vous aidera à en déterminer les causes et à les corriger.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Cet article s’applique aux machines virtuelles Azure exécutant Windows. Pour les machines virtuelles Azure sous Linux, voir [Résoudre les problèmes de connexion SSH à une machine virtuelle Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).

Si vous avez besoin d’aide supplémentaire concernant n’importe quel point de cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site de support Azure](https://azure.microsoft.com/support/options/), puis cliquez sur **Obtenir un support**.


<a id="quickfixrdp"></a>
## Corriger les erreurs courantes liées au Bureau à distance

Cette section répertorie les étapes à suivre pour corriger rapidement les problèmes liés à la connexion Bureau à distance.

### Machines virtuelles créées à l’aide du modèle de déploiement classique

Ces étapes peuvent résoudre la plupart des échecs de connexion Bureau à distance dans les machines virtuelles Azure créées à l’aide du modèle de déploiement classique. Après chaque étape, essayez de vous reconnecter à la machine virtuelle.

- Réinitialisez le service Bureau à distance à partir du [portail Azure](https://portal.azure.com) pour résoudre les problèmes de démarrage avec le serveur RDP.<br> Cliquez sur **Parcourir** > **Machines virtuelles (classiques)** > votre machine virtuelle Windows > **Réinitialiser l’accès à distance**.

- Redémarrez la machine virtuelle pour résoudre d'autres problèmes de démarrage.<br> Cliquez sur **Parcourir** > **Machines virtuelles (classiques)** > votre machine virtuelle Windows > **Redémarrer**.

- Redimensionnez la machine virtuelle pour résoudre les problèmes d'hôte.<br> Cliquez sur **Parcourir** > **Machines virtuelles (classiques)** > votre machine virtuelle Windows > **Paramètres** > **Redimensionner**. Pour connaître la procédure détaillée, consultez [Redimensionner la machine virtuelle](https://msdn.microsoft.com/library/dn168976.aspx).

- Passez en revue le journal de la console ou la capture d’écran de votre machine virtuelle pour corriger les problèmes de démarrage.<br> Cliquez sur **Parcourir** > **Machines virtuelles (classiques)** > votre machine virtuelle Windows > **Paramètres** > **Diagnostics de démarrage**.

- Vérifiez l’intégrité des ressources de la machine virtuelle pour les problèmes de plateforme.<br> Cliquez sur **Parcourir** > **Machines virtuelles (classiques)** > votre machine virtuelle Windows > **Paramètres** > **Vérifier l’intégrité**.

## Machines virtuelles créées à l’aide du modèle de déploiement Resource Manager

Ces étapes peuvent résoudre la plupart des échecs de connexion Bureau à distance dans les machines virtuelles Azure créées à l’aide du modèle de déploiement Resource Manager. Après chaque étape, essayez de vous reconnecter à la machine virtuelle.

- _Réinitialisez l’accès à distance_ à l’aide de Powershell<br> a. Si ce n'est pas déjà fait, [installez Azure PowerShell et connectez-vous à votre abonnement Azure](../powershell-install-configure.md) à l'aide de la méthode Azure AD. Notez que vous n’avez pas besoin de passer en mode Resource Manager dans les nouvelles versions d’Azure PowerShell 1.0.x.

	b. Réinitialisez votre connexion Bureau à distance en utilisant l’une des commandes Azure PowerShell suivantes. Remplacez `myRG`, `myVM`, `myVMAccessExtension` et l’emplacement par des valeurs adaptées à votre installation.

	```
	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccessExtension" -ExtensionType "VMAccessAgent" -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" -Location Westus
	```
	OU<br>

  ```
  Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus
  ```

- Redémarrez la machine virtuelle pour résoudre d'autres problèmes de démarrage.<br> Cliquez sur **Parcourir** > **Machines virtuelles** > votre machine virtuelle Windows > **Redémarrer**.

- Redimensionnez la machine virtuelle pour résoudre les problèmes d'hôte.<br> Cliquez sur **Parcourir** > **Machines virtuelles** > votre machine virtuelle Windows > **Paramètres** > **Taille**.

- Passez en revue le journal de la console ou la capture d’écran de votre machine virtuelle pour corriger les problèmes de démarrage.<br> Cliquez sur **Parcourir** > **Machines virtuelles** > votre machine virtuelle Windows > **Paramètres** > **Diagnostics de démarrage**.


Si les étapes ci-dessus n’ont pas permis de résoudre vos échecs de connexion Bureau à distance, passez à la section suivante.

## Dépannage des erreurs spécifiques à la connexion Bureau à distance

Voici les erreurs les plus courantes que vous pouvez rencontrer quand vous tentez d’établir une connexion Bureau à distance à votre machine virtuelle Azure :

1. [Erreur de connexion Bureau à distance : La session distante a été déconnectée, car aucun serveur de licences Bureau à distance n’est disponible pour fournir une licence](#rdplicense).

2. [Erreur de connexion Bureau à distance : Le Bureau à distance ne trouve pas le « nom » de l'ordinateur.](#rdpname)

3. [Erreur de connexion Bureau à distance : Une erreur d'authentification s'est produite. L’autorité de sécurité locale ne peut pas être contactée](#rdpauth).

4. [Message d'erreur de sécurité Windows : Vos informations d'identification n'ont pas fonctionné](#wincred).

5. [Erreur de connexion Bureau à distance : Cet ordinateur ne peut pas se connecter à l’ordinateur distant](#rdpconnect).

<a id="rdplicense"></a>
### Erreur de connexion Bureau à distance : La session distante a été déconnectée, car aucun serveur de licences Bureau à distance n’est disponible pour fournir une licence.

Cause : La période de grâce du Gestionnaire de licences de 120 jours pour le rôle de serveur Bureau à distance a expiré et vous devez installer les licences.

Pour contourner ce problème, enregistrez une copie locale du fichier RDP à partir du portail, puis exécutez cette commande au niveau d'une invite de commande Windows PowerShell pour vous connecter. Cette opération désactive uniquement la gestion des licences pour cette connexion.

		mstsc <File name>.RDP /admin

Si vous n’avez pas besoin de plus de deux connexions Bureau à distance simultanées à la machine virtuelle, vous pouvez utiliser le Gestionnaire de serveur pour supprimer le rôle de serveur Bureau à distance.

Consultez également le billet de blog [La machine virtuelle Azure échoue et affiche le message « Aucun serveur de licences Bureau à distance n’est disponible »](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx).

<a id="rdpname"></a>
### Erreur de connexion Bureau à distance : Le Bureau à distance ne trouve pas le « nom » de l’ordinateur.

Cause : Le client Bureau à distance de votre ordinateur n’a pas pu résoudre le nom de l’ordinateur dans les paramètres du fichier RDP.

Solutions possibles :

- Si vous êtes sur un intranet d’entreprise, vérifiez que votre ordinateur a bien accès au serveur proxy et qu’il peut lui transmettre le trafic HTTPS.
- Si vous utilisez un fichier RDP stocké localement, essayez d'utiliser celui généré par le portail. Ainsi, vous serez certain de disposer du nom DNS correct pour la machine virtuelle ou le service cloud et pour le port du point de terminaison de la machine virtuelle. Voici un exemple de fichier RDP généré par le portail :

		full address:s:tailspin-azdatatier.cloudapp.net:55919
		prompt for credentials:i:1

La partie adresse de ce fichier RDP comprend le nom de domaine complet du service cloud qui contient la machine virtuelle (ici, tailspin-azdatatier.cloudapp.net) et le port TCP externe du point de terminaison pour le trafic de Bureau à distance (55919).

<a id="rdpauth"></a>
### Erreur de connexion Bureau à distance : Une erreur d’authentification s’est produite. L’autorité de sécurité locale ne peut pas être contactée.

Cause : La machine virtuelle cible n'a pas pu localiser l'autorité de sécurité dans la partie nom d'utilisateur de vos informations d'identification.

Quand votre nom d’utilisateur est au format *SecurityAuthority*\\*UserName* (par exemple : CORP\\User1), la partie *SecurityAuthority* est soit le nom d’ordinateur de la machine virtuelle (pour l’autorité de sécurité locale), soit un nom de domaine Active Directory.

Solutions possibles :

- Si votre compte d'utilisateur est local à la machine virtuelle, vérifiez si le nom de la machine virtuelle est correctement orthographié.
- Si le compte figure dans un domaine Active Directory, vérifiez l’orthographe du nom de domaine.
- S’il s’agit d’un compte de domaine Active Directory et que le nom de domaine est orthographié correctement, vérifiez qu’un contrôleur de domaine est disponible dans ce domaine. Ce problème peut être courant dans un réseau virtuel Azure contenant des contrôleurs de domaine dans lequel un ordinateur contrôleur de domaine n’est pas démarré. Pour contourner ce problème, vous pouvez utiliser un compte d’administrateur local, plutôt qu’un compte de domaine.

<a id="wincred"></a>
### Message d’erreur de sécurité Windows : Vos informations d’identification n’ont pas fonctionné.

Cause : La machine virtuelle cible n’a pas pu valider votre nom de compte et votre mot de passe.

Un ordinateur Windows peut valider les informations d’identification d’un compte local ou d’un compte de domaine.

- Pour les comptes locaux, utilisez la syntaxe *ComputerName*\\*UserName* (par exemple : SQL1\\Admin4798).
- Pour les comptes de domaine, utilisez la syntaxe *DomainName*\\*UserName* (par exemple : CONTOSO\\johndoe).

Si vous avez promu votre machine virtuelle en tant que contrôleur de domaine dans une nouvelle forêt Active Directory, le compte d’administrateur local auquel vous êtes connecté est également converti en compte équivalent avec le même mot de passe dans la nouvelle forêt et le nouveau domaine. Le compte local est alors supprimé. Par exemple, si vous êtes connecté au compte local DC1\\DCAdmin et que vous avez promu la machine virtuelle en tant que contrôleur de domaine dans une nouvelle forêt pour le domaine corp.contoso.com, le compte local DC1\\DCAdmin est supprimé et un compte de domaine (CORP\\DCAdmin) est créé avec le même mot de passe.

Vérifiez que le nom du compte est un nom qui peut être considéré comme valide par la machine virtuelle, et que le mot de passe est correct.

Pour modifier le mot de passe du compte administrateur local, voir [Réinitialisation d’un mot de passe ou du Service Bureau à distance pour les machines virtuelles Windows](virtual-machines-windows-reset-rdp.md).

<a id="rdpconnect"></a>
### Erreur de connexion Bureau à distance : Cet ordinateur ne peut pas se connecter à l’ordinateur distant.

Cause : Le compte utilisé pour vous connecter ne dispose pas des droits de connexion au Bureau à distance.

Chaque ordinateur Windows dispose d’un groupe local d’utilisateurs du Bureau à distance, qui comporte les comptes et les groupes autorisés à se connecter à distance. Les membres du groupe Administrateurs local y ont également accès, même si ces comptes ne sont pas répertoriés dans le groupe local d’utilisateurs du Bureau à distance. Pour les ordinateurs associés à un domaine, le groupe Administrateurs local contient également les administrateurs de domaine du domaine en question.

Assurez-vous que le compte que vous utilisez pour vous connecter dispose des droits de connexion au Bureau à distance. Pour contourner le problème, utilisez un compte d’administrateur local ou d’administrateur de domaine pour vous connecter au Bureau à distance puis utilisez le composant logiciel enfichable Gestion de l’ordinateur (**Outils système > Utilisateurs et groupes locaux > Groupes > Utilisateurs du Bureau à distance**) pour ajouter le compte de votre choix au groupe local Utilisateurs du Bureau à distance.

## Dépannage des erreurs Bureau à distance génériques

Si aucune de ces erreurs ne s’est produite et que vous ne parvenez toujours pas à vous connecter à la machine virtuelle par le biais du Bureau à distance, consultez le [guide de dépannage détaillé du Bureau à distance](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## Ressources supplémentaires

[Package de diagnostic Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Réinitialisation d’un mot de passe ou du service Bureau à distance pour les machines virtuelles Windows](virtual-machines-windows-reset-rdp.md)

[Installation et configuration d’Azure PowerShell](../powershell-install-configure.md)

[Résolution des problèmes des connexions SSH avec une machine virtuelle Azure Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Résoudre les problèmes d’accès à une application exécutée sur une machine virtuelle Azure](virtual-machines-linux-troubleshoot-app-connection.md)

<!----HONumber=AcomDC_0323_2016-->