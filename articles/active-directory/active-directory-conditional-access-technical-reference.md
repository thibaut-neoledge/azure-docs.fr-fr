
<properties
	pageTitle="Référence technique : accès conditionnel aux applications Azure AD | Microsoft Azure"
	description="Avec le contrôle d’accès conditionnel, Azure Active Directory vérifie les conditions spécifiques que vous choisissez lors de l’authentification de l’utilisateur et avant d’autoriser l’accès à l’application. Une fois que ces conditions sont remplies, l’utilisateur est authentifié et autorisé à accéder à l’application."
    services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="02/11/2016"
	ms.author="femila"/>

# Référence technique : accès conditionnel aux applications Azure AD

## Services activés avec accès conditionnel
Les règles d’accès conditionnel sont prises en charge sur différents types d’application Azure AD. Cette liste comprend les éléments suivants :

- Applications fédérées dans la galerie d’applications Azure AD
- Application d’authentification unique par mot de passe dans la galerie d’applications Azure AD
- Applications inscrites auprès du proxy d’application Azure
- Applications métiers et mutualisées développées inscrites auprès d’Azure AD
- Visual Studio Online
- Application distante Azure

## Activer les règles d’accès

Chaque règle peut être activée ou désactivée sur la base de l’application. Lorsque les règles sont activées, elles s’appliquent à tous les utilisateurs qui accèdent à l’application. Lorsqu’elles sont désactivées, elles ne sont pas utilisées et n’ont aucun impact sur l’expérience de connexion de l’utilisateur.

## Application de règles à des utilisateurs spécifiques
Les règles peuvent être appliquées à des ensembles spécifiques d’utilisateurs basés sur le groupe de sécurité en définissant l’option Appliquer à. Cette option peut être définie sur Tous les utilisateurs ou Groupes. Lorsqu’elle est définie sur Tous les utilisateurs, les règles s’appliquent à n’importe quel utilisateur ayant accès à l’application. L’option Groupes permet de sélectionner des groupes de sécurité et de distribution spécifiques, et d’appliquer les règles à ces groupes uniquement. Lorsque vous déployez une règle pour la première fois, il est courant de commencer par l’appliquer à un ensemble limité d’utilisateurs, qui sont membres d’un groupe pilote. La règle peut ensuite être appliquée à tous les utilisateurs de l’organisation. Des groupes sélectionnés peuvent également être exclus de la stratégie à l’aide de l’option Sauf. Tous les membres de ces groupes seront exclus et ce, même s’ils apparaissent dans un groupe inclus.

## Réseaux Au bureau

Les règles d’accès conditionnel qui utilisent un réseau Au bureau s’appuient sur des plages IP approuvées qui ont été configurées dans Azure AD. Ces règles incluent les éléments suivants :

- Exiger l’authentification multifacteur à l’extérieur de l’entreprise
- Bloquer l’accès quand l’utilisateur n’est pas au bureau

Les plages IP approuvées peuvent être configurées sur la [page des paramètres de l’authentification multifacteur](../multi-factor-authentication/multi-factor-authentication-whats-next.md). La stratégie d’accès conditionnel utilisera les plages configurées sur chaque demande d’authentification et l’émission de jetons pour l’évaluation des règles. La revendication du réseau d’entreprise interne n’est pas utilisée, car elle n’est pas disponible pour les sessions longues, tels que les jetons d’actualisation dans les applications mobiles.

## Règles par application
Les règles sont configurées par application, permettant ainsi aux services à valeur élevée d’être sécurisé sans affecter l’accès à d’autres services. Les règles d’accès conditionnel peuvent être configurées dans l’onglet Configurer de l’application.

Les règles actuellement proposées sont les suivantes :

- Imposer l’authentification multifacteur
 - Tous les utilisateurs auxquels la stratégie est appliquée devront avoir effectué au moins une fois l’authentification multifacteur.
- Exiger l’authentification multifacteur à l’extérieur de l’entreprise
 - Tous les utilisateurs auxquels la stratégie est appliquée devront avoir effectué au moins une fois l’authentification multifacteur s’ils accèdent au service à partir d’un emplacement distant. S’ils se déplacent du bureau vers un emplacement distant, ils devront effectuer l’authentification multifacteur lors de l’accès au service.
- Bloquer l’accès quand l’utilisateur n’est pas au bureau 
 - Tous les utilisateurs auxquels la stratégie est appliquée seront bloqués lorsqu’ils tenteront d’accéder au service à partir d’un emplacement distant. S’ils se déplacent du bureau vers un emplacement distant, ils seront autorisés à y accéder lorsqu’ils se trouvent au bureau.

<!---HONumber=AcomDC_0218_2016-->