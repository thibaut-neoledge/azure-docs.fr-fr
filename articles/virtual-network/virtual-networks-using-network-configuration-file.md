---
title: "Configurer un réseau virtuel Azure (Classic) - Fichier de configuration de réseau | Microsoft Docs"
description: "Découvrez comment modifier des réseaux virtuels (Classic) en exportant, modifiant et important un fichier de configuration de réseau à l’aide du portail Azure (Classic)."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 3827dc0958c51fa0c4ecb1a2e8e3b7bbed42a75a
ms.lasthandoff: 02/28/2017


---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Configurer un réseau virtuel (Classic) à l’aide d’un fichier de configuration de réseau
Vous pouvez configurer un réseau virtuel (Classic) à l’aide du portail Azure (Classic) ou à l’aide d’un fichier de configuration de réseau. Vous ne pouvez pas utiliser de fichier de configuration de réseau pour créer ou modifier un réseau virtuel via le modèle de déploiement Azure Resource Manager. Vous ne pouvez pas non plus utiliser le portail Azure pour créer ou modifier un réseau virtuel (Classic).

## <a name="creating-and-modifying-a-network-configuration-file"></a>Création et modification d'un fichier de configuration réseau
Pour créer un fichier de configuration réseau, le plus simple consiste à exporter les paramètres réseau de la configuration d’un réseau virtuel (Classic) existant, puis à modifier ce fichier pour qu’il inclue les paramètres à configurer dans vos réseaux virtuels.

Pour modifier le fichier de configuration réseau, il vous suffit d'ouvrir le fichier, d'apporter les modifications appropriées et d’enregistrer le fichier. Vous pouvez utiliser n’importe quel éditeur *xml* pour apporter des modifications au fichier de configuration réseau. 

Vous devez respecter les instructions relatives aux [paramètres de schéma du fichier de configuration réseau](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

Azure considère tout sous-réseau qui comprend des éléments déployés comme étant **« en cours d'utilisation »**. Une fois le sous-réseau utilisé, il ne peut pas être modifié. Avant toute modification, déplacez tout ce que vous avez déployé sur le sous-réseau vers un autre sous-réseau qui n'est pas modifié.   Consultez [Déplacer une machine virtuelle ou une instance de rôle vers un autre sous-réseau](virtual-networks-move-vm-role-to-subnet.md).

## <a name="export-and-import-virtual-network-settings-using-the-azure-portal-classic"></a>Exporter et importer des paramètres de réseau virtuel à l’aide du portail Azure (Classic)
Vous pouvez importer les paramètres d’exportation de configuration réseau contenus dans votre fichier de configuration réseau à l’aide de PowerShell ou du portail de gestion. Les instructions ci-dessous vous aideront à exporter et importer ces paramètres à l'aide du portail de gestion. 

### <a name="to-export-your-network-settings"></a>Pour exporter vos paramètres réseau
Lorsque effectuez une exportation, tous les paramètres des réseaux virtuels de votre abonnement sont écrits dans un fichier .xml. 

1. Connectez-vous au [portail Azure (Classic)](https://manage.windowsazure.com/).
2. Dans le portail, au bas de la page **réseaux**, cliquez sur **Exporter**. 
3. Dans la fenêtre **Exporter la configuration de réseau** , vérifiez que vous avez sélectionné l’abonnement pour lequel vous voulez exporter vos paramètres réseau. Ensuite, cliquez sur la coche dans la partie inférieure droite. 
4. Quand vous y êtes invité, enregistrez le fichier *NetworkConfig.xml* à l’emplacement de votre choix.

### <a name="to-import-your-network-settings"></a>Pour importer vos paramètres réseau
1. Dans le portail, dans le volet de navigation en bas à gauche, cliquez sur **Nouveau**.
2. Cliquez sur **Services réseau** -> **Réseau virtuel** -> **Importer la configuration**.
3. Dans la page **Importer le fichier de configuration de réseau**, accédez à votre fichier de configuration réseau, puis cliquez sur la flèche **Suivant**.
4. Dans la page **Création de votre réseau** , vous verrez des informations à l’écran présentant les sections de votre configuration réseau qui vont être créées ou modifiées. Si les modifications vous paraissent correctes, cliquez sur la coche pour procéder à la mise à jour ou à la création de votre réseau virtuel. 


