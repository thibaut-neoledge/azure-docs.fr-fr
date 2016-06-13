<properties
	pageTitle="Meilleures pratiques relatives à OAuth 2.0 dans Azure AD | Microsoft Azure"
	description="Cet article décrit les meilleures pratiques pour développer des applications qui utilisent OAuth 2.0 dans Azure Active Directory."
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="priyamo"/>


# Meilleures pratiques relatives à OAuth 2.0 dans Azure Active Directory

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

## Utiliser le paramètre d’état

Le paramètre `state` est une valeur générée de manière aléatoire, non réutilisable (généralement un GUID) que le client envoie dans la demande. Il s’agit d’un paramètre facultatif, mais il est fortement recommandé de l’inclure dans les demandes de code d’autorisation. La réponse inclut également la valeur `state`, et l’application doit vérifier que les valeurs d’état sont identiques avant d’utiliser la réponse.

Le paramètre `state` permet de détecter les falsifications de requête intersites (CSRF, Cross Site Request Forgery) menaçant le client. Pour plus d’informations sur les falsifications de requête intersites, consultez [Cross-Site Request Forgery](https://tools.ietf.org/html/rfc6749#section-10.12) (Falsifications de requête intersites) dans l’infrastructure d’autorisation OAuth 2.0.

## Mettre en cache des jetons d’accès

Pour réduire le nombre d’appels réseau de l’application cliente et la latence associée, l’application cliente doit mettre en cache des jetons d’accès tout au long de la durée de vie des jetons, spécifiée dans la réponse OAuth 2.0. Pour déterminer la durée de vie des jetons, utilisez les valeurs de paramètre `expires_in` ou `expires_on`.

Si une ressource de l’API web renvoie un code d’erreur `invalid_token`, cela peut indiquer que la ressource a déterminé que le jeton a expiré. Si les temps horloge du client et de la ressource sont différents (on parle alors de « différence de temps »), la ressource peut considérer que le jeton a expiré avant que celui-ci n’ait été effacé du cache du client. Si cela se produit, effacez le jeton du cache, même si sa durée de vie calculée n’a pas expiré.

## Gérer les jetons d’actualisation

Les jetons d’actualisation n’ont pas de durée de vie spécifiée. En règle générale, leur durée de vie est relativement longue. Toutefois, dans certains cas, les jetons d’actualisation expirent, sont révoqués ou ne disposent pas de privilèges suffisants pour l’action souhaitée. L’application cliente doit envisager et gérer correctement les erreurs retournées par le point de terminaison d’émission de jeton.

Lorsque vous recevez une réponse avec une erreur de jeton d’actualisation, ignorez le jeton d’actualisation actuel et demandez un nouveau code d’autorisation ou un nouveau jeton d’accès. C’est particulièrement le cas lorsque vous utilisez un jeton d’actualisation dans le flux d’octroi de code d’autorisation : si vous recevez une réponse avec le code d’erreur `interaction_required` ou `invalid_grant`, ignorez le jeton d’actualisation et demandez un nouveau code d’autorisation.

## Vérifier le paramètre ID de la ressource

L’application cliente doit vérifier la valeur du paramètre `resource_id` dans la réponse. Dans le cas contraire, un service malveillant peut être en mesure de provoquer une attaque d’élévation de privilèges.

 La stratégie recommandée pour empêcher une attaque consiste à vérifier que le paramètre resource\_id correspond à la base de l’URL de l’API web faisant l’objet de l’accès. Par exemple, si https://service.contoso.com/data fait l’objet d’un accès, le `resource_id` peut être https://service.contoso.com/. L’application cliente doit rejeter un `resource_id` qui ne commence pas par l’URL de base sauf s’il existe une autre façon fiable de vérifier l’ID.

## Émission de jetons et instructions relatives aux nouvelles tentatives

Azure AD prend en charge plusieurs points de terminaison d’émission de jeton pouvant faire l’objet de demandes de clients. Utilisez les instructions suivantes pour implémenter la logique de nouvelle tentative pour ces points de terminaison afin de gérer une défaillance réseau ou de serveur inattendue.

Les échecs suivant de nouvelles tentatives retournent généralement des codes d’erreur de série HTTP 500 pour une demande à un point de terminaison Azure AD. Dans certains scénarios, le client est une application ou un service qui effectue des demandes automatisées à Azure AD. Dans d’autres scénarios, par exemple une fédération web qui utilise le protocole WS-Federation, le client est un navigateur web et l’utilisateur final doit réessayer manuellement.

### Implémenter la logique de nouvelle tentative selon les réponses d’erreur de série HTTP 500

La logique de nouvelle tentative est fortement recommandée lorsque le service Access Control Service (ACS) d’Active Directory retourne des erreurs de série HTTP 500. notamment :

- Erreur HTTP 500 - Erreur interne du serveur
- Erreur HTTP 502 - Passerelle incorrecte
- Erreur HTTP 503 - Service indisponible
- Erreur HTTP 504 - Dépassement du délai de la passerelle

Même si chaque code HTTP peut être répertorié explicitement dans la logique de nouvelle tentative, il suffit d’appeler la logique de nouvelle tentative si une erreur de série HTTP 500 est retournée.

En règle générale, la logique de nouvelle tentative n’est pas recommandée lorsque des codes d’erreur de série HTTP 400 sont retournés. Un code de réponse d’erreur de série HTTP 400 provenant du service ACS signifie que la demande n’est pas valide et doit être revue.

La logique de nouvelle tentative doit être déclenchée par des codes d’erreur HTTP, notamment de série HTTP 504 (dépassement du délai du serveur externe) ou HTTP 500 et non par des codes d’erreur ACS, par exemple ACS90005. Les codes d’erreur ACS sont des codes informatifs et peuvent être modifiés.

### Utilisation requise d’un minuteur d’interruption entre les nouvelles tentatives pour un contrôle de flux optimal

Lorsqu’un client reçoit une erreur de série HTTP 500, il doit attendre avant de renouveler la demande. Pour de meilleurs résultats, il est recommandé de prolonger cette période à chaque nouvelle tentative. Cette approche permet de résoudre rapidement les erreurs temporaires tout en optimisant le taux de demandes pour les problèmes de réseau ou de serveur temporaires dont la résolution est plus longue.

Par exemple, utilisez un minuteur d’interruption exponentiel qui augmente le délai avant chaque nouvelle tentative de façon exponentielle, par exemple : 1 seconde pour la nouvelle tentative 1, 2 secondes pour la nouvelle tentative 2, 4 secondes pour la nouvelle tentative 3 et ainsi de suite.

Ajustez le nombre de nouvelles tentatives et le délai entre chacune d’elles en fonction de vos exigences en termes d’expérience utilisateur. Toutefois, nous recommandons jusqu’à cinq nouvelles tentatives sur une période de cinq minutes. Les échecs causés par le dépassement de délai sont plus longs à résoudre.

## Étapes suivantes

[Bibliothèques d’authentification d’Azure Active Directory](active-directory-authentication-libraries.md)

<!---HONumber=AcomDC_0601_2016-->