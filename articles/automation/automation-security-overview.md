<properties
   pageTitle="Sécurité dans Azure Automation"
   description="Cet article présente une vue d’ensemble de la sécurité de l’automatisation et les méthodes d’authentification disponibles pour les comptes Automation dans Azure Automation."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="sécurité de l’automatisation, automatisation sécurisée" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/06/2016"
   ms.author="magoedte" />

# Sécurité dans Azure Automation
Azure Automation vous permet d’automatiser des tâches sur des ressources dans Azure, en local et avec d’autres fournisseurs de cloud comme Amazon Web Services (AWS). Pour qu’un Runbook exécute les actions requises, il doit avoir les autorisations nécessaires pour accéder en toute sécurité aux ressources avec les droits minimaux requis dans l’abonnement. Cet article couvre les différents scénarios d’authentification pris en charge par Azure Automation et vous montre comment vous lancer en fonction de l’environnement ou des environnements que vous devez gérer.

## Présentation du compte Automation
Lorsque vous démarrez Azure Automation pour la première fois, vous devez créer au moins un compte Automation. Les comptes Automation vous permettent d’isoler vos ressources Automation (Runbooks, ressources, configurations) des ressources contenues dans d’autres comptes Automation. Vous pouvez utiliser des comptes Automation pour séparer les ressources dans des environnements logiques distincts. Par exemple, vous pouvez utiliser un compte pour le développement, un autre pour la production et un autre pour l’environnement local. Un compte Azure Automation est différent de votre compte Microsoft ou des comptes créés dans votre abonnement Azure.

Les ressources Automation de chaque compte Automation sont associées à une seule région Azure, mais les comptes Automation peuvent gérer des ressources dans n’importe quelle région. L’existence de stratégies qui requièrent l’isolation des données et des ressources dans une région spécifique constitue la raison principale de création de comptes Automation dans différentes régions.

>[AZURE.NOTE]Les comptes Automation et les ressources qu’ils contiennent, créés dans le portail Azure, ne sont pas accessibles dans le portail Azure Classic. Si vous souhaitez gérer ces comptes ou leurs ressources avec Windows PowerShell, vous devez utiliser les modules Azure Resource Manager.

Toutes les tâches que vous effectuez sur les ressources à l’aide d’Azure Resource Manager (ARM) et des applets de commande Azure dans Azure Automation doivent s’authentifier sur Azure à l’aide de l’authentification basée sur les informations d’identification d’organisation Azure Active Directory. L’authentification par certificat était la méthode d’authentification d’origine avec le mode Azure Service Management (ASM), mais elle était compliquée à configurer. L’authentification auprès d’Azure avec l’utilisateur Azure AD a été introduite en 2014 non seulement pour simplifier le processus de configuration d’un compte d’authentification, mais également pour prendre en charge la possibilité de s’authentifier de manière non interactive auprès d’Azure avec un seul compte d’utilisateur compatible avec le mode ASM et le mode ARM.

Nous avons récemment publié une autre mise à jour, où nous créons maintenant automatiquement un objet principal du service Azure AD lors de la création du compte Automation. Cela correspond à un compte d’authentification Azure. Il s’agit de la méthode d’authentification par défaut pour l’automatisation des runbooks avec Azure Resource Manager.

Le contrôle d’accès en fonction du rôle est disponible dans le mode ARM pour attribuer des actions autorisées à un compte d’utilisateur Azure AD et à un principal du service, et pour authentifier ce principal du service. Pour obtenir plus d’informations susceptibles de vous aider à développer votre modèle de gestion des autorisations Automation, consultez l’article [Contrôle d’accès en fonction du rôle dans Azure Automation](../automation/automation-role-based-access-control.md).

Les Runbooks exécutés sur un Runbook Worker hybride dans votre centre de données ou sur des services informatiques dans AWS ne peuvent pas utiliser la même méthode que celle généralement utilisée pour l’authentification des Runbooks auprès des ressources Azure. Cela est dû au fait que ces ressources sont en cours d’exécution en dehors d’Azure et, par conséquent, elles nécessitent leurs propres informations d’identification de sécurité définies dans Automation pour s’authentifier auprès des ressources auxquelles elles accèdent localement.

## Méthodes d’authentification

Le tableau suivant résume les différentes méthodes d’authentification pour chaque environnement pris en charge par Azure Automation et l’article décrivant comment configurer l’authentification pour vos runbooks.

Méthode | Environnement | Article
----------|----------|----------
Compte d’utilisateur Azure AD | Azure Resource Manager et gestion des services Azure | [Authentifier des Runbooks avec un compte d’utilisateur Azure AD](../automation/automation-sec-configure-aduser-account.md)
Objet principal du service Azure AD | Azure Resource Manager | [Authentifier des Runbooks avec un compte d’identification Azure](../automation/automation-sec-configure-azure-runas-account.md)
Authentification Windows | Centre de données local | [Authentifier des Runbooks pour des Runbook Workers hybrides](../automation/automation-hybrid-runbook-worker.md)
Informations d'identification AWS | Amazon Web Services | [Authentifier des Runbooks avec Amazon Web Services (AWS)](../automation/automation-sec-configure-aws-account.md)

<!---HONumber=AcomDC_0713_2016-->