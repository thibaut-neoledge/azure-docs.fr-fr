<properties
   pageTitle="Gestion des unités administratives dans Azure Active Directory"
   description="Utilisation d'unités administratives pour une délégation plus précise des autorisations dans Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/26/2016"
   ms.author="curtand"/>

# Gestion des unités administratives dans Azure AD - version préliminaire publique

Cet article décrit les unités administratives. Il s’agit d’un nouveau conteneur de ressources Azure Active Directory qui peut être utilisé pour déléguer des autorisations administratives à des sous-ensembles d’utilisateurs et y appliquer des stratégies. Dans Azure Active Directory, les unités administratives permettent aux administrateurs centraux de déléguer des autorisations aux administrateurs régionaux ou de définir une stratégie à un niveau granulaire.

Cela est utile dans les organisations disposant de divisions indépendantes, par exemple, une grande université qui se compose de nombreuses écoles autonomes (école de commerce, école d’ingénieurs, etc.) qui sont indépendantes les unes des autres. Ces divisions comportent leurs propres administrateurs informatiques qui contrôlent les accès, gèrent les utilisateurs et définissent des stratégies pour leur département en particulier. Les administrateurs centraux souhaitent accorder à ces administrateurs régionaux des autorisations relatives aux utilisateurs de leurs divisions et non à l’ensemble d’entre eux. Plus précisément, en s’appuyant sur le même exemple, un administrateur central peut créer une unité administrative pour une école particulière (une école de commerce) et la remplir uniquement par les utilisateurs de cette école de commerce. De son côté, l’administrateur central peut attribuer un rôle défini au personnel informatique de l’école de commerce. Concrètement, il lui donne des autorisations administratives relatives à l’unité administrative de l’école de commerce uniquement.

> [AZURE.IMPORTANT]
Vous pouvez créer et utiliser des unités administratives uniquement si vous activez Azure Active Directory Premium. Pour plus d'informations, consultez la section [Prise en main d’Azure AD Premium](active-directory-get-started-premium.md).

Du point de vue de l'administrateur central, une unité administrative est un objet de répertoire qui peut être créé et rempli par des ressources. **Dans cette version, ces ressources peuvent être uniquement des utilisateurs.** Une fois créée et remplie, l'unité administrative peut être utilisée comme un cadre permettant de limiter l’autorisation accordée aux ressources contenues dans l’unité administrative.

## Gestion des unités administratives

Dans cette version préliminaire, vous pouvez créer et gérer des unités administratives à l'aide du module Azure Active Directory pour les cmdlets Windows PowerShell.

Pour plus d'informations sur la configuration logicielle requise et sur l’installation du module Azure AD et pour plus d'informations sur les cmdlets du module Azure AD pour la gestion d’unités administratives, et notamment la syntaxe, les descriptions de paramètres et des exemples, consultez la section [Gestion d’Azure AD à l'aide de Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).


## Étapes suivantes
[Éditions d’Azure Active Directory](active-directory-editions.md)

<!---HONumber=AcomDC_0511_2016-->