<properties
	pageTitle="Résolution des problèmes d'accès aux applications sur une machine virtuelle | Microsoft Azure"
	description="Si vous ne pouvez pas accéder à une application exécutée sur une machine virtuelle Azure, procédez comme suit pour isoler la source du problème."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="dkshir"/>

# Résolution des problèmes d’accès à une application exécutée sur une machine virtuelle Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Cet article traite de la résolution des problèmes d'accès aux applications sur une machine virtuelle créée avec le modèle de déploiement classique ou le modèle de déploiement Resource Manager.

Cet article décrit une approche méthodique pour isoler la source du problème et la corriger dans le cas où vous ne parvenez pas à accéder à une application exécutée sur une machine virtuelle Azure

> [AZURE.NOTE]Pour vous aider à vous connecter à une machine virtuelle Azure, consultez la page [Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure Windows](virtual-machines-troubleshoot-remote-desktop-connections.md) ou [Résolution des problèmes des connexions SSH avec une machine virtuelle Azure Linux](virtual-machines-troubleshoot-ssh-connections.md).

La résolution des problèmes d’accès à une application exécutée sur une machine virtuelle Azure comporte quatre domaines principaux.

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access1.png)

1.	L’application exécutée sur la machine virtuelle Azure.
2.	La machine virtuelle Azure.
3.	Les points de terminaison Azure pour le service cloud qui contient la machine virtuelle (pour les machines virtuelles créées à l’aide de l’API Service Management), les règles NAT entrantes (pour les machines virtuelles créées dans Resource Manager) et les groupes de sécurité réseau.
4.	Votre périphérique de périmètre Internet.

Pour les ordinateurs clients qui accèdent à l’application par le biais d’une connexion de réseau privé virtuel de site à site ou d’une connexion ExpressRoute, les principaux éléments susceptibles de poser problème sont l’application et la machine virtuelle Azure. Pour déterminer la source du problème et sa correction, procédez comme suit.

## Étape 1 : pouvez-vous accéder à l’application à partir de la machine virtuelle cible ?

Essayez d’accéder à l’application avec le programme client approprié à partir de la machine virtuelle sur laquelle l’application s’exécute, utilisez le nom d’hôte local, l’adresse IP locale ou l’adresse de bouclage (127.0.0.1).

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access2.png)

Par exemple, si l’application est un serveur web, exécutez un navigateur sur la machine virtuelle et essayez d’accéder à une page web hébergée sur la machine virtuelle.

Si vous pouvez accéder à l’application, allez à l’[étape 2](#step2).

Si vous ne pouvez pas accéder à l’application, vérifiez les éléments suivants :

- L’application est exécutée sur la machine virtuelle cible.
- L’application écoute sur les ports TCP et UDP attendus.

Sur les machines virtuelles Windows et Linux, utilisez la commande **netstat** pour afficher les ports d’écoute actifs. Examinez la sortie pour les ports attendus sur lesquels votre application doit être à l’écoute. Redémarrez l’application ou configurez-la pour utiliser les ports attendus en fonction des besoins.

## <a id="step2"></a>Étape2 : pouvez-vous accéder à l’application à partir d’une autre machine virtuelle dans le même réseau virtuel ?

Essayez d’accéder à l’application à partir d’une machine virtuelle différente dans le même réseau virtuel que la machine virtuelle sur laquelle l’application s’exécute à l’aide du nom d’hôte de la machine virtuelle, de son adresse IP publique ou privée affectée par Azure ou de l’adresse IP du fournisseur. Pour les machines virtuelles créées à l’aide de l’API Service Management, n’utilisez pas l’adresse IP publique du service cloud.

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access3.png)

Par exemple, si l’application est un serveur web, essayez d’accéder à un navigateur sur une autre machine virtuelle du même réseau virtuel.

Si vous pouvez accéder à l’application, allez à l’[étape 3](#step3).

Si vous ne pouvez pas accéder à l’application, vérifiez les éléments suivants :

- Le pare-feu de l’hôte sur la machine virtuelle cible autorise le trafic de demandes entrantes et de réponses sortantes.
- Un logiciel de détection d’intrusion ou de surveillance réseau s’exécutant sur la machine virtuelle cible autorise le trafic.
- Les groupes de sécurité réseau autorisent le trafic.
- Un composant distinct qui s’exécute dans votre réseau virtuel dans le chemin d’accès entre la machine virtuelle de test et la machine virtuelle, par exemple un équilibreur de charge ou un pare-feu, autorise le trafic.

Sur une machine virtuelle Windows, utilisez le pare-feu Windows avec fonctions avancées de sécurité pour déterminer si les règles de pare-feu excluent le trafic entrant et sortant de votre application

## <a id="step3"></a>Étape 3 : pouvez-vous accéder à l’application à partir d’un ordinateur qui est en dehors du réseau virtuel, mais pas connecté au même réseau que votre ordinateur ?

Essayez d’accéder à l’application à partir d’un ordinateur en dehors du réseau virtuel car la machine virtuelle sur laquelle l’application est exécutée n’est pas sur le même réseau que votre ordinateur client d’origine.

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access4.png)

Par exemple, si l’application est un serveur web, essayez d’accéder à la page web à partir d’un navigateur sur un autre ordinateur ne faisant pas partie de ce réseau virtuel.

Si vous ne pouvez pas accéder à l’application, vérifiez les éléments suivants :

- Pour les machines virtuelles créées à l’aide de l’API Service Management, que la configuration de points de terminaison pour la machine virtuelle autorise le trafic entrant, notamment le protocole (TCP ou UDP) et les numéros de port public et privé. Pour plus d’informations, voir l’article [Configuration des points de terminaison sur une machine virtuelle](virtual-machines-set-up-endpoints.md).
- Pour les machines virtuelles à l’aide de l’API Service Management, que les listes de contrôle d’accès sur le point de terminaison n’empêchent pas le trafic entrant à partir d’Internet. Pour plus d’informations, voir l’article [Configuration des points de terminaison sur une machine virtuelle](virtual-machines-set-up-endpoints.md).
- Pour les machines virtuelles créées dans Resource Manager, que la configuration de règles NAT entrantes pour la machine virtuelle autorise le trafic entrant, notamment le protocole (TCP ou UDP) et les numéros de port public et privé.
- Que les groupes de sécurité réseau autorisent le trafic de demandes entrantes et de réponses sortantes. Pour plus d’informations, voir [Présentation du groupe de sécurité réseau](virtual-networks-nsg.md).

Si la machine virtuelle ou le point de terminaison est membre d’un jeu à charge équilibrée :

- Vérifiez que le protocole de la sonde (TCP ou UDP) et le numéro de port sont corrects.
- Si le protocole de la sonde et le port sont différents du protocole de jeu à charge équilibrée et du port :
	- Vérifiez que l’application écoute sur le protocole de la sonde (TCP ou UDP) et sur le numéro de port (utilisez **netstat –a** sur la machine virtuelle cible).
	- Le pare-feu de l’hôte sur la machine virtuelle cible autorise le trafic de demandes de sonde entrantes et de réponses de sonde sortantes.

Si vous pouvez accéder à l’application, assurez-vous que votre périphérique de périmètre Internet autorise :

- Le trafic des demandes des applications sortantes, de votre ordinateur client à la machine virtuelle Azure.
- Le trafic des réponses des applications entrantes, de la machine virtuelle Azure.

## Étapes suivantes

Si vous avez effectué les étapes 1 à 3 dans cet article et que vous avez besoin d’une aide supplémentaire pour corriger le problème, vous pouvez :

- Obtenir l’aide des experts Azure dans le monde entier. Soumettre votre problème sur les forums MSDN Azure ou Stack Overflow. Pour plus d’informations, consultez les [forums Microsoft Azure](http://azure.microsoft.com/support/forums/).
- Signaler un incident au support Azure Accédez au [site de support Azure](http://azure.microsoft.com/support/options/) et cliquez sur **Obtenir un support** dans la section **Support technique et sur la facturation**.

## Ressources supplémentaires

[Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure Windows](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Résolution des problèmes des connexions SSH avec une machine virtuelle Azure Linux](virtual-machines-troubleshoot-ssh-connections.md)

<!---HONumber=Oct15_HO2-->