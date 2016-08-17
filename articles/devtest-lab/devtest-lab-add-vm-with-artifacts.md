<properties
	pageTitle="Ajouter une machine virtuelle avec des artefacts à un laboratoire| Microsoft Azure"
	description="Découvrez comment ajouter une machine virtuelle avec des artefacts dans DevTest Labs"
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
	ms.date="08/01/2016"
	ms.author="tarcher"/>

# Ajouter une machine virtuelle avec des artefacts à un laboratoire

> [AZURE.VIDEO how-to-create-vms-with-artifacts-in-a-devtest-lab]

## Vue d'ensemble

Vous créez une machine virtuelle dans un laboratoire à partir d’une *base* qui est une [image personnalisée](./devtest-lab-create-template.md), une [formule](./devtest-lab-manage-formulas.md) ou une [image Marketplace](./devtest-lab-configure-marketplace-images.md).

Les *artefacts* de DevTest Labs vous permettent de spécifier des *actions* qui sont exécutées quand la machine virtuelle est créée.

Les actions des artefacts peuvent réaliser des procédures, telles que l’exécution de scripts Windows PowerShell, l’exécution de commandes Bash et l’installation de logiciels.

Les *paramètres* des artefacts vous permettent de personnaliser l'artefact pour votre scénario spécifique.

Cet article vous explique comment créer une machine virtuelle dans votre laboratoire à l’aide d’artefacts.

## Ajout d'une machine virtuelle avec des artefacts

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Sélectionnez **Parcourir**, puis **DevTest Labs** dans la liste.

1. Dans la liste des laboratoires, sélectionnez le laboratoire dans lequel vous souhaitez créer la nouvelle machine virtuelle.

1. Dans le volet du laboratoire, sélectionnez **+ MV de laboratoire** (+ Lab VM) comme indiqué dans l’illustration suivante. ![Bouton Ajouter une machine virtuelle de laboratoire](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. Dans le panneau **Choisir une base**, sélectionnez une base pour la machine virtuelle.

1. Dans le volet **MV de laboratoire** (Lab VM), entrez un nom pour la nouvelle machine virtuelle dans la zone de texte **Nom de la MV de laboratoire** (Lab VM Name).

	![Panneau Machine virtuelle de laboratoire](./media/devtest-lab-add-vm-with-artifacts/devtestlab-lab-vm-blade.png)

1. Entrez un **nom d'utilisateur** qui obtiendra les privilèges d'administrateur sur la machine virtuelle.

1. Si le type de système d’exploitation de la base sélectionnée est Linux, spécifiez *Mot de passe* ou *Clé publique SSH* comme type d’authentification.

1. Selon le type d’authentification spécifié, entrez un mot de passe ou une clé publique SSH.

1. Sélectionnez **Taille de machine virtuelle**, puis l’un des éléments prédéfinis qui spécifient les cœurs du processeur, la taille de la RAM et la taille du disque dur de la machine virtuelle à créer.

1. Sélectionnez **Réseau virtuel**, puis le réseau virtuel souhaité.

1. Sélectionnez **Sous-réseau**, puis le sous-réseau.

1. Si la stratégie du laboratoire est définie de façon à autoriser des adresses IP publiques pour le sous-réseau sélectionné, spécifiez si vous voulez ou non que l’adresse IP soit publique en sélectionnant **Oui** ou **non**. Dans le cas contraire, cette option est désactivée et paramétrée sur **Non**.

1. Sélectionnez **Artefacts** et, dans la liste des artefacts, sélectionnez et configurez les artefacts que vous souhaitez ajouter à l’image de base. **Remarque :** si vous n’êtes pas familier avec DevTest Labs ou avec la configuration d’artefacts, passez à la section [Ajout d’un artefact existant à une machine virtuelle](#add-an-existing-artifact-to-a-vm), puis revenez ici quand vous avez terminé.

1. Si vous voulez afficher ou copier le modèle ARM, passez à la section [Enregistrer un modèle ARM](#save-arm-template), puis revenez ici quand vous avez terminé.

1. Sélectionnez **Créer** pour ajouter la machine virtuelle spécifiée au laboratoire.

1. Le volet du laboratoire affiche l'état de la création de la machine virtuelle ; premièrement comme **Création en cours**, puis comme **En cours d'exécution** après le démarrage de la machine virtuelle.

1. Accédez à la section [Étapes suivantes](#next-steps).

## Ajout d’un artefact existant à une machine virtuelle

Au cours de la création d’une machine virtuelle, vous pouvez ajouter des artefacts existants. Chaque laboratoire comprend des artefacts provenant du dépôt d’artefacts DevTest Labs public, ainsi que les artefacts que vous avez créés et ajoutés à votre propre dépôt d’artefacts. Pour découvrir comment créer des artefacts, consultez l’article [Découvrez comment créer vos propres artefacts pour les utiliser avec DevTest Labs](devtest-lab-artifact-author.md).

1. Dans le panneau **Machine virtuelle de laboratoire**, sélectionnez **Artefacts**.

1. Dans le panneau **Ajouter des artefacts**, sélectionnez l’artefact souhaité.

![Volet Ajouter des artefacts](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

1. Entrez les valeurs de paramètres requises et les paramètres facultatifs dont vous avez besoin.

1. Sélectionnez **Ajouter** pour ajouter l’artefact et revenir au volet **Ajouter des artefacts**.

1. Continuez à ajouter des artefacts en fonction des besoins de votre machine virtuelle.

1. Une fois que vous avez ajouté vos artefacts, vous pouvez [modifier l’ordre dans lequel les artefacts sont exécutés](#change-the-order-in-which-artifacts-are-run). Vous pouvez également revenir à [Afficher ou modifier un artefact](#view-or-modify-an-artifact).

## Modification de l'ordre dans lequel les artefacts sont exécutés

Par défaut, les actions des artefacts sont exécutées dans l'ordre dans lequel ceux-ci sont ajoutés à la machine virtuelle. Les étapes suivantes montrent comment modifier l’ordre dans lequel les artefacts sont exécutés.

1. En haut du panneau **Ajouter des artefacts**, sélectionnez le lien indiquant le nombre d’artefacts ajoutés à la machine virtuelle.

    ![Nombre d’artefacts ajoutés à la machine virtuelle](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Exécution d’un glisser-déplacer des artefacts dans la liste en fonction de l’ordre souhaité. **Remarque :** si vous avez des difficultés à faire glisser l’artefact, vérifiez que vous effectuez l’opération depuis le côté gauche de l’artefact.

1. Cliquez sur **OK** quand vous avez terminé.

## Affichage ou modification d’un artefact

Les étapes suivantes montrent comment afficher ou modifier les paramètres d’un artefact :

1. En haut du panneau **Ajouter des artefacts**, sélectionnez le lien indiquant le nombre d’artefacts ajoutés à la machine virtuelle.

    ![Nombre d’artefacts ajoutés à la machine virtuelle](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Dans le panneau **Artefacts sélectionnés**, sélectionnez l’artefact que vous voulez afficher ou modifier.

1. Dans le panneau **Ajouter un artefact**, apportez les modifications nécessaires, puis cliquez sur **OK** pour fermer le panneau **Ajouter un artefact**.

1. Cliquez sur **OK** pour fermer le panneau **Artefacts sélectionnés**.

## Enregistrer un modèle ARM

Un modèle ARM constitue un moyen déclaratif de définir un déploiement qui peut être répété. Les étapes suivantes expliquent comment enregistrer le modèle ARM pour la machine virtuelle en cours de création. Une fois enregistré, vous pouvez utiliser le modèle ARM pour [déployer de nouvelles machines virtuelles avec Azure PowerShell](../resource-group-overview.md#template-deployment).

1. Dans le panneau **Machine virtuelle de laboratoire**, sélectionnez **Afficher le modèle ARM**.

1. Dans le panneau **Afficher le modèle Azure Resource Manager**, sélectionnez tout le texte du modèle.

1. Copiez le texte sélectionné dans le Presse-papiers.

1. Cliquez sur **OK** pour fermer le panneau **Afficher le modèle Azure Resource Manager**.

1. Ouvrez un éditeur de texte.

1. Collez le texte du modèle depuis le Presse-papiers.

1. Enregistrez le fichier pour une utilisation ultérieure.

## Étapes suivantes

- Une fois la machine virtuelle créée, vous pouvez vous y connecter en sélectionnant **Connexion** dans le panneau de la machine virtuelle.
- Découvrez comment [créer des artefacts personnalisés pour vos machines virtuelles DevTest Labs](devtest-lab-artifact-author.md).
- Explorer la [Galerie de modèles de démarrage rapide d’ARM DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)

<!---HONumber=AcomDC_0803_2016-->