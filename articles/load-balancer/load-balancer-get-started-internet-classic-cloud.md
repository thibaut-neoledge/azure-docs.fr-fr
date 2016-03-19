<properties 
   pageTitle="Création d'un équilibreur de charge accessible sur Internet dans un modèle de déploiement classique avec les services cloud | Microsoft Azure"
   description="Découvrez comment créer un équilibreur de charge accessible sur Internet dans un modèle de déploiement classique pour les services cloud"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/30/2015"
   ms.author="joaoma" />

# Création d'un équilibreur de charge accessible sur Internet pour les services cloud

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article traite du modèle de déploiement classique. Vous pouvez également [découvrir comment créer un équilibreur de charge accessible sur Internet à l’aide d’Azure Resource Manager](load-balancer-get-started-internet-arm-cli.md).

Les services cloud sont automatiquement configurés avec un équilibrage de charge et peuvent être personnalisés via le modèle de service

## Création d’un équilibreur de charge à l'aide du fichier de définition de service
 
Vous pouvez utiliser le Kit de développement logiciel (SDK) Azure pour .NET 2.5 pour mettre à jour votre service cloud. Les paramètres de point de terminaison des services cloud sont définis dans le fichier .csdef de [définition de service](https://msdn.microsoft.com/library/azure/gg557553.aspx).

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




## Vérifier l'état d'intégrité de l'équilibrage de charge pour les services cloud


Voici un exemple de sonde d’intégrité :

	 	<LoadBalancerProbes>
    	<LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
 	 	</LoadBalancerProbes>

L’équilibreur de charge combine les informations du point de terminaison et de la sonde pour créer une URL sous la forme http://{DIP de VM}:80/Probe.aspx pour interroger l’intégrité du service.

Le service détecte les sondes périodiques à partir de la même adresse IP. Il s'agit de la requête de la sonde d'intégrité en provenance de l'hôte du nœud sur lequel la machine virtuelle s'exécute. Le service doit répondre avec un code d’état HTTP 200 pour que l’équilibrage de charge suppose que le service est sain. Tout autre code d'état HTTP (par exemple 503) prend directement la machine virtuelle hors service.

La définition de la sonde en contrôle également la fréquence. Dans le cas ci-dessus, l’équilibrage de charge sonde le point de terminaison toutes les 5 secondes. Si aucune réponse positive n'est reçue pendant 10 secondes (deux intervalles de sondage), la sonde est considérée comme défaillante et la machine virtuelle est mise hors service. De même, si le service est hors service et qu’une réponse positive est reçue, le service est immédiatement remis en service. Si le service fluctue entre sain et défectueux, l'équilibrage de charge peut décider de retarder la remise en service du service jusqu'à ce qu'il soit sain pendant un certain nombre d'analyses.

Vérifiez le schéma de définition de service de la [sonde d’intégrité](https://msdn.microsoft.com/library/azure/jj151530.aspx) pour plus d’informations.

## Étapes suivantes

[Prise en main de la configuration d’un équilibrage de charge interne](load-balancer-internal-getstarted.md)

[Configuration d'un mode de distribution d'équilibrage de charge](load-balancer-distribution-mode.md)

[Configuration des paramètres de délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_1203_2015-->