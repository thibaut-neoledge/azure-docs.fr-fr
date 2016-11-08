---
title: Gérer les formules dans Azure DevTest Labs pour créer des machines virtuelles | Microsoft Docs
description: Découvrez comment créer, mettre à jour et supprimer des formules Azure DevTest Labs et les utiliser pour créer de nouvelles machines virtuelles.
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: ''

ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: tarcher

---
# Gérer les formules DevTest Labs pour créer des machines virtuelles
Dans Azure DevTest Labs, une formule est une liste de valeurs de propriétés par défaut utilisée pour créer une machine virtuelle (VM). Quand vous créez une machine virtuelle à partir d’une formule, les valeurs par défaut peuvent être utilisées telles quelles ou modifiées. Comme les [images personnalisées](devtest-lab-create-template.md) et les [images Marketplace](devtest-lab-configure-marketplace-images.md), les formules représentent un mécanisme d’approvisionnement rapide de machines virtuelles.

Dans cet article, vous allez apprendre à effectuer les tâches suivantes :

* [Créer une formule](#create-a-formula)
* [Utiliser une formule pour mettre en service une machine virtuelle](#use-a-formula-to-provision-a-vm)
* [Modifier une formule](#modify-a-formula)
* [Supprimer une formule](#delete-a-formula)

> [!NOTE]
> Les formules, comme les [images personnalisées](devtest-lab-create-template.md), vous permettent de créer une image de base à partir d’un fichier de disque dur virtuel. L’image de base peut ensuite servir à approvisionner une nouvelle machine virtuelle. Pour déterminer ce qui est adapté à votre environnement particulier, reportez-vous à l’article [Comparaison entre les images personnalisées et les formules dans DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).
> 
> 

## Créer une formule
Toute personne disposant des autorisations *Utilisateurs* de DevTest Labs est en mesure de créer des machines virtuelles en utilisant une formule comme base. Il existe deux façons de créer des formules :

* À partir d’une base : quand vous voulez définir toutes les caractéristiques de la formule.
* À partir d’une machine virtuelle de laboratoire existante : quand vous voulez créer une formule à partir des paramètres d’une machine virtuelle existante.

### Créer une formule à partir d’une base
Les étapes suivantes vous guideront tout au long du processus de création d’une formule à partir d’une image personnalisée, image Marketplace ou autre formule.

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Plus de services**, puis **DevTest Labs** dans la liste.
3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.
4. Dans le panneau du laboratoire, sélectionnez **Formules (bases réutilisables)**.
   
    ![Menu de formule](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Sur le panneau des **formules de laboratoire**, sélectionnez **+ Ajouter**.
   
    ![Ajouter une formule](./media/devtest-lab-manage-formulas/add-formula.png)
6. Dans le panneau **Choisir une base**, sélectionnez la base (l’image personnalisée, l’image Marketplace ou la formule) à partir de laquelle vous voulez créer la formule.
   
    ![Liste de base](./media/devtest-lab-manage-formulas/base-list.png)
7. Sur le panneau **Créer une formule**, spécifiez les valeurs suivantes :
   
   * **Nom de la formule** : entrez un nom pour votre formule. Cette valeur s'affichera dans la liste des images de base lorsque vous créerez une machine virtuelle. Le nom est validé au fur et à mesure que vous le tapez. S’il n’est pas valide, un message indique les exigences d’un nom valide.
   * **Description** : entrez une description significative pour votre formule. Cette valeur est disponible dans le menu contextuel de la formule quand vous créez une machine virtuelle.
   * **Nom d’utilisateur** - Entrez un nom d'utilisateur qui obtiendra les privilèges d'administrateur.
   * **Mot de passe** - Entrez, ou sélectionnez dans la liste déroulante, une valeur qui est associée au secret (mot de passe) que vous souhaitez utiliser pour l’utilisateur spécifié.
   * **Image** : ce champ affiche le nom de l'image de base que vous avez sélectionnée dans le panneau précédent.
   * **Taille de machine virtuelle** - Sélectionnez un des éléments prédéfinis qui spécifient les cœurs du processeur, la taille de la RAM et la taille du disque dur de la machine virtuelle à créer.
   * **Réseau virtuel** - Spécifiez le réseau virtuel souhaité.
   * **Sous-réseau** - Spécifiez le sous-réseau souhaité.
   * **Adresse IP publique** : si la stratégie du labo est définie de manière à autoriser des adresses IP publiques pour le sous-réseau sélectionné, spécifiez si vous souhaitez que l’adresse IP soit publique en sélectionnant **Oui** ou **Non**. Dans le cas contraire, cette option est désactivée et paramétrée sur **Non**.
   * **Artefacts** : Sélectionnez et configurez les artefacts que vous souhaitez ajouter à l'image de base. Les valeurs de chaîne sécurisées ne sont pas enregistrées avec la formule. Par conséquent, les paramètres d’artefact qui sont des chaînes sécurisées ne sont pas affichés.
     
       ![Créer une formule](./media/devtest-lab-manage-formulas/create-formula.png)
8. sélectionnez **Créer** pour créer la formule.

### Créer une formule depuis une machine virtuelle
Les étapes suivantes vous guident dans le processus de création d’une formule à partir d’une machine virtuelle existante.

> [!NOTE]
> Pour créer une formule à partir d’une machine virtuelle, la machine virtuelle doit avoir été créée après le 30 mars 2016.
> 
> 

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Plus de services**, puis **DevTest Labs** dans la liste.
3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.
4. Dans le panneau **Présentation** du laboratoire, sélectionnez la machine virtuelle à partir de laquelle vous souhaitez créer la formule.
   
    ![Machines virtuelles de labo](./media/devtest-lab-manage-formulas/my-vms.png)
5. Dans le panneau de la machine virtuelle, cliquez sur **Créer une formule (base réutilisable)**.
   
    ![Créer une formule](./media/devtest-lab-manage-formulas/create-formula-menu.png)
6. Dans le panneau **Créer une formule**, entrez un **Nom** et une **Description** pour votre nouvelle formule.
   
    ![Panneau Créer une formule](./media/devtest-lab-manage-formulas/create-formula-blade.png)
7. Sélectionnez **OK** pour créer la formule.

## Utiliser une formule pour mettre en service une machine virtuelle
Une fois que vous avez créé une formule, vous pouvez créer une machine virtuelle sur la base de cette formule. La section [Ajouter une machine virtuelle avec des artefacts](devtest-lab-add-vm-with-artifacts.md#add-a-vm-with-artifacts) vous guide tout au long du processus.

## Modifier une formule
Pour modifier une formule, procédez comme suit :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Plus de services**, puis **DevTest Labs** dans la liste.
3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.
4. Dans le panneau du laboratoire, sélectionnez **Formules (bases réutilisables)**.
   
    ![Menu de formule](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Dans le panneau des **formules de laboratoire**, sélectionnez la formule à modifier.
6. Dans le panneau **Mettre à jour la formule**, effectuez les modifications souhaitées, puis sélectionnez **Mettre à jour**.

## Supprimer une formule
Pour supprimer une formule, procédez comme suit :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Plus de services**, puis **DevTest Labs** dans la liste.
3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.
4. Dans le panneau **Paramètres** du laboratoire, sélectionnez **Formules**.
   
    ![Menu de formule](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Dans le panneau des **formules de laboratoire**, sélectionnez les points de suspension à droite de la formule à supprimer.
   
    ![Menu de formule](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. Dans le menu contextuel de la formule, sélectionnez **Supprimer**.
   
    ![Menu contextuel de la formule](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. sélectionnez **Oui** dans la boîte de dialogue de confirmation de la suppression.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Billets de blog connexes
* [Images personnalisées ou formules ?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## Étapes suivantes
Quand vous avez créé une formule à utiliser lors de la création d’une machine virtuelle, l’étape suivante consiste à [ajouter une machine virtuelle à votre laboratoire](devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0907_2016-->