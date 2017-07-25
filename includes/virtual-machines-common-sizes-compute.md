<!-- F-series, Fs-series* -->

La série F est basée sur le processeur Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz pouvant aller jusqu’à 3,1 GHz avec Intel Turbo Boost Technology 2.0. Ce sont les mêmes performances d’UC que la série Dv2 de machines virtuelles.  Affichant le coût le plus bas par heure, la série F offre le meilleur rapport prix-performances de la gamme Azure si l’on considère les unités de calcul Azure (ACU) par cœur. 

Les machines virtuelles de série F sont un excellent choix pour les charges de travail qui exigent des processeurs plus rapides, mais n’ont pas besoin d’autant de mémoire ou de SSD local par cœur de processeur.  Les charges de travail telles que l’analyse, les serveurs de jeux, les serveurs web et le traitement par lots tireront avantage de la série F.

La série Fs propose tous les avantages de la série F, en plus du stockage Premium.

## <a name="fs-series"></a>Série Fs*

ACU : 210-250

| Taille | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Disques de données max. | Débit de disque local et en cache max : E/S par seconde / Mbits/s (taille du cache en Gio) | Débit de disque maximal sans mise en cache : E/S / Mbits/s | Nombre max de cartes réseau / Performance réseau attendue (Mbits/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |2 |4 000 / 32 (12) |3 200 / 48 |2 / 750 |
| Standard_F2s |2 |4 |8 |4 |8 000 / 64 (24) |6 400 / 96 |2 / 1 500 |
| Standard_F4s |4 |8 |16 |8 |16 000 / 128 (48) |12 800 / 192 |4 / 3 000 |
| Standard_F8s |8 |16 |32 |16 |32 000 / 256 (96) |25 600 / 384 |8 / 6 000 |
| Standard_F16s |16 |32 |64 |32 |64 000 / 512 (192) |51 200 / 768 |8 / 6000-12000 &#8224; |

Mbits/s = 10^6 octets par seconde, et Gio = 1024^3 octets.

* Le débit de disque maximal possible (E/S par seconde ou Mbits/s) avec une machine virtuelle de la série Fs peut être limité par le nombre, la taille et la répartition des disques attachés.  Pour plus d’informations, consultez l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../articles/storage/storage-premium-storage.md).


<br>
## <a name="f-series"></a>Série F

ACU : 210-250

| Taille         | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Débit de disque local max : E/S par seconde / Mbits/s de lecture / Mbits/s d’écriture | Disques de données max / débit : E/S par seconde | Nombre max de cartes réseau / Performance réseau attendue (Mbits/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 2 / 2 x 500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 4 / 4 x 500                         | 2 / 1 500                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 8 / 8 x 500                         | 4 / 3 000                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 16 / 16 x 500                       | 8 / 6 000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 32 / 32 x 500                       | 8 / 6 000 à 12 000 &#8224;           |


<br>


