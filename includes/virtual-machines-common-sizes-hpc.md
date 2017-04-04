<!-- A-series - compute-intensive instances, H-series -->

Les tailles A8 à A11 et celles de la série H sont également appelées *instances nécessitant beaucoup de ressources système*. Le matériel qui exécute ces tailles a été conçu et optimisé pour les applications nécessitant beaucoup de ressources système et réseau, notamment les applications en cluster pour des calculs complexes, la modélisation et les simulations. La série A8-A11 utilise un processeur Intel Xeon E5-2670 cadencé à 2,6 GHZ, et la série H un processeur Intel Xeon E5-2667 v3 cadencé à 3,2 GHz. 

Les machines virtuelles de la série H sont des machines virtuelles de calcul haute performance de nouvelles génération, destinées à répondre à des besoins de calcul de haut niveau, par exemple en relation avec la modélisation moléculaire et la dynamique des fluides. Les machines virtuelles à 8 et 16 cœurs reposent sur la technologie de processeur Intel Haswell E5-2667 V3 avec mémoire DDR4 et stockage SSD local. 

En plus de la puissance substantielle du processeur, la série H offre différentes options pour des réseaux RDMA à faible latence, en utilisant FDR InfiniBand et plusieurs configurations de mémoire pouvant satisfaire des exigences de calcul nécessitant une mémoire conséquente.



## <a name="h-series"></a>Série H

ACU : 290-300

| Taille | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Disques de données max. | Débit de disque max : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1 000 |16 |16 x 500 |2 / Élevée |
| Standard_H16 |16 |112 |2000 |32 |32 x 500 |4 / Très élevée |
| Standard_H8m |8 |112 |1 000 |16 |16 x 500 |2 / Élevée |
| Standard_H16m |16 |224 |2000 |32 |32 x 500 |4 / Très élevée |
| Standard_H16r* |16 |112 |2000 |32 |32 x 500 |4 / Très élevée |
| Standard_H16mr* |16 |224 |2000 |32 |32 x 500 |4 / Très élevée |

*Prenant en charge RDMA

<br>



## <a name="a-series---compute-intensive-instances"></a>Série A - Instances de calcul intensif

ACU : 225

| Taille | Cœurs d’unité centrale | Mémoire : Gio | Disque dur local : Gio | Disques de données max. | Débit de disque de données max : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8* |8 |56 |382 |16 |16 x 500 |2 / Élevée |
| Standard_A9* |16 |112 |382 |16 |16 x 500 |4 / Très élevée |
| Standard_A10 |8 |56 |382 |16 |16 x 500 |2 / Élevée |
| Standard_A11 |16 |112 |382 |16 |16 x 500 |4 / Très élevée |

*Prenant en charge RDMA

<br>



