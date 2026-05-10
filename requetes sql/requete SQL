
--  PARTIE 1 : CATÉGORIE FILM
-- ==============================================================================

-- Requête 1 : Le volume global
-- Objectif : Obtenir très rapidement le nombre total de locations enregistrées 
SELECT COUNT(*) as "nb_locations" 
FROM rental;

-- Requête 2 : L'offre par catégorie
-- Objectif : Compter le nombre de films disponibles pour chaque catégorie (Action, 
-- Comédie, etc.) et trier le résultat pour voir quels genres sont les plus représentés.
SELECT C.NAME as "categorie", COUNT(F.TITLE) as "Nombre de films" 
FROM Film F, Film_category FC, category C 
WHERE F.Film_id = FC.Film_id 
AND FC.category_id = C.category_id 
GROUP BY NAME 
ORDER BY 1 ;

-- Requête 3 : Les films "dormants"
-- Objectif : Identifier les films de l'inventaire qui n'ont absolument jamais été loués. 
SELECT title AS "Titre sans location" 
FROM Film F 
WHERE NOT EXISTS ( 
    SELECT 1 
    FROM Inventory I 
    JOIN Rental R ON R.inventory_id = I.inventory_id 
    WHERE I.film_id = F.film_id 
);


-- ==============================================================================
--  PARTIE 2 : VISION CLIENT
-- ==============================================================================

-- Requête 4 : Le palmarès des meilleurs clients
-- Objectif : Dresser le profil complet des clients les plus rentables.
SELECT c.customer_id, c.first_name, c.last_name, COUNT(r.rental_id) AS nombre_locations, 
SUM(p.amount) AS montant_total_depense, MIN(r.rental_date) AS date_premiere_location 
FROM customer c 
INNER JOIN rental r ON c.customer_id = r.customer_id 
INNER JOIN payment p ON r.rental_id = p.rental_id 
GROUP BY c.customer_id, c.first_name, c.last_name 
ORDER BY nombre_locations DESC;


-- ==============================================================================
-- PARTIE 3 : VISION LOCATION
-- ==============================================================================

-- Requête 5 : L'affluence par jour de la semaine
-- Objectif : Savoir quels jours génèrent le plus de locations (du dimanche au samedi) 
-- afin d'anticiper les jours de forte influence en boutique.
SELECT 
CASE strftime('%w', rental_date) 
WHEN '0' THEN 'Dimanche' 
WHEN '1' THEN 'Lundi' 
WHEN '2' THEN 'Mardi' 
WHEN '3' THEN 'Mercredi' 
WHEN '4' THEN 'Jeudi' 
WHEN '5' THEN 'Vendredi' 
WHEN '6' THEN 'Samedi' 
END AS jour_semaine, 
COUNT(*) AS nb_locations 
FROM Rental 
GROUP BY strftime('%w', rental_date) 
ORDER BY nb_locations DESC;

-- Requête 6 : L'affluence par tranche horaire
-- Objectif : Analyser l'activité selon de grandes périodes de la journée.
SELECT 
CASE 
WHEN strftime('%H', rental_date) BETWEEN '06' AND '11' THEN 'Matin (6h-11h)' 
WHEN strftime('%H', rental_date) BETWEEN '12' AND '17' THEN 'Après-midi (12h-17h)' 
WHEN strftime('%H', rental_date) BETWEEN '18' AND '23' THEN 'Soir (18h-23h)' 
ELSE 'Nuit (23h-6h)' 
END AS periode_journee, 
COUNT(*) AS nb_locations 
FROM Rental 
GROUP BY periode_journee 
ORDER BY nb_locations DESC;

-- Requête 7 : Le croisement Jours & Horaires
-- Objectif : Affiner l'analyse des deux requêtes précédentes en croisant les jours 
-- avec les tranches horaires.
SELECT 
CASE strftime('%w', rental_date) 
WHEN '0' THEN 'Dimanche' 
WHEN '1' THEN 'Lundi' 
WHEN '2' THEN 'Mardi' 
WHEN '3' THEN 'Mercredi' 
WHEN '4' THEN 'Jeudi' 
WHEN '5' THEN 'Vendredi' 
WHEN '6' THEN 'Samedi' 
END AS jour_semaine, 
CASE 
WHEN strftime('%H', rental_date) BETWEEN '06' AND '11' THEN 'Matin' 
WHEN strftime('%H', rental_date) BETWEEN '12' AND '17' THEN 'Après-midi' 
WHEN strftime('%H', rental_date) BETWEEN '18' AND '23' THEN 'Soir' 
ELSE 'Nuit' 
END AS periode_journee, 
COUNT(*) AS nb_locations 
FROM Rental 
GROUP BY jour_semaine, periode_journee 
ORDER BY jour_semaine;


-- ==============================================================================
--  PARTIE 4 : CONCLUSION (Indicateurs Financiers & Géographiques)
-- ==============================================================================

-- Requête 8 : La rentabilité moyenne par film
-- Objectif : Observer à quel point un film donné rapporte de l'argent en moyenne à 
-- chaque fois qu'il est loué, tout en affichant son volume total de locations.
SELECT fi.film_id, COUNT(r.rental_id) AS "Nb location", 
ROUND(AVG(amount), 2) AS "Moyenne par location et film" 
FROM Payment p 
INNER JOIN Rental r ON p.rental_id = r.rental_id 
INNER JOIN Inventory i ON r.inventory_id = i.inventory_id 
INNER JOIN Film fi ON i.film_id = fi.film_id 
GROUP BY fi.film_id;

-- Requête 9 : Le montant moyen dépensé par ticket
-- Objectif : Connaître le panier moyen lié à chaque acte de location individuel.
SELECT r.rental_id, ROUND(AVG(p.AMOUNT), 2) as "Montant moyen dépensé par location" 
FROM rental r, payment p 
WHERE r.rental_id = p.rental_id 
GROUP BY r.rental_id;

-- Requête 10 : Le panier moyen selon le client
-- Objectif : Regarder l'investissement moyen par client.
SELECT c.customer_id, ROUND(AVG(p.AMOUNT), 2) as "Montant moyen dépensé par client par location" 
FROM Customer c, payment p 
WHERE c.customer_id = p.customer_id 
GROUP BY c.customer_id;

-- Requête 11 : Le volume de location par Pays
-- Objectif : Cartographier la provenance géographique des revenus en comptant combien 
-- de locations ont été réalisées par pays
SELECT count(c.country_id) AS "nombre de location par pays", c.country 
FROM Country c, city cy, address a, customer cm, rental r 
WHERE c.country_id = cy.country_id 
AND cy.city_id = a.city_id 
AND a.address_id = cm.address_id 
AND cm.customer_id = r.customer_id 
GROUP BY c.country_id, c.country 
ORDER BY count(c.country_id) DESC;
