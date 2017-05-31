---
title: "Gérer les journaux des flux de groupe de sécurité réseau avec Azure Network Watcher | Microsoft Docs"
description: "Cette page explique comment gérer les journaux des flux de groupe de sécurité réseau dans Azure Network Watcher"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: b059cfdcf21d8b61589a5edf3bd1832070e564e3
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---

# <a name="manage-network-security-group-flow-logs-in-the-azure-portal"></a>Gérer les groupes de sécurité réseau dans le portail Azure

> [!div class="op_single_selector"]
> - [portail Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [INTERFACE DE LIGNE DE COMMANDE](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

Les journaux des flux de groupe de sécurité réseau désignent une fonctionnalité de Network Watcher qui vous permet de visualiser des informations sur le trafic IP d’entrée et de sortie par le biais d’un groupe de sécurité réseau. Ces flux de journaux sont écrits au format json et affichent les flux entrants et sortants en fonction de la règle, la carte réseau à laquelle le flux s’applique, des informations à 5 tuples sur le flux (adresse IP source/de destination, port source/de destination, protocole), ainsi que l’autorisation ou le refus du trafic.

## <a name="before-you-begin"></a>Avant de commencer

Ce scénario suppose que vous ayez déjà suivi la procédure décrite dans [Create a Network Watcher (Créer une instance Network Watcher)](network-watcher-create.md) pour créer une instance Network Watcher. Ce scénario suppose également qu’un groupe de ressources avec une machine virtuelle valide existe et peut être utilisé.

## <a name="register-insights-provider"></a>Inscription du fournisseur Insights

Pour permettre au journal de flux de fonctionner correctement, le fournisseur **Microsoft.Insights** doit être enregistré. Pour inscrire le fournisseur, accédez à **Abonnements** et sélectionnez l’abonnement pour lequel vous souhaitez activer les journaux de flux. Sur le panneau **Abonnement**, sélectionnez **Fournisseurs de ressources**. Parcourez la liste des fournisseurs et vérifiez que **microsoft.insights** est enregistré. Si ce n’est pas le cas, cliquez sur **Inscrire**.

![afficher les fournisseurs][providers]

## <a name="enable-flow-logs"></a>Activer les journaux des flux

Ces étapes vous guident tout au long de l’activation des journaux des flux sur un groupe de sécurité réseau.

### <a name="step-1"></a>Étape 1 :

Accédez à une instance Network Watcher et sélectionnez **Flow logs** (Journaux des flux)

![vue d’ensemble des journaux des flux][1]

### <a name="step-2"></a>Étape 2

Sélectionnez un groupe de sécurité réseau dans la liste en cliquant dessus.

![vue d’ensemble des journaux des flux][2]

### <a name="step-3"></a>Étape 3 

Dans le panneau **Flow logs settings** (Paramètres des journaux des flux), définissez l’état sur **Activé** et configurez un compte de stockage.  Lorsque vous avez terminé, cliquez sur **OK** et sur **Enregistrer**

![vue d’ensemble des journaux des flux][3]

## <a name="download-flow-logs"></a>Télécharger des journaux des flux

Les journaux des flux sont enregistrés dans un compte de stockage. Pour afficher vos journaux des flux, vous devez les télécharger.

### <a name="step-1"></a>Étape 1

Pour télécharger des journaux des flux, cliquez sur **Vous pouvez télécharger les journaux de flux à partir des comptes de stockage configurés**.  Vous accédez à une vue du compte de stockage dans laquelle vous pouvez accéder à votre journal à télécharger.

![paramètres des journaux des flux][4]

### <a name="step-2"></a>Étape 2

Accédez au compte de stockage approprié, puis à **Conteneurs** > **insights-log-networksecuritygroupflowevent**

![paramètres des journaux des flux][5]

### <a name="step-3"></a>Étape 3

Accédez à l’emplacement du journal des flux, sélectionnez le journal des flux, puis cliquez sur **Télécharger**

![paramètres des journaux des flux][6]

Pour plus d’informations sur la structure du journal, consultez [Network Security Group Flow log Overview (Vue d’ensemble des journaux des flux de groupe de sécurité réseau)](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [visualiser vos journaux des flux de groupe de sécurité réseau avec Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-portal/figure1.png
[2]: ./media/network-watcher-nsg-flow-logging-portal/figure2.png
[3]: ./media/network-watcher-nsg-flow-logging-portal/figure3.png
[4]: ./media/network-watcher-nsg-flow-logging-portal/figure4.png
[5]: ./media/network-watcher-nsg-flow-logging-portal/figure5.png
[6]: ./media/network-watcher-nsg-flow-logging-portal/figure6.png
[providers]: ./media/network-watcher-nsg-flow-logging-portal/providers.png
