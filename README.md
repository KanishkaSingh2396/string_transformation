# string_transformation
--first N character
SELECT LEFT('ABCDEFG', 3);

![image](https://user-images.githubusercontent.com/89623051/140747863-56c9b0d5-582d-4eca-8bdb-a08be858876a.png)


--Return the first 51 characters of the description from the film table with film_id = 14**

SELECT
  LEFT(description, 51) AS short_description
FROM rentals.film
WHERE film_id = 14;

![image](https://user-images.githubusercontent.com/89623051/140748354-62fa6fd9-e07d-4872-b010-681bfdd2035b.png)

---last N characters
SELECT RIGHT('ABCDEFG', 3);

---substring by position
SELECT SUBSTRING('12345ABC9', 6, 3);

![image](https://user-images.githubusercontent.com/89623051/140748758-122814ee-0817-4f55-8f75-8e9f4d96c82d.png)

--character length
SELECT CHAR_LENGTH('123456789');

--What is the max number of characters in the description field?

SELECT
  MAX(CHAR_LENGTH(description))
FROM rentals.film_list;

![image](https://user-images.githubusercontent.com/89623051/140749046-762ccd31-f1f7-4086-bf01-191211196fbe.png)

---starting position of text
SELECT POSITION('a' in '12345a');

![image](https://user-images.githubusercontent.com/89623051/140749197-55f98ec1-a8b0-487b-87df-82610cbcbb5a.png)

--What position does the word ‘Astronaut’ have in the description from the film called ANGELS LIFE?
SELECT
  POSITION('Astronaut' IN description) AS starting_position
FROM rentals.film
WHERE title = 'ANGELS LIFE'

--Convert to Lower Case
select LOWER('ABC');

--Using a CTE show the first 30 characters from the description and also the lower case version for film_id = 14
WITH film_id_14
AS
(
  SELECT LEFT(description, 30) AS short_description
  FROM dvd_rentals.film
  WHERE film_id = 14
)
SELECT
  short_description,
  LOWER(short_description) as lowercase_short_description
FROM film_id_14;

![image](https://user-images.githubusercontent.com/89623051/140750066-293c35c5-fef6-47bf-b676-db6789c62e44.png)

--Using a subquery, show the first 20 characters from the first time ‘Shark’ is mentioned in the description field and also the upper case version for film_id = 14

SELECT
  shark_text,
  LOWER(shark_text) as lowercase_shark_text
FROM (
  SELECT
    SUBSTRING(description, POSITION('Shark' in description), 20) AS shark_text
  FROM rentals.film
  WHERE film_id = 14
) AS subquery;

![image](https://user-images.githubusercontent.com/89623051/140750991-628d44cf-545c-4e81-9d58-e532a6999544.png)

--title case
select INITCAP('hello world!');

![image](https://user-images.githubusercontent.com/89623051/140751195-e04457e2-b77f-4ba3-aeef-7d94b6eb5911.png)

--Using sequential CTEs show the first and last 17 characters from the description field and also the title case version for film_id = 14

WITH
text_input AS
(
  SELECT
    LEFT(description, 17) AS first_17_char,
    RIGHT(description, 17) AS last_17_char
  FROM dvd_rentals.film
  WHERE film_id = 14
),
title_case_text AS
(
  SELECT
    first_17_char,
    INITCAP(first_17_char) as title_first_17_char,
    last_17_char,
    INITCAP(last_17_char) as title_last_17_char
  FROM text_input
)
SELECT * FROM title_case_text;

![image](https://user-images.githubusercontent.com/89623051/140751693-6e7e6686-0012-4ff0-8c56-9a9b15ef2eb9.png)

--formatting string

--The FM in the code below stands for fill mode which suppresses leading and trailing characters.

WITH numbers (column1) AS (
VALUES
  (123456789),
  (1234567890),  -- What do you think will happen?
  (123456.789),
  (1000),
  (100)
)
SELECT
  -- how many characters is our format below?
  TO_CHAR(column1, '$FM999,999,999')
FROM numbers;

--Padding Strings
--A very common transformation for product related data analytics teams is to “pad” the product numbers of a specific column to make the final output into a specific character --length.

WITH products (product_code) AS (
VALUES
  ('1234-BOX'),
  ('3421-EACH'),
  ('35895-PACK'),
  ('451884-CARTON')
)
SELECT * FROM products;

![image](https://user-images.githubusercontent.com/89623051/140753147-822060d9-96a0-4dd2-aa6a-802c6a5f9b44.png)

--Firstly we’ll want to find the POSITION of the - character so we can apply our LPAD function to the LEFT of our product_code column using the POSITION value and apply the padding transformation to add extra 0 characters in front of our number component.

--We can also subtract 1 from this POSITION value so we ensure we don’t include the - in our output!


WITH products (product_code) AS (
VALUES
  ('1234-BOX'),
  ('3421-EACH'),
  ('35895-PACK'),
  ('451884-CARTON')
)
SELECT
  LPAD(
  -- input text we want to apply the padding on, in this case it's a LEFT output
    LEFT(
      product_code,                       -- column target
      POSITION('-' IN product_code) - 1  -- subtract 1 from the position of dash
    ),
    6,   -- the max length of our padded output column
    '0'  -- the character we will use to "pad" our input text
  ) AS product_id
FROM products;

![image](https://user-images.githubusercontent.com/89623051/140757841-569eb0b0-9407-4fc6-9993-73c470fe4da7.png)

--pattern matching
--Exact Pattern Match
--We can simply use the = in our WHERE filters and CASE WHEN statements to perform this exact match.

WITH test_data (text_value) AS (
VALUES
  ('Hello World!'),
  ('hello World!'),
  ('Hello to the world!'),
  ('Hello, world')
)
SELECT text_value
from test_data
WHERE text_value = 'Hello World!';

![image](https://user-images.githubusercontent.com/89623051/140758764-fa25b7b6-8e66-4071-8faf-c8ecd1f9a3b6.png)

--Case Sensitive Fuzzy Matching
--A wildcard percentage sign '%' character can be used to replace any number of characters in a string field and an underscore wildcard '_' can be used to replace any single ----character.

1. Right '%' Wildcard
WITH test_data (text_value) AS (
VALUES
  ('Hello World!'),
  ('hello World!'),
  ('Hello Santa!')
)
SELECT text_value
from test_data
WHERE text_value LIKE 'Hello%';

![image](https://user-images.githubusercontent.com/89623051/140759466-860af6ff-13fd-413d-b077-044c4f16380c.png)

2. Left '%' Wildcard
WITH test_data (text_value) AS (
VALUES
  ('Hello World!'),
  ('hello World!'),
  ('Hello world!')
)
SELECT text_value
from test_data
WHERE text_value LIKE '%World!';

![image](https://user-images.githubusercontent.com/89623051/140759737-0b95517b-3df9-4b9f-b441-48f5c6f0530c.png)

3. Double '%' Wildcard
WITH test_data (text_value) AS (
VALUES
  ('Hello World!'),
  ('hello World!'),
  ('Hello world!'),
  ('Jello World is amazing')
)
SELECT text_value
from test_data
WHERE text_value LIKE '%ello World%';

![image](https://user-images.githubusercontent.com/89623051/140759894-ae9eb223-2b7f-4981-b2d1-ff75428133f9.png)

4. Multiple '%' Wildcards
WITH test_data (text_value) AS (
VALUES
  ('Hello World!'),
  ('hello World!'),
  ('Hello world!'),
  ('Will this hello Word show up?')
)
SELECT text_value
from test_data
WHERE text_value LIKE '%el%Wor%';

![image](https://user-images.githubusercontent.com/89623051/140760613-a8fc3647-2bf8-441b-9706-9c61af7fceaf.png)

--'_' Wildcards
--We can also use the _ character to match any single character which is very similar to a wildcard but it is explicit with the number of characters to match.

WITH test_data (text_value) AS (
VALUES
  ('Hello World!'),
  ('hello World!'),
  ('Hello world!'),
  ('Jello World')
)
SELECT text_value
from test_data
WHERE text_value LIKE '_ello_World_';

![image](https://user-images.githubusercontent.com/89623051/140760998-6fd15bc4-6744-4267-9216-0394c2e35007.png)

--Case Insensitive Match
WITH test_data (text_value) AS (
VALUES
  ('Hello World!'),
  ('hello World!'),
  ('Hello world!!!!!'),
  ('HEllo world'),
  ('HeLlO WoRlD')
)
SELECT text_value
from test_data
WHERE text_value ILIKE '%hello world%';

![image](https://user-images.githubusercontent.com/89623051/140761897-2d377190-2925-448c-b030-9fb60484e3a0.png)

--NOT LIKE and NOT ILIKE
SELECT COUNT(*)
FROM rentals.film_list
WHERE description NOT ILIKE '%monkey%';

![image](https://user-images.githubusercontent.com/89623051/140762489-b6dd3eea-8bf2-4679-a22b-57286754cace.png)


