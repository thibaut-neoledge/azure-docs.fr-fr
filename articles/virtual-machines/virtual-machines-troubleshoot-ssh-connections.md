<properties 
	pageTitle="" 
	description="Si vous ne pouvez pas connecter votre machine virtuelle Azure, utilisez les étapes qui suivent pour isoler la source du problème."
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager"/>


<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="josephd"/>

# 

Cet article décrit une approche méthodique pour isoler et corriger le problème dans le cas où vous ne parvenez pas à vous connecter aux machines virtuelles Azure Linux.

## Étape 1 : réinitialiser la configuration, la clé ou le mot de passe SSH

Suivez les instructions dans [Comment réinitialiser un mot de passe ou SSH pour les machines virtuelles Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) sur la machine virtuelle. Avec ces instructions, vous pouvez :

- Réinitialiser le mot de passe ou la clé SSH
- Créer un nouveau compte d’utilisateur sudo.
- Réinitialiser la configuration SSH.

Si le client SSH ne peut toujours pas atteindre le service SSH sur la machine virtuelle, cela peut être dû à de nombreuses raisons. Voici les composants impliqués.
 
![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot1.png)

Les sections suivantes traitent de l’identification et de la détermination des différentes causes du problème, et fournissent des solutions et des solutions de contournement.

## Étape 2 : étapes préliminaires avant la résolution détaillée des problèmes

Vérifiez d’abord l’état de la machine virtuelle dans le portail de gestion Azure ou dans le portail Azure en version préliminaire.

Dans le portail de gestion Azure :

1. Cliquez sur **Machines virtuelles** > *Nom de la machine virtuelle*.
2. Cliquez sur **Tableau de bord** pour la machine virtuelle, pour vérifier son état.
3. Cliquez sur **Surveiller** pour voir l’activité récente des ressources de calcul, de stockage et réseau. 
4. Cliquez sur **Points de terminaison** pour vous assurer qu’il existe un point de terminaison pour le trafic SSH.

Dans le portail Azure en version préliminaire :

1. Cliquez sur **Parcourir** > **Machines virtuelles** > *Nom de la machine virtuelle*. Pour une machine virtuelle créée dans Resource Manager, cliquez sur **Parcourir** > **Machines virtuelles (v2)** > *Nom de machine virtuelle*. Le volet d’état de la machine virtuelle doit afficher **En cours d’exécution**. Faites défiler vers le bas pour voir l’activité récente des ressources de calcul, de stockage et réseau.
2. Cliquez sur **Paramètres** pour examiner les points de terminaison, les adresses IP et les autres paramètres.

Pour vérifier la connectivité réseau, analysez les points de terminaison configurés et déterminez si vous pouvez atteindre la machine virtuelle via un autre protocole, comme HTTP ou un autre service connu.

Si nécessaire, [redémarrez la machine virtuelle](https://msdn.microsoft.com/library/azure/dn763934.aspx) ou [redimensionnez la machine virtuelle](https://msdn.microsoft.com/library/dn168976.aspx).

Une fois ces étapes effectuées, essayez à nouveau la connexion SSH.

## Étape 3 : résolution détaillée des problèmes 

L’incapacité de votre client SSH à atteindre le service SSH sur la machine virtuelle Azure peut être due aux sources suivantes de problèmes ou de configurations incorrectes :

- Ordinateur client SSH
- Appareil du périmètre de l’organisation
- point de terminaison de service cloud et liste de contrôle d’accès (ACL) ;
- Groupes de sécurité réseau
- Machine virtuelle Azure Linux

### Source 1 : ordinateur client SSH

Pour vous assurer que votre ordinateur n’est pas la source du problème ou de la configuration incorrecte, vérifiez que votre ordinateur peut établir des connexions SSH avec un autre ordinateur Linux local.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot2.png)
 
Si vous n’y parvenez pas, recherchez sur votre ordinateur :

- un paramètre de pare-feu local qui bloque le trafic SSH entrant ou sortant (TCP 22) ;
- un logiciel de proxy client installé localement qui empêche les connexions SSH ;
- un logiciel de surveillance réseau installé localement qui empêche les connexions SSH ;
- d’autres types de logiciels de sécurité qui surveillent le trafic ou autorisent/interdisent des types spécifiques de trafic empêchant les connexions SSH.

Dans tous les cas, essayez de désactiver temporairement le logiciel concerné et tentez d’établir une connexion SSH avec un ordinateur local pour déterminer la cause. Contactez ensuite votre administrateur réseau pour corriger les paramètres du logiciel pour autoriser les connexions SSH.

Si vous utilisez l’authentification par certificat, vérifiez que vous avez ces autorisations sur le dossier .ssh dans votre répertoire de base :

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id_rsa (ou les autres fichiers où vous pouvez avoir stocké vos clés privées)
- Chmod 644 ~/.ssh/known_hosts (contient les hôtes auxquels vous vous êtes connecté via SSH)

### Source 2 : appareil du périmètre de l’organisation

Pour vous assurer que votre appareil du périmètre de l’organisation n’est pas la source des problèmes ou de la configuration incorrecte, vérifiez qu’un ordinateur directement connecté à Internet peut établir des connexions SSH avec votre machine virtuelle Azure. Si vous accédez à la machine virtuelle via une connexion VPN ou ExpressRoute de site à site, passez à [Source 4  : groupes de sécurité réseau](#nsg).

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot3.png)
 
Si vous n’avez pas d’ordinateur directement connecté à Internet, vous pouvez facilement créer une machine virtuelle Azure dans son propre groupe de ressources ou service cloud et l’utiliser. Pour plus d’informations, consultez [Créer une machine virtuelle exécutant Linux dans Azure](virtual-machines-linux-tutorial.md). Une fois le test terminé, supprimez le groupe de ressources ou la machine virtuelle et le service cloud.

Si vous pouvez créer une connexion SSH avec un ordinateur directement connecté à Internet, recherchez les éléments suivants sur votre appareil du périmètre de l’organisation :

- un pare-feu interne qui bloque le trafic SSH avec Internet ;
- un serveur proxy qui empêche les connexions SSH ;
- un logiciel de détection d’intrusion ou de surveillance réseau s’exécutant sur les appareils de votre réseau du périmètre et qui empêche les connexions SSH.

Contactez votre administrateur réseau pour corriger les paramètres de vos appareils du périmètre de l’organisation pour permettre le trafic SSH avec Internet.

### Source 3 : Point de terminaison de service cloud et liste de contrôle d’accès

Pour vérifier que le point de terminaison de service cloud et la liste de contrôle d’accès ne sont pas la source de votre problème ou de votre erreur de configuration des machines virtuelles créées dans la gestion des services, vérifiez qu’une autre machine virtuelle Azure du même réseau virtuel peut établir des connexions SSH avec votre machine virtuelle Azure.
 
![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot4.png)

> [AZURE.NOTE]Pour les machines virtuelles créées dans Resource Manager, passez à [Source 4 : Groupes de sécurité réseau](#nsg).

Si vous ne disposez pas d’une autre machine virtuelle dans le même réseau virtuel, vous pouvez facilement en créer une. Pour plus d’informations, consultez [Créer une machine virtuelle exécutant Linux dans Azure](virtual-machines-linux-tutorial.md). Une fois le test terminé, supprimez la machine virtuelle supplémentaire.

Si vous pouvez créer une connexion SSH avec une machine virtuelle dans le même réseau virtuel, vérifiez :

- la configuration du point de terminaison pour le trafic SSH sur la machine virtuelle cible ; le port TCP privé du point de terminaison doit correspondre au port TCP sur lequel le service SSH de la machine virtuelle est à l’écoute. Par défaut, il s’agit du port 22. Pour les machines virtuelles créées dans Resource Manager à l’aide de modèles, vérifiez le numéro du port TCP de SSH dans le Portail Azure en version préliminaire en accédant à **Parcourir** > **Machines virtuelles (v2)** > *Nom de la machine virtuelle* > **Paramètres** > **Points de terminaison**.
- La liste de contrôle d’accès du point de terminaison du trafic SSH sur la machine virtuelle cible. Les listes de contrôle d’accès vous permettent de spécifier le trafic Internet entrant autorisé et interdit en fonction de l’adresse IP source. Une mauvaise configuration des listes de contrôle d’accès peut empêcher le trafic SSH entrant d’accéder au point de terminaison. Examinez vos listes de contrôle d’accès pour vous assurer que le trafic entrant provenant des adresses IP publiques de votre proxy ou d’un autre serveur Edge est autorisé. Pour plus d’informations, consultez [À propos des listes de contrôle d’accès (ACL) réseau](https://msdn.microsoft.com/library/azure/dn376541.aspx).

Pour vérifier que le point de terminaison n’est pas la source du problème, supprimez le point de terminaison actuel et créez un autre point de terminaison, en spécifiant le nom **SSH** (port TCP 22 pour le numéro du port public et privé). Pour plus d’informations, consultez [Configuration des points de terminaison sur une machine virtuelle dans Azure](virtual-machines-set-up-endpoints.md).

### <a id="nsg"></a>Source 4 : groupes de sécurité réseau

Les groupes de sécurité réseau vous permettent de mieux contrôler le trafic entrant et sortant autorisé. Vous pouvez créer des règles qui s’étendent aux sous-réseaux et aux services cloud d’un réseau virtuel Azure. Examinez les règles de votre groupe de sécurité réseau pour vous assurer que le trafic SSH vers et depuis Internet est autorisé. Pour plus d’informations, consultez [À propos des groupes de sécurité réseau](../traffic-manager/virtual-networks-nsg.md).

### Source 5 : machine virtuelle Azure Linux 

La dernière source des problèmes possibles est la machine virtuelle Azure elle-même.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot5.png)
 
Si vous ne l’avez pas déjà fait, suivez les instructions données dans [Comment réinitialiser un mot de passe ou SSH pour les machines virtuelles Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) sur la machine virtuelle.

Essayez une nouvelle fois de vous connecter à partir de votre ordinateur. Si vous ne réussissez toujours pas à vous connecter, voici quelques-uns des problèmes possibles :

- Le service SSH n’est pas en cours d’exécution sur la machine virtuelle cible.
- Le service SSH n’est pas à l’écoute sur le port TCP 22. Pour tester cela, installez un client telnet sur votre ordinateur local et exécutez « telnet *NomServiceCloud*.cloudapp.net 22 ». Ceci permet de déterminer si la machine virtuelle autorise les communications entrantes et sortantes avec le point de terminaison SSH.
- Le pare-feu local sur la machine virtuelle cible a des règles qui empêchent le trafic SSH entrant ou sortant.
- Un logiciel de détection d’intrusion ou de surveillance réseau s’exécutant sur la machine virtuelle Azure empêche les connexions SSH.


## Étape 4 : soumettre votre problème sur les forums de support Azure

Pour obtenir de l’aide de la part d’experts Azure du monde entier, vous pouvez soumettre votre problème sur les forums MSDN Azure ou Stack Overflow. Pour plus d’informations, consultez les [forums Microsoft Azure](http://azure.microsoft.com/support/forums/).

## Étape 5 : signaler un incident au support Azure

Si vous avez effectué les étapes 1 à 4 présentées dans cet article et que vous avez soumis votre problème sur les forums de support Azure, mais que vous ne pouvez toujours pas créer une connexion SSH, vous pouvez envisager de recréer la machine virtuelle.

Si vous ne pouvez pas recréer la machine virtuelle, il est peut-être temps de signaler un incident de support Azure.

Pour signaler un incident, allez sur le [site de support Azure](http://azure.microsoft.com/support/options/), puis cliquez sur **Obtenir un support**.

Pour plus d’informations sur l’utilisation du support Azure, consultez le [FAQ du support Microsoft Azure](http://azure.microsoft.com/support/faq/).

## Ressources supplémentaires

[Comment réinitialiser un mot de passe ou SSH pour les machines virtuelles Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)

[Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure Windows](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Résolution des problèmes d’accès à une application exécutée sur une machine virtuelle Azure](virtual-machines-troubleshoot-access-application.md)
 

<!---HONumber=July15_HO2-->