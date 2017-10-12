---
title: "Approvisionner des machines virtuelles pour la science des données Azure en tant que serveurs de bloc-notes IPython | Microsoft Docs"
description: "Configurez une machine virtuelle pour la science des données en tant que serveur de bloc-notes IPython avec des outils de support."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 95e1fa87-794a-4d03-80a4-af4f3f3ac31e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: bradsev
ms.openlocfilehash: 88fe9673176cdade92faad4bbdcb2e1bd11f4a55
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="provision-azure-data-science-virtual-machines-as-ipython-notebook-servers"></a>Approvisionner des machines virtuelles pour la science des données Azure en tant que serveurs de bloc-notes IPython
Les instructions fournies ici décrivent la procédure de configuration d'une machine virtuelle Azure et d'une machine virtuelle Azure équipée du service SQL en tant que serveurs Notebook IPython. Le machine virtuelle Windows est configurée avec des outils connexes, tels que Notebook IPython, l’Explorateur de stockage Azure et AzCopy, ainsi que d’autres utilitaires utiles pour les projets de science des données. Par exemple, l’Explorateur de stockage Azure et AzCopy facilitent le chargement de données dans le stockage Azure depuis votre ordinateur local ou le téléchargement de ces données vers votre ordinateur local à partir du stockage. 

Ce menu pointe vers des rubriques qui décrivent comment configurer les différents environnements de science de données utilisés par le [processus TDSP (Team Data Science Process)](overview.md).

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

Vous pouvez approvisionner et configurer plusieurs types de machines virtuelles Azure en vue de les utiliser au sein d’un environnement de science des données dans le cloud. Le choix du type de machine virtuelle à utiliser dépend du type et de la quantité des données à modéliser avec l’apprentissage automatique, ainsi que de la destination cible de ces données dans le cloud. 

* Pour plus d’informations sur les questions à prendre en compte lors de cette prise de décision, consultez la page [Planifier votre environnement de science des données Azure Machine Learning](plan-your-environment.md). 
* Pour accéder à un catalogue de scénarios que vous pouvez rencontrer lorsque vous effectuez des analyses avancées, consultez la page [Scénarios du processus de science des données Azure en action dans Azure Machine Learning](plan-sample-scenarios.md)

Deux ensembles d’instructions vous sont fournis :

* [Configurer une machine virtuelle Azure comme serveur IPython Notebook pour des analyses avancées](../data-science-virtual-machine/setup-virtual-machine.md) indique comment approvisionner une machine virtuelle Azure avec Notebook IPython et d’autres outils utilisés pour des projets de science des données permettant de recourir à une forme de stockage Azure autre que SQL pour le stockage des données.
* [Configurer une machine virtuelle Azure SQL Server comme serveur IPython Notebook pour des analyses avancées](../data-science-virtual-machine/setup-sql-server-virtual-machine.md) indique comment approvisionner une machine virtuelle Azure SQL Server avec Notebook IPython et d’autres outils utilisés pour des projets de science des données permettant de recourir à une base de données SQL pour le stockage des données.

Une fois approvisionnées et configurées, ces machines virtuelles sont utilisables sous forme de serveurs Notebook IPython pour l’exploration et le traitement des données, ainsi que pour l’exécution d’autres tâches avec Azure Machine Learning et le processus TDSP (Team Data Science Process). Les étapes suivantes du processus de science des données sont présentées dans le [parcours d'apprentissage TDSP](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) et peuvent inclure des étapes de déplacement de données dans SQL Server ou HDInsight, de traitement et d'échantillonnage des données en vue d'en extraire de l'information pertinente avec Azure Machine Learning.

> [!NOTE]
> Le service Azure Virtual Machines est facturé au tarif du **paiement à l’utilisation**. Pour vous assurer de ne pas être facturé lorsque vous n’utilisez pas votre machine virtuelle, elle doit être définie sur l’état **Arrêté (désalloué)** à partir du [Portail Azure Classic](http://manage.windowsazure.com/). Pour obtenir des instructions détaillées sur la libération de votre machine virtuelle, consultez la page [Arrêter et libérer une machine virtuelle inutilisée](../data-science-virtual-machine/setup-virtual-machine.md#shutdown)
> 
> 

