---
title: Tailles de services cloud | Microsoft Docs
description: "Répertorie les différentes tailles de machines virtuelles (et les identifiants) des rôles web et de travail des services cloud Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 1127c23e-106a-47c1-a2e9-40e6dda640f6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/27/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: d6faf73b3fb5f56e42a07dc9c0bbf1a48767c7c8


---
# <a name="sizes-for-cloud-services"></a>Tailles de services cloud
Cette rubrique décrit les tailles et options disponibles pour les instances de rôle de Cloud Services (rôles web et rôles de travail). Il expose également les points à prendre en considération pour le déploiement quand vous planifiez l'utilisation de ces ressources. Chaque taille a un identifiant que vous allez placer dans votre [fichier de définition de service](cloud-services-model-and-package.md#csdef).

Cloud Services est l’un des nombreux types de ressources de calcul proposés par Azure. Cliquez [ici](cloud-services-choose-me.md) pour en savoir plus sur Cloud Services.

> [!NOTE]
> Pour connaître les limites d'Azure associées, consultez l'article [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md)
> 
> 

## <a name="sizes-for-web-and-worker-role-instances"></a>Tailles pour les instances de rôle web et de travail
Azure offre le choix entre plusieurs tailles standard. Voici des considérations quant à certaines de ces tailles :

* Les machines virtuelles de la série D sont conçues pour exécuter des applications qui nécessitent une puissance de calcul et des performances de disque temporaire supérieures. Ces machines virtuelles se caractérisent par des processeurs plus rapides, un rapport mémoire-cœur plus élevé et un disque SSD pour le disque temporaire. Pour plus d’informations, voir l’annonce suivante sur le blog Azure : [Nouvelles tailles de machines virtuelles de la série D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)(en anglais).
* La série Dv2, suite de la série D d’origine, comprend un processeur plus puissant. Le processeur de la série Dv2 est environ 35 % plus rapide que le processeur de la série D. Il est basé sur la dernière génération de processeur 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) et comporte la technologie 2.0 Intel Turbo Boost, et peut atteindre 3,1 GHz. La série Dv2 a les mêmes configurations de disque et de mémoire que la série D.
* Les machines virtuelles de la série G offrent le plus de mémoire et s’exécutent sur des hôtes équipés de processeurs de la famille Intel Xeon E5 V3.
* Les machines virtuelles de la série A peuvent être déployées sur différents types de matériels et processeurs. La taille est limitée, en fonction du matériel, pour offrir des performances de processeur cohérentes pour l’instance en cours d’exécution, quel que soit le matériel sur lequel elle est déployée. Pour déterminer le matériel physique sur lequel cette taille est déployée, interrogez le matériel virtuel à partir de la machine virtuelle.
* La taille A0 est trop sollicitée sur le matériel physique. Pour cette taille spécifique uniquement, les autres déploiements de clients peuvent affecter les performances de la charge de travail en cours d’exécution. Les performances relatives sont décrites ci-dessous comme référence attendue, soumises à une variation approximative de 15 pour cent.

La taille de la machine virtuelle a une incidence sur la tarification. La taille influe également sur les capacités de traitement, de mémoire et de stockage de la machine virtuelle. Les coûts de stockage sont calculés séparément en fonction des pages utilisées dans le compte de stockage. Pour plus d’informations, voir les pages [Machines virtuelles Tarification](https://azure.microsoft.com/pricing/details/virtual-machines/) et [Tarification Azure Storage](https://azure.microsoft.com/pricing/details/storage/). 

Les considérations ci-dessous peuvent vous aider à choisir une taille :

* Les tailles A8 à A11 et celles de la série H sont également appelées *instances nécessitant beaucoup de ressources système*. Le matériel qui exécute ces tailles a été conçu et optimisé pour les applications nécessitant beaucoup de ressources système et réseau, notamment les applications en cluster pour des calculs complexes, la modélisation et les simulations. La série A8-A11 utilise un processeur Intel Xeon E5-2670 @ 2,6 GHZ, et la série H un processeur Intel Xeon E5-2667 v3 @ 3,2 GHz. Pour plus d’informations et pour connaître les éléments à prendre en considération sur l’utilisation de ces tailles, consultez l’article [À propos de la série H et de la série A nécessitant beaucoup de ressource système](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
* Les séries Dv2, D et G sont idéales pour les applications qui exigent des processeurs plus rapides, de meilleures performances de disque local, ou qui ont des exigences de mémoire plus élevées.  Elles offrent une combinaison puissante pour de nombreuses applications professionnelles.
* Certains hôtes physiques des centres de données Azure ne prennent pas en charge les tailles de machines virtuelles élevées, comme A5 à A11. Ainsi, vous pouvez obtenir le message d’erreur **Échec de la configuration de la machine virtuelle {nom de la machine}** ou **Échec de la création de la machine virtuelle {nom de la machine}** pendant le redimensionnement d’une machine virtuelle existante, la création d’une machine virtuelle dans un réseau virtuel créé avant le 16 avril 2013 ou l’ajout d’une nouvelle machine virtuelle à un service cloud existant. Pour découvrir les solutions de contournement pour chaque scénario de déploiement, consultez [Erreur : « Échec de la configuration de la machine virtuelle »](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) (en anglais) sur le forum d'assistance.  
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
| [Standard_A0](#a-series) |50 |
| [Standard_A1-4](#a-series) |100 |
| [Standard_A5-7](#a-series) |100 |
| [A8-A11](#a-series) |225* |
| [D1-14](#d-series) |160 |
| [D1-15v2](#dv2-series) |210 - 250* |
| [G1-5](#g-series) |180 - 240* |
| [H](#h-series) |290 - 300* |

Les unités ACU signalées par un astérisque (*) utilisent la technologie Intel ® Turbo pour augmenter la fréquence du processeur et améliorer les performances.  Cette amélioration des performances peut varier en fonction de la taille et de la charge de travail de la machine virtuelle, et des autres charges de travail en cours d’exécution sur le même hôte.

## <a name="size-tables"></a>Tableaux des tailles
Les tableaux ci-après indiquent les tailles et les capacités qu’elles offrent.

* La capacité de stockage est indiquée en unités de Gio ou 1 024^3 octets. Lors de la comparaison de disques mesurés en Go (1 000^3 octets) à des disques mesurés en Gio (1 024^3) n’oubliez pas que les indications de capacité en Gio peuvent sembler plus petites. Par exemple, 1 023 Gio = 1 098,4 Go
* Le débit de disque est mesuré en opérations d’entrée/sortie par seconde (IOPS) et Mbits/s où Mbits/s = 10^6 octets par seconde.
* Les disques de données peuvent fonctionner en mode avec ou sans mise en cache. En cas de fonctionnement du disque de données avec mise en cache, le mode de mise en cache hôte est défini sur **ReadOnly** ou **ReadWrite**.  En cas de fonctionnement du disque de données sans mise en cache, le mode de mise en cache hôte est défini sur **Aucun**.
* La bande passante réseau maximale est la bande passante maximale agrégée allouée et affectée par type de machine virtuelle. La bande passante maximale fournit des recommandations pour la sélection du bon type de machine virtuelle afin de garantir une capacité réseau adéquate. Lors du déplacement entre Faible, Modéré, Élevé et Très élevé, le débit augmente en conséquence. Les performances réseau réelles dépendent de nombreux facteurs, notamment les charges du réseau et de l’application, ainsi que les paramètres réseau de l’application.

## <a name="a-series"></a>Série A
| Taille | Cœurs d’unité centrale | Mémoire : Gio | Disque dur local : Gio | Disques de données max. | Débit de disque de données max : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 |1 |0,768 |20 |1 |1 x 500 |1 / Faible |
| Standard_A1 |1 |1,75 |70 |2 |2 x 500 |1 / Modérée |
| Standard_A2 |2 |3,5 Go |135 |4 |4 x 500 |1 / Modérée |
| Standard_A3 |4 |7 |285 |8 |8 x 500 |2 / Élevée |
| Standard_A4 |8 |14 |605 |16 |16 x 500 |4 / Élevée |
| Standard_A5 |2 |14 |135 |4 |4 x 500 |1 / Modérée |
| Standard_A6 |4 |28 |285 |8 |8 x 500 |2 / Élevée |
| Standard_A7 |8 |56 |605 |16 |16 x 500 |4 / Élevée |

## <a name="a-series---compute-intensive-instances"></a>Série A - Instances de calcul intensif
Pour plus d’informations et pour connaître les éléments à prendre en compte pour l’utilisation de ces tailles, consultez [À propos des machines virtuelles de série H ou de série A nécessitant beaucoup de ressources système](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Taille | Cœurs d’unité centrale | Mémoire : Gio | Disque dur local : Gio | Disques de données max. | Débit de disque de données max : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8* |8 |56 |382 |16 |16 x 500 |2 / Élevée |
| Standard_A9* |16 |112 |382 |16 |16 x 500 |4 / Très élevée |
| Standard_A10 |8 |56 |382 |16 |16 x 500 |2 / Élevée |
| Standard_A11 |16 |112 |382 |16 |16 x 500 |4 / Très élevée |

*Prenant en charge RDMA

## <a name="d-series"></a>Série D
| Taille | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Disques de données max. | Débit de disque de données max : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
| --- | --- | --- | --- | --- | --- | --- |
| D1 standard |1 |3,5 |50 |2 |2 x 500 |1 / Modérée |
| D2 standard |2 |7 |100 |4 |4 x 500 |2 / Élevée |
| D3 standard |4 |14 |200 |8 |8 x 500 |4 / Élevée |
| D4 standard |8 |28 |400 |16 |16 x 500 |8 / Élevée |
| D11 standard |2 |14 |100 |4 |4 x 500 |2 / Élevée |
| D12 standard |4 |28 |200 |8 |8 x 500 |4 / Élevée |
| D13 standard |8 |56 |400 |16 |16 x 500 |8 / Élevée |
| D14 standard |16 |112 |800 |32 |32 x 500 |8 / Très élevée |

## <a name="dv2-series"></a>Série Dv2
| Taille | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Disques de données max. | Débit de disque de données max : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |3,5 |50 |2 |2 x 500 |1 / Modérée |
| Standard_D2_v2 |2 |7 |100 |4 |4 x 500 |2 / Élevée |
| Standard_D3_v2 |4 |14 |200 |8 |8 x 500 |4 / Élevée |
| Standard_D4_v2 |8 |28 |400 |16 |16 x 500 |8 / Élevée |
| Standard_D5_v2 |16 |56 |800 |32 |32 x 500 |8 / Extrêmement élevée |
| Standard_D11_v2 |2 |14 |100 |4 |4 x 500 |2 / Élevée |
| Standard_D12_v2 |4 |28 |200 |8 |8 x 500 |4 / Élevée |
| Standard_D13_v2 |8 |56 |400 |16 |16 x 500 |8 / Élevée |
| Standard_D14_v2 |16 |112 |800 |32 |32 x 500 |8 / Extrêmement élevée |
| Standard_D15_v2 |20 |140 |1 000 |40 |40 x 500 |8 / Extrêmement élevée |

## <a name="g-series"></a>Série G
| Taille | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Disques de données max. | Débit de disque max : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1 |2 |28 |384 |4 |4 x 500 |1 / Élevée |
| Standard_G2 |4 |56 |768 |8 |8 x 500 |2 / Élevée |
| Standard_G3 |8 |112 |1 536 |16 |16 x 500 |4 / Très élevée |
| Standard_G4 |16 |224 |3 072 |32 |32 x 500 |8 / Extrêmement élevée |
| Standard_G5 |32 |448 |6 144 |64 |64 x 500 |8 / Extrêmement élevée |

## <a name="h-series"></a>Série H
Les machines virtuelles de la série H sont des machines virtuelles de calcul haute performance de nouvelles génération, destinées à répondre à des besoins de calcul de haut niveau, par exemple en relation avec la modélisation moléculaire et la dynamique des fluides. Les machines virtuelles à 8 et 16 cœurs reposent sur la technologie de processeur Intel Haswell E5-2667 V3 avec mémoire DDR4 et stockage SSD local. 

En plus de la puissance substantielle du processeur, la série H offre différentes options pour des réseaux RDMA à faible latence, en utilisant FDR InfiniBand et plusieurs configurations de mémoire pouvant satisfaire des exigences de calcul nécessitant une mémoire conséquente.

| Taille | Cœurs d’unité centrale | Mémoire : Gio | SSD local = Gio | Disques de données max. | Débit de disque max : E/S par seconde | Cartes réseau (max)/Bande passante réseau |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1 000 |16 |16 x 500 |8 / Élevée |
| Standard_H16 |16 |112 |2000 |32 |32 x 500 |8 / Très élevée |
| Standard_H8m |8 |112 |1 000 |16 |16 x 500 |8 / Élevée |
| Standard_H16m |16 |224 |2000 |32 |32 x 500 |8 / Très élevée |
| Standard_H16r* |16 |112 |2000 |32 |32 x 500 |8 / Très élevée |
| Standard_H16mr* |16 |224 |2000 |32 |32 x 500 |8 / Très élevée |

*Prenant en charge RDMA

## <a name="notes-standard-a0---a4-using-cli-and-powershell"></a>Remarques : Standard A0 - A4 à l’aide de l’interface de ligne de commande et de Powershell
Dans le modèle de déploiement classique, certains noms de tailles de machines virtuelles sont légèrement différents dans Powershell et dans l’interface de ligne de commande :

* Standard_A0 est ExtraSmall (Très petit) 
* Standard_A1 est Small (Petit)
* Standard_A2 est Medium (Moyen)
* Standard_A3 est Large (Grand)
* Standard_A4 est ExtraLarge (Très grand)

## <a name="configure-sizes-for-cloud-services"></a>Configurer les tailles pour les Cloud Services
Vous pouvez spécifier la taille de l’ordinateur virtuel d’une instance de rôle dans le cadre du modèle de service décrit par le [fichier de définition de service](cloud-services-model-and-package.md#csdef). La taille du rôle détermine le nombre de cœurs du processeur, la capacité de mémoire et la taille du système de fichiers local qui lui est allouée. Choisissez la taille du rôle en fonction des besoins en ressources de votre application.

Voici un exemple qui montre comment configurer un rôle avec la taille [Standard_D2](#general-purpose-d) pour une instance de rôle web :

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2">
...
</WorkerRole>
```

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur l’ [abonnement Azure et les limites, quotas et contraintes des services](../azure-subscription-service-limits.md).
* Pour des charges de travail telles que le calcul haute performance (HPC), consultez [À propos des machines virtuelles de série H et de série A nécessitant beaucoup de ressources système](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .




<!--HONumber=Nov16_HO3-->


