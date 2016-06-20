<properties
	pageTitle="Configurer les paramètres d’une image Azure Marketplace dans un laboratoire | Microsoft Azure"
	description="Configurer quelles images Azure Marketplace peuvent être utilisées lors de la création d’une machine virtuelle dans DevTest Labs"
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
	ms.date="06/01/2016"
	ms.author="tarcher"/>

# Configurer les paramètres d’une image Azure Marketplace dans un laboratoire

## Vue d'ensemble

DevTest Labs prend en charge la création de nouvelles machines virtuelles basées sur des images Azure Marketplace en fonction de la manière dont vous avez configuré des images Azure Marketplace à utiliser dans votre laboratoire. Cet article explique comment spécifier, le cas échéant, les images Azure Marketplace qui peuvent être utilisées lors de la création de machines virtuelles dans un laboratoire.

## Sélection des images Azure Marketplace autorisées lors de la création d’une machine virtuelle

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Sélectionnez **Parcourir**, puis **DevTest Labs** dans la liste.

1. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.

1. Le panneau **Paramètres** du laboratoire sélectionné s’affiche.

1. Sur le panneau **Paramètres**, sélectionnez **Images Marketplace**

	![Configuration de l’utilisation d’images Azure Marketplace](./media/devtest-lab-configure-marketplace-images/lab-settings-marketplace-images.png)

1. Spécifiez si vous souhaitez que toutes les images Azure Marketplace qualifiées puissent être utilisées comme base d’une nouvelle machine virtuelle. Si vous sélectionnez **Oui**, toutes les images Azure Marketplace répondant à tous les critères suivants sont autorisées dans le laboratoire :

	- L’image crée une seule machine virtuelle, **et**
	- L’image utilise Azure Resource Manager pour configurer des machines virtuelles, **et**
	- L’image ne nécessite pas l’achat d’un plan de licences supplémentaire
	
	Si vous ne souhaitez autoriser aucune image, ou vous souhaitez spécifier les images pouvant être utilisées, sélectionnez **Non**.
 
	![Option permettant d’autoriser l’utilisation de toutes les images Marketplace en tant qu’images de base pour des machines virtuelles](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
 
1. Si vous sélectionnez **Non** à l’étape précédente, la case à cocher **Images autorisées/Sélectionner tout** est activée. Vous pouvez utiliser cette option avec la zone de recherche pour rapidement sélectionner ou désélectionner tous les éléments affichés dans la liste. Vous pouvez également sélectionner les images Azure Marketplace que vous souhaitez autoriser pour la création de machines virtuelles individuellement en activant la case à cocher correspondante de chaque image. N’effectuez aucune sélection dans la liste si vous ne souhaitez autoriser aucune image Azure Marketplace dans le laboratoire.

	![Vous pouvez spécifier les images Azure Marketplace pouvant être utilisées en tant qu’images de base pour des machines virtuelles](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

## Étapes suivantes

Une fois que vous avez configuré comment les images Azure Marketplace sont autorisées lors de la création d’une machine virtuelle, l’étape suivante consiste à [ajouter une machine virtuelle à votre laboratoire](./devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0608_2016-->