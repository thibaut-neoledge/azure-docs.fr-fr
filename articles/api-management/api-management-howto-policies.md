---
title: Stratégies dans Gestion des API Azure | Microsoft Docs
description: Apprenez à créer, à modifier et à configurer des stratégies dans Gestion des API.
services: api-management
documentationcenter: ''
author: steved0x
manager: erikre
editor: ''

ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2016
ms.author: sdanie

---
# Stratégies dans Gestion des API Azure
Dans Gestion des API Azure, les stratégies sont une fonctionnalité puissante du système qui permet à l’éditeur de modifier le comportement de l’API grâce à la configuration. Les stratégies sont un ensemble d'instructions qui sont exécutées dans l'ordre sur demande ou sur réponse d'une API. Les instructions les plus utilisées comprennent la conversion du format XML au format JSON et la limitation du débit d'appels pour restreindre la quantité d'appels entrants d'un développeur. De nombreuses autres stratégies sont disponibles.

Pour obtenir la liste complète des instructions et des paramètres de stratégie, consultez la section [Référence de stratégie][Référence de stratégie].

Les stratégies sont appliquées au niveau de la passerelle qui se trouve entre le consommateur de l’API et l’API managée. La passerelle reçoit toutes les demandes et les transfère normalement sans les modifier à l’API sous-jacente. Cependant, une stratégie peut appliquer des modifications à la demande entrante et à la réponse sortante.

Les expressions de stratégie peuvent être utilisées comme valeurs d’attribut ou valeurs de texte dans l’une des stratégies de Gestion des API, sauf si la stratégie le spécifie autrement. Certaines stratégies, telles que les stratégies [Contrôler le flux][Contrôler le flux] et [Définir la variable][Définir la variable], sont basées sur des expressions de stratégie. Pour plus d’informations, consultez les rubriques [Stratégies avancées][Stratégies avancées] et [Expressions de stratégie][Expressions de stratégie].

## <a name="scopes"> </a>Configuration des stratégies
Les stratégies peuvent être configurées de façon globale, ou bien au niveau d'un [produit][produit], d'une [API][API] ou d'une [opération][opération]. Pour configurer une stratégie, accédez à l'éditeur Stratégies dans le portail des éditeurs.

![Policies menu][policies-menu]

L’éditeur de stratégies se compose de trois sections principales : la portée de la stratégie (en haut), la définition de la stratégie, là où les stratégies sont modifiées (à gauche) et la liste des instructions (à droite) :

![Policies editor][policies-editor]

Pour configurer une stratégie, vous devez d'abord sélectionner la portée à laquelle elle doit s'appliquer. Dans la capture d'écran ci-dessous, le produit **Starter** est sélectionné. Notez que le symbole carré à côté du nom de la stratégie indique qu'une stratégie est déjà appliquée à ce niveau.

![Scope][policies-scope]

Comme une stratégie est déjà appliquée, la configuration est présentée dans l'affichage de la définition.

![Configuration][policies-configure]

Initialement, la stratégie est affichée uniquement en lecture seule. Pour pouvoir modifier la définition, cliquez sur l’action **Configurer la stratégie**.

![Modifier][policies-edit]

La définition de la stratégie est un simple document XML qui décrit une séquence d'instructions entrantes et sortantes. Le code XML peut être modifié directement dans la fenêtre de définition. Une liste d'instructions est fournie à droite. Les instructions applicables à la portée actuelle sont activées et mises en surbrillance, comme l'instruction **Limit Call Rate** dans la capture d'écran ci-dessus.

Lorsque vous cliquez sur une instruction active, le code XML correspondant est inséré à l'emplacement du curseur dans la fenêtre de définition.

> [!NOTE]
> Si la stratégie que vous souhaitez ajouter n’est pas activée, vérifiez que vous êtes dans l’étendue correcte pour cette stratégie. Chaque instruction de stratégie est conçue pour une utilisation dans certaines étendues et sections de la stratégie. Pour consulter les sections de la stratégie et les étendues pour une stratégie, consultez la section **Utilisation** de cette stratégie dans la [Référence de la stratégie][Référence de la stratégie].
> 
> 

La liste complète des instructions et des paramètres des stratégies se trouve dans la section [Référence de stratégie][Référence de stratégie].

Par exemple, pour ajouter une nouvelle instruction pour limiter les demandes entrantes à certaines adresses IP, placez le curseur juste à l'intérieur du contenu de l'élément `inbound` XML, puis cliquez sur l'instruction **Restrict caller IP**.

![Restriction policies][policies-restrict]

Ceci ajoute un code XML à l'élément `inbound`, indiquant comment configurer l'instruction.

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address"/>
    </ip-filter>

Pour limiter les demandes entrantes et n'accepter que celles venant de l'adresse IP 1.2.3.4, modifiez le code XML comme suit :

    <ip-filter action="allow">
        <address>1.2.3.4</address>
    </ip-filter>

![Save][policies-save]

Lorsque vous avez terminé la configuration des instructions de la stratégie, cliquez sur **Enregistrer**. Les modifications sont ajoutées immédiatement à la passerelle Gestion des API.

## <a name="sections"> </a>Configuration de la stratégie
Une stratégie est une série d'instructions qui s'exécutent dans l'ordre pour une demande et une réponse. La configuration se compose des sections `inbound`, `backend`, `outbound` et `on-error`, comme présenté dans la configuration suivante.

    <policies>
      <inbound>
        <!-- statements to be applied to the request go here -->
      </inbound>
      <backend>
        <!-- statements to be applied before the request is forwarded to 
             the backend service go here -->
      </backend>
      <outbound>
        <!-- statements to be applied to the response go here -->
      </outbound>
      <on-error>
        <!-- statements to be applied if there is an error condition go here -->
      </on-error>
    </policies> 

S'il existe une erreur lors du traitement d'une demande, les autres étapes des sections `inbound`, `backend` ou `outbound` sont ignorées et l'exécution passe aux instructions de la section `on-error`. En plaçant des instructions de stratégie dans la section `on-error`, vous pouvez consulter l'erreur à l'aide de la propriété `context.LastError`, inspecter et personnaliser la réponse à l'erreur à l'aide de la stratégie `set-body`, puis configurer ce qui se passe si une erreur se produit. Il existe des codes d'erreur pour les étapes intégrées et pour les erreurs qui peuvent se produire pendant le traitement d'instructions de stratégie. Pour plus d'informations, consultez [Gestion des erreurs dans les stratégies de gestion des API](https://msdn.microsoft.com/library/azure/mt629506.aspx).

Comme les stratégies peuvent être spécifiées à différents niveaux (globale, produits, API et opérations), la configuration vous permet de spécifier l'ordre dans lequel les instructions de la définition de la stratégie sont exécutées par rapport à la stratégie parente.

Les étendues de stratégie sont évaluées dans l'ordre suivant.

1. Étendue globale
2. Étendue produit
3. Étendue API
4. Étendue opération

Les instructions qu'elles contiennent sont évaluées en fonction de l'emplacement de l'élément `base`, s'il est présent.

Par exemple, si vous avez une stratégie configurée au niveau global et une stratégie configurée pour une API, dès que cette API est utilisée, les deux stratégies sont appliquées. Le service Gestion des API permet de trier de façon déterminée les instructions de stratégie combinées via l'élément de base.

    <policies>
        <inbound>
            <cross-domain />
            <base />
            <find-and-replace from="xyz" to="abc" />
        </inbound>
    </policies>

Dans l'exemple de définition de stratégie ci-dessus, l'instruction `cross-domain` s'exécute avant toutes les autres stratégies de niveau supérieur, qui sont à leur tour suivies de la stratégie `find-and-replace`.

Si la même stratégie apparaît deux fois dans l'instruction de stratégie, la stratégie la plus récemment évaluée est appliquée. Cela permet de remplacer les stratégies qui sont définies dans une étendue plus élevée. Pour afficher les stratégies dans l'étendue actuelle dans l'éditeur de stratégie, cliquez sur **Recalculer la stratégie en vigueur pour l'étendue sélectionnée**.

Remarque : une stratégie globale n'a aucune stratégie parente et l'utilisation de l'élément `<base>` n'a aucun effet.

## Étapes suivantes
Découvrez la vidéo suivante sur les expressions de stratégie.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

[Référence de la stratégie]: api-management-policy-reference.md
[Référence de stratégie]: api-management-policy-reference.md
[produit]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md#add-apis
[opération]: api-management-howto-add-operations.md

[Stratégies avancées]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Contrôler le flux]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Définir la variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Expressions de stratégie]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
[policies-editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
[policies-scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
[policies-configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
[policies-edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[policies-save]: ./media/api-management-howto-policies/api-management-policies-save.png

<!---HONumber=AcomDC_0810_2016-->