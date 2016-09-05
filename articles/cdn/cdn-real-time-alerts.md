<properties
	pageTitle="Alertes en temps réel Azure CDN | Microsoft Azure"
	description="Alertes en temps réel dans Microsoft Azure CDN. Les alertes en temps réel fournissent des notifications sur les performances des points de terminaison dans votre profil CDN."
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="casoper"/>

# Alertes en temps réel dans Microsoft Azure CDN

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]


## Vue d'ensemble

Ce document explique les alertes en temps réel dans Microsoft Azure CDN. Cette fonctionnalité fournit des notifications en temps réel sur les performances des points de terminaison dans votre profil CDN. Vous pouvez configurer des alertes par courrier électronique ou HTTP en fonction des éléments suivants :

* Bande passante
* Codes d’état
* États du cache
* Connexions

## Création d’une alerte en temps réel

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre profil CDN.

	![Panneau du profil CDN](./media/cdn-real-time-alerts/cdn-profile-blade.png)

2. Dans le panneau Profil CDN, cliquez sur le bouton **Gérer**.

	![Bouton de gestion du panneau de profil CDN](./media/cdn-real-time-alerts/cdn-manage-btn.png)

	Le portail de gestion CDN s'ouvre.

3. Pointez sur l’onglet **Analyse**, puis sur le menu volant **Statistiques en temps réel**. Cliquez sur **Alertes en temps réel**.

	![Portail de gestion CDN](./media/cdn-real-time-alerts/cdn-premium-portal.png)

	La liste des configurations d’alerte existantes (le cas échéant) s’affiche.

4. Cliquez sur le bouton **Ajouter une alerte**.

	![Bouton d’ajout d’alerte](./media/cdn-real-time-alerts/cdn-add-alert.png)

	Un formulaire s’affiche pour créer une nouvelle alerte.

	![Formulaire de nouvelle alerte](./media/cdn-real-time-alerts/cdn-new-alert.png)

5. Si vous souhaitez que cette alerte soit active lorsque vous cliquez sur **Enregistrer**, cochez la case **Alerte activée**.

6. Entrez un nom descriptif pour votre alerte dans le champ **Nom**.

7. Dans la liste déroulante **Type de média**, sélectionnez **HTTP Large Object**.

	![Type de média avec HTTP Large Object sélectionné](./media/cdn-real-time-alerts/cdn-http-large.png)

	> [AZURE.IMPORTANT] Vous devez sélectionner **HTTP Large Object** comme **Type de média**. Les autres options ne sont pas utilisées par **Azure CDN de Verizon**. Si vous ne sélectionnez pas **HTTP Large Object**, votre alerte ne se déclenchera jamais.

8. Créez une **Expression** à surveiller en sélectionnant **Mesure**, **Opérateur** et **Valeur de déclenchement**.

	- Pour **Mesure**, sélectionnez le type de condition que vous souhaitez surveiller. **Bandwidth Mbps (Mbits/s de bande passante)** est la quantité de bande passante en mégabits par seconde. **Nombre total de connexions** est le nombre de connexions HTTP simultanées à nos serveurs Edge. Pour obtenir les définitions des différents états de cache et les codes d’état, voir [Azure CDN Cache Status Codes (Codes d’état du cache Azure CDN)](https://msdn.microsoft.com/library/mt759237.aspx) et [Azure CDN HTTP Status Codes (Codes d’état HTTP d’Azure CDN)](https://msdn.microsoft.com/library/mt759238.aspx)
	- **Opérateur** est l’opérateur mathématique qui établit la relation entre la mesure et la valeur du déclencheur.
	- **Valeur de déclenchement** est la valeur de seuil qui doit être atteinte avant l’envoi d’une notification.

	Dans l’exemple ci-dessous, l’expression que j’ai créée indique que je souhaite être averti lorsque le nombre de codes d’état 404 est supérieur à 25.

	![Expression de l’exemple d’alerte en temps réel](./media/cdn-real-time-alerts/cdn-expression.png)

9. Pour **Intervalle**, entrez la fréquence à laquelle vous souhaitez que l’expression soit évaluée.

10. Dans la liste déroulante **Notify on (Notifier sur)**, sélectionnez quand vous souhaitez être averti lorsque l’expression est vraie.
	
	- **Condition Start (Début de la condition)** indique qu’une notification sera envoyée lors de la première détection de la condition spécifiée.
	- **Condition End (Fin de la condition)** indique qu’une notification sera envoyée lorsque la condition spécifiée ne sera plus détectée. Cette notification ne peut être déclenchée qu’une fois que notre système de surveillance du réseau a détecté que la condition spécifiée s’est produite.
	- **Continu** indique qu’une notification sera envoyée chaque fois que le système de surveillance du réseau détecte la condition spécifiée. N’oubliez pas que le système de surveillance du réseau ne recherche la condition spécifiée qu’une seule fois par intervalle.
	- **Condition Start and End (Début et fin de la condition)** indique qu’une notification sera envoyée lors de la première détection de la condition spécifiée, puis à nouveau lorsque la condition ne sera plus détectée.

11. Si vous souhaitez recevoir des notifications par courrier électronique, cochez la case **Notify by Email (Notifier par courrier électronique)**.

	![Formulaire de notification par courrier électronique](./media/cdn-real-time-alerts/cdn-notify-email.png)
	
	Dans le champ **À**, entrez l’adresse de messagerie à laquelle vous voulez que les notifications soient envoyées. Pour **Objet** et **Corps**, vous pouvez laisser la valeur par défaut ou personnaliser le message à l’aide de la liste **Available keywords (Mots clés disponibles)** pour insérer dynamiquement les données d’alerte lors de l’envoi du message.

	> [AZURE.NOTE] Vous pouvez tester la notification par courrier électronique en cliquant sur le bouton **Tester la notification**, mais uniquement après l’enregistrement de la configuration de l’alerte.

12. Si vous souhaitez que les notifications soient publiées sur un serveur web, cochez la case **Notify par HTTP Post (Notifier par HTTP Post)**.

	![Formulaire de notification par HTTP Post](./media/cdn-real-time-alerts/cdn-notify-http.png)

	Dans le champ **Url**, entrez l’adresse URL à laquelle vous souhaitez que le message HTTP soit publié. Dans la zone de texte **En-têtes**, entrez les en-têtes HTTP à envoyer dans la demande. Pour **Corps**, vous pouvez personnaliser le message à l’aide de la liste **Available keywords (Mots clés disponibles)** pour insérer dynamiquement les données d’alerte lors de l’envoi du message. La valeur par défaut des zones de texte **En-têtes** et **Corps** est une charge XML semblable à celle de l’exemple ci-dessous.

	```
	<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
		<![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
	</string>
	```

	> [AZURE.NOTE] Vous pouvez tester la notification HTTP Post en cliquant sur le bouton **Tester la notification**, mais uniquement après l’enregistrement de la configuration de l’alerte.

13. Cliquez sur le bouton **Enregistrer** pour enregistrer votre configuration d’alerte. Si vous avez coché la case **Alerte activée** à l’étape 5, votre alerte est maintenant active.

## Étapes suivantes

- Analyser des [statistiques en temps réel dans Azure CDN](cdn-real-time-stats.md)
- Aller plus loin en vous familiarisant avec les [rapports HTTP avancés](cdn-advanced-http-reports.md)
- Analyser les [modes d’utilisation](cdn-analyze-usage-patterns.md)

<!---HONumber=AcomDC_0824_2016-->