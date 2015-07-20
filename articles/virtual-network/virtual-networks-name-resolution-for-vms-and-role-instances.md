<properties 
   pageTitle="Résolution pour les machines virtuelles et les instances de rôle"
   description="Scénarios de résolution de noms pour Microsoft Azure IaaS, les solutions hybrides, entre différents services cloud, Active Directory et à l’aide de votre propre serveur DNS"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2015"
   ms.author="joaoma" />

# Résolution de noms pour les machines virtuelles et les instances de rôle

Selon la manière dont vous utilisez Azure pour héberger les solutions IaaS, PaaS et les solutions hybrides, vous pouvez configurer les machines virtuelles et les instances de rôles créées pour qu’elles interagissent avec d’autres machines virtuelles et instances de rôle. Bien que cette communication puisse être mise en place par le biais d’adresses IP, il est bien plus simple d’utiliser des noms d’hôte, dont vous vous souviendrez facilement. Toutefois, ces noms d’hôte doivent être résolus d’une manière ou d’une autre en adresses IP afin d’établir la communication.

Lorsque des instances de rôle et des machines virtuelles hébergées dans Azure doivent résoudre des noms d’hôte et de domaine en adresses IP internes, elles peuvent utiliser l’une des deux méthodes suivantes :

- [Résolution de noms dans Azure](azure-provided-name-resolution)

- [Résolution de noms à l’aide de votre propre serveur DNS](name-resolution-using-your-own-DNS-server)

Le type de résolution de noms que vous utilisez dépend de la manière dont vos machines virtuelles et vos instances de rôle doivent communiquer au sein de votre service cloud et avec d’autres services cloud.

**Le tableau suivant illustre des scénarios et des solutions correspondantes de résolution de noms :**

| **Scénario** | **Résolution de noms fournie par :** | **Pour plus d’informations, consultez :** |
|------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Résolution de noms entre des instances de rôle ou des machines virtuelles situées dans le même service cloud | Résolution de noms dans Azure | [Résolution de noms dans Azure](#azure-provided-name-resolution) |
| Résolution de noms entre des machines virtuelles et des instances de rôle situées dans le même réseau virtuel | Résolution de noms dans Azure, avec la résolution FQDNorName, à l’aide de votre propre serveur DNS | - [Résolution de noms dans Azure](#azure-provided-name-resolution) - [Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server) - [Configuration requise serveur DNS](#dns-server-requirements) |
| Résolution de noms entre des machines virtuelles et des instances de rôles situées dans différents réseaux virtuels | Résolution de noms à l’aide de votre propre serveur DNS | - [Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server) - [Configuration requise serveur DNS](#dns-server-requirements) |
| Entre différents locaux : résolution de noms entre les instances de rôle ou machines virtuelles dans Azure et sur les ordinateurs locaux | Résolution de noms à l’aide de votre propre serveur DNS | - [Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server) - [Configuration requise serveur DNS](#dns-server-requirements) |
| Recherche inversée des adresses IP internes | Résolution de noms à l’aide de votre propre serveur DNS | - [Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server) - [Configuration requise serveur DNS](#dns-server-requirements) |
| Résolution de noms pour les domaines personnalisés (par exemple, domaines Active Directory, domaines inscrits) | Résolution de noms à l’aide de votre propre serveur DNS | - [Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server) - [Configuration requise serveur DNS](#dns-server-requirements) |
| Résolution de noms entre des instances de rôle situées dans différents services cloud, non dans un réseau virtuel | Non applicable. La connectivité entre des machines virtuelles et des instances de rôle de différents services cloud n’est pas prise en charge en dehors d’un réseau virtuel. | Non applicable. |

> [AZURE.NOTE]La résolution de noms entre des ordinateurs sur Internet et vos points de terminaisons publics est automatiquement fournie par Azure, sans nécessiter aucune configuration.

## Résolution de noms dans Azure

Avec la résolution pour les domaines de l’Internet public, Azure propose la résolution de noms d’hôte des machines virtuelles et des instances de rôle hébergées dans le même service cloud à l’aide de noms d’hôtes, et entre des machines virtuelles et instances de rôle de différents services cloud hébergées sur le même réseau virtuel à l’aide de noms de domaine complets. Bien que la résolution de noms fournie par Azure ne nécessite aucune configuration, elle ne convient pas à l’ensemble des scénarios de déploiement, comme illustré dans le tableau ci-dessus.

> [AZURE.NOTE]Avec les rôles Web et de travail, il est également possible d’accéder aux adresses IP internes des instances de rôle basées sur le nom de rôle et le numéro d’instance à l’aide de l’API REST de gestion des services Microsoft Azure. Pour plus d’informations, consultez la [documentation de référence sur l’API REST de gestion des services](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### Fonctionnalités et considérations

**Fonctionnalités :**

- Simplicité d’utilisation : aucune configuration n’est requise pour utiliser le service DNS fourni par Azure.

- La résolution de noms est proposée entre les instances de rôle ou machines virtuelles du même service cloud.

- La résolution de noms est fournie entre les instances de rôle et les machines virtuelles situées sur le même réseau virtuel mais dans différents services cloud, à l’aide du nom de domaine complet de l’instance de rôle ou de la machine virtuelle cibles.

- Vous pouvez créer des noms d’hôtes qui décrivent vos déploiements de manière plus appropriée, plutôt que d’utiliser des noms générés automatiquement.

- Les recherches DNS standard dédiées à la résolution des noms de domaine publics sont prises en charge.

**Considérations :**

- La résolution de noms entre réseaux virtuels n’est pas disponible.

- Vous pouvez inscrire des noms d’hôte et noms de domaine complets uniquement pour les machines virtuelles et les instances de rôle hébergées sur les 180 premiers services cloud ajoutés à un réseau virtuel Azure. Si vous possédez plus de 180 services cloud, quel que soit le nombre de machines virtuelles et d’instances de rôle de chaque service, vous devez fournir votre propre serveur DNS pour la résolution de noms.

- L’utilisation de plusieurs noms d’hôte pour les mêmes machines virtuelles ou instances de rôle n’est pas prise en charge.

- La résolution de noms entre locaux n’est pas disponible.

- Le suffixe DNS créé par Azure ne peut pas être modifié.

- Vous ne pouvez pas enregistrer manuellement vos propres enregistrements dans un serveur DNS fourni par Azure.

- WINS et NetBIOS ne sont pas pris en charge. (Vous ne pouvez pas répertorier vos machines virtuelles dans l’explorateur de réseaux de l’Explorateur Windows.)

- Les noms d’hôte doivent être compatibles DNS (ils doivent comporter uniquement les caractères 0-9, a-z et « - » et ne peuvent pas démarrer ou se terminer par un « - ». Voir la section 2 de RFC 3696.)

- Le trafic de requêtes DNS est limité par machine virtuelle. Si votre application exécute des requêtes DNS fréquentes sur plusieurs noms cibles, certaines de ces requêtes peuvent expirer. Pour éviter cela, il est recommandé de recourir à la mise en cache client.

## Résolution de noms à l’aide de votre propre serveur DNS

Si vos besoins en matière de résolution de noms ne sont pas satisfaits par les fonctionnalités fournies par Microsoft Azure, vous pouvez utiliser votre propre serveur DNS. Si vous utilisez votre propre serveur DNS, vous êtes en charge de la gestion des enregistrements nécessaires pour vos services cloud.

> [AZURE.NOTE]Il est recommandé d’éviter d’utiliser un serveur DNS externe, sauf si votre scénario de déploiement l’exige.

## Configuration requise du serveur DNS

Si vous envisagez d’utiliser un modèle de résolution de noms qui n’est pas fourni par Azure, le serveur DNS que vous spécifiez doit prendre en charge l’enregistrement dynamique DNS via Dynamic DNS (DDNS).

- Le nettoyage des enregistrements doit être désactivé sur votre serveur DNS. Les adresses IP Azure présentent des baux étendus. Dès lors, les enregistrements peuvent être supprimés du serveur DNS durant le nettoyage.

- La récursivité doit être activée sur le serveur DNS, ceci pour permettre la résolution des noms de domaines externes.

- Le serveur DNS doit être accessible (sur le port TCP/UDP 53) par les clients sollicitant la résolution de noms et par les services et les machines virtuelles qui enregistreront leurs noms.

- Il est également recommandé de protéger le serveur contre tout accès Internet, car de nombreux robots y recherchent des programmes de résolution récursifs ouverts.


## Spécification des serveurs DNS

Vous pouvez spécifier plusieurs serveurs DNS à utiliser par vos machines virtuelles et vos instances de rôle. Le cas échéant, les serveurs DNS seront utilisés dans l’ordre dans lequel ils sont définis, selon un modèle de basculement (non de tourniquet [round robin]). Pour chaque requête DNS, le client essaie le serveur DNS préféré. Il sollicite les autres serveurs uniquement si le préféré ne répond pas. Pour cette raison, vérifiez que les serveurs DNS sont répertoriés dans l’ordre approprié pour votre environnement.

> [AZURE.NOTE]Si vous modifiez les paramètres DNS sur un fichier de configuration de réseau virtuel d’ores et déjà déployé, vous devez redémarrer l’ensemble des machines virtuelles pour que les modifications prennent effet.

### Définition d’un serveur DNS à l’aide du portail de gestion

Lorsque vous créez votre réseau virtuel à l’aide du portail de gestion, vous pouvez spécifier l’adresse IP et le nom du (ou des) serveur(s) DNS que vous souhaitez utiliser. Une fois que le réseau virtuel est créé, les machines virtuelles et les instances de rôle déployées sur le réseau virtuel sont automatiquement configurées avec vos paramètres DNS spécifiés, sauf si vous spécifiez le(s) serveur(s) à utiliser pour le déploiement. Pour plus d’informations sur la configuration des paramètres Microsoft Azure Virtual Network, consultez la section [À propos des paramètres de réseau virtuel dans le portail de gestion](https://msdn.microsoft.com/library/azure/jj156074.aspx).

> [AZURE.NOTE]Vous pouvez utiliser jusqu’à 9 serveurs DNS uniquement.

### Définition d’un serveur DNS à l’aide de fichiers de configuration

Vous pouvez spécifier des paramètres DNS à l’aide de deux fichiers de configuration différents : le fichier de *configuration de réseau* et le fichier de *configuration de service*.

Le fichier de configuration de réseau est créé pour chaque machine virtuelle que vous ajoutez à Azure. Lorsque vous ajoutez des instances de rôle ou des machines virtuelles à un service cloud de réseau virtuel, les paramètres DNS de votre fichier de configuration réseau sont appliqués à l’instance de rôle ou à la machine virtuelle, sauf si le fichier de configuration de service présente ses propres paramètres DNS.

Le fichier de configuration de service est créé pour chaque service cloud que vous ajoutez à Azure. Lorsque vous ajoutez des instances de rôle ou des machines virtuelles au service cloud, les paramètres DNS de votre fichier de configuration de service sont appliqués à l’instance de rôle ou à la machine virtuelle.

> [AZURE.NOTE]Les paramètres du fichier de configuration de service remplacent les paramètres du fichier de configuration de réseau. Par exemple, si une machine virtuelle est ajoutée à un service cloud qui fait partie d’un réseau virtuel et que les fichiers de configuration de réseau et de service présentent tous deux des paramètres DNS, les paramètres DNS du fichier de configuration de service sont appliqués à la machine virtuelle.


## Voir aussi

[Schéma de configuration de service Microsoft Azure](https://msdn.microsoft.com/library/azure/ee758710) [Schéma de configuration Virtual Network](https://msdn.microsoft.com/library/azure/jj157100) [À propos de la configuration des paramètres Virtual Network dans le portail de gestion](https://msdn.microsoft.com/library/azure/jj156074.aspx) [Configurer un réseau virtuel à l’aide d’un fichier de configuration de réseau](https://msdn.microsoft.com/library/azure/jj156097.aspx) [Tâches de configuration Microsoft Azure Virtual Network](https://msdn.microsoft.com/library/azure/jj156206.aspx)

<!---HONumber=July15_HO2-->