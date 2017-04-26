---
title: "Plusieurs adresses IP pour les machines virtuelles Azure - Modèle | Microsoft Docs"
description: "Apprenez à affecter plusieurs adresses IP à une machine virtuelle à l’aide d’un modèle Azure Resource Manager."
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: d4b189fb23dda1167c4f6b17b618c718d32dd98f
ms.lasthandoff: 04/21/2017


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-an-azure-resource-manager-template"></a>Affecter plusieurs adresses IP à des machines virtuelles à l’aide d’un modèle Azure Resource Manager

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Cet article explique comment créer une machine virtuelle dans le modèle de déploiement Azure Resource Manager à l’aide d’un modèle Resource Manager. Plusieurs adresses IP publiques et privées ne peuvent pas être affectées à la même carte réseau lors du déploiement d’une machine virtuelle dans le modèle de déploiement classique. Pour en savoir plus sur les modèles de déploiement Azure, voir [Comprendre les modèles de déploiement](../resource-manager-deployment-model.md).

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="template-description"></a>Description du modèle

Le déploiement d’un modèle vous permet de créer rapidement et de manière cohérente des ressources Azure avec différentes valeurs de configuration. Lisez l’article [Guide de création d’un modèle Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md?toc=%2fazure%2fvirtual-network%2ftoc.json) si vous n’êtes pas familiarisé avec les modèles Azure Resource Manager. Le modèle [Déployer une machine virtuelle avec plusieurs adresses IP](https://azure.microsoft.com/resources/templates/101-vm-multiple-ipconfig) est utilisé dans cet article.

<a name="resources"></a>Le déploiement du modèle crée les ressources suivantes :

|Ressource|Nom|Description|
|---|---|---|
|Interface réseau|*myNic1*|Les trois configurations IP décrites dans la section du scénario de cet article sont créées et affectées à cette carte réseau.|
|Ressource d’adresse IP publique|2 sont créés : *myPublicIP* et *myPublicIP2*|Ces ressources sont affectées à des adresses IP statiques publics et aux configurations IP *IPConfig-1* et *IPConfig-2* décrites dans le scénario.|
|machine virtuelle|*myVM1*|Une machine virtuelle DS3 standard.|
|Réseau virtuel|*myVNet1*|Un réseau virtuel avec un sous-réseau nommé *mySubnet*.|
|Compte de stockage|Unique pour le déploiement|Un compte de stockage.|

<a name="parameters"></a>Lorsque vous déployez le modèle, vous devez spécifier des valeurs pour les paramètres suivants :

|Nom|Description|
|---|---|
|adminUsername|Nom d’utilisateur administrateur. Le nom d’utilisateur doit satisfaire aux [exigences de nom d’utilisateur Azure](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|adminPassword|Mot de passe administrateur. Le mot de passe doit satisfaire les [exigences de mot de passe Azure](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
|dnsLabelPrefix|Nom DNS de PublicIPAddressName1. Le nom DNS est résolu en une des adresses IP publiques affectées à la machine virtuelle. Le nom doit être unique au sein de la région Azure (emplacement) dans laquelle vous créez la machine virtuelle.|
|dnsLabelPrefix1|Nom DNS de PublicIPAddressName2. Le nom DNS est résolu en une des adresses IP publiques affectées à la machine virtuelle. Le nom doit être unique au sein de la région Azure (emplacement) dans laquelle vous créez la machine virtuelle.|
|OSVersion:|La version de Windows/Linux pour la machine virtuelle. Le système d’exploitation est une image entièrement corrigée de la version de Windows/Linux sélectionnée.|
|imagePublisher|L’éditeur d’image Windows/Linux pour la machine virtuelle sélectionnée.|
|imageOffer|L’image Windows/Linux pour la machine virtuelle sélectionnée.|

Chacune des ressources déployées par le modèle est configurée avec plusieurs paramètres par défaut. Vous pouvez afficher ces paramètres à l’aide de l’une des méthodes suivantes :

- **Afficher le modèle dans GitHub :** si vous êtes familiarisé avec les modèles, vous pouvez afficher les paramètres dans le [modèle](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json).
- **Afficher les paramètres après le déploiement :** si vous n’êtes pas familiarisé avec les modèles, vous pouvez déployer le modèle à l’aide des étapes de l’une des sections suivantes, puis afficher les paramètres après le déploiement.

Vous pouvez utiliser le portail Azure, PowerShell ou l’interface de ligne de commande (CLI) Azure pour déployer le modèle. Toutes les méthodes produisent le même résultat. Pour déployer le modèle, suivez les étapes de l’une des sections suivantes :

## <a name="deploy-using-the-azure-portal"></a>Déployer à l’aide du portail Azure

Pour déployer le modèle à l’aide du portail Azure, procédez comme suit :

1. Si vous le souhaitez, modifiez le modèle. Le modèle déploie les ressources et les paramètres répertoriés dans la section des [ressources](#resources) de cet article. Pour en savoir plus sur les modèles et leur création, consultez [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Déployez le modèle selon l’une des méthodes suivantes :
    - **Sélectionner le modèle dans le portail :** suivez les étapes de l’article [Déployer des ressources à partir d’un modèle personnalisé](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template). Choisissez le modèle existant nommé *101-vm-multiple-ipconfig*.
    - **Directement :** cliquez sur le bouton ci-dessous pour ouvrir le modèle directement dans le portail :<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-multiple-ipconfig%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

Quelle que soit la méthode choisie, vous devez fournir des valeurs pour le [paramètres](#parameters) répertoriés précédemment dans cet article. Une fois la machine virtuelle déployée, connectez la machine virtuelle et ajoutez les adresses IP privées au système d’exploitation que vous avez déployé en suivant les étapes de la section [Ajouter des adresses IP à un système d’exploitation de machine virtuelle](#os-config) de cet article. N’ajoutez pas les adresses IP publiques au système d’exploitation.

## <a name="deploy-using-powershell"></a>Déployer à l’aide de PowerShell

Pour déployer le modèle à l’aide de PowerShell, procédez comme suit :

1. Déployez le modèle en suivant les étapes de l’article [Déployer un modèle avec PowerShell](../azure-resource-manager/resource-group-template-deploy-cli.md). L’article décrit plusieurs options de déploiement d’un modèle. Si vous choisissez de déployer à l’aide de `-TemplateUri parameter`, l’URI de ce modèle est *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*. Si vous choisissez de déployer à l’aide du paramètre `-TemplateFile`, copiez le contenu du [fichier de modèle](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) de GitHub dans un nouveau fichier sur votre ordinateur. Si vous le souhaitez, modifiez le contenu du modèle. Le modèle déploie les ressources et les paramètres répertoriés dans la section des [ressources](#resources) de cet article. Pour en savoir plus sur les modèles et leur création, lisez l’article [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

    Quelle que soit l’option que vous choisissez pour déployer le modèle, vous devez fournir des valeurs pour les valeurs de paramètre répertoriées dans la section des [paramètres](#parameters) de cet article. Si vous choisissez de fournir des paramètres à l’aide d’un fichier de paramètres, copiez le contenu du [fichier de paramètres](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json) de GitHub dans un nouveau fichier sur votre ordinateur. Modifiez les valeurs dans le fichier. Utilisez le fichier que vous avez créé en tant que valeur pour le paramètre `-TemplateParameterFile`.

    Pour déterminer les valeurs valides pour les paramètres OSVersion, ImagePublisher et imageOffer, suivez les étapes de l’article [Accéder à et sélectionner des images de machine virtuelle Windows](../virtual-machines/windows/cli-ps-findimage.md).

    >[!TIP]
    >Si vous ne savez pas si un dnslabelprefix est disponible, entrez la commande `Test-AzureRmDnsAvailability -DomainNameLabel <name-you-want-to-use> -Location <location>` pour le déterminer. S’il est disponible, la commande renvoie `True`.

2. Une fois la machine virtuelle déployée, connectez la machine virtuelle et ajoutez les adresses IP privées au système d’exploitation que vous avez déployé en suivant les étapes de la section [Ajouter des adresses IP à un système d’exploitation de machine virtuelle](#os-config) de cet article. N’ajoutez pas les adresses IP publiques au système d’exploitation.

## <a name="deploy-using-the-azure-cli"></a>Déployer à l’aide de l’interface de ligne de commande (CLI) Azure

Pour déployer le modèle à l’aide de l’interface de ligne de commande (CLI) Azure 1.0, procédez comme suit :

1. Déployez le modèle en suivant les étapes de l’article [Déployer un modèle avec l’interface de ligne de commande (CLI) Azure](../azure-resource-manager/resource-group-template-deploy-cli.md). L’article décrit plusieurs options de déploiement du modèle. Si vous choisissez de déployer à l’aide de `--template-uri` (-f), l’URI de ce modèle est *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*. Si vous choisissez de déployer à l’aide du paramètre `--template-file` (f-), copiez le contenu du [fichier de modèle](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) de GitHub dans un nouveau fichier sur votre ordinateur. Si vous le souhaitez, modifiez le contenu du modèle. Le modèle déploie les ressources et les paramètres répertoriés dans la section des [ressources](#resources) de cet article. Pour en savoir plus sur les modèles et leur création, lisez l’article [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

    Quelle que soit l’option que vous choisissez pour déployer le modèle, vous devez fournir des valeurs pour les valeurs de paramètre répertoriées dans la section des [paramètres](#parameters) de cet article. Si vous choisissez de fournir des paramètres à l’aide d’un fichier de paramètres, copiez le contenu du [fichier de paramètres](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json) de GitHub dans un nouveau fichier sur votre ordinateur. Modifiez les valeurs dans le fichier. Utilisez le fichier que vous avez créé en tant que valeur pour le paramètre `--parameters-file` (-e).

    Pour déterminer les valeurs valides pour les paramètres OSVersion, ImagePublisher et imageOffer, suivez les étapes de l’article [Accéder à et sélectionner des images de machine virtuelle Windows](../virtual-machines/windows/cli-ps-findimage.md).

2. Une fois la machine virtuelle déployée, connectez la machine virtuelle et ajoutez les adresses IP privées au système d’exploitation que vous avez déployé en suivant les étapes de la section [Ajouter des adresses IP à un système d’exploitation de machine virtuelle](#os-config) de cet article. N’ajoutez pas les adresses IP publiques au système d’exploitation.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

