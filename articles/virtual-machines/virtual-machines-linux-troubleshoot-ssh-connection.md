<properties
	pageTitle="Résoudre les problèmes de connexion SSH à une machine virtuelle Azure | Microsoft Azure"
	description="Dépannez et résolvez les erreurs SSH comme l’échec de connexion SSH ou le refus de connexion SSH pour une machine virtuelle Azure exécutant Linux."
	keywords="refus de la connexion ssh,erreur ssh,ssh azure,échec de la connexion ssh"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="dkshir"/>

# Résolution des problèmes des connexions SSH avec une machine virtuelle Azure Linux

Les erreurs SSH lors de la tentative de connexion à une machine virtuelle Azure Linux peuvent avoir plusieurs causes. Cet article vous aidera à les déterminer et à les corriger.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Cet article s’applique uniquement aux machines virtuelles Azure exécutant Linux. Pour les machines virtuelles Azure exécutant Windows, voir [Résolution des problèmes de connexion Bureau à distance vers une machine virtuelle Azure](virtual-machines-windows-troubleshoot-rdp-connection.md).

Si vous avez besoin d’aide supplémentaire concernant n’importe quel point de cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](http://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site de support Azure](http://azure.microsoft.com/support/options/), puis cliquez sur **Obtenir un support**. Pour plus d’informations sur l’utilisation du support Azure, lisez la [FAQ du support Microsoft Azure](http://azure.microsoft.com/support/faq/).


## Corriger les erreurs de SSH courantes

Cette section répertorie les étapes à suivre pour corriger rapidement les problèmes liés à la connexion SSH.

### Machines virtuelles créées à l’aide du modèle de déploiement classique

Suivez les étapes suivantes pour résoudre les échecs de connexion SSH les plus courants :

1. _Réinitialisez l’accès à distance_ à partir du [portail Azure](https://portal.azure.com).<br> Cliquez sur **Parcourir** > **Machines virtuelles (classiques)** > votre machine virtuelle Linux > **Réinitialiser l’accès à distance**.

2. Redémarrez la machine virtuelle.<br> À partir du [portail Azure](https://portal.azure.com), cliquez sur **Parcourir** > **Machines virtuelles (classiques)** > votre machine virtuelle Linux > **Redémarrer**.<br> À partir du [portail Azure Classic](https://manage.windowsazure.com), ouvrez **Machines virtuelles** > **Instances** et cliquez sur **Redémarrer**.

3. [Redimensionnez la machine virtuelle](https://msdn.microsoft.com/library/dn168976.aspx).

4. Suivez les instructions dans [Comment réinitialiser un mot de passe ou SSH pour les machines virtuelles basées sur Linux](virtual-machines-linux-classic-reset-access.md) sur la machine virtuelle, pour :

	- réinitialiser le mot de passe ou la clé SSH ;
	- créer un compte d’utilisateur _sudo_ ;
	- réinitialiser la configuration SSH.

5. Vérifiez l’intégrité des ressources de la machine virtuelle pour les problèmes de plateforme.<br> Cliquez sur **Parcourir** > **Machines virtuelles (classiques)** > votre machine virtuelle Linux > **Paramètres** > **Vérifier l’intégrité**.


### Machines virtuelles créées à l’aide du modèle de déploiement Resource Manager

Pour résoudre les problèmes de SSH courants pour les machines virtuelles créées à l’aide du modèle de déploiement de Resource Manager, essayez les étapes suivantes.

1. _Réinitialisez la connexion SSH_ à votre machine virtuelle Linux sur la ligne de commande, à l'aide de l'interface de ligne de commande Azure ou d'Azure PowerShell. Assurez-vous que [Microsoft Azure Linux Agent](virtual-machines-linux-agent-user-guide.md) version 2.0.5 ou ultérieure est installé.

**Utilisation de l’interface de ligne de commande Azure** :

a. Si ce n'est pas déjà fait, [installez l'interface de ligne de commande Azure et connectez-vous à votre abonnement Azure](../xplat-cli-install.md) à l'aide de la commande `azure login`.

b. Assurez-vous que vous êtes en mode Resource Manager. Les versions les plus récentes de l’interface de ligne de commande Azure adoptent par défaut le mode Resource Manager.

	```
	azure config mode arm
	```

c. Réinitialisez la connexion SSH à l'aide de l'une des méthodes suivantes.

* Utilisez la commande `vm reset-access`, comme dans l'exemple suivant.

	```
	azure vm reset-access -g YourResourceGroupName -n YourVirtualMachineName -r
	```

Cette commande installera l'extension `VMAccessForLinux` sur votre machine virtuelle.

* Sinon, créez un fichier nommé PrivateConf.json avec le contenu suivant :

	```
	{  
		"reset_ssh":"True"
	}
	```

Puis, exécutez manuellement l'extension `VMAccessForLinux` pour réinitialiser votre connexion SSH.

	```
	azure vm extension set "YourResourceGroupName" "YourVirtualMachineName" VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
	```

**Utilisation d’Azure PowerShell** :

a. Si ce n'est pas déjà fait, [installez Azure PowerShell et connectez-vous à votre abonnement Azure](../powershell-install-configure.md) à l'aide de la méthode Azure AD. Dans les versions d’Azure PowerShell antérieures à 1.0.x, vous devez basculer vers le mode Resource Manager à l’aide de _Switch-AzureMode_.

b. Exécutez l'extension `VMAccessForLinux` pour réinitialiser votre connexion SSH, comme illustré dans l'exemple suivant. Dans les versions antérieures, la commande serait _Set-AzureVMExtension_.

	```
	Set-AzureRmVMExtension -ResourceGroupName "yourRG" -VMName "yourVM" -Location "West US" -Name "VMAccessForLinux" -Publisher "Microsoft.OSTCExtensions" -ExtensionType "VMAccessForLinux" -TypeHandlerVersion "1.2" -SettingString "{}" -ProtectedSettingString '{"reset_ssh":true}'
	```

2. Redémarrez votre machine virtuelle Linux à partir du [portail Azure](https://portal.azure.com).<br> Cliquez sur **Parcourir** > **Machines virtuelles** > votre machine virtuelle Linux > **Redémarrer**.

3. _Réinitialisez votre mot de passe ou la clé SSH_ pour votre machine virtuelle Linux sur la ligne de commande, à l'aide de l'interface de ligne de commande Azure ou d'Azure PowerShell. Vous pouvez également créer un nom d’utilisateur et mot de passe avec l’autorité _sudo_, comme illustré dans l’exemple suivant.

**Utilisation de l’interface de ligne de commande Azure** :

Installez et configurez l’interface de ligne de commande Azure comme indiqué ci-dessus. Basculez en mode Resource Manager, si nécessaire, et exécutez l’extension par le biais de l’une des méthodes suivantes.

* Exécutez la commande `vm reset-access` pour définir toutes les informations d'identification SSH.

	```
	azure vm reset-access TestRgV2 TestVmV2 -u NewUser -p NewPassword
	```

Pour plus d'informations, tapez `azure vm reset-access -h` sur la ligne de commande.

* Sinon, créez un fichier nommé PrivateConf.json avec le contenu suivant.

	```
	{
		"username":"NewUsername", "password":"NewPassword", "expiration":"2016-01-01", "ssh_key":"", "reset_ssh":false, "remove_user":""
	}
	```

Exécutez ensuite l’extension Linux à l’aide du fichier ci-dessus.

	```
	$azure vm extension set "testRG" "testVM" VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
	```

Remarque : vous pouvez utiliser une procédure similaire à celle décrite dans la section [Comment réinitialiser un mot de passe ou SSH pour les machines virtuelles basées sur Linux](virtual-machines-linux-classic-reset-access.md) pour essayer d'autres variations. N’oubliez pas de modifier les instructions de l’interface de ligne de commande Azure pour le mode Resource Manager.


**Utilisation d’Azure PowerShell** :

Installez et configurez Azure PowerShell comme indiqué ci-dessus. Basculez en mode Resource Manager, puis exécutez l'extension comme suit.

	```
	$RGName = 'testRG'
	$VmName = 'testVM'
	$Location = 'West US'

	$ExtensionName = 'VMAccessForLinux'
	$Publisher = 'Microsoft.OSTCExtensions'
	$Version = '1.2'

	$PublicConf = '{}'
	$PrivateConf = '{"username":"NewUsername", "password":"NewPassword", "ssh_key":"", "reset_ssh":false, "remove_user":""}'

	Set-AzureRmVMExtension -ResourceGroupName $RGName -VMName $VmName -Location $Location -Name $ExtensionName -Publisher $Publisher -ExtensionType $ExtensionName -TypeHandlerVersion $Version -SettingString $PublicConf -ProtectedSettingString $PrivateConf
	```

Veillez à remplacer les valeurs $RGName, $VmName, $Location et les informations d'identification SSH par les valeurs spécifiques à votre installation.



## Résolution détaillée des erreurs SSH

Si le client SSH ne peut toujours pas atteindre le service SSH sur la machine virtuelle, cela peut être dû à de nombreuses causes. Voici les composants impliqués.

![Diagramme qui affiche les composants d’un service SSH](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot1.png)

Les sections suivantes vous aident à isoler la source du problème et à déterminer différents types de solutions.

### Étapes préliminaires

Tout d'abord, vérifiez l'état de la machine virtuelle sur le portail.

Dans le [portail Azure Classic](https://manage.windowsazure.com), pour les machines virtuelles suivant un modèle de déploiement classique :

1. Cliquez sur **Machines virtuelles** > *Nom de la machine virtuelle*.
2. Cliquez sur le **tableau de bord** de la machine virtuelle pour vérifier son état.
3. Cliquez sur **Surveiller** pour voir l’activité récente des ressources de calcul, de stockage et réseau.
4. Cliquez sur **Points de terminaison** pour vous assurer qu’il existe un point de terminaison pour le trafic SSH.

Dans le [portail Azure](https://portal.azure.com) :

1. Pour une machine virtuelle créée avec un modèle de déploiement classique, cliquez sur **Parcourir** > **Machines virtuelles (classiques)** > *Nom de la machine virtuelle*. Pour une machine virtuelle créée avec Resource Manager, cliquez sur **Parcourir** > **Machines virtuelles** > *Nom de la machine virtuelle*. Le volet d’état de la machine virtuelle doit afficher **En cours d’exécution**. Faites défiler vers le bas pour voir l’activité récente des ressources de calcul, de stockage et réseau.
2. Cliquez sur **Paramètres** pour examiner les points de terminaison, les adresses IP et les autres paramètres. Pour identifier les points de terminaison sur les machines virtuelles créées avec Resource Manager, vérifiez si un [groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) est défini, les règles qui s'y appliquent et si elles sont référencées dans le sous-réseau.

Pour vérifier la connectivité réseau, contrôlez les points de terminaison configurés et déterminez si vous pouvez atteindre la machine virtuelle par le biais d’un autre protocole, comme HTTP ou un autre service.

Une fois ces étapes effectuées, essayez à nouveau la connexion SSH.


### Découvrir la source du problème

Le client SSH sur votre ordinateur n’a peut-être pas pu atteindre le service SSH sur la machine virtuelle Azure en raison des sources suivantes de problèmes ou de configurations incorrectes :

- Ordinateur client SSH
- Appareil du périmètre de l’organisation
- point de terminaison de service cloud et liste de contrôle d’accès (ACL) ;
- Groupes de sécurité réseau
- Machine virtuelle Azure Linux

#### Source 1 : ordinateur client SSH

Pour vous assurer que votre ordinateur n’est pas la source de l’échec, vérifiez qu’il peut établir des connexions SSH avec un autre ordinateur Linux local.

![Diagramme qui met en évidence un composant d'ordinateur client SSH](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot2.png)

Si cette opération échoue, recherchez sur votre ordinateur :

- un paramètre de pare-feu local qui bloque le trafic SSH entrant ou sortant (TCP 22) ;
- un logiciel de proxy client installé localement qui empêche les connexions SSH ;
- un logiciel de surveillance réseau installé localement qui empêche les connexions SSH ;
- d’autres types de logiciels de sécurité qui surveillent le trafic ou autorisent/interdisent des types spécifiques de trafic.

Dans tous les cas, désactivez temporairement le logiciel concerné et essayez d’établir une connexion SSH avec un ordinateur local pour déterminer la cause. Contactez ensuite votre administrateur réseau pour corriger les paramètres du logiciel pour autoriser les connexions SSH.

Si vous utilisez l’authentification par certificat, vérifiez que vous avez ces autorisations sur le dossier .ssh dans votre répertoire de base :

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id\_rsa (ou tout autre fichier où vous avez stocké vos clés privées)
- Chmod 644 ~/.ssh/known\_hosts (contient les hôtes auxquels vous vous êtes connecté via SSH)

#### Source 2 : appareil du périmètre de l’organisation

Pour vous assurer que votre appareil du périmètre de l’organisation n’est pas la source de l’échec, vérifiez qu’un ordinateur directement connecté à Internet peut établir des connexions SSH avec votre machine virtuelle Azure. Si vous accédez à la machine virtuelle par le biais d'une connexion VPN ou ExpressRoute de site à site, passez à [Source 4 : groupes de sécurité réseau](#nsg).

![Diagramme qui met en évidence un appareil du périmètre de l’organisation](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot3.png)

Si vous n’avez pas d’ordinateur directement connecté à Internet, vous pouvez facilement créer une machine virtuelle Azure dans son propre groupe de ressources ou service cloud et l’utiliser. Pour plus d’informations, consultez [Créer une machine virtuelle exécutant Linux dans Azure](virtual-machines-linux-cli-create.md). Une fois le test terminé, supprimez le groupe de ressources ou la machine virtuelle et le service cloud.

Si vous pouvez créer une connexion SSH avec un ordinateur directement connecté à Internet, recherchez les éléments suivants sur votre appareil du périmètre de l’organisation :

- un pare-feu interne qui bloque le trafic SSH avec Internet ;
- un serveur proxy qui empêche les connexions SSH ;
- un logiciel de détection d’intrusion ou de surveillance réseau s’exécutant sur les appareils de votre réseau du périmètre et qui empêche les connexions SSH.

Contactez votre administrateur réseau pour corriger les paramètres de vos appareils du périmètre de l’organisation pour permettre le trafic SSH avec Internet.

#### Source 3 : Point de terminaison de service cloud et liste de contrôle d’accès

> [AZURE.NOTE] Cette source s’applique uniquement aux machines virtuelles créées à l’aide du modèle de déploiement classique. Pour les machines virtuelles créées à l'aide de Resource Manager, passez à [Source 4 : groupes de sécurité réseau](#nsg).

Pour éliminer le point de terminaison de service cloud et la liste de contrôle d'accès comme source de l'échec, pour les machines virtuelles créées à l'aide du [modèle de déploiement classique](../resource-manager-deployment-model.md), vérifiez qu'une autre machine virtuelle Azure dans le même réseau virtuel peut établir des connexions SSH avec votre machine virtuelle.

![Diagramme qui met en évidence un point de terminaison de service cloud et une liste de contrôle d’accès](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot4.png)

Si vous ne disposez pas d’une autre machine virtuelle dans le même réseau virtuel, vous pouvez facilement en créer une. Pour plus d’informations, consultez [Créer une machine virtuelle exécutant Linux dans Azure](virtual-machines-linux-cli-create.md). Une fois le test terminé, supprimez la machine virtuelle supplémentaire.

Si vous pouvez créer une connexion SSH avec une machine virtuelle dans le même réseau virtuel, vérifiez :

- la configuration du point de terminaison pour le trafic SSH sur la machine virtuelle cible ; le port TCP privé du point de terminaison doit correspondre au port TCP sur lequel le service SSH de la machine virtuelle est à l’écoute (par défaut, il s’agit du port 22). Pour les machines virtuelles créées à l’aide de modèles dans le modèle de déploiement Resource Manager, vérifiez le numéro du port TCP SSH dans le portail Azure en accédant à **Parcourir** > **Machines virtuelles (v2)** > *Nom de la machine virtuelle* > **Paramètres** > **Points de terminaison**.
- La liste de contrôle d’accès du point de terminaison du trafic SSH sur la machine virtuelle cible. Les listes de contrôle d’accès vous permettent de spécifier le trafic Internet entrant autorisé ou interdit en fonction de l’adresse IP source. Une mauvaise configuration des listes de contrôle d’accès peut empêcher le trafic SSH entrant d’accéder au point de terminaison. Vérifiez vos listes de contrôle d’accès pour vous assurer que le trafic entrant provenant des adresses IP publiques de votre proxy ou d’un autre serveur Edge est autorisé. Pour plus d'informations, consultez [À propos des listes de contrôle d'accès (ACL) réseau](../virtual-network/virtual-networks-acl.md).

Pour vérifier que le point de terminaison n'est pas la source du problème, supprimez le point de terminaison actuel, créez un autre point de terminaison et spécifiez le nom **SSH** (port TCP 22 pour le numéro du port public et privé). Pour plus d’informations, consultez [Configuration des points de terminaison sur une machine virtuelle dans Azure](virtual-machines-windows-classic-setup-endpoints.md).

<a id="nsg"></a>
#### Source 4 : groupes de sécurité réseau

Les groupes de sécurité réseau vous permettent de mieux contrôler le trafic entrant et sortant autorisé. Vous pouvez créer des règles qui s’étendent aux sous-réseaux et aux services cloud d’un réseau virtuel Azure. Vérifiez les règles de votre groupe de sécurité réseau pour vous assurer que le trafic SSH vers et depuis Internet est autorisé. Pour plus d'informations, consultez [À propos des groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md).

#### Source 5 : machine virtuelle Azure Linux

La dernière source des problèmes possibles est la machine virtuelle Azure elle-même.

![Diagramme qui met en évidence une machine virtuelle Azure Linux](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot5.png)

Si vous ne l'avez pas déjà fait, suivez les instructions permettant de [réinitialiser un mot de passe ou SSH pour les machines virtuelles basées sur Linux](virtual-machines-linux-classic-reset-access.md) sur la machine virtuelle.

Essayez une nouvelle fois de vous connecter à partir de votre ordinateur. Si cette opération échoue, l’une des raisons suivantes peut en être la cause :

- Le service SSH n’est pas en cours d’exécution sur la machine virtuelle cible.
- Le service SSH n’est pas à l’écoute sur le port TCP 22. Pour tester cela, installez un client telnet sur votre ordinateur local et exécutez « telnet *NomServiceCloud*.cloudapp.net 22 ». Cette opération permet de déterminer si la machine virtuelle autorise les communications entrantes et sortantes avec le point de terminaison SSH.
- Le pare-feu local sur la machine virtuelle cible a des règles qui empêchent le trafic SSH entrant ou sortant.
- Un logiciel de détection d’intrusion ou de surveillance réseau s’exécutant sur la machine virtuelle Azure empêche les connexions SSH.


## Ressources supplémentaires

Pour les machines virtuelles suivant un modèle de déploiement classique, suivez les instructions de la section [Comment réinitialiser un mot de passe ou SSH pour les machines virtuelles basées sur Linux](virtual-machines-linux-classic-reset-access.md).

[Résolution des problèmes de connexion du Bureau à distance Windows avec une machine virtuelle Azure Windows](virtual-machines-windows-troubleshoot-rdp-connection.md)

[Résoudre les problèmes d’accès à une application exécutée sur une machine virtuelle Azure](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0323_2016-->