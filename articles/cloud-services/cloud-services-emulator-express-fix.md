---
title: "Configurer l’émulateur express pour déboguer des applications de service cloud dans Visual Studio | Microsoft Docs"
description: "Explique comment installer le redistribuable C++ pour activer l’émulateur express dans Visual Studio"
services: cloud-services
documentationcenter: 
author: cawa
manager: paulyuk
editor: 
ms.assetid: 22b20f7a-23f4-4f7f-b536-3bf1e01adcd1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/02/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: 165ab7363efaf90eaab41098f71e2f1b846c346e
ms.openlocfilehash: 05d672dcb1335c617bb8d8cae43947bcd5e9ab3d

---
# <a name="use-emulator-express-to-debug-cloud-services-application-in-vs-2017"></a>Utiliser l’émulateur express pour déboguer des applications de service cloud dans VS 2017
Cet article explique comment utiliser l’émulateur express pour déboguer des applications de service cloud dans VS 2017.

## <a name="background-context"></a>Contexte
L’émulateur express est utilisé par défaut pour le débogage des rôles web et de travail des services cloud dans Visual Studio. Ce paramètre est spécifié dans la page de propriétés du projet de service cloud.

![Ouvrir les propriétés du projet][0]

![L’émulateur express est sélectionné par défaut][1]

Le [Redistribuable Visual C++][Redistribuable Visual C++] pour Visual Studio est requis par l’émulateur express. Il n’est actuellement pas installé avec la charge de travail Azure. En cas d’appui sur F5 pour déboguer une application de service cloud, Visual Studio invite à installer ce composant et à continuer le débogage.

![Invite d’installation du redistribuable C++][2]

Cliquez sur Oui pour installer le redistribuable C++.

![Installer le redistribuable C++][3]

Appuyez de nouveau sur F5 pour lancer des sessions de débogage.

![Démarrer le débogage][4]

![Débogage réussi][5]

> Remarque : l’installation du redistribuable Visual C++ n’est nécessaire qu’une seule fois. Si vous avez effectué une mise à niveau à partir d’une version antérieure du Kit de développement logiciel Azure et avez déjà installé l’émulateur express, vous ne rencontrerez pas ce problème.
> 
> 

## <a name="manual-workaround"></a>Solution de contournement manuel
Vous pouvez également installer le [Redistribuable Visual C++][Redistribuable Visual C++] manuellement. Le résultat sera le même que si Visual Studio l’avait installé sur votre système.

[vcredist_x86.exe][vcredist_x86.exe]

[vcredist_x64.exe][vcredist_x64.exe]

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’utilisation de l’émulateur de calcul Azure pour déboguer vos applications de service cloud dans Visual Studio : [Utilisation de l’émulateur express pour exécuter et déboguer un service cloud sur une machine locale][Utilisation de l’émulateur express pour exécuter et déboguer un service cloud sur une machine locale]

[Redistribuable Visual C++]:https://www.microsoft.com/en-us/download/details.aspx?id=30679
[vcredist_x86.exe]:https://download.microsoft.com/download/1/6/B/16B06F60-3B20-4FF2-B699-5E9B7962F9AE/VSU_4/vcredist_x86.exe
[vcredist_x64.exe]:https://download.microsoft.com/download/1/6/B/16B06F60-3B20-4FF2-B699-5E9B7962F9AE/VSU_4/vcredist_x64.exe
[Utilisation de l’émulateur express pour exécuter et déboguer un service cloud sur une machine locale]:https://azure.microsoft.com/en-us/documentation/articles/vs-azure-tools-emulator-express-debug-run/

[0]: ./media/cloud-services-emulator-express-fix/vs-05.png
[1]: ./media/cloud-services-emulator-express-fix/vs-06.png
[2]: ./media/cloud-services-emulator-express-fix/vs-01.png
[3]: ./media/cloud-services-emulator-express-fix/vs-02.png
[4]: ./media/cloud-services-emulator-express-fix/vs-03.png
[5]: ./media/cloud-services-emulator-express-fix/vs-04.png



<!--HONumber=Nov16_HO3-->


