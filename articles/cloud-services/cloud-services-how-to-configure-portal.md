---
title: "Configuration d’un service cloud (portail) | Microsoft Docs"
description: "Découvrez comment configurer des services cloud dans Azure. Apprenez à mettre à jour la configuration d'un service cloud et à configurer l'accès distant aux instances de rôle. Ces exemples utilisent le portail Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 7308f3c0-825e-499d-bfa5-c60f86371921
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: adegeo
ms.openlocfilehash: a7e891d05ffe4cc2b4f68dce072a81499cc6de80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-cloud-services"></a>Configuration des services cloud
> [!div class="op_single_selector"]
> * [Portail Azure](cloud-services-how-to-configure-portal.md)
> * [Portail Azure Classic](cloud-services-how-to-configure.md)
>
>

Vous pouvez configurer les paramètres les plus couramment utilisés pour un service cloud dans le portail Azure. Ou, si vous voulez mettre à jour directement vos fichiers de configuration, téléchargez un fichier de configuration de service pour le mettre à jour, puis chargez-le et mettez à jour le service cloud avec les modifications de la configuration. Dans les deux cas, les mises à jour de la configuration sont transmises à toutes les instances de rôle.

Vous pouvez également gérer les instances de vos rôles de service cloud ou le Bureau à distance qui s’y trouve.

Azure ne peut garantir que 99,95 % de disponibilité du service pendant les mises à jour de la configuration si vous avez au moins deux instances de rôle pour chaque rôle. Cela permet à une machine virtuelle de traiter les demandes du client pendant que l'autre est mise à jour. Pour plus d'informations, consultez la page [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Modification d'un service cloud
Dans le [portail Azure](https://portal.azure.com/), accédez à votre service cloud. Vous pouvez alors gérer plusieurs aspects.

![Page Paramètres](./media/cloud-services-how-to-configure-portal/cloud-service.png)

Les liens **Paramètres** ou **Tous les paramètres** permettent d’accéder au panneau **Paramètres** où vous pouvez modifier les **propriétés**, modifier la **configuration**, gérer les **certificats**, configurer les **règles d’alerte** et gérer les **utilisateurs** qui ont accès à ce service cloud.

![Panneau Paramètres du service cloud Azure](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Gestion de la version de SE invité

Par défaut, Azure met régulièrement à jour votre SE invité vers la dernière image prise en charge d’un produit de la famille de systèmes d’exploitation que vous avez spécifiée dans votre configuration de service (.cscfg), par exemple, Windows Server 2016.

Si vous devez cibler une version de système d’exploitation spécifique, vous pouvez la définir dans le panneau **Configuration**.

![Définition de la version du système d’exploitation](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)


>[!IMPORTANT]
> Lorsque vous choisissez une version de système d’exploitation spécifique, les mises à jour de SE automatiques sont désactivées et l’application de correctifs relève alors de votre responsabilité. Vous devez vous assurer que vos instances de rôle reçoivent les mises à jour, sans quoi votre application serait exposée à des failles de sécurité.

## <a name="monitoring"></a>Analyse
Vous pouvez ajouter des alertes à votre service cloud. Cliquez sur **Paramètres** > **Règles d’alerte** > **Ajouter une alerte**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Vous pouvez alors configurer une alerte. Avec la liste déroulante **Métrique** , vous pouvez configurer une alerte pour les types de données suivants.

* Lecture du disque
* Écriture sur le disque
* Entrée réseau
* Sortie réseau
* Pourcentage UC

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Configuration de la surveillance à partir d’une vignette de métrique
Au lieu d’utiliser **Paramètres** > **Règles d’alerte**, vous pouvez cliquer sur l’une des vignettes de métrique dans la section **Surveillance** du panneau **Service cloud**.

![Surveillance du service cloud](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Vous pouvez alors personnaliser le graphique utilisé avec la vignette ou ajouter une règle d’alerte.

## <a name="reboot-reimage-or-remote-desktop"></a>Redémarrage, réinitialisation ou Bureau à distance
À ce stade, vous ne pouvez pas configurer le Bureau à distance à l’aide du **portail Azure**. Toutefois, vous pouvez le configurer par le biais du [portail Azure Classic](cloud-services-role-enable-remote-desktop.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md) ou [Visual Studio](../vs-azure-tools-remote-desktop-roles.md).

Tout d’abord, cliquez sur l’instance de service cloud.

![Instance de service cloud](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Dans le panneau qui s’affiche, vous pouvez initier une connexion Bureau à distance, redémarrer l’instance à distance ou réinitialiser l’instance à distance (pour commencer avec une toute nouvelle image).

![Boutons d’instance de service cloud](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Reconfigurer votre fichier .cscfg
Vous devrez peut-être reconfigurer votre service cloud via le fichier [configuration de service (cscfg)](cloud-services-model-and-package.md#cscfg). Vous devez d’abord télécharger le fichier .cscfg, puis le modifier et le charger.

1. Cliquez sur l’icône **Paramètres** ou sur le lien **Tous les paramètres** pour ouvrir le panneau **Paramètres**.

    ![Page Paramètres](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Cliquez sur l’élément **Configuration** .

    ![Panneau de configuration](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Cliquez sur le bouton **Download** .

    ![Download](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Après la mise à jour du fichier de configuration de service, téléchargez et appliquez les mises à jour de la configuration :

    ![Télécharger](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Sélectionnez le fichier .cscfg et cliquez sur **OK**.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [déployer un service cloud](cloud-services-how-to-create-deploy-portal.md).
* Configurez un [nom de domaine personnalisé](cloud-services-custom-domain-name-portal.md).
* [Gérez votre service cloud](cloud-services-how-to-manage-portal.md).
* Configurez des [certificats SSL](cloud-services-configure-ssl-certificate-portal.md).
