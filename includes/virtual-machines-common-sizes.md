


Azure offre le choix entre plusieurs tailles standard. Voici des considérations quant à certaines de ces tailles :

* Les machines virtuelles de la série D sont conçues pour exécuter des applications qui nécessitent une puissance de calcul et des performances de disque temporaire supérieures. Ces machines virtuelles se caractérisent par des processeurs plus rapides, un rapport mémoire-cœur plus élevé et un disque SSD pour le disque temporaire. Pour plus d’informations, voir l’annonce suivante sur le blog Azure : [Nouvelles tailles de machines virtuelles de la série D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)(en anglais).
* La série Dv2, suite de la série D d’origine, comprend un processeur plus puissant. Le processeur de la série Dv2 est environ 35 % plus rapide que le processeur de la série D. Il est basé sur la dernière génération de processeur 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) et comporte la technologie 2.0 Intel Turbo Boost, et peut atteindre 3,1 GHz. La série Dv2 a les mêmes configurations de disque et de mémoire que la série D.
* La série F est basée sur le processeur Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz pouvant aller jusqu’à 3,1 GHz avec Intel Turbo Boost Technology 2.0. Ce sont les mêmes performances d’UC que la série Dv2 de machines virtuelles.  Affichant le coût le plus bas par heure, la série F offre le meilleur rapport prix-performances de la gamme Azure si l’on considère les unités de calcul Azure (ACU) par cœur. 
  
    La série F introduit également un nouveau standard de nom de taille de machine virtuelle pour Azure. Pour les tailles de machines virtuelles de cette série et à venir, la valeur numérique après la lettre du nom de la gamme correspond au nombre de cœurs de processeur. Les fonctionnalités supplémentaires, notamment l’optimisation pour le stockage premium, seront désignées par les lettres suivant le nombre de cœurs de processeur. Ce format d’affectation de noms sera utilisé pour les tailles de machines virtuelles commercialisées à l’avenir mais ne changera pas rétroactivement le nom des tailles de machines virtuelles qui sont déjà commercialisées.
* Les machines virtuelles de la série G offrent le plus de mémoire et s’exécutent sur des hôtes équipés de processeurs de la famille Intel Xeon E5 V3.
* Les machines virtuelles des séries DS, DSv2 F et GS peuvent utiliser Premium Storage, qui offre un stockage hautes performances à faible latence pour les charges de travail utilisant une quantité importante d’E/S. Ces machines virtuelles utilisent des disques SSD pour héberger les disques de la machine virtuelle et offrent également un cache de disque SSD local. Le stockage Premium est disponible dans certaines régions. Pour plus d’informations, consultez l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../articles/storage/storage-premium-storage.md).
*   Les machines virtuelles des séries A et Av2 peuvent être déployées sur différents types de matériels et processeurs. La taille est limitée, en fonction du matériel, pour offrir des performances de processeur cohérentes pour l’instance en cours d’exécution, quel que soit le matériel sur lequel elle est déployée. Pour déterminer le matériel physique sur lequel cette taille est déployée, interrogez le matériel virtuel à partir de la machine virtuelle.
* La taille A0 est trop sollicitée sur le matériel physique. Pour cette taille spécifique uniquement, les autres déploiements de clients peuvent affecter les performances de la charge de travail en cours d’exécution. Les performances relatives sont décrites ci-dessous comme référence attendue, soumises à une variation approximative de 15 pour cent.

La taille de la machine virtuelle a une incidence sur la tarification. La taille influe également sur les capacités de traitement, de mémoire et de stockage de la machine virtuelle. Les coûts de stockage sont calculés séparément en fonction des pages utilisées dans le compte de stockage. Pour plus d’informations, voir les pages [Machines virtuelles Tarification](https://azure.microsoft.com/pricing/details/virtual-machines/) et [Tarification Azure Storage](https://azure.microsoft.com/pricing/details/storage/). 

Les considérations ci-dessous peuvent vous aider à choisir une taille :

* Les tailles A8 à A11 et celles de la série H sont également appelées *instances nécessitant beaucoup de ressources système*. Le matériel qui exécute ces tailles a été conçu et optimisé pour les applications nécessitant beaucoup de ressources système et réseau, notamment les applications en cluster pour des calculs complexes, la modélisation et les simulations. La série A8-A11 utilise un processeur Intel Xeon E5-2670 cadencé à 2,6 GHZ, et la série H un processeur Intel Xeon E5-2667 v3 cadencé à 3,2 GHz. Pour plus d’informations et pour connaître les éléments à prendre en considération sur l’utilisation de ces tailles, consultez l’article [À propos de la série H et de la série A nécessitant beaucoup de ressource système](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
* Les séries Dv2, D, G et DS/GS sont idéales pour les applications qui exigent des processeurs plus rapides, de meilleures performances de disque local, ou qui ont des exigences de mémoire plus élevées.  Elles offrent une combinaison puissante pour de nombreuses applications professionnelles.
* Les machines virtuelles de série F sont un excellent choix pour les charges de travail qui exigent des processeurs plus rapides, mais n’ont pas besoin d’autant de mémoire ou de SSD local par cœur de processeur.  Les charges de travail telles que l’analyse, les serveurs de jeux, les serveurs web et le traitement par lots tireront avantage de la série F.
* Certains hôtes physiques des centres de données Azure ne prennent pas en charge les tailles de machines virtuelles élevées, comme A5 à A11. Ainsi, vous pouvez obtenir le message d’erreur **Échec de la configuration de la machine virtuelle <machine name>** ou **Échec de la création de la machine virtuelle <machine name>** pendant le redimensionnement d’une machine virtuelle existante, la création d’une machine virtuelle dans un réseau virtuel créé avant le 16 avril 2013 ou l’ajout d’une machine virtuelle à un service cloud existant. Pour découvrir les solutions de contournement pour chaque scénario de déploiement, consultez [Erreur : « Échec de la configuration de la machine virtuelle »](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) (en anglais) sur le forum d'assistance.  
* Il se peut également que votre abonnement limite le nombre de cœurs que vous pouvez déployer dans certaines familles de taille. Pour augmenter un quota, contactez le support technique Azure.

## <a name="performance-considerations"></a>Considérations relatives aux performances
Nous avons créé le concept d’unité de calcul Azure (ACU) pour permettre de comparer les performances de calcul (UC) des références Azure. Cela vous aidera à identifier facilement la référence la plus à même de répondre à vos besoins en termes de performances.  L’unité ACU est actuellement normalisée sur une machine virtuelle de petite taille (Standard_A1) correspondant à 100, et toutes les autres références représentent ensuite approximativement la rapidité avec laquelle la référence en question peut exécuter un test d’évaluation. 

> [!IMPORTANT]
> Cette unité ACU est fournie uniquement à titre indicatif.  Les résultats de votre charge de travail peuvent varier. 
> 
> 

<br>

| Famille de références | ACU/Cœur |
| --- | --- |
| [A0](#a-series) |50 |
| [A1-A4](#a-series) |100 |
| [A5-A7](#a-series) |100 |
| [A1_v2-A8_v2](#av2-series) |100 |
| [A2m_v2-A8m_v2](#av2-series) |100 |
| [A8-A11](#a-series) |225* |
| [D1-D14](#d-series) |160 |
| [D1_v2-D15_v2](#dv2-series) |210 - 250* |
| [DS1-DS14](#ds-series) |160 |
| [DS1_v2-DS15_v2](#dsv2-series) |210-250* |
| [F1-F16](#f-series) |210-250* |
| [F1s-F16s](#fs-series) |210-250* |
| [G1-G5](#g-series) |180 - 240* |
| [GS1-GS5](#gs-series) |180 - 240* |
| [H](#h-series) |290 - 300* |
| [L4s-L32s](#l-series) |180 - 240* |

Les unités ACU signalées par un astérisque (*) utilisent la technologie Intel ® Turbo pour augmenter la fréquence du processeur et améliorer les performances.  Cette amélioration des performances peut varier en fonction de la taille et de la charge de travail de la machine virtuelle, et des autres charges de travail en cours d’exécution sur le même hôte.

## <a name="size-tables"></a>Tableaux des tailles
Les tableaux ci-après indiquent les tailles et les capacités qu’elles offrent.

* La capacité de stockage est indiquée en unités de Gio ou 1 024^3 octets. Lors de la comparaison de disques mesurés en Go (1&000;^3 octets) à des disques mesurés en Gio (1&024;^3) n’oubliez pas que les indications de capacité en Gio peuvent sembler plus petites. Par exemple, 1 023 Gio = 1 098,4 Go
* Le débit de disque est mesuré en opérations d’entrée/sortie par seconde (IOPS) et Mbits/s où Mbits/s = 10^6 octets par seconde.
* Les disques de données peuvent fonctionner en mode avec ou sans mise en cache.  En cas de fonctionnement du disque de données avec mise en cache, le mode de mise en cache hôte est défini sur **ReadOnly** ou **ReadWrite**.  En cas de fonctionnement du disque de données sans mise en cache, le mode de mise en cache hôte est défini sur **Aucun**.
* La bande passante réseau maximale est la bande passante maximale agrégée allouée et affectée par type de machine virtuelle. La bande passante maximale fournit des recommandations pour la sélection du bon type de machine virtuelle afin de garantir une capacité réseau adéquate. Lors du déplacement entre Faible, Modéré, Élevé et Très élevé, le débit augmente en conséquence. Les performances réseau réelles dépendent de nombreux facteurs, notamment les charges du réseau et de l’application, ainsi que les paramètres réseau de l’application.

## <a name="a-series"></a>Série A
| Taille | Cœurs d’unité centrale | Mémoire : Gio | Disque dur local : Gio | Disques de données max. | Débit de disque de données max : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 |1 |0,768 |20 |1 |1 x 500 |1 / Faible |
| Standard_A1 |1 |1,75 |70 |2 |2 x 500 |1 / Modérée |
| Standard_A2 |2 |3,5 |135 |4 |4 x 500 |1 / Modérée |
| Standard_A3 |4 |7 |285 |8 |8 x 500 |2 / Élevée |
| Standard_A4 |8 |14 |605 |16 |16 x 500 |4 / Élevée |
| Standard_A5 |2 |14 |135 |4 |4 x 500 |1 / Modérée |
| Standard_A6 |4 |28 |285 |8 |8 x 500 |2 / Élevée |
| Standard_A7 |8 |56 |605 |16 |16 x 500 |4 / Élevée |

<br>

## <a name="a-series---compute-intensive-instances"></a>Série A - Instances de calcul intensif
Pour plus d’informations et pour connaître les éléments à prendre en compte pour l’utilisation de ces tailles, consultez [À propos des machines virtuelles de série H ou de série A nécessitant beaucoup de ressources système](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Taille | Cœurs d’unité centrale | Mémoire : Gio | Disque dur local : Gio | Disques de données max. | Débit de disque de données max : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8* |8 |56 |382 |16 |16 x 500 |2 / Élevée |
| Standard_A9* |16 |112 |382 |16 |16 x 500 |4 / Très élevée |
| Standard_A10 |8 |56 |382 |16 |16 x 500 |2 / Élevée |
| Standard_A11 |16 |112 |382 |16 |16 x 500 |4 / Très élevée |

*Prenant en charge RDMA

<br>

## <a name="av2-series"></a>Série Av2

| Taille            | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Débit de disque local max : E/S par seconde / Mbits/s de lecture / Mbits/s d’écriture | Disques de données max / débit : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2 x 500                         | 1 / Modérée                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500                         | 2 / Modérée                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500                         | 4 / Élevée                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16 / 16 x 500                       | 8 / Élevée                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500                         | 2 / Modérée                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500                         | 4 / Élevée                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16 / 16 x 500                       | 8 / Élevée                     |

## <a name="d-series"></a>Série D

| Taille         | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Débit de disque local max : E/S par seconde / Mbits/s de lecture / Mbits/s d’écriture | Disques de données max / débit : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| D1 standard  | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 2 / 2 x 500                         | 1 / Modérée                 |
| D2 standard  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 4 / 4 x 500                         | 2 / Élevée                     |
| D3 standard  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 8 / 8 x 500                         | 4 / Élevée                     |
| D4 standard  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 16 / 16 x 500                       | 8 / Élevée                     |
| D11 standard | 2         | 14          | 100            | 6000 / 93 / 46                                           | 4 / 4 x 500                         | 2 / Élevée                     |
| D12 standard | 4         | 28          | 200            | 12000 / 187 / 93                                         | 8 / 8 x 500                         | 4 / Élevée                     |
| D13 standard | 8         | 56          | 400            | 24000 / 375 / 187                                        | 16 / 16 x 500                       | 8 / Élevée                     |
| D14 standard | 16        | 112         | 800            | 48000 / 750 / 375                                        | 32 / 32 x 500                       | 8 / Très élevée                |
<br>

## <a name="dv2-series"></a>Série Dv2


| Taille              | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Débit de disque local max : E/S par seconde / Mbits/s de lecture / Mbits/s d’écriture | Disques de données max / débit : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1_v2    | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 2 / 2 x 500                         | 1 / Modérée                 |
| Standard_D2_v2    | 2         | 7           | 100            | 6000 / 93 / 46                                           | 4 / 4 x 500                         | 2 / Élevée                     |
| Standard_D3_v2    | 4         | 14          | 200            | 12000 / 187 / 93                                         | 8 / 8 x 500                         | 4 / Élevée                     |
| Standard_D4_v2    | 8         | 28          | 400            | 24000 / 375 / 187                                        | 16 / 16 x 500                       | 8 / Élevée                     |
| Standard_D5_v2    | 16        | 56          | 800            | 48000 / 750 / 375                                        | 32 / 32 x 500                       | 8 / Extrêmement élevée           |
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 4 / 4 x 500                         | 2 / Élevée                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 8 / 8 x 500                         | 4 / Élevée                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 16 / 16 x 500                       | 8 / Élevée                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 32 / 32 x 500                       | 8 / Extrêmement élevée           |
| Standard_D15_v2** | 20        | 140         | 1&000;          | 60000 / 937 / 468                                        | 40 / 40 x 500                       | 8 / Extrêmement élevée*          |

* Dans certaines régions, l’accélération réseau est disponible pour la taille Standard_D15_v2. Pour plus d’informations sur l’utilisation et la disponibilité, consultez [Accelerated Networking is in Preview](https://azure.microsoft.com/updates/accelerated-networking-in-preview/) (L’accélération réseau est disponible en version préliminaire) et [Accélération réseau pour une machine virtuelle](../articles/virtual-network/virtual-network-accelerated-networking-powershell.md).

**L’instance G5 est isolée sur un matériel dédié à un client unique.

<br>

## <a name="ds-series"></a>Séries DS*
| Taille | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Disques de données max. | Débit de disque local et en cache max : E/S par seconde / Mbits/s (taille du cache en Gio) | Débit de disque maximal sans mise en cache : E/S / Mbits/s | Cartes réseau (max)/Bande passante réseau |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3,5 |7 |2 |4 000 / 32 (43) |3 200 / 32 |1 / Modérée |
| Standard_DS2 |2 |7 |14 |4 |8 000 / 64 (86) |6 400 / 64 |2 / Élevée |
| Standard_DS3 |4 |14 |28 |8 |16 000 / 128 (172) |12 800 / 128 |4 / Élevée |
| Standard_DS4 |8 |28 |56 |16 |32 000 / 256 (344) |25 600 / 256 |8 / Élevée |
| Standard_DS11 |2 |14 |28 |4 |8 000 / 64 (72) |6 400 / 64 |2 / Élevée |
| Standard_DS12 |4 |28 |56 |8 |16 000 / 128 (144) |12 800 / 128 |4 / Élevée |
| Standard_DS13 |8 |56 |112 |16 |32 000 / 256 (288) |25 600 / 256 |8 / Élevée |
| Standard_DS14 |16 |112 |224 |32 |64 000 / 512 (576) |51 200 / 512 |8 / Très élevée |

Mbits/s = 10^6 octets par seconde, et Gio = 1024^3 octets.

* Le débit de disque maximal possible (E/S par seconde ou Mbits/s) avec une machine virtuelle de la série DS peut être limité par le nombre, la taille et la répartition des disques attachés.  Pour plus d’informations, consultez l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../articles/storage/storage-premium-storage.md).

<br>

## <a name="dsv2-series"></a>Séries DSv2*
| Taille | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Disques de données max. | Débit de disque maximal avec mise en cache : E/S par seconde/ Mbits/s (taille du cache en Gio) | Débit de disque maximal sans mise en cache : E/S / Mbits/s | Cartes réseau (max)/Bande passante réseau |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3,5 |7 |2 |4 000 / 32 (43) |3 200 / 48 |1 Modérée |
| Standard_DS2_v2 |2 |7 |14 |4 |8 000 / 64 (86) |6 400 / 96 |2 Élevée |
| Standard_DS3_v2 |4 |14 |28 |8 |16 000 / 128 (172) |12 800 / 192 |4 Élevée |
| Standard_DS4_v2 |8 |28 |56 |16 |32 000 / 256 (344) |25 600 / 384 |8 Élevée |
| Standard_DS5_v2 |16 |56 |112 |32 |64 000 / 512 (688) |51 200 / 768 |8 Extrêmement élevé |
| Standard_DS11_v2 |2 |14 |28 |4 |8 000 / 64 (72) |6 400 / 96 |2 Élevée |
| Standard_DS12_v2 |4 |28 |56 |8 |16 000 / 128 (144) |12 800 / 192 |4 Élevée |
| Standard_DS13_v2 |8 |56 |112 |16 |32 000 / 256 (288) |25 600 / 384 |8 Élevée |
| Standard_DS14_v2 |16 |112 |224 |32 |64 000 / 512 (576) |51 200 / 768 |8 Extrêmement élevé |
| Standard_DS15_v2*** |20 |140 |280 |40 |80 000 / 640 (720) |64 000 / 960 |8 / Extrêmement élevée** |

Mbits/s = 10^6 octets par seconde, et Gio = 1024^3 octets.

* Le débit de disque maximal possible (E/S par seconde ou Mbits/s) avec une machine virtuelle de la série DSv2 peut être limité par le nombre, la taille et la répartition des disques attachés.  Pour plus d’informations, consultez l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../articles/storage/storage-premium-storage.md).

** Dans certaines régions, l’accélération réseau est disponible pour la taille Standard_DS15_v2. Pour plus d’informations sur l’utilisation et la disponibilité, consultez [Accelerated Networking is in Preview](https://azure.microsoft.com/updates/accelerated-networking-in-preview/) (L’accélération réseau est disponible en version préliminaire) et [Accélération réseau pour une machine virtuelle](../articles/virtual-network/virtual-network-accelerated-networking-powershell.md).

***L’instance G5 est isolée sur un matériel dédié à un client unique.
<br>

## <a name="f-series"></a>Série F

| Taille         | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Débit de disque local max : E/S par seconde / Mbits/s de lecture / Mbits/s d’écriture | Disques de données max / débit : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 2 / 2 x 500                         | 1 / Modérée                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 4 / 4 x 500                         | 2 / Élevée                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 8 / 8 x 500                         | 4 / Élevée                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 16 / 16 x 500                       | 8 / Élevée                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 32 / 32 x 500                       | 8 / Extrêmement élevée           |
<br>

## <a name="fs-series"></a>Série Fs*
| Taille | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Disques de données max. | Débit de disque local et en cache max : E/S par seconde / Mbits/s (taille du cache en Gio) | Débit de disque maximal sans mise en cache : E/S / Mbits/s | Cartes réseau (max)/Bande passante réseau |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |2 |4 000 / 32 (12) |3 200 / 48 |1 / Modérée |
| Standard_F2s |2 |4 |8 |4 |8 000 / 64 (24) |6 400 / 96 |2 / Élevée |
| Standard_F4s |4 |8 |16 |8 |16 000 / 128 (48) |12 800 / 192 |4 / Élevée |
| Standard_F8s |8 |16 |32 |16 |32 000 / 256 (96) |25 600 / 384 |8 / Élevée |
| Standard_F16s |16 |32 |64 |32 |64 000 / 512 (192) |51 200 / 768 |8 / Extrêmement élevée |

Mbits/s = 10^6 octets par seconde, et Gio = 1024^3 octets.

* Le débit de disque maximal possible (E/S par seconde ou Mbits/s) avec une machine virtuelle de la série Fs peut être limité par le nombre, la taille et la répartition des disques attachés.  Pour plus d’informations, consultez l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../articles/storage/storage-premium-storage.md).

<br>

## <a name="g-series"></a>Série G

| Taille         | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Débit de disque local max : E/S par seconde / Mbits/s de lecture / Mbits/s d’écriture | Disques de données max / débit : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 4 / 4 x 500                       | 1 / Élevée                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 8 / 8 x 500                       | 2 / Élevée                     |
| Standard_G3  | 8         | 112         | 1&536;          | 24000 / 375 / 187                                        | 16 / 16 x 500                     | 4 / Très élevée                |
| Standard_G4  | 16        | 224         | 3&072;          | 48000 / 750 / 375                                        | 32 / 32 x 500                     | 8 / Extrêmement élevée           |
| Standard_G5* | 32        | 448         | 6&144;          | 96000 / 1500 / 750                                       | 64 / 64 x 500                     | 8 / Extrêmement élevée           |

*L’instance est isolée sur un matériel dédié à un client unique.
<br>

## <a name="gs-series"></a>Série GS*
| Taille | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Disques de données max. | Débit de disque local et en cache max : E/S par seconde / Mbits/s (taille du cache en Gio) | Débit de disque maximal sans mise en cache : E/S / Mbits/s | Cartes réseau (max)/Bande passante réseau |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 |2 |28 |56 |4 |10 000 / 100 (264) |5 000 / 125 |1 / Élevée |
| Standard_GS2 |4 |56 |112 |8 |20 000 / 200 (528) |10 000 / 250 |2 / Élevée |
| Standard_GS3 |8 |112 |224 |16 |40 000 / 400 (1 056) |20 000 / 500 |4 / Très élevée |
| Standard_GS4 |16 |224 |448 |32 |80 000 / 800 (2 112) |40 000 / 1 000 |8 / Extrêmement élevée |
| Standard_GS5** |32 |448 |896 |64 |160 000 / 1 600 (4 224) |80 000 / 2 000 |8 / Extrêmement élevée |

Mbits/s = 10^6 octets par seconde, et Gio = 1024^3 octets.

* Le débit de disque maximal possible (E/S par seconde ou Mbits/s) avec une machine virtuelle de la série GS peut être limité par le nombre, la taille et la répartition des disques attachés. Pour plus d’informations, consultez l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../articles/storage/storage-premium-storage.md). 

**L’instance G5 est isolée sur un matériel dédié à un client unique.
<br>

## <a name="h-series"></a>Série H
Les machines virtuelles de la série H sont des machines virtuelles de calcul haute performance de nouvelles génération, destinées à répondre à des besoins de calcul de haut niveau, par exemple en relation avec la modélisation moléculaire et la dynamique des fluides. Les machines virtuelles à 8 et 16 cœurs reposent sur la technologie de processeur Intel Haswell E5-2667 V3 avec mémoire DDR4 et stockage SSD local. 

En plus de la puissance substantielle du processeur, la série H offre différentes options pour des réseaux RDMA à faible latence, en utilisant FDR InfiniBand et plusieurs configurations de mémoire pouvant satisfaire des exigences de calcul nécessitant une mémoire conséquente.

Pour plus d’informations et pour connaître les éléments à prendre en compte pour l’utilisation de ces tailles, consultez [À propos des machines virtuelles de série H ou de série A nécessitant beaucoup de ressources système](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

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


## <a name="ls-series"></a>Série Ls* 

La série Ls est optimisée pour les charges de travail qui requièrent un stockage local à faible latence, comme les bases de données NoSQL (Cassandra, MongoDB, Cloudera et Redis). La série Ls offre jusqu’à 32 cœurs de processeur, grâce à la [Famille de processeurs Intel® Xeon® E5 v3](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Les performances du processeur sont les mêmes que pour la série G/GS et elles sont associées à 8 Gio de mémoire par cœur de processeur.  

 
| Taille          | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Disques de données max. | Débit de disque maximal avec mise en cache : E/S par seconde/ Mbits/s (taille du cache en Gio) | Débit de disque maximal sans mise en cache : E/S / Mbits/s | Cartes réseau (max)/Bande passante réseau | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s  | 4    | 32   | 678   | 8              | NA / NA (0)          | 5 000 / 125                               | 2 / Élevée       | 
| Standard_L8s  | 8    | 64   | 1,388 | 16             | NA / NA (0)          | 10 000 / 250                              | 4 / Très élevée  | 
| Standard_L16s | 16   | 128  | 2,807 | 32             | NA / NA (0)          | 20 000 / 500                              | 8 / Extrêmement élevée | 
| Standard_L32s** | 32   | 256  | 5,630 | 64             | NA / NA (0)          | 40 000 / 1 000                            | 8 / Extrêmement élevée | 
 
Mbits/s = 10^6 octets par seconde, et Gio = 1024^3 octets. 

* Le débit de disque maximal possible (E/S par seconde ou Mbits/s) avec une machine virtuelle de la série Ls peut être limité par le nombre, la taille et la répartition des disques attachés. Pour plus d’informations, consultez l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../articles/storage/storage-premium-storage.md). 

**L’instance G5 est isolée sur un matériel dédié à un client unique.



## <a name="n-series"></a>Série N
Les tailles NC et NV sont également appelées instances compatibles GPU. Il s’agit de machines virtuelles spécialisées qui incluent des cartes GPU NVIDIA, optimisées pour différents scénarios et cas d’utilisation. Les tailles NV sont optimisées et conçues pour la visualisation à distance, la diffusion en continu, les jeux, le codage et les scénarios VDI utilisant des infrastructures comme OpenGL et DirectX. Les tailles NC sont optimisées pour les applications nécessitant des ressources réseau et de calcul importantes, les algorithmes, notamment les applications et simulations CUDA et OpenCL. 


### <a name="nv-instances"></a>Instances NV
Les instances NV sont alimentées par une carte GPU NVIDIA Tesla M60 et NVIDIA GRID pour les applications de bureautique accélérées et les bureaux virtuels où les clients pourront visualiser leurs données ou simulations. Les utilisateurs pourront de visualiser leurs flux de travail nécessitant beaucoup de graphismes sur les instances NV afin d’obtenir des fonctionnalités graphiques de qualité supérieure et exécuter par ailleurs des charges de travail simple précision comme le codage et le rendu. Le modèle Tesla M60 offre 4 096 cœurs CUDA dans une conception à double GPU avec jusqu'à 36 flux H.264 1080p. 


| Taille | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | GPU |
| --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 |
| Standard_NV12 |12 |112 |680 | 2 |
| Standard_NV24 |24 |224 |1&440; | 4 |

1 GPU = une moitié de carte M60.

**Systèmes d’exploitation pris en charge**

* Windows Server 2016, Windows Server 2012 R2 - consultez [Configuration du pilote série N pour Windows](../articles/virtual-machines/virtual-machines-windows-n-series-driver-setup.md)

### <a name="nc-instances"></a>Instances NC
Les instances NC sont alimentées par une carte NVIDIA Tesla K80. Les utilisateurs peuvent désormais exploiter plus rapidement leurs données en tirant parti de CUDA pour les applications d’exploration d’énergie, de simulations de crash, de rendu avec lancer de rayon, de formation approfondie et bien plus encore. Le module Tesla K80 offre 4 992 cœurs CUDA avec une conception à double GPU, jusqu'à 2,91 téraflops double précision et 8,93 téraflops simple précision.

| Taille | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | GPU |
| --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 |
| Standard_NC24 |24 |224 | 1&440; | 4 |
| Standard_NC24r* |24 |224 | 1&440; | 4 |

1 GPU = une moitié de carte K80.

*Prenant en charge RDMA

**Systèmes d’exploitation pris en charge**

* Windows Server 2016, Windows Server 2012 R2 - consultez [Configuration du pilote série N pour Windows](../articles/virtual-machines/virtual-machines-windows-n-series-driver-setup.md)
* Ubuntu 16.04 LTS - consultez [Configuration du pilote série N pour Linux](../articles/virtual-machines/virtual-machines-linux-n-series-driver-setup.md)

<br>

## <a name="notes-standard-a0---a4-using-cli-and-powershell"></a>Remarques : Standard A0 - A4 à l’aide de l’interface de ligne de commande et de Powershell
Dans le modèle de déploiement classique, certains noms de tailles de machines virtuelles sont légèrement différents dans Powershell et dans l’interface de ligne de commande :

* Standard_A0 est ExtraSmall (Très petit) 
* Standard_A1 est Small (Petit)
* Standard_A2 est Medium (Moyen)
* Standard_A3 est Large (Grand)
* Standard_A4 est ExtraLarge (Très grand)

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur l’ [abonnement Azure et les limites, quotas et contraintes des services](../articles/azure-subscription-service-limits.md).
* Pour des charges de travail telles que le calcul haute performance (HPC), consultez [À propos des machines virtuelles de série H et de série A nécessitant beaucoup de ressources système](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .

