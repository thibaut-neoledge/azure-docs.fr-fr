---
title: "Connexion à Azure Government avec l’interface de ligne de commande Azure | Microsoft Docs"
description: "Informations sur la gestion de votre abonnement dans Azure Government en vous connectant à l’interface de ligne de commande Azure"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: c7cbe993-375e-4aed-9aa5-2f4b2111f71c
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: 6e070870a93d2636f076b339dd401358735a9bd0
ms.lasthandoff: 02/21/2017


---


# <a name="connect-to-azure-government-with-azure-cli"></a>Connexion à Azure Government avec l’interface de ligne de commande Azure

Pour utiliser l’interface de ligne de commande Azure, vous devez vous connecter à Azure Government au lieu de la version publique d’Azure. L’interface de ligne de commande Azure peut servir à gérer un grand abonnement via un script ou d’accéder à des fonctionnalités qui ne sont pas actuellement disponibles dans le portail Azure. Si vous avez utilisé l’interface de ligne de commande Azure dans Public Azure, tout est très similaire.  Les éléments qui diffèrent dans Azure Government sont les suivants :

Vous disposez de plusieurs méthodes [pour installer l’interface de ligne de commande Azure](https://docs.microsoft.com/en-us/azure/xplat-cli-install). Si vous avez déjà installé Node, la méthode la plus simple consiste à installer le package npm :

Pour installer l’interface CLI à partir d’un package npm, vérifiez que vous avez téléchargé et installé les [derniers fichiers Node.js et npm](https://nodejs.org/en/download/package-manager/). Ensuite, exécutez **npm install** pour installer le package azure-cli :

```bash
npm install -g azure-cli
```

Sur les distributions Linux, vous devrez peut-être utiliser **sudo** pour exécuter la commande **npm**, comme suit :

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Si vous devez installer ou mettre à jour Node.js et npm sur votre distribution Linux ou votre système d’exploitation, nous vous recommandons d’installer la dernière version de Node.js LTS (4.x). Si vous utilisez une version antérieure, vous pouvez obtenir des erreurs d’installation.


Une fois que vous avez installé l’interface de ligne de commande Azure, vous devez vous connecter à Azure Government :

```
azure login --username your-user-name@your-gov-tenant.onmicrosoft.com  --environment AzureUSGovernment
```

Une fois que vous êtes connecté, vous pouvez exécuter les commandes CLI Azure comme vous le feriez normalement :

```
azure webapp list my-resource-group
```
