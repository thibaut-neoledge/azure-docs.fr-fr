<properties
   pageTitle="Configuration de routage requise pour ExpressRoute | Microsoft Azure"
   description="Cette page détaille les conditions nécessaires à la configuration et à la gestion du routage pour les circuits ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2015"
   ms.author="cherylmc"/>


# Configuration requise pour le routage ExpressRoute  

Pour vous connecter aux services de cloud Microsoft à l'aide d'ExpressRoute, vous devez configurer et gérer le routage. Certains fournisseurs de connectivité proposent la configuration et la gestion du routage comme un service géré. Vérifiez auprès de votre fournisseur de connectivité s’il offre un tel service. Si ce n'est pas le cas, vous devez respecter les conditions décrites ci-dessous.

Reportez-vous à l’article [Circuit et domaines de routage](expressroute-circuit-peerings.md) pour obtenir une description des sessions de routage qui doivent être configurées afin d’établir la connectivité.

**Remarque :** Microsoft ne prend pas en charge les protocoles de redondance de routeur (HSRP, VRRP, par exemple) pour les configurations à haute disponibilité. Nous nous appuyons sur une paire de sessions BGP par homologation pour la haute disponibilité.

## Adresses IP pour les homologations

Vous devez réserver quelques blocs d'adresses IP pour configurer le routage entre votre réseau et les routeurs Microsoft Enterprise Edge (MSEE) . Cette section fournit une liste des conditions requises et décrit les règles relatives à la façon dont ces adresses IP doivent être acquises et utilisées.

### Adresses IP pour l'homologation privée Azure

Pour configurer les homologations, vous pouvez utiliser des adresses IP privées ou publiques. La plage d'adresses utilisée pour configurer des routages ne doit pas chevaucher les plages d'adresses utilisées pour créer des réseaux virtuels dans Azure.

 - Vous devez réserver un sous-réseau /29 ou deux sous-réseaux /30 pour les interfaces de routage.
 - Les sous-réseaux utilisés pour le routage peuvent être des adresses IP privées ou publiques.
 - Les sous-réseaux ne doivent pas entrer en conflit avec la plage réservée par le client pour une utilisation dans le cloud Microsoft.
 - Si un sous-réseau /29 est utilisé, il est subdivisé en deux sous-réseaux /30. 
 - Le premier sous-réseau /30 sera utilisé pour le lien principal, et le second sous-réseau /30 servira pour le lien secondaire.
 - Pour chacun des sous-réseaux /30, vous devez utiliser la première adresse IP du sous-réseau /30 sur votre routeur. Microsoft utilisera la deuxième adresse IP du sous-réseau /30 configurer une session BGP.
 - Vous devez configurer les deux sessions BGP pour que notre [contrat SLA de disponibilité](http://azure.microsoft.com/support/legal/sla/) soit valide.  

#### Exemple pour l'homologation privée

Si vous choisissez d'utiliser un sous-réseau a.b.c.d/29 pour configurer l'homologation, il sera scindé en deux sous-réseaux /30. Dans l'exemple ci-dessous, nous examinerons l’utilisation du sous-réseau a.b.c.d/29.

a.b.c.d/29 sera scindé en a.b.c.d/30 et a.b.c.d+4/30 puis transmis à Microsoft via les API d’approvisionnement. Vous utiliserez a.b.c.d+1 comme l'IP VRF pour le PE principal et Microsoft utilisera a.b.c.d+2 comme IP VRF pour le MSEE principal. Vous allez utiliser a.b.c.d+5 comme l'IP VRF pour le PE secondaire et Microsoft utilisera a.b.c.d+6 IP VRF pour le MSEE secondaire.

Prenons le cas où vous avez sélectionné 192.168.100.128/29 pour configurer l'homologation privée. 192.168.100.128/29 inclut les adresses de 192.168.100.128 à 192.168.100.133, parmi lesquelles :

- 192\.168.100.128/30 sera attribuée à link1, et le fournisseur utilisera 192.168.100.129 tandis que Microsoft utilisera 192.168.100.130.
- 192\.168.100.132/30 sera attribuée à link2, et le fournisseur utilisera 192.168.100.133 tandis que Microsoft utilisera 192.168.100.134.

### Adresses IP pour homologations publiques Azure et Microsoft

Vous devez utiliser des adresses IP publiques que vous possédez pour configurer les sessions BGP. Microsoft doit être en mesure de vérifier la propriété des adresses IP via les Registres Internet de routage régional (RIR) et les Registres Internet de routage (IRR).

- Vous devez utiliser un sous-réseau unique /29 ou deux sous-réseaux /30 afin de configurer l'homologation BGP pour chaque homologation par circuit ExpressRoute (si vous en avez plusieurs). 
- Si un sous-réseau /29 est utilisé, il est subdivisé en deux sous-réseaux /30. 
	- Le premier sous-réseau /30 sera utilisé pour le lien principal, et le second sous-réseau /30 servira pour le lien secondaire.
	- Pour chacun des sous-réseaux /30, vous devez utiliser la première adresse IP du sous-réseau /30 sur votre routeur. Microsoft utilisera la deuxième adresse IP du sous-réseau /30 configurer une session BGP.
	- Vous devez configurer les deux sessions BGP pour que notre [contrat SLA de disponibilité](http://azure.microsoft.com/support/legal/sla/) soit valide.

Assurez-vous que votre adresse IP et votre numéro AS sont enregistrés à votre nom dans l’un des registres répertoriés ci-dessous.

- [ARIN](https://www.arin.net/)
- [APNIC](https://www.apnic.net/)
- [AFRINIC](https://www.afrinic.net/)
- [LACNIC](http://www.lacnic.net/)
- [RIPE NCC](https://www.ripe.net/)
- [RADB](http://www.radb.net/)
- [ALTDB](http://altdb.net/)
- [LEVEL3](rr.Level3.net)


## Échange de routage dynamique

L’échange de routage s’effectuera via le protocole eBGP. Des sessions EBGP sont établies entre les MSEE et les routeurs. L'authentification des sessions BGP n'est pas obligatoire. Si nécessaire, un hachage MD5 peut être configuré. Examinez le workflow de configuration de routage pour plus d'informations sur la configuration des sessions BGP.

## Numéros système autonomes

Microsoft utilisera le numéro AS 12076 pour les homologations publiques Azure, privées Azure et Microsoft. Nous avons réservé le numéro AS 65515 pour un usage interne. Les numéros AS 16 bits et 32 bits sont pris en charge. Vous pouvez utiliser des numéros AS privés pour l'homologation privée Azure. Vous devez utiliser des numéros AS enregistrés à votre nom pour les homologations publiques Azure et Microsoft.

Il n'existe aucune exigence concernant une symétrie de transfert des données. Les chemins d’envoi et de réception peuvent transiter par différentes paires de routeurs. Les routages identiques doivent être publiés des deux côtés sur plusieurs paires de circuits vous appartenant. Les métriques de routage n’ont pas besoin d’être identiques.

## Agrégation de routages et limites de préfixes

Nous prenons en charge jusqu'à 4 000 préfixes qui nous sont proposés via l'homologation privée Azure. Ce chiffre peut être augmenté jusqu’à 10 000 préfixes si le module complémentaire ExpressRoute premium est activé. Nous acceptons jusqu'à 200 préfixes par session BGP pour les homologations publiques Azure et Microsoft.

La session BGP s’arrêtera si le nombre de préfixes dépasse la limite. Nous accepterons les routages par défaut uniquement sur le lien d'homologation privée. Le fournisseur doit filtrer les adresses IP de routage et privées par défaut (RFC 1918) des chemins d’homologation publique Azure et Microsoft.

## Routage de transit et routage entre régions

ExpressRoute ne peut pas être configuré comme des routeurs de transit. Vous devez vous appuyer sur votre fournisseur de connectivité pour les services de routage de transit.

## Publication des routages par défaut

Les routages par défaut sont autorisés uniquement sur les sessions d'homologation privées Azure. Dans ce cas, nous acheminerons tout le trafic des réseaux virtuels associés vers votre réseau. La publication de routages par défaut dans l'homologation privée entraîne le blocage du chemin Internet à partir d’Azure. Vous devez compter sur votre matériel edge d'entreprise afin d’acheminer le trafic depuis et vers Internet pour les services hébergés dans Azure.

 Pour activer la connectivité avec d'autres services Azure et services d'infrastructure, vous devez vous assurer qu'un des éléments suivants est en place :

 - L’homologation publique Azure est activée pour acheminer le trafic vers les points de terminaison publics
 - Vous utilisez le routage défini par l’utilisateur pour permettre la connectivité Internet pour chaque sous-réseau nécessitant une connectivité Internet définie par l'utilisateur.

**Remarque :** la publication des routages par défaut arrêtera Windows et toute autre activation de licence de machine virtuelle. Suivez les instructions [ici](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) pour contourner ce problème.

## Prise en charge des communautés BGP

Cette section fournit une vue d'ensemble de l'utilisation des communautés BGP avec ExpressRoute. Microsoft publiera des routages sur les chemins d’homologation publiques et Microsoft avec des routages marqués à l’aide des valeurs de communauté appropriées. La logique de cette procédure et les détails concernant les valeurs de la communauté sont décrits ci-dessous. Cependant, Microsoft ignorera toutes les valeurs de communauté marquées pour des itinéraires qui lui sont proposés.

Si vous vous connectez à Microsoft via ExpressRoute dans n’importe quel emplacement d’homologation d’une région géopolitique, vous aurez accès à tous les services de cloud Microsoft de toutes les régions situées dans les limites géopolitiques.

Par exemple, si vous êtes connecté à Microsoft à Amsterdam via ExpressRoute, vous aurez accès à tous les services de cloud Microsoft hébergés en Europe du Nord et en Europe occidentale.

Reportez-vous à la page [Partenaires ExpressRoute et emplacements d'homologation](expressroute-locations.md) pour obtenir une liste détaillée des régions géopolitiques, des zones Azure associées et des emplacements d’homologation ExpressRoute correspondants.

Vous pouvez acheter plusieurs circuits ExpressRoute par région géopolitique. Le fait de disposer de plusieurs connexions vous offre des avantages significatifs en termes de haute disponibilité en raison de la redondance géographique. Si vous avez plusieurs circuits ExpressRoute, vous recevrez le même jeu de préfixes publiés par Microsoft sur les chemins d'homologation publiques et Microsoft. Cela signifie que vous disposez de plusieurs chemins de leur réseau vers Microsoft. Vous risquez ainsi de prendre des décisions de routage non optimales au sein de votre réseau. Et par conséquent, vous risquez de rencontrer des problèmes de connectivité non optimale avec différents services.

Microsoft marquera les préfixes publiés via l'homologation publique et l’homologation Microsoft avec les valeurs de communauté BGP appropriées indiquant la région dans laquelle les préfixes sont hébergés. Vous pouvez compter sur les valeurs de communauté pour prendre des décisions de routage avisées et offrir aux clients un routage optimal.

| **Région géopolitique** | **Région Microsoft Azure (s'applique également à Office 365)** | **Valeur de communauté BGP** |
|---|---|---|
| **États-Unis** |	Est des États-Unis | 12076:3004 |
| | Est des États-Unis 2 | 12076:3005 |
| | Ouest des États-Unis | 12076:3006 |
| | Centre des États-Unis | 12076:3009 |
| | Nord du centre des États-Unis | 12076:3007 |
| | Sud du centre des États-Unis | 12076:3008 |
| **Amérique du Sud** | Sud du Brésil | 12076:3014 |
| **Europe** | Europe du Nord | 12076:3003 |
| | Europe de l'Ouest | 12076:3002 |
| **Asie-Pacifique** | Asie de l'Est | 12076:3010 |
| | Asie du Sud-Est | 12076:3011 |
| **Japon** | Est du Japon | 12076:3012 |
| | Ouest du Japon | 12076:3013 |
| **Australie** | Est de l’Australie | 12076:3015 |
| | Sud-est de l’Australie | 12076:3016 |
| **Inde** | Sud de l'Inde | 12076:3019 |
| | Inde-Ouest | 12076:3018 |
| | Inde-Centre | 12076:3017 |

Tous les routages publiés par Microsoft seront marqués avec la valeur de communauté appropriée.


Par ailleurs, Microsoft marquera également des préfixes basés sur le service auquel ils appartiennent. Cela s'applique uniquement à l'homologation Microsoft. Le tableau ci-dessous fournit un mappage d’un service à la valeur de communauté BGP.

| **Service** |	**Valeur de communauté BGP** |
|---|---|
| **Microsoft Exchange** | 12076:5010 |
| **SharePoint** | 12076:5020 |
| **Skype Entreprise** | 12076:5030 |
| **CRM Online** | 12076:5040 |
| **Autres services Office 365** | 12076:5100 |
| **Préfixes globaux / Anycast** | 12076:5200 |


### Manipulation des préférences de routage

Microsoft ignore les valeurs de communauté BGP que vous définissez. Vous devez configurer une paire de sessions BGP par homologation afin de garantir que les conditions requises pour le [contrat SLA de disponibilité](http://azure.microsoft.com/support/legal/sla/) sont remplies. Vous pouvez toutefois configurer votre réseau pour préférer un lien plutôt qu’un autre en vous appuyant sur les techniques de manipulation de routage BGP standard. Vous pouvez appliquer différentes préférences locales BGP à chaque lien afin de favoriser un chemin d'accès plutôt qu’un autre de votre réseau vers Microsoft. Vous pouvez ajouter le chemin aux publications de routage afin de forcer le flux de trafic de Microsoft vers votre réseau.

## Étapes suivantes

- Configurez votre connexion ExpressRoute.
	- [Création d’un circuit ExpressRoute](expressroute-howto-circuit-classic.md)
	- [Configuration du routage](expressroute-howto-routing-classic.md)
	- [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-classic.md)

<!---HONumber=Oct15_HO1-->