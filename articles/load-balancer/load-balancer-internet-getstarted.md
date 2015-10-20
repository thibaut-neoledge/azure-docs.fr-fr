
<properties
   pageTitle="Prise en main de la configuration de votre équilibreur de charge accessible sur Internet | Microsoft Azure"
   description="Définissez votre premier jeu d'équilibrage de charge accessible sur Internet pour vos machines virtuelles ou vos services cloud."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/12/2015"
   ms.author="joaoma" />

# Prise en main de la configuration de votre équilibreur de charge accessible sur Internet

> [AZURE.SELECTOR]
- [Azure classic steps](load-balancer-internet-getstarted.md)
- [Resource Manager Powershell steps](load-balancer-arm-powershell.md)

Les services d'équilibrage de charge de Microsoft Azure fonctionnent avec tous les types de clients (IaaS ou PaaS) et tous les système d'exploitation pris en charge (systèmes d'exploitation Windows ou Linux).


## Configurer un équilibrage de charge accessible sur Internet pour les machines virtuelles

Afin d'équilibrer le trafic réseau à partir d'Internet sur les machines virtuelles d'un service cloud, vous devez créer un jeu d'équilibrage de la charge. Cette procédure suppose que vous avez déjà créé les machines virtuelles et qu’elles sont toutes dans le même service cloud.

**Pour configurer un ensemble d'équilibrage de charge interne pour les machines virtuelles**

1. Dans le portail Azure, cliquez sur **Machines virtuelles**, puis sur le nom d'une machine virtuelle du jeu d'équilibrage de la charge.
2.	Cliquez sur **Points de terminaison**, puis sur **Ajouter**.

4.	Sur la page **Ajouter un point de terminaison à une machine virtuelle**, cliquez sur la flèche droite.

4.	Dans la page **Spécifier les détails du point de terminaison** :
	- Dans **Nom**, saisissez le nom du point de terminaison ou sélectionnez-en un dans la liste des points de terminaison prédéfinis pour les protocoles communs.
	-  Dans **Protocole**, sélectionnez le protocole requis, TCP ou UDP, pour ce type de point de terminaison.
 	-  Dans **Port public et Port privé**, entrez les numéros de port dont se servira la machine virtuelle. Vous pouvez utiliser le port privé et des règles de pare-feu sur la machine virtuelle pour rediriger le trafic de façon pertinente pour votre application. Le port privé et le port public peuvent être identiques. Par exemple, pour un point de terminaison pour le trafic Web (HTTP), vous pouvez attribuer le port 80 comme port public ou privé.

5.	Sélectionnez **Créer un jeu d'équilibrage de la charge**, puis cliquez sur la flèche vers la droite.

6.	Sur la page **Configurer le jeu d'équilibrage de la charge**, indiquez le nom du jeu d'équilibrage de charge et attribuez les valeurs correspondant au comportement de sonde de l'équilibrage de charge Azure. L'équilibrage de la charge utilise des sondes pour déterminer si les machines virtuelles du jeu d'équilibrage de la charge sont en mesure de recevoir le trafic entrant.

7.	Cliquez sur la coche pour créer le point de terminaison à charge équilibrée. **Oui** s'affiche dans la colonne **Nom du jeu d'équilibrage de la charge** de la page **Points de terminaison** de la machine virtuelle.

8.	Dans le portail, cliquez sur **Machines virtuelles**, sur le nom d'une machine virtuelle supplémentaire du jeu d'équilibrage de la charge, sur **Points de terminaison**, puis sur **Ajouter**.

9.	Sur la page **Ajouter un point de terminaison à la machine virtuelle**, cliquez sur **Ajouter un point de terminaison à un jeu d'équilibrage de la charge existant**, sélectionnez le nom du jeu d'équilibrage de la charge, puis cliquez sur la flèche vers la droite.

10.	Sur la page **Spécifier les détails du point de terminaison**, tapez le nom du point de terminaison, puis cliquez sur la coche. Pour les machines virtuelles supplémentaires dans le jeu d’équilibrage de la charge, répétez les étapes 8 à 10.

Vous pouvez également configurer des points de terminaison avec les applets de commande Windows PowerShell suivantes :

- Add-AzureEndpoint

[Add-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495300)

- Get-AzureEndpoint

[Get-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495158)

- Remove-AzureEndpoint

[Remove-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495161)


## Configurer un équilibrage de charge accessible pour les services de Cloud


Les services cloud sont automatiquement configurés avec un équilibrage de charge et peuvent être personnalisés via le modèle de service

Vous pouvez utiliser le Kit de développement logiciel (SDK) Azure pour .NET 2.5 pour mettre à jour votre service cloud. Les paramètres de point de terminaison des services cloud sont définis dans le fichier de [définition de service](https://msdn.microsoft.com/library/azure/gg557553.aspx).csdef.

L’exemple suivant montre la configuration d’un fichier servicedefinition.csdef pour un déploiement de cloud :

En vérifiant l'extrait de code du fichier .csdef généré par un déploiement de cloud, vous pouvez voir le point de terminaison externe configuré pour utiliser les ports HTTP sur les ports 10000, 10001 et 10002.


	<ServiceDefinition name=“Tenant“>
   	<WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
  	</WorkerRole>
	</ServiceDefinition>




### Vérifier l'état d'intégrité de l'équilibrage de charge pour les services cloud


Voici un exemple de sonde d’intégrité :

	 	<LoadBalancerProbes>
    	<LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
 	 	</LoadBalancerProbes>

L'équilibrage de charge combine les informations du point de terminaison et de la sonde pour créer une URL sous la forme http://{DIP de VM}:80/Probe.aspx pour interroger l'intégrité du service.

Le service détecte les sondes périodiques à partir de la même adresse IP. Il s'agit de la requête de la sonde d'intégrité en provenance de l'hôte du nœud sur lequel la machine virtuelle s'exécute. Le service doit répondre avec un code d’état HTTP 200 pour que l’équilibrage de charge suppose que le service est sain. Tout autre code d'état HTTP (par exemple 503) prend directement la machine virtuelle hors service.

La définition de la sonde en contrôle également la fréquence. Dans le cas ci-dessus, l’équilibrage de charge sonde le point de terminaison toutes les 5 secondes. Si aucune réponse positive n'est reçue pendant 10 secondes (deux intervalles de sondage), la sonde est considérée comme défaillante et la machine virtuelle est mise hors service. De même, si le service est hors service et qu’une réponse positive est reçue, le service est immédiatement remis en service. Si le service fluctue entre sain et défectueux, l'équilibrage de charge peut décider de retarder la remise en service du service jusqu'à ce qu'il soit sain pendant un certain nombre d'analyses.

Vérifiez le schéma de définition de service de la [sonde d’intégrité](https://msdn.microsoft.com/library/azure/jj151530.aspx) pour plus d’informations.

## Configurer de l'équilibrage de charge à l'aide de PowerShell

Après avoir créé une machine virtuelle, vous pouvez utiliser les applets de commande PowerShell pour ajouter un équilibrage de charge à une machine virtuelle dans le même service cloud.

Dans l'exemple ci-dessous, vous allez ajouter un équilibrage de charge appelé « webfarm » au point de terminaison du service cloud « mycloudservice » (ou mycloudservice.cloudapp.net) et la machine virtuelle avec le nom myVM. L'équilibrage de charge reçoit le trafic sur le port 80 et effectue l'équilibrage de charge du trafic réseau entre les machines virtuelles sur le port 8080 avec HTTP.

	Get-AzureVM -ServiceName "mycloudservice" -Name "MyVM" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM


## Étapes suivantes

[Prise en main de la configuration d’un équilibrage de charge interne](load-balancer-internal-getstarted.md)

[Configuration d'un mode de distribution d'équilibrage de charge](load-balancer-distribution-mode.md)

[Configuration des paramètres de délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)

<!---HONumber=Oct15_HO3-->