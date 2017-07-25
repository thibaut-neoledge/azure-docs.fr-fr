---
title: "Utilisez des agents de machine virtuelle Azure pour l’intégration continue avec Jenkins."
description: "Agents de machine virtuelle Azure en tant que subordonnés de Jenkins."
services: multiple
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: 5f2df414b4d0e8798b7ed6d90d0ea0fb79d42fc2
ms.contentlocale: fr-fr
ms.lasthandoff: 06/21/2017

---
# <a name="use-azure-vm-agents-for-continuous-integration-with-jenkins"></a>Utilisez des agents de machine virtuelle Azure pour l’intégration continue avec Jenkins.

Ce guide de démarrage rapide montre comment utiliser le plug-in d’agents de machine virtuelle Azure Jenkins pour créer un agent Linux (Ubuntu) à la demande dans Azure.

## <a name="prerequisites"></a>Composants requis

Pour effectuer ce démarrage rapide :

* Si vous n’avez pas encore de maître Jenkins, vous pouvez démarrer avec le [modèle de solution](install-jenkins-solution-template.md). 
* Reportez-vous à [Créer un principal du service Azure avec Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) si vous n’avez pas encore de principal de service Azure.

## <a name="install-azure-vm-agents-plugin"></a>Installer le plug-in Agents de machine virtuelle Azure

Si vous démarrez à partir du [modèle de solution](install-jenkins-solution-template.md), le plug-in Agents de machine virtuelle Azure est installé dans le maître Jenkins.

Sinon, installez le plug-in **Agents de machine virtuelle Azure** à partir du tableau de bord Jenkins.

## <a name="configure-the-plugin"></a>Configurer le plug-in

* Dans le tableau de bord Jenkins, cliquez sur **Manage Jenkins (Gérer Jenkins) -> Configure System (Configurer le système) ->**. Faites défiler vers le bas de la page et recherchez la section avec la liste déroulante **Add new cloud (Ajouter un nouveau cloud)**. Dans le menu, sélectionnez **Microsoft Azure VM Agents (Agents de machine virtuelle Microsoft Azure)**.
* Sélectionnez un compte existant dans la liste déroulante Azure Credentials (Informations d’identification Azure).  Pour ajouter un nouveau **principal du service Microsoft Azure**, entrez les valeurs suivantes : ID d’abonnement, ID de client, clé secrète client et point de terminaison de jeton OAuth 2.0.

![Informations d’identification Azure](./media/jenkins-azure-vm-agents/service-principal.png)

* Cliquez sur **Verify configuration (Vérifier la configuration)** pour vous assurer que la configuration du profil est correcte.
* Enregistrez la configuration et passez à l’étape suivante.

## <a name="template-configuration"></a>Configuration du modèle

### <a name="general-configuration"></a>Configuration générale
Ensuite, configurez un modèle qui servira à définir un agent de machine virtuelle Azure. 

* Cliquez sur **Add (Ajouter)** pour ajouter un modèle. 
* Entrez un nom pour votre nouveau modèle. 
* Pour l’étiquette, entrez « ubuntu ». Cette étiquette est utilisée lors de la configuration du travail.
* Dans la zone de liste modifiable, sélectionnez la région souhaitée.
* Sélectionnez la taille de machine virtuelle qui vous intéresse.
* Spécifiez le nom de compte de stockage Azure ou laissez le champ vide pour utiliser le nom par défaut « jenkinsarmst ».
* Spécifiez la durée de rétention en minutes. Ce paramètre définit le nombre de minutes que Jenkins peut attendre avant de supprimer automatiquement un agent inactif. Spécifiez 0 si vous ne souhaitez pas que les agents inactifs soient supprimés automatiquement.

![Configuration générale](./media/jenkins-azure-vm-agents/general-config.png)

### <a name="image-configuration"></a>Configuration d’une image

Pour créer un agent Linux (Ubuntu), sélectionnez **Image reference (Référence d’image)** et utilisez la configuration suivante comme exemple. Consultez la [place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) pour obtenir les dernières images Azure prises en charge.

* Éditeur d’images : Canonical
* Offre de l’image : UbuntuServer
* Référence SKU de l’image : 14.04.5-LTS
* Version de l’image : la plus récente
* Type de système d’exploitation : Linux
* Méthode de lancement : SSH
* Fournir des informations d’identification d’administrateur
* Pour le script d’initialisation de machine virtuelle, entrez :
```
# Install Java
sudo apt-get -y update
sudo apt-get install -y openjdk-7-jdk
sudo apt-get -y update --fix-missing
sudo apt-get install -y openjdk-7-jdk
```
![Configuration d’une image](./media/jenkins-azure-vm-agents/image-config.png)

* Cliquez sur **Verify Template (Vérifier le modèle)** pour vérifier la configuration.
* Cliquez sur **Save**.

## <a name="create-a-job-in-jenkins"></a>Créer un travail dans Jenkins

* Dans le tableau de bord Jenkins, cliquez sur **Nouvel élément**. 
* Entrez un nom, sélectionnez **Freestyle project (Projet libre)** et cliquez sur **OK**.
* Dans l’onglet **General (Général)**, sélectionnez l’option « Restrict where project can be run » (Restreindre les emplacements d’exécution du projet) et saisissez « ubuntu » dans le champ Label Expression (Expression d’étiquette). Vous voyez maintenant « ubuntu » dans la liste déroulante.
* Cliquez sur **Save**.

![Configurer un travail](./media/jenkins-azure-vm-agents/job-config.png)

## <a name="build-your-new-project"></a>Générer votre nouveau projet

* Revenez au tableau de bord Jenkins.
* Cliquez avec le bouton droit de la souris sur le travail que vous venez de créer, puis choisissez l’option **Build now (Générer maintenant)**. Une génération est lancée. 
* Une fois la génération terminée, accédez à la **sortie de la console**. Vous voyez que la génération a été effectuée à distance sur Azure.

![Sortie de la console](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="reference"></a>Référence

* Vidéo Azure Friday : [intégration continue avec Jenkins à l’aide d’agents de machine virtuelle Azure](https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents)
* Informations de prise en charge et options de configuration : [wiki du plug-in Jenkins pour agents de machine virtuelle Azure](https://wiki.jenkins-ci.org/display/JENKINS/Azure+VM+Agents+Plugin) 


