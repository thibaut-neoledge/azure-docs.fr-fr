<properties
   pageTitle="Azure AD Connect : topologies prises en charge | Microsoft Azure"
   description="Cette rubrique détaille les topologies prises en charge et celles qui ne le sont pas pour Azure AD Connect"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/22/2016"
   ms.author="andkjell"/>

# Topologies pour Azure AD Connect

L’objectif de cette rubrique est de décrire différentes topologies locales et Azure AD avec Azure AD Connect comme solution d’intégration principale. Elle décrit les configurations prises en charge et celles qui ne le sont pas.

Légende des images dans le document :

| Description | Icône |
|-----|-----|
| Forêt Active Directory locale | ![AD](./media/active-directory-aadconnect-topologies/LegendAD1.png)|
| Active Directory avec importation filtrée | ![AD](./media/active-directory-aadconnect-topologies/LegendAD2.png)|
| Serveur Azure AD Connect Sync | ![Synchronisation](./media/active-directory-aadconnect-topologies/LegendSync1.png)|
| « Mode intermédiaire » du serveur Azure AD Connect Sync | ![Synchronisation](./media/active-directory-aadconnect-topologies/LegendSync2.png)|
| GALSync avec FIM2010 ou MIM2016 | ![Synchronisation](./media/active-directory-aadconnect-topologies/LegendSync3.png)|
| Serveur Azure AD Connect Sync, détaillé |![Synchronisation](./media/active-directory-aadconnect-topologies/LegendSync4.png)|
| Annuaire Azure AD |![AAD](./media/active-directory-aadconnect-topologies/LegendAAD.png)|
| Scénario non pris en charge | ![Non pris en charge](./media/active-directory-aadconnect-topologies/LegendUnsupported.png)



## Une seule forêt, un seul annuaire Azure AD
![Une seule forêt, un seul annuaire](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

La topologie la plus courante est une seule forêt locale, avec un ou plusieurs domaines, et un seul annuaire Azure AD (également appelé locataire). L’authentification Azure AD est effectuée avec la synchronisation de mot de passe. Il s’agit de la topologie prise en charge par l’installation rapide d’Azure AD Connect.

### Forêt unique, plusieurs serveurs de synchronisation connectés à un annuaire Azure AD
![Une seule forêt filtrée – Non pris en charge](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

La connexion de plusieurs serveurs Azure AD Connect au même annuaire Azure Active Directory n’est pas prise en charge, même s’ils sont configurés pour synchroniser un ensemble d’objets mutuellement exclusifs (à l’exception d’un [serveur intermédiaire](#staging-server)). Ceci pourrait être tenté dans le cas où un domaine dans une forêt n’est pas accessible depuis un emplacement réseau commun ou dans une tentative de répartir la charge de la synchronisation sur plusieurs serveurs.

## Plusieurs forêts, un seul annuaire Azure AD
![Plusieurs forêts, un seul annuaire](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

De nombreuses organisations possèdent des environnements comportant plusieurs forêts Active Directory locales. Il existe plusieurs raisons de déployer plus d’une forêt Active Directory locale, par exemple quand il s’agit de forêts de ressources de comptes, de forêts liées à des fusions et acquisitions ou de forêts utilisées pour externaliser des données.

Quand vous avez plusieurs forêts, toutes les forêts doivent être accessibles par un même serveur Azure AD Connect Sync. Le serveur ne doit pas être joint à un domaine et il peut être placé si nécessaire dans une zone DMZ du réseau pour pouvoir atteindre toutes les forêts.

L’Assistant Azure AD Connect propose plusieurs options pour la consolidation des utilisateurs : ainsi, même si le même utilisateur est représenté plusieurs fois dans des forêts différentes, il n’est représenté qu’une seule fois dans Azure AD. Certaines topologies courantes sont décrites ci-dessous. Vous configurez la topologie dont vous disposez en utilisant le chemin d’installation personnalisé dans l’Assistant Installation et vous sélectionnez l’option correspondante dans la page « Identification de vos utilisateurs uniquement ». La consolidation se produit uniquement pour les utilisateurs. Si des groupes sont en doublon, ceux-ci ne sont pas consolidés avec la configuration par défaut.

Des topologies courantes sont présentées dans la section suivante : [Topologies distinctes](#multiple-forests-separate-topologies), [Maillage complet](#multiple-forests-full-mesh-with-optional-galsync) et [Comptes-ressources](#multiple-forests-account-resource-forest).

Dans la configuration par défaut fournie par Azure AD Connect Sync, il est supposé que :

1.	les utilisateurs n’ont qu’un seul compte activé et la forêt où se trouve ce compte est utilisée pour authentifier l’utilisateur. Ceci vaut pour la synchronisation de mot de passe et pour la fédération ; userPrincipalName et sourceAnchor/immutableID proviendront de cette forêt.
2.	les utilisateurs n’ont qu’une seule boîte aux lettres ;
3.	la forêt qui héberge la boîte aux lettres d’un utilisateur a la meilleure qualité de données pour les attributs visibles dans la liste d’adresses globale Exchange. Si aucune boîte aux lettres n’est associée à l’utilisateur, n’importe quelle forêt peut être utilisée pour fournir ces valeurs d’attributs.
4.	Si vous avez une boîte aux lettres liée, il existe également un autre compte dans une forêt différente de celle utilisée pour la connexion.

Si votre environnement ne correspond pas à ces hypothèses, voici ce qui se produit :

-	Si vous avez plusieurs comptes actifs ou plusieurs boîtes aux lettres, le moteur de synchronisation en choisit un et ignore les autres.
-	Si vous avez des boîtes aux lettres liées, mais aucun autre compte, ces comptes ne sont pas exportés vers Azure AD et l’utilisateur n’appartient à aucun groupe. Dans DirSync, une boîte aux lettres liée est représentée comme une boîte aux lettres normale : il s’agit donc d’un comportement intentionnellement différent pour mieux prendre en charge les scénarios avec plusieurs forêts.

### Plusieurs forêts, plusieurs serveurs de synchronisation connectés à un annuaire Azure AD
![Plusieurs forêts, synchronisation multiple – Non pris en charge](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

La connexion de plusieurs serveurs Azure AD Connect Sync à un même annuaire Azure AD n’est pas prise en charge (à l’exception d’un [serveur intermédiaire](#staging-server)).

### Plusieurs forêts – topologies distinctes
« Les utilisateurs ne sont représentés qu’une seule fois à travers tous les annuaires »

![Plusieurs forêts, utilisateurs représentés une seule fois](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Plusieurs forêts, topologies distinctes](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

Dans cet environnement, toutes les forêts locales sont traitées comme des entités distinctes et aucun utilisateur n’est présent dans une autre forêt. Chaque forêt a sa propre organisation Exchange et il n’existe pas de GALSync entre les forêts. Cette situation peut se présenter suite à une fusion/acquisition ou dans une organisation où chaque division fonctionne de manière isolée. Dans Azure AD, ces forêts sont dans la même organisation et s’affichent avec une liste d’adresses globale unifiée. Dans cette image, chaque objet de chaque forêt est représenté une seule fois dans le métaverse et agrégé dans l’annuaire Azure AD cible.

### Plusieurs forêts – utilisateurs en correspondance
Une configuration courante pour tous les scénarios avec plusieurs forêts où vous sélectionnez une des options sous « Les identités utilisateurs existent sur plusieurs annuaires » est que les groupes de distribution et de sécurité peuvent se trouver dans chaque forêt, et qu’ils peuvent contenir un mélange d’utilisateurs, de contacts et d’entités de sécurité externes (FSP, Foreign Security Principal).

Les entités de sécurité externes sont utilisées dans ADDS pour représenter des membres d’autres forêts dans un groupe de sécurité. Le moteur de synchronisation résout l’entité de sécurité externe en son utilisateur réel et représente le groupe de sécurité dans Azure AD avec toutes les entités de sécurité externes résolues en leur objet réel.

### Plusieurs forêts – maillage complet avec GALSync facultative
« Les identités utilisateurs existent sur plusieurs annuaires. Correspondance à l’aide de : attribut de messagerie »

![Plusieurs forêts, messagerie des utilisateurs](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Plusieurs forêts, maillage complet](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Une topologie à maillage complet permet aux utilisateurs et aux ressources de se trouver dans n’importe quelle forêt. En général, il existe des approbations bidirectionnelles entre les forêts.

Si Exchange est présent dans plusieurs forêts, il peut éventuellement y avoir une solution GALSync locale représentant un utilisateur d’une forêt comme un contact dans toutes les autres forêts. GALSync est généralement implémenté à l’aide de Forefront Identity Manager 2010 ou Microsoft Identity Manager 2016. Azure AD Connect ne peut pas être utilisé pour une GALSync locale.

Dans ce scénario, les objets d’identité sont joints à l’aide de l’attribut de messagerie. Ainsi, un utilisateur ayant une boîte aux lettres dans une forêt est joint aux contacts dans les autres forêts.

### Plusieurs forêts – forêt comptes-ressources
« Les identités utilisateurs existent sur plusieurs annuaires. Correspond à l’aide de : attributs ObjectSID et msExchMasterAccountSID »

![Plusieurs forêts, ObjectSID des utilisateurs](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![Plusieurs forêts, comptes-ressources](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

Dans une topologie de forêt comptes-ressources, vous avez une ou plusieurs forêts de comptes avec des comptes d’utilisateurs actifs.

Ce scénario inclut une forêt qui fait confiance à toutes les forêts de comptes. Cette forêt a généralement un schéma Active Directory étendu avec Exchange et Lync. Tous les services Exchange et Lync, ainsi que d’autres services partagés, sont situés dans cette forêt. Les utilisateurs ont un compte d’utilisateur désactivé dans cette forêt et la boîte aux lettres est liée à la forêt de comptes.

## Considérations sur Office 365 et la topologie
Certaines charges de travail Office 365 ont certaines restrictions quant aux topologies prises en charge. Si vous envisagez d’utiliser une de celles-ci, reportez-vous aux pages de topologies prises en charge de chaque charge de travail.

| Charge de travail | |
| --------- | --------- |
| Exchange Online |	S’il existe plusieurs organisations Exchange locales (c’est-à-dire si Exchange a été déployé sur plusieurs forêts), vous devez utiliser Exchange 2013 SP1 ou ultérieur. Vous pouvez trouver des détails ici : [Déploiements hybrides avec plusieurs forêts Active Directory](https://technet.microsoft.com/fr-FR/library/jj873754.aspx) |
| Skype Entreprise | Lors de l’utilisation de plusieurs forêts locales, seule la topologie de forêt comptes-ressources est prise en charge. Vous pouvez trouver des détails sur les topologies prises en charge ici : [Configuration requise de l’environnement pour Skype Entreprise Server 2015](https://technet.microsoft.com/fr-FR/library/dn933910.aspx) |

## Serveur intermédiaire
![Serveur intermédiaire](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect prend en charge l’installation d’un second serveur en « mode intermédiaire ». Un serveur dans ce mode lit seulement les données de tous les annuaires connectés et dispose ainsi d’une copie à jour des données d’identité. En cas d’incident où le serveur principal tombe en panne, il est facile de basculer manuellement vers le deuxième serveur à l’aide de l’Assistant Azure AD Connect. Ce second serveur peut de préférence se trouver dans un autre centre de données, car aucune infrastructure n’est partagée avec le serveur principal. Toute modification de configuration apportée au serveur principal doit être copiée par vous sur le second serveur.

Un serveur intermédiaire peut également être utilisé pour tester une nouvelle configuration personnalisée et l’effet qu’elle aura sur vos données. Vous pouvez prévisualiser les modifications et ajuster la configuration. Quand vous êtes satisfait de la nouvelle configuration, vous pouvez faire du serveur intermédiaire le serveur actif et placer l’ancien serveur actif en mode intermédiaire.

Cette méthode peut également être utilisée si vous voulez remplacer le serveur de synchronisation et que vous voulez en préparer un nouveau avant d’arrêter le serveur actuellement actif.

Il est possible d’avoir plusieurs serveurs intermédiaires si vous voulez disposer de plusieurs sauvegardes dans différents centres de données.

## Plusieurs annuaires Azure AD
Microsoft recommande d’avoir un seul annuaire dans Azure AD pour une organisation. Avant d’envisager d’utiliser plusieurs annuaires Azure AD, lisez ces rubriques qui couvrent des scénarios courants et qui vous permettront de n’utiliser qu’un seul annuaire.

| Rubrique | |
| --------- | --------- |
| Délégation à l’aide d’unités administratives | [Gestion des unités administratives dans Azure AD](active-directory-administrative-units-management.md)

![Plusieurs forêts, plusieurs annuaires](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Il existe une relation 1:1 entre un serveur Azure AD Connect Sync et un annuaire Azure AD. Pour chaque annuaire Azure AD, vous avez besoin d’une installation d’un serveur Azure AD Connect Sync. Les instances d’annuaire Azure AD sont isolées par conception et les utilisateurs qui sont dans un annuaire ne peuvent pas voir les utilisateurs d’un autre annuaire. Si c’est ce qui est souhaité, il s’agit d’une configuration prise en charge, mais dans le cas contraire, vous devez utiliser les modèles d’annuaire unique Azure AD décrits ci-dessus.

### Chaque objet une seule fois dans un annuaire Azure AD
![Une seule forêt filtrée](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

Dans cette topologie, un seul serveur Azure AD Connect Sync est connecté à chaque annuaire Azure AD. Les serveurs Azure AD Connect Sync doivent être configurés pour le filtrage et ils ont donc chacun un ensemble d’objets mutuellement exclusifs à traiter, par exemple en délimitant l’étendue de chaque serveur à un domaine ou unité d’organisation particulier. Un domaine DNS ne peut être inscrit que dans un seul annuaire Azure AD. Les UPN des utilisateurs d’Active Directory local doivent donc également utiliser des espaces de noms distincts. Par exemple, dans l’image ci-dessus, trois suffixes d’UPN distincts sont inscrits dans Active Directory local : contoso.com, fabrikam.com et wingtiptoys.com. Les utilisateurs de chaque domaine Active Directory local utilisent un espace de noms différent.

Dans cette topologie, il n’existe pas de « GALSync » entre les instances d’annuaires Azure AD. Le carnet d’adresses dans Exchange Online et dans Skype Entreprise montreront donc seulement des utilisateurs du même annuaire.

Avec cette topologie, un seul des annuaires Azure AD peut activer Exchange hybride avec Active Directory local.

La condition requise d’un ensemble d’objets mutuellement exclusifs s’applique également à l’écriture différée. Ainsi, certaines fonctionnalités d’écriture différée ne pas prises en charge avec cette topologie, car elles supposent une seule configuration locale. notamment :

-	Écriture différée des groupes avec la configuration par défaut
-	Écriture différée des appareils

### Chaque objet plusieurs fois dans un annuaire Azure AD
![Une seule forêt, plusieurs annuaires – Non pris en charge](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Une seule forêt, plusieurs connecteurs – Non pris en charge](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

La synchronisation d’un même utilisateur vers plusieurs annuaires Azure AD n’est pas prise en charge. La modification d’une configuration pour faire en sorte que les utilisateurs dans un annuaire Azure AD apparaissent comme contacts dans un autre annuaire Azure AD n’est pas prise en charge. La modification d’Azure AD Connect Sync pour qu’il se connecte à plusieurs annuaires Azure AD n’est pas non plus prise en charge.

### GALsync à l’aide de l’écriture différée
![Plusieurs forêts, plusieurs annuaires, GALSync1, Non pris en charge](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Plusieurs forêts, plusieurs annuaires, GALSync2, Non pris en charge](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Les annuaires Azure AD sont isolés par conception. La modification de la configuration d’Azure AD Connect Sync pour lire des données à partir d’un autre annuaire Azure AD pour générer une liste d’adresses globale commune et unifiée entre les annuaires n’est pas prise en charge. L’exportation d’utilisateurs comme contacts vers un autre annuaire Active Directory local avec Azure AD Connect Sync n’est pas prise en charge.

### GALsync avec le serveur de synchronisation local
![Plusieurs forêts, plusieurs annuaires, GALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

L’utilisation de FIM2010/MIM2016 local pour effectuer une synchronisation des utilisateurs de la liste d’adresses globale entre deux organisations Exchange est prise en charge. Les utilisateurs d’une organisation apparaissent alors comme des utilisateurs/contacts externes dans l’autre organisation. Ces différents annuaires AD locaux peuvent ensuite être synchronisés vers leurs propres annuaires Azure AD.


## Étapes suivantes
Pour savoir comment installer Azure AD Connect pour ces scénarios, consultez [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

En savoir plus sur la configuration d’[Azure AD Connect sync](active-directory-aadconnectsync-whatis.md).

En savoir plus sur l’[intégration de vos identités locales à Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0128_2016-->