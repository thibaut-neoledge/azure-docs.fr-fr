<properties 
	pageTitle="Stratégies dans Gestion des API Azure" 
	description="Apprenez à créer, à modifier et à configurer des stratégies dans Gestion des API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/28/2015" 
	ms.author="sdanie"/>

# Stratégies dans Gestion des API Azure

Dans Gestion des API Azure (version préliminaire), les stratégies sont une fonctionnalité puissante du système qui permet à l'éditeur de modifier le comportement de l'API grâce à la configuration. Les stratégies sont un ensemble d'instructions qui sont exécutées dans l'ordre sur demande ou sur réponse d'une API. Les instructions les plus utilisées comprennent la conversion du format XML au format JSON et la limitation du débit d'appels pour restreindre la quantité d'appels entrants d'un développeur. De nombreuses autres stratégies sont disponibles.

Pour obtenir la liste complète des instructions et des paramètres de stratégie, consultez la section [Référence de stratégie][Référence de stratégie].

Les stratégies sont appliquées au niveau du proxy qui se trouve entre le consommateur de l'API et l'API managée. Le proxy reçoit toutes les demandes et les transfère normalement sans les modifier à l'API sous-jacente. Cependant, une stratégie peut appliquer des modifications à la demande entrante et à la réponse sortante.

## Configuration des stratégies

Les stratégies peuvent être configurées de façon globale, ou bien au niveau d'un [produit][produit], d'une [API][API] ou d'une [opération][opération]. Pour configurer une stratégie, accédez à l'éditeur Stratégies dans le portail des éditeurs.

![Policies menu][Policies menu]

L'éditeur de stratégies comprend trois sections principales : la portée de la stratégie (en haut), la définition de la stratégie, là où les stratégies sont modifiées (à gauche) et la liste des instructions (à droite) :

![Policies editor][Policies editor]

Pour configurer une stratégie, vous devez d'abord sélectionner la portée à laquelle elle doit s'appliquer. Dans la capture d'écran ci-dessous, le produit 15 Day Free Trial est sélectionné. Notez que le symbole carré à côté du nom de la stratégie indique qu'une stratégie est déjà appliquée à ce niveau.

![Scope][Scope]

Comme une stratégie est déjà appliquée, la configuration est présentée dans l'affichage de la définition.

![Configurer][Configurer]

Initialement, la stratégie est affichée uniquement en lecture seule. Pour pouvoir modifier la définition, cliquez sur l'action Configurer la stratégie.

![Edit][Edit]

La définition de la stratégie est un simple document XML qui décrit une séquence d'instructions entrantes et sortantes. Le code XML peut être modifié directement dans la fenêtre de définition. Une liste d'instructions est fournie à droite. Les instructions applicables à la portée actuelle sont activées et mises en surbrillance, comme l'instruction Limit Call Rate dans la capture d'écran ci-dessus.

Lorsque vous cliquez sur une instruction active, le code XML correspondant est inséré à l'emplacement du curseur dans la fenêtre de définition.

La liste complète des instructions et des paramètres des stratégies se trouve dans la section [Référence de stratégie][Référence de stratégie].

Par exemple, pour ajouter une nouvelle instruction pour limiter les demandes entrantes à certaines adresses IP, placez le curseur juste à l'intérieur du contenu de l'élément XML « Inbound », puis cliquez sur l'instruction Restrict caller IP.

![Restriction policies][Restriction policies]

Ceci ajoute un code XML à l'élément entrant, indiquant comment configurer l'instruction.

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address"/>
    </ip-filter>

Pour limiter les demandes entrantes et n'accepter que celles venant de l'adresse IP 1.2.3.4, modifiez le code XML comme suit :

    <ip-filter action="allow">
        <address>1.2.3.4</address>
    </ip-filter>

![Save][Save]

Lorsque vous avez terminé la configuration des instructions de la stratégie, cliquez sur Enregistrer. Les modifications sont ajoutées immédiatement au proxy Gestion des API.

# Configuration de la stratégie

Une stratégie est une série d'instructions qui s'exécutent dans l'ordre pour une demande et une réponse. La configuration se compose de deux parties, une partie entrante (demande) et une partie sortante (stratégie), comme présenté dans la configuration.

    <policies>
        <inbound>
            <!-- statements to be applied to the request go here -->
        </inbound>
        <outboud>
            <!-- statements to be applied to the response go here -->
        <outbound>
    </policies>

Comme les stratégies peuvent être spécifiées à différents niveaux (globale, produits, API et opérations), la configuration vous permet de spécifier l'ordre dans lequel les instructions de la définition sont exécutées par rapport à la stratégie parente.

Par exemple, si vous avez une stratégie configurée au niveau global et une stratégie configurée pour une API, dès que cette API est utilisée, les deux stratégies sont appliquées. Le service Gestion des API permet de trier de façon déterminée les instructions de stratégie combinées via l'élément de base.

    <policies>
        <inbound>
            <cross-domain />
            <base />
            <find-and-replace from="xyz" to="abc" />
        </inbound>
    </policies>

Dans l'exemple de définition de stratégie ci-dessus, l'instruction interdomaines s'exécute avant toutes les autres stratégies de niveau supérieur, qui sont à leur tour suivies de la stratégie rechercher/remplacer.

Remarque : une stratégie globale n'a pas d'autre stratégie au-dessus d'elle. L'élément *base* est toujours *non fonctionnel*, ou n'a aucun effet.

  [Référence de stratégie]: ../api-management-policy-reference
  [produit]: ../api-management-howto-add-products
  [API]: ../api-management-howto-add-products/#add-apis
  [opération]: ../api-management-howto-add-operations
  [Policies menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
  [Policies editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
  [Scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
  [Configurer]: ./media/api-management-howto-policies/api-management-policies-configure.png
  [Edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
  [Restriction policies]: ./media/api-management-howto-policies/api-management-policies-restrict.png
  [Save]: ./media/api-management-howto-policies/api-management-policies-save.png

<!--HONumber=46--> 
 