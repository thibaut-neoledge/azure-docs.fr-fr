<properties
   pageTitle="Latences de la création de rapports Azure Active Directory | Microsoft Azure"
   description="Délai nécessaire pour que les événements de création de rapports s’affichent dans Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/07/2016"
   ms.author="dhanyahk"/>

# Latences de rapport Azure Active Directory

*Cette documentation fait partie du guide [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).*

Rapport | Minimale | Moyenne | Maximale
------------------------------------------------------- | -------- | ---------- | ----------
**Rapports de sécurité** | | |
Activité de connexion anormale | 2 heures | 4 heures | 8 heures
Connexions à partir de sources inconnues | 2 heures | 4 heures | 8 heures
Connexions après plusieurs échecs | 2 heures | 4 heures | 8 heures
Connexions depuis plusieurs zones géographiques | 2 heures | 4 heures | 8 heures
Connexions depuis des adresses IP avec des activités suspectes | 2 heures | 4 heures | 8 heures
Connexions à partir d’appareils potentiellement infectés | 2 heures | 4 heures | 8 heures
Utilisateurs ayant une activité de connexion anormale | 2 heures | 4 heures | 8 heures
Utilisateurs avec des informations d’identification volées | 2 heures | 4 heures | 8 heures
**Rapports d’application** | | |
Activité d’approvisionnement de compte | 2 heures | 4 heures | 8 heures
Erreurs de configuration de compte | 2 heures | 4 heures | 8 heures
Utilisation des applications | 2 heures | 4 heures | 8 heures
État de substitution de mot de passe | 2 heures | 4 heures | 8 heures
**Rapports d’audit et d’activité** | | |
Rapport d’audit | 1 minute | 15 minutes | 30 minutes
Activité de réinitialisation de mot de passe (Azure AD) | 2 heures | 4 heures | 8 heures
Activité de réinitialisation de mot de passe (Identity Manager) | 2 heures | 4 heures | 8 heures
Activité d’enregistrement de réinitialisation de mot de passe (Azure AD) | 2 heures | 4 heures | 8 heures
Activité d’enregistrement de réinitialisation de mot de passe (Identity Manager) | 2 heures | 4 heures | 8 heures
Activité de groupes en libre-service (Azure AD) | 2 heures | 4 heures | 8 heures
Activité de groupes en libre-service (Identity Manager) | 2 heures | 4 heures | 8 heures
**Rapports RMS** | | |
Utilisateurs RMS les plus actifs | 2 heures | 4 heures | 8 heures
Utilisation de RMS | 2 heures | 4 heures | 8 heures
Utilisation d’un appareil RMS | 2 heures | 4 heures | 8 heures
Utilisation d’applications fonctionnant avec RMS | 2 heures | 4 heures | 8 heures
**Rapports sur la version préliminaire privée** | | |
Toute l’activité de connexion des utilisateurs | 2 heures | 4 heures | 8 heures

<!------HONumber=AcomDC_0309_2016-->