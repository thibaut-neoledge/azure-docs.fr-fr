<properties
	pageTitle="Ajout d’une machine virtuelle avec des artefacts à un DevTest Lab | Microsoft Azure"
	description="Découvrez comment ajouter une machine virtuelle avec des artefacts à un laboratoire de test et développement DevTest Lab"
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
	ms.date="03/21/2016"
	ms.author="tarcher"/>

# Ajout d’une machine virtuelle avec des artefacts à un laboratoire de test et développement DevTest Lab

> [AZURE.NOTE] Cliquez sur le lien suivant pour afficher la vidéo d’accompagnement de cet article : [Créer des machines virtuelles avec des artefacts dans un atelier DevTest Labs](/documentation/videos/how-to-create-vms-with-artifacts-in-a-devtest-lab)

## Vue d'ensemble

Vous créez une machine virtuelle dans un laboratoire de test et développement (DevTest Lab) à partir d’une image de base qui est [personnalisée](./devtest-lab-create-template.md) ou une image Marketplace.

Les *artefacts* de laboratoire de test et développement vous permettent de spécifier des *actions* qui sont exécutées lorsque la machine virtuelle est créée.

Les actions des artefacts peuvent réaliser des procédures, telles que l’exécution de scripts Windows PowerShell, l’exécution de commandes Bash et l’installation de logiciels.

Les *paramètres* des artefacts vous permettent de personnaliser l'artefact pour votre scénario spécifique.

Cet article vous explique comment créer une machine virtuelle dans votre laboratoire à l’aide d’artefacts.

## Ajout d'une machine virtuelle avec des artefacts

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Appuyez sur **Parcourir**, puis sur **DevTest Labs** dans la liste.

1. Dans la liste des laboratoires, cliquez sur le laboratoire dans lequel vous souhaitez créer la nouvelle machine virtuelle.

1. Dans le volet du laboratoire, cliquez sur **+ MV de laboratoire** (+ Lab VM) comme indiqué dans l'illustration suivante. ![Volet d'accueil DevTest Lab](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. Dans le volet **MV de laboratoire** (Lab VM), entrez un nom pour la nouvelle machine virtuelle dans la zone de texte **Nom de la MV de laboratoire** (Lab VM Name).

1. Cliquez sur **Base / Configurer les paramètres requis** et sélectionnez une image de base pour la machine virtuelle.

    ![Paramètres de machine virtuelle de laboratoire](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-1.png)

1. Une fois que vous avez sélectionné une image de base et appuyé sur **OK**, le panneau **MV de laboratoire** se développe pour inclure les éléments permettant de spécifier les informations de compte d’utilisateur, notamment le **Nom d’utilisateur**, le **type d’identification** (si le type de système d’exploitation pour la base sélectionnée est Linux) et le **Mot de passe** (si le type d’authentification *Mot de passe* est sélectionné).

    ![Volet MV de laboratoire développé](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-2.png)

1. Entrez un **nom d'utilisateur** qui obtiendra les privilèges d'administrateur sur la machine virtuelle.

1. Si le type de système d’exploitation de la base sélectionnée est Linux, spécifiez *Mot de passe* ou *Clé publique SSH* comme type d’authentification.

1. Selon le type d’authentification spécifié, entrez un mot de passe ou une clé publique SSH.

1. Cliquez sur **Taille MV** et sélectionnez l'un des éléments prédéfinis qui spécifient les cœurs du processeur, la taille de la RAM et la taille du disque dur de la machine virtuelle à créer.

1. Cliquez sur **Réseau virtuel**, puis sélectionnez le réseau virtuel souhaité.

1. Cliquez sur **Sous-réseau** et sélectionnez le sous-réseau.

1. Si la stratégie du laboratoire est définie de manière à autoriser des adresses IP publiques pour le sous-réseau sélectionné, spécifiez si vous souhaitez ou non que l’adresse IP soit public en sélectionnant **Oui** ou **non**. Dans le cas contraire, cette option est désactivée et paramétrée sur **Non**.

1. Cliquez sur **Artefacts** et, dans la liste des artefacts, sélectionnez et configurez les artefacts que vous souhaitez ajouter à l'image de base. **Remarque :** si vous n'êtes pas familier avec DevTest Labs ou avec la configuration d’artefacts, passez à la section [Ajout d’un artefact existant à une machine virtuelle](#add-an-existing-artifact-to-a-vm), puis revenez ici lorsque vous avez terminé.

1. Cliquez sur **Créer** pour ajouter la machine virtuelle spécifiée au laboratoire.

1. Le volet du laboratoire affiche l'état de la création de la machine virtuelle ; premièrement comme **Création en cours**, puis comme **En cours d'exécution** après le démarrage de la machine virtuelle.

1. Accédez à la section [Étapes suivantes](#next-steps).

## Ajout d’un artefact existant à une machine virtuelle

Au cours de la création d’une machine virtuelle, vous pouvez ajouter des artefacts existants. Chaque laboratoire comprend des artefacts provenant du référentiel d'artefacts pour le laboratoire de test et développement public, ainsi que les artefacts que vous avez créés et ajoutés à votre propre référentiel d'artefacts. Pour découvrir comment créer des artefacts, consultez l’article [Découvrez comment créer vos propres artefacts pour les utiliser avec DevTest Labs](devtest-lab-artifact-author.md).

1. Sur le panneau **MV de laboratoire**, appuyez sur **Artefacts**. 

1. Sur le panneau **Ajouter des artefacts**, cliquez sur l’objet souhaité.

![Volet Ajouter des artefacts](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

1. Entrez les valeurs de paramètres requises et les paramètres facultatifs dont vous avez besoin.  

1. Cliquez sur **Ajouter** pour ajouter l'artefact et revenir au volet **Ajouter des artefacts**.

1. Continuez à ajouter des artefacts en fonction des besoins de votre machine virtuelle.

1. Une fois que vous avez ajouté vos artefacts, vous pouvez [modifier l’ordre dans lequel les artefacts sont exécutés](#change-the-order-in-which-artifacts-are-run). Vous pouvez également revenir à [Affichage ou modification d’un artefact](#view-or-modify-an-artifact).

## Modification de l'ordre dans lequel les artefacts sont exécutés

Par défaut, les actions des artefacts sont exécutées dans l'ordre dans lequel ceux-ci sont ajoutés à la machine virtuelle. Les étapes suivantes montrent comment modifier l’ordre dans lequel les artefacts sont exécutés.

1. En haut du panneau **Ajouter des artefacts**, cliquez sur le lien indiquant le nombre d'artefacts ajoutés à la machine virtuelle.

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Exécution d’un glisser-déplacer des artefacts dans la liste en fonction de l’ordre souhaité. **Remarque :** si vous avez des difficultés à faire glisser l’artefact, assurez-vous que vous effectuez l’opération depuis le côté gauche de l’artefact.

1. Cliquez sur **OK** lorsque vous avez terminé.

## Affichage ou modification d’un artefact

Les étapes suivantes montrent comment afficher ou modifier les paramètres d’un artefact :

1. En haut du panneau **Ajouter des artefacts**, cliquez sur le lien indiquant le nombre d'artefacts ajoutés à la machine virtuelle.

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Sur le panneau **Artefacts sélectionnés**, cliquez sur l’artefact que vous souhaitez afficher ou modifier.

1. Dans le panneau **Ajouter des artefacts**, apportez les modifications nécessaires, puis appuyez sur **OK** pour fermer le panneau **Ajouter des artefacts**.

1. Cliquez sur **OK** pour fermer le volet **Artefacts sélectionnés**.

## Étapes suivantes

- Une fois la machine virtuelle créée, vous pouvez vous y connecter en cliquant sur **Connexion** sur son panneau.
- Découvrez comment créer des artefacts en consultant l’article [Découvrez comment créer vos propres artefacts pour les utiliser avec DevTest Labs](devtest-lab-artifact-author.md).

<!---HONumber=AcomDC_0323_2016-->