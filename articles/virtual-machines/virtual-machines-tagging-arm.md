<properties
   pageTitle="Balisage d’une machine virtuelle | Microsoft Azure"
   description="Découvrez comment baliser une machine virtuelle Azure créée à l’aide du modèle de déploiement du Gestionnaire des ressources."
   services="virtual-machines"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure-services"
   ms.date="11/10/2015"
   ms.author="dkshir;memccror"/>

# Comment baliser une machine virtuelle dans Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique


Cet article décrit différentes façons d’ajouter des balises à une machine virtuelle dans Azure à l’aide d’Azure Resource Manager. Les balises sont des paires clé/valeur définies par l’utilisateur, qui peuvent être placées directement sur une ressource ou sur un groupe de ressources. Azure prend actuellement en charge jusqu’à 15 balises par ressource et par groupe de ressources. Les balises peuvent être placées sur une ressource au moment de la création ou bien ajoutées à une ressource existante. Notez que les balises ne sont prises en charge que pour les ressources créées via Azure Resource Manager.

## Balisage d’une machine virtuelle via des modèles

Voyons d’abord le balisage via des modèles. [Ce modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm) place des balises sur les ressources suivantes : Calcul (Machine virtuelle), Stockage (Compte de stockage) et Réseau (Adresse IP publique, Réseau virtuel et Interface réseau).

Cliquez sur le bouton **Déployer sur Azure** à partir du [lien du modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm). Ceci permet d'accéder au [portail Azure](http://portal.azure.com/), où vous pouvez déployer ce modèle.

![Déploiement simple avec des balises](./media/virtual-machines-tagging-arm/deploy-to-azure-tags.png)

Ce modèle inclut les balises suivantes :*Service**Application* et *Créé par*. Vous pouvez ajouter/modifier ces balises directement dans le modèle si vous voulez d’autres noms de balises.

![Balises Azure dans un modèle](./media/virtual-machines-tagging-arm/azure-tags-in-a-template.png)

Comme vous pouvez le voir, les balises sont définies en tant que paires clé/valeur, séparées par un signe deux-points (:). Les balises doivent être définies dans ce format :

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

Enregistrez le fichier de modèle après l’avoir modifié avec les balises de votre choix.

Ensuite, dans la section **Modifier les paramètres**, vous pouvez entrer les valeurs de vos balises.

![Modifier des balises dans le portail Azure](./media/virtual-machines-tagging-arm/edit-tags-in-azure-portal.png)

Cliquez sur **Créer** pour déployer ce modèle avec vos valeurs pour les balises.


## Balisage via le portail

Après avoir créé vos ressources avec des balises, vous pouvez afficher, ajouter et supprimer des balises dans le portail.

Sélectionnez l’icône Balises pour afficher vos balises :

![Icône Balises dans le portail Azure](./media/virtual-machines-tagging-arm/azure-portal-tags-icon.png)

Ajoutez une nouvelle balise via le portail en définissant votre propre paire clé/valeur, puis enregistrez-la.

![Ajouter une nouvelle balise dans le portail Azure](./media/virtual-machines-tagging-arm/azure-portal-add-new-tag.png)

Votre nouvelle balise doit maintenant apparaître dans la liste des balises pour votre ressource.

![Nouvelle balise enregistrée dans le portail Azure](./media/virtual-machines-tagging-arm/azure-portal-saved-new-tag.png)


## Balisage avec PowerShell

Pour créer, ajouter et supprimer des balises via PowerShell, vous devez d’abord configurer votre [environnement PowerShell avec Azure Resource Manager][]. Une fois que vous avez terminé la configuration, vous pouvez placer des balises sur les ressources Calcul, Réseau et Stockage au moment de la création ou après la création de la ressource via PowerShell. Cet article se concentre sur l’affichage et la modification des balises placées sur des machines virtuelles.

Accédez d’abord à une machine virtuelle via l’applet de commande `Get-AzureVM`.

        PS C:\> Get-AzureVM -ResourceGroupName "MyResourceGroup" -Name "MyWindowsVM"

Si votre machine virtuelle contient déjà des balises, vous verrez toutes les balises sur votre ressource :

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Si vous voulez ajouter des balises via PowerShell, vous pouvez utiliser la commande `Set-AzureResource`. Notez que lors de la mise à jour des balises via PowerShell, les balises sont mises à jour comme un tout. Ainsi, si vous ajoutez une balise à une ressource qui a déjà des balises, vous devez inclure toutes les balises que vous voulez placer sur la ressource. Voici un exemple montrant comment ajouter des balises supplémentaires à une ressource via des applets de commande PowerShell.

Cette première applet de commande définit toutes les balises placées sur *MyWindowsVM* sur la variable *tags*, à l’aide de la fonction `Get-AzureResource` et `Tags`. Notez que le paramètre `ApiVersion` est facultatif ; s’il n’est pas spécifié, la dernière version de l'API du fournisseur de ressources est utilisée.

        PS C:\> $tags = (Get-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/virtualmachines" -ApiVersion 2015-05-01-preview).Tags

La deuxième commande affiche les balises pour la variable donnée.

        PS C:\> $tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment

La troisième commande ajoute une balise supplémentaire à la variable *tags*. Notez l'utilisation de **+=** pour ajouter la nouvelle paire clé/valeur à la liste *tags*.

        PS C:\> $tags +=@{Name="Location";Value="MyLocation"}

La quatrième commande définit toutes les balises définies dans la variable *tags* sur la ressource donnée. Dans ce cas, il s’agit de MyWindowsVM.

        PS C:\> Set-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview -Tag $tags

La cinquième commande affiche toutes les balises sur la ressource. Comme vous pouvez le voir, *Emplacement* est maintenant défini en tant que balise avec la valeur *MyLocation*.

        PS C:\> (Get-AzureResource -ResourceName "MyWindowsVM" -ResourceGroupName "MyResourceGroup" -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview).Tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment
        Value		MyLocation
        Name		Location

Pour en savoir plus sur le balisage dans PowerShell, consultez les [applets de commande des ressources Azure][].


## Balisage avec l’interface de ligne de commande Azure

Le balisage est également pris en charge pour les ressources qui sont déjà créés via l’interface de ligne de commande Azure. Pour commencer, configurez votre [environnement CLI Azure][]. Connectez-vous à votre abonnement via l’interface de ligne de commande Azure et passez en mode ARM. Vous pouvez afficher toutes les propriétés d’une machine virtuelle donnée, y compris les balises, à l’aide de cette commande :

        azure vm show -g MyResourceGroup -n MyVM

Contrairement à PowerShell, si vous ajoutez des balises à une ressource qui contient déjà des balises, il est inutile de spécifier toutes les balises (anciennes et nouvelles) avant d'utiliser la commande `azure vm set`. Au lieu de cela, cette commande vous permet d’ajouter une balise à votre ressource. Pour ajouter une nouvelle balise de machine virtuelle via l'interface de ligne de commande Azure, vous pouvez utiliser la commande `azure vm set` avec le paramètre de balise **-t** :

        azure vm set -g MyResourceGroup -n MyVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Pour supprimer toutes les balises, vous pouvez utiliser le paramètre **–T** dans la commande `azure vm set`.

        azure vm set – g MyResourceGroup –n MyVM -T


Maintenant que nous avons appliqué des balises à nos ressources via PowerShell, l’interface de ligne de commande et le portail, examinons les détails d’utilisation pour afficher les balises dans le portail de facturation.


## Affichage de vos balises dans les détails d’utilisation

Les balises placées sur des ressources Calcul, Réseau et Stockage via Azure Resource Manager apparaissent dans vos détails d'utilisation dans le [portail de facturation](https://account.windowsazure.com/).

Cliquez sur **Télécharger les détails d'utilisation** pour afficher les détails d'utilisation dans votre abonnement.

![Détails d’utilisation dans le portail Azure](./media/virtual-machines-tagging-arm/azure-portal-tags-usage-details.png)

Sélectionnez votre relevé de facturation et les détails d'utilisation **Version 2** :

![Détails d’utilisation dans la version préliminaire 2 du portail Azure](./media/virtual-machines-tagging-arm/azure-portal-version2-usage-details.png)

Dans les détails d'utilisation, vous pouvez voir toutes les balises dans la colonne **Balises** :

![Colonne Balises dans le portail Azure](./media/virtual-machines-tagging-arm/azure-portal-tags-column.png)

En analysant ces balises ainsi que leur utilisation, les organisations sont en mesure de mieux comprendre leurs données de consommation.


## Ressources supplémentaires

* [Présentation d’Azure Resource Manager][]
* [Utilisation de balises pour organiser vos ressources Azure][]
* [Comprendre votre facture Azure][]
* [Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure][]




[environnement PowerShell avec Azure Resource Manager]: ../powershell-azure-resource-manager.md
[applets de commande des ressources Azure]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[environnement CLI Azure]: ./xplat-cli-azure-resource-manager.md
[Présentation d’Azure Resource Manager]: ../resource-group-overview.md
[Utilisation de balises pour organiser vos ressources Azure]: ../resource-group-using-tags.md
[Comprendre votre facture Azure]: ../billing-understand-your-bill.md
[Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure]: ../billing-usage-rate-card-overview.md

<!----HONumber=AcomDC_1210_2015-->