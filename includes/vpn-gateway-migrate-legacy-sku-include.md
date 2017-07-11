> [!NOTE]
> L’adresse IP publique de la passerelle VPN change lors du passage de l’ancienne référence SKU à la nouvelle.
> 

Vous ne pouvez pas redimensionner vos passerelles VPN Azure directement entre les anciennes références SKU et les nouvelles. Si vous disposez de passerelles VPN dans le modèle de déploiement du Gestionnaire des ressources qui utilisent d’anciennes références SKU, vous pouvez effectuer une migration vers les nouvelles références SKU. Pour ce faire, supprimez la passerelle VPN existante pour votre réseau virtuel, puis créez-en une nouvelle.

Workflow de migration :

1. Supprimez toutes les connexions à la passerelle de réseau virtuel.
2. Supprimez l’ancienne passerelle VPN.
3. Créez la nouvelle passerelle VPN.
4. Mettez à jour vos périphériques VPN locaux avec la nouvelle adresse IP de passerelle VPN (pour les connexions site à site, S2S).
5. Mettez à jour la valeur de l’adresse IP de passerelle de toutes les passerelles de réseau local d’une connexion entre deux réseaux virtuels qui s’y connecteront.
6. Téléchargez les nouveaux packages de configuration VPN pour les clients point à site (P2S) se connectant au réseau virtuel via cette passerelle VPN.
7. Recréez les connexions à la passerelle de réseau virtuel.