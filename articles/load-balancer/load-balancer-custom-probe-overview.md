<properties 
   pageTitle="Sondes personnalisées de l’équilibreur de charge et surveillance de l’état d’intégrité | Microsoft Azure"
   description="Découvrez comment utiliser les sondes personnalisées de l’équilibreur de charge Azure pour surveiller des instances derrière un équilibreur de charge"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma" />


# Sondes d’équilibreur de charge 

Azure Load Balancer offre la possibilité de surveiller l’intégrité des instances de serveur à l’aide de sondes. Quand une sonde ne répond pas, Azure Load Balancer n’envoie plus de nouvelles connexions à l’instance défaillante. Les connexions existantes ne sont pas affectées et les nouvelles connexions sont envoyées aux instances saines.

Des sondes personnalisées TCP ou HTTP doivent être configurées quand vous utilisez des machines virtuelles derrière un équilibreur de charge. Les rôles de service cloud (rôles de travail et rôles web) sont les seules instances de serveur avec surveillance par sonde de l’agent invité.
 
## Présentation du nombre et du délai d’expiration des sondes

Le comportement de sonde dépend du nombre de sondes ayant réussi/échoué nécessaires pour marquer une instance Active/Inactive. Cette valeur est calculée par SuccessFailCount = Délai d’expiration/Fréquence. Pour le portail, le délai d’expiration est défini sur deux fois la valeur de la fréquence (Délai d’expiration = Fréquence * 2).

La configuration de sonde de toutes les instances à charge équilibrée pour un point de terminaison (jeu à charge équilibrée) doit être identique. Cela signifie que vous ne pouvez avoir une configuration de sonde différente (par exemple, le port local, le délai d’expiration, etc.) pour chaque instance de rôle ou machine virtuelle du même service hébergé pour une combinaison de points de terminaison donnée.


>[AZURE.IMPORTANT] Une sonde d’équilibreur de charge utilise une adresse IP 168.63.129.16. Cette adresse IP publique est utilisée pour faciliter un canal de communication vers des ressources de plateforme internes pour le scénario « Apportez votre propre réseau virtuel IP ». L’adresse IP publique virtuelle 168.63.129.16 est utilisée dans toutes les régions et ne change pas. Il est recommandé que l’adresse IP soit autorisée dans toutes les stratégies de pare-feu local. Elle ne doit pas être considérée comme un risque de sécurité, car seule la plateforme Azure interne peut émettre un message à partir de cette adresse. Si elle n’est pas autorisée, divers scénarios peuvent présenter un comportement inattendu, comme la configuration de la même plage d’adresses IP 168.63.129.16 et l’obtention d’une adresse IP en double.


## Types de sondes

### Sonde d’agent invité

Pour les services cloud uniquement. Azure Load Balancer utilise l’agent invité de la machine virtuelle, écoute et répond avec une réponse HTTP 200 OK uniquement si l’instance est prête (c.-à-d. si l’état de l’instance n’est pas Occupé, Recyclage, Arrêté, etc.).

Pour plus d’informations, consultez la rubrique relative à la [configuration du fichier de définition de service (csdef) pour les sondes d’intégrité](https://msdn.microsoft.com/library/azure/jj151530.asp) ou la rubrique [Création d’un équilibreur de charge accessible sur Internet pour les services cloud](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).
 
### Que fait une sonde d’agent invité pour marquer une instance comme étant défaillante ?

Si l’agent invité ne répond pas avec HTTP 200 OK, Azure Load Balancer marque l’instance comme ne répondant pas et arrête d’envoyer du trafic vers cette instance. Azure Load Balancer continue d’effectuer un test ping sur l’instance et, si l’agent invité répond avec un HTTP 200, Azure Load Balancer recommence à envoyer le trafic vers cette instance.

Quand vous utilisez un rôle web, le code de site web s’exécute généralement dans w3wp.exe, qui n’est pas surveillé par la structure Azure ou l’agent invité. Par conséquent, les échecs dans w3wp.exe (par exemple les réponses HTTP 500) ne sont pas notifiés à l’agent invité et l’équilibreur de charge ne peut pas mettre cette instance hors service.


### Sonde HTTP personnalisée

La sonde d’équilibreur de charge HTTP personnalisée remplace la sonde de l’agent invité par défaut et vous permet de créer votre propre logique personnalisée pour déterminer l’intégrité de l’instance de rôle. L’équilibreur de charge sonde votre point de terminaison (par défaut, toutes les 15 secondes) et l’instance est prise en considération dans la rotation d’équilibreur de charge si elle répond avec le code de statut HTTP 200 dans le délai imparti (par défaut, 31 secondes). Cela peut être utile pour implémenter votre propre logique, afin de supprimer des instances de la rotation d’équilibreur de charge, par exemple en retournant un état autre que 200 si l’instance a une utilisation supérieure à 90 % du processeur. Pour les rôles web utilisant w3wp.exe, cela signifie également que vous obtenez une analyse automatique de votre site web, dans la mesure où les défaillances dans votre code de site web renvoient un état autre que 200 à la sonde d’équilibreur de charge.

>[AZURE.NOTE] La sonde HTTP personnalisée prend en charge les chemins relatifs et uniquement le protocole HTTP. HTTPS n’est pas pris en charge.


### Que fait une sonde HTTP personnalisée pour marquer une instance comme étant défaillante ? 

- L’application HTTP renvoie un code de réponse HTTP autre que 200 (par exemple, 403, 404, 500, etc..). Il s’agit d’une reconnaissance positive que l’instance d’application veut mettre immédiatement hors service.

-  Dans le cas où le serveur HTTP ne répond pas du tout après le délai d’expiration. Notez qu’en fonction de la valeur de délai d’expiration définie, cela peut induire que plusieurs requêtes de sonde restent sans réponse avant de marquer la sonde comme inactive (c’est-à-dire que des sondes SuccessFailCount sont envoyées).
- 	Quand le serveur ferme la connexion via une réinitialisation TCP.

### Sonde TCP personnalisée

Les sondes TCP établissent une connexion en effectuant une connexion en trois temps au port défini.

### Que fait une sonde TCP personnalisée pour marquer une instance comme étant défaillante ?

- Dans le cas où le serveur TCP ne répond pas du tout après le délai d’expiration. Cela dépend du nombre de demandes de sonde ayant échoué, qui ont été configurées pour rester sans réponse avant que la sonde soit marquée comme inactive.
- 	Elle reçoit une réinitialisation TCP de l’instance de rôle.

Pour comprendre comment configurer une sonde d’intégrité HTTP ou une sonde TCP, consultez [Création d’un équilibreur de charge accessible sur Internet dans Resource Manager](load-balancer-get-started-internet-arm-ps.md#create-lb-rules-nat-rules-a-probe-and-a-load-balancer).

## Rajout d’instances saines à l’équilibreur de charge

Les sondes TCP et HTTP sont considérées comme saines et marquent l’instance de rôle comme saine dans les cas suivants :

- La machine virtuelle démarre pour la première fois et l’équilibreur de charge obtient une sonde positive.
- Le nombre SuccessFailCount (voir ci-dessus) définit la valeur de sondes réussies nécessaires pour marquer l’instance de rôle comme saine. Si une instance de rôle a été supprimée, un nombre de sondes réussies consécutives égal à SuccessFailCount est requis pour marquer l’instance de rôle comme active.

>[AZURE.NOTE] Si l’intégrité d’une instance de rôle fluctue, Azure Load Balancer attend plus longtemps avant de mettre l’instance de rôle dans un état d’intégrité normal. Cette opération est effectuée via une stratégie pour protéger l’utilisateur et l’infrastructure.

## Analytique des journaux pour l’équilibreur de charge

Vous pouvez utiliser l’[analytique des journaux pour l’équilibreur de charge](load-balancer-monitor-log.md) pour vérifier l’état d’intégrité des sondes et le nombre de sondes. La journalisation peut être utilisée avec Power BI ou Operational Insights pour fournir des statistiques de l’état d’intégrité de l’équilibreur de charge.
 

<!---HONumber=AcomDC_0302_2016-->