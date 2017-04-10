
<!-- A-series, Av2-series, D-series, Dv2-series, DS-series*, DSv2-series* -->

Les machines virtuelles des séries A et Av2 peuvent être déployées sur différents types de matériels et processeurs. La taille est limitée, en fonction du matériel, pour offrir des performances de processeur cohérentes pour l’instance en cours d’exécution, quel que soit le matériel sur lequel elle est déployée. Pour déterminer le matériel physique sur lequel cette taille est déployée, interrogez le matériel virtuel à partir de la machine virtuelle.

Les machines virtuelles de la série D sont conçues pour exécuter des applications qui nécessitent une puissance de calcul et des performances de disque temporaire supérieures. Ces machines virtuelles se caractérisent par des processeurs plus rapides, un rapport mémoire-cœur plus élevé et un disque SSD pour le disque temporaire. Pour plus d’informations, voir l’annonce suivante sur le blog Azure : [Nouvelles tailles de machines virtuelles de la série D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)(en anglais).

La série Dv2, suite de la série D d’origine, comprend un processeur plus puissant. Le processeur de la série Dv2 est environ 35 % plus rapide que le processeur de la série D. Il est basé sur la dernière génération de processeur 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) et comporte la technologie 2.0 Intel Turbo Boost, et peut atteindre 3,1 GHz. La série Dv2 a les mêmes configurations de disque et de mémoire que la série D.

Les tailles du niveau De base sont principalement destinées aux charges de travail de développement et aux autres applications qui ne requièrent pas d’équilibrage de charge ou de mise à l’échelle automatique, ou aux machines virtuelles utilisant beaucoup de mémoire. Pour savoir quelles sont les tailles de machines virtuelles les plus appropriées pour les applications de production, consultez (Tailles des machines virtuelles)[virtual-machines-size-specs.md] et pour des informations sur la tarification des machines virtuelles, consultez [Tarification des machines virtuelles Linux](https://azure.microsoft.com/pricing/details/virtual-machines/).

> [!NOTE]
> À partir du 15 avril, toutes les machines virtuelles Azure, indépendamment de leur taille, prendront en charge au moins 2 interfaces réseau (NIC). Pour obtenir des informations spécifiques à une région, ne ratez pas les [mises à jour du service](https://azure.microsoft.com/en-us/updates/). Elles sont signalées ci-dessous par le caractère « ^ ».

## <a name="dsv2-series"></a>Séries DSv2*

ACU : 210-250

| Taille | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Disques de données max. | Débit de disque maximal avec mise en cache : E/S par seconde/ Mbits/s (taille du cache en Gio) | Débit de disque maximal sans mise en cache : E/S / Mbits/s | Cartes réseau (max)/Bande passante réseau |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3,5 |7 |2 |4 000 / 32 (43) |3 200 / 48 |2^ / Modérée |
| Standard_DS2_v2 |2 |7 |14 |4 |8 000 / 64 (86) |6 400 / 96 |2 Élevée |
| Standard_DS3_v2 |4 |14 |28 |8 |16 000 / 128 (172) |12 800 / 192 |4 Élevée |
| Standard_DS4_v2 |8 |28 |56 |16 |32 000 / 256 (344) |25 600 / 384 |8 Élevée |
| Standard_DS5_v2 |16 |56 |112 |32 |64 000 / 512 (688) |51 200 / 768 |8 Extrêmement élevé |



## <a name="dv2-series"></a>Série Dv2

ACU : 210-250

| Taille              | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Débit de disque local max : E/S par seconde / Mbits/s de lecture / Mbits/s d’écriture | Disques de données max / débit : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1_v2    | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 2 / 2 x 500                         | 2^ / Modérée                 |
| Standard_D2_v2    | 2         | 7           | 100            | 6000 / 93 / 46                                           | 4 / 4 x 500                         | 2 / Élevée                     |
| Standard_D3_v2    | 4         | 14          | 200            | 12000 / 187 / 93                                         | 8 / 8 x 500                         | 4 / Élevée                     |
| Standard_D4_v2    | 8         | 28          | 400            | 24000 / 375 / 187                                        | 16 / 16 x 500                       | 8 / Élevée                     |
| Standard_D5_v2    | 16        | 56          | 800            | 48000 / 750 / 375                                        | 32 / 32 x 500                       | 8 / Extrêmement élevée           |

<br>

## <a name="ds-series"></a>Séries DS*
| Taille | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Disques de données max. | Débit de disque local et en cache max : E/S par seconde / Mbits/s (taille du cache en Gio) | Débit de disque maximal sans mise en cache : E/S / Mbits/s | Cartes réseau (max)/Bande passante réseau |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3,5 |7 |2 |4 000 / 32 (43) |3 200 / 32 |2^ / Modérée |
| Standard_DS2 |2 |7 |14 |4 |8 000 / 64 (86) |6 400 / 64 |2 / Élevée |
| Standard_DS3 |4 |14 |28 |8 |16 000 / 128 (172) |12 800 / 128 |4 / Élevée |
| Standard_DS4 |8 |28 |56 |16 |32 000 / 256 (344) |25 600 / 256 |8 / Élevée |

<br>
## <a name="d-series"></a>Série D 

ACU : 160

| Taille         | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Débit de disque local max : E/S par seconde / Mbits/s de lecture / Mbits/s d’écriture | Disques de données max / débit : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| D1 standard  | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 2 / 2 x 500                         | 2^ / Modérée                 |
| D2 standard  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 4 / 4 x 500                         | 2 / Élevée                     |
| D3 standard  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 8 / 8 x 500                         | 4 / Élevée                     |
| D4 standard  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 16 / 16 x 500                       | 8 / Élevée                     |

<br>


## <a name="av2-series"></a>Série Av2

ACU : 100

| Taille            | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Débit de disque local max : E/S par seconde / Mbits/s de lecture / Mbits/s d’écriture | Disques de données max / débit : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2 x 500                         | 2^ / Modérée                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500                         | 2 / Modérée                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500                         | 4 / Élevée                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16 / 16 x 500                       | 8 / Élevée                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500                         | 2 / Modérée                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500                         | 4 / Élevée                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16 / 16 x 500                       | 8 / Élevée                     |

<br>

## <a name="a-series"></a>Série A

ACU : 50-100

| Taille | Cœurs d’unité centrale | Mémoire : Gio | Disque dur local : Gio | Disques de données max. | Débit de disque de données max : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0* |1 |0,768 |20 |1 |1 x 500 |2^ / Faible |
| Standard_A1 |1 |1,75 |70 |2 |2 x 500 |2^ / Modérée |
| Standard_A2 |2 |3,5 |135 |4 |4 x 500 |2^ / Modérée |
| Standard_A3 |4 |7 |285 |8 |8 x 500 |2 / Élevée |
| Standard_A4 |8 |14 |605 |16 |16 x 500 |4 / Élevée |
| Standard_A5 |2 |14 |135 |4 |4 x 500 |2^ / Modérée |
| Standard_A6 |4 |28 |285 |8 |8 x 500 |2 / Élevée |
| Standard_A7 |8 |56 |605 |16 |16 x 500 |4 / Élevée |
<br>

*La taille A0 est trop sollicitée sur le matériel physique. Pour cette taille spécifique uniquement, les autres déploiements de clients peuvent affecter les performances de la charge de travail en cours d’exécution. Les performances relatives sont décrites ci-dessous comme référence attendue, soumises à une variation approximative de 15 pour cent.

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0-A4 à l’aide de l’interface de ligne de commande et de Powershell
Dans le modèle de déploiement classique, certains noms de tailles de machines virtuelles sont légèrement différents dans Powershell et dans l’interface de ligne de commande :

* Standard_A0 est ExtraSmall (Très petit) 
* Standard_A1 est Small (Petit)
* Standard_A2 est Medium (Moyen)
* Standard_A3 est Large (Grand)
* Standard_A4 est ExtraLarge (Très grand)

## <a name="basic-a"></a>De base A

|Taille - Taille\Nom |Cœurs d’unité centrale|Mémoire|Cartes réseau (max)|Taille max. du disque temporaire |Bande passante disques de données (1 023 Go chacun)|Bande passante Nombre maximal d’opérations d’E/S par seconde (300 par disque)|
|---|---|---|---|---|---|---|
|0A0\Basic_A0|1|768 Mo|2^| 20 Go|1|1 x 300|
|A1\Basic_A1|1|1,75 Go|2^| 40 Go |2|2 x 300|
|A2\Basic_A2|2|3,5 Go|2^| 60 Go|4|4 x 300|
|A3\Basic_A3|4|7 Go|2^| 120 Go |8|8 x 300|
|A4\Basic_A4|8|14 Go|2^| 240 Go |16|16 x 300|
