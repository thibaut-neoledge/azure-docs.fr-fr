<properties
	pageTitle="Affichage des tendances de coût de laboratoire mensuelles estimées dans Azure DevTest Labs | Microsoft Azure"
	description="En savoir plus sur le graphique des tendances des coûts mensuels estimés Azure DevTest Labs."
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
	ms.date="09/06/2016"
	ms.author="tarcher"/>

# Affichage des tendances de coût de laboratoire mensuelles estimées dans Azure DevTest Labs

La fonctionnalité de gestion des coûts de DevTest Labs vous permet de suivre le coût de votre labo. Cet article explique comment utiliser le graphique **Tendance des coûts mensuels estimés** pour afficher le coût estimé à ce jour pour le mois civil en cours, ainsi que le coût projeté pour la fin du mois civil en cours. Dans cet article, vous allez apprendre à afficher le graphique de tendances de coût mensuelles estimées dans le portail Azure.

## Affichage du graphique Tendance des coûts mensuels estimés

Pour afficher le graphique Tendance des coûts mensuels estimés, procédez comme suit :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Sélectionnez **Plus de services**, puis **DevTest Labs** dans la liste.

1. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.

1. Dans le panneau du laboratoire, sélectionnez **Paramètres de coût**.

1. Dans le panneau **Paramètres de coût**, sélectionnez **Tendances de coût de laboratoire**.

1. La capture d'écran suivante montre un exemple de graphique de coûts.

    ![Graphique de coûts](./media/devtest-lab-configure-cost-management/graph.png)

La valeur **Coût estimé** est le coût estimé à ce jour pour le mois calendaire en cours. Le **Coût projeté** est le coût estimé pour le mois calendaire entier, calculé à l’aide du coût du laboratoire lors des cinq derniers jours.
 
Les montants des coûts sont arrondis à l’entier supérieur. Par exemple :

- 5,01 est arrondi à 6
- 5,50 est arrondi à 6
- 5,99 est arrondi à 6

Comme indiqué au-dessus du graphique, les coûts que vous voyez dans le graphique sont les coûts *estimés* avec les tarifs de l’offre [Paiement à l'utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/). Par ailleurs, les éléments suivants ne sont *pas* inclus dans le calcul des coûts :

- Les abonnements CSP et Dreamspark ne sont pas actuellement pris en charge. En effet, Azure DevTest Labs utilise les [API de facturation Azure](../billing-usage-rate-card-overview.md) pour calculer les coûts de laboratoire, et celles-ci ne prennent pas en charge les abonnements CSP et Dreamspark.
- Les tarifs de votre offre. Actuellement, nous ne sommes pas en mesure d'utiliser les tarifs de votre offre (affichés sous votre abonnement) que vous avez négociés avec Microsoft ou les partenaires Microsoft. Nous utilisons les tarifs du paiement à l'utilisation.
- Vos taxes
- Vos remises
- Votre devise de facturation. Actuellement, le coût du labo s'affiche uniquement dans la devise USD.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Billets de blog connexes

- [Two more things to keep your cost on track in DevTest Labs (Deux autres astuces pour maîtriser vos coûts dans DevTest Labs)](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
- [Why Cost Thresholds? (Pourquoi définir des seuils de coût ?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## Étapes suivantes

Voici quelques possibilités d’opérations pour la suite :

- [Définir des stratégies de laboratoire](./devtest-lab-set-lab-policy.md) : apprenez à définir les différentes stratégies utilisées pour gérer l’utilisation de votre laboratoire et de ses machines virtuelles.
- [Créer une image personnalisée](./devtest-lab-create-template.md) : lorsque vous créez une machine virtuelle, vous spécifiez une base, qui peut être soit une image personnalisée, soit une image Marketplace. Cet article explique comment créer une image personnalisée à partir d’un fichier VHD.
- [Configurer des images Marketplace](./devtest-lab-configure-marketplace-images.md) : DevTest Labs prend en charge la création de machines virtuelles basées sur des images Azure Marketplace. Cet article explique comment spécifier, le cas échéant, les images Azure Marketplace pouvant être utilisées lors de la création de machines virtuelles dans un laboratoire.
- [Créer une machine virtuelle dans un laboratoire](./devtest-lab-add-vm-with-artifacts.md) : montre comment créer une machine virtuelle à partir d’une image de base (personnalisée ou Marketplace) et comment utiliser des artefacts dans votre machine virtuelle.

<!---HONumber=AcomDC_0907_2016-->