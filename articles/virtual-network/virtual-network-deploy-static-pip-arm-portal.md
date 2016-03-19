<properties 
   pageTitle="Déployer une machine virtuelle avec une adresse IP publique statique à l'aide du portail Azure dans Resource Manager | Microsoft Azure"
   description="Découvrez comment déployer des machines virtuelles avec une adresse IP publique statique à l'aide du portail Azure dans Resource Manager"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="telmos" />

# Déployer une machine virtuelle avec une adresse IP publique statique à l’aide du portail Azure

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## Créer une machine virtuelle avec une adresse IP publique statique 

Pour créer une machine virtuelle avec une adresse IP publique statique dans le portail Azure, procédez comme suit.

1. Dans un navigateur, accédez au [portail Azure](https://portal.azure.com) et, si nécessaire, connectez-vous avec votre compte Azure.
2. Dans le coin supérieur gauche du portail, cliquez sur **Nouveau**>>**Calculer**>**Windows Server 2012 R2 Datacenter**.
3. Dans la liste **Sélectionner un modèle de déploiement**, sélectionnez **Resource Manager**, puis cliquez sur **Créer**.
4. Dans le panneau **De base**, entrez les informations de la machine virtuelle comme indiqué ci-dessous, puis cliquez sur **OK**.

	![Portail Azure - De base](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)

5. Dans le panneau **Choisir une taille**, cliquez sur **A1 Standard** comme indiqué ci-dessous, puis cliquez sur **Sélectionner**.

	![Portail Azure - Choisir une taille](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)

6. Dans le panneau **Paramètres**, cliquez sur **Adresse IP publique**, puis dans le panneau **Créer une adresse IP publique**, sous **Affectation**, cliquez sur **Statique** comme indiqué ci-dessous. Cliquez ensuite sur **OK**.

	![Portail Azure - Créer une adresse IP publique](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)

7. Dans le panneau **Paramètres**, cliquez sur **OK**.
8. Passez en revue le panneau **Résumé** (tel qu’illustré ci-dessous), puis cliquez sur **OK**.

	![Portail Azure - Créer une adresse IP publique](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)

9. Notez la nouvelle vignette dans votre tableau de bord.

	![Portail Azure - Créer une adresse IP publique](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)

10. Une fois la machine virtuelle créée, le panneau **Paramètres** apparaît comme dans l’illustration ci-dessous.

	![Portail Azure - Créer une adresse IP publique](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

<!---HONumber=AcomDC_0211_2016-->