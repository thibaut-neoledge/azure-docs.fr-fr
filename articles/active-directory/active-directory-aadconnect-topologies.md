<properties
    pageTitle="Azure AD Connect : topologies prises en charge | Microsoft Azure"
    description="Cette rubrique détaille les topologies prises en charge et celles qui ne le sont pas pour Azure AD Connect"
    services="active-directory"
    documentationCenter=""
    authors="AndKjell"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
	ms.topic="article"
    ms.date="06/27/2016"
    ms.author="andkjell"/>

# Topologies pour Azure AD Connect

L’objectif de cette rubrique est de décrire différentes topologies locales et Azure AD avec Azure AD Connect comme solution d’intégration principale. Elle décrit les configurations prises en charge et celles qui ne le sont pas.

Légende des images dans le document :

Description | Icône
-----|-----
Forêt Active Directory locale| ![AD](./media/active-directory-aadconnect-topologies/LegendAD1.png)
Active Directory avec importation filtrée| ![AD](./media/active-directory-aadconnect-topologies/LegendAD2.png)
Serveur Azure AD Connect Sync| ![Synchronisation](./media/active-directory-aadconnect-topologies/LegendSync1.png)
« Mode intermédiaire » du serveur Azure AD Connect Sync| ![Synchronisation](./media/active-directory-aadconnect-topologies/LegendSync2.png)
GALSync avec FIM2010 ou MIM2016| ![Synchronisation](./media/active-directory-aadconnect-topologies/LegendSync3.png)
Serveur Azure AD Connect Sync, détaillé| ![Synchronisation](./media/active-directory-aadconnect-topologies/LegendSync4.png)
Annuaire Azure AD |![AAD](./media/active-directory-aadconnect-topologies/LegendAAD.png)
Scénario non pris en charge | ![Non pris en charge](./media/active-directory-aadconnect-topologies/LegendUnsupported.png)


## Une seule forêt, un seul annuaire Azure AD
![Une seule forêt, un seul annuaire](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

La topologie la plus courante est une seule forêt locale, avec un ou plusieurs domaines, et un seul annuaire Azure AD (également appelé locataire). L’authentification Azure AD est effectuée avec la synchronisation de mot de passe. Il s’agit de la seule topologie prise en charge par l’installation rapide d’Azure AD Connect.

### Forêt unique, plusieurs serveurs de synchronisation connectés à un annuaire Azure AD
![Une seule forêt filtrée non prise en charge](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

La connexion de plusieurs serveurs Azure AD Connect Sync à un même annuaire Azure AD n’est pas prise en charge (à l’exception d’un [serveur intermédiaire](#staging-server)). Elle n’est pas prise en charge, même si ces derniers sont configurés pour synchroniser un ensemble d’objets mutuellement exclusifs. Vous l’avez peut-être considéré si vous ne parvenez pas à atteindre l’ensemble des domaines d’une forêt à partir d’un seul serveur ou à répartir une charge entre plusieurs serveurs.

## Plusieurs forêts, un seul annuaire Azure AD
![Plusieurs forêts, un seul annuaire](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

De nombreuses organisations possèdent des environnements comportant plusieurs forêts Active Directory locales. Il existe plusieurs raisons de déployer plus d’une forêt Active Directory locale. Par exemple : des modèles avec des forêts de ressources de comptes et comme conséquence suite à une fusion ou une acquisition.

Quand vous avez plusieurs forêts, toutes les forêts doivent être accessibles par un même serveur Azure AD Connect Sync. Vous n’êtes pas obligé de joindre le serveur à un domaine. Le serveur peut être placé dans une zone DMZ du réseau si nécessaire pour atteindre toutes les forêts.

L’Assistant d’installation d’Azure AD Connect offre plusieurs options différentes pour consolider les utilisateurs représentés dans plusieurs forêts. L’objectif est de représenter un utilisateur une seule fois dans Azure AD. Il existe certaines topologies courantes que vous pouvez configurer dans le chemin d’installation personnalisé de l’Assistant Installation. Sélectionnez l’option correspondante qui représente votre topologie sur la page **Identification unique de vos utilisateurs**. La consolidation est uniquement configurée pour les utilisateurs. Les groupes en doublon ne sont pas consolidés avec la configuration par défaut.

Des topologies courantes sont présentées dans la section suivante : [Topologies distinctes](#multiple-forests-separate-topologies), [Maillage complet](#multiple-forests-full-mesh-with-optional-galsync) et [Comptes-ressources](#multiple-forests-account-resource-forest).

La configuration par défaut d’Azure AD Connect Sync suppose que :

1. les utilisateurs n’ont qu’un seul compte activé et la forêt où se trouve ce compte est utilisée pour authentifier l’utilisateur. Cette supposition comprend la synchronisation des mots de passe ainsi que la fédération. UserPrincipalName et sourceAnchor/immutableID proviennent de cette forêt.
2. les utilisateurs n’ont qu’une seule boîte aux lettres ;
3. la forêt qui héberge la boîte aux lettres d’un utilisateur a la meilleure qualité de données pour les attributs visibles dans la liste d’adresses globale Exchange. Si aucune boîte aux lettres n’est associée à l’utilisateur, n’importe quelle forêt peut être utilisée pour fournir ces valeurs d’attributs.
4. Si vous avez une boîte aux lettres liée, il existe également un autre compte dans une forêt différente de celle utilisée pour la connexion.

Si votre environnement ne correspond pas à ces suppositions, voici ce qui se produit :

- Si vous avez plusieurs comptes actifs ou plusieurs boîtes aux lettres, le moteur de synchronisation en choisit un et ignore les autres.
- Une boîte aux lettres liée sans aucun autre compte actif n’est pas exportée vers Azure AD. Le compte d’utilisateur n’est pas représenté en tant que membre d’un groupe quelconque. Dans DirSync, une boîte aux lettres liée est représentée comme une boîte aux lettres normale : il s’agit donc d’un comportement intentionnellement différent pour mieux prendre en charge les scénarios avec plusieurs forêts.

Pour plus de détails, consultez [Présentation de la configuration par défaut](active-directory-aadconnectsync-understanding-default-configuration.md).

### Plusieurs forêts, plusieurs serveurs de synchronisation connectés à un annuaire Azure AD
![Plusieurs forêts, plusieurs serveurs de synchronisation non pris en charge](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

La connexion de plusieurs serveurs Azure AD Connect Sync à un même annuaire Azure AD n’est pas prise en charge. L’exception est l’utilisation d’un [serveur intermédiaire](#staging-server).

### Plusieurs forêts – topologies distinctes
**Les utilisateurs ne sont représentés qu’une seule fois à travers tous les annuaires**

![Plusieurs forêts, utilisateurs représentés une fois](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Plusieurs forêts, topologies séparées](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

Dans cet environnement, toutes les forêts locales sont traitées comme des entités distinctes et aucun utilisateur n’est présent dans une autre forêt. Chaque forêt a sa propre organisation Exchange et il n’existe pas de GALSync entre les forêts. Cette topologie peut se présenter suite à une fusion/acquisition ou dans une organisation où chaque division fonctionne de manière isolée. Dans Azure AD, ces forêts sont dans la même organisation et s’affichent avec une liste d’adresses globale unifiée. Dans cette image, chaque objet de chaque forêt est représenté une seule fois dans le métaverse et agrégé dans l’annuaire Azure AD cible.

### Plusieurs forêts – utilisateurs en correspondance
**Les identités utilisateurs existent sur plusieurs annuaires**

Dans chaque scénario figurent des groupes de distribution et de sécurité, qui peuvent contenir une combinaison d’utilisateurs, de contacts et d’entités de sécurité externes.

Les entités de sécurité externes sont utilisées dans ADDS pour représenter des membres d’autres forêts dans un groupe de sécurité. Toutes les entités de sécurité externes sont résolues en leur objet réel dans Azure AD.

### Plusieurs forêts – maillage complet avec GALSync facultative
**Les identités utilisateurs existent sur plusieurs annuaires. Correspondance à l’aide de : attribut de messagerie**

![Plusieurs forêts, utilisateurs avec boîte aux lettres](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Plusieurs forêts, maillage complet](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Une topologie à maillage complet permet aux utilisateurs et aux ressources de se trouver dans n’importe quelle forêt. En général, il existe des approbations bidirectionnelles entre les forêts.

Si Exchange est présent dans plusieurs forêts, il peut éventuellement y avoir une solution GALSync locale. Chaque utilisateur est représenté en tant que contact dans toutes les autres forêts GALSync est généralement implémenté à l’aide de Forefront Identity Manager 2010 ou Microsoft Identity Manager 2016. Azure AD Connect ne peut pas être utilisé pour une GALSync locale.

Dans ce scénario, les objets d’identité sont joints à l’aide de l’attribut de messagerie. Un utilisateur ayant une boîte aux lettres dans une forêt est joint aux contacts dans les autres forêts.

### Plusieurs forêts – forêt comptes-ressources
**Les identités utilisateurs existent sur plusieurs annuaires. Correspond à l’aide de : attributs ObjectSID et msExchMasterAccountSID**

![Plusieurs forêts, correspondance des utilisateurs avec ObjectSID](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![Plusieurs forêts, AccountResource](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

Dans une topologie de forêt comptes-ressources, vous avez une ou plusieurs forêts de comptes avec des comptes d’utilisateurs actifs. Une ou plusieurs forêts de ressources comporteront des comptes désactivés.

Dans ce scénario, une (ou plusieurs) **forêt de ressources** approuve toutes les **forêts de comptes**. Cette forêt de ressources a généralement un schéma Active Directory étendu avec Exchange et Lync. Tous les services Exchange et Lync, ainsi que d’autres services partagés, sont situés dans cette forêt. Les utilisateurs ont un compte d’utilisateur désactivé dans cette forêt et la boîte aux lettres est liée à la forêt de comptes.

## Considérations sur Office 365 et la topologie
Certaines charges de travail Office 365 ont certaines restrictions quant aux topologies prises en charge. Si vous souhaitez utiliser l’une d’elles, consultez la rubrique sur les topologies prises en charge pour la charge de travail.

Charge de travail |  
--------- | ---------
Exchange Online | S’il existe plusieurs organisations Exchange locales (c’est-à-dire si Exchange a été déployé sur plusieurs forêts), vous devez utiliser Exchange 2013 SP1 ou ultérieur. Vous pouvez trouver des détails ici : [Déploiements hybrides avec plusieurs forêts Active Directory](https://technet.microsoft.com/library/jj873754.aspx)
Skype Entreprise | Lors de l’utilisation de plusieurs forêts locales, seule la topologie de forêt comptes-ressources est prise en charge. Vous pouvez trouver des détails sur les topologies prises en charge ici : [Configuration requise de l’environnement pour Skype Entreprise Server 2015](https://technet.microsoft.com/library/dn933910.aspx)

## Serveur intermédiaire
![Serveur intermédiaire](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect prend en charge l’installation d’un second serveur en **mode intermédiaire**. Un serveur dans ce mode lit les données de tous les annuaires connectés, sans rien écrire. Il utilise le cycle de synchronisation normale et possède donc une copie des données d’identité à jour. En cas d’échec du serveur principal (lors d’un sinistre), vous pouvez basculer sur le serveur intermédiaire. Pour cela, utilisez l’Assistant Azure AD Connect. Ce second serveur peut de préférence se trouver dans un autre centre de données, car aucune infrastructure n’est partagée avec le serveur principal. Toute modification de configuration apportée au serveur principal doit être copiée manuellement sur le second serveur.

Un serveur intermédiaire peut également être utilisé pour tester une nouvelle configuration personnalisée et l’effet qu’elle aura sur vos données. Vous pouvez prévisualiser les modifications et ajuster la configuration. Quand vous êtes satisfait de la nouvelle configuration, vous pouvez faire du serveur intermédiaire le serveur actif et placer l’ancien serveur actif en mode intermédiaire.

Cette méthode peut également être utilisée pour remplacer le serveur de synchronisation active. Préparez le nouveau serveur et configurez-le en mode intermédiaire. Assurez-vous qu’il est en bon état, désactivez le mode intermédiaire (rendant ainsi le serveur actif), puis arrêtez le serveur actif actuel.

Il est possible d’avoir plusieurs serveurs intermédiaires si vous voulez disposer de plusieurs sauvegardes dans différents centres de données.

## Plusieurs annuaires Azure AD
Microsoft recommande d’avoir un seul annuaire dans Azure AD pour une organisation. Avant d’envisager d’utiliser plusieurs annuaires Azure AD, lisez ces rubriques qui couvrent des scénarios courants et qui vous permettront de n’utiliser qu’un seul annuaire.

Rubrique |  
--------- | ---------
Délégation à l’aide d’unités administratives | [Gestion des unités administratives dans Azure AD](active-directory-administrative-units-management.md)

![Plusieurs forêts, plusieurs annuaires](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Il existe une relation 1:1 entre un serveur Azure AD Connect Sync et un annuaire Azure AD. Pour chaque annuaire Azure AD, vous avez besoin d’une installation d’un serveur Azure AD Connect Sync. Les instances d’annuaire Azure AD sont isolées de par leur conception et les utilisateurs qui sont dans un annuaire ne peuvent pas voir les utilisateurs d’un autre annuaire. Si cette séparation est souhaitée, il s’agit d’une configuration prise en charge, mais dans le cas contraire, vous devez utiliser les modèles d’annuaire unique Azure AD.

### Chaque objet une seule fois dans un annuaire Azure AD
![Une seule forêt filtrée](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

Dans cette topologie, un seul serveur Azure AD Connect Sync est connecté à chaque annuaire Azure AD. Les serveurs de synchronisation Azure AD Connect doivent être configurés pour le filtrage et ils ont donc chacun un ensemble d’objets mutuellement exclusifs. Vous pouvez, par exemple, délimiter l’étendue de chaque serveur à un domaine ou à une unité d’organisation spécifique. Un domaine DNS peut uniquement être inscrit dans un seul annuaire Azure AD. L’UPN (nom d’utilisateur principal) des utilisateurs dans l’AD local doit également être composé d’espaces de noms distincts. Par exemple, dans l’image ci-dessus, trois suffixes d’UPN distincts sont inscrits dans Active Directory local : contoso.com, fabrikam.com et wingtiptoys.com. Les utilisateurs de chaque domaine Active Directory local utilisent un espace de noms différent.

Il n’existe aucune solution GALsync entre les instances d’annuaire Azure AD. Le carnet d’adresses dans Exchange Online et Skype Entreprise affiche uniquement les utilisateurs du même annuaire.

Cette topologie comprend les restrictions suivantes pour les scénarios sinon pris en charge :

- Seul l’un des annuaires Azure AD peut activer Exchange hybride avec l’Active Directory local.
- Les appareils Windows 10 ne peuvent être associés uniquement à un annuaire Azure AD.

La condition requise d’un ensemble d’objets mutuellement exclusifs s’applique également à l’écriture différée. Certaines fonctionnalités d’écriture différée ne sont pas prises en charge avec cette topologie, car elles supposent une seule configuration locale :

-	Écriture différée des groupes avec la configuration par défaut
-	Écriture différée des appareils

### Chaque objet plusieurs fois dans un annuaire Azure AD
![Une seule forêt, plusieurs annuaires non pris en charge](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Une seule forêt, plusieurs connecteurs non pris en charge](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

- La synchronisation d’un même utilisateur vers plusieurs annuaires Azure AD n’est pas prise en charge.
- La modification d’une configuration pour faire en sorte que les utilisateurs dans un annuaire Azure AD apparaissent comme contacts dans un autre annuaire Azure AD n’est pas prise en charge.
- La modification d’Azure AD Connect Sync pour qu’il se connecte à plusieurs annuaires Azure AD n’est pas prise en charge.

### GALsync à l’aide de l’écriture différée
![Plusieurs forêts, plusieurs annuaires, GALSync1, Non pris en charge](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Plusieurs forêts, plusieurs annuaires, GALSync2, Non pris en charge](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Les annuaires Azure AD sont isolés par conception.

- La modification de la configuration d’Azure AD Connect Sync pour lire des données à partir d’un autre annuaire Azure AD n’est pas prise en charge.
- L’exportation d’utilisateurs comme contacts vers un autre annuaire Active Directory local avec Azure AD Connect Sync n’est pas prise en charge.

### GALsync avec le serveur de synchronisation local
![Plusieurs forêts, plusieurs annuaires, GALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

L’utilisation de FIM2010/MIM2016 local pour effectuer une synchronisation des utilisateurs de GALsync entre deux organisations Exchange est prise en charge. Les utilisateurs d’une organisation apparaissent alors comme des utilisateurs/contacts externes dans l’autre organisation. Ces différents annuaires AD locaux peuvent ensuite être synchronisés vers leurs propres annuaires Azure AD.

## Étapes suivantes
Pour savoir comment installer Azure AD Connect pour ces scénarios, consultez [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

En savoir plus sur la configuration d’[Azure AD Connect sync](active-directory-aadconnectsync-whatis.md).

En savoir plus sur l’[intégration de vos identités locales à Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0928_2016-->