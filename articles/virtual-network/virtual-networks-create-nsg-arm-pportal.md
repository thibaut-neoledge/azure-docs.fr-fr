<properties 
   pageTitle="Création de NSG en mode ARM à l’aide du portail en version préliminaire | Microsoft Azure"
   description="Apprenez à créer et à déployer des NSG dans ARM, à l’aide du portail en version préliminaire"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2015"
   ms.author="telmos" />

# Génération de NSG à l’aide du portail en version préliminaire

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article traite du modèle de déploiement de Resource Manager. Vous pouvez également [créer des NSG dans le modèle de déploiement classique](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

## Déployer le modèle ARM en cliquant pour déployer

À ce stade, vous ne pouvez pas créer de NSG à partir de la version préliminaire. Toutefois, vous gérez des NSG existants. Avant de gérer des NSG, utiliser l’exemple de modèle disponible dans le référentiel public pour créer les ressources décrites dans le scénario ci-dessus. Déployez [ce modèle](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG), cliquez sur **Déployer vers Azure**, remplacez les valeurs de paramètres par défaut si nécessaire, et suivez les instructions du portail.

## Création de règles dans un NSG existant

Pour créer des règles dans un NSG existant à partir du portail en version préliminaire, suivez les étapes ci-dessous.

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **Parcourir>** > **Groupes de sécurité réseau**.

![Portail en version préliminaire : NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure1.png)

3. Dans la liste des NSGs, cliquez sur **NSG-FrontEnd** > **Règles de sécurité de trafic entrant**.

![Portail en version préliminaire : NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)

4. Dans la liste **Règles de sécurité de trafic entrant**, cliquez sur **Ajouter**.

![Portail en version préliminaire : Ajouter une règle](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)

5. Dans le panneau **Ajouter une règle de sécurité de trafic entrant**, créez une règle nommée *web-rule* avec une priorité de *200* autorisant l’accès via *TCP* au port *80* à n’importe quelle machine virtuelle à partir de n’importe quelle source, puis cliquez sur **OK**. Notez que la plupart de ces paramètres sont déjà des valeurs par défaut.

![Portail en version préliminaire : Paramètres des règles](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)

6. Après quelques secondes, vous voyez la nouvelle règle dans le NSG.

![Portail en version préliminaire : Nouvelle règle](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)

<!---HONumber=Oct15_HO3-->