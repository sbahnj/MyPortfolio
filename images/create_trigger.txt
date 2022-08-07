-- Trigger for checking for duplicate species/items:

---

delimiter $$

 

CREATE TRIGGER no_duplicate_species_items BEFORE INSERT

ON pokemon

FOR EACH ROW

 

BEGIN

       DECLARE cur_species VARCHAR(50);

    DECLARE cur_item VARCHAR(50);

    DECLARE is_empty INT DEFAULT 0;

       DECLARE cur_team CURSOR FOR SELECT p.species_name, p.item FROM pokemon AS p WHERE p.team_id = NEW.team_id;

    DECLARE CONTINUE HANDLER FOR NOT FOUND SET is_empty = 1;

    OPEN cur_team;

 

       IF NOT is_empty THEN

              check_duplicates: LOOP

                    FETCH cur_team INTO cur_species, cur_item;

                    IF cur_species = NEW.species_name THEN

                           SIGNAL SQLSTATE '45000'

                           SET MESSAGE_TEXT = 'Rollback insert--Duplicate species';

                    END IF;

       

                    IF cur_item = NEW.item THEN

                           SIGNAL SQLSTATE '45000'

                           SET MESSAGE_TEXT = 'Rollback insert--Duplicate held item';

                    END IF;

              END LOOP check_duplicates;

       END IF;

END $$

 

delimiter ;