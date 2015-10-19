<properties
	pageTitle="Impossible de se connecter à une machine virtuelle Azure par le biais de SSH | Microsoft Azure"
	description="Résolution des problèmes des connexions SSH avec une machine virtuelle Azure exécutant Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="dkshir"/>

# Résolution des problèmes des connexions SSH avec une machine virtuelle Azure Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Cet article traite du dépannage des connexions SSH sur une machine virtuelle créée avec le modèle de déploiement classique ou le modèle de déploiement du Gestionnaire des ressources.


Les échecs de connexion SSH sur une machine virtuelle Azure Linux peuvent avoir plusieurs causes. Cet article vous aidera à les déterminer et à corriger les échecs.

> [AZURE.NOTE]Cet article s’applique uniquement aux machines virtuelles Azure exécutant Linux. Pour résoudre les problèmes de connexion à des machines virtuelles exécutant Windows, consultez [cet article](virtual-machines-troubleshoot-remote-desktop-connections.md).

## Contacter le Support technique Azure

Si vous avez besoin d’aide supplémentaire concernant n’importe quel point de cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](http://azure.microsoft.com/support/forums/).

Vous pouvez également signaler un incident au support Azure. Accédez au [site de support Azure](http://azure.microsoft.com/support/options/), puis cliquez sur **Obtenir un support**. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](http://azure.microsoft.com/support/faq/).


## Étapes de base


### Modèle de déploiement classique

Pour résoudre les échecs de connexion SSH les plus courants sur les machines virtuelles créées à l’aide du modèle de déploiement classique, essayez les étapes suivantes :

1. Réinitialisez l’accès à distance à partir du [portail Azure](https://portal.azure.com). Cliquez sur **Parcourir tout** > **Machines virtuelles (classiques)** > votre machine virtuelle Windows > **Réinitialiser l’accès à distance**.

	![Réinitialiser l'accès à distance](./media/virtual-machines-troubleshoot-ssh-connections/Portal-SSH-Reset-Windows.png)

2. Redémarrez la machine virtuelle de la même façon. À partir du [portail Azure en version préliminaire](https://portal.azure.com), cliquez sur **Parcourir tout** > **Machines virtuelles (classiques)** > votre machine virtuelle Windows > **Redémarrer**. À partir du [portail de gestion Azure](https://manage.windowsazure.com), ouvrez **Machines virtuelles** > **Instances** et cliquez sur **Redémarrer**.

3. [Redimensionnez la machine virtuelle](https://msdn.microsoft.com/library/dn168976.aspx).

4. Suivez les instructions dans [Comment réinitialiser un mot de passe ou SSH pour les machines virtuelles basées sur Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) sur la machine virtuelle, pour :

	- réinitialiser le mot de passe ou la clé SSH ;
	- créer un nouveau compte d’utilisateur sudo ;
	- réinitialiser la configuration SSH.


### Modèle de déploiement de Resource Manager

Pour résoudre les problèmes de SSH courants pour les machines virtuelles créées à l’aide du modèle de déploiement de Resource Manager, essayez les étapes suivantes.

1. Réinitialisez la connexion SSH à votre machine virtuelle Linux sur la ligne de commande. Assurez-vous que [Microsoft Azure Linux Agent](virtual-machines-linux-agent-user-guide.md) version 2.0.5 ou ultérieure est installé.

	[A] Utilisation de l’interface de ligne de commande Azure :

	Étape 1 : Si ce n’est pas déjà fait, [installez l’interface de ligne de commande Azure et connectez-vous à votre abonnement Azure](../xplat-cli-install.md) à l’aide de la `azure login` commande.

	Étape 2 : Basculez en mode Resource Manager.

		azure config mode arm

	Étape 3 : Vous pouvez réinitialiser la connexion SSH à l’aide de l’une des méthodes suivantes.

	(i) Utilisez la commande `vm reset-access`, comme dans l’exemple suivant.

		azure vm reset-access -g TestRgV2 -n TestVmV2 -r

	Cette commande installera l’extension `VMAccessForLinux` sur votre machine virtuelle.

	(ii) Sinon, vous pouvez créer un fichier nommé PrivateConf.json avec le contenu suivant :

		{
			"reset_ssh":"True"
		}

	Exécutez ensuite manuellement l’extension `VMAccessForLinux` pour réinitialiser votre connexion SSH.

		azure vm extension set "testRG" "testVM" VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json

	[B] Utilisation d’Azure PowerShell :

	Étape 1 : Si ce n’est pas déjà fait, [installez Azure PowerShell et connectez-vous à votre abonnement Azure](../powershell-install-configure.md) à l’aide de la méthode Azure AD.

	Étape 2 : Basculez en mode Resource Manager.

		Switch-AzureMode -Name AzureResourceManager

	Étape 3 : Exécutez l’extension `VMAccessForLinux` pour réinitialiser votre connexion SSH, comme dans l’exemple suivant.

		Set-AzureVMExtension -ResourceGroupName "testRG" -VMName "testVM" -Location "West US" -Name "VMAccessForLinux" -Publisher "Microsoft.OSTCExtensions" -ExtensionType "VMAccessForLinux" -TypeHandlerVersion "1.2" -SettingString "{}" -ProtectedSettingString '{"reset_ssh":true}'

2. Redémarrez votre machine virtuelle Linux à partir du portail. À partir du [portail Azure en version préliminaire](https://portal.azure.com), cliquez sur **Parcourir tout** > **Machines virtuelles** > votre machine virtuelle Windows > **Redémarrer**.

	![Redémarrer V2](./media/virtual-machines-troubleshoot-ssh-connections/Portal-SSH-Restart-V2-Windows.png)

3. Réinitialisez votre mot de passe et/ou la clé SSH pour votre machine virtuelle Linux sur la ligne de commande. Vous pouvez également créer un nouveau nom d’utilisateur/mot de passe avec autorité sudo, comme dans l’exemple suivant.

	[A] Utilisation de l’interface de ligne de commande Azure :

	Installez et configurez l’interface de ligne de commande Azure comme indiqué ci-dessus. Basculez en mode Resource Manager, puis exécutez l’extension via l’une des méthodes suivantes.

	(i) Exécutez la commande `vm reset-access` pour définir toutes les informations d’identification SSH.

		azure vm reset-access TestRgV2 TestVmV2 -u NewUser -p NewPassword

	Pour plus d’informations, tapez `azure vm reset-access -h` sur la ligne de commande.

	(ii) Sinon, vous pouvez créer un fichier nommé PrivateConf.json avec le contenu suivant.

		{
			"username":"NewUsername", "password":"NewPassword", "expiration":"2016-01-01", "ssh_key":"", "reset_ssh":false, "remove_user":""
		}

	Exécutez ensuite l’extension Linux à l’aide du fichier ci-dessus.

		$azure vm extension set "testRG" "testVM" VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json

	Remarque : vous pouvez utiliser une procédure similaire à celle décrite dans la section [Comment réinitialiser un mot de passe ou SSH pour les machines virtuelles basées sur Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) pour essayer d’autres variations. N’oubliez pas de modifier les instructions de l’interface de ligne de commande Azure pour le mode Resource Manager.

	[B] Utilisation d’Azure PowerShell :

	Installez et configurez Azure PowerShell comme indiqué ci-dessus. Basculez en mode Resource Manager, puis exécutez l’extension comme suit.µ

		$RGName = 'testRG'
		$VmName = 'testVM'
		$Location = 'West US'

		$ExtensionName = 'VMAccessForLinux'
		$Publisher = 'Microsoft.OSTCExtensions'
		$Version = '1.2'

		$PublicConf = '{}'
		$PrivateConf = '{"username":"NewUsername", "password":"NewPassword", "ssh_key":"", "reset_ssh":false, "remove_user":""}'

		Set-AzureVMExtension -ResourceGroupName $RGName -VMName $VmName -Location $Location -Name $ExtensionName -Publisher $Publisher -ExtensionType $ExtensionName -TypeHandlerVersion $Version -SettingString $PublicConf -ProtectedSettingString $PrivateConf

	Veillez à remplacer les valeurs $RGName, $VmName, $Location et les informations d’identification SSH par les valeurs spécifiques à votre installation.

## Résolution détaillée des problèmes

Si le client SSH ne peut toujours pas atteindre le service SSH sur la machine virtuelle, cela peut être dû à de nombreuses causes. Voici les composants impliqués.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot1.png)

Les sections suivantes vous aident à isoler la source du problème et à déterminer différents types de solutions.

### Étapes préalables à la résolution des problèmes

Tout d’abord, vérifiez l’état de la machine virtuelle sur le portail Azure.

Dans le [portail de gestion Azure](https://manage.windowsazure.com), pour les machines virtuelles suivant un modèle de déploiement classique :

1. Cliquez sur **Machines virtuelles** > *Nom de la machine virtuelle*.
2. Cliquez sur le **tableau de bord** de la machine virtuelle pour vérifier son état.
3. Cliquez sur **Surveiller** pour voir l’activité récente des ressources de calcul, de stockage et réseau.
4. Cliquez sur **Points de terminaison** pour vous assurer qu’il existe un point de terminaison pour le trafic SSH.

Dans le [portail Azure en version préliminaire](https://portal.azure.com) :

1. Pour une machine virtuelle créée avec un modèle de déploiement classique, cliquez sur **Parcourir** > **Machines virtuelles (classiques)** > *Nom de la machine virtuelle*. Pour une machine virtuelle créée avec Resource Manager, cliquez sur **Parcourir** > **Machines virtuelles** > *Nom de la machine virtuelle*. Le volet d’état de la machine virtuelle doit afficher **En cours d’exécution**. Faites défiler vers le bas pour voir l’activité récente des ressources de calcul, de stockage et réseau.
2. Cliquez sur **Paramètres** pour examiner les points de terminaison, les adresses IP et les autres paramètres. Pour identifier les points de terminaison sur les machines virtuelles créées avec Resource Manager, vérifiez si un [groupe de sécurité réseau](../traffic-manager/virtual-networks-nsg.md) est défini, les règles qui s’y appliquent et si elles sont référencées dans le sous-réseau.

Pour vérifier la connectivité réseau, contrôlez les points de terminaison configurés et déterminez si vous pouvez atteindre la machine virtuelle par le biais d’un autre protocole, comme HTTP ou un autre service.

Une fois ces étapes effectuées, essayez à nouveau la connexion SSH.


### Étapes de dépannage

Le client SSH sur votre ordinateur n’a peut-être pas pu atteindre le service SSH sur la machine virtuelle Azure en raison des sources suivantes de problèmes ou de configurations incorrectes :

- Ordinateur client SSH
- Appareil du périmètre de l’organisation
- point de terminaison de service cloud et liste de contrôle d’accès (ACL) ;
- Groupes de sécurité réseau
- Machine virtuelle Azure Linux

#### Source 1 : ordinateur client SSH

Pour vous assurer que votre ordinateur n’est pas la source de l’échec, vérifiez qu’il peut établir des connexions SSH avec un autre ordinateur Linux local.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot2.png)

Si cette opération échoue, recherchez sur votre ordinateur :

- un paramètre de pare-feu local qui bloque le trafic SSH entrant ou sortant (TCP 22) ;
- un logiciel de proxy client installé localement qui empêche les connexions SSH ;
- un logiciel de surveillance réseau installé localement qui empêche les connexions SSH ;
- d’autres types de logiciels de sécurité qui surveillent le trafic ou autorisent/interdisent des types spécifiques de trafic.

Dans tous les cas, désactivez temporairement le logiciel concerné et essayez d’établir une connexion SSH avec un ordinateur local pour déterminer la cause. Contactez ensuite votre administrateur réseau pour corriger les paramètres du logiciel pour autoriser les connexions SSH.

Si vous utilisez l’authentification par certificat, vérifiez que vous avez ces autorisations sur le dossier .ssh dans votre répertoire de base :

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id\_rsa (ou tout autre fichier où vous avez stocké vos clés privées)
- Chmod 644 ~/.ssh/known\_hosts (contient les hôtes auxquels vous vous êtes connecté via SSH)

#### Source 2 : appareil du périmètre de l’organisation

Pour vous assurer que votre appareil du périmètre de l’organisation n’est pas la source de l’échec, vérifiez qu’un ordinateur directement connecté à Internet peut établir des connexions SSH avec votre machine virtuelle Azure. Si vous accédez à la machine virtuelle par le biais d’une connexion VPN ou ExpressRoute de site à site, passez à [Source 4 : groupes de sécurité réseau](#nsg).

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot3.png)

Si vous n’avez pas d’ordinateur directement connecté à Internet, vous pouvez facilement créer une machine virtuelle Azure dans son propre groupe de ressources ou service cloud et l’utiliser. Pour plus d’informations, consultez [Créer une machine virtuelle exécutant Linux dans Azure](virtual-machines-linux-tutorial.md). Une fois le test terminé, supprimez le groupe de ressources ou la machine virtuelle et le service cloud.

Si vous pouvez créer une connexion SSH avec un ordinateur directement connecté à Internet, recherchez les éléments suivants sur votre appareil du périmètre de l’organisation :

- un pare-feu interne qui bloque le trafic SSH avec Internet ;
- un serveur proxy qui empêche les connexions SSH ;
- un logiciel de détection d’intrusion ou de surveillance réseau s’exécutant sur les appareils de votre réseau du périmètre et qui empêche les connexions SSH.

Contactez votre administrateur réseau pour corriger les paramètres de vos appareils du périmètre de l’organisation pour permettre le trafic SSH avec Internet.

#### Source 3 : Point de terminaison de service cloud et liste de contrôle d’accès

> [AZURE.NOTE]Cette source s’applique uniquement aux machines virtuelles créées à l’aide du modèle de déploiement classique. Pour les machines virtuelles créées à l’aide de Resource Manager, passez à [Source 4 : groupes de sécurité réseau](#nsg).

Pour éliminer le point de terminaison de service cloud et la liste de contrôle d’accès comme source de l’échec, pour les machines virtuelles créées à l’aide du [modèle de déploiement classique](../resource-manager-deployment-model.md), vérifiez qu’une autre machine virtuelle Azure dans le même réseau virtuel peut établir des connexions SSH avec votre machine virtuelle.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot4.png)

Si vous ne disposez pas d’une autre machine virtuelle dans le même réseau virtuel, vous pouvez facilement en créer une. Pour plus d’informations, consultez [Créer une machine virtuelle exécutant Linux dans Azure](virtual-machines-linux-tutorial.md). Une fois le test terminé, supprimez la machine virtuelle supplémentaire.

Si vous pouvez créer une connexion SSH avec une machine virtuelle dans le même réseau virtuel, vérifiez :

- la configuration du point de terminaison pour le trafic SSH sur la machine virtuelle cible ; le port TCP privé du point de terminaison doit correspondre au port TCP sur lequel le service SSH de la machine virtuelle est à l’écoute (par défaut, il s’agit du port 22). Pour les machines virtuelles créées à l’aide de modèles dans le modèle de déploiement Resource Manager, vérifiez le numéro du port TCP de SSH dans le portail Azure en version préliminaire en accédant à **Parcourir** > **Machines virtuelles (v2)** > *Nom de la machine virtuelle* > **Paramètres** > **Points de terminaison**.
- La liste de contrôle d’accès du point de terminaison du trafic SSH sur la machine virtuelle cible. Les listes de contrôle d’accès vous permettent de spécifier le trafic Internet entrant autorisé ou interdit en fonction de l’adresse IP source. Une mauvaise configuration des listes de contrôle d’accès peut empêcher le trafic SSH entrant d’accéder au point de terminaison. Vérifiez vos listes de contrôle d’accès pour vous assurer que le trafic entrant provenant des adresses IP publiques de votre proxy ou d’un autre serveur Edge est autorisé. Pour plus d’informations, consultez [À propos des listes de contrôle d’accès (ACL) réseau](../virtual-network/virtual-networks-acl.md).

Pour vérifier que le point de terminaison n’est pas la source du problème, supprimez le point de terminaison actuel, créez un autre point de terminaison et spécifiez le nom **SSH** (port TCP 22 pour le numéro du port public et privé). Pour plus d’informations, consultez [Configuration des points de terminaison sur une machine virtuelle dans Azure](virtual-machines-set-up-endpoints.md).

<a id="nsg"></a>
#### Source 4 : groupes de sécurité réseau

Les groupes de sécurité réseau vous permettent de mieux contrôler le trafic entrant et sortant autorisé. Vous pouvez créer des règles qui s’étendent aux sous-réseaux et aux services cloud d’un réseau virtuel Azure. Vérifiez les règles de votre groupe de sécurité réseau pour vous assurer que le trafic SSH vers et depuis Internet est autorisé. Pour plus d’informations, consultez [À propos des groupes de sécurité réseau](../traffic-manager/virtual-networks-nsg.md).

#### Source 5 : machine virtuelle Azure Linux

La dernière source des problèmes possibles est la machine virtuelle Azure elle-même.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot5.png)

Si vous ne l’avez pas déjà fait, suivez les instructions permettant de [réinitialiser un mot de passe ou SSH pour les machines virtuelles basées sur Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) sur la machine virtuelle.

Essayez une nouvelle fois de vous connecter à partir de votre ordinateur. Si cette opération échoue, l’une des raisons suivantes peut en être la cause :

- Le service SSH n’est pas en cours d’exécution sur la machine virtuelle cible.
- Le service SSH n’est pas à l’écoute sur le port TCP 22. Pour tester cela, installez un client telnet sur votre ordinateur local et exécutez « telnet *NomServiceCloud*.cloudapp.net 22 ». Cette opération permet de déterminer si la machine virtuelle autorise les communications entrantes et sortantes avec le point de terminaison SSH.
- Le pare-feu local sur la machine virtuelle cible a des règles qui empêchent le trafic SSH entrant ou sortant.
- Un logiciel de détection d’intrusion ou de surveillance réseau s’exécutant sur la machine virtuelle Azure empêche les connexions SSH.


## Ressources supplémentaires

Pour les machines virtuelles suivant un modèle de déploiement classique, suivez les instructions de la section [Comment réinitialiser un mot de passe ou SSH pour les machines virtuelles basées sur Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md).

[Résolution des problèmes de connexion du Bureau à distance Windows avec une machine virtuelle Azure Windows](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Résolution des problèmes d’accès à une application exécutée sur une machine virtuelle Azure](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=Oct15_HO2-->