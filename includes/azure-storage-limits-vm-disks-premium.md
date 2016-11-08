**Disques de machine virtuelle : par limites de compte**

| Ressource | Limite par défaut |
| --- | --- |
| Capacité totale des disques par compte |35 To |
| Capacité totale des instantanés par compte |10 To |
| Bande passante maximale par compte (entrée + sortie<sup>1</sup>) |<=50 Gbits/s |

<sup>1</sup>*Entrée* désigne toutes les données (requêtes) envoyées à un compte de stockage. *Sortie* désigne toutes les données (réponses) reçues d'un compte de stockage.

**Disques de machine virtuelle : par limites de disques**

| Type de disque de stockage Premium | P10 | P20 | P30 |
| --- | --- | --- | --- |
| Taille du disque |128 Go |512 Go |1024 Go (1 To) |
| Nb max. d'E/S par seconde par disque |500 |2 300 |5 000 |
| Débit max. par disque |100 Mo par seconde |150 Mo par seconde |200 Mo par seconde |
| Nombre maximal de disques par compte de stockage |280 |70 |35 |

**Disques de machine virtuelle : par limites de machines virtuelles**

| Ressource | Limite par défaut |
| --- | --- |
| Nb max. d'E/S par seconde par machine virtuelle |80 000 E/S par seconde avec la machine virtuelle GS5<sup>1</sup> |
| Débit max. par machine virtuelle |2 000 Mo/s par seconde avec la machine virtuelle GS5<sup>1</sup> |

<sup>1</sup>Consultez la section [Tailles de machine virtuelle](../articles/virtual-machines/virtual-machines-linux-sizes.md) pour connaître les autres limites de tailles de machine virtuelle.

<!---HONumber=AcomDC_0615_2016-->