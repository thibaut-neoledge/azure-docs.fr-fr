<properties
	pageTitle="Comparaison entre les images personnalisées et les formules dans DevTest Labs | Microsoft Azure"
	description="Découvrez les différences entre les images personnalisées et les formules comme bases de machine virtuelle afin de déterminer laquelle correspond le mieux à votre environnement."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/22/2016"
	ms.author="tarcher"/>

# Comparaison entre les images personnalisées et les formules dans DevTest Labs

## Vue d'ensemble
Les [images personnalisées](./devtest-lab-create-template.md) et les [formules](./devtest-lab-manage-formulas.md) peuvent toutes deux être utilisées comme bases pour [créer de nouvelles machines virtuelles](./devtest-lab-add-vm-with-artifacts.md). Toutefois, la différence principale entre les images personnalisées et les formules est qu'une image personnalisée est simplement une image basée sur un disque dur virtuel, alors qu’une formule correspond à une image basée sur un disque dur virtuel *ainsi que* des paramètres préconfigurés, comme la taille de la machine virtuelle, le réseau virtuel et le sous-réseau, les artefacts, etc. Ces paramètres préconfigurés sont définis avec les valeurs par défaut. Celles-ci peuvent être remplacées au moment de la création de la machine virtuelle. Cet article explique quelques-uns des avantages et des inconvénients de l'utilisation d'images personnalisées et de formules.
 
## Avantages et inconvénients des images personnalisées
Les images personnalisées représentent un moyen statique et immuable de créer des machines virtuelles dans l’environnement de votre choix.

**Avantages**
- L’approvisionnement de machines virtuelles à partir d'une image personnalisée est rapide car rien ne change une fois que la machine virtuelle est lancée à partir de l'image. En d'autres termes, il n'existe aucun paramètre à appliquer car l'image personnalisée est tout simplement une image sans paramètres. 
- Les machines virtuelles créées à partir d'une image personnalisée sont toutes identiques.

**Inconvénients**
- Si vous avez besoin de mettre à jour certains aspects de l'image personnalisée, l'image doit être recréée.  

## Avantages et inconvénients des formules
Les formules représentent un moyen dynamique de créer des machines virtuelles à partir de la configuration/des paramètres souhaités.

**Avantages**
- Les modifications de l'environnement peuvent être capturées à la volée au moyen d’artefacts. Par exemple, si vous souhaitez installer une machine virtuelle avec les derniers éléments de votre pipeline de lancement ou inscrire les toutes dernières lignes de code de votre référentiel, vous pouvez simplement spécifier un artefact qui déploiera les derniers éléments ou inscrira les dernières lignes de code dans la formule avec l’image de base cible. Chaque fois que cette formule sera utilisée pour créer des machines virtuelles, les derniers éléments/lignes de code seront déployés/inscrites sur la machine virtuelle. 
- Les formules, à la différence des images personnalisées, peuvent définir des paramètres par défaut, par exemple la taille des machines virtuelles et les paramètres de réseau virtuel. 
- Les paramètres enregistrés dans une formule sont affichés en tant que valeurs par défaut, mais peuvent être modifiés à la création de la machine virtuelle. 

**Inconvénients**
- La création d'une machine virtuelle à partir d'une formule peut prendre plus de temps qu’à partir d'une image personnalisée.

<!---HONumber=AcomDC_0427_2016-->