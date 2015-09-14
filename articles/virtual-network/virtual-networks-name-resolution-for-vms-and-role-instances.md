<properties 
   pageTitle="Résolution pour les machines virtuelles et les instances de rôle"
	description="Scénarios de résolution de noms pour Microsoft Azure IaaS, les solutions hybrides, entre différents services cloud, Active Directory et à l’aide de votre propre serveur DNS"
	services="virtual-network"
	documentationCenter="na"
	authors="joaoma"
	manager="jdial"
	editor="tysonn"/>
<tags 
   ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/10/2015"
	ms.author="joaoma"/>

# Résolution de noms pour les machines virtuelles et les instances de rôle

Selon la manière dont vous utilisez Azure pour héberger les solutions IaaS, PaaS et les solutions hybrides, vous pouvez configurer les machines virtuelles et les instances de rôles créées pour qu’elles communiquent entre elles. Bien que cette communication puisse être mise en place par le biais d’adresses IP, il est bien plus simple d’utiliser des noms dont vous vous souviendrez facilement et qui ne seront pas modifiés.

Lorsque des instances de rôle et des machines virtuelles hébergées dans Azure doivent résoudre des noms de domaine en adresses IP internes, elles peuvent utiliser l’une des deux méthodes suivantes :

- [Résolution de noms dans Azure](#azure-provided-name-resolution)

- [Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server)

Le type de résolution de noms que vous utilisez dépend de la manière dont vos machines virtuelles et vos instances de rôle doivent communiquer entre elles.

**Le tableau suivant illustre des scénarios et des solutions correspondantes de résolution de noms :**

| **Scénario** | **Résolution de noms fournie par :** | **Pour plus d’informations, consultez :** |
|--------------|----------------------------------|-------------------------------|
| Résolution de noms entre des instances de rôle ou des machines virtuelles situées dans le même service cloud | Résolution de noms dans Azure | [Résolution de noms dans Azure](#azure-provided-name-resolution)|
| Résolution de noms entre des machines virtuelles et des instances de rôle situées dans le même réseau virtuel | Résolution de noms dans Azure (déploiements ARM uniquement) ou Résolution de noms à l’aide de votre propre serveur DNS | [Résolution de noms dans Azure](#azure-provided-name-resolution), [Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server) et [Configuration requise serveur DNS](#dns-server-requirements) |
| Résolution de noms entre des machines virtuelles et des instances de rôles situées dans différents réseaux virtuels | Résolution de noms à l’aide de votre propre serveur DNS| [Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server) et [Configuration requise serveur DNS](#dns-server-requirements)|
| Entre différents locaux : résolution de noms entre les instances de rôle ou machines virtuelles dans Azure et sur les ordinateurs locaux| Résolution de noms à l’aide de votre propre serveur DNS| [Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server) et [Configuration requise serveur DNS](#dns-server-requirements)|
| Recherche inversée des adresses IP internes| Résolution de noms à l’aide de votre propre serveur DNS| [Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server) et [Configuration requise serveur DNS](#dns-server-requirements)|
| Résolution de noms pour les domaines non publics (par exemple, domaines Active Directory)| Résolution de noms à l’aide de votre propre serveur DNS| [Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server) et [Configuration requise serveur DNS](#dns-server-requirements)|
| Résolution de noms entre des instances de rôle situées dans différents services cloud, non dans un réseau virtuel| Non applicable. La connectivité entre des machines virtuelles et des instances de rôle de différents services cloud n’est pas prise en charge en dehors d’un réseau virtuel.| Non applicable.|


## Résolution de noms dans Azure

Avec la résolution des noms DNS publics, Azure fournit la résolution de noms interne pour les machines virtuelles et les instances de rôle qui résident dans le même réseau virtuel ou service cloud. Les machines virtuelles/instances dans un service cloud partagent le même suffixe DNS, le nom d’hôte seul est donc suffisant. Dans les réseaux virtuels classiques, différents services cloud ont des suffixes DNS différents, le nom de domaine complet est donc nécessaire. Dans les réseaux virtuels ARM, le suffixe DNS est courant sur le réseau virtuel ; le nom de domaine complet n’est donc pas nécessaire et le nom DNS peut être affecté à la carte réseau ou à la machine virtuelle. Bien que la résolution de noms fournie par Azure ne nécessite aucune configuration, elle ne convient pas à l’ensemble des scénarios de déploiement, comme illustré dans le tableau ci-dessus.

> [AZURE.NOTE]Avec les rôles Web et de travail, il est également possible d’accéder aux adresses IP internes des instances de rôle basées sur le nom de rôle et le numéro d’instance à l’aide de l’API REST de gestion des services Microsoft Azure. Pour plus d’informations, voir [Référence de l’API REST de gestion des services](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### Fonctionnalités et considérations

**Fonctionnalités :**

- Simplicité d’utilisation : aucune configuration n’est requise pour utiliser la résolution de noms dans Azure.

- La résolution de noms est proposée entre les instances de rôle ou machines virtuelles du même service cloud, sans qu’un nom de domaine complet soit nécessaire.

- La résolution de noms est proposée entre les machines virtuelles dans des réseaux virtuels ARM, sans qu’un nom de domaine complet soit nécessaire ; les réseaux classiques ont besoin du nom de domaine complet lors de la résolution de noms dans différents services cloud.

- Vous pouvez créer des noms d’hôtes qui décrivent vos déploiements de manière plus appropriée, plutôt que d’utiliser des noms générés automatiquement.

**Considérations :**

- La résolution de noms entre réseaux virtuels n’est pas disponible.

- Vous pouvez inscrire des noms d’hôte uniquement pour les machines virtuelles et les instances de rôle hébergées sur les 180 premiers services cloud ajoutés à un réseau virtuel Azure. Si vous possédez plus de 180 services cloud, quel que soit le nombre de machines virtuelles et d’instances de rôle de chaque service, vous devez fournir votre propre serveur DNS pour la résolution de noms.

- La résolution de noms entre locaux n’est pas disponible.

- Le suffixe DNS créé par Azure ne peut pas être modifié.

- Vous ne pouvez pas enregistrer manuellement vos propres enregistrements.

- WINS et NetBIOS ne sont pas pris en charge. (Vous ne pouvez pas répertorier vos machines virtuelles dans l’explorateur de réseaux de l’Explorateur Windows.)

- Les noms d’hôte doivent être compatibles DNS (ils doivent comporter uniquement les caractères 0-9, a-z et « - » et ne peuvent pas démarrer ou se terminer par un « - ». Voir la section 2 de RFC 3696.)

- Le trafic de requêtes DNS est limité par machine virtuelle. Si votre application exécute des requêtes DNS fréquentes sur plusieurs noms cibles, certaines de ces requêtes peuvent expirer. Pour éviter cela, il est recommandé d’activer la mise en cache client. Cette option est activée par défaut sous Windows, mais il est possible que cela ne soit pas le cas avec certaines distributions Linux.

## Résolution de noms à l’aide de votre propre serveur DNS

Si vos besoins en matière de résolution de noms ne sont pas satisfaits par les fonctionnalités fournies par Azure, vous pouvez utiliser votre ou vos propres serveurs DNS. Si vous utilisez votre ou vos propres serveurs DNS, vous êtes en charge de la gestion des enregistrements DNS.

> [AZURE.NOTE]Il est recommandé d’éviter d’utiliser un serveur DNS externe, sauf si votre scénario de déploiement l’exige.

## Configuration requise du serveur DNS

Si vous envisagez d’utiliser un modèle de résolution de noms qui n’est pas fourni par Azure, le serveur DNS que vous spécifiez doit prendre en charge les éléments suivants :

- Le serveur DNS doit accepter l’enregistrement DNS dynamique via le protocole DDNS (DNS dynamique) ou bien vous devez créer les enregistrements requis.

- S’il s’appuie sur un DNS dynamique, le serveur DNS doit avoir désactivé le nettoyage des enregistrements. Dans Azure, les adresses IP présentent des baux DHCP étendus, ce qui peut entraîner la suppression des enregistrements du serveur DNS durant le nettoyage.

- La récursivité doit être activée sur le serveur DNS, ceci pour permettre la résolution des noms de domaines externes.

- Le serveur DNS doit être accessible (sur le port TCP/UDP 53) par les clients sollicitant la résolution de noms et par les services et les machines virtuelles qui enregistreront leurs noms.

- Il est également recommandé de protéger le serveur contre tout accès Internet, car de nombreux robots y recherchent des programmes de résolution récursifs ouverts.

- Pour de meilleures performances, lors de l’utilisation de machines virtuelles Azure en tant que serveurs DNS, le protocole IPv6 doit être désactivé et une [adresse IP publique de niveau d’instance](virtual-networks-instance-level-public-ip.mp) doit être affectée à chaque machine virtuelle du serveur DNS.

## Spécification des serveurs DNS

Vous pouvez spécifier plusieurs serveurs DNS à utiliser par vos machines virtuelles et vos instances de rôle. Pour chaque requête DNS, le client essaie le serveur DNS préféré. Il sollicite les autres serveurs uniquement si le préféré ne répond pas, c’est-à-dire si les requêtes DNS ne sont pas soumises à l’équilibrage de charge entre les différents serveurs DNS. Pour cette raison, vérifiez que les serveurs DNS sont répertoriés dans l’ordre approprié pour votre environnement.

> [AZURE.NOTE]Si vous modifiez les paramètres DNS sur un fichier de configuration de réseau virtuel d’ores et déjà déployé, vous devez redémarrer l’ensemble des machines virtuelles pour que les modifications prennent effet.

### Définition d’un serveur DNS dans le portail de gestion

Lorsque vous créez un réseau virtuel dans le portail de gestion, vous pouvez spécifier l’adresse IP et le nom du ou des serveurs DNS que vous souhaitez utiliser. Une fois que le réseau virtuel est créé, les machines virtuelles et les instances de rôle déployées sur le réseau virtuel sont automatiquement configurées avec les paramètres DNS spécifiés. Les serveurs DNS spécifiés pour un service cloud spécifique (Azure Classic) ou une carte réseau (déploiements ARM) sont prioritaires sur ceux spécifiés pour le réseau virtuel. Voir [Gestion des propriétés du réseau virtuel](virtual-networks-settings.md)

### Définition d’un serveur DNS à l’aide de fichiers de configuration (Azure Classic)

Pour les réseaux virtuels classiques, vous pouvez spécifier des paramètres DNS à l’aide de deux fichiers de configuration différents : le fichier de *configuration de réseau* et le fichier de *configuration de service*.

> [AZURE.NOTE]Les serveurs DNS du fichier de configuration de service remplacent les paramètres du fichier de configuration de réseau.
 
Le fichier de configuration de réseau décrit les réseaux virtuels de votre abonnement. Lorsque vous ajoutez des instances de rôle ou des machines virtuelles à un service cloud de réseau virtuel, les paramètres DNS de votre fichier de configuration de réseau sont appliqués à chaque instance de rôle ou machine virtuelle, sauf si des serveurs DNS spécifiques au service cloud ont été spécifiés.

Le fichier de configuration de service est créé pour chaque service cloud que vous ajoutez à Azure. Lorsque vous ajoutez des instances de rôle ou des machines virtuelles au service cloud, les paramètres DNS de votre fichier de configuration de service sont appliqués à chaque instance de rôle ou machine virtuelle.



## Étapes suivantes

[Schéma de configuration de service Microsoft Azure](https://msdn.microsoft.com/library/azure/ee758710)

[Schéma de configuration du réseau virtuel](https://msdn.microsoft.com/library/azure/jj157100)

[À propos de la configuration des paramètres de réseau virtuel dans le Portail de gestion](virtual-networks-settings.md)

[Configuration d'un réseau virtuel à l'aide d'un fichier de configuration réseau](virtual-networks-using-network-configuration-file.md)

<!---HONumber=September15_HO1-->