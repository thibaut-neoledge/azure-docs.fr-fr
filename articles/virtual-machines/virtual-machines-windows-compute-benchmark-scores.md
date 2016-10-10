<properties
 pageTitle="Calcul des scores de test d’évaluation pour les machines virtuelles Windows | Microsoft Azure"
 description="Comparer les scores de test d’évaluation de calcul SPECint pour les machines virtuelles Azure exécutant Windows Server"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/22/2016"
 ms.author="danlep"/>

# Calcul des scores de test d’évaluation pour les machines virtuelles Windows

Les scores de test d’évaluation SPECInt suivants montrent les performances de calcul pour la gamme de machines virtuelles hautes performances d’Azure exécutant Windows Server. Les scores de test d’évaluation de calcul sont également disponibles pour [les machines virtuelles Linux](virtual-machines-linux-compute-benchmark-scores.md).



## Série A - Calcul intensif


Taille | Processeurs virtuels | Nœuds NUMA | UC | Exécutions | Taux de base moy. | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_A8 | 8 | 1 | Processeur Intel Xeon E5-2670 0 à 2,6 GHz | 10 | 236\.1 | 1\.1
Standard\_A9 | 16 | 2 | Processeur Intel Xeon E5-2670 0 à 2,6 GHz | 10 | 450\.3 | 7\.0
Standard\_A10 | 8 | 1 | Processeur Intel Xeon E5-2670 0 à 2,6 GHz | 5 | 235\.6 | 0\.9
Standard\_A11 | 16 | 2 | Processeur Intel Xeon E5-2670 0 à 2,6 GHz |7 | 454\.7 | 4\.8

## Série Dv2


Taille | Processeurs virtuels | Nœuds NUMA | UC | Exécutions | Taux de base moy. | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_D1\_v2 | 1 | 1 | Intel Xeon E5-2673 v3 à 2,4 GHz | 83 | 36\.6 | 2\.6
Standard\_D2\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 à 2,4 GHz | 27 | 70\.0 | 3\.7
Standard\_D3\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 à 2,4 GHz | 19 | 130\.5 | 4\.4
Standard\_D4\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 à 2,4 GHz | 19 | 238\.1 | 5\.2
Standard\_D5\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 à 2,4 GHz | 14 | 460\.9 | 15\.4
Standard\_D11\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 à 2,4 GHz | 19 | 70\.1 | 3\.7
Standard\_D12\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 à 2,4 GHz | 2 | 132\.0 | 1\.4
Standard\_D13\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 à 2,4 GHz | 17 | 235\.8 | 3\.8
Standard\_D14\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 à 2,4 GHz | 15 | 460\.8 | 6\.5


## Série G, Série GS


Taille | Processeurs virtuels | Nœuds NUMA | UC | Exécutions | Taux de base moy. | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_G1, Standard\_GS1 | 2 | 1 | Intel Xeon E5-2698B v3 à 2 GHz | 31 | 71\.8 | 6\.5
Standard\_G2, Standard\_GS2 | 4 | 1 | Intel Xeon E5-2698B v3 à 2 GHz | 5 | 133\.4 | 13\.0
Standard\_G3, Standard\_GS3 | 8 | 1 | Intel Xeon E5-2698B v3 à 2 GHz | 6 | 242\.3 | 6\.0
Standard\_G4, Standard\_GS4 | 16 | 1 | Intel Xeon E5-2698B v3 à 2 GHz | 15 | 398\.9 | 6\.0
Standard\_G5, Standard\_GS5 | 32 | 2 | Intel Xeon E5-2698B v3 à 2 GHz | 22 | 762\.8 | 3\.7

## Série H

Taille | Processeurs virtuels | Nœuds NUMA | UC | Exécutions | Itérations/s | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_H8 | 8 | 1 | Intel Xeon E5-2667 v3 à 3,2 GHz | 5 | 297,4 | 0\.9
Standard\_H16 | 16 | 2 | Intel Xeon E5-2667 v3 à 3,2 GHz | 5 | 575,8 | 6,8
Standard\_H8m | 8 | 1 | Intel Xeon E5-2667 v3 à 3,2 GHz | 5 | 297,0 | 1\.2
Standard\_H16m | 16 | 2 | Intel Xeon E5-2667 v3 à 3,2 GHz | 5 | 572,2 | 3\.9
Standard\_H16r | 16 | 2 | Intel Xeon E5-2667 v3 à 3,2 GHz | 5 | 573,2 | 2,9
Standard\_H16mr | 16 | 2 | Intel Xeon E5-2667 v3 à 3,2 GHz | 7 | 569,6 | 2\.8

## À propos de SPECint

Les chiffres Windows ont été calculés en exécutant [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) sur Windows Server. SPECint a été exécuté à l’aide de l’option de taux de base (SPECint\_rate2006), avec une copie par cœur. SPECint se compose de 12 tests distincts, chacun exécuté trois fois, en prenant la valeur médiane de chaque test et en les pondérant pour former un score composite. Ces tests ont été exécutés sur plusieurs machines virtuelles pour fournir les scores moyens indiqués.


## Étapes suivantes

* Pour plus d’informations sur les capacités de stockage, les détails du disque et d’autres considérations relatives au choix d’une taille de machine virtuelle, consultez [Tailles des machines virtuelles](virtual-machines-windows-sizes.md).

<!---HONumber=AcomDC_0928_2016-->