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
	ms.date="05/08/2016"
	ms.author="tarcher"/>

# Définir des stratégies de laboratoire

## Vue d'ensemble

DevTest Labs vous permet de spécifier des stratégies clés qui régissent l’utilisation de votre laboratoire et de ses machines virtuelles. Par exemple, vous pouvez définir les règles des tailles autorisées des machines virtuelles créées ainsi que le seuil du nombre de machines virtuelles pouvant être créées et planifier les tâches pour démarrer/arrêter automatiquement les machines virtuelles de laboratoire.

## Accès aux stratégies d’un laboratoire

Pour afficher (et modifier) les stratégies d’un laboratoire, procédez comme suit :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Appuyez sur **Parcourir**, puis sur **DevTest Labs** dans la liste.

1. Dans la liste des laboratoires, appuyez sur le laboratoire souhaité.

1. Appuyez sur **Paramètres**.

	![Paramètres](./media/devtest-lab-set-lab-policy/lab-blade-settings.png)

1. Dans le panneau **Paramètres** figure un ensemble de paramètres appelé **Stratégies des machines virtuelles**.

	![Paramètres](./media/devtest-lab-set-lab-policy/policies.png)

	Appuyez sur la stratégie souhaitée dans la liste ci-dessous pour en savoir plus sur sa définition :

	- [Tailles de machine virtuelle autorisées](#set-allowed-vm-sizes) - Sélectionnez la liste des tailles de machine virtuelle autorisées dans le laboratoire. Un utilisateur ne peut créer des machines virtuelles qu’à partir de cette liste.

	- [Nombre maximal de machines virtuelles par utilisateur.](#set-maximum-vms-per-user) - Spécifiez le nombre maximal de machines virtuelles qui peuvent être créées par un utilisateur.

	- [Nombre total de machines virtuelles autorisées](#set-total-vms-allowed) - Spécifiez le nombre maximal de machines virtuelles qui peuvent être créées pour un laboratoire.

	- [Arrêt automatique](#set-auto-shutdown) - Spécifiez l’heure à laquelle les machines virtuelles du laboratoire actif doivent s’arrêter automatiquement.

	- [Démarrage automatique](#set-auto-start) - Spécifiez l’heure à laquelle les machines virtuelles du laboratoire actif doivent démarrer automatiquement.

## Définir les tailles de machine virtuelle autorisées

La stratégie pour définir les tailles de machine virtuelle autorisées vous permet de spécifier les tailles de machine virtuelle autorisées dans le laboratoire et contribue ainsi à réduire les pertes de laboratoire. Si cette stratégie est activée, seules les tailles de machine virtuelle de cette liste peuvent être utilisées pour créer des machines virtuelles.

1. Dans le panneau **Paramètres** du laboratoire, sous **Stratégies des machines virtuelles**, cliquez sur **Tailles de machine virtuelle autorisées**.

	![Paramètres](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)
 
1. Appuyez sur **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.

1. Si vous activez cette stratégie, appuyez sur une ou plusieurs tailles de machine virtuelle pouvant être créées dans votre laboratoire.

1. Appuyez sur **Enregistrer**.

## Définir le nombre maximal de machines virtuelles par utilisateur

La stratégie du **Nombre maximal de machines virtuelles par utilisateur** vous permet de spécifier le nombre maximal de machines virtuelles pouvant être créées par un utilisateur individuel. Si un utilisateur tente de créer une machine virtuelle alors que le nombre limite par utilisateur est atteint, un message d’erreur indique que la machine virtuelle ne peut pas être créée.

1. Dans le panneau **Paramètres** du laboratoire, sous **Stratégies des machines virtuelles**, appuyez sur **Nombre maximal de machines virtuelles par utilisateur**.

	![Paramètres](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Appuyez sur **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.

1. Si vous activez cette stratégie, dans la zone de texte **Nombre maximal de machines virtuelles autorisées par utilisateur**, entrez une valeur numérique indiquant le nombre maximal de machines virtuelles qu’un utilisateur peut créer. Si vous entrez un nombre non valide, l’interface utilisateur affiche le nombre maximal autorisé pour ce champ.

1. Appuyez sur **Enregistrer**.

## Définir le nombre total de machines virtuelles autorisées

La stratégie du **Nombre total de machines virtuelles autorisées** vous permet de spécifier le nombre maximal de machines virtuelles pouvant être créées pour le laboratoire actuel. Si un utilisateur tente de créer une machine virtuelle alors que le nombre limite par laboratoire est atteint, un message d’erreur indique que la machine virtuelle ne peut pas être créée.

1. Dans le panneau **Paramètres** du laboratoire, sous **Stratégies des machines virtuelles**, cliquez sur **Nombre total de machines virtuelles autorisées**.

	![Paramètres](./media/devtest-lab-set-lab-policy/total-vms-allowed.png)

1. Appuyez sur **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.

1. Si vous activez cette stratégie, dans la zone de texte **Nombre total de machines virtuelles autorisées dans ce laboratoire**, entrez une valeur numérique indiquant le nombre maximal de machines virtuelles pouvant être créées pour le laboratoire actuel. Si vous entrez un nombre non valide, l’interface utilisateur affiche le nombre maximal autorisé pour ce champ.

1. Appuyez sur **Enregistrer**.

## Définir l’arrêt automatique

La stratégie d’arrêt automatique vous permet d’indiquer l’heure à laquelle les machines virtuelles du laboratoire doivent s’arrêter et contribue ainsi à réduire les pertes de laboratoire.

1. Dans le panneau **Paramètres** du laboratoire, sous **Stratégies des machines virtuelles**, cliquez sur **Arrêt automatique**.

	![Paramètres](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Appuyez sur **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.

1. Si vous activez cette stratégie, spécifiez une heure locale d’arrêt pour toutes les machines virtuelles du laboratoire actif.

1. Appuyez sur **Enregistrer**.

1. Par défaut, une fois activée, cette stratégie s’applique à toutes les machines virtuelles dans le laboratoire en cours. Pour supprimer ce paramètre sur une machine virtuelle spécifique, ouvrez le panneau de la machine virtuelle et modifiez son paramètre **Arrêt automatique**.

## Définir le démarrage automatique

La stratégie de démarrage automatique vous permet de spécifier quand les machines virtuelles du laboratoire doivent être démarrées.

1. Dans le panneau **Paramètres** du laboratoire, sous **Stratégies des machines virtuelles**, cliquez sur **Démarrage automatique**.

	![Paramètres](./media/devtest-lab-set-lab-policy/auto-start.png)

1. Appuyez sur **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.

1. Si vous activez cette stratégie, spécifiez l’heure locale de démarrage programmé et les jours de la semaine auxquels cette heure s’applique.

1. Appuyez sur **Enregistrer**.

1. Une fois activée, cette stratégie n’est pas automatiquement appliquée à toutes les machines virtuelles dans le laboratoire en cours. Pour appliquer ce paramètre à une machine virtuelle spécifique, ouvrez le panneau de la machine virtuelle et modifiez son paramètre **Démarrage automatique**.

## Étapes suivantes

Une fois que vous avez défini et appliqué les différents paramètres de stratégies des machines virtuelles pour votre laboratoire, voici ce que vous pouvez essayer de faire :

- [Configurer la gestion des coûts](./devtest-lab-configure-cost-management.md) : montre comment utiliser le graphique **Tendance des coûts mensuels estimés** pour afficher le coût estimé à ce jour pour le mois en cours, ainsi que le coût calculé par projection pour la fin du mois en cours.
- [Créer une image personnalisée](./devtest-lab-create-template.md) - Lorsque vous créez une machine virtuelle, vous spécifiez une base, qui peut être soit une image personnalisée, soit une image Marketplace. Cet article explique comment créer une image personnalisée à partir d’un fichier VHD.
- [Configurer des images Marketplace](./devtest-lab-configure-marketplace-images.md) - DevTest Labs prend en charge la création de nouvelles machines virtuelles basées sur des images Azure Marketplace. Cet article explique comment spécifier, le cas échéant, les images Azure Marketplace pouvant être utilisées lors de la création de nouvelles machines virtuelles dans un laboratoire.
- [Créer une machine virtuelle dans un laboratoire](./devtest-lab-add-vm-with-artifacts.md) : montre comment créer une machine virtuelle à partir d’une image de base (personnalisée ou Marketplace) et comment utiliser des artefacts dans votre machine virtuelle.

<!---HONumber=AcomDC_0525_2016-->