<properties
	pageTitle="Résolution de problèmes de connexion Bureau à distance vers une machine virtuelle Azure | Microsoft Azure"
	description="Résolvez des erreurs concernant la connexion Bureau à distance pour une machine virtuelle Windows. Accédez à une procédure de prévention rapide, à une aide par message d'erreur et à un dépannage réseau détaillé."
	keywords="erreur bureau à distance,erreur de connexion bureau à distance,impossible se connecter à la machine virtuelle,résolution des problèmes de connexion bureau à distance"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="support-article"
	ms.date="04/12/2016"
	ms.author="iainfou"/>

# Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure exécutant Windows

La connexion RDP (Remote Desktop Protocol) à votre machine virtuelle Azure Windows peut échouer pour diverses raisons. Le problème peut être lié au service Bureau à distance sur la machine virtuelle, à la connexion réseau ou encore au client Bureau à distance sur votre ordinateur hôte. Cet article vous guide à travers certaines des méthodes plus courantes pour résoudre les problèmes de connexion RDP. Vous pouvez également lire [des concepts et des étapes de résolution des problèmes RDP plus détaillés](virtual-machines-windows-detailed-troubleshoot-rdp.md) si votre problème n’est pas répertorié ici, ou si vous ne pouvez toujours pas vous connecter à votre machine virtuelle suivant le protocole RDP.

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/), puis cliquez sur **Obtenir un support**.

<a id="quickfixrdp"></a>

## Résolution des problèmes des machines virtuelles créées à l’aide du modèle de déploiement Resource Manager

Après chaque étape de résolution des problèmes, essayez de vous reconnecter à la machine virtuelle.

> [AZURE.TIP] Si le bouton Connecter du portail est grisé et si vous n’êtes pas connecté à Azure avec une connexion [Express Route](../expressroute/expressroute-introduction.md) ou [VPN de site à site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), vous devez créer votre machine virtuelle et lui attribuer une adresse IP publique pour pouvoir utiliser le protocole RDP. Pour en savoir plus sur les adresses IP publiques dans Azure, consultez [cet article](../virtual-network/virtual-network-ip-addresses-overview-arm.md).

- Réinitialisez l’accès à distance à l’aide de Powershell.
	- Si vous ne l’avez pas déjà fait, [installez et configurez la dernière version d’Azure PowerShell](../powershell-install-configure.md).

	- Réinitialisez votre connexion RDP en utilisant l’une des commandes PowerShell suivantes. Remplacez `myRG`, `myVM`, `myVMAccessExtension` et l’emplacement par des valeurs adaptées à votre installation.

	```
	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
		-Name "myVMAccessExtension" -ExtensionType "VMAccessAgent" `
		-Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" `
		-Location Westus
	```
	OU

  	```
	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" `
		-VMName "myVM" -Name "myVMAccess" -Location Westus
	```

	> [AZURE.NOTE] Dans les exemples précédents, `myVMAccessExtension` ou `MyVMAccess` est un nom que vous spécifiez pour la nouvelle extension installée dans le cadre de ce processus. Souvent, ce nom est souvent celui de la machine virtuelle. Si vous avez déjà utilisé VMAccessAgent, vous pouvez obtenir le nom de l’extension existante à l’aide de `Get-AzureRmVM -ResourceGroupName "myRG" -Name "myVM"` pour vérifier les propriétés de la machine virtuelle. Ensuite, examinez la section Extensions de la sortie. Comme une machine virtuelle ne peut avoir qu’un agent VMAccessAgent, vous devez également ajouter le paramètre `-ForceReRun` lors de l’utilisation de `Set-AzureRmVMExtension` pour forcer la réinscription de l’agent.

- Redémarrez votre machine virtuelle pour résoudre d’autres problèmes de démarrage. Sélectionnez **Parcourir** > **Machines virtuelles** > *votre machine virtuelle* > **Redémarrer**.

- [Redéployer une machine virtuelle vers un nouveau nœud Azure](virtual-machines-windows-redeploy-to-new-node.md).

	Notez qu’une fois cette opération terminée, les données de disque éphémères seront perdues et les adresses IP dynamiques associées à la machine virtuelle seront mises à jour.
	
- Vérifiez que vos [règles de groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) autorisent le trafic RDP (port TCP 3389).

- Passez en revue le journal de la console ou la capture d'écran de votre machine virtuelle pour corriger les problèmes de démarrage. Sélectionnez **Parcourir** > **Machines virtuelles** > *votre machine virtuelle Windows* > **Support + Résolution des problèmes** > **Diagnostics de démarrage**.

- [Réinitialisez le mot de passe de votre machine virtuelle](virtual-machines-windows-reset-rdp.md).

- Si vous rencontrez toujours des problèmes de protocole RDP, vous pouvez [ouvrir une demande de support](https://azure.microsoft.com/support/options/) ou lire [des concepts et des étapes de résolution des problèmes RDP plus détaillés](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## Résolution des problèmes des machines virtuelles créées à l’aide du modèle de déploiement classique

Après chaque étape de résolution des problèmes, essayez de vous reconnecter à la machine virtuelle.

- Réinitialisez le service Bureau à distance à partir du [portail Azure](https://portal.azure.com). Sélectionnez **Parcourir** > **Machines virtuelles (classiques)** > *votre machine virtuelle* > **Réinitialiser l’accès à distance**.

- Redémarrez votre machine virtuelle pour résoudre d’autres problèmes de démarrage. Sélectionnez **Parcourir** > **Machines virtuelles (classiques)** > *votre machine virtuelle* > **Redémarrer**.

- [Redéployer une machine virtuelle vers un nouveau nœud Azure](virtual-machines-windows-redeploy-to-new-node.md).

	Notez qu’une fois cette opération terminée, les données de disque éphémères seront perdues et les adresses IP dynamiques associées à la machine virtuelle seront mises à jour.
	
- Vérifiez que votre [point de terminaison Cloud Services autorise le trafic RDP](../cloud-services/cloud-services-role-enable-remote-desktop.md).

- Passez en revue le journal de la console ou la capture d'écran de votre machine virtuelle pour corriger les problèmes de démarrage. Sélectionnez **Parcourir** > **Machines virtuelles (classiques)** > *votre machine virtuelle* > **Paramètres** > **Diagnostics de démarrage**.

- Vérifiez l’intégrité des ressources de votre machine virtuelle et recherchez des problèmes de plateforme. Sélectionnez **Parcourir** > **Machines virtuelles (classiques)** > *votre machine virtuelle* > **Paramètres** > **Vérifier l’intégrité**.

- [Réinitialisez le mot de passe de votre machine virtuelle](virtual-machines-windows-reset-rdp.md).

- Si vous rencontrez toujours des problèmes de protocole RDP, vous pouvez [ouvrir une demande de support](https://azure.microsoft.com/support/options/) ou lire [des concepts et des étapes de résolution des problèmes RDP plus détaillés](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## Dépannage des erreurs spécifiques à la connexion Bureau à distance

Vous pouvez recevoir une erreur spécifique lorsque vous tentez de vous connecter à votre machine virtuelle suivant le protocole RDP. Voici les messages d’erreur les plus courants :

- [La session distante a été déconnectée, car aucun serveur de licences Bureau à distance n’est disponible pour fournir une licence](#rdplicense).

- [Le Bureau à distance ne trouve pas le « name » de l’ordinateur](#rdpname).

- [Une erreur d’authentification s’est produite. L’autorité de sécurité locale ne peut pas être contactée](#rdpauth).

- [Message d'erreur de sécurité Windows : Vos informations d'identification n'ont pas fonctionné](#wincred).

- [Cet ordinateur ne peut pas se connecter à l’ordinateur distant](#rdpconnect).

<a id="rdplicense"></a>
### La session distante a été déconnectée, car aucun serveur de licences Bureau à distance n’est disponible pour fournir une licence.

Cause : La période de grâce du Gestionnaire de licences de 120 jours pour le rôle de serveur Bureau à distance a expiré et vous devez installer les licences.

Pour contourner ce problème, enregistrez une copie locale du fichier RDP à partir du portail, puis exécutez cette commande au niveau d’une invite de commande PowerShell pour vous connecter. Cette opération ne désactive la licence que pour cette connexion :

		mstsc <File name>.RDP /admin

Si vous n’avez pas besoin de plus de deux connexions Bureau à distance simultanées à la machine virtuelle, vous pouvez utiliser le Gestionnaire de serveur pour supprimer le rôle de serveur Bureau à distance.

Pour plus d’information, consultez le billet de blog [La machine virtuelle Azure échoue et affiche le message « Aucun serveur de licences Bureau à distance n’est disponible »](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx).

<a id="rdpname"></a>
### Le bureau à distance ne trouve pas le « name » de l’ordinateur.

Cause : Le client Bureau à distance de votre ordinateur ne peut pas résoudre le nom de l’ordinateur dans les paramètres du fichier RDP.

Solutions possibles :

- Si vous êtes sur un intranet d’entreprise, vérifiez que votre ordinateur a bien accès au serveur proxy et qu’il peut lui transmettre le trafic HTTPS.

- Si vous utilisez un fichier RDP stocké localement, essayez d’utiliser celui généré par le portail. Ainsi, vous serez certain de disposer du nom DNS correct de la machine virtuelle ou du service cloud et du port du point de terminaison de la machine virtuelle. Voici un exemple de fichier RDP généré par le portail :

		full address:s:tailspin-azdatatier.cloudapp.net:55919
		prompt for credentials:i:1

La partie adresse de ce fichier RDP comporte :
- Le nom de domaine complet du service cloud qui contient la machine virtuelle ("tailspin-ici azdatatier.cloudapp.NET" dans cet exemple).

- Le port TCP externe du point de terminaison pour le trafic Bureau à distance (55919).

<a id="rdpauth"></a>
### Une erreur d’authentification s’est produite. L’autorité de sécurité locale ne peut pas être contactée.

Cause : La machine virtuelle cible n’a pas pu localiser l’autorité de sécurité dans la partie nom d’utilisateur de vos informations d’identification.

Lorsque votre nom d’utilisateur est au format *AutoritéSécurité*\\*NomUtilisateur* (par exemple, CORP\\User1), la partie *AutoritéSécurité* est soit le nom d’ordinateur de la machine virtuelle (pour l’autorité de sécurité locale), soit un nom de domaine Active Directory.

Solutions possibles :

- Si le compte est local à la machine virtuelle, vérifiez que le nom de la machine virtuelle est correctement orthographié.

- Si le compte se trouve dans un domaine Active Directory, vérifiez l’orthographe du nom de domaine.

- S’il s’agit d’un compte de domaine Active Directory et que le nom de domaine est orthographié correctement, vérifiez qu’un contrôleur de domaine est disponible dans ce domaine. Dans les réseaux virtuels Azure qui contiennent des contrôleurs de domaine, il est courant qu’un contrôleur de domaine soit indisponible, car il n’a pas démarré. Pour résoudre ce problème, utilisez un compte d’administrateur local au lieu d’un compte de domaine.

<a id="wincred"></a>
### Message d’erreur de sécurité Windows : Vos informations d’identification n’ont pas fonctionné.

Cause : La machine virtuelle cible ne peut pas valider le nom et le mot de passe de votre compte.

Un ordinateur Windows peut valider les informations d’identification d’un compte local ou d’un compte de domaine.

- Pour les comptes locaux, utilisez la syntaxe *ComputerName*\\*UserName* (par exemple : SQL1\\Admin4798).
- Pour les comptes de domaine, utilisez la syntaxe *NomDomaine*\\*NomUtilisateur* (par exemple, CONTOSO\\johndoe).

Si vous avez promu votre machine virtuelle en tant que contrôleur de domaine d’une nouvelle forêt Active Directory, le compte d’administrateur local auquel vous êtes connecté est converti en un compte équivalent avec le même mot de passe dans la nouvelle forêt et le nouveau domaine. Le compte local est alors supprimé.

Par exemple, si vous êtes connecté au compte local DC1\\DCAdmin et que vous avez promu la machine virtuelle en tant que contrôleur de domaine dans une nouvelle forêt pour le domaine corp.contoso.com, le compte local DC1\\DCAdmin est supprimé et un compte de domaine (CORP\\DCAdmin) est créé avec le même mot de passe.

Vérifiez que le nom du compte est un nom qui peut être considéré comme valide par la machine virtuelle, et que le mot de passe est correct.

Pour modifier le mot de passe du compte administrateur local, voir [Réinitialisation d’un mot de passe ou du Service Bureau à distance pour les machines virtuelles Windows](virtual-machines-windows-reset-rdp.md).

<a id="rdpconnect"></a>
### Cet ordinateur ne peut pas se connecter à l’ordinateur distant.

Cause : Le compte utilisé pour vous connecter ne dispose pas des droits de connexion au Bureau à distance.

Chaque ordinateur Windows dispose d’un groupe local d’utilisateurs du Bureau à distance, qui contient les comptes et groupes autorisés à s’y connecter à distance. Les membres du groupe local d’administrateurs y ont également accès, même si ces comptes ne sont pas répertoriés dans le groupe local des utilisateurs du Bureau à distance. Pour les ordinateurs associés à un domaine, le groupe local d’administrateurs contient également les administrateurs du domaine en question.

Vérifiez que le compte que vous utilisez pour vous connecter dispose des droits de connexion au Bureau à distance. Pour résoudre ce problème, utilisez un compte d’administrateur local ou de domaine pour vous connecter via le Bureau à distance. Puis utilisez le composant logiciel enfichable Microsoft Management Console (**Outils système > Utilisateurs et groupes locaux > Groupes > Utilisateurs du Bureau à distance**) pour ajouter le compte de votre choix au groupe local des utilisateurs du Bureau à distance.

## Résolution des erreurs génériques du Bureau à distance

Si aucune de ces erreurs ne s’est produite et que vous ne parvenez toujours pas à vous connecter à la machine virtuelle avec le Bureau à distance, consultez le [guide de résolution des problèmes du Bureau à distance](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## Ressources supplémentaires

[Package de diagnostic Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Réinitialisation d’un mot de passe ou du service Bureau à distance pour les machines virtuelles Windows](virtual-machines-windows-reset-rdp.md)

[Installation et configuration d’Azure PowerShell](../powershell-install-configure.md)

[Résolution des problèmes des connexions SSH avec une machine virtuelle Azure Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Résoudre les problèmes d’accès à une application exécutée sur une machine virtuelle Azure](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0608_2016-->