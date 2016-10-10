<properties
	pageTitle="Création d’un groupe à haute disponibilité pour les machines virtuelles | Microsoft Azure"
	description="Apprenez à créer un groupe à haute disponibilité pour vos machines virtuelles à l’aide du portail Azure ou de PowerShell en utilisant le modèle de déploiement Resource Manager."
	keywords="groupe à haute disponibilité"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="cynthn"/>


# Créer un groupe à haute disponibilité 

Lors de l’utilisation du portail, si vous souhaitez que votre machine virtuelle fasse partie d’un groupe à haute disponibilité, vous devez créer celui-ci avant.

Pour plus d’informations sur la création et l’utilisation des groupes à haute disponibilité, consultez la page [Gestion de la disponibilité des machines virtuelles](virtual-machines-windows-manage-availability.md).


## Utilisation du portail pour créer un groupe à haute disponibilité avant la création des machines virtuelles

1. Dans le menu Hub, cliquez sur **Parcourir** et sélectionnez **Groupes à haute disponibilité**.

2. Dans le **panneau Groupes à haute disponibilité**, cliquez sur **Ajouter**.

	![Capture d’écran illustrant le bouton Ajouter pour la création d’un groupe à haute disponibilité.](./media/virtual-machines-windows-create-availability-set/add-availability-set.png)

3. Dans le panneau **Créer un groupe à haute disponibilité**, renseignez les informations du groupe.

	![Capture d’écran illustrant les informations que vous devez entrer pour créer le groupe à haute disponibilité.](./media/virtual-machines-windows-create-availability-set/create-availability-set.png)

	- **Nom** : le nom doit compter entre 1 et 80 caractères composés de chiffres, de lettres, de points, de traits de soulignement et de tirets. Le premier caractère doit être une lettre ou un chiffre. Le dernier caractère doit être une lettre, un chiffre ou un trait de soulignement.
	- **Domaines d’erreur** : les domaines d’erreur définissent le groupe de machines virtuelles partageant une source d’alimentation et un commutateur réseau communs. Par défaut, les machines virtuelles sont réparties entre trois domaines d’erreur maximum et peuvent être permutées entre le 1er et le 3e.
	- **Domaines de mise à jour** : cinq domaines de mise à jour sont assignés par défaut et ils peuvent être définis entre 1 et 20. Les domaines de mise à jour indiquent les groupes de machines virtuelles et les équipements physiques sous-jacents pouvant être redémarrés en même temps. Par exemple, si nous spécifions cinq domaines de mise à jour, dans le cas où un seul groupe à haute disponibilité comprend plus de cinq machines virtuelles, la sixième machine est placée dans le même domaine de mise à jour que la première, la septième dans le même que la deuxième, etc. L’ordre du redémarrage peut ne pas être séquentiel, mais un seul domaine de mise à jour est redémarré à la fois.
	- **Abonnement** : sélectionnez l’abonnement que vous souhaitez utiliser, si vous en avez plusieurs.
	- **Groupe de ressources** : sélectionnez un groupe de ressources existant en cliquant sur la flèche et en sélectionnant un groupe de ressources dans la liste déroulante. Vous pouvez également créer un nouveau groupe de ressources en entrant un nom. Le nom peut contenir les caractères suivants : lettres, chiffres, points, tirets, traits de soulignement et parenthèses ouvrantes et fermantes. Le nom ne peut pas se terminer par un point. Toutes les machines virtuelles dans le groupe à haute disponibilité doivent être créées dans le même groupe de ressources que le groupe à haute disponibilité.
	- **Emplacement** : sélectionnez un emplacement dans la liste déroulante.

4. Après avoir entré les informations, cliquez sur **Créer**. Une fois que le groupe à haute disponibilité a été créé, il s’affiche dans la liste lorsque vous actualisez le portail.

## Utilisation du portail pour créer simultanément une machine virtuelle et un groupe à haute disponibilité

Si vous créez une machine virtuelle à l’aide du portail, vous pouvez également créer un groupe à haute disponibilité pour la machine virtuelle lorsque vous créez la première machine virtuelle dans le groupe.

![Capture d’écran illustrant le processus de création d’un groupe à haute disponibilité lorsque vous créez la machine virtuelle.](./media/virtual-machines-windows-create-availability-set/new-vm-avail-set.png)


## Ajout d’une machine virtuelle à un groupe à haute disponibilité existant

Pour chaque machine virtuelle supplémentaire que vous créez devant appartenir au groupe, assurez-vous de la créer dans le même **groupe de ressources**, puis sélectionnez le groupe à haute disponibilité existant, défini à l’étape 3.

![Capture d’écran illustrant la sélection d’un groupe à haute disponibilité existant à utiliser pour votre machine virtuelle.](./media/virtual-machines-windows-create-availability-set/add-vm-to-set.png)



## Utilisation de PowerShell pour créer un groupe à haute disponibilité

Cet exemple crée un groupe à haute disponibilité dans le groupe de ressources **RMResGroup** dans l’emplacement **États-Unis de l’Ouest**. Cette opération doit être effectuée avant la création de la première machine virtuelle qui appartiendra au groupe.

	New-AzureRmAvailabilitySet -ResourceGroupName "RMResGroup" -Name "AvailabilitySet03" -Location "West US"
	
Pour plus d’informations, consultez [New-AzureRmAvailabilitySet](https://msdn.microsoft.com/library/mt619453.aspx).


## Résolution des problèmes

- Lorsque vous créez une machine virtuelle, si le groupe à haute disponibilité que vous voulez utiliser n’est pas dans la liste déroulante du portail, elle a peut-être été créée dans un autre groupe de ressources. Si vous ne connaissez pas le groupe de ressources pour votre groupe à haute disponibilité, cliquez sur Parcourir > Groupes à haute disponibilité dans le menu Hub pour afficher la liste de vos groupes à haute disponibilité et les groupes de ressources auxquels ils appartiennent.


## Étapes suivantes

Ajout de stockage supplémentaire à votre machine virtuelle en ajoutant un [disque de données](virtual-machines-windows-attach-disk-portal.md) supplémentaire.

<!---HONumber=AcomDC_0928_2016-->