
<properties 
	pageTitle="Utilisation de l’éditeur de règles de synchronisation Azure AD Connect" 
	description="Découvrez comment utiliser l’éditeur de règles de synchronisation Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Éditeur de règles de synchronisation Azure AD Connect


## Utilisation de l’éditeur de règles de synchronisation

Dans Azure AD Connect, vous pouvez configurer et optimiser le flux des objets et des attributs entre Azure AD et vos annuaires locaux en configurant des règles de synchronisation.

Une règle de synchronisation est un objet de configuration avec un jeu d’attributs qui circule quand une condition est remplie. Elle sert aussi à décrire la relation entre un objet dans un espace de connecteur et un objet dans le métaverse, relation appelée jointure ou correspondance. Les règles de synchronisation ont une priorité qui indique leurs relations les unes par rapport aux autres. Une règle de synchronisation avec une valeur de priorité plus basse possède une priorité plus élevée et, en cas de conflit de flux d’attributs, c’est la priorité la plus élevée qui l’emporte. Les règles de synchronisation peuvent être configurées à l’aide de l’éditeur de règles de synchronisation.

En guise d’exemple, nous allons examiner la règle de synchronisation « Entrant depuis AD – Utilisateur AccountEnabled ». Nous allons marquer cette ligne dans l’éditeur de règles de synchronisation et sélectionner Modifier. Une règle de synchronisation comporte quatre sections de configuration : Description, Filtre d’étendue, Règles de jointure et Transformations.

### Description
La première section fournit des informations de base telles que le nom et la description.

<center>![Join Rule](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync1.png) </center>

Y figurent également des informations concernant le système connecté associé à cette règle, le type d’objet dans le système connecté auquel il s’applique et le type d’objet de métaverse. Le type d’objet du métaverse est toujours « person », que le type d’objet source soit un utilisateur, iNetOrgPerson ou un contact. Comme il ne doit jamais changer, il est créé en tant que type générique. Le type de lien peut être Join, StickyJoin ou Provision. Ce paramètre fonctionne avec la section Règles de jointure et il est traité plus loin dans ce document.

### Filtre d’étendue

La section Filtre d’étendue sert à configurer à quel moment une règle de synchronisation doit s’appliquer. Comme le nom de la règle de synchronisation que nous examinons indique qu’elle ne doit être appliquée qu’aux utilisateurs activés, l’étendue est configurée pour que l’attribut AD userAccountControl ne doive pas avoir le bit 2 défini. Quand nous trouvons un utilisateur dans AD, nous appliquons cette règle si userAccountControl prend la valeur décimale 512 (utilisateur normal activé), mais nous ne l’appliquons pas si l’attribut userAccountControl de l’utilisateur que nous trouvons a la valeur 514 (utilisateur normal désactivé).

<center>![Join Rule](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync2.png) </center>

Le filtre d’étendue possède des groupes et des clauses qui peuvent être imbriqués. Toutes les clauses à l’intérieur d’un groupe doivent être satisfaites pour qu’une règle de synchronisation s’applique. Quand plusieurs groupes sont définis, au moins l’un d’entre eux doit être satisfait pour que la règle s’applique. Autrement dit, une opération OR logique est évaluée entre les groupes et une opération AND logique est évaluée à l’intérieur d’un groupe. On trouve un exemple dans la règle de synchronisation sortante Sortant vers AAD – Group Join indiquée ci-dessous. Il existe deux groupes de filtres de synchronisation, un pour les groupes de sécurité (securityEnabled ÉGALE True) et un autre pour les groupes de distribution (securityEnabled ÉGALE False).

<center>![Join Rule](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync3.png) </center>

Cette règle sert à définir les groupes qui doivent être approvisionnés dans AAD. Les groupes de distribution doivent être activés pour la messagerie électronique pour pouvoir être synchronisés avec AAD, mais pour les groupes de sécurité cela n’est pas nécessaire. Comme on peut le voir, de nombreux autres attributs supplémentaires sont également évalués.

###Règles de jointure
La troisième section sert à configurer la relation entre les objets dans l’espace de connecteur et les objets dans le métaverse. La règle que nous avons examinée plus haut n’ayant aucune configuration pour Join Rules, nous allons plutôt examiner Entrant depuis AD – User Join.

<center>![Join Rule](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync4.png) </center>

Le contenu des règles de jointure dépend de l’option de correspondance sélectionnée dans l’Assistant Installation. Pour une règle entrante, l’évaluation commence avec un objet dans l’espace de connecteur source et chaque groupe dans les règles de jointure est évalué de manière séquentielle. Si un objet source évalué correspond exactement à un objet dans le métaverse selon l’une des règles de jointure, les deux objets sont joints. Si toutes les règles ont été évaluées et qu’il n’existe aucune correspondance, le type de lien indiqué dans la page de description est utilisé. Si ce paramètre a la valeur Provision, un nouvel objet est créé dans la cible, le métaverse. L’approvisionnement d’un nouvel objet dans le métaverse porte également le nom de projection. Les règles de jointure ne sont évaluées qu’une seule fois. Quand un objet d’espace de connecteur et un objet de métaverse sont joints, ils le restent tant que l’étendue de la règle de synchronisation est encore satisfaite. Lors de l’évaluation de plusieurs règles de synchronisation, une seule d’entre elles avec des règles de jointure définies doit être dans l’étendue. Si plusieurs règles de synchronisation avec des règles de jointure sont détectées pour un objet, une erreur est levée. Pour cette raison, la meilleure pratique consiste à n’avoir qu’une seule règle de synchronisation avec jointure définie quand plusieurs règles de synchronisation sont dans l’étendue pour un objet. Dans la configuration par défaut d’Azure AD Connect, vous pouvez identifier facilement ces règles, car leur nom se termine par le mot « Join ». Une règle de synchronisation sans aucune règle de jointure définie applique les flux d’attributs si une autre règle de synchronisation a joint les objets ensemble ou a approvisionné un nouvel objet dans la cible.

###Transformations
La section Transformation définit tous les flux d’attributs qui s’appliquent à l’objet cible quand les objets sont joints et quand le filtre d’étendue est satisfait. Si l’on revient à notre règle de synchronisation Entrant depuis AD – Utilisateur AccountEnabled, on trouve les transformations suivantes :

<center>![Join Rule](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync5.png) </center>

Pour mettre tout cela en contexte, dans un déploiement de forêt Account-Resource, nous nous attendons à trouver un compte activé dans la forêt de comptes et un compte désactivé dans la forêt de ressources avec des paramètres Exchange et Lync. La règle de synchronisation que nous examinons contient les attributs nécessaires pour la connexion et nous souhaitons que ceux-ci circulent à partir de la forêt dans laquelle nous avons trouvé un compte activé. Tous ces flux d’attributs sont regroupés dans une règle de synchronisation. Une transformation peut avoir différents types : Constant, Direct et Expression. Un flux constant transmet toujours une valeur particulière. Dans l’exemple ci-dessus, nous affecterons toujours la valeur True à l’attribut de métaverse nommé accountEnabled. Un flux direct transmet la valeur de l’attribut source vers l’attribut cible. Le troisième type de flux, Expression, permet d’effectuer des configurations avancées. Le langage d’expression étant VBA (Visual Basic pour Applications), un utilisateur ayant une expérience de Microsoft Office ou VBScript reconnaîtra le format. Les attributs sont placés entre crochets : [nom_attribut]. Les noms des attributs et des fonctions respectent la casse, mais l’Éditeur de règles de synchronisation évalue les expressions et affiche un avertissement si elles ne sont pas valides. Toutes les expressions sont placées sur une seule ligne avec les fonctions imbriquées. Pour illustrer toute la puissance du langage de configuration, voici le flux pour pwdLastSet, mais avec des commentaires supplémentaires insérés :

		// If-then-else
		IIF(
		// (The evaluation for IIF) Is the attribute pwdLastSet present in AD? 
		IsPresent([pwdLastSet]),
		// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by AAD, and finally convert it to a string.
		CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
		// (The False part of IIF) Nothing to contribute
		NULL
		)

Les transformations couvrent de nombreux aspects et constituent une large partie de la configuration personnalisée possible avec Azure AD Connect. La configuration personnalisée n’est pas traitée dans ce document de vue d’ensemble, mais nous examinerons certains flux d’attributs supplémentaires plus loin dans ce document.
 

<!---HONumber=July15_HO3-->