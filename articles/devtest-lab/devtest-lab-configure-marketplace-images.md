<properties
	pageTitle="Configuration des paramètres d’image Azure Marketplace dans un DevTest Lab | Microsoft Azure"
	description="Configuration des images Azure Marketplace peuvent être utilisées lors de la création d’une machine virtuelle dans un DevTest Lab"
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
	ms.date="03/15/2016"
	ms.author="tarcher"/>

# Configuration des paramètres d’image Azure Marketplace dans un DevTest Lab

## Vue d'ensemble

DevTest Labs prend en charge la création de nouvelles machines virtuelles basées sur des images Azure Marketplace en fonction de la manière dont vous avez configuré des images Azure Marketplace à utiliser dans votre laboratoire. Cet article explique comment spécifier, le cas échéant, les images Azure Marketplace pouvant être utilisées lors de la création de nouvelles machines virtuelles dans un DevTest Lab.

## Sélection des images Azure Marketplace autorisées lors de la création d’une machine virtuelle

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Appuyez sur **Parcourir**, puis sur **DevTest Labs** dans la liste.

1. Dans la liste des labos, sélectionnez le labo souhaité.

1. Le panneau **Paramètres** du laboratoire sélectionné s’affiche.

1. Sur le panneau **Paramètres**, appuyez sur **Images Marketplace**

	![Configuration de l’utilisation d’images Azure Marketplace](./media/devtest-lab-configure-marketplace-images/lab-settings-marketplace-images.png)

1. Spécifiez si vous souhaitez que toutes les images Azure Marketplace qualifiées puissent être utilisées comme base d’une nouvelle machine virtuelle. Si vous sélectionnez **Oui**, toutes les images Azure Marketplace répondant à tous les critères suivants sont autorisées dans le laboratoire :

	- L’image crée une seule machine virtuelle, **et**
	- L’image utilise Azure Resource Manager pour configurer des machines virtuelles, **et**
	- L’image ne nécessite pas l’achat d’un plan de licences supplémentaire
	
	Si vous ne souhaitez autoriser aucune image, ou vous souhaitez spécifier les images pouvant être utilisées, sélectionnez **Non**.
 
	![Option permettant d’autoriser l’utilisation de toutes les images Marketplace en tant qu’images de base pour des machines virtuelles](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
 
1. Si vous sélectionnez **Non** à l’étape précédente, la case à cocher **Images autorisées/Sélectionner tout** est activée. Vous pouvez utiliser cette option avec la zone de recherche pour rapidement sélectionner ou désélectionner tous les éléments affichés dans la liste. Vous pouvez également sélectionner les images Azure Marketplace que vous souhaitez autoriser pour la création de machines virtuelles individuellement en activant la case à cocher correspondante de chaque image. N’effectuez aucune sélection dans la liste si vous ne souhaitez autoriser aucune image Azure Marketplace dans le laboratoire.

	![Vous pouvez spécifier les images Azure Marketplace pouvant être utilisées en tant qu’images de base pour des machines virtuelles](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

## Étapes suivantes

Une fois que vous avez configuré l’autorisation des images Azure Marketplace lors de la création d’une machine virtuelle, l’étape suivante consiste à [ajouter une machine virtuelle dans votre DevTest Lab](./devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0316_2016-->