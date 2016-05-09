<properties
	pageTitle="Configuration de la gestion des coûts | Microsoft Azure"
	description="Découvrez comment configurer les fonctionnalités de gestion des coûts de votre DevTest Lab."
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
	ms.date="04/18/2016"
	ms.author="tarcher"/>

# Configurer la gestion des coûts

## Vue d'ensemble

La fonctionnalité de gestion des coûts de DevTest Labs vous permet de suivre le coût de votre labo. Cet article explique comment utiliser le graphique **Tendance des coûts mensuels estimés** pour afficher le coût estimé à ce jour pour le mois civil en cours, ainsi que le coût projeté pour la fin du mois civil en cours.

## Activation du graphique Tendance des coûts mensuels estimés

Afin d'activer le graphique Tendance des coûts mensuels estimés, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Appuyez sur **Parcourir**, puis sur **DevTest Labs** dans la liste.

1. Dans la liste des laboratoires, appuyez sur le laboratoire souhaité.

1. Appuyez sur **Paramètres**.

	![Paramètres](./media/devtest-lab-configure-cost-management/lab-blade-settings.png)

1. Dans le panneau **Paramètres** du labo, sous **Stratégies de coûts**, cliquez sur **Seuils de coûts**.

	![Menu](./media/devtest-lab-configure-cost-management/menu.png)
 
1. Sur le panneau **Seuils de coûts**, cliquez sur **Activés** pour activer cette fonctionnalité et sur **Désactivés** pour la désactiver.

1. Appuyez sur **Enregistrer**.

Après activation de cette fonctionnalité, il peut s’écouler plusieurs heures avant que le graphique n’affiche les coûts estimés et projetés. En effet, un service s'exécute toutes les heures pour rassembler ces informations, mais quelques heures après la collecte des données actives. Par exemple, supposons que vous démarriez une machine virtuelle à 1 h 00. Le coût associé à cette machine virtuelle ne sera sans doute pas incorporé au graphique de coûts avant quelques heures.
 
La capture d'écran suivante montre un exemple de graphique de coûts.

![Graphique de coûts](./media/devtest-lab-configure-cost-management/graph.png)

La valeur **Coût estimé** correspond au coût estimé à ce jour pour le mois civil en cours tandis que le **Coût projeté** est le coût estimé de l'ensemble du mois civil en cours.

Comme indiqué au-dessus du graphique, les coûts que vous voyez dans le graphique sont les coûts *estimés* avec les tarifs de l’offre [Paiement à l'utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/). Par ailleurs, les éléments suivants ne sont *pas* inclus dans le calcul des coûts :

- Les tarifs de votre offre. Actuellement, nous ne sommes pas en mesure d'utiliser les tarifs de votre offre (affichés sous votre abonnement) que vous avez négociés avec Microsoft ou les partenaires Microsoft. Nous utilisons les tarifs du paiement à l'utilisation.
- Vos taxes
- Vos remises
- Votre devise de facturation. Actuellement, le coût du labo s'affiche uniquement dans la devise USD.

## Étapes suivantes

Voici quelques possibilités d’opérations pour la suite :

- [Définir des stratégies de labo](./devtest-lab-set-lab-policy.md) - Apprenez à définir les différentes stratégies utilisées pour gérer l’utilisation de votre labo et de ses machines virtuelles. 
- [Créer une image personnalisée](./devtest-lab-create-template.md) - Lorsque vous créez une machine virtuelle, vous spécifiez une base, qui peut être soit une image personnalisée, soit une image Marketplace. Cet article explique comment créer une image personnalisée à partir d’un fichier VHD.
- [Configurer des images Marketplace](./devtest-lab-configure-marketplace-images.md) - DevTest Labs prend en charge la création de nouvelles machines virtuelles basées sur des images Azure Marketplace. Cet article explique comment spécifier, le cas échéant, les images Azure Marketplace pouvant être utilisées lors de la création de nouvelles machines virtuelles dans un laboratoire.
- [Créer une machine virtuelle dans un labo](./devtest-lab-add-vm-with-artifacts.md) - Montre comment créer une machine virtuelle à partir d’une image de base (personnalisée ou Marketplace) et comment utiliser des artefacts dans votre machine virtuelle.

<!---HONumber=AcomDC_0427_2016-->