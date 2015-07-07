<properties 
   pageTitle="Comment migrer des groupes d’affinités vers un réseau virtuel régional"
   description="En savoir plus sur la migration des groupes d’affinités vers les réseaux virtuels régionaux"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/29/2015"
   ms.author="telmos" />

# Comment migrer des groupes d’affinités vers un réseau virtuel régional

Vous pouvez utiliser un groupe d’affinités pour vous assurer que les ressources créées dans le même groupe d’affinités sont physiquement hébergées par des serveurs proches les uns des autres, ce qui leur permet de communiquer plus rapidement. Auparavant, les groupes d’affinités étaient requis pour la création des réseaux virtuels. À cette époque, le service de gestionnaire réseau qui gérait les réseaux virtuels ne fonctionnait qu’au sein d’un ensemble de serveurs physiques ou d’une unité d’échelle. Les récentes améliorations architecturales ont augmenté de l’étendue de gestion réseau à une région.

Suite à ces améliorations architecturales, les groupes d’affinités ne sont plus recommandés ou requis pour les réseaux virtuels. L’utilisation des groupes d’affinités pour les réseaux virtuels est remplacée par les régions. Les réseaux virtuels associés à des régions sont appelés réseaux virtuels régionaux.

En outre, nous vous recommandons de ne pas utiliser les groupes d’affinités en général. Outre la spécification du réseau virtuel, les groupes d’affinités étaient également importants pour garantir que des ressources, telles que le calcul et le stockage, étaient placées proches les unes des autres. Toutefois, avec l’architecture actuelle du réseau Azure, ces exigences de placement ne sont plus nécessaires. Consultez [Groupes d’affinités et machines virtuelles](#Affinity-groups-and-VMs) pour les quelques rares cas restants pour lesquels vous pouvez avoir besoin d’utiliser un groupe d’affinités.

## Création de réseaux virtuels régionaux et migration

À partir de maintenant, quand vous créez des réseaux virtuels, utilisez une *Région*. L’option s’affiche dans le portail de gestion. Notez que dans le fichier de configuration réseau, cette option s’appelle *Emplacement*.

>[AZURE.IMPORTANT]Bien qu’il soit techniquement possible de créer un réseau virtuel associé à un groupe d’affinités, il n’existe aucune raison valable de le faire. De nombreuses nouvelles fonctionnalités, telles que les groupes de sécurité réseau, sont disponibles uniquement quand vous utilisez un réseau virtuel régional et ne sont pas disponibles pour les réseaux virtuels associés à des groupes d’affinités.

### À propos des réseaux virtuels actuellement associés à des groupes d’affinités

Les réseaux virtuels qui sont actuellement associés à des groupes d’affinités pourront être migrés vers des réseaux virtuels régionaux très prochainement. Nous mettrons à jour cette page pour vous tenir informé du processus en cours et de la date à laquelle vous pourrez commencer les étapes de migration.

Notez que les groupes d’affinités existeront toujours, même si nous ne recommandons plus leur utilisation pour les réseaux virtuels. Pour cette raison, vous pouvez choisir de ne pas migrer votre réseau virtuel ou de retarder la migration. Votre réseau virtuel fonctionnera toujours. Toutefois, vous pourrez rencontrer des limitations et vous ne pourrez pas tirer parti des nombreuses nouvelles fonctionnalités liées aux réseaux virtuels régionaux. Nous vous encourageons vivement à migrer votre réseau virtuel du groupe d’affinités vers un réseau virtuel régional dès que votre réseau virtuel sera activé.

### Migration anticipée

La migration anticipée est disponible pour les clients disposant d’un contrat de support. Pour choisir la migration anticipée, procédez comme suit :

1. Dans le **portail de gestion**, dans votre page de réseau virtuel, en haut à droite, cliquez sur **Contacter le support Microsoft**.

	![Demande de migration vers un réseau virtuel régional](./media/virtual-networks-migrate-to-regional-vnet/IC790447.png)

1. Dans la page **Contacter le support Microsoft**, sélectionnez les éléments suivants : Abonnement : si vous avez plusieurs abonnements, sélectionnez l’abonnement qui correspond au réseau virtuel que vous voulez migrer. Type de support : technique Type de produit : réseaux virtuels Type de problème : migration d’un réseau virtuel vers un réseau virtuel régional

	- **Abonnement :** si vous avez plusieurs abonnements, sélectionnez l’abonnement qui correspond au réseau virtuel que vous voulez migrer.

	- **Type de support :** *technique*

	- **Type de produit :** *réseaux virtuels*

	- **Type de problème :** *migration d’un réseau virtuel vers un réseau virtuel régional*

1. Prérequis : votre réseau virtuel doit être déjà activé pour la migration pour effectuer les étapes suivantes. Vous saurez que votre réseau virtuel a été activé quand vous recevrez une confirmation (dans le cas d’une demande de migration anticipée), ou que cette page a été mise à jour pour vous informer que tous les réseaux virtuels sont activés pour la migration.

1. Exportez le fichier de configuration réseau. Vous pouvez utiliser PowerShell ou le portail de gestion. Pour obtenir des instructions sur l’utilisation du portail de gestion, consultez [Configuration de votre réseau virtuel à l’aide d’un fichier de configuration réseau](../virtual-networks-using-network-configuration-file/).

1. Modifiez votre fichier de configuration réseau, en remplaçant les anciennes valeurs par les nouvelles.

	> [AZURE.NOTE]**Emplacement** correspond à la région que vous avez spécifiée pour le groupe d’affinités associé à votre réseau virtuel. Par exemple, si votre réseau virtuel est associé à un groupe d’affinités qui se trouve dans l’Ouest des États-Unis, votre emplacement doit pointer sur Ouest des États-Unis pendant la migration.
	
	Modifiez les lignes suivantes dans votre fichier de configuration réseau, en remplaçant les valeurs avec les vôtres :

	**Ancienne valeur :** <VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG">

	**Nouvelle valeur :** <VirtualNetworkSitename="VNetUSWest" Location="West US">

1. Enregistrez vos modifications et [importez](../virtual-networks-using-network-configuration-file/) la configuration réseau dans Azure.

## Groupe d’affinités et machines virtuelles

Comme mentionné précédemment, les groupes d’affinités ne sont plus recommandés pour les machines virtuelles. Vous devez utiliser un groupe d’affinités uniquement si un ensemble de machines virtuelles doit avoir la latence réseau la plus basse possible entre les machines virtuelles. En plaçant les machines virtuelles dans un groupe d’affinités, celles-ci sont placées dans le même cluster de calcul ou unité d’échelle.

Il est important de noter que l’utilisation d’un groupe d’affinités peut avoir deux conséquences potentiellement négatives :

- La taille de l’ensemble des machines virtuelles sera limitée à celle proposée par l’unité d’échelle de calcul.

- Il est fort probable que vous ne puissiez pas allouer de nouvelle machine virtuelle. C’est le cas quand l’unité d’échelle spécifique du groupe d’affinités se trouve hors capacité.

### Marche à suivre en cas de machine virtuelle dans un groupe d’affinités

Les machines virtuelles qui sont actuellement dans un groupe d’affinités n’ont pas besoin d’être supprimées du groupe d’affinités.

Une fois qu’une machine virtuelle est déployée, elle l’est sur une seule unité d’échelle. Les groupes d’affinités peuvent restreindre la taille disponible de l’ensemble des machines virtuelles pour un nouveau déploiement de machines virtuelles, mais les machines virtuelles existantes déployées sont déjà limitées à la taille disponible de l’ensemble des machines virtuelles dans l’unité d’échelle où elles sont déployées. Pour cette raison, la suppression d’une machine virtuelle du groupe d’affinités n’a aucun effet.
 

<!---HONumber=62-->