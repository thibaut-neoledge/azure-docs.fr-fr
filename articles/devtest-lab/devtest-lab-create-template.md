<properties
	pageTitle="Créer une image personnalisée DevTest Labs à partir d’un fichier VHD | Microsoft Azure"
	description="Découvrez comment créer une image personnalisée à partir d’un fichier VHD, qui peut ensuite être utilisé pour créer des machines virtuelles dans DevTest Labs"
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
	ms.date="08/02/2016"
	ms.author="tarcher"/>

# Créer une image personnalisée DevTest Labs à partir d’un fichier VHD

## Vue d'ensemble

Une fois que vous avez [créé un laboratoire](devtest-lab-create-lab.md), vous pouvez [ajouter des machines virtuelles à ce laboratoire](devtest-lab-add-vm-with-artifacts.md). Quand vous créez une machine virtuelle, vous spécifiez une *base*, qui peut être une *image personnalisée* ou *image du Marketplace*. Dans cet article, vous allez apprendre à créer une image personnalisée à partir d’un fichier VHD. Notez que vous devez avoir accès à un fichier VHD valide pour effectuer toutes les étapes de cet article.

## Créer une image personnalisée

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Sélectionnez **Parcourir**, puis **DevTest Labs** dans la liste.

1. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.

1. Le panneau **Paramètres** du laboratoire sélectionné s’affiche.

1. Dans le panneau **Paramètres** du laboratoire, sélectionnez **Images personnalisées**.

    ![Option Images personnalisées](./media/devtest-lab-create-template/lab-settings-custom-images.png)

1. Dans le panneau **Images personnalisées**, sélectionnez **+ Image personnalisée**.

    ![Ajouter une image personnalisée](./media/devtest-lab-create-template/add-custom-image.png)

1. Entrez le nom de l’image personnalisée. Ce nom s’affiche dans la liste des images de base quand vous créez une machine virtuelle.

1. Entrez la description de l’image personnalisée. Cette description s’affiche dans la liste des images de base quand vous créez une machine virtuelle.

1. Sélectionnez **Fichier VHD**.

1. Si vous avez accès à un fichier VHD qui n’est pas répertorié, ajoutez-le en suivant les instructions de la section [Télécharger un fichier VHD](#upload-a-vhd-file) et revenez ici après avoir terminé.

1. Sélectionnez le fichier VHD souhaité.

1. Cliquez sur **OK** pour fermer le panneau **Fichier VHD**.

1. Sélectionnez **Configuration du système d’exploitation**.

1. Dans l’onglet **Configuration du système d’exploitation**, sélectionnez **Windows** ou **Linux**.

1. Si vous sélectionnez **Windows**, cochez la case pour indiquer si *Sysprep* a été exécuté sur la machine.

1. Cliquez sur **OK** pour fermer le panneau **Configuration du système d’exploitation**.

1. Cliquez sur **OK** pour créer l’image personnalisée.

1. Accédez à la section [Étapes suivantes](#next-steps).

##Téléchargement d’un fichier VHD

Pour ajouter une nouvelle image personnalisée, vous devez avoir accès à un fichier VHD.

1. Dans le panneau **Fichier VHD**, sur **Télécharger un fichier VHD à l’aide de PowerShell**.

    ![Télécharger une image](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. Le panneau suivant affiche des instructions pour modifier et exécuter un script PowerShell qui télécharge un fichier VHD sur votre abonnement Azure. **Remarque :** la taille du fichier VHD et votre vitesse de connexion influent sur la durée de ce processus.

## Billets de blog connexes

- [Custom images or formulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/) (Images personnalisées ou formules ?)
- [Copying Custom Images between Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs) (Copie d’images personnalisées entre plusieurs Azure DevTest Labs)

##Étapes suivantes

Une fois que vous avez ajouté une image personnalisée à utiliser lors de la création d’une machine virtuelle, l’étape suivante consiste à [ajouter une machine virtuelle à votre laboratoire](./devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0803_2016-->