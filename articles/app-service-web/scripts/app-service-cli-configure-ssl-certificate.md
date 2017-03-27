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
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: a28bb7b8d30ebe6589dfb792e9294a180544569f
ms.lasthandoff: 03/11/2017

---

# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>Lier un certificat SSL personnalisé à une application web

Cet exemple de script crée une application web dans App Service avec ses ressources associées, puis lie le certificat SSL d’un nom de domaine personnalisé à celle-ci. 

Avant d’exécuter ce script, vérifiez ces points :

- Une connexion avec Azure a été créée à l’aide de la commande `az login`.
- Vous avez accès à la page de configuration DNS du bureau d’enregistrement de votre domaine.
- Vous disposez d’un fichier .PFX valide et de son mot de passe pour le certificat SSL que vous souhaitez charger et lier.

Cet exemple fonctionne dans un interpréteur de commandes Bash. Pour en savoir plus les options d’exécution de scripts Azure CLI dans le client Windows, consultez la page [Running the Azure CLI in Windows (Exécution d’Azure CLI dans Windows)](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Lier un certificat SSL personnalisé à une application web")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crée un plan App Service. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Crée une application web Azure. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | Met à jour un plan App Service en fonction du niveau tarifaire. |
| [az appservice web config ssl upload](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#upload) | Charger un certificat SSL personnalisé sur une application web. |
| [az appservice web config ssl bind](https://docs.microsoft.com/en-us/cli/azure/appservice/web/config/ssl#bind) | Lier un certificat SSL chargé à une application web. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI App Service dans la [documentation relative à Azure App Service](../app-service-cli-samples.md).
