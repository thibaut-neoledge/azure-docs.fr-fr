<properties 
   pageTitle="Résolution pour les machines virtuelles et les instances de rôle"
   description="Scénarios de résolution de noms pour Microsoft Azure IaaS, les solutions hybrides, entre différents services cloud, Active Directory et à l’aide de votre propre serveur DNS"
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/02/2015"
   ms.author="joaoma" />

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

Avec la résolution des noms DNS publics, Azure fournit la résolution de noms interne pour les machines virtuelles et les instances de rôle qui résident dans le même réseau virtuel ou service cloud. Comme les machines virtuelles/instances d’un service cloud partagent le même suffixe DNS, le nom d’hôte seul suffit, mais, dans les réseaux virtuels classiques, les différents services cloud ayant des suffixes DNS distincts, le nom de domaine complet est nécessaire pour résoudre les noms entre les différents services cloud. Dans les réseaux virtuels ARM, comme le suffixe DNS est le même sur le réseau virtuel, le nom de domaine complet n’est pas nécessaire et le nom DNS peut être affecté à la carte réseau ou à la machine virtuelle. Bien que la résolution de noms fournie par Azure ne nécessite aucune configuration, elle ne convient pas à l’ensemble des scénarios de déploiement, comme illustré dans le tableau ci-dessus.

> [AZURE.NOTE] Avec les rôles Web et de travail, il est également possible d’accéder aux adresses IP internes des instances de rôle basées sur le nom de rôle et le numéro d’instance à l’aide de l’API REST de gestion des services Microsoft Azure. Pour plus d’informations, voir [Référence de l’API REST de gestion des services](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### Fonctionnalités et considérations

**Fonctionnalités :**

- Simplicité d’utilisation : aucune configuration n’est requise pour utiliser la résolution de noms dans Azure.

- Le service de résolution de noms dans Azure est hautement disponible, vous évitant d’avoir à créer et à gérer des clusters de vos propres serveurs DNS.

- La résolution de noms est proposée entre les instances de rôle/machines virtuelles du même service cloud, sans qu’un nom de domaine complet soit nécessaire.

- La résolution de noms est proposée entre les machines virtuelles dans des réseaux virtuels ARM, sans qu’un nom de domaine complet soit nécessaire ; les réseaux virtuels classiques ont besoin du nom de domaine complet lors de la résolution de noms dans différents services cloud.

- Vous pouvez créer des noms d’hôtes qui décrivent vos déploiements de manière plus appropriée, plutôt que d’utiliser des noms générés automatiquement.

**Considérations :**

- La résolution de noms entre les réseaux virtuels et entre Azure et les machines locales n’est pas disponible.

- Le suffixe DNS créé par Azure ne peut pas être modifié.

- Vous ne pouvez pas enregistrer manuellement vos propres enregistrements.

- WINS et NetBIOS ne sont pas pris en charge. (Vous ne pouvez pas répertorier vos machines virtuelles dans l’explorateur de réseaux de l’Explorateur Windows.)

- Les noms d’hôte doivent être compatibles DNS (ils doivent comporter uniquement les caractères 0-9, a-z et « - » et ne peuvent pas démarrer ou se terminer par un « - ». Voir la section 2 de RFC 3696.)

- Le trafic de requêtes DNS est limité pour chaque machine virtuelle. Cela ne devrait pas avoir d’incidence sur la plupart des applications. Si la limitation de requêtes est respectée, assurez-vous que la mise en cache côté client est activée. Pour plus d’informations, consultez [Tirer le meilleur parti de la résolution de noms dans Azure](#Getting-the-most-from-Azure-provided-name-resolution).

- Seules les machines virtuelles des 180 premiers services cloud sont enregistrées pour chaque réseau virtuel classique. Cela ne s’applique pas aux réseaux virtuels ARM.


### Tirer le meilleur parti de la résolution de noms dans Azure
**Mise en cache côté client :**

Les requêtes DNS n’ont pas toutes besoin d’être envoyées sur le réseau. La mise en cache côté client permet de réduire la latence et d’améliorer la résilience aux spots réseau en résolvant les requêtes DNS récurrentes à partir d’un cache local. Les enregistrements DNS ont une durée de vie qui permet au cache de les stocker aussi longtemps que possible sans affecter leur fraîcheur ; la mise en cache côté client convient donc à la plupart des situations.

Le client DNS Windows par défaut possède un cache DNS intégré. Certaines distributions Linux n’incluant pas la mise en cache par défaut, il est recommandé de l’ajouter à chaque machine virtuelle Linux (après avoir vérifié qu’il n’existe pas déjà un cache local).

Un certain nombre de packages de mise en cache DNS différents sont disponibles, par exemple, dnsmasq. Voici les étapes pour installer dnsmasq sur les distributions les plus courantes :

- **Ubuntu (utilise resolvconf)** :
	- installez simplement le package dnsmasq (« sudo apt-get install dnsmasq »).
- **SUSE (utilise netconf)** :
	- installez le package dnsmasq (« sudo zypper install dnsmasq ») 
	- activez le service dnsmasq (« systemctl enable dnsmasq.service ») 
	- démarrez le service dnsmasq (« systemctl start dnsmasq.service ») 
	- modifiez « /etc/sysconfig/network/config » et remplacez NETCONFIG\_DNS\_FORWARDER="" par « dnsmasq »
	- mettez à jour resolv.conf (netconfig update) pour définir le cache en tant que programme de résolution DNS local
- **OpenLogic (utilise NetworkManager)** :
	- installez le package dnsmasq (« sudo yum install dnsmasq »)
	- activez le service dnsmasq (« systemctl enable dnsmasq.service »)
	- démarrez le service dnsmasq (« systemctl start dnsmasq.service »)
	- ajoutez « prepend domain-name-servers 127.0.0.1; » à « /etc/dhclient-eth0.conf »
	- redémarrez le service réseau (« service network restart ») pour définir le cache en tant que programme de résolution DNS local

> [AZURE.NOTE] : le package 'dnsmasq' constitue l’un des nombreux caches DNS disponibles pour Linux. Avant de l’utiliser, vérifiez son adéquation à vos besoins et assurez-vous qu’aucun autre cache n’est installé.

**Nouvelles tentatives côté client :**

DNS est principalement un protocole UDP. Comme le protocole UDP ne garantit pas la remise des messages, la logique de nouvelle tentative est gérée dans le protocole DNS lui-même. Chaque client DNS (système d’exploitation) peut afficher une logique de nouvelle tentative différente selon la préférence de son auteur :

 - Les systèmes d’exploitation Windows effectuent de nouvelles tentatives après 1 seconde, puis à nouveau après 2 secondes, 4 secondes et encore 4 secondes. 
 - La configuration Linux par défaut effectue une nouvelle tentative après 5 secondes. Il est recommandé de modifier ce paramètre pour effectuer de nouvelles tentatives 5 fois à des intervalles de 1 seconde.  

Pour vérifier les paramètres actuels sur une machine virtuelle Linux, accédez à 'cat /etc/resolv.conf' et consultez la ligne 'options', par exemple :

	options timeout:1 attempts:5

Le fichier resolv.conf est généralement généré automatiquement et ne doit pas être modifié. Les étapes spécifiques pour l’ajout de la ligne 'options' varient selon la distribution :

- **Ubuntu** (utilise resolvconf) :
	- ajoutez la ligne 'options' à '/etc/resolveconf/resolv.conf.d/head' 
	- exécutez 'resolvconf -u' pour mettre à jour
- **SUSE** (utilise netconf) :
	- ajoutez 'timeout:1 attempts:5' au paramètre NETCONFIG\_DNS\_RESOLVER\_OPTIONS="" dans '/etc/sysconfig/network/config' 
	- exécutez 'netconfig update' pour mettre à jour
- **OpenLogic** (utilise NetworkManager) :
	- ajoutez 'echo "options timeout:1 attempts:5"' à '/etc/NetworkManager/dispatcher.d/11-dhclient' 
	- exécutez 'service network restart' pour mettre à jour

## Résolution de noms à l’aide de votre propre serveur DNS

Si vos besoins en matière de résolution de noms ne sont pas satisfaits par les fonctionnalités fournies par Microsoft Azure, vous pouvez utiliser vos propres serveurs DNS. Lorsque vous utilisez vos propres serveurs DNS, vous êtes en charge de la gestion des enregistrements DNS.

> [AZURE.NOTE] Il est recommandé d’éviter d’utiliser un serveur DNS externe, sauf si votre scénario de déploiement l’exige.

## Configuration requise du serveur DNS

Si vous envisagez d’utiliser un modèle de résolution de noms qui n’est pas fourni par Azure, le serveur DNS que vous spécifiez doit prendre en charge les éléments suivants :

- Étant donné que vos serveurs DNS remplaceront ceux fournis par Azure, votre solution DNS doit répondre à vos spécifications de communication entre machines virtuelles. Si vous vous appuyez sur les noms d’hôtes pour la communication entre machines virtuelles, ces noms d’hôtes doivent être inscrits dans vos serveurs DNS. Cela dépend de nombreux facteurs, mais [ce document](virtual-networks-name-resolution-ddns.md) fournit quelques conseils utiles.

- S’il s’appuie sur un DNS dynamique, le serveur DNS doit avoir désactivé le nettoyage des enregistrements. Dans Azure, les adresses IP présentent des baux DHCP étendus, ce qui peut entraîner la suppression des enregistrements du serveur DNS durant le nettoyage.

- La récursivité doit être activée sur le serveur DNS, ceci pour permettre la résolution des noms de domaines externes.

- Le serveur DNS doit être accessible (sur le port TCP/UDP 53) par les clients sollicitant la résolution de noms et par les services et les machines virtuelles qui enregistreront leurs noms.

- Il est également recommandé de protéger le serveur contre tout accès Internet, car de nombreux robots y recherchent des programmes de résolution récursifs ouverts.

- Pour de meilleures performances, lors de l’utilisation de machines virtuelles Azure en tant que serveurs DNS, le protocole IPv6 doit être désactivé et une [adresse IP publique de niveau d’instance](virtual-networks-instance-level-public-ip.mp) doit être affectée à chaque machine virtuelle du serveur DNS.
	- Si vous choisissez Windows Server comme serveur DNS, [cet article](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) fournit une analyse des performances et des optimisations.


## Spécification des serveurs DNS

Vous pouvez spécifier plusieurs serveurs DNS à utiliser par vos machines virtuelles et vos instances de rôle. Pour chaque requête DNS, le client essaie le serveur DNS préféré. Il sollicite les autres serveurs uniquement si le préféré ne répond pas, c’est-à-dire si les requêtes DNS ne sont pas soumises à l’équilibrage de charge entre les différents serveurs DNS. Pour cette raison, vérifiez que les serveurs DNS sont répertoriés dans l’ordre approprié pour votre environnement.

> [AZURE.NOTE] Si vous modifiez les paramètres DNS sur un fichier de configuration de réseau virtuel d’ores et déjà déployé, vous devez redémarrer l’ensemble des machines virtuelles pour que les modifications prennent effet.

### Définition d’un serveur DNS dans le portail de gestion

Lorsque vous créez un réseau virtuel dans le portail de gestion, vous pouvez spécifier l’adresse IP et le nom du ou des serveurs DNS que vous souhaitez utiliser. Une fois que le réseau virtuel est créé, les machines virtuelles et les instances de rôle déployées sur le réseau virtuel sont automatiquement configurées avec les paramètres DNS spécifiés. Les serveurs DNS spécifiés pour un service cloud spécifique (Azure Classic) ou une carte réseau (déploiements ARM) sont prioritaires sur ceux spécifiés pour le réseau virtuel. Voir [Gestion des propriétés du réseau virtuel](virtual-networks-settings.md).

### Définition d’un serveur DNS à l’aide de fichiers de configuration (Azure Classic)

Pour les réseaux virtuels classiques, vous pouvez spécifier des paramètres DNS à l’aide de deux fichiers de configuration différents : le fichier de *configuration de réseau* et le fichier de *configuration de service*.

Le fichier de configuration de réseau décrit les réseaux virtuels de votre abonnement. Lorsque vous ajoutez des instances de rôle ou des machines virtuelles à un service cloud de réseau virtuel, les paramètres DNS de votre fichier de configuration de réseau sont appliqués à chaque instance de rôle ou machine virtuelle, sauf si des serveurs DNS spécifiques au service cloud ont été spécifiés.

Le fichier de configuration de service est créé pour chaque service cloud que vous ajoutez à Azure. Lorsque vous ajoutez des instances de rôle ou des machines virtuelles au service cloud, les paramètres DNS de votre fichier de configuration de service sont appliqués à chaque instance de rôle ou machine virtuelle.

> [AZURE.NOTE] Les serveurs DNS du fichier de configuration de service remplacent les paramètres du fichier de configuration de réseau.


## Étapes suivantes

[Schéma de configuration de service Microsoft Azure](https://msdn.microsoft.com/library/azure/ee758710)

[Schéma de configuration du réseau virtuel](https://msdn.microsoft.com/library/azure/jj157100)

[À propos de la configuration des paramètres de réseau virtuel dans le Portail de gestion](virtual-networks-settings.md)

[Configuration d'un réseau virtuel à l'aide d'un fichier de configuration réseau](virtual-networks-using-network-configuration-file.md)

<!---HONumber=AcomDC_0204_2016-->