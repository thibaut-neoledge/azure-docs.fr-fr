<properties pageTitle="Activité de connexion anormale" description="Un rapport incluant les connexions identifiées comme anormales par nos algorithmes Machine Learning."" services="active-directory" documentationCenter="" authors="kenhoff" manager="ilanas" editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2015"
	ms.author="kenhoff"/>

# Activité de connexion anormale

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| <p>Ce rapport inclut les connexions qui ont été identifiées comme « anormales » par nos algorithmes d'apprentissage automatique. Une tentative de connexion peut être considérée comme anormale si celle-ci a été effectuée depuis un emplacement inattendu et/ou à une heure également suspecte. Cela peut signifier qu'un pirate a essayé de se connecter à l'aide de ce compte. L'algorithme d'apprentissage automatique considère les événements comme « anormaux » ou « suspects », où « suspects » indique une probabilité plus élevée de violation de la sécurité.</p><p>Les résultats de ce rapport vous indiqueront ces connexions, ainsi que la classification, l’emplacement et l’horodatage associé à chaque connexion.</p><p>Nous enverrons une notification par courrier électronique aux administrateurs généraux si nous constatons 10 événements de connexion ou plus en l’espace de 30 jours. Veillez à inclure aad-alerts-noreply@mail.windowsazure.com dans votre liste d'expéditeurs approuvés.</p> | Répertoire > onglet Rapports |

<!---HONumber=July15_HO4-->