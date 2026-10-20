# Landing page Coocoat — guide de mise en ligne

Site statique : une page HTML, sans base de données ni serveur à installer. Il suffit de l'héberger et de brancher le domaine **coocoat.com**.

## Contenu du dossier

| Élément | Rôle |
|---|---|
| `index.html` | La page. Tous les réglages sont dans le bloc `CONFIG`, en haut du `<script>` en bas du fichier. |
| `assets/` | Photos, vignettes (`assets/thumbs/`), vidéo, logos utilisés par la page. |
| `favicon/` | Icônes d'onglet, d'iPhone et d'Android, plus `site.webmanifest`. |
| `logo/` | Logo Coocoat en SVG et PNG (fond transparent, fond crème, version blanche) pour Whop, Instagram, etc. Pas nécessaire au site. |

---

## 1. À faire AVANT la mise en ligne (important)

Ouvre `index.html`, descends jusqu'à `const CONFIG = {`.

1. **Paiement Whop (les `plan`)**
   Les boutons d'achat ouvrent le **checkout Whop dans une fenêtre, sur la page** (couleur, quantité, options et Duo compris). Les plans sont **déjà créés** dans Whop (entreprise Coocoat) et leurs ID sont dans `CONFIG` :

   | Dans `CONFIG` | Plan Whop à créer (paiement unique, produit physique, en EUR) |
   |---|---|
   | `variants['Beige'].plan`, `['Wine Red'].plan`, `['Matcha Green'].plan` | Coocoat – 44,95 € (un plan par couleur, ou le même ID pour les trois) |
   | `bundle.plan` | Coocoat Duo (2 châles) – 70 € |
   | `addons.powerBank.plan` | Power Bank – 19,95 € |
   | `addons.mysteryBox.plan` | Mystery Box – 9,50 € |

   - Prix TTC (TVA incluse), paiement unique, adresse de livraison demandée.
   - Whop n'accepte qu'**un exemplaire de chaque plan par commande**. La quantité est donc limitée à 3 (`maxQty`) : 2 = un Duo, 3 = un Duo + 1 châle. Si tu actives l'achat en plusieurs exemplaires sur les plans dans Whop, augmente `maxQty`.
   - Les couleurs choisies sont envoyées avec la commande (métadonnée `colors`, ex. `Beige ×1, Wine Red ×1`), et les options dans `addons`. Elles sont visibles sur le paiement dans Whop : c'est là qu'il faut les lire pour préparer l'envoi.
   - Avec 2 châles ou plus, la page met automatiquement 1 plan Duo + les châles en plus au prix normal. Il n'y a pas de code promo à créer.

2. **Devise**
   La page affiche des prix en **€**. Les plans Whop doivent donc être en euros, sinon les clients voient 44,95 € sur la page et paient 44,95 $.

3. **Pages du pied de page (`pages`)**
   Contact, livraison, remboursement, confidentialité, CGV, mentions légales : mets l'URL complète de chaque page. Un lien laissé à `null` est masqué. Les **Mentions légales** sont obligatoires en France.

4. **Fin de l'offre (`saleEnds`)**
   Mets la vraie date de fin de l'offre −30 %. À cette date, le compte à rebours, les badges « SAVE 30% » et le prix barré disparaissent tout seuls. 
   Règle européenne (directive Omnibus) : le prix barré (`comparePrice`, 64,95 €) doit être le prix le plus bas pratiqué dans les 30 jours avant le début de la promo.

5. **Avis et chiffres**
   - `rating` / `reviewCount` (4,8 et 541 avis) et les avis clients affichés viennent de l'ancienne page. Il faut les remplacer par de **vrais** avis Coocoat (application Judge.me, par exemple), ou mettre `rating: null` pour cacher les étoiles. En France, afficher de faux avis est interdit et sanctionné.
   - `ordersLast24h` : le vrai nombre de commandes des dernières 24 h (tableau de bord Whop). `null` cache la ligne.

6. **Pop-up d'achats (`recentOrders`)**
   Le pop-up affiche :
   - la date de livraison estimée pour la ville du visiteur, détectée par son adresse IP (seulement pour les pays listés dans `shipsTo`) ;
   - les **vraies** commandes récentes que tu ajoutes dans `recentOrders`, au format `{ name: 'Camille B.', city: 'Bordeaux', color: 'Wine Red', time: '2026-10-20T09:12:00+02:00' }`. Les commandes de plus de 72 h sont ignorées.

   Ajoute `?demo=1` à l'adresse pour voir le pop-up avec des exemples (marqués « DEMO »).

7. **Politiques (livraison, remboursement, CGV)**
   La page promet :
   - livraison gratuite et suivie sous 7 jours ;
   - satisfait ou remboursé 30 jours.

   Tes politiques (et les conditions affichées sur Whop) doivent dire la même chose. Dans la politique de confidentialité, ajoute une ligne indiquant que la ville du visiteur est estimée via son IP (service geojs.io) pour afficher la date de livraison.

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

## 3. Et Whop ?

Le paiement se fait dans la page, il n'y a pas de boutique séparée à brancher sur le domaine. Renomme simplement l'entreprise et les produits en « Coocoat » dans Whop : c'est le nom que le client voit au moment du paiement.

Après une étape de validation bancaire (3-D Secure), le client revient sur la page, qui affiche la confirmation ou l'échec. Pour revenir sur une autre page, mets son adresse dans `returnUrl` (en https).

---

## 4. Tester en local avant d'envoyer

Dans le dossier :
```bash
python3 -m http.server 8000
```
Puis ouvrir http://localhost:8000 (ajouter `?demo=1` pour voir les pop-ups). Le checkout Whop marche aussi en local.
