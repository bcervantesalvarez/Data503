# Data 503 Final Project

## Use this code for your own pokemon adventure! 

### Part 1

Due to time constraints there are some missing tables that would have added more flexiblity to my analysis.

```sql
CREATE TABLE IF NOT EXISTS pokemon (
    id SMALLINT PRIMARY KEY,
    identifier TEXT,
    species_id SMALLINT,
    height SMALLINT,
    weight SMALLINT,
    base_experience SMALLINT,
    "order" SMALLINT,
    is_default BOOLEAN
);


CREATE TABLE IF NOT EXISTS pokemon_types (
    pokemon_id SMALLINT,
    type_id SMALLINT PRIMARY KEY,
    slot SMALLINT,
    CONSTRAINT ref_pokemon
        FOREIGN KEY(pokemon_id)
            REFERENCES pokemon(id)
);

CREATE TABLE IF NOT EXISTS pokemon_abilities (
    pokemon_id SMALLINT,
    ability_id SMALLINT PRIMARY KEY,
    is_hidden BOOLEAN,
    slot SMALLINT,
    CONSTRAINT ref_pokemon
        FOREIGN KEY(pokemon_id)
            REFERENCES pokemon(id)
);


CREATE TABLE IF NOT EXISTS generations (
    id SMALLINT,
    main_region_id SMALLINT PRIMARY KEY,
    identifier CHAR(15) 
);

CREATE TABLE IF NOT EXISTS types (
    id SMALLINT PRIMARY KEY,
    identifier CHAR(8),
    generation_id SMALLINT,
    damage_class_id SMALLINT,
    CONSTRAINT ref_pokemon_types
        FOREIGN KEY(id)
            REFERENCES pokemon_types(type_id),
    CONSTRAINT ref_generations 
        FOREIGN KEY(generation_id)
            REFERENCES generations(main_region_id)
);

CREATE TABLE IF NOT EXISTS abilities (
    id SMALLINT,
    identifier TEXT,
    generation_id SMALLINT,
    is_main_series BOOLEAN,
    CONSTRAINT ref_pokemon_abilities
        FOREIGN KEY(id)
            REFERENCES pokemon_abilities(ability_id),
    CONSTRAINT ref_generations 
        FOREIGN KEY(generation_id)
            REFERENCES generations(main_region_id)
);

CREATE TABLE IF NOT EXISTS moves (
  id SMALLINT,
  identifier TEXT,
  generation_id SMALLINT,
  type_id SMALLINT,
  power SMALLINT,
  pp SMALLINT,
  accuracy SMALLINT,
  priority SMALLINT,
  target_id SMALLINT,
  damage_class_id SMALLINT,
  effect_id SMALLINT,
  effect_chance SMALLINT,
  contest_type_id SMALLINT,
  contest_effect_id SMALLINT,
  super_contest_effect_id SMALLINT,
  CONSTRAINT ref_types
        UNIQUE(damage_class_id, type_id),
  CONSTRAINT ref_generations 
        FOREIGN KEY(generation_id)
            REFERENCES generations(main_region_id),
  CONSTRAINT ref_types_2
        FOREIGN KEY(type_id)
            REFERENCES types(id)
);

```

### Part 2

This portion of the sql file is for transforming and preparing a csv file for analysis.

```sql
SELECT 
    identifier AS pokemon_name, 
    pokemon_types.type_id,
    pokemon_abilities.ability_id
INTO temp1
FROM pokemon
LEFT JOIN pokemon_types
ON pokemon.id = pokemon_types.pokemon_id
LEFT JOIN pokemon_abilities
ON pokemon.id  = pokemon_abilities.pokemon_id;


SELECT 
    pokemon_name,
    types.identifier AS pokemon_type,
    abilities.identifier AS pokemon_ability,
    types.generation_id AS gen_id,
    types.id AS type_id
INTO temp2
FROM temp1
LEFT JOIN types
ON temp1.type_id = types.id
LEFT JOIN abilities
ON temp1.ability_id = abilities.id;


DROP TABLE temp3;
SELECT 
    pokemon_name,
    pokemon_type,
    pokemon_ability,
    generations.identifier AS pokemon_generation,
    moves.identifier AS pokemon_move,
    moves.power AS pokemon_power,
    moves.accuracy AS pokemon_accuracy,
    moves.pp AS pokemon_pp
INTO temp3
FROM temp2
LEFT JOIN generations
ON temp2.gen_id = generations.main_region_id
LEFT JOIN moves
ON temp2.type_id = moves.type_id;

SELECT *
FROM temp3
WHERE pokemon_power IS NOT NULL 
    AND pokemon_accuracy IS NOT NULL
ORDER BY pokemon_accuracy, pokemon_power;


COPY temp3
TO '/Users/Shared/Data_503/Datasets/scuffed_pokedex.csv'
WITH (FORMAT CSV, HEADER);
```
