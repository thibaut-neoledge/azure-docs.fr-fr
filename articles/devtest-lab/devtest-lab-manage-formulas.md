<properties
	pageTitle="Gérer les formules DevTest Labs pour créer des machines virtuelles | Microsoft Azure"
	description="Découvrez comment créer, mettre à jour et supprimer des formules DevTest Labs et les utiliser pour créer de nouvelles machines virtuelles."
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
	ms.date="05/08/2016"
	ms.author="tarcher"/>

# Gérer les formules DevTest Labs pour créer des machines virtuelles

## Vue d'ensemble

Les formules, comme les [images personnalisées](./devtest-lab-create-template.md) et les [images Marketplace](./devtest-lab-configure-marketplace-images.md), représentent un mécanisme d’approvisionnement rapide de machines virtuelles. Dans DevTest Labs, une formule est une liste de valeurs de propriétés par défaut utilisée pour créer une machine virtuelle de laboratoire. Quand vous créez une machine virtuelle à partir d’une formule, les valeurs par défaut peuvent être utilisées telles quelles ou modifiées.

Dans cet article, vous allez apprendre à effectuer les tâches suivantes :

- [Créer une formule](#create-a-new-formula)
- [Utiliser une formule pour créer une machine virtuelle](#use-a-formula-to-create-a-new-vm)
- [Modifier une formule](#modify-a-formula)
- [Supprimer une formule](#delete-a-formula)

> [AZURE.NOTE] Les formules sont similaires aux [images personnalisées](./devtest-lab-create-template.md) dans la mesure où toutes deux vous permettent de créer une image de base à partir d’un disque dur virtuel utilisé pour approvisionner une machine virtuelle. Pour déterminer ce qui est adapté à votre environnement particulier, reportez-vous à l’article [Comparaison entre les images personnalisées et les formules dans DevTest Labs](./devtest-lab-comparing-vm-base-image-types.md).

## Créer une formule
Toute personne disposant des autorisations *Utilisateurs* de DevTest Labs est en mesure de créer des machines virtuelles dans un labo en utilisant une formule comme base. Il existe deux façons de créer des formules :

- À partir de zéro : quand vous voulez définir toutes les caractéristiques de la formule à partir de zéro.
- À partir d’une machine virtuelle de laboratoire existante : quand vous voulez créer une formule à partir des paramètres d’une machine virtuelle existante.

### Créer une formule à partir de zéro
Les étapes suivantes vous guident dans le processus de création d’une formule à partir de zéro.

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Appuyez sur **Parcourir**, puis sur **DevTest Labs** dans la liste.

1. Dans la liste des labos, sélectionnez le labo souhaité.

1. Le panneau **Paramètres** du labo sélectionné s’affiche.

1. Dans le panneau **Paramètres** du labo, appuyez sur **Formules**.

    ![Menu de formule](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. Sur le panneau **Formules de labo**, appuyez sur **+ Ajouter**.

    ![Ajouter une formule](./media/devtest-lab-manage-formulas/add-formula.png)

1. Dans le panneau **Choisir une base**, cliquez sur l’image personnalisée ou l’image Marketplace à partir de laquelle vous voulez créer la formule.

    ![Liste de base](./media/devtest-lab-manage-formulas/base-list.png)

1. Sur le panneau **Créer une formule**, spécifiez les valeurs suivantes :

	- **Nom de la formule** : entrez un nom pour votre formule. Cette valeur s'affichera dans la liste des images de base lorsque vous créerez une machine virtuelle. Le nom est validé au fur et à mesure que vous le tapez. S’il n’est pas valide, un message indique les exigences d’un nom valide.
	- **Description** : entrez une description significative pour votre formule. Cette valeur est disponible dans le menu contextuel de la formule quand vous créez une machine virtuelle.
	- **Image** : ce champ affiche le nom de l'image de base que vous avez sélectionnée dans le panneau précédent. 
	- **Taille de la machine virtuelle** : sélectionnez l'un des éléments prédéfinis qui spécifient les cœurs du processeur, la taille de la RAM et la taille du disque dur de la machine virtuelle à créer.
	- **Réseau virtuel** : sélectionnez le réseau virtuel souhaité.
	- **Sous-réseau** : sélectionnez le sous-réseau souhaité.
	- **Adresse IP publique** : si la stratégie du labo est définie de manière à autoriser des adresses IP publiques pour le sous-réseau sélectionné, spécifiez si vous souhaitez ou non que l’adresse IP soit publique en sélectionnant **Oui** ou **Non**. Dans le cas contraire, cette option est désactivée et paramétrée sur **Non**.
	- **Artefacts** : dans la liste des artefacts, sélectionnez et configurez les artefacts que vous souhaitez ajouter à l'image de base. Notez que les paramètres d’artefact qui sont des chaînes sécurisées ne s’affichent pas car la formule n’enregistre pas les valeurs des chaînes sécurisées. 

    	![Créer une formule](./media/devtest-lab-manage-formulas/create-formula.png)

1. Cliquez sur **Créer** pour créer la formule. Une fois la formule créée, elle est répertoriée dans le panneau **Formules de laboratoire**.

	![Formule juste créée](./media/devtest-lab-manage-formulas/newly-created-formula.png)

### Créer une formule à partir d’une machine virtuelle de laboratoire
Les étapes suivantes vous guident dans le processus de création d’une formule à partir d’une machine virtuelle existante.

> [AZURE.NOTE] Seules les machines virtuelles créées après le 30 mars 2016 prennent en charge la création d’une formule à partir d’une machine virtuelle de laboratoire.

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Appuyez sur **Parcourir**, puis sur **DevTest Labs** dans la liste.

1. Dans la liste des labos, sélectionnez le labo souhaité.

1. Dans le panneau du laboratoire, recherchez la section nommée **Mes machines virtuelles**, puis cliquez sur la machine virtuelle à partir de laquelle vous voulez créer la nouvelle formule.

	![Machines virtuelles de labo](./media/devtest-lab-manage-formulas/my-vms.png)

1. Dans le panneau **Paramètres** de la machine virtuelle, cliquez sur **Créer une formule**.

	![Créer une formule](./media/devtest-lab-manage-formulas/create-formula-menu.png)

1. Dans le panneau **Créer une formule**, entrez un **Nom** et une **Description** pour votre nouvelle formule, puis cliquez sur **OK**. Une fois la formule créée, elle est répertoriée dans le panneau **Formules de laboratoire**.

	![Panneau Créer une formule](./media/devtest-lab-manage-formulas/create-formula-blade.png)

## Modifier une formule
Pour modifier une formule, procédez comme suit :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Appuyez sur **Parcourir**, puis sur **DevTest Labs** dans la liste.

1. Dans la liste des labos, sélectionnez le labo souhaité.

1. Dans le panneau **Paramètres** du labo, appuyez sur **Formules**.

    ![Menu de formule](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. Dans le panneau **Formules de laboratoire**, cliquez sur la formule à modifier.

1. Dans le panneau **Mettre à jour la formule**, effectuez les modifications souhaitées, puis cliquez sur **Mettre à jour**.

## Supprimer une formule 
Pour supprimer une formule, procédez comme suit :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Appuyez sur **Parcourir**, puis sur **DevTest Labs** dans la liste.

1. Dans la liste des labos, sélectionnez le labo souhaité.

1. Dans le panneau **Paramètres** du labo, appuyez sur **Formules**.

    ![Menu de formule](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. Dans le panneau **Formules de laboratoire**, cliquez sur les points de suspension à droite de la formule à supprimer.

    ![Menu de formule](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)

1. Dans le menu contextuel de la formule, sélectionnez **Supprimer**.

    ![Menu contextuel de la formule](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)

1. Cliquez sur **Oui** dans la boîte de dialogue de confirmation de suppression.

    ![Menu contextuel de la formule](./media/devtest-lab-manage-formulas/formula-delete-confirmation.png)

## Étapes suivantes
Quand vous avez créé une formule à utiliser lors de la création d’une machine virtuelle, l’étape suivante consiste à [ajouter une machine virtuelle à votre laboratoire](./devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0518_2016-->