#### Limites d’ExpressRoute

Les limites suivantes s’appliquent aux ressources ExpressRoute par abonnement.

| Ressource | Limite par défaut |
|---|---|
| Circuits ExpressRoute par abonnement | 10 |
| Circuits ExpressRoute par région par abonnement pour ARM | 10 |
| Nombre maximal d’itinéraires pour l’homologation privée Azure avec ExpressRoute Standard | 4 000 |
| Nombre maximal d’itinéraires pour l’homologation privée Azure avec le module complémentaire ExpressRoute Premium | 10 000 |
| Nombre maximal d’itinéraires pour l’homologation publique Azure avec ExpressRoute Standard | 200 |
| Nombre maximal d’itinéraires pour l’homologation publique Azure avec le module complémentaire ExpressRoute Premium | 200 |
| Nombre maximal d’itinéraires pour l’homologation Microsoft Azure avec ExpressRoute Standard | 200 |
| Nombre maximal d’itinéraires pour l’homologation Microsoft Azure avec le module complémentaire ExpressRoute Premium | 200 |
| Nombre de liens de réseau virtuel autorisés par circuit ExpressRoute | voir le tableau ci-dessous |

#### Nombre de réseaux virtuels par circuit ExpressRoute

| **Taille du circuit** | **Nombre de liens de réseau virtuel de la configuration pour la version standard** | **Nombre de liens de réseau virtuel avec le module complémentaire Premium** |
|---|---|---|
| 10 Mbits/s | 10 | Non pris en charge |
| 50 Mbits/s | 10 | 20 |
| 100 Mbits/s | 10 | 25 |
| 200 Mbits/s | 10 | 25 |
| 500 Mbits/s | 10 | 40 |
| 1 Gbit/s | 10 | 50 |
| 2 Gbit/s | 10 | 60 |
| 5 Gbit/s | 10 | 75 |
| 10 Gbits/s | 10 | 100 |

<!---HONumber=Oct15_HO4-->