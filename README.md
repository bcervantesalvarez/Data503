# Data503

### If you're reading this, it's too late...

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
