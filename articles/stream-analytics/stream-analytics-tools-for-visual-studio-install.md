---
title: "Instructions d’installation pour Azure Stream Analytics Tools pour Visual Studio | Microsoft Docs"
description: "Instructions d’installation pour Azure Stream Analytics Tools pour Visual Studio"
keywords: visual studio
documentationcenter: 
services: stream-analytics
author: su-jie
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/19/2017
ms.author: sujie
ms.openlocfilehash: 88abf40cefaca150c67e3a1068006fb0fa254305
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="installation-instructions-for-stream-analytics-tools-for-visual-studio"></a>Instructions d’installation pour Stream Analytics Tools pour Visual Studio
Stream Analytics Tools prend désormais en charge Visual Studio 2017, 2015 et 2013. Dans ce document, nous indiquons comment installer et désinstaller l’outil.

[Découvrez comment utiliser Stream Analytics Tools pour Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio)

## <a name="install"></a>Installer
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Téléchargez [Visual Studio 2017 (15.3 ou version ultérieure)](https://www.visualstudio.com/). Les éditions Enterprise (Ultimate/Premium), Professional et Community sont prises en charge ; l’édition Express n’est pas prise en charge. 
* Stream Analytics Tools fait partie des charges de travail Développement Azure et Stockage et traitement des données dans Visual Studio 2017. Activez l’une de ces deux charges de travail dans le cadre de votre installation de Visual Studio.

![Installer Stream Analytics Tools pour Visual Studio 1](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-01.png)
![Installer Stream Analytics Tools pour Visual Studio 2](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Installez Visual Studio 2015, Visual Studio 2013 Update 4. Les éditions Enterprise (Ultimate/Premium), Professional et Community sont prises en charge ; l’édition Express n’est pas prise en charge. 
* Installez le kit SDK Microsoft Azure pour .NET version 2.7.1 ou ultérieure à l’aide de [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).
* Installez [Azure Stream Analytics Tools pour Visual Studio](http://aka.ms/asatoolsvs).



## <a name="update"></a>Mettre à jour

### <a name="visual-studio-2017"></a>Visual Studio 2017
Le rappel de l’existence d’une nouvelle version s’affiche dans la notification Visual Studio. 

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
L’outil Stream Analytics Tools pour Visual Studio installé recherche automatiquement les nouvelles versions, et vous pouvez suivre les instructions dans la fenêtre contextuelle pour installer la version la plus récente. 


## <a name="uninstall"></a>Désinstaller l’interface

### <a name="visual-studio-2017"></a>Visual Studio 2017
Double-cliquez sur le programme d’installation de Visual Studio et sélectionnez **Modifier**. Décochez la case **Azure Data Lake et Stream Analytics Tools** de la charge de travail **Stockage et traitement des données** ou **Développement Azure**.

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Accédez au Panneau de configuration et désinstallez Microsoft Azure Data Lake et Stream Analytics Tools pour Visual Studio.





