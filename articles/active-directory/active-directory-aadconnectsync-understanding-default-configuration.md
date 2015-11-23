<properties
   pageTitle="Synchronisation Azure AD Connect : présentation de la configuration par défaut | Microsoft Azure"
   description="Cet article décrit la configuration par défaut dans la synchronisation Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="11/10/2015"
   ms.author="andkjell"/>

# Présentation de la configuration par défaut

Cet article vous guide tout au long de la configuration par défaut de la synchronisation Azure AD Connect. L’objectif est que le lecteur comprenne comment fonctionne le modèle de configuration, nommé approvisionnement déclaratif, dans un exemple réel. Cet article suppose que vous avez déjà installé et configuré la synchronisation Azure AD à l’aide de l’Assistant d’installation.

## Description du scénario

Dans cet exemple, nous utilisons un déploiement comportant une forêt de comptes (A), une forêt de ressources (R) et un répertoire Azure AD.

![scénario](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

Dans cette configuration, nous supposons trouver un compte activé dans la forêt de compte et un compte désactivé dans la forêt de ressources avec une boîte aux lettres liée.

Nos objectifs avec la configuration par défaut sont les suivants :

- Les informations d’attribut liées à la connexion seront synchronisées à partir de la forêt avec le compte activé.
- Les attributs qui se trouvent dans la LAG (liste d’adresses globale) seront synchronisés à partir de la forêt avec la boîte aux lettres. Si aucune boîte aux lettres n’est trouvée, toute autre forêt sera utilisée.
- Si une boîte aux lettres liée est trouvée, le compte activé lié doit être trouvé pour l’objet à exporter vers Azure AD.

## Éditeur de règles de synchronisation

La configuration peut être affichée et modifiée avec l’outil Éditeur de règles de synchronisation (SRE), dont le raccourci se trouve dans le menu Démarrer.

![Éditeur de règles de synchronisation](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

Le SRE est un outil du kit de ressources installé avec la synchronisation Azure AD Connect. Pour être en mesure de le démarrer, vous devez être membre du groupe ADSyncAdmins. Lorsqu’il démarre, vous voyez quelque chose comme suit :

![Règles de synchronisation entrante](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

Dans ce volet, vous verrez toutes les règles de synchronisation créées pour votre configuration. Chaque ligne du tableau correspond à une règle de synchronisation. À gauche, sous Types de règles, deux types sont présentés : Entrante et Sortante Les termes entrante et sortante sont à prendre du point de vue du métaverse. Dans cette vue d’ensemble, nous allons principalement nous concentrer sur les règles de trafic entrant. La liste réelle des règles de synchronisation dépend du schéma détecté dans AD. Dans l’image ci-dessus, la forêt de compte (fabrikamonline.com) ne dispose d’aucun service (par exemple, Exchange ou Lync), et aucune règle de synchronisation n’a été créée pour ces services. Toutefois, dans la forêt de ressources (res.fabrikamonline.com), vous trouverez des règles de synchronisation pour ces services. Le contenu des règles diffère selon la version détectée. Par exemple, dans un déploiement avec Exchange 2013, plus de flux d’attributs sont configurés que dans Exchange 2010 et Exchange 2007.

## Règle de synchronisation

Une règle de synchronisation est un objet de configuration avec un jeu d’attributs qui circule quand une condition est remplie. Elle sert aussi à décrire la relation entre un objet dans un espace de connecteur et un objet dans le métaverse, relation appelée jointure ou correspondance. Les règles de synchronisation ont une valeur de précédence qui indique leurs relations les unes par rapport aux autres. Une règle de synchronisation avec une valeur de précédence plus basse possède une précédence plus élevée et, en cas de conflit de flux d’attributs, c’est la précédence la plus élevée qui l’emporte.

En guise d’exemple, nous allons examiner la règle de synchronisation **Entrant depuis AD – Utilisateur AccountEnabled**. Nous allons marquer cette ligne dans l’outil SRE et sélectionner **Modifier**.

Dans la mesure où il s’agit d’une règle par défaut, nous recevrons un avertissement à l’ouverture de la règle. Vous ne devez pas apporter de [modifications aux règles par défaut](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) vous êtes donc invité à indiquer vos intentions. Dans ce cas, vous souhaitez uniquement afficher la règle, donc sélectionnez **Non**.

![Règles de synchronisation entrante](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

Une règle de synchronisation comporte quatre sections de configuration : Description, Filtre d’étendue, Règles de jointure et Transformations.

### Description

La première section fournit des informations de base telles que le nom et la description.

![Modifier la règle de synchronisation entrante](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

Y figurent également des informations concernant le système connecté associé à cette règle, le type d’objet dans le système connecté auquel il s’applique et le type d’objet de métaverse. Le type d’objet du métaverse est toujours « person », que le type d’objet source soit un utilisateur, iNetOrgPerson ou un contact. Comme il ne doit jamais changer, il est créé en tant que type générique. Le type de lien peut être Join, StickyJoin ou Provision. Ce paramètre fonctionne avec la section Règles de jointure et il est traité plus loin dans ce document.

Vous pouvez également voir que cette règle de synchronisation est utilisée pour la synchronisation de mot de passe. Si un utilisateur est dans la portée pour cette règle de synchronisation, le mot de passe est synchronisé du local vers le cloud (en supposant que vous avez activé la fonctionnalité de synchronisation de mot de passe).

### Filtre d’étendue

La section Filtre d’étendue sert à configurer à quel moment une règle de synchronisation doit s’appliquer. Comme le nom de la règle de synchronisation que nous examinons indique qu’elle ne doit être appliquée qu’aux utilisateurs activés, l’étendue est configurée de sorte que l’attribut AD **userAccountControl** ne doive pas avoir le bit 2 défini. Quand nous trouvons un utilisateur dans AD, nous appliquons cette règle de synchronisation si **userAccountControl** prend la valeur décimale 512 (utilisateur normal activé), mais nous ne l’appliquons pas si l’attribut **userAccountControl** de l’utilisateur que nous trouvons a la valeur 514 (utilisateur normal désactivé).

![Modifier la règle de synchronisation entrante](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

Le filtre d’étendue possède des groupes et des clauses qui peuvent être imbriqués. Toutes les clauses à l’intérieur d’un groupe doivent être satisfaites pour qu’une règle de synchronisation s’applique. Quand plusieurs groupes sont définis, au moins l’un d’entre eux doit être satisfait pour que la règle s’applique. Autrement dit, une opération OR logique est évaluée entre les groupes et une opération AND logique est évaluée à l’intérieur d’un groupe. On trouve un exemple dans la règle de synchronisation sortante **Sortant vers AAD – Group Join**, indiquée ci-dessous. Il existe plusieurs groupes de filtres de synchronisation, par exemple, un pour les groupes de sécurité (securityEnabled ÉGALE True) et un autre pour les groupes de distribution (securityEnabled ÉGALE False).

![Modifier la règle de synchronisation sortante](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

Cette règle sert à définir les groupes qui doivent être approvisionnés dans AAD. Les groupes de distribution doivent être activés pour la messagerie électronique pour pouvoir être synchronisés avec AAD, mais pour les groupes de sécurité cela n’est pas nécessaire. Comme on peut le voir, de nombreux autres attributs supplémentaires sont également évalués.

### Règles de jointure

La troisième section sert à configurer la relation entre les objets dans l’espace de connecteur et les objets dans le métaverse. La règle que nous avons examinée plus haut n’ayant aucune configuration pour Join Rules, nous allons plutôt examiner **Entrant depuis AD – User Join**.

![Modifier la règle de synchronisation entrante](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

Le contenu des règles de jointure dépend de l’option de correspondance sélectionnée dans l’Assistant Installation. Pour une règle entrante, l’évaluation commence avec un objet dans l’espace de connecteur source et chaque groupe dans les règles de jointure est évalué de manière séquentielle. Si un objet source évalué correspond exactement à un objet dans le métaverse selon l’une des règles de jointure, les deux objets sont joints. Si toutes les règles ont été évaluées et qu’il n’existe aucune correspondance, le type de lien indiqué dans la page de description est utilisé. Si ce paramètre a la valeur Provision, un nouvel objet est créé dans la cible, le métaverse. L’approvisionnement d’un nouvel objet dans le métaverse porte également le nom de projection.

Les règles de jointure ne sont évaluées qu’une seule fois. Quand un objet d’espace de connecteur et un objet de métaverse sont joints, ils le restent tant que l’étendue de la règle de synchronisation est encore satisfaite.

Lors de l’évaluation de plusieurs règles de synchronisation, une seule d’entre elles avec des règles de jointure définies doit être dans l’étendue. Si plusieurs règles de synchronisation avec des règles de jointure sont détectées pour un objet, une erreur est levée. Pour cette raison, la meilleure pratique consiste à n’avoir qu’une seule règle de synchronisation avec jointure définie quand plusieurs règles de synchronisation sont dans l’étendue pour un objet. Dans la configuration par défaut pour la synchronisation Azure Active Directory, vous pouvez identifier facilement ces règles, car leur nom se termine par le mot « Join ». Une règle de synchronisation sans aucune règle de jointure définie applique les flux d’attributs si une autre règle de synchronisation a joint les objets ensemble ou a approvisionné un nouvel objet dans la cible.

Si vous examinez l’illustration ci-dessus, vous pouvez voir que la règle essaie de joindre **objectSID** avec **msExchMasterAccountSid** (Exchange) et **msRTCSIP-OriginatorSid** (Lync), résultat que nous attendons dans une topologie de forêt de comptes/ressources. Nous constatons la même règle sur toutes les forêts, c’est-à-dire l’hypothèse que chaque forêt peut être une forêt de comptes ou de ressources. Cela fonctionne également si vous disposez de comptes qui résident dans une forêt unique et n’ont pas à être joints.

### Transformations

La section Transformation définit tous les flux d’attributs qui s’appliquent à l’objet cible quand les objets sont joints et quand le filtre d’étendue est satisfait. Si l’on revient à notre règle de synchronisation **Entrant depuis AD – Utilisateur AccountEnabled**, on trouve les transformations suivantes :

![Modifier la règle de synchronisation entrante](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

Pour mettre tout cela en contexte, dans un déploiement de forêt Account-Resource, nous nous attendons à trouver un compte activé dans la forêt de comptes et un compte désactivé dans la forêt de ressources avec des paramètres Exchange et Lync. La règle de synchronisation que nous examinons contient les attributs nécessaires pour la connexion et nous souhaitons que ceux-ci circulent à partir de la forêt dans laquelle nous avons trouvé un compte activé. Tous ces flux d’attributs sont regroupés dans une règle de synchronisation.

Une transformation peut avoir différents types : Constant, Direct et Expression.

- Un flux constant transmet toujours une valeur particulière. Dans l’exemple ci-dessus, nous affecterons toujours la valeur True à l’attribut de métaverse nommé accountEnabled.
- Un flux direct transmet la valeur de l’attribut source vers l’attribut cible.
- Le troisième type de flux, Expression, permet d’effectuer des configurations avancées.

Le langage d’expression étant VBA (Visual Basic pour Applications), un utilisateur ayant une expérience de Microsoft Office ou VBScript reconnaîtra le format. Les attributs sont placés entre crochets : [nom\_attribut]. Les noms des attributs et des fonctions respectent la casse, mais l’Éditeur de règles de synchronisation évalue les expressions et affiche un avertissement si elles ne sont pas valides. Toutes les expressions sont placées sur une seule ligne avec les fonctions imbriquées. Pour illustrer toute la puissance du langage de configuration, voici le flux pour pwdLastSet, mais avec des commentaires supplémentaires insérés :

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by AAD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Les transformations couvrent de nombreux aspects et constituent une large partie de la configuration personnalisée possible avec la synchronisation Azure AD Connect. Des informations supplémentaires sont disponibles dans les autres articles relatifs à la synchronisation Azure AD Connect.

## Précédence

Nous avons maintenant vu certaines règles de synchronisation individuelles, mais les règles fonctionnent de concert dans la configuration. Dans certains cas, une valeur d’attribut est partagée à partir de plusieurs règles de synchronisation vers le même attribut cible. Dans ce cas, la précédence d’attribut est utilisée pour déterminer quel attribut l’emportera. Par exemple, examinons l’attribut sourceAnchor. Cet attribut est un important pour pouvoir se connecter à Azure AD. Nous pouvons trouver un flux d’attributs pour cet attribut dans deux règles de synchronisation différentes : **Entrant depuis AD – Utilisateur AccountEnabled** et **Entrant depuis AD – Utilisateur Common**. En raison de la précédence de la règle de synchronisation, l’attribut sourceAnchor est partagé à partir de la forêt avec un compte activé en premier lieu s’il existe plusieurs objets joints à l’objet métaverse. S’il n’y a aucun compte activé, nous allons utiliser la règle de synchronisation fourre-tout **Entrant depuis AD – Utilisateur Common**. Cela garantit que même pour les comptes qui sont désactivés, nous fournirons toujours un sourceAnchor.

![Règles de synchronisation entrante](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

La précédence des règles de synchronisation est définie dans les groupes par l’Assistant d’installation. Les règles d’un groupe ont toutes le même nom, mais elles sont connectées à différents répertoires connectés. L’Assistant d’installation donnera à la règle **Entrant depuis AD – User Join** la précédence la plus élevée et effectuera une itération sur tous les répertoires AD connectés. Il continuera ensuite avec les groupes de règles suivants dans un ordre prédéfini. À l’intérieur d’un groupe, les règles sont ajoutées dans l’ordre dans lequel les connecteurs ont été ajoutés à l’Assistant. Si un autre connecteur est ajouté via l’Assistant, les règles de synchronisation seront réordonnées et les règles du nouveau connecteur seront insérées en dernier dans chaque groupe.

## Exemple complet

Nous en savons maintenant assez sur les règles de synchronisation pour comprendre le fonctionnement de la configuration avec les différentes règles de synchronisation. Si nous regardons un utilisateur et les attributs qui sont partagés avec métaverse, les règles sont appliquées dans l’ordre suivant :

| Nom | Commentaire |
| :------------- | :------------- |
| Entrant depuis AD – User Join | Règle pour joindre les objets de l’espace de connecteur avec métaverse. |
| Entrant depuis AD – Utilisateur AccountEnabled | Attributs requis pour la connexion à Azure AD et Office 365. Nous voulons ces attributs à partir du compte activé. |
| Entrant depuis AD – Utilisateur Common à partir d’Exchange | Attributs trouvés dans la liste d’adresses globale. Nous supposons que la qualité des données est meilleure dans la forêt où nous avons trouvé la boîte aux lettres de l’utilisateur. |
| Entrant depuis AD – Utilisateur Common | Attributs trouvés dans la liste d’adresses globale. Dans le cas où nous ne trouvons pas de boîte aux lettres, tout autre objet joint peut contribuer à la valeur d’attribut. |
| Entrant depuis AD – Utilisateur Exchange | Existe seulement si Exchange a été détecté. Transfère tous les attributs Exchange d’infrastructure. |
| Entrant depuis AD – Utilisateur Lync | Existe seulement si Lync a été détecté. Transfère tous les attributs Lync d’infrastructure. |

## Ressources supplémentaires

* [Azure AD Connect Sync : personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration des identités locales dans Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=Nov15_HO3-->