<properties
	pageTitle="Création d’une image personnalisée DevTest Lab à partir d’un fichier VHD | Microsoft Azure"
	description="Apprenez à créer une image personnalisée à partir d’un fichier VHD, qui peut ensuite servir à créer des machines virtuelles dans une instance de service DevTest Lab"
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
	ms.date="03/13/2016"
	ms.author="tarcher"/>

# Création d’une image personnalisée DevTest Lab à partir d’un fichier VHD

## Vue d’ensemble

Une fois que vous avez [créé une instance de service DevTest Lab](devtest-lab-create-lab.md), vous pouvez [ajouter des machines virtuelles à ce laboratoire](devtest-lab-add-vm-with-artifacts.md). Quand vous créez une machine virtuelle, vous spécifiez une *base*, qui peut être une *image personnalisée* ou *image du Marketplace*. Dans cet article, vous allez apprendre à créer une image personnalisée à partir d’un fichier VHD. Notez que vous devez avoir accès à un fichier VHD valide pour effectuer toutes les étapes de cet article.

## Création d’une image personnalisée

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Appuyez sur **Parcourir**, puis sur **DevTest Labs** dans la liste.

1. Dans la liste des labos, sélectionnez le labo souhaité.

1. Le panneau **Paramètres** du laboratoire sélectionné s’affiche.

1. Dans le panneau **Paramètres** du laboratoire, appuyez sur **Images personnalisées**.

    ![Option Images personnalisées](./media/devtest-lab-create-template/lab-settings-custom-images.png)

1. Dans le panneau **Paramètres** du laboratoire, appuyez sur **+ Image personnalisée**.

    ![Ajouter une image personnalisée](./media/devtest-lab-create-template/add-custom-image.png)

1. Entrez le nom de l’image personnalisée. Ce nom s’affiche dans la liste des images de base quand vous créez une machine virtuelle.

1. Entrez la description de l’image personnalisée. Cette description s’affiche dans la liste des images de base quand vous créez une machine virtuelle.

1. Appuyez sur **Fichier VHD**.

1. Si vous avez accès à un fichier VHD qui n’est pas répertorié, ajoutez-le en suivant les instructions de la section [Télécharger un fichier VHD](#upload-a-vhd-file) et revenez ici après avoir terminé.

1. Sélectionnez le fichier VHD souhaité.

1. Appuyez sur **OK** pour fermer le panneau **Fichier VHD**.

1. Appuyez sur **Configuration du système d’exploitation**.

1. Dans l’onglet **Configuration du système d’exploitation**, sélectionnez **Windows** ou **Linux**.

1. Si vous sélectionnez **Windows**, cochez la case pour indiquer si *Sysprep* a été exécuté sur la machine.

1. Sélectionnez **OK** pour fermer le panneau **Configuration du système d’exploitation**.

1. Appuyez sur **OK** pour créer l’image personnalisée.

1. Accédez à la section [Étapes suivantes](#next-steps).

##Téléchargement d’un fichier VHD

Pour ajouter une nouvelle image personnalisée, vous devez avoir accès à un fichier VHD.

1. Dans le panneau **Fichier VHD**, appuyez sur **Télécharger un fichier VHD à l’aide de PowerShell**.

    ![Télécharger une image](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. Le panneau suivant affiche des instructions pour modifier et exécuter un script PowerShell qui télécharge un fichier VHD sur votre abonnement Azure. **Remarque :** la taille du fichier VHD et votre vitesse de connexion influent sur la durée de ce processus.

##Étapes suivantes

Une fois que vous avez ajouté une image personnalisée à utiliser pendant la création d’une machine virtuelle, l’étape suivante consiste à [ajouter une machine virtuelle à votre instance du service DevTest Lab](./devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0316_2016-->