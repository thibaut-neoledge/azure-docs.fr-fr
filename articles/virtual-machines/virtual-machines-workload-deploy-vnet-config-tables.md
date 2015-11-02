<properties
	pageTitle="Réseau virtuel utilisant des tables de configuration | Microsoft Azure"
	description="Découvrez comment configurer un réseau virtuel Azure intersite avec des paramètres issus d'une table de configuration contenant des paramètres prédéfinis."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="josephd"/>

# Création d’un réseau virtuel intersite à l’aide de tables de configuration

[AZURE.INCLUDE [learn-about-deployment-models-classic-include](../../includes/learn-about-deployment-models-classic-include.md)]Modèle de déploiement Resource Manager.

Les étapes de cette rubrique vous guident lors de la création d’un réseau virtuel intersite à l’aide de paramètres spécifiés précédemment dans l’ensemble de tables de configuration suivant :

- Table V : configuration de réseaux virtuels intersite
- Table S : sous-réseaux du réseau virtuel
- Table D : serveurs DNS locaux
- Table L : préfixes d'adresses pour le réseau local

Ces tables sont généralement remplies dans une rubrique qui décrit la configuration d'une charge de travail informatique dans Azure et incluent un réseau virtuel intersite. Consulter [Phase 1 : configurer Azure](virtual-machines-workload-intranet-sharepoint-phase1.md) pour obtenir un exemple.

La procédure suivante référencie les informations contenues dans ces tables pour vous guider tout au long du processus de configuration du réseau virtuel. Si vous n’avez pas encore spécifié les paramètres de ces tables dans une autre rubrique, mais que vous voulez toujours configurer un réseau virtuel intersite, consultez [Configurer une connexion de site à site intersite sur un réseau virtuel Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [AZURE.NOTE]Cette procédure vous guide tout au long de la création d'un réseau virtuel qui utilise une connexion VPN de site à site. Pour en savoir plus sur l’utilisation d’Azure ExpressRoute pour votre connexion de site à site, consultez [Présentation technique d’ExpressRoute](../expressroute/expressroute-introduction.md).

## Création d’un nouveau réseau virtuel intersite Azure avec vos paramètres de configuration de table

1. Connectez-vous au [portail Azure](https://manage.windowsazure.com/).
2. Dans la barre des tâches, cliquez sur **Nouveau > Services réseau > Réseau virtuel > Création personnalisée**.
3. Dans la page **Détails du réseau virtuel** :
	- Entrez le nom de l'élément 1 dans la table V sous **Nom**.
	- Sélectionnez la région à partir de l'élément 2 dans la table V sous **Emplacement**.
4. Cliquez sur la flèche Suivant pour continuer.
5. Dans la page **Serveurs DNS et connectivité VPN** :
	- Sous **Serveurs DNS**, configurez un nom convivial et une adresse IP pour vos serveurs DNS locaux pour chaque entrée dans la table D.
	- Sous **Connectivité de site à site**, sélectionnez **Configuration d'un VPN de site à site**.
	- Si vous avez déjà configuré un réseau local et que vous souhaitez l'utiliser, sélectionnez son nom sous **Réseau Local**. Si vous souhaitez créer un réseau local, sélectionnez **Spécifier un nouveau réseau Local** sous **Réseau Local**.
	- Si vous n'avez pas déjà configuré un réseau local pour votre abonnement, vous ne verrez pas le champ **Réseau Local**.
6. Cliquez sur la flèche Suivant pour continuer.
7. Dans la page **Connectivité de site à site** (si vous avez sélectionné **Spécifier un nouveau réseau local** à l’étape 5) :
	- Entrez le nom de l'élément 3 dans la table V (le nom de réseau local) sous **Nom**.
	- Entrez l'adresse de l'élément 4 dans la table V sous **Adresse IP du périphérique VPN**.
	- Sous **Espace d'adressage**, entrez les espaces d'adressage IP du réseau de votre organisation en termes de préfixe (sous **IP de départ**) et de longueur de préfixe (sous **CIDR (nombre d'adresses)**) pour chaque entrée dans la table L.
8. Cliquez sur la flèche Suivant pour continuer.
9. Dans la page **Espace d’adresses du réseau virtuel** :
	- Sous **Espace d'adressage**, entrez l'espace d'adressage IP privé du réseau virtuel dans l'élément 5 de la table V en termes de préfixe (sous **IP de départ**) et de longueur de préfixe (sous **CIDR (nombre d'adresses)**).
	- Sous **Sous-réseaux**, pour chaque entrée dans la table S :
		- Entrez le nom du sous-réseau dans la colonne **Sous-réseaux** en remplaçant le nom par défaut si nécessaire.
		- Entrez l'espaces d'adressage IP privé du sous-réseau en termes de préfixe (sous **IP de départ**) et de longueur de préfixe (sous **CIDR (nombre d'adresses)**).
	- Cliquez sur **Ajouter un sous-réseau de passerelle**.
10. Cliquez sur la coche pour terminer la configuration.

## Ressources supplémentaires

[Présentation du réseau virtuel](../virtual-network/virtual-networks-overview.md)

[Tâches de configuration du réseau virtuel](../documentation/services/virtual-machines/)

[Configurer une connexion de site à site intersite sur un réseau virtuel Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md)

<!---HONumber=Oct15_HO4-->