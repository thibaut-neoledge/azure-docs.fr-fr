---
title: "Redéployer Azure Stack | Microsoft Docs"
description: "Redéployer Azure Stack."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: erikje
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 891cde9b16bbbb51729129b6ad7a0f3794307baa
ms.contentlocale: fr-fr
ms.lasthandoff: 09/15/2017

---
# <a name="redeploy-azure-stack"></a>Redéployer Azure Stack
Pour redéployer Azure Stack, vous devez recommencer à partir de zéro comme décrit ci-dessous.

## <a name="steps-to-redeploy-azure-stack"></a>Procédure de redéploiement d’Azure Stack
1. Sur l’hôte du kit de développement, ouvrez une console PowerShell avec élévation de privilèges > accédez au script asdk-installer.ps1 > exécutez-le > cliquez sur **Redémarrer**.
2. Sélectionnez le système d’exploitation de base (et non pas **Azure Stack**), puis cliquez sur **Suivant**.
3. Après le redémarrage de l’hôte du kit de développement, supprimez le fichier CloudBuilder.vhdx qui a été utilisé lors du déploiement précédent.
4. [Déployer le kit de développement](azure-stack-run-powershell-script.md).

## <a name="next-steps"></a>Étapes suivantes
[Se connecter à Azure Stack](azure-stack-connect-azure-stack.md)


