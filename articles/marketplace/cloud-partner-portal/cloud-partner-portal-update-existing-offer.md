---
title: "Mettre à jour une offre existante pour Azure Marketplace | Microsoft Docs"
description: "Cet article fournit des détails concernant la mise à jour d’une offre existante via le portail Cloud Partner"
services: cloud-partner-portal
documentationcenter: 
author: anuragdalmia
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2017
ms.author: andalmia
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 48424cb03c7fa521a2556ff00c2136eb5fae38ad
ms.lasthandoff: 04/12/2017


---
# <a name="update-an-existing-offer-for-azure-marketplace"></a>Mettre à jour une offre existante pour Azure Marketplace
Il existe différents types de mises à jour que vous pouvez appliquer à votre offre lorsqu’elle est publiée.

1. Ajout d’une nouvelle version image de machine virtuelle à des références (SKU) existantes.
1. Modifiez les régions dans lesquelles une référence (SKU) est disponible. 
1. Ajout de nouvelles références (SKU). 
1. Mise à jour des métadonnées marketplace d’une offre/référence (SKU).

Pour cela, vous devez mettre à jour votre offre dans le [portail Cloud Partner](https://cloudpartner.azure.com/) puis la republier. Cet article vous présente en détail les différents aspects de la mise à jour de votre offre de machine virtuelle.

## <a name="unpermitted-changes-to-vm-offersku"></a>Modifications non autorisées apportées à l’offre/référence (SKU) de machine virtuelle
Certains attributs d’une offre/référence (SKU) de machine virtuelle ne peuvent pas être modifiés une fois que l’offre est publiée dans Azure Marketplace.
1. ID d’offre et ID de l’éditeur de l’offre.
2. ID de référence (SKU) de références (SKU) existantes.
3. Configuration du port ouvert de références (SKU) existantes.
4. Nombre de disques de données de références (SKU) existantes.
5. Modifications du modèle de facturation/licence apportées à des références (SKU) existantes.

## <a name="updating-the-vm-image-version-for-a-sku"></a>Mise à jour de la version image de machine virtuelle pour une référence (SKU)
L’image de machine virtuelle pour une référence de votre offre peut avoir été mise à jour avec des fonctionnalités supplémentaires, des correctifs de sécurité, etc. Dans de tels scénarios, vous pouvez mettre à jour l’image de machine virtuelle associée à votre référence (SKU). Pour ce faire, procédez comme suit :
1. Connectez-vous au [portail Microsoft Cloud Partner](https://cloudpartner.azure.com/)
2. Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour
3. Sous le formulaire des **références (SKU)**, cliquez sur la référence (SKU) pour laquelle vous souhaitez mettre à jour l’image de machine virtuelle
4. Sous **Version disque**, cliquez sur **+Nouvelle version disque** pour ajouter une nouvelle image de machine virtuelle
5. Fournissez la **version disque** des nouvelles images de machine virtuelle. La version disque doit suivre le format [version sémantique](http://semver.org/). Les versions doivent être au format X.Y.Z, où X, Y et Z sont des entiers. Assurez-vous que la nouvelle version que vous fournissez est supérieure aux versions antérieures ; sinon elles ne s’afficheront pas dans le portail Azure ou dans Azure marketplace lors de la republication.
6. Dans le champ **URL de disque dur virtuel de système d’exploitation**, saisissez l[’URI de signature d’accès partagé](../../marketplace-publishing/marketplace-publishing-vm-image-creation.md#52-get-the-shared-access-signature-uri-for-your-vm-images) créé pour le disque dur virtuel du système d’exploitation. Notez que le nombre de disques de données ne peut pas varier entre différentes versions de la référence (SKU). Si des versions précédentes avaient des disques de données configurés, cette nouvelle version doit également avoir des disques de données configurés. 
7. Cliquez sur **Publier** pour lancer le workflow de publication afin que la nouvelle version de la machine virtuelle soit publiée sur Azure marketplace et le portail Azure. 

## <a name="changing-regions-a-sku-is-available-in"></a>Modification des régions dans lesquelles une référence (SKU) est disponible
Au fil du temps, vous pouvez proposer votre offre/référence (SKU) dans d’autres régions. Vous pouvez également arrêter la prise en charge de l’offre/référence (SKU) dans une région donnée. Pour cela, procédez comme suit.

1. Connectez-vous au [portail Microsoft Cloud Partner](https://cloudpartner.azure.com/)
2. Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour
3. Sous le formulaire des **références (SKU)**, cliquez sur la référence (SKU) pour laquelle vous souhaitez mettre à jour la disponibilité de la région
4. Cliquez sur le bouton **Sélectionner des pays** sous le champ **Disponibilité par pays/région**. 
5. Dans la fenêtre contextuelle, ajoutez ou supprimez les régions souhaitées pour cette référence (SKU).
6. Cliquez sur **Publier** pour lancer le workflow de publication afin de mettre à jour la disponibilité des régions pour vos références (SKU). 

Si une référence (SKU) est proposée dans une nouvelle région, vous devez spécifier les tarifs pour cette région particulière en **exportant les données de tarification**. Notez que si vous ajoutez une région qui était disponible auparavant, vous ne pourrez pas mettre à jour son prix car les modifications de prix ne sont pas autorisées. 

## <a name="adding-a-new-sku"></a>Ajout d’une nouvelle référence (SKU)
Vous pouvez choisir de proposer une nouvelle référence (SKU) pour votre offre existante. Pour cela, suivez les étapes ci-dessous.

1. Connectez-vous au [portail Microsoft Cloud Partner](https://cloudpartner.azure.com/)
2. Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour
3. Sous le formulaire des **références (SKU)**, cliquez sur **Ajouter une nouvelle référence (SKU)** et fournissez un **ID de SKU** dans la fenêtre contextuelle. 
4. Suivez le reste des étapes comme spécifié [ici](../../cloud-partner-portal/cloud-partner-portal-publish-virtual-machine.md).
6. Cliquez sur **Publier** pour lancer le workflow de publication afin de publier votre nouvelle référence (SKU).

## <a name="updating-offer-marketplace-metadata"></a>Mise à jour des métadonnées marketplace de l’offre.
Dans certains scénarios, vous devez mettre à jour les métadonnées marketplace associées à votre offre, par exemple vos logos de société. Pour ce faire, procédez comme suit.

1. Connectez-vous au [portail Microsoft Cloud Partner](https://cloudpartner.azure.com/)
2. Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour
3. Accédez au formulaire **Marketplace** et suivez les instructions affichées [ici](../../cloud-partner-portal/cloud-partner-portal-publish-virtual-machine.md) pour apporter des modifications. 
4. Cliquez sur **Publier** pour lancer le workflow de publication afin de publier vos modifications.


