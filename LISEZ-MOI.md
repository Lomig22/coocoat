# Landing page Coocoat — guide de mise en ligne

Site statique : une page HTML, sans base de données ni serveur à installer. Il suffit de l'héberger et de brancher le domaine **coocoat.com**.

## Contenu du dossier

| Élément | Rôle |
|---|---|
| `index.html` | La page. Tous les réglages sont dans le bloc `CONFIG`, en haut du `<script>` en bas du fichier. |
| `assets/` | Photos, vignettes (`assets/thumbs/`), vidéo, logos utilisés par la page. |
| `favicon/` | Icônes d'onglet, d'iPhone et d'Android, plus `site.webmanifest`. |
| `logo/` | Logo Coocoat en SVG et PNG (fond transparent, fond crème, version blanche) pour Shopify, Instagram, etc. Pas nécessaire au site. |

---

## 1. À faire AVANT la mise en ligne (important)

Ouvre `index.html`, descends jusqu'à `const CONFIG = {`.

1. **Boutique Shopify (`store` et les `id`)**
   Les boutons d'achat pointent **encore vers la boutique toastyblanki.com** et ses identifiants de produits. Il faut mettre ceux de la boutique Coocoat :
   - `store` : l'adresse de la boutique Coocoat (par exemple `https://shop.coocoat.com`, voir l'étape 3).
   - `variants` : l'ID de chaque couleur. Dans Shopify : *Produits → le produit → cliquer sur la variante*. L'ID est le nombre à la fin de l'URL.
   - `addons` : les ID de la Power Bank et de la Mystery Box.
   - Pour tester : ouvrir `https://ta-boutique/cart/ID:1`. Ça doit arriver directement au paiement avec le bon produit.

2. **Devise**
   La page affiche des prix en **€**. La boutique Shopify doit donc facturer en euros : *Paramètres → Général → Devise de la boutique : EUR*. Sinon les clients voient 44,95 € sur la page et paient 44,95 $.

3. **Code promo du pack Duo (2 pour 70 €)**
   Dans Shopify : *Réductions → Créer → Montant de réduction sur des produits*.
   - Code : `DUO70`
   - Montant fixe : **19,90 €** (= 2 × 44,95 − 70)
   - S'applique à : le produit Coocoat uniquement
   - Quantité minimum : **2**
   - Cocher **« Appliquer la réduction une seule fois par commande »**

   La page ajoute ce code toute seule quand on choisit le pack Duo, ou quand on met une quantité de 2 ou plus.

4. **Fin de l'offre (`saleEnds`)**
   Mets la vraie date de fin de l'offre −30 %. À cette date, le compte à rebours, les badges « SAVE 30% » et le prix barré disparaissent tout seuls. Pense aussi à retirer le prix barré dans Shopify à ce moment-là.
   Règle européenne (directive Omnibus) : le prix barré (`comparePrice`, 64,95 €) doit être le prix le plus bas pratiqué dans les 30 jours avant le début de la promo.

5. **Avis et chiffres**
   - `rating` / `reviewCount` (4,8 et 541 avis) et les avis clients affichés viennent de l'ancienne page. Il faut les remplacer par de **vrais** avis Coocoat (application Judge.me, par exemple), ou mettre `rating: null` pour cacher les étoiles. En France, afficher de faux avis est interdit et sanctionné.
   - `ordersLast24h` : le vrai nombre de commandes des dernières 24 h (Shopify Analytics). `null` cache la ligne.

6. **Pop-up d'achats (`recentOrders`)**
   Le pop-up affiche :
   - la date de livraison estimée pour la ville du visiteur, détectée par son adresse IP (seulement pour les pays listés dans `shipsTo`) ;
   - les **vraies** commandes récentes que tu ajoutes dans `recentOrders`, au format `{ name: 'Camille B.', city: 'Bordeaux', color: 'Wine Red', time: '2026-10-20T09:12:00+02:00' }`. Les commandes de plus de 72 h sont ignorées.

   Ajoute `?demo=1` à l'adresse pour voir le pop-up avec des exemples (marqués « DEMO »).
   Pour l'automatiser avec les vraies commandes Shopify, une application de type « Sales Pop » branchée sur les commandes réelles fait la même chose.

7. **Politiques Shopify** (*Paramètres → Politiques*)
   La page promet :
   - livraison gratuite et suivie sous 7 jours ;
   - satisfait ou remboursé 30 jours.

   Les politiques Shopify doivent dire la même chose. Remplis aussi les **Mentions légales**, obligatoires en France (le lien est en bas de page). Dans la politique de confidentialité, ajoute une ligne indiquant que la ville du visiteur est estimée via son IP (service geojs.io) pour afficher la date de livraison.

8. **Mystery Box** : remplace « A cozy surprise » par une vraie description de ce qu'elle contient (obligatoire avant achat).

---

## 2. Mettre la page en ligne

### Option simple : Netlify (gratuit)
1. Aller sur **app.netlify.com/drop** et glisser **tout le dossier** (celui qui contient `index.html`).
2. Netlify donne une adresse du type `xxx.netlify.app`. Vérifier que la page s'affiche.
3. *Domain management → Add a domain* → `coocoat.com`.
4. Chez le registrar du domaine (OVH, GoDaddy, Namecheap…), dans la zone DNS :
   - enregistrement **A** `@` → `75.2.60.5`
   - enregistrement **CNAME** `www` → `xxx.netlify.app`
5. Attendre la propagation (quelques minutes à quelques heures). Le HTTPS s'active tout seul.

### Alternative : Vercel
Même principe : importer le dossier, puis dans la zone DNS mettre **A** `@` → `76.76.21.21` et **CNAME** `www` → `cname.vercel-dns.com`.

---

## 3. Et la boutique Shopify ?

Un domaine ne peut pointer que vers un seul site. Si la landing page est sur **coocoat.com**, mets la boutique Shopify sur un sous-domaine :

1. Shopify : *Paramètres → Domaines → Connecter un domaine existant* → `shop.coocoat.com`.
2. Dans la zone DNS : **CNAME** `shop` → `shops.myshopify.com`.
3. Dans `index.html` : `store: 'https://shop.coocoat.com'`.

Pense aussi à renommer la boutique et le produit en « Coocoat » dans Shopify : c'est le nom que le client voit au moment du paiement.

---

## 4. Tester en local avant d'envoyer

Dans le dossier :
```bash
python3 -m http.server 8000
```
Puis ouvrir http://localhost:8000 (ajouter `?demo=1` pour voir les pop-ups).
