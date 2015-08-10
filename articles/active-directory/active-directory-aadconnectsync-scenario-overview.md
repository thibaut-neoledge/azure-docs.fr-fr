<properties
	pageTitle="Azure AD Connect Sync : présentation du scénario de forêts multiples"
    description="L’objectif de cette rubrique est de décrire certains scénarios courants et comment ils sont représentés dans le service de synchronisation d’Azure AD Connect Sync."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect Sync : présentation du scénario de forêts multiples

De nombreuses organisations possèdent des environnements comportant plusieurs forêts Active Directory locales. Il existe plusieurs raisons de déployer plus d’une forêt Active Directory locale, par exemple quand il s’agit de forêts de ressources de comptes, de forêts liées à des fusions et acquisitions ou de forêts utilisées pour externaliser des données.

Microsoft propose DirSync, une solution pour les scénarios à forêt unique, et FIM, une solution pour les scénarios à forêts multiples. Toutefois, configurer FIM peut s’avérer difficile et très long.

Avec Azure AD Connect Sync, vous pouvez simplifier considérablement la configuration et la rendre plus prédictive.

Dans la configuration par défaut fournie par Azure AD Connect Sync, on part du principe que :

1. les utilisateurs n’ont qu’un seul compte activé et la forêt où se trouve ce compte est utilisée pour fédérer l’utilisateur ;
2. les utilisateurs n’ont qu’une seule boîte aux lettres ;
3. la forêt qui héberge la boîte aux lettres d’un utilisateur a la meilleure qualité de données pour les attributs visibles dans la liste d’adresses globale Exchange. Si aucune boîte aux lettres n’est associée à l’utilisateur, n’importe quelle forêt peut être utilisée pour fournir ces valeurs d’attributs.


L’objectif de cette rubrique est de décrire certains scénarios courants et comment ils sont représentés dans le service de synchronisation d’Azure AD Connect Sync :

1. technologies distinctes ; 
2. maillage complet avec solution GALSync facultative ; 
3. forêt de ressources de comptes ; 


## Technologies distinctes

Dans cet environnement, toutes les forêts locales sont traitées comme des entités distinctes et aucun utilisateur n’est présent dans une autre forêt.<br> Chaque forêt possède sa propre organisation Exchange et il n’existe aucune solution GALSync entre les forêts. Cette situation peut se présenter suite à une fusion/acquisition ou dans une organisation où chaque division fonctionne de manière isolée.

![Technologies distinctes][1]

Dans ce schéma, chaque objet de chaque forêt est représenté une fois dans le métaverse et agrégé dans l’annuaire Azure AD cible. Le résultat final serait le même que si un serveur de synchronisation d’annuaire était connecté à chaque forêt Active Directory source.
 




## Maillage complet avec solution GALSync facultative

Une topologie à maillage complet permet aux utilisateurs et aux ressources de se trouver dans n’importe quelle forêt. En général, il existe des approbations bidirectionnelles entre les forêts.

Si Exchange est présent dans plusieurs forêts, il peut éventuellement y avoir une solution GALSync représentant un utilisateur d’une forêt en tant que contact dans chaque autre forêt.

Dans ce scénario, les objets d’identités sont généralement joints à l’aide de l’attribut de messagerie. Ainsi, un utilisateur ayant une boîte aux lettres dans une forêt est joint aux contacts dans les autres forêts. Dans chaque forêt figurent des groupes de distribution et de sécurité, qui peuvent contenir une combinaison d’utilisateurs, de contacts et d’entités de sécurité externes.

L’illustration suivante présente ce scénario.

![Maillage complet avec solution GALSync facultative][2]


## Forêt de ressources de comptes
Dans une topologie de forêt de ressources de comptes, vous avez une ou plusieurs forêts de comptes avec des comptes d’utilisateurs actifs.<br> Ce scénario inclut une forêt qui fait confiance à toutes les forêts de comptes. Cette forêt a généralement un schéma Active Directory étendu avec Exchange et Lync. Tous les services Exchange et Lync, ainsi que d’autres services partagés, se trouvent dans cette forêt. Les utilisateurs ont un compte d’utilisateur désactivé dans cette forêt et la boîte aux lettres est liée à la forêt de comptes.

Le schéma ci-dessous décrit ce scénario avec un seul compte.

![Forêt de ressources de comptes][3]


## Ressources supplémentaires

* [Azure AD Connect Sync : personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

 
<!--Image references-->
[1]: ./media/active-directory-aadsync-scenario-overview/ic750599.png
[2]: ./media/active-directory-aadsync-scenario-overview/ic750600.png
[3]: ./media/active-directory-aadsync-scenario-overview/ic750601.png

<!---HONumber=July15_HO5-->