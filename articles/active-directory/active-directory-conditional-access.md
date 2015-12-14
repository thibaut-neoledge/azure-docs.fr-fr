<properties
	pageTitle="Gestion des risques avec accès conditionnel"
	description="Autoriser partout l’accès à des ressources spécifiques au sein du réseau d’entreprise à partir d’appareils connus, conformément aux stratégies et interdire l’accès à partir d’appareils perdus, volés, non conformes."
	services="active-directory, virtual-network"
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
	ms.date="11/24/2015"
	ms.author="femila"/>


# Gestion des risques avec accès conditionnel

Les tendances actuelles des employés concernant leur travail, leur productivité et les moyens d’effectuer leur travail évoluent rapidement. Les employés apportent leurs appareils personnels au travail. Sur ces appareils personnels, ils disposent d’applications qu’ils utilisent dans leur vie numérique personnelle. Et ils se sont habitués à la liberté et aux possibilités que ces dernières leur offrent. Ils souhaitent utiliser ces mêmes applications au travail et, cette souplesse dont ils jouissent dans leur vie numérique personnelle, ils souhaitent également en bénéficier dans leur vie professionnelle. Aujourd’hui, les employés en entreprise s’attendent à pouvoir travailler à partir de n’importe quel endroit, sur les appareils de leur choix, et à pouvoir se connecter et accéder en toute transparence aux applications professionnelles.

Cette flexibilité permettant aux utilisateurs de travailler en tout endroit, à tout moment et de la façon qu’ils veulent, s’accompagne de risques accrus. Beaucoup de données pointent vers ces appareils volés, déplacés ou perdus. Nombre de ces smartphones et tablettes contiennent une quantité invraisemblable d’informations sensibles et confidentielles relatives aux clients et aux entreprises. Il s’agit là de l’équilibre précaire que les architectes informatiques, les spécialistes de la sécurité et les administrateurs informatiques essaient de maintenir. Il convient de trouver un compromis entre permettre aux utilisateurs d’être productifs sur tous les appareils qu’ils aiment et offrir le niveau approprié de sécurité et de protection du contenu d’entreprise résidant sur ces appareils.

Avec les diverses fonctionnalités d’accès conditionnel offertes par Azure Active Directory, Office 365 et Microsoft Intune, les administrateurs informatiques peuvent résoudre les problèmes suivants :

- Autoriser les employés à accéder au réseau d’entreprise à partir de tout endroit, sans pour autant laisser la porte ouverte pour qui que ce soit sur Internet.
- Autoriser un accès en tout endroit à des ressources spécifiques à partir d’appareils conformes aux stratégies.
- Interdire les accès à partir d’appareils perdus, volés ou non conformes.

![][1]

## Étapes suivantes

Les rubriques suivantes décrivent les différents mécanismes disponibles pour la définition de stratégies d’accès conditionnel dans votre organisation.

- [Vue d’ensemble du service d’inscription d’appareil Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)
- [Configuration d’un accès conditionnel en local à l’aide du service d’inscription d’appareils Azure Active Directory](active-directory-conditional-access-on-premises-setup.md)
- [Stratégies d’accès conditionnel basées sur les appareils pour les services Office 365](active-directory-conditional-access-device-policies.md)
- [Vue d’ensemble de l’accès conditionnel Azure pour les applications SaaS](active-directory-conditional-access-azuread-connected-apps.md)


<!--Image references-->
[1]: ./media/active-directory-conditional-access/condaccoverviewvsdx1.png

<!---HONumber=AcomDC_1203_2015-->