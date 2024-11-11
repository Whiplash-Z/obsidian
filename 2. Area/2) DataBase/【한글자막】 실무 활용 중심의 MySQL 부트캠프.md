```mysql
SHOW DATABASES;  
  
# 1. 데이터베이스 생성하기  
# CREATE DATABASE <database_name>;  
CREATE DATABASE pet_shop;  
CREATE DATABASE SlimeStore;  
CREATE DATABASE chicken_coop;  
  
  
# 2. 데이터베이스 삭제하기.  
#  DROP DATABASE <database-name>;  
DROP DATABASE SlimeStore;  
DROP DATABASE pet_shop;  
  
# 3. 데이터베이스 사용하기.  
# USE <database-name>;  
USE chicken_coop;  
  
# 4. 선택된 데이터베이스 확인하기  
# SELECT database();  
SELECT DATABASE();  
  
# 5. 데이터베이스를 생성하고, 정보 확인하기  
CREATE DATABASE pet_shop;  
  
CREATE TABLE cats  
(  
    name VARCHAR(50),  
    age  INT  
);  
  
CREATE TABLE dogs  
(  
    name  VARCHAR(50),  
    breed VARCHAR(50),  
    age   INT  
);  
  
SHOW COLUMNS FROM cats;  
SHOW COLUMNS FROM dogs;  
DESC cats;  
DESC dogs;  
  
# 6. 테이블 삭제하기  
# DROP TABLE <table-name>;  
DROP TABLE cats;  
  
  
# 7. 연습문제(1)  
CREATE TABLE pastries  
(  
    name     VARCHAR(50),  
    quantity INT  
);  
  
DESC pastries;  
  
DROP TABLE pastries;  
  
# 8. 데이터 삽입하기  
CREATE TABLE cats  
(  
    name VARCHAR(50),  
    age  INT  
);  
  
DESC cats;  
  
INSERT INTO cats(name, age)  
VALUES ('Blue Steel', 5);  
  
INSERT INTO cats(name, age)  
VALUES ('Jenkins', 7);  
  
# 9. 데이터 확인하기  
SELECT *  
FROM cats;  
  
# 10. 입력 순서를 수동으로 지정하기.  
INSERT INTO cats(age, name)  
VALUES (2, 'Beth');  
  
# 11. 데이터를 복수로 넣기.  
INSERT INTO cats(name, age)  
VALUES ('Meatball', 5),  
       ('Turkey', 1),  
       ('Potato Face', 15);  
  
  
# 연습문제(2)  
CREATE TABLE people  
(  
    first_name VARCHAR(20),  
    last_name  VARCHAR(20),  
    age        INT  
);  
  
INSERT INTO people(first_name, last_name, age)  
VALUES ('Tina', 'Belcher', 13),  
       ('Bob', 'Belcher', 42),  
       ('Linda', 'belcher', 45),  
       ('Phillip', 'Frond', 38),  
       ('Calvin', 'Fischoeder', 70);  
  
SELECT *  
FROM people;  
DROP TABLE people;  
SHOW TABLES;  
  
# 12. NULL, NOT NULL  
CREATE TABLE cats2  
(  
    name VARCHAR(100) NOT NULL,  
    age  INT          NOT NULL  
);  
  
INSERT INTO cats2(name, age)  
VALUES ('bilbo', 5);  
SELECT *  
FROM cats2;  
DESC cats;  
DESC cats2;  
  
# 13. DEFAULT VALUE  
CREATE TABLE cats3  
(  
    name VARCHAR(20) DEFAULT 'no name provided',  
    age  INT         DEFAULT 99  
);  
  
DESC cats3;  
INSERT INTO cats3(age) VALUE (13);  
INSERT INTO cats3() VALUE ();  
  
SELECT *  
FROM cats3;  
  
CREATE TABLE cats4  
(  
    name VARCHAR(20) NOT NULL DEFAULT 'nunamed',  
    age  INT         NOT NULL DEFAULT 99  
);  
  
# 14. 기본키(Primary Key)  
CREATE TABLE unique_cats  
(  
    cat_id INT PRIMARY KEY,  
    name   VARCHAR(100) NOT NULL,  
    age    INT          NOT NULL  
);  
  
CREATE TABLE unique_cats2  
(  
    cat_id INT,  
    name   VARCHAR(100) NOT NULL,  
    age    INT          NOT NULL,  
    PRIMARY KEY (cat_id)  
);  
  
DESC unique_cats;  
DESC unique_cats2;  
  
  
# 15. AUTO_INCREMENT  
CREATE TABLE unique_cat3  
(  
    cat_id INT AUTO_INCREMENT,  
    name   VARCHAR(100) NOT NULL,  
    age    INT          NOT NULL,  
    PRIMARY KEY (cat_id)  
);  
  
INSERT INTO unique_cat3(name, age)  
VALUES ('bb', 1);  
SELECT *  
FROM unique_cat3;  
  
  
# 연습문제 (3)CREATE TABLE employees  
(  
    id             INT PRIMARY KEY AUTO_INCREMENT,  
    last_name      VARCHAR(100) NOT NULL,  
    first_name     VARCHAR(100) NOT NULL,  
    middle_name    VARCHAR(100),  
    age            INT          NOT NULL,  
    current_status VARCHAR(100) NOT NULL DEFAULT 'employed'  
  
);  
  
INSERT INTO employees(first_name, last_name, age) VALUE ('thomas', 'chickenman', 87);  
SELECT *  
FROM employees;  
  
  
# CRUD START !!  
CREATE TABLE cats  
(  
    cat_id INT AUTO_INCREMENT,  
    name   VARCHAR(100),  
    breed  VARCHAR(100),  
    age    INT,  
    PRIMARY KEY (cat_id)  
);  
  
INSERT INTO cats(name, breed, age)  
VALUES ('Ringo', 'Tabby', 4),  
       ('Cindy', 'Maine Coon', 10),  
       ('Dumbledore', 'Maine Coon', 11),  
       ('Egg', 'Persian', 4),  
       ('Misty', 'Tabby', 13),  
       ('George Michael', 'Ragdoll', 9),  
       ('Jackson', 'Sphynx', 7);  
  
# READ -> SELECT  
SELECT *  
FROM cats;  
SELECT name, breed  
FROM cats;  
  
# WHERE  
SELECT *  
FROM cats  
WHERE age = 4;  
  
SELECT name, age  
FROM cats  
WHERE age = 4;  
  
SELECT *  
FROM cats  
WHERE name = 'egg';  
  
SELECT cat_id  
FROM cats;  
  
SELECT name, breed  
FROM cats;  
  
SELECT name, age  
FROM cats  
WHERE breed = 'Tabby';  
  
SELECT cat_id, age  
FROM cats  
WHERE cat_id = age;  
  
# Aliases  (테이블의 열 이름을 실제로 변경하지는 않음)  
SELECT cat_id AS id, name  
FROM cats;  
  
# UPDATE  
UPDATE cats  
SET breed = 'Shorthail'  
WHERE breed = 'Tabby';  
  
SELECT *  
FROM cats;  
  
# 도전 과제  
SELECT *  
FROM cats  
WHERE name = 'Jack';  
  
UPDATE cats  
SET name = 'Jack'  
WHERE name = 'Jackson';  
  
SELECT *  
FROM cats  
WHERE name = 'Ringo';  
  
UPDATE cats  
SET breed = 'British Shorthair'  
WHERE name = 'Ringo';  
  
  
SELECT *  
FROM cats  
WHERE breed = 'Maine Coon';  
UPDATE cats  
SET age = 12  
WHERE breed = 'Maine Coon';  
  
  
# DELETE  
# CAUTION:: DELETE FROM cats; -> cats 테이블의 모든 행이 다 사라진다.  
DELETE  
FROM cats  
WHERE name = 'Egg';  
  
# 도전과제  
DELETE  
FROM cats  
WHERE age = 4;  
  
DELETE  
FROM cats  
WHERE cat_id = age;  
  
DELETE  
FROM cats;  
  
SELECT *  
FROM cats;  
  
# 섹션6: CRUD 도전과제 !CREATE DATABASE shirts_db;  
USE shirts_db;  
  
CREATE TABLE shirts  
(  
    shirt_id   INT AUTO_INCREMENT PRIMARY KEY,  
    article    VARCHAR(100),  
    color      VARCHAR(100),  
    shirt_size VARCHAR(100),  
    last_worn  INT  
);  
  
INSERT INTO shirts(article, color, shirt_size, last_worn)  
VALUES ('t-shirt', 'white', 'S', 10),  
       ('t-shirt', 'green', 'S', 200),  
       ('polo shirt', 'black', 'M', 10),  
       ('tank top', 'blue', 'S', 50),  
       ('t-shirt', 'pink', 'S', 0),  
       ('polo shirt', 'red', 'M', 5),  
       ('tank top', 'white', 'S', 200),  
       ('tank top', 'blue', 'M', 15);  
  
SELECT *  
FROM shirts;  
  
INSERT INTO shirts(color, article, shirt_size, last_worn)  
VALUES ('Purple', 'polo shirts', 'M', 50);  
  
SELECT article, color  
FROM shirts;  
  
SELECT article, color, shirts.shirt_size, shirts.last_worn  
FROM shirts  
WHERE shirt_size = 'M';  
  
SELECT article, shirt_size  
FROM shirts  
WHERE article = 'polo shirt';  
  
UPDATE shirts  
SET shirt_size = 'L'  
WHERE article = 'polo shirt';  
  
SELECT *  
FROM shirts;  
  
# last worn 15 days ago -> last won 0  
UPDATE shirts  
SET last_worn = 0  
WHERE last_worn = 15;  
  
  
UPDATE shirts  
SET shirt_size = 'XS',  
    color      = 'off white'  
WHERE article = 't-shirt'  
  AND color = 'white';  
  
SELECT *  
FROM shirts  
WHERE last_worn >= 200;  
  
DELETE  
FROM shirts  
WHERE last_worn >= 200;  
  
SELECT *  
FROM shirts  
WHERE article = 'tank top';  
  
DELETE  
FROM shirts  
WHERE article = 'tank top';  
  
DELETE  
FROM shirts;  
  
SELECT *  
FROM shirts;  
  
DROP TABLE shirts;  
  
  
# 섹션 7: 문자열 함수의 세계  
CREATE DATABASE book_shop;  
USE book_shop;  
  
  
CREATE TABLE books  
(  
    book_id        INT NOT NULL AUTO_INCREMENT,  
    title          VARCHAR(100),  
    author_fname   VARCHAR(100),  
    author_lname   VARCHAR(100),  
    released_year  INT,  
    stock_quantity INT,  
    pages          INT,  
    PRIMARY KEY (book_id)  
);  
  
INSERT INTO books (title, author_fname, author_lname, released_year, stock_quantity, pages)  
VALUES ('The Namesake', 'Jhumpa', 'Lahiri', 2003, 32, 291),  
       ('Norse Mythology', 'Neil', 'Gaiman', 2016, 43, 304),  
       ('American Gods', 'Neil', 'Gaiman', 2001, 12, 465),  
       ('Interpreter of Maladies', 'Jhumpa', 'Lahiri', 1996, 97, 198),  
       ('A Hologram for the King: A Novel', 'Dave', 'Eggers', 2012, 154, 352),  
       ('The Circle', 'Dave', 'Eggers', 2013, 26, 504),  
       ('The Amazing Adventures of Kavalier & Clay', 'Michael', 'Chabon', 2000, 68, 634),  
       ('Just Kids', 'Patti', 'Smith', 2010, 55, 304),  
       ('A Heartbreaking Work of Staggering Genius', 'Dave', 'Eggers', 2001, 104, 437),  
       ('Coraline', 'Neil', 'Gaiman', 2003, 100, 208),  
       ('What We Talk About When We Talk About Love: Stories', 'Raymond', 'Carver', 1981, 23, 176),  
       ("Where I'm Calling From: Selected Stories", 'Raymond', 'Carver', 1989, 12, 526),  
       ('White Noise', 'Don', 'DeLillo', 1985, 49, 320),  
       ('Cannery Row', 'John', 'Steinbeck', 1945, 95, 181),  
       ('Oblivion: Stories', 'David', 'Foster Wallace', 2004, 172, 329),  
       ('Consider the Lobster', 'David', 'Foster Wallace', 2005, 92, 343);  
  
SELECT *  
FROM books;  
DESC books;  
  
# CONCAT 다루기  
SELECT CONCAT(books.author_fname, ' ', author_lname) AS "author name"  
FROM books;  
  
SELECT CONCAT_WS('-', title, author_fname, author_lname)  
FROM books;  
  
# SUBSTRING -> 긴 문자열을 작은 문자여롤 반환하는 함수  
SELECT SUBSTRING('Hello World', 1, 5);  
SELECT SUBSTRING('Hello World', 7);  
SELECT SUBSTRING('Hello World', -5);  
SELECT SUBSTR(title, 1, 10) AS 'short title'  
FROM books;  
  
# 문자열 함수 조합하기  
SELECT CONCAT(SUBSTR(title, 1, 10), '...') AS 'short_title'  
FROM books;  
  
SELECT CONCAT(  
               SUBSTR(books.author_fname, 1, 1), '.', SUBSTR(author_lname, 1, 1), '.'  
       ) AS author_initials  
FROM books;  
  
# REPLACE -> 문자열의 일부를 다른 대체 문자열로 바꾸는 함수  
# REPLACE (str, from_str, to_str)  
SELECT REPLACE('Hello World', 'Hell', '$!@#');  
SELECT REPLACE('cheese bread coffee milk', ' ', ' and ');  
SELECT REPLACE(title, ' ', ' - ')  
FROM books;  
  
SELECT title  
FROM books;  
  
# REVERSE -> 제공한 문자열을 거꾸로 출력하는 함수  
SELECT REVERSE('Hello World');  
SELECT REVERSE(author_fname)  
FROM books;  
SELECT CONCAT(author_fname, REVERSE(author_fname))  
FROM books;  
  
# CHAR_LENGTH -> 문자열에 있는 글자수를 알려주는 함수  
SELECT CHAR_LENGTH('Hey!!');  
SELECT CONCAT(author_lname, ' is ', CHAR_LENGTH(author_lname), ' characters long')  
FROM books;  
  
# UPPER(), LOWER()  
SELECT UPPER('Hello World');  
SELECT UCASE('Hello World');  
SELECT LOWER('Hello World');  
SELECT LCASE('Hello World');  
  
SELECT CONCAT('I LOVE THE', UPPER(books.title), '!!!')  
FROM books;  
  
# 기타 문자열 함수  
SELECT INSERT('Hello Bobby', 6, 0, 'There');  
  
SELECT LEFT('omghahalol!', 3);  
  
SELECT RIGHT('omghahalol!', 4);  
  
SELECT REPEAT('ha', 4);  
  
SELECT TRIM('  pickle  ');  
  
# 문자열 함수 도전과제  
SELECT UPPER(REVERSE('Why does my cat look at me with such hatred?'));  
  
SELECT REPLACE(title, ' ', '->') AS 'title'  
FROM books;  
  
SELECT books.author_lname AS forwards, REVERSE(books.author_lname) AS backwards  
FROM books;  
  
SELECT UPPER(CONCAT(author_fname, ' ', author_lname)) AS 'full name in caps'  
FROM books;  
  
SELECT CONCAT(title, ' was released in ', books.released_year) AS 'blurb'  
FROM books;  
  
SELECT title, CHAR_LENGTH(books.title) AS 'character count'  
FROM books;  
  
SELECT CONCAT(SUBSTRING(title, 1, 10), '...')  AS 'short title',  
       CONCAT(author_lname, ',', author_fname) AS 'author',  
       CONCAT(stock_quantity, ' in stock')     AS 'quantity'  
FROM books;  
  
# 섹션 8. 선택사항 정교화하기.  
  
# DISTINCT -> 중복 결과 제거  
SELECT books.author_lname  
FROM books;  
  
SELECT DISTINCT books.author_lname  
FROM books;  
  
SELECT books.released_year  
FROM books;  
  
SELECT DISTINCT books.released_year  
FROM books;  
  
SELECT DISTINCT CONCAT(books.author_fname, ' ', books.author_lname)  
FROM books;  
  
SELECT DISTINCT author_fname, books.author_lname  
FROM books;  
  
# ORDER BY  
SELECT books.book_id, title, books.author_lname  
FROM books;  
  
SELECT books.book_id, author_fname, books.author_lname, released_year  
FROM books  
ORDER BY released_year DESC;  
  
  
SELECT author_lname, released_year, title  
FROM books  
ORDER BY author_lname, released_year;  
  
# LIMIT -> 반환되는 결과의 수를 조절  
SELECT book_id, title, books.released_year  
FROM books  
ORDER BY book_id  
LIMIT 10, 3;  
  
# LIKE -> 기본 검색을 수행하는 연산자 (WHERE처럼 정확히 일치하는 항목 기반 X)SELECT title, author_fname, author_lname  
FROM books  
WHERE author_fname = 'David';  
  
SELECT title, author_fname, author_lname  
FROM books  
WHERE author_fname LIKE '%da%';  
  
SELECT *  
FROM books  
WHERE title LIKE '%:%';  
  
SELECT *  
FROM books  
WHERE author_fname LIKE '_____';  
  
SELECT *  
FROM books  
WHERE author_fname LIKE '_a_';  
  
SELECT *  
FROM books  
WHERE title LIKE '%\%%';  
  
SELECT *  
FROM books  
WHERE title LIKE '%\_%';  
  
  
# 섹션 8 연습문제  
# 1. Select All Story Collections 'Titles that contain 'stories'  
SELECT title  
FROM books  
WHERE title LIKE '%stories';  
  
# 2. Find the Longest Book, Print Out Title and Page Count  
SELECT title, pages  
FROM books  
ORDER BY pages DESC  
LIMIT 1;  
  
# 3. Print a summary containing the title and year, for the 3 most recent books.  
SELECT CONCAT(title, ' - ', released_year) AS 'summary'  
FROM books  
ORDER BY released_year DESC  
LIMIT 3;  
  
# 4. Find all books with an author_lname that contains a space(" ")  
SELECT title, author_lname  
FROM books  
WHERE author_lname LIKE '% %';  
  
# 5. Find The 3 Books With The Lowest Stock, select title, year and stock  
SELECT title, released_year, stock_quantity  
FROM books  
ORDER BY stock_quantity, title  
LIMIT 3;  
  
# 6. Print title and author_lname, sorted first by author_lname and then by title.  
SELECT title, author_lname  
FROM books  
ORDER BY author_lname, title;  
  
# 7. Sorted Alphabetically By Last Name  
SELECT UPPER(CONCAT('my favorite author is ', author_fname, ' ', author_lname, '!')) AS 'yell'  
FROM books  
ORDER BY author_lname;  
  
  
# 집계함수(Aggregate Functions): 여러 데이터를 대상으로 한 번에 수행하는 작업  
  
SELECT COUNT(*)  
FROM books;  
  
SELECT COUNT(released_year)  
FROM books;  
  
SELECT COUNT(DISTINCT released_year)  
FROM books;  
  
# How many titles contain "the"?  
SELECT COUNT(*)  
FROM books  
WHERE title LIKE '%the%';  
  
# GROUP BY: 동일한 데이터를 요약 혹은 집계해서 하나의 행으로 만든다.  
# 집계 작업을 할 때는 공통적인 요소가 있는 것을 대상으로 수행할 수 있다.  
SELECT author_lname  
FROM books  
GROUP BY author_lname;  
  
SELECT DISTINCT author_lname  
FROM books;  
  
SELECT author_lname, COUNT(*) AS books_written  
FROM books  
GROUP BY author_lname  
ORDER BY books_written DESC;  
  
SELECT released_year, COUNT(*)  
FROM books  
GROUP BY released_year;  
  
# MIN & MAX  
SELECT MIN(released_year)  
FROM books;  
  
SELECT MAX(pages)  
FROM books;  
  
SELECT title, pages  
FROM books  
ORDER BY pages DESC  
LIMIT 1;  
  
SELECT title, pages  
FROM books  
WHERE pages = (SELECT MAX(pages)  
               FROM books);  
  
SELECT title, released_year  
FROM books  
WHERE released_year = (SELECT MIN(released_year) FROM books);  
  
  
# 다중열 GROUP BYSELECT author_fname, author_lname, COUNT(*)  
FROM books  
GROUP BY author_lname, author_fname;  
  
SELECT CONCAT(author_fname, ' ', author_lname) AS author, COUNT(*)  
FROM books  
GROUP BY author;  
  
# MIN & MAX with GROUP BY  
  
SELECT author_lname, MIN(released_year)  
FROM books  
GROUP BY author_lname;  
  
SELECT author_lname, MAX(released_year), MIN(released_year)  
FROM books  
GROUP BY author_lname;  
  
SELECT author_lname,  
       COUNT(*)           AS books_written,  
       MAX(released_year) AS latest_release,  
       MIN(released_year) AS earliest_release,  
       MAX(pages)         AS longest_page_count  
FROM books  
GROUP BY author_lname;  
  
SELECT author_lname,  
       author_fname,  
       COUNT(*)           AS books_written,  
       MAX(released_year) AS latest_release,  
       MIN(released_year) AS earliest_release  
FROM books  
GROUP BY author_lname, author_fname;  
  
USE book_shop;  
  
SELECT *  
FROM books;  
  
SELECT SUM(pages)  
FROM books;  
  
SELECT author_lname, SUM(pages), COUNT(*)  
FROM books  
GROUP BY author_lname;  
  
  
SELECT released_year, AVG(stock_quantity), COUNT(*)  
FROM books  
GROUP BY released_year;  
  
  
SELECT released_year AS 'year',  
       COUNT(*)      AS '# books',  
       AVG(pages)    AS 'avg pages'  
FROM books  
GROUP BY released_year  
ORDER BY released_year;  
  
SHOW DATABASES;  
  
# CHAR & VARCHAR  
# VARCHAR는 다양한 크기의 텍스트를 최상의 방식으로 저장ㅎ랄 수 있도록 최적화되어 있다. (고정되지 않은 값)  
# CHAR: 크기가 같거나, 비슷한 텍스트를 저장해야 하는 경우 (주 이름의 약어, 콜로라도(CO), 캘리포니아(CA), 알래스카(AK) : 항상 2글자(크기 고정)  
  
CREATE TABLE friends  
(  
    name VARCHAR(10)  
);  
  
INSERT INTO friends (name)  
VALUES ('tom'),  
       ('juan pablo'),  
       ('james');  
  
SELECT *  
FROM friends;  
  
  
CREATE TABLE states  
(  
    abbr CHAR(2)  
);  
  
INSERT INTO states (abbr)  
VALUES ('CA'),  
       ('NY');  
# 항상 두 개의 문자를 저장하도록, 가능한 한 최소한의 메모리를 차지하도록 설계되어 있음.  
  
SELECT *  
FROM states;  
  
  
DESC states;  
INSERT INTO states (abbr)  
VALUES ('X');  
  
INSERT INTO states(abbr)  
VALUES ('Ohio');  
  
  
SELECT *, CHAR_LENGTH(abbr)  
FROM states;  
  
  
DESC friends;  
  
INSERT INTO friends (name)  
VALUES ('asdf asdf asdf asdf asdf');  
  
  
# NUMBERS  
# 1. TINYINT / UNSIGNED (OPTION)  
CREATE TABLE parent  
(  
    children TINYINT  
);  
  
DESC parent;  
INSERT INTO parent (children)  
VALUES (2),  
       (7),  
       (0);  
INSERT INTO parent (children)  
VALUES (123412342134);  
  
DROP TABLE parent;  
  
CREATE TABLE parent  
(  
    children TINYINT UNSIGNED # 양수 값만 저장할 수 있게 하는 방법  
);  
INSERT INTO parent (children)  
VALUES (3); # 가능  
INSERT INTO parent (children)  
VALUES (-3); # 불가능  
DESC parent;  
  
# 2. DECIMAL: 정확한 소수를 저장할 수 있다. (가장 정확한 유형)  
# DECIMAL(5,2) = DECIMAL(할당할 총 자릿수, 소수점 뒤에 오는 자릿수) = DECIMAL(최대 5자리 숫자, 소수점 최대 2번째까지)  
# (ex: 999.99), (0.1) => 가능  
# 1520.1 => 불가능 (소수점 앞에는 최대 3자리, 뒤에는 최대 2자리)  
  
CREATE TABLE products  
(  
    price DECIMAL(5, 2)  
);  
  
INSERT INTO products (price)  
VALUES (4.50);  
  
INSERT INTO products (price)  
    VALUE (456.99);  
  
INSERT INTO products (price)  
VALUES (8347.1); # 불가능  
  
INSERT INTO products (price)  
VALUES (5.026123123123123123); # 소수점 3번째 자리가 반올림 되어 5.03으로 저장됨.  
  
INSERT INTO products (price)  
VALUES (99.999); # 소수점 3번째 자리가 반올림 되어 5.03으로 저장됨.  
  
show WARNINGS ;  
  
  
SELECT *  
FROM products;  
  
# 3. FLOAT & DOUBLE  
# DECIMAL과 차이점: DECIMAL 데이터 유형은 숫자를 더 정확하게 나타낼 수 있지만, 더 많은 공간을 차지하게 된다.  
# FLOAT, DOUBLE을 사용하면 더 적은 공간으로도 더 큰 수나 소수점 아래 숫자가 더 많은 수를 저장할 수 있다. (정확성은 떨어짐)  
# DECIMAL은 메모리에서 숫자를 십진수로 저장하고, 부동 소수점 숫자들(FLOAT, DOUBLE)은 그렇지 않다.  
  
CREATE TABLE nums(  
    x FLOAT,  
    y DOUBLE  
);  
  
INSERT INTO nums (x, y) VALUE (1.123, 1.123);  
INSERT INTO nums (x, y) VALUE (1.123456789123456789, 1.123456789123456789); # FLOAT는 7번째부터 정밀도 문제가 발생한다.  
SELECT *  
FROM nums;  
  
  
# 4. Dates & TImes & DATETIME  
# Date(YYYY-MM-DD) Format  
# Time(HH:MM:SS) Format  
# DATETIME(YYYY-MM-DD HH:MM:SS) Format  
CREATE TABLE people(  
    name VARCHAR(100),  
    birthdate DATE,  
    birthtime TIME,  
    birthdt DATETIME  
);  
  
desc people;  
  
INSERT INTO people (name, birthdate, birthtime, birthdt)  
VALUES ('Elton', '2000-12-25', '11:00:00', '2000-12-25 11:00:00');  
  
INSERT INTO people (name, birthdate, birthtime, birthdt)  
VALUES ('Lulu', '1985-04-11', '9:45:10', '1985-04-11 9:45:10');  
  
INSERT INTO people (name, birthdate, birthtime, birthdt)  
VALUES ('Juan', '2020-08-15', '23:59:10', '2020-08-15 23:59:10');  
  
  
  
SELECT *  
FROM people;  
  
SELECT  
    birthdate,  
    DAY(birthdate),  
    DAYOFWEEK(birthdate),  
    DAYOFYEAR(birthdate)  
FROM people;  
  
SELECT  
     birthdate,  
    MONTHNAME(birthdate),  
    YEAR(birthdate)  
FROM people;  
  
# 날짜 양식 바꾸기  
SELECT birthdate, DATE_FORMAT(birthdate, '%a %b %D') FROM people;  
  
SELECT birthdt, DATE_FORMAT(birthdt, '%H:%i') FROM people;  
  
SELECT birthdt, DATE_FORMAT(birthdt, 'BORN ON: %r') FROM people;  
  
  
SELECT CURTIME();  
SELECT NOW();  
SELECT CURDATE();  
INSERT INTO people (name, birthdate, birthtime, birthdt)  
VALUES ('Hazel', CURDATE(), CURTIME(), NOW());  
  
  
# TIMESTAMP: 날짜와 시간을 결합한 것.  
# DTAETIME과 무엇이 다를까?  
/**  
  1. TIMESTAMP는 저장 공간(메모리)을 덜 차지한다.  
  2. 더 작은 범위의 날짜를 지원하기 때문이다.(1970년 1월 1일 ~ 2038년 1월 19일 3시 14분 UTC), DATETIME: 1000년 ~ 9990년까지 가능  
  3. DATETIME과 TIMESTAMP의 범위가 겹칠 때는 아무거나 사용해도 상관없다.  
 */  
CREATE TABLE captions(  
    text VARCHAR(150),  
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP  
);  
DESC captions;  
  
INSERT INTO captions (text)  
VALUES ('just me and the girls chillin');  
  
INSERT INTO captions (text)  
VALUES ('beautiful sunset');  
  
  
CREATE TABLE captions2(  
    text VARCHAR(150),  
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
    updated_at TIMESTAMP ON UPDATE CURRENT_TIMESTAMP  
);  
  
INSERT INTO captions2 (text)  
VALUES ('i love life!');  
  
SELECT *  
FROM captions2;  
  
SELECT CURRENT_TIME;  
SELECT CURRENT_DATE;  
  
  
  
UPDATE captions2  
SET text='i love live !!!!!';  
  
  
  
  
SELECT *  
FROM captions;
```