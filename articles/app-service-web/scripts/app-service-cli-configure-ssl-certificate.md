---
title: "Exemple de script Azure CLI - Lier un certificat SSL personnalisé à une application web | Microsoft Docs"
description: "Exemple de script Azure CLI - Lier un certificat SSL personnalisé à une application web"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/19/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: d4fab3fb2c297bf5f498b63bee46692febb9180b
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---

# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>Lier un certificat SSL personnalisé à une application web

Cet exemple de script crée une application web dans App Service avec ses ressources associées, puis lie le certificat SSL d’un nom de domaine personnalisé à celle-ci. Pour cet exemple, vous avez besoin des éléments suivants :

* Un accès à la page de configuration DNS du Registre de votre domaine.
* Un fichier .PFX valide et son mot de passe pour le certificat SSL que vous voulez charger et lier.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Lier un certificat SSL personnalisé à une application web")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crée un plan App Service. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#create) | Crée une application web Azure. |
| [az webapp config hostname add](https://docs.microsoft.com/cli/azure/webapp/config/hostname#add) | Mappe un domaine personnalisé à une application web. |
| [az webapp config ssl upload](https://docs.microsoft.com/cli/azure/webapp/config/ssl#upload) | Charge un certificat SSL personnalisé sur une application web. |
| [az webapp config ssl bind](https://docs.microsoft.com/cli/azure/webapp/config/ssl#bind) | Lie un certificat SSL chargé à une application web. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI App Service dans la [documentation relative à Azure App Service](../app-service-cli-samples.md).

