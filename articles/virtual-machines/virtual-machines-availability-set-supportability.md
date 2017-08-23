---
title: "Possibilité de prise en charge de l’ajout de machines virtuelles Azure à un groupe à haute disponibilité | Microsoft Docs"
description: "Possibilité de prise en charge de l’ajout de machines virtuelles Azure à un groupe à haute disponibilité."
services: virtual-machines-linux
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
ms.assetid: 
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/15/2017
ms.author: delhan
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: f07108f5d7a792dcc9aae8a4fe889878db168bc6
ms.contentlocale: fr-fr
ms.lasthandoff: 07/21/2017

---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Possibilité de prise en charge de l’ajout de machines virtuelles Azure à un groupe à haute disponibilité

Vous pouvez parfois rencontrer des limitations lorsque vous ajoutez des machines virtuelles à un groupe à haute disponibilité. Le tableau suivant détaille les séries de machines virtuelles que vous pouvez combiner dans le même groupe à haute disponibilité.

Voici la matrice de possibilité de prise en charge pour combiner différents types de machines virtuelles :

Série et groupe à haute disponibilité|Deuxième machine virtuelle|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Première machine virtuelle|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Les autres séries ne pourraient pas être dans le même groupe à haute disponibilité, car elles nécessitent un matériel spécifique.
