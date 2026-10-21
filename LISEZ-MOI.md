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

   | Dans `CONFIG` | Produits Whop (prix TTC, paiement unique, adresse de livraison demandée) |
   |---|---|
   | `variants[…].plan` | « Coocoat – Beige », « Coocoat – Wine Red », « Coocoat – Matcha Green » : 44,95 € |
   | `bundle.plans` | Un Duo par paire de couleurs (6), ex. « Coocoat Duo – Beige + Wine Red » : 70 € |
   | `addons.powerBank.plan` | Coocoat Power Bank : 19,95 € |
   | `addons.mysteryBox.plan` | Coocoat Mystery Box : 9,50 € |

   La couleur fait partie du nom du produit : elle s'affiche dans le checkout du client et sur chaque vente dans Whop.
   **Ajouter une couleur :** créer dans Whop le produit « Coocoat – Couleur » et les Duo de cette couleur avec chacune des autres, puis les ajouter dans `variants` et `bundle.plans`, et ajouter la pastille (`swatch`) dans la page.
   - Prix TTC (TVA incluse), paiement unique, adresse de livraison demandée.
   - Whop n'accepte qu'**un exemplaire de chaque plan par commande**. La quantité est donc limitée à 3 (`maxQty`) : 2 = un Duo, 3 = un Duo + 1 châle. Si tu actives l'achat en plusieurs exemplaires sur les plans dans Whop, augmente `maxQty`.
   - Les couleurs et options sont aussi envoyées dans les métadonnées du paiement (`colors`, `addons`).
   - Avec 2 châles ou plus, la page met automatiquement 1 plan Duo + les châles en plus au prix normal. Il n'y a pas de code promo à créer.

2. **Devises (`markets`)**
   La page affiche les prix en **€**, sauf pour les visiteurs du **Royaume-Uni** (et Jersey, Guernesey, île de Man), détectés par leur adresse IP, qui voient des prix fixes en **£** : 39,95 £ le châle (prix barré 57,95 £), 59,95 £ le Duo, 16,95 £ la Power Bank et 7,95 £ la Mystery Box. Ces prix ont leurs propres produits Whop en livres, donc la page et le checkout affichent le même montant.
   - Pour changer un prix en £ : le modifier dans Whop **et** dans `CONFIG.markets.GBP`.
   - Pour ajouter un pays avec sa devise : créer les produits Whop dans cette devise, puis ajouter un bloc dans `markets` sur le modèle de `GBP`.
   - Hors zone euro et Royaume-Uni (ex. États-Unis), la page affiche les prix en €, mais Whop peut afficher le checkout dans la devise locale du client, au taux du jour (option « adaptive pricing », activée sur les plans en €).

3. **Pages du pied de page (dossier `pages/`)**
   Contact, livraison, remboursement, confidentialité, CGV et mentions légales sont rédigées et reliées au pied de page. Il reste à remplir les passages **surlignés en jaune** (`<mark>[…]</mark>`) avec les infos de ta société : raison sociale, adresse, SIRET, TVA, responsable de la publication, hébergeur et médiateur de la consommation. Ces infos sont obligatoires en France. L'email de contact est `contact@coocoat.com` : remplace-le partout s'il est différent.

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

   Les pages du dossier `pages/` disent déjà la même chose. Si tu changes une promesse sur la page, change-la aussi là.

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

**Apple Pay / Google Pay :** le fichier `vercel.json` sert le fichier de vérification Apple Pay de Whop sur `/.well-known/apple-developer-merchantid-domain-association`. Le domaine `www.coocoat.com` est déclaré dans Whop. Si tu changes de domaine, il faut le déclarer aussi dans Whop (*Paramètres de checkout → Apple Pay*).

Après une étape de validation bancaire (3-D Secure), le client revient sur la page, qui affiche la confirmation ou l'échec. Pour revenir sur une autre page, mets son adresse dans `returnUrl` (en https).

---

## 4. Tester en local avant d'envoyer

Dans le dossier :
```bash
python3 -m http.server 8000
```
Puis ouvrir http://localhost:8000 (ajouter `?demo=1` pour voir les pop-ups). Le checkout Whop marche aussi en local.
