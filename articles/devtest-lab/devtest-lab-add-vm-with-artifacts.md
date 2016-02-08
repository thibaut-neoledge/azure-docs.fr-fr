    <properties
	pageTitle="Add a VM with artifacts to a DevTest Lab | Microsoft Azure"
	description="Create a new virtual machine with Artifacts in DevTest Lab."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/01/2015"
	ms.author="tarcher"/>

# Ajout d'une machine virtuelle avec des artefacts à un laboratoire de test et développement Azure DevTest Lab

## Vue d'ensemble

Vous créez une machine virtuelle dans un laboratoire de test et développement (DevTest Lab) à partir d'une image de base Azure ou d'une image que vous avez téléchargée dans votre laboratoire.

Les *artefacts* de laboratoire de test et développement vous permettent de spécifier les actions qui sont exécutées lorsque la machine virtuelle est créée. Les actions des artefacts peuvent effectuer des procédures, comme exécuter des commandes Powershell et Bash, et installer des logiciels. Les *paramètres* des artefacts vous permettent de personnaliser l'artefact pour votre scénario spécifique.

Votre laboratoire comprend des artefacts provenant du référentiel d'artefacts pour le laboratoire de test et développement public, ainsi que les artefacts qui sont créés et ajoutés à votre propre référentiel d'artefacts.

Cet article vous explique comment créer une machine virtuelle dans votre laboratoire à l'aide d'artefacts.

## Ajout d'une machine virtuelle avec des artefacts

1. Connectez-vous au [portail Azure en version préliminaire](https://portal.azure.com).

1. Cliquez sur **Parcourir**, puis cliquez sur **Laboratoires de test et développement** dans la liste.

1. Dans la liste des laboratoires, cliquez sur le laboratoire dans lequel vous souhaitez créer la nouvelle machine virtuelle.

1. Dans le volet du laboratoire, cliquez sur **+ MV de laboratoire** (+ Lab VM) comme indiqué dans l'illustration suivante. ![Volet d'accueil DevTest Lab](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. Dans le volet **MV de laboratoire** (Lab VM), entrez un nom pour la nouvelle machine virtuelle dans la zone de texte **Nom de la MV de laboratoire** (Lab VM Name).

1. Cliquez sur **Base / Configurer les paramètres requis** et sélectionnez une image de base pour la machine virtuelle.

    ![Paramètres de machine virtuelle de laboratoire](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-1.png)

1. Après avoir sélectionné une image de base, le volet **MV de laboratoire** se développe pour inclure les éléments **Nom d'utilisateur** et **Mot de passe**. Entrez un **nom d'utilisateur** qui obtiendra les privilèges d'administrateur sur la machine virtuelle.

    ![Volet MV de laboratoire développé](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-2.png)

1. Entrez un **mot de passe**.

1. Cliquez sur **Taille MV** et sélectionnez l'un des éléments prédéfinis qui spécifient les cœurs du processeur, la taille de la RAM et la taille du disque dur de la machine virtuelle à créer.

1. Cliquez sur **Artefacts** et, dans la liste des artefacts, sélectionnez et configurez les artefacts que vous souhaitez ajouter à l'image de base. **Remarque :** si vous n'êtes pas familier avec l'application DevTest Labs ou avec la configuration des artefacts, passez à la section [Sélection et configuration d'un artefact](#configuring-an-artifact), puis revenez ici lorsque vous avez terminé.

1. Cliquez sur **Créer** pour ajouter la machine virtuelle spécifiée au laboratoire.

1. Le volet du laboratoire affiche l'état de la création de la machine virtuelle ; premièrement comme **Création en cours**, puis comme **En cours d'exécution** après le démarrage de la machine virtuelle. Pour vous connecter à la machine virtuelle, cliquez sur la machine virtuelle et, à partir du volet de la machine virtuelle, cliquez sur **Connexion**.

## Sélection et configuration d'un artefact

Lorsque vous créez une machine virtuelle, vous pouvez ajouter des artefacts en cliquant sur **Artefacts** dans le volet **MV de laboratoire**. Ceci affiche le volet **Ajouter des artefacts** qui vous permet d'ajouter et de configurer les artefacts de votre machine virtuelle à partir du référentiel DevTest Lab officiel (**Official Repo**) et d'artefacts du référentiel de l'équipe.

![Volet Ajouter des artefacts](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

**Ajout d'un artefact à une machine virtuelle**

Suivez ces étapes pour chaque artefact que vous souhaitez ajouter à votre machine virtuelle :

1. Cliquez sur l'artefact souhaité dans le volet **Ajouter des artefacts** pour afficher un volet qui vous permet de spécifier les paramètres de l'artefact.  

2. Entrez les valeurs de paramètres requises et les paramètres facultatifs dont vous avez besoin.

3. Cliquez sur **Ajouter** pour ajouter l'artefact et revenir au volet **Ajouter des artefacts**.

**Modification de l'ordre dans lequel les artefacts sont exécutés**

Lorsque vous ajoutez et configurez des artefacts pour votre machine virtuelle, un lien affichant le nombre d'artefacts en cours est affiché en haut du volet **Ajouter des artefacts**. Par défaut, les actions des artefacts sont exécutées dans l'ordre dans lequel ceux-ci sont ajoutés à la machine virtuelle. Pour modifier l'ordre dans lequel les artefacts sont exécutés, il vous suffit de faire glisser et de déplacer les artefacts dans la liste, puis de cliquer sur **OK** lorsque vous avez terminé.

**Affichage/modification des artefacts sélectionnés**

Suivez ces étapes pour afficher ou modifier les paramètres de vos artefacts sélectionnés :

1. En haut du volet **Ajouter des artefacts**, cliquez sur le lien indiquant le nombre d'artefacts ajoutés à la machine virtuelle.

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Pour afficher ou modifier les paramètres d'un artefact spécifique, cliquez sur cet artefact dans le volet **Artefacts sélectionnés**.

1. Apportez les modifications nécessaires, puis cliquez sur **OK** pour fermer le volet **Ajouter des artefacts**.

1. Cliquez sur **OK** pour fermer le volet **Artefacts sélectionnés**.

1. Cliquez sur **OK** pour fermer le volet **Ajouter des artefacts**.

## Étapes suivantes

Découvrez comment [créer des artefacts personnalisés pour votre machine virtuelle](devtest-lab-artifact-author.md).

<!---HONumber=AcomDC_0128_2016-->