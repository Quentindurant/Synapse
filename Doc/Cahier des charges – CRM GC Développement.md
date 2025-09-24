
# 1. Contexte et objectifs

Pourquoi développer un CRM maison plutôt que d’utiliser une solution existante ? Quels seraient les points différenciants par rapport à des solutions comme Zoho ?

Objectifs principaux : centraliser l’information, automatiser les processus, améliorer le suivi client, faciliter la gestion technique et logistique, etc.

Compte tenu de la spécificité de notre positionnement — entre le partenaire et le client — nous avons besoin d’un outil entièrement modulable, adapté à nos besoins et, surtout, intuitif et facile à prendre en main.


# 2. Utilisateurs & Rôles

👉 Définir pour chaque rôle ce qu’il peut voir, créer/éditer et valider/clôturer :

- Admin – vision globale, gestion des partenaires/techniciens/rapports/support ☐  

- Support – gestion des tickets ☐

- Staging – préparation matériel, stock ☐

- ADV –commandes, suivi technicien, employ du temps, gestion intervention ☐

- Compta – factures, règlements, relances ☐

- Déploiement/Installation – techniciens + interventions terrain ☐

- Partenaire – suivi de ses clients, tickets, commandes ☐


# 3. Modules fonctionnels

## Gestion des clients

- Fiches clients détaillées : coordonnées, contacts, historique, notes internes
    
- Plateformes/centrex associés + liens
    
- Historique interventions, tickets, commandes
    
- Segmentation par secteur, taille, type (Pharmacie, Assurance, etc.)
  
## Tickets (Support)

- Création par le support ou par partenaire
    
- Typologie (incident, demande, projet, SAV)
    
- Statuts (ouvert, en cours, résolu, escaladé, clos)
    
- SLA (délai de traitement)
    
- Attribution auto/manuelle au bon technician ( à verifier l’utiliter)
    
- Recherche de client intelligent (par numéro, système prénom etc..)
 
## Commandes

- Saisie commande (produits, services, quantités)
    
- Suivi étapes : saisie > validation ADV > staging > expédition > installation > facturation
    
- Mail auto au client à chaque étape clé  
  
## Techniciens

- Coordonnées, compétences, zones couvertes
    
- Tri par secteur et compétences
    
- Géolocalisation en temps réel (optionnel)
    
- Notation (ponctualité, qualité, efficacité)
    
- Planning (disponibilités, congés, interventions)

## Carte interactive

- Vue de France avec techniciens positionnés
    
- Clients actifs et installations
    
- Tickets ouverts affichés en overlay
  

## Communication & Notifications

- Emails automatiques (commandes, interventions, clôture)
    
- SMS de rappel
    
- Notifications internes (alerte retard, escalade)

## Compta

- Factures automatiques
    
- Gestion des paiements (statut payé/en attente)
    
- Relances auto par mail
    
- Tableaux de bord CA / marges

## ADV

- Cycle de commande complet : devis/BC, validation, staging (préparation/provisioning), expédition, livraison, planification des installations (date/heure), facturation (acomptes, solde, avoirs).
    
- Planification techniciens : gestion des emplois du temps (terrain & distants), créneaux, conflits, rappels.
    
- Documents : devis, BC, BL, PV signé, facture/avoir (PDF brandés).
    
- Notifications : emails/SMS aux clients et internes aux jalons clés.

# 4. Modules transverses

- Recherche globale (tickets, clients, techniciens, commandes)
    
- Statistiques & Dashboard (par rôle)
    
- RGPD & sécurité (logs, droits d’accès, cryptage mots de passe)
    
- Mobile friendly (techniciens sur le terrain)

# 5. Suggestions utiles pour plus tard 💡

- Gestion des stocks (matériel staging, dispo live)
    
- Chat interne (mini Slack interne)
    
- Suivi des contrats de maintenance (dates renouvellement, alertes expirations)
    
- IA de pré-support (IA qui analyse la demande client et la clarifie/met rrapidement sur une piste de resolution)
  
  
  ## Tableau des droits Simplifiée

| Module            | Admin | Support | Commercial | ADV | Tech Interne | Tech Terrain | Chef Projet | Compta | Partenaire            |
| ----------------- | ----- | ------- | ---------- | --- | ------------ | ------------ | ----------- | ------ | --------------------- |
| **Clients**       | ✅     | 👁️     | ✅          | ✅   | 👁️          | 👁️          | 👁️         | 👁️    | ✅ Ses clients         |
| **Tickets**       | ✅     | ✅       | 👁️        | 👁️ | ✅            | ✅            | 👁️         | ❌      | ✅ Ses tickets         |
| **Commandes**     | ✅     | 👁️     | ✅          | ✅   | 👁️          | 👁️          | 👁️         | 👁️    | ✅ Ses commandes       |
| **Planning**      | ✅     | ❌       | 👁️        | ✅   | 👁️          | ✅ Personnel  | ✅           | ❌      | 👁️ Ses interventions |
| **Interventions** | ✅     | ❌       | 👁️        | ✅   | ✅ Support    | ✅ Terrain    | ✅           | ❌      | 👁️ Chez ses clients  |
| **Staging/Stock** | ✅     | ❌       | ❌          | ✅   | ✅            | ❌            | 👁️         | ❌      | ❌                     |
| **Comptabilité**  | ✅     | ❌       | ❌          | ❌   | ❌            | ❌            | ❌           | ✅      | ❌                     |

**Légende** : ✅ = CRUD complet, 👁️ = Lecture, ❌ = Aucun accès
