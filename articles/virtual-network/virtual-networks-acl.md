<properties 
   pageTitle="Qu’est-ce qu’une liste de contrôle d’accès (ACL) réseau ?"
   description="En savoir plus sur les listes de contrôle d’accès"
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
   ms.date="09/22/2015"
   ms.author="telmos" />

# Qu’est-ce qu’une liste de contrôle d’accès de point de terminaison (ACL) ?

Une liste de contrôle d’accès de point de terminaison (ACL) est une amélioration de sécurité disponible pour votre déploiement Azure. Une liste ACL permet d’autoriser ou refuser le trafic de manière sélective pour un point de terminaison de machine virtuelle. Cette capacité de filtrage des paquets offre une couche de sécurité supplémentaire. Vous ne pouvez spécifier des listes de contrôle d’accès réseau que pour les points de terminaison. Vous ne pouvez pas spécifier de liste ACL pour un réseau virtuel ou un sous-réseau spécifique contenu dans un réseau virtuel.

> [AZURE.IMPORTANT]Il est recommandé d’utiliser autant que possible les groupes de sécurité réseau (NSG) au lieu des listes de contrôle d’accès. Pour en savoir plus sur les groupes de sécurité réseau, consultez [Qu’est-ce qu’un groupe de sécurité réseau ?](../virtual-networks-nsg).

Les listes ACL peuvent être configurées à l’aide de PowerShell ou du portail de gestion. Pour configurer une liste de contrôle d’accès réseau à l’aide de PowerShell, consultez [Gestion des listes de contrôle d’accès (ACL) pour les points de terminaison à l’aide de PowerShell](virtual-networks-acl-powershell.md). Pour configurer une liste de contrôle d’accès réseau à l’aide du portail de gestion, consultez [Configuration des points de terminaison sur une machine virtuelle](../virtual-machines-set-up-endpoints/).

Les listes de contrôle d’accès réseau vous permettent d’effectuer les opérations suivantes :

- Autoriser ou refuser le trafic entrant de manière sélective, en fonction de la plage d’adresses IPv4 du sous-réseau distant, sur un point de terminaison d’entrée de machine virtuelle.

- Bloquer certaines adresses IP.

- Créer plusieurs règles par point de terminaison de machine virtuelle.

- Spécifier jusqu’à 50 règles de liste ACL par point de terminaison de machine virtuelle.

- Utiliser les règles dans un ordre précis pour garantir que l’ensemble approprié de règles est appliqué à un point de terminaison donné de machine virtuelle (de la plus basse à la plus élevée).

- Spécifier une liste ACL pour une adresse IPv4 spécifique du sous-réseau distant.

## Fonctionnement des listes ACL

Une liste ACL est un objet qui contient une liste de règles. Quand vous créez une liste ACL et que vous l’appliquez à un point de terminaison de machine virtuelle, le filtrage de paquets a lieu sur le nœud hôte de votre machine virtuelle. Cela signifie que le trafic provenant d’adresses IP distantes est filtré par le nœud hôte en fonction des règles de liste ACL correspondantes au lieu de votre machine virtuelle. Cela évite à votre machine virtuelle de dépenser de précieux cycles de processeur en filtrage des paquets.

Quand une machine virtuelle est créée, une liste ACL par défaut est mise en place pour bloquer tout le trafic entrant. Toutefois, si un point de terminaison est créé (port 3389), la liste ACL par défaut est modifiée pour autoriser tout le trafic entrant sur ce point de terminaison. Le trafic entrant provenant des sous-réseaux distants est alors autorisé sur ce point de terminaison et aucun pare-feu n’a besoin d’être configuré. Tous les autres ports sont bloqués pour le trafic entrant, sauf si des points de terminaison sont créés pour ces ports. Le trafic sortant est autorisé par défaut.

**Exemple de table de listes ACL par défaut**

| **N° de règle** | **Sous-réseau distant** | **Point de terminaison** | **Permit/Deny** |
|--------|---------------|----------|-------------|
| 100 | 0\.0.0.0/0 | 3389 | Permit |

## Permit et Deny

Vous pouvez autoriser ou refuser le trafic réseau de manière sélective pour un point de terminaison d’entrée de machine virtuelle en créant des règles qui spécifient « Permit » ou « Deny ». Il est important de noter que, par défaut, quand un point de terminaison est créé, tout le trafic est refusé sur le point de terminaison. Pour cette raison, vous devez comprendre comment créer des règles Permit/Deny et les placer dans l’ordre de priorité approprié, pour garantir un contrôle granulaire du trafic réseau que vous voulez autoriser sur le point de terminaison de machine virtuelle.

Éléments à prendre en considération :

1. **Aucune liste ACL** : par défaut, quand un point de terminaison est créé, tout le trafic est autorisé sur le point de terminaison.

1. **Permit** : quand vous ajoutez une ou plusieurs plages « Permit », vous refusez toutes les autres plages par défaut. Seuls les paquets de la plage d’adresses IP autorisée pourront communiquer avec le point de terminaison de machine virtuelle.

1. **Deny** : quand vous ajoutez une ou plusieurs plages « Deny », vous autorisez toutes les autres plages de trafic par défaut.

1. **Combinaison de Permit et Deny** : vous pouvez utiliser une combinaison de règles « Permit » et « Deny » quand vous voulez qu’une plage d’adresses IP spécifique soit autorisée ou refusée.

## Règles et priorité des règles

Les listes de contrôle d’accès réseau peuvent être configurées sur des points de terminaison de machine virtuelle spécifiques. Par exemple, vous pouvez spécifier une liste de contrôle d’accès réseau pour un point de terminaison RDP créé sur une machine virtuelle qui verrouille l’accès de certaines adresses IP. Le tableau ci-dessous indique un moyen d’accorder l’accès à des adresses IP virtuelles (VIP) publiques d’une plage donnée à un point de terminaison RDP. Toutes les autres adresses IP distantes sont refusées. Nous suivons un ordre de règles qui donne la *priorité à la plus basse*.

### Plusieurs règles

Dans l’exemple ci-dessous, si vous souhaitez autoriser l’accès au point de terminaison RDP uniquement à partir de deux plages d’adresses IPv4 publiques (65.0.0.0/8 et 159.0.0.0/8), vous spécifiez deux règles *Permit*. Dans ce cas, le point de terminaison RDP étant créé par défaut pour une machine virtuelle, vous pouvez vouloir verrouiller l’accès au port RDP pour un sous-réseau distant. L’exemple ci-dessous indique un moyen d’accorder l’accès à des adresses IP virtuelles (VIP) publiques d’une plage donnée pour un point de terminaison RDP. Toutes les autres adresses IP distantes sont refusées. Cela fonctionne, car les listes de contrôle d’accès réseau permettent de configurer un point de terminaison de machine virtuelle spécifique et l’accès est refusé par défaut.

**Exemple - Plusieurs règles**

| **N° de règle** | **Sous-réseau distant** | **Point de terminaison** | **Permit/Deny** |
|--------|---------------|----------|-------------|
| 100 | 65\.0.0.0/8 | 3389 | Permit |
| 200 | 159\.0.0.0/8 | 3389 | Permit |

### Ordre des règles

Comme plusieurs règles peuvent être spécifiées pour un point de terminaison, elles doivent être organisées pour déterminer celle qui est prioritaire. L’ordre des règles indique la priorité. Les listes de contrôle d’accès réseau suivent un ordre de règles qui donne la *priorité à la plus basse*. Dans l’exemple ci-dessous, l’accès au point de terminaison sur le port 80 est accordé de manière sélective à certaines plages d’adresses IP uniquement. Pour ce faire, nous utilisons une règle Deny (règle n° 100) pour les adresses de l’espace 175.1.0.1/24. Une deuxième règle est ensuite spécifiée avec la priorité 200 qui permet l’accès à toutes les autres adresses de la plage 175.0.0.0/8.

**Exemple - Priorité des règles**

| **N° de règle** | **Sous-réseau distant** | **Point de terminaison** | **Permit/Deny** |
|--------|---------------|----------|-------------|
| 100 | 175\.1.0.1/24 | 80 | Deny |
| 200 | 175\.0.0.0/8 | 80 | Permit |

## Listes de contrôle d’accès réseau et jeux d’équilibrage de la charge

Les liste de contrôle d’accès réseau peuvent être spécifiées sur un point de terminaison de jeu d’équilibrage de la charge. Si une liste ACL est spécifiée pour un jeu d’équilibrage de la charge, la liste de contrôle d’accès réseau est appliquée à toutes les machines virtuelles de ce jeu d’équilibrage de la charge. Par exemple, si un jeu d’équilibrage de la charge est créé avec « Port 80 » et qu’il contient trois machines virtuelles, la liste de contrôle d’accès réseau créée sur le point de terminaison « Port 80 » d’une machine virtuelle s’applique automatiquement aux autres machines virtuelles.

![Listes de contrôle d’accès réseau et jeux d’équilibrage de la charge](./media/virtual-networks-acl/IC674733.png)

## Étapes suivantes

[Comment gérer les listes de contrôle d’accès (ACL) pour les points de terminaison à l’aide de PowerShell](../virtual-networks-acl-powershell)

<!---HONumber=Oct15_HO3-->