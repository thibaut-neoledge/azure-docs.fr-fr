<properties
   pageTitle="Workflow de configuration d'un circuit ExpressRoute | Microsoft Azure"
   description="Cette page vous guide tout au long des workflows pour la configuration du circuit ExpressRoute et des homologations"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2015"
   ms.author="cherylmc"/>

# Workflows d’approvisionnement du circuit ExpressRoute et états du circuit
Cette page vous guide de façon sommaire tout au long des workflows d’approvisionnement du service et de configuration du routage.

![](./media/expressroute-workflows/expressroute-circuit-workflow.png)

L'illustration et les étapes correspondantes suivantes montrent les tâches que vous devez effectuer pour approvisionner un circuit ExpressRoute de bout en bout.

1. Utilisez PowerShell pour configurer un circuit ExpressRoute. Suivez les instructions de l’article [Création de circuits ExpressRoute](expressroute-howto-circuit-classic.md) pour plus de détails.

2. Commandez la connectivité auprès du fournisseur de services. Ce processus varie. Contactez votre fournisseur de connectivité pour plus d’informations sur la commande de connectivité.

3. Assurez-vous que le circuit a été correctement approvisionné en vérifiant l’état approvisionnement du circuit ExpressRoute via PowerShell.

4. Configurez les domaines de routage. Si votre fournisseur de connectivité gère la couche 3 pour vous, il configurera le routage pour votre circuit. Si votre fournisseur de connectivité offre uniquement des services de couche 2, vous devez configurer le routage conformément aux instructions décrites dans les pages [Conditions requises pour le routage](expressroute-routing.md) et [Configuration du routage](expressroute-howto-routing-classic.md).

	1. Activer l'homologation privée Azure : vous devez activer cette homologation pour vous connecter aux machines virtuelles/services de cloud déployés au sein de réseaux virtuels. 
	2. Activer l'homologation publique Azure : vous devez activer l'homologation publique Azure si vous souhaitez vous connecter à des services Azure hébergés sur des adresses IP publiques. Cette étape est nécessaire pour accéder aux ressources Azure si vous avez choisi d'activer le routage par défaut pour l'homologation privée Azure.
	3. Activer l’homologation Microsoft : vous devez activer cette option pour accéder aux services Office 365 et CRM Online. 
	
	>[AZURE.IMPORTANT]Si vous activez l'homologation Microsoft, assurez-vous que l'homologation publique Azure est également activée pour accéder à Azure AD. Pour vous connecter à Microsoft, vous devez veiller à utiliser un proxy/appareil edge différent de celui que vous utilisez pour Internet. L’utilisation du même appareil edge à la fois pour ExpressRoute et Internet entraîne un routage asymétrique et provoque des pertes de connectivité sur votre réseau.


	![](./media/expressroute-workflows/expressroute-routing-workflow.png)

5. Liaison de réseaux virtuels à des circuits ExpressRoute - vous pouvez lier des réseaux virtuels à votre circuit ExpressRoute. Suivez les instructions [pour lier des réseaux virtuels](expressroute-howto-linkvnets-classic.md) à votre circuit. Ces réseaux virtuels peuvent figurer dans le même abonnement Azure que le circuit ExpressRoute, ou dans un autre abonnement.


## États d’approvisionnement du circuit ExpressRoute

Chaque circuit ExpressRoute comporte deux états :

- État d’approvisionnement du fournisseur de service
- Statut

L'état représente l’état d'approvisionnement de Microsoft. Cette propriété peut avoir l'un des états suivants : *Enabled*, *Enabling* ou *Disabling*. Le circuit ExpressRoute doit être dans l'état suivant pour pouvoir être utilisé.

L'état d’approvisionnement du fournisseur de connectivité représente l'état du côté du fournisseur de connectivité. Il peut afficher *NotProvisioned*, *Provisioning* ou *Provisioned*. Le circuit ExpressRoute doit être dans l'état Provisioned pour pouvoir être utilisé.

### États possibles d'un circuit ExpressRoute

Cette section répertorie les états possibles d’un circuit ExpressRoute.

#### Lors de la création

Le circuit ExpressRoute affiche l'état indiqué ci-dessous dès que vous exécutez l'applet de commande PowerShell pour créer le circuit ExpressRoute.

	ServiceProviderProvisioningState : NotProvisioned
	Status                           : Enabled


#### Lorsque le fournisseur de connectivité est en cours d’approvisionnement du circuit

Le circuit ExpressRoute affiche l'état indiqué ci-dessous dès que vous passez la clé de service au fournisseur de connectivité et qu’il démarre le processus d’approvisionnement.

	ServiceProviderProvisioningState : Provisioning
	Status                           : Enabled


#### Lorsque fournisseur de connectivité a terminé le processus d’approvisionnement

Le circuit ExpressRoute affiche l'état indiqué ci-dessous dès que le fournisseur de connectivité a terminé le processus d’approvisionnement.

	ServiceProviderProvisioningState : Provisioned
	Status                           : Enabled

Provisioned et Enabled sont les seuls états dans lesquels le circuit peut se trouver pour pouvoir être utilisé. Si vous utilisez un fournisseur de couche 2, vous pouvez configurer le routage pour votre circuit uniquement lorsqu'il est dans cet état.

#### Si une annulation de provisionnement est lancée d’abord du côté Microsoft

Le circuit ExpressRoute affiche l'état indiqué dès que vous exécutez l'applet de commande PowerShell pour supprimer le circuit ExpressRoute.

	ServiceProviderProvisioningState : Provisioning
	Status                           : Disabling

Vous devez contacter votre fournisseur de connectivité pour annuler l’approvisionnement du circuit ExpressRoute. **Important :** Microsoft continuera à facturer le circuit jusqu'à ce que vous exécutiez l'applet de commande PowerShell pour annuler l’approvisionnement du circuit.

#### Si une annulation de provisionnement est lancée du côté du fournisseur de services

Si vous avez demandé au fournisseur de services d’annuler d’abord l’approvisionnement du circuit ExpressRoute, le circuit affichera l'état indiqué ci-dessous une fois que le fournisseur de services a terminé le processus d'annulation de l’approvisionnement.


	ServiceProviderProvisioningState : NotProvisioned
	Status                           : Enabled

Vous pouvez choisir de le réactiver si nécessaire, ou exécuter des applets de commande PowerShell pour supprimer le circuit. **Important :** Microsoft continuera à facturer le circuit jusqu'à ce que vous exécutiez l'applet de commande PowerShell pour annuler l’approvisionnement du circuit.


## État de configuration d’une session de routage

Le protocole d’approvisionnement BGP vous indique si la session BGP a été activée sur le matériel edge Microsoft. L'état doit être activé pour que vous puissiez utiliser l'homologation.

Il est important de vérifier l'état de la session BGP, en particulier pour l'homologation Microsoft. En plus de l'état d’approvisionnement BGP, il existe un autre état appelé *état des préfixes publics publiés*. Les préfixes publics publiés doivent afficher l’état *configured*, à la fois pour que la session BGP soit opérationnelle et pour que votre routage fonctionne de bout en bout.

Si l'état du préfixe public publié indique qu’une *validation est nécessaire*, la session BGP n'est pas activée car les préfixes publiés ne correspondent pas au numéro AS dans un des registres de routage.

>[AZURE.IMPORTANT]Si l'état des préfixes publics publiés indique une *validation manuelle*, vous devez ouvrir un ticket de support auprès du [support Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) et fournir la preuve que vous possédez les adresses IP publiés ainsi que le numéro système autonome associé.


## Étapes suivantes

- Configurez votre connexion ExpressRoute.
	- [Création d’un circuit ExpressRoute](expressroute-howto-circuit-classic.md)
	- [Configuration du routage](expressroute-howto-routing-classic.md)
	- [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-classic.md)

<!---HONumber=Sept15_HO4-->