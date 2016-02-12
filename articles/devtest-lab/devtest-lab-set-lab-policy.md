<properties
	pageTitle="Définir des stratégies de laboratoire | Microsoft Azure"
	description="Apprenez à définir des stratégies de laboratoire telles que les tailles de machine virtuelle, le nombre maximal de machines virtuelles par utilisateur et l’arrêt automatique."
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
	ms.date="02/03/2016"
	ms.author="tarcher"/>

# Définir des stratégies de laboratoire

## Vue d'ensemble

DevTest Lab vous permet de spécifier des stratégies clés qui régissent l’utilisation de votre laboratoire et de ses machines virtuelles. Ces stratégies incluent les seuils de coûts, les tailles de machine virtuelle autorisées, le nombre maximal de machines virtuelles par utilisateur et un arrêt automatique pour les machines virtuelles de votre laboratoire.

## Accès aux stratégies d’un laboratoire

Pour afficher (et modifier) les stratégies d’un laboratoire, procédez comme suit :

1. Connectez-vous au [portail Azure en version préliminaire](https://portal.azure.com).

1. Appuyez sur **Parcourir**, puis sur **DevTest Labs** dans la liste.

1. Dans la liste des laboratoires, appuyez sur le laboratoire souhaité.

1. Appuyez sur **Paramètres**.

	![Paramètres](./media/devtest-lab-set-lab-policy/lab-blade-settings.png)

1. Dans le panneau **Paramètres** figure un ensemble de paramètres appelé **Stratégies**.

	![Paramètres](./media/devtest-lab-set-lab-policy/policies.png)

	Appuyez sur la stratégie souhaitée dans la liste ci-dessous pour en savoir plus sur sa définition :

	- Seuils de coûts - Cette stratégie n’est pas prise en charge actuellement.

	- [Tailles de machine virtuelle autorisées](#set-allowed-vm-sizes) - Sélectionnez la liste des tailles de machine virtuelle autorisées dans le laboratoire. Un utilisateur ne peut créer des machines virtuelles qu’à partir de cette liste.

	- [Nombre maximal de machines virtuelles](#set-maximum-vms) - Spécifiez le nombre maximal de machines virtuelles pouvant être créées pour un laboratoire, ainsi que le nombre maximal de machines virtuelles qu’un utilisateur peut créer.

	- [Arrêt automatique](#set-auto-shutdown) - Spécifiez l’heure à laquelle les machines virtuelles du laboratoire actif doivent s’arrêter.

## Définir les tailles de machine virtuelle autorisées

La stratégie pour définir les tailles de machine virtuelle autorisées vous permet de spécifier les tailles de machine virtuelle autorisées dans le laboratoire et contribue ainsi à réduire les pertes de laboratoire. Si cette stratégie est activée, seules les tailles de machine virtuelle de cette liste peuvent être utilisées pour créer des machines virtuelles.

1. Dans le panneau **Paramètres** du laboratoire, sous **Stratégies**, appuyez sur **Tailles de machine virtuelle autorisées**.

	![Paramètres](./media/devtest-lab-set-lab-policy/allowed-vm-sizes-policy.png)
 
1. Appuyez sur **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.

1. Si vous avez activé cette stratégie, appuyez sur une ou plusieurs tailles de machine virtuelle pouvant être créées dans votre laboratoire.

1. Appuyez sur **Enregistrer**.

## Définir le nombre maximal de machines virtuelles

La stratégie du nombre maximal de machines virtuelles vous permet de spécifier le nombre maximal de machines virtuelles pouvant être créées pour le laboratoire actif, ainsi que le nombre maximal de machines virtuelles qu’un utilisateur peut créer. Si un utilisateur tente de créer une machine virtuelle alors que le nombre limite par utilisateur ou par laboratoire est atteint, un message d’erreur indique que la machine virtuelle ne peut pas être créée.

1. Dans le panneau **Paramètres** du laboratoire, sous **Stratégies**, appuyez sur **Nombre maximal de machines virtuelles**.

	![Paramètres](./media/devtest-lab-set-lab-policy/max-vms-policies.png)

1. Dans la section **Stratégie par utilisateur** :
 
	1. Appuyez sur **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.
	
	1. Si vous avez activé cette stratégie, dans la zone de texte **Nombre maximal de machines virtuelles autorisées par utilisateur**, entrez une valeur numérique indiquant le nombre maximal de machines virtuelles qu’un utilisateur peut créer. Si vous entrez un nombre non valide, l’interface utilisateur affiche le nombre maximal autorisé pour ce champ.

1. Dans la section **Stratégie par laboratoire** :
 
	1. Appuyez sur **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.
	
	1. Si vous avez activé cette stratégie, dans la zone de texte **Nombre maximal de machines virtuelles autorisées dans ce laboratoire**, entrez une valeur numérique indiquant le nombre maximal de machines virtuelles pouvant être créées dans le laboratoire actif. Si vous entrez un nombre non valide, l’interface utilisateur affiche le nombre maximal autorisé pour ce champ.

1. Appuyez sur **Enregistrer**.

## Définir l’arrêt automatique

La stratégie d’arrêt automatique vous permet d’indiquer l’heure à laquelle les machines virtuelles du laboratoire doivent s’arrêter et contribue ainsi à réduire les pertes de laboratoire.

1. Dans le panneau **Paramètres** du laboratoire, sous **Stratégies**, appuyez sur **Arrêt automatique**.

	![Paramètres](./media/devtest-lab-set-lab-policy/auto-shutdown-policy.png)

1. Appuyez sur **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.

1. Si vous avez activé cette stratégie, spécifiez une heure d’arrêt pour toutes les machines virtuelles du laboratoire actif.

1. Appuyez sur **Enregistrer**.

<!---HONumber=AcomDC_0204_2016-->