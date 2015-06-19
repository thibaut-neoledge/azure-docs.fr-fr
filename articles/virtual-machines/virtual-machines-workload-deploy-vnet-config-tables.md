<properties 
	pageTitle="Création d'un réseau virtuel intersite à l'aide de tables de configuration" 
	description="Cette rubrique décrit comment configurer un réseau virtuel intersite à l'aide de tables de configuration prédéterminées." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="josephd"/>

# Création d'un réseau virtuel intersite à l'aide de tables de configuration

Les étapes de cette rubrique vous guident lors de la création d'un réseau virtuel intersite à l'aide de paramètres spécifiés précédemment dans l'ensemble de tables de configuration suivant :

- Table V : configuration de réseaux virtuels intersite
- Table S : sous-réseaux du réseau virtuel
- Table D : serveurs DNS locaux
- Table L : préfixes d'adresses pour le réseau local

Ces tables sont généralement remplies dans une rubrique qui décrit la configuration d'une charge de travail informatique dans Azure et incluent un réseau virtuel intersite. Consulter [Phase 1 : configurer Azure](virtual-machines-workload-intranet-sharepoint-phase1.md) pour obtenir un exemple.

La procédure suivante référencie les informations contenues dans ces tables pour vous guider tout au long du processus de configuration du réseau virtuel. Si vous n'avez pas encore spécifié les paramètres de ces tables dans une autre rubrique, mais souhaitez toujours configurer un réseau virtuel intersite, consultez [Configuration d'une connexion de site à site intersite sur un réseau virtuel Azure](https://msdn.microsoft.com/library/dn133795.aspx).

> [AZURE.NOTE]Cette procédure vous guide tout au long de la création d'un réseau virtuel qui utilise une connexion VPN de site à site. Pour en savoir plus sur l'utilisation d'ExpressRoute pour votre connexion de site à site, consultez [Présentation technique d'ExpressRoute](https://msdn.microsoft.com/library/dn606309.aspx).
 
## Créer un réseau virtuel intersite Azure avec vos paramètres de configuration de table

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/).
2. Dans la barre des tâches, cliquez sur **Nouveau > Services réseau > Réseau virtuel > Création personnalisée**.
3. Sur la page Détails du réseau virtuel :
- Entrez le nom de l'élément 1 dans la table V sous **Nom**.
- Sélectionnez la région à partir de l'élément 2 dans la table V sous **Emplacement**. 
4. Cliquez sur la flèche Suivant pour continuer.
5. Sur la page Serveurs DNS et connectivité VPN :
- Sous **Serveurs DNS**, configurez un nom convivial et une adresse IP pour vos serveurs DNS locaux pour chaque entrée dans la table D.
- Sous **Connectivité de site à site**, sélectionnez **Configuration d'un VPN de site à site**.
- Si vous avez déjà configuré un réseau local et que vous souhaitez l'utiliser, sélectionnez son nom sous **Réseau Local**. Si vous souhaitez créer un réseau local, sélectionnez **Spécifier un nouveau réseau Local** sous **Réseau Local**.
- Si vous n'avez pas déjà configuré un réseau local pour votre abonnement, vous ne verrez pas le champ **Réseau Local**. 
6. Cliquez sur la flèche Suivant pour continuer.
7. Sur la page Connectivité de Site à Site (si vous avez sélectionné **Spécifier un nouveau réseau Local** à l'étape 5) :
- Entrez le nom de l'élément 3 dans la table V (le nom de réseau local) sous **Nom**.
- Entrez l'adresse de l'élément 4 dans la table V sous **Adresse IP du périphérique VPN**.
- Sous **Espace d'adressage**, entrez les espaces d'adressage IP du réseau de votre organisation en termes de préfixe (sous **IP de départ**) et de longueur de préfixe (sous **CIDR (nombre d'adresses)**) pour chaque entrée dans la table L.
8. Cliquez sur la flèche Suivant pour continuer.
9. Sur la page Espace d'adresses du réseau virtuel :
- Sous **Espace d'adressage**, entrez l'espace d'adressage IP privé du réseau virtuel dans l'élément 5 de la table V en termes de préfixe (sous **IP de départ**) et de longueur de préfixe (sous **CIDR (nombre d'adresses)**).
- Sous **Sous-réseaux**, pour chaque entrée dans la table S :
	- Entrez le nom du sous-réseau dans la colonne **Sous-réseaux** en remplaçant le nom par défaut si nécessaire.
	- Entrez l'espaces d'adressage IP privé du sous-réseau en termes de préfixe (sous **IP de départ**) et de longueur de préfixe (sous **CIDR (nombre d'adresses)**).
- Cliquez sur **Ajouter un sous-réseau de passerelle**.
10. Cliquez sur la coche pour terminer la configuration.

## Ressources supplémentaires

[Présentation du réseau virtuel](https://msdn.microsoft.com/library/jj156007.aspx).

[Tâches de configuration du réseau virtuel](https://msdn.microsoft.com/library/jj156206.aspx)

[Configuration d'une connexion de site à site intersite sur un réseau virtuel Azure](https://msdn.microsoft.com/library/dn133795.aspx)

<!--HONumber=54--> 