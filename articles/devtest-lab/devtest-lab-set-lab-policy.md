---
title: Définir des stratégies de laboratoire dans Azure DevTest Labs | Microsoft Docs
description: Apprenez à définir des stratégies de laboratoire telles que les tailles de machine virtuelle, le nombre maximal de machines virtuelles par utilisateur et l’arrêt automatique.
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
ms.date: 09/12/2016
ms.author: tarcher

---
# Définir des stratégies de laboratoire dans Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/How-to-set-VM-policies-in-a-DevTest-Lab/player]
> 
> 

Azure DevTest Labs vous permet de spécifier des stratégies essentielles qui vous aident à contrôler les coûts et à réduire le gaspillage dans vos laboratoires. Ces stratégies de laboratoire incluent le nombre maximal de machines virtuelles créées par utilisateur et par laboratoire, ainsi que différentes options de démarrage automatique et d’arrêt automatique.

## Accès aux stratégies d’un laboratoire dans Azure DevTest Labs
Les étapes suivantes vous guident lors de la configuration des stratégies pour un laboratoire dans Azure DevTest Labs :

Pour afficher (et modifier) les stratégies d’un laboratoire, procédez comme suit :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Plus de services**, puis **DevTest Labs** dans la liste.
3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.
4. Sélectionnez **Paramètres de stratégie**.
5. Le panneau **Paramètres de stratégie** contient un menu de paramètres que vous pouvez spécifier :
   
    ![Panneau Paramètres de stratégie](./media/devtest-lab-set-lab-policy/policies.png)
   
    Pour plus d’informations sur la définition d’une stratégie, sélectionnez-la dans la liste suivante :
   
   * [Tailles de machine virtuelle autorisées](#set-allowed-virtual-machine-sizes) : sélectionnez la liste des tailles de machine virtuelle autorisées dans le laboratoire. Un utilisateur ne peut créer des machines virtuelles qu’à partir de cette liste.
   * [Machines virtuelles par utilisateur](#set-virtual-machines-per-user) : spécifiez le nombre maximal de machines virtuelles qui peuvent être créées par un utilisateur.
   * [Machines virtuelles par laboratoire](#set-virtual-machines-per-lab) : spécifiez le nombre maximal de machines virtuelles qui peuvent être créées pour un laboratoire.
   * [Arrêt automatique](#set-auto-shutdown) : spécifiez l’heure à laquelle les machines virtuelles du laboratoire actif doivent s’arrêter automatiquement.
   * [Démarrage automatique](#set-auto-start) : spécifiez l’heure à laquelle les machines virtuelles du laboratoire actif doivent démarrer automatiquement.

## Définir les tailles de machine virtuelle autorisées
La stratégie pour définir les tailles de machine virtuelle autorisées vous permet de spécifier les tailles de machine virtuelle autorisées dans le laboratoire et contribue ainsi à réduire les pertes de laboratoire. Si cette stratégie est activée, seules les tailles de machine virtuelle de cette liste peuvent être utilisées pour créer des machines virtuelles.

1. Dans le panneau **Paramètres de stratégie** du laboratoire, sélectionnez **Tailles de machine virtuelle autorisées**.
   
    ![Tailles de machine virtuelle autorisées](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)
2. Sélectionnez **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.
3. Si vous activez cette stratégie, sélectionnez une ou plusieurs tailles de machine virtuelle pouvant être créées dans votre laboratoire.
4. Sélectionnez **Enregistrer**.

## Définir les machines virtuelles par utilisateur
La stratégie **Machines virtuelles par utilisateur** vous permet de spécifier le nombre maximal de machines virtuelles pouvant être créées par un utilisateur individuel. Si un utilisateur tente de créer une machine virtuelle alors que le nombre limite par utilisateur est atteint, un message d’erreur indique que la machine virtuelle ne peut pas être créée.

1. Dans le panneau **Paramètres de stratégie** du laboratoire, sélectionnez **Machines virtuelles par utilisateur**.
   
    ![Machines virtuelles par utilisateur](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)
2. Sélectionnez **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.
3. Si vous activez cette stratégie, entrez une valeur numérique indiquant le nombre maximal de machines virtuelles qu’un utilisateur peut créer. Si vous entrez un nombre non valide, l’interface utilisateur affiche le nombre maximal autorisé pour ce champ.
4. Sélectionnez **Enregistrer**.

## Définir les machines virtuelles par laboratoire
La stratégie **Machines virtuelles par laboratoire** vous permet de spécifier le nombre maximal de machines virtuelles pouvant être créées pour le laboratoire actuel. Si un utilisateur tente de créer une machine virtuelle alors que le nombre limite par laboratoire est atteint, un message d’erreur indique que la machine virtuelle ne peut pas être créée.

1. Dans le panneau **Paramètres de stratégie** du laboratoire, sélectionnez **Machines virtuelles par laboratoire**.
   
    ![Machines virtuelles par laboratoire](./media/devtest-lab-set-lab-policy/total-vms-allowed.png)
2. Sélectionnez **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.
3. Si vous activez cette stratégie, entrez une valeur numérique indiquant le nombre maximal de machines virtuelles pouvant être créées pour le laboratoire actuel. Si vous entrez un nombre non valide, l’interface utilisateur affiche le nombre maximal autorisé pour ce champ.
4. Sélectionnez **Enregistrer**.

## Définir l’arrêt automatique
La stratégie d’arrêt automatique vous permet d’indiquer l’heure à laquelle les machines virtuelles du laboratoire doivent s’arrêter et contribue ainsi à réduire les pertes de laboratoire.

1. Dans le panneau **Paramètres de stratégie** du laboratoire, sélectionnez **Arrêt automatique**.
   
    ![Arrêt automatique](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
2. Sélectionnez **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.
3. Si vous activez cette stratégie, spécifiez une heure locale d’arrêt pour toutes les machines virtuelles du laboratoire actif.
4. Sélectionnez **Enregistrer**.
5. Par défaut, une fois activée, cette stratégie s’applique à toutes les machines virtuelles dans le laboratoire en cours. Pour supprimer ce paramètre sur une machine virtuelle spécifique, ouvrez le panneau de la machine virtuelle et modifiez son paramètre **Arrêt automatique**.

## Définir le démarrage automatique
La stratégie de démarrage automatique vous permet de spécifier quand les machines virtuelles du laboratoire doivent être démarrées.

1. Dans le panneau **Paramètres de stratégie** du laboratoire, sélectionnez **Démarrage automatique**.
   
    ![Démarrage automatique](./media/devtest-lab-set-lab-policy/auto-start.png)
2. Sélectionnez **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.
3. Si vous activez cette stratégie, spécifiez l’heure locale de démarrage programmé et les jours de la semaine auxquels cette heure s’applique.
4. Sélectionnez **Enregistrer**.
5. Une fois activée, cette stratégie n’est pas automatiquement appliquée à toutes les machines virtuelles dans le laboratoire en cours. Pour appliquer ce paramètre à une machine virtuelle spécifique, ouvrez le panneau de la machine virtuelle et modifiez son paramètre **Démarrage automatique**.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Étapes suivantes
Une fois que vous avez défini et appliqué les différents paramètres de stratégies des machines virtuelles pour votre laboratoire, voici ce que vous pouvez essayer de faire :

* [Configurer la gestion des coûts](devtest-lab-configure-cost-management.md) : montre comment utiliser le graphique **Tendance des coûts mensuels estimés** pour afficher le coût estimé à ce jour pour le mois en cours, ainsi que le coût calculé par projection pour la fin du mois en cours.
* [Créer une image personnalisée](devtest-lab-create-template.md) : lorsque vous créez une machine virtuelle, vous spécifiez une base, qui peut être soit une image personnalisée, soit une image Marketplace. Cet article explique comment créer une image personnalisée à partir d’un fichier VHD.
* [Configurer des images Marketplace](devtest-lab-configure-marketplace-images.md) : Azure DevTest Labs prend en charge la création de machines virtuelles basées sur des images Azure Marketplace. Cet article explique comment spécifier, le cas échéant, les images Azure Marketplace pouvant être utilisées lors de la création de machines virtuelles dans un laboratoire.
* [Créer une machine virtuelle dans un laboratoire](devtest-lab-add-vm-with-artifacts.md) : montre comment créer une machine virtuelle à partir d’une image de base (personnalisée ou Marketplace) et comment utiliser des artefacts dans votre machine virtuelle.

<!---HONumber=AcomDC_0914_2016-->