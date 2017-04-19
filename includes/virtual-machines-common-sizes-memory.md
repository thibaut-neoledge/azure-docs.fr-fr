

* Les séries Dv2, D, G et DS/GS sont idéales pour les applications qui exigent des processeurs plus rapides, de meilleures performances de disque local, ou qui ont des exigences de mémoire plus élevées.  Elles offrent une combinaison puissante pour de nombreuses applications professionnelles.

Les machines virtuelles de la série D sont conçues pour exécuter des applications qui nécessitent une puissance de calcul et des performances de disque temporaire supérieures. Ces machines virtuelles se caractérisent par des processeurs plus rapides, un rapport mémoire-cœur plus élevé et un disque SSD pour le disque temporaire. Pour plus d’informations, voir l’annonce suivante sur le blog Azure : [Nouvelles tailles de machines virtuelles de la série D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)(en anglais).

La série Dv2, suite de la série D d’origine, comprend un processeur plus puissant. Le processeur de la série Dv2 est environ 35 % plus rapide que le processeur de la série D. Il est basé sur la dernière génération de processeur 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) et comporte la technologie 2.0 Intel Turbo Boost, et peut atteindre 3,1 GHz. La série Dv2 a les mêmes configurations de disque et de mémoire que la série D.


## <a name="gs-series"></a>Série GS*

ACU : 180 - 240

| Taille | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Disques de données max. | Débit de disque local et en cache max : E/S par seconde / Mbits/s (taille du cache en Gio) | Débit de disque maximal sans mise en cache : E/S / Mbits/s | Cartes réseau (max)/Bande passante réseau |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 |2 |28 |56 |4 |10 000 / 100 (264) |5 000 / 125 |2 / Élevée |
| Standard_GS2 |4 |56 |112 |8 |20 000 / 200 (528) |10 000 / 250 |2 / Élevée |
| Standard_GS3 |8 |112 |224 |16 |40 000 / 400 (1 056) |20 000 / 500 |4 / Très élevée |
| Standard_GS4 |16 |224 |448 |32 |80 000 / 800 (2 112) |40 000 / 1 000 |8 / Extrêmement élevée |
| Standard_GS5** |32 |448 |896 |64 |160 000 / 1 600 (4 224) |80 000 / 2 000 |8 / Extrêmement élevée |

Mbits/s = 10^6 octets par seconde, et Gio = 1024^3 octets.

* Le débit de disque maximal possible (E/S par seconde ou Mbits/s) avec une machine virtuelle de la série GS peut être limité par le nombre, la taille et la répartition des disques attachés. Pour plus d’informations, consultez l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../articles/storage/storage-premium-storage.md). 

**L’instance G5 est isolée sur un matériel dédié à un client unique.
<br>

## <a name="g-series"></a>Série G

ACU : 180 - 240

| Taille         | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Débit de disque local max : E/S par seconde / Mbits/s de lecture / Mbits/s d’écriture | Disques de données max / débit : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 4 / 4 x 500                       | 2 / Élevée                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 8 / 8 x 500                       | 2 / Élevée                     |
| Standard_G3  | 8         | 112         | 1 536          | 24000 / 375 / 187                                        | 16 / 16 x 500                     | 4 / Très élevée                |
| Standard_G4  | 16        | 224         | 3 072          | 48000 / 750 / 375                                        | 32 / 32 x 500                     | 8 / Extrêmement élevée           |
| Standard_G5* | 32        | 448         | 6 144          | 96000 / 1500 / 750                                       | 64 / 64 x 500                     | 8 / Extrêmement élevée           |

*L’instance est isolée sur un matériel dédié à un client unique.
<br>


## <a name="dsv2-series"></a>Séries DSv2*

ACU : 210-250

| Taille | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Disques de données max. | Débit de disque maximal avec mise en cache : E/S par seconde/ Mbits/s (taille du cache en Gio) | Débit de disque maximal sans mise en cache : E/S / Mbits/s | Cartes réseau (max)/Bande passante réseau |
| --- | --- | --- | --- | --- | --- | --- | --- |
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
bits/s = 10^6 octets par seconde, et Gio = 1024^3 octets.

* Le débit de disque maximal possible (E/S par seconde ou Mbits/s) avec une machine virtuelle de la série DS peut être limité par le nombre, la taille et la répartition des disques attachés.  Pour plus d’informations, consultez l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../articles/storage/storage-premium-storage.md).

## <a name="dv2-series"></a>Série Dv2

ACU : 21 - 250

| Taille              | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Débit de disque local max : E/S par seconde / Mbits/s de lecture / Mbits/s d’écriture | Disques de données max / débit : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 4 / 4 x 500                         | 2 / Élevée                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 8 / 8 x 500                         | 4 / Élevée                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 16 / 16 x 500                       | 8 / Élevée                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 32 / 32 x 500                       | 8 / Extrêmement élevée           |
| Standard_D15_v2** | 20        | 140         | 1 000          | 60000 / 937 / 468                                        | 40 / 40 x 500                       | 8 / Extrêmement élevée*          |

* Dans certaines régions, l’accélération réseau est disponible pour la taille Standard_D15_v2. Pour plus d’informations sur l’utilisation et la disponibilité, consultez [Accelerated Networking is in Preview](https://azure.microsoft.com/updates/accelerated-networking-in-preview/) (L’accélération réseau est disponible en version préliminaire) et [Accélération réseau pour une machine virtuelle](../articles/virtual-network/virtual-network-accelerated-networking-powershell.md).

**L’instance G5 est isolée sur un matériel dédié à un client unique.

<br>

## <a name="ds-series"></a>Séries DS*

ACU : 160

| Taille | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Disques de données max. | Débit de disque maximal avec mise en cache : E/S par seconde/ Mbits/s (taille du cache en Gio) | Débit de disque maximal sans mise en cache : E/S / Mbits/s | Cartes réseau (max)/Bande passante réseau |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |4 |8 000 / 64 (72) |6 400 / 64 |2 / Élevée |
| Standard_DS12 |4 |28 |56 |8 |16 000 / 128 (144) |12 800 / 128 |4 / Élevée |
| Standard_DS13 |8 |56 |112 |16 |32 000 / 256 (288) |25 600 / 256 |8 / Élevée |
| Standard_DS14 |16 |112 |224 |32 |64 000 / 512 (576) |51 200 / 512 |8 / Très élevée |


Mbits/s = 10^6 octets par seconde, et Gio = 1024^3 octets.

* Le débit de disque maximal possible (E/S par seconde ou Mbits/s) avec une machine virtuelle de la série DS peut être limité par le nombre, la taille et la répartition des disques attachés.  Pour plus d’informations, consultez l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../articles/storage/storage-premium-storage.md).

## <a name="d-series"></a>Série D

ACU : 160

| Taille         | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Débit de disque local max : E/S par seconde / Mbits/s de lecture / Mbits/s d’écriture | Disques de données max / débit : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| D11 standard | 2         | 14          | 100            | 6000 / 93 / 46                                           | 4 / 4 x 500                         | 2 / Élevée                     |
| D12 standard | 4         | 28          | 200            | 12000 / 187 / 93                                         | 8 / 8 x 500                         | 4 / Élevée                     |
| D13 standard | 8         | 56          | 400            | 24000 / 375 / 187                                        | 16 / 16 x 500                       | 8 / Élevée                     |
| D14 standard | 16        | 112         | 800            | 48000 / 750 / 375                                        | 32 / 32 x 500                       | 8 / Très élevée                |
<br>


<br>

