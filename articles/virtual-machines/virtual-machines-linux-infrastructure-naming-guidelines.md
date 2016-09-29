<properties
	pageTitle="Instructions de dénomination d’infrastructure | Microsoft Azure"
	description="Découvrez-en plus sur les principales instructions de conception et d’implémentation pour la dénomination dans des services d’infrastructure Azure."
	documentationCenter=""
	services="virtual-machines-linux"
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2016"
	ms.author="iainfou"/>

# Instructions de dénomination d’infrastructure

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Cet article se concentre sur la compréhension de l’approche des conventions de dénomination de vos diverses ressources Azure afin de créer un ensemble de ressources logique et facilement identifiable au sein de votre environnement.

## Instructions d’implémentation pour les conventions d’affectation de noms

Décisions :

- Quelles sont vos conventions d’affectation de noms pour les ressources Azure ?

Tâches :

- Définissez les affixes à utiliser parmi vos ressources pour assurer la cohérence.
- Définissez des noms de compte de stockage devant être globalement uniques.
- Documentez la convention de dénomination à utiliser et à distribuer à toutes les parties impliquées pour assurer la conformité à travers les déploiements.

## Conventions d’affectation de noms

Vous devez avoir une convention d’affectation de noms adaptée avant tout processus de création dans Azure. Une convention d’affectation de noms garantit que toutes les ressources ont un nom prévisible, afin de réduire la charge administrative associée à leur gestion.

Vous pouvez choisir de suivre un ensemble spécifique de conventions d’affectation de noms définies pour votre organisation, ou pour un compte ou abonnement Azure spécifique. Bien qu’il soit facile d’établir des règles implicites au sein d’organisations lorsque vous travaillez avec des ressources Azure, vous devez être en mesure de les mettre à l’échelle pour des équipes collaborant dans Azure.

Convenez d’un ensemble de conventions d’affectation de noms en amont. Certains facteurs sont à prendre en compte pour l’ensemble des règles de dénomination.

## Affixes

Lorsque vous cherchez à définir une convention de dénomination, une décision est à prendre quant au positionnement de l’affixe :

- au début du nom (préfixe)
- à la fin du nom (suffixe)

Voici deux exemples de noms possibles pour un groupe de ressources avec l’affixe `rg` :

- Rg-WebApp (préfixe)
- WebApp-Rg (suffixe)

Les affixes peuvent faire référence à différents aspects des ressources spécifiques. Le tableau suivant présente des exemples généralement utilisés.

| Aspect | Exemples | Remarques |
|:-------------------------------------|:-----------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------|
| Environnement | dev, stg, prod | En fonction de l’objectif et du nom de chaque environnement. |
| Lieu | usw (West US), use (East US 2) | En fonction de la région du centre de données et de l’organisation. |
| Composant, service ou produit Azure | Rg pour groupe de ressources, VNet pour réseau virtuel | En fonction du produit auquel la ressource est associée. |
| Rôle | base de données, application, web | En fonction du rôle de la machine virtuelle. |
| Instance | 01, 02, 03, etc. | Pour les ressources possédant plusieurs instances. Par exemple, des serveurs Web à charge équilibrée dans un service cloud. |


Lors de l’établissement de conventions d’affectation de noms, assurez-vous qu’elles indiquent clairement les affixes à utiliser pour chaque type de ressource et à quelle position (suffixe ou préfixe).

## Dates

Dans de nombreux cas, il est important de déterminer la date de création à partir du nom d’une ressource. Nous recommandons le format de date AAAAMMJJ. Ce format permet non seulement d’enregistrer la date complète, mais également de trier par ordre alphabétique et par ordre chronologique deux ressources dont les noms diffèrent uniquement au niveau de la date.

## Ressources d’affectation de noms

Définissez chaque type de ressource dans la convention d’affectation de noms, qui doit comprendre des règles définissant l’attribution de nom pour chaque ressource créée. Ces règles doivent s’appliquer à tous les types de ressources, par exemple :

- Abonnements
- Comptes
- Comptes de stockage
- Réseaux virtuels
- Sous-réseaux
- Groupes à haute disponibilité
- Groupes de ressources
- Machines virtuelles
- Points de terminaison
- groupes de sécurité réseau ;
- contrôleur

Les noms doivent être descriptifs, afin de fournir suffisamment d’informations pour déterminer la ressource à laquelle ils font référence.

## Noms des ordinateurs

Lorsque vous créez une machine virtuelle, Azure requiert un nom de machine virtuelle contenant jusqu’à 64 caractères utilisé pour le nom de la ressource. Azure utilise le même nom pour le système d’exploitation installé sur la machine virtuelle. Toutefois, ces noms peuvent ne pas toujours être identiques.

Si une machine virtuelle est créée à partir d’un fichier d’image .vhd qui contient déjà un système d’exploitation, le nom de la machine virtuelle dans Azure peut différer du nom d’ordinateur du système d’exploitation de la machine virtuelle. Dans ce cas, la gestion de la machine virtuelle devient plus difficile. C’est pourquoi nous le déconseillons. Affectez à la ressource de la machine virtuelle Azure le nom d’ordinateur attribué au système d’exploitation de cette machine virtuelle.

Nous recommandons que le nom de la machine virtuelle Azure soit le même que le nom d’ordinateur du système d’exploitation sous-jacent.

## Noms des comptes de stockage

Le nom des comptes de stockage sont régis par des règles spécifiques. Vous ne pouvez utiliser que des lettres minuscules et des chiffres. Pour plus d’informations, voir [Création d’un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account). En outre, le nom du compte de stockage, avec core.windows.net, doit être un nom DNS unique et globalement valide. Par exemple, si le compte de stockage est appelé mystorageaccount, les noms DNS suivants qui en résultent doivent être uniques :

- mystorageaccount.blob.core.windows.net
- mystorageaccount.table.core.windows.net
- mystorageaccount.queue.core.windows.net


## Étapes suivantes
[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0914_2016-->