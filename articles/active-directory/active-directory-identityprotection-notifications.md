<properties
	pageTitle="Notifications d’Azure Active Directory Identity Protection | Microsoft Azure"
	description="Découvrez comment les notifications prennent en charge vos activités d’examen."
	services="active-directory"
	keywords="azure active directory identity protection, cloud app discovery, gestion d’applications, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="markvi"/>

#Notifications d’Azure Active Directory Identity Protection 


Azure AD Identity Protection envoie deux types d’e-mails de notification automatisés pour vous aider à gérer le risque des utilisateurs et les événements à risque :

- E-mail d’alerte en cas d’utilisateur compromis

- E-mail de synthèse hebdomadaire

## E-mail d’alerte en cas d’utilisateur compromis

Un e-mail d’alerte en cas d’utilisateur compromis est généré lorsqu’Azure AD Identity Protection identifie un compte compromis. Cet e-mail inclut un lien vers le rapport Utilisateurs associés à un indicateur de risque dans le tableau de bord d’Identity Protection. Nous vous recommandons d’examiner immédiatement les notifications d’utilisateurs compromis.


## E-mail de synthèse hebdomadaire

L’e-mail de synthèse hebdomadaire contient un récapitulatif des nouveaux événements à risque.<br> Il inclut :

- Les utilisateurs à risque
- Activités suspectes
- Les vulnérabilités détectées
- Des liens vers les rapports connexes dans Identity Protection


<br> ![Correction](./media/active-directory-identityprotection-notifications/400.png "Correction") <br>

Vous pouvez désactiver l’envoi de l’e-mail de synthèse hebdomadaire. <br><br> ![Risque des utilisateurs](./media/active-directory-identityprotection-notifications/62.png "Risque des utilisateurs") <br>
 

**Pour ouvrir la boîte de dialogue de configuration connexe** :

1. Dans le panneau d’**Azure AD Identity Protection**, cliquez sur **Paramètres**. <br><br> ![Stratégie de risque d’utilisateur](./media/active-directory-identityprotection-notifications/401.png "Stratégie de risque d’utilisateur") <br>

2. Dans la section **Général**, cliquez sur **Notifications**. <br><br> ![Stratégie de risque d’utilisateur](./media/active-directory-identityprotection-notifications/405.png "Stratégie de risque d’utilisateur") <br>




## Voir aussi

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md) 

<!---HONumber=AcomDC_0518_2016-->