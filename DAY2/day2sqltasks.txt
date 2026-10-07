--INNER JOIN--
SELECT Books.book_name, Members.member_name,borrow_date
FROM Books INNER JOIN Borrow
ON Books.book_id = Borrow.book_id
INNER JOIN Members 
ON Borrow.member_id = Members.member_id;


SELECT Books.book_name, Members.member_name, Borrow.borrow_date
FROM Books
INNER JOIN Borrow
    ON Books.book_id = Borrow.book_id
INNER JOIN Members
    ON Borrow.member_id = Members.member_id;

SELECT Books.book_name,
       Books.author,
       Members.member_name,
       Members.city
FROM Books
INNER JOIN Borrow
    ON Books.book_id = Borrow.book_id
INNER JOIN Members
    ON Borrow.member_id = Members.member_id;

SELECT Books.book_name
FROM Books
INNER JOIN Borrow
    ON Books.book_id = Borrow.book_id
INNER JOIN Members
    ON Borrow.member_id = Members.member_id
WHERE Members.city = 'Chennai';

SELECT Books.book_name
FROM Books
INNER JOIN Borrow
    ON Books.book_id = Borrow.book_id
INNER JOIN Members
    ON Borrow.member_id = Members.member_id
WHERE Members.member_id = 207;

SELECT Members.member_name
FROM Members
INNER JOIN Borrow
    ON Members.member_id = Borrow.member_id
INNER JOIN Books
    ON Borrow.book_id = Books.book_id
WHERE Books.category = 'Technology';

SELECT Books.book_name, Members.member_name
FROM Books
INNER JOIN Borrow
    ON Books.book_id = Borrow.book_id
INNER JOIN Members
    ON Borrow.member_id = Members.member_id;

--LEFT JOIN--

SELECT Borrow.*, Books.book_name
FROM Books
RIGHT JOIN Borrow
    ON Books.book_id = Borrow.book_id;

SELECT Books.book_name
FROM Books
LEFT JOIN Borrow
    ON Books.book_id = Borrow.book_id;

SELECT Members.member_name, Books.book_name
FROM Members
LEFT JOIN Borrow
    ON Members.member_id = Borrow.member_id
LEFT JOIN Books
    ON Borrow.book_id = Books.book_id;

SELECT Books.book_name
FROM Books
LEFT JOIN Borrow
    ON Books.book_id = Borrow.book_id
WHERE Borrow.book_id IS NULL;

SELECT Members.member_name
FROM Members
LEFT JOIN Borrow
    ON Members.member_id = Borrow.member_id
WHERE Borrow.member_id IS NULL;

--RIGHT JOIN--

SELECT Borrow.*, Books.book_name
FROM Books
RIGHT JOIN Borrow
    ON Books.book_id = Borrow.book_id;

SELECT Borrow.*, Members.member_name
FROM Borrow
INNER JOIN Members
    ON Borrow.member_id = Members.member_id;

SELECT Members.member_name, Borrow.*
FROM Borrow
RIGHT JOIN Members
    ON Borrow.member_id = Members.member_id;

--CROSS JOIN--

SELECT Books.book_name, Members.member_name
FROM Books
CROSS JOIN Members;

SELECT COUNT(*) AS total_combinations
FROM Books
CROSS JOIN Members;

SELECT Members.member_name, Books.book_name
FROM Members
CROSS JOIN Books
WHERE Books.category = 'Technology';

--JOIN + GROUP BY--

SELECT Members.member_name,
       COUNT(Borrow.book_id) AS books_borrowed
FROM Members
INNER JOIN Borrow
    ON Members.member_id = Borrow.member_id
GROUP BY Members.member_id, Members.member_name;

SELECT Books.book_name,
       COUNT(Borrow.member_id) AS number_of_members
FROM Books
INNER JOIN Borrow
    ON Books.book_id = Borrow.book_id
GROUP BY Books.book_id, Books.book_name;

SELECT Books.book_name,
       COUNT(Borrow.member_id) AS borrow_count
FROM Books
INNER JOIN Borrow
    ON Books.book_id = Borrow.book_id
GROUP BY Books.book_id, Books.book_name
ORDER BY borrow_count DESC
LIMIT 1;

SELECT Members.member_name,
       COUNT(Borrow.book_id) AS books_borrowed
FROM Members
INNER JOIN Borrow
    ON Members.member_id = Borrow.member_id
GROUP BY Members.member_id, Members.member_name
HAVING COUNT(Borrow.book_id) > 2;

SELECT Books.category,
       COUNT(Borrow.book_id) AS times_borrowed
FROM Books
INNER JOIN Borrow
    ON Books.book_id = Borrow.book_id
GROUP BY Books.category;

SELECT Books.category,
       COUNT(Borrow.book_id) AS total_borrowed
FROM Books
INNER JOIN Borrow
    ON Books.book_id = Borrow.book_id
GROUP BY Books.category;

--C1--

SELECT *
FROM Books
WHERE price > (
    SELECT AVG(price)
    FROM Books
);

SELECT *
FROM Books
WHERE price < (
    SELECT AVG(price)
    FROM Books
);

SELECT *
FROM Books
WHERE price = (
    SELECT MAX(price)
    FROM Books
);

SELECT *
FROM Books
WHERE price = (
    SELECT MIN(price)
    FROM Books
);

SELECT *
FROM Books
WHERE price = (
    SELECT price
    FROM Books
    WHERE book_id = 2
);

SELECT *
FROM Books
WHERE stock_quantity > (
    SELECT AVG(stock_quantity)
    FROM Books
);

--C2--

SELECT *
FROM Books
WHERE category IN (
    SELECT category
    FROM Books
    GROUP BY category
    HAVING COUNT(*) > 1
);

SELECT *
FROM Books
WHERE author IN (
    SELECT author
    FROM Books
    GROUP BY author
    HAVING COUNT(*) > 1
);

SELECT *
FROM Books
WHERE category IN (
    SELECT category
    FROM Books
    GROUP BY category
    HAVING AVG(price) > 500
);

SELECT *
FROM Members
WHERE member_id IN (
    SELECT Borrow.member_id
    FROM Borrow
    INNER JOIN Books
        ON Borrow.book_id = Books.book_id
    WHERE Books.category = 'Technology'
);

--C3--

SELECT *
FROM Books
WHERE book_id NOT IN (
    SELECT book_id
    FROM Borrow
);

SELECT *
FROM Members
WHERE member_id NOT IN (
    SELECT member_id
    FROM Borrow
);

SELECT DISTINCT author
FROM Books
WHERE book_id NOT IN (
    SELECT book_id
    FROM Borrow
);

--C4--

SELECT *
FROM Books
WHERE EXISTS (
    SELECT 1
    FROM Borrow
    WHERE Borrow.book_id = Books.book_id
);

SELECT *
FROM Members
WHERE EXISTS (
    SELECT 1
    FROM Borrow
    WHERE Borrow.member_id = Members.member_id
);

SELECT *
FROM Books
WHERE (
    SELECT COUNT(*)
    FROM Borrow
    WHERE Borrow.book_id = Books.book_id
) >= 2;

--D1--

WITH avg_price AS (
    SELECT AVG(price) AS average_price
    FROM Books
)
SELECT *
FROM Books
WHERE price > (
    SELECT average_price
    FROM avg_price
);

WITH category_avg AS (
    SELECT category,
           AVG(price) AS average_price
    FROM Books
    GROUP BY category
)
SELECT *
FROM category_avg;

WITH category_avg AS (
    SELECT category,
           AVG(price) AS average_price
    FROM Books
    GROUP BY category
)
SELECT Books.*
FROM Books
INNER JOIN category_avg
    ON Books.category = category_avg.category
WHERE Books.price > category_avg.average_price;

WITH category_stock AS (
    SELECT category,
           SUM(stock_quantity) AS total_stock
    FROM Books
    GROUP BY category
)
SELECT *
FROM category_stock;

WITH category_stock AS (
    SELECT category,
           SUM(stock_quantity) AS total_stock
    FROM Books
    GROUP BY category
)
SELECT *
FROM category_stock
WHERE total_stock > 20;

WITH category_max AS (
    SELECT category,
           MAX(price) AS max_price
    FROM Books
    GROUP BY category
)
SELECT Books.*
FROM Books
INNER JOIN category_max
    ON Books.category = category_max.category
   AND Books.price = category_max.max_price;

WITH author_count AS (
    SELECT author,
           COUNT(*) AS book_count
    FROM Books
    GROUP BY author
)
SELECT *
FROM author_count;

WITH author_count AS (
    SELECT author,
           COUNT(*) AS book_count
    FROM Books
    GROUP BY author
)
SELECT *
FROM author_count
WHERE book_count > 1;

--D2--

SELECT book_name,
       price,
       RANK() OVER (ORDER BY price DESC) AS price_rank
FROM Books;

SELECT book_name,
       price,
       RANK() OVER (ORDER BY price ASC) AS price_rank
FROM Books;

SELECT book_name,
       price,
       ROW_NUMBER() OVER (ORDER BY price) AS row_number
FROM Books;

SELECT book_name,
       category,
       price,
       RANK() OVER (
           PARTITION BY category
           ORDER BY price DESC
       ) AS category_rank
FROM Books;

SELECT book_name,
       category,
       price,
       DENSE_RANK() OVER (
           PARTITION BY category
           ORDER BY price DESC
       ) AS category_rank
FROM Books;

SELECT book_name,
       category,
       price,
       AVG(price) OVER (
           PARTITION BY category
       ) AS category_average
FROM Books;

SELECT book_name,
       category,
       price,
       MAX(price) OVER (
           PARTITION BY category
       ) AS category_highest_price
FROM Books;

SELECT book_name,
       category,
       price,
       MIN(price) OVER (
           PARTITION BY category
       ) AS category_lowest_price
FROM Books;

SELECT book_name,
       category,
       price,
       price - AVG(price) OVER (
           PARTITION BY category
       ) AS difference_from_average
FROM Books;

SELECT book_name,
       stock_quantity,
       SUM(stock_quantity) OVER (
           ORDER BY book_id
       ) AS cumulative_stock
FROM Books;

SELECT book_name,
       category,
       stock_quantity,
       SUM(stock_quantity) OVER (
           PARTITION BY category
           ORDER BY book_id
       ) AS cumulative_stock
FROM Books;

SELECT book_name,
       price,
       LAG(price) OVER (
           ORDER BY book_id
       ) AS previous_price
FROM Books;

SELECT book_name,
       price,
       LEAD(price) OVER (
           ORDER BY book_id
       ) AS next_price
FROM Books;

SELECT book_name,
       price,
       LAG(price) OVER (
           ORDER BY book_id
       ) AS previous_price,
       price - LAG(price) OVER (
           ORDER BY book_id
       ) AS price_difference
FROM Books;

--D3--

SELECT book_name,
       price,
       CASE
           WHEN price > 600 THEN 'Expensive'
           ELSE 'Affordable'
       END AS price_category
FROM Books;

SELECT book_name,
       price,
       CASE
           WHEN price < 400 THEN 'Low'
           WHEN price BETWEEN 400 AND 700 THEN 'Medium'
           ELSE 'High'
       END AS price_category
FROM Books;

SELECT book_name,
       stock_quantity,
       CASE
           WHEN stock_quantity = 0 THEN 'Out of Stock'
           WHEN stock_quantity BETWEEN 1 AND 5 THEN 'Low Stock'
           ELSE 'Available'
       END AS stock_status
FROM Books;

SELECT book_name,
       price,
       CASE
           WHEN price < 400 THEN 'Low'
           WHEN price BETWEEN 400 AND 700 THEN 'Medium'
           ELSE 'High'
       END AS price_category
FROM Books;

SELECT book_name,
       stock_quantity,
       CASE
           WHEN stock_quantity = 0 THEN 'Out of Stock'
           WHEN stock_quantity BETWEEN 1 AND 5 THEN 'Low Stock'
           ELSE 'Available'
       END AS stock_status
FROM Books;

SELECT
    CASE
        WHEN price < 400 THEN 'Low'
        WHEN price BETWEEN 400 AND 700 THEN 'Medium'
        ELSE 'High'
    END AS price_category,
    COUNT(*) AS book_count
FROM Books
GROUP BY
    CASE
        WHEN price < 400 THEN 'Low'
        WHEN price BETWEEN 400 AND 700 THEN 'Medium'
        ELSE 'High'
    END;

SELECT book_name,
       price,
       CASE
           WHEN price > 700 THEN price - 100
           ELSE price
       END AS discounted_price
FROM Books;

--D4--

CREATE VIEW Technology_Books AS
SELECT *
FROM Books
WHERE category = 'Technology';

SELECT * FROM Technology_Books;

CREATE VIEW Expensive_Books AS
SELECT *FROM Books WHERE price > 600;

SELECT *FROM Expensive_Books;

CREATE VIEW Available_Books AS SELECT * FROM Books WHERE stock_quantity > 0;

SELECT * FROM Available_Books;

CREATE VIEW Library_Borrow_Details AS
SELECT Books.book_name,
       Books.author,
       Members.member_name,
       Members.city,
       Borrow.borrow_date
FROM Books
INNER JOIN Borrow
    ON Books.book_id = Borrow.book_id
INNER JOIN Members
    ON Borrow.member_id = Members.member_id;

SELECT *FROM Library_Borrow_Details;

CREATE VIEW Category_Average_Price AS
SELECT category,
       AVG(price) AS average_price
FROM Books
GROUP BY category;

SELECT * FROM Category_Average_Price;

CREATE VIEW Borrowing_Members AS
SELECT DISTINCT Members.member_id,
       Members.member_name,
       Members.city
FROM Members
INNER JOIN Borrow
    ON Members.member_id = Borrow.member_id;

SELECT * FROM Borrowing_Members;

SELECT column_name,
       data_type
FROM information_schema.columns
WHERE table_name = 'technology_books';

CREATE OR REPLACE VIEW Technology_Books AS
SELECT book_id,
       book_name,
       author,
       price,
       category,
       stock_quantity
FROM Books
WHERE category = 'Technology';
SELECT *FROM Technology_Books;

DROP VIEW Available_Books;

--D5--

CREATE OR REPLACE FUNCTION GetAllBooks()
RETURNS TABLE (
    book_id INT,
    book_name VARCHAR,
    author VARCHAR,
    price DECIMAL(10,2),
    category VARCHAR,
    stock_quantity INT
)
LANGUAGE SQL
AS $$
    SELECT book_id, book_name, author, price, category, stock_quantity
    FROM Books;
$$;

SELECT * FROM GetAllBooks();

CREATE OR REPLACE FUNCTION GetAllMembers()
RETURNS TABLE (
    member_id INT,
    member_name VARCHAR,
    city VARCHAR,
    phone VARCHAR,
    email VARCHAR
)
LANGUAGE SQL
AS $$
    SELECT member_id, member_name, city, phone, email
    FROM Members;
$$;

SELECT * FROM GetAllMembers();

CREATE OR REPLACE FUNCTION GetBooksByCategory(p_category VARCHAR)
RETURNS TABLE (
    book_id INT,
    book_name VARCHAR,
    author VARCHAR,
    price DECIMAL(10,2),
    category VARCHAR,
    stock_quantity INT
)
LANGUAGE SQL
AS $$
    SELECT book_id, book_name, author, price, category, stock_quantity
    FROM Books
    WHERE category = p_category;
$$;

SELECT * FROM GetBooksByCategory('Technology');

CREATE OR REPLACE FUNCTION GetBooksByAuthor(p_author VARCHAR)
RETURNS TABLE (
    book_id INT,
    book_name VARCHAR,
    author VARCHAR,
    price DECIMAL(10,2),
    category VARCHAR,
    stock_quantity INT
)
LANGUAGE SQL
AS $$
    SELECT book_id, book_name, author, price, category, stock_quantity
    FROM Books
    WHERE author = p_author;
$$;
SELECT * FROM GetBooksByAuthor('Ravi Kumar');

CREATE OR REPLACE FUNCTION GetBooksAbovePrice(p_price DECIMAL)
RETURNS TABLE (
    book_id INT,
    book_name VARCHAR,
    author VARCHAR,
    price DECIMAL(10,2),
    category VARCHAR,
    stock_quantity INT
)
LANGUAGE SQL
AS $$
    SELECT book_id, book_name, author, price, category, stock_quantity
    FROM Books
    WHERE price > p_price;
$$;
SELECT * FROM GetBooksAbovePrice(600);

CREATE OR REPLACE FUNCTION GetMemberBorrowDetails(p_member_id INT)
RETURNS TABLE (
    borrow_id INT,
    book_id INT,
    member_id INT,
    borrow_date DATE
)
LANGUAGE SQL
AS $$
    SELECT borrow_id, book_id, member_id, borrow_date
    FROM Borrow
    WHERE member_id = p_member_id;
$$;
SELECT * FROM GetMemberBorrowDetails(201);

CREATE OR REPLACE FUNCTION GetCategoryBooks(
    p_category VARCHAR,
    p_price_limit DECIMAL
)
RETURNS TABLE (
    book_id INT,
    book_name VARCHAR,
    author VARCHAR,
    price DECIMAL(10,2),
    category VARCHAR,
    stock_quantity INT
)
LANGUAGE SQL
AS $$
    SELECT book_id, book_name, author, price, category, stock_quantity
    FROM Books
    WHERE category = p_category
      AND price <= p_price_limit;
$$;
SELECT * FROM GetCategoryBooks('Technology', 700);

--E--

SELECT DISTINCT Members.member_name
FROM Members
INNER JOIN Borrow
    ON Members.member_id = Borrow.member_id
INNER JOIN Books
    ON Borrow.book_id = Books.book_id
WHERE Books.price > (
    SELECT AVG(price)
    FROM Books
);

SELECT book_name, category, price, author
FROM Books
WHERE price = (
    SELECT MAX(B2.price)
    FROM Books B2
    WHERE B2.category = Books.category
);

SELECT category, AVG(price) AS average_price
FROM Books
GROUP BY category
HAVING AVG(price) > (
    SELECT AVG(price)
    FROM Books
);

SELECT Members.member_name,
       COUNT(Borrow.book_id) AS books_borrowed
FROM Members
INNER JOIN Borrow
    ON Members.member_id = Borrow.member_id
GROUP BY Members.member_id, Members.member_name
HAVING COUNT(Borrow.book_id) > 1;

SELECT *
FROM Books
WHERE price > 500
  AND book_id NOT IN (
      SELECT book_id
      FROM Borrow
  );

SELECT book_name, price
FROM (
    SELECT book_name,
           price,
           RANK() OVER (ORDER BY price DESC) AS price_rank
    FROM Books
) AS ranked_books
WHERE price_rank <= 3;

SELECT category,
       COUNT(*) AS total_books,
       AVG(price) AS average_price,
       SUM(stock_quantity) AS total_stock
FROM Books
GROUP BY category;

SELECT book_name,
       category,
       price,
       AVG(price) OVER (
           PARTITION BY category
       ) AS category_average_price,
       price - AVG(price) OVER (
           PARTITION BY category
       ) AS difference_from_average
FROM Books;

SELECT Members.member_name,
       COUNT(Borrow.book_id) AS books_borrowed
FROM Members
LEFT JOIN Borrow
    ON Members.member_id = Borrow.member_id
GROUP BY Members.member_id, Members.member_name;

WITH member_counts AS (
    SELECT Members.member_id,
           Members.member_name,
           COUNT(Borrow.book_id) AS books_borrowed
    FROM Members
    LEFT JOIN Borrow
        ON Members.member_id = Borrow.member_id
    GROUP BY Members.member_id, Members.member_name
)
SELECT *
FROM member_counts
WHERE books_borrowed > (
    SELECT AVG(books_borrowed)
    FROM member_counts
);

SELECT member_name,
       book_name,
       price
FROM (
    SELECT Members.member_name,
           Books.book_name,
           Books.price,
           RANK() OVER (
               PARTITION BY Members.member_id
               ORDER BY Books.price DESC
           ) AS price_rank
    FROM Members
    INNER JOIN Borrow
        ON Members.member_id = Borrow.member_id
    INNER JOIN Books
        ON Borrow.book_id = Books.book_id
) AS ranked
WHERE price_rank = 1;

SELECT category,
       COUNT(*) AS total_books,
       AVG(price) AS average_price
FROM Books
GROUP BY category
HAVING COUNT(*) >= 2
   AND AVG(price) > 500;

SELECT book_name,
       category,
       price
FROM (
    SELECT book_name,
           category,
           price,
           RANK() OVER (
               PARTITION BY category
               ORDER BY price DESC
           ) AS price_rank
    FROM Books
) AS ranked
WHERE price_rank <= 2;

SELECT *
FROM Books
WHERE price > (
    SELECT AVG(B2.price)
    FROM Books B2
    WHERE B2.category = Books.category
)
AND stock_quantity > 5;

CREATE OR REPLACE VIEW Book_Borrow_Count AS
SELECT Books.book_name,
       Books.category,
       Books.price,
       Books.stock_quantity AS stock,
       COUNT(Borrow.borrow_id) AS borrow_count
FROM Books
LEFT JOIN Borrow
    ON Books.book_id = Borrow.book_id
GROUP BY Books.book_id,
         Books.book_name,
         Books.category,
         Books.price,
         Books.stock_quantity;
SELECT * FROM Book_Borrow_Count;

CREATE OR REPLACE FUNCTION GetCategoryBooksSorted(
    p_category VARCHAR
)
RETURNS TABLE (
    book_id INT,
    book_name VARCHAR,
    author VARCHAR,
    price DECIMAL(10,2),
    category VARCHAR,
    stock_quantity INT
)
LANGUAGE SQL
AS $$
    SELECT book_id,
           book_name,
           author,
           price,
           category,
           stock_quantity
    FROM Books
    WHERE category = p_category
    ORDER BY price DESC;
$$;
SELECT * FROM GetCategoryBooksSorted('Technology');

CREATE OR REPLACE FUNCTION GetBooksByPriceRange(
    p_min_price DECIMAL,
    p_max_price DECIMAL
)
RETURNS TABLE (
    book_id INT,
    book_name VARCHAR,
    author VARCHAR,
    price DECIMAL(10,2),
    category VARCHAR,
    stock_quantity INT
)
LANGUAGE SQL
AS $$
    SELECT book_id,
           book_name,
           author,
           price,
           category,
           stock_quantity
    FROM Books
    WHERE price BETWEEN p_min_price AND p_max_price;
$$;

SELECT * FROM GetBooksByPriceRange(400, 700);

WITH category_borrow_count AS (
    SELECT Books.category,
           COUNT(Borrow.book_id) AS borrowed_books
    FROM Books
    INNER JOIN Borrow
        ON Books.book_id = Borrow.book_id
    GROUP BY Books.category
)
SELECT *
FROM category_borrow_count
WHERE borrowed_books > 2;

SELECT
    CASE
        WHEN price < 400 THEN 'Low'
        WHEN price BETWEEN 400 AND 700 THEN 'Medium'
        ELSE 'High'
    END AS price_range,
    COUNT(*) AS book_count
FROM Books
GROUP BY
    CASE
        WHEN price < 400 THEN 'Low'
        WHEN price BETWEEN 400 AND 700 THEN 'Medium'
        ELSE 'High'
    END;

SELECT book_name,
       category,
       price
FROM (
    SELECT book_name,
           category,
           price,
           RANK() OVER (
               PARTITION BY category
               ORDER BY price DESC
           ) AS category_rank
    FROM Books
) AS ranked
WHERE category_rank = 1;


--F--

SELECT *
FROM Books
WHERE price = (
    SELECT MAX(price)
    FROM Books
    WHERE price < (
        SELECT MAX(price)
        FROM Books
    )
);

SELECT *
FROM Books
WHERE price = (
    SELECT MAX(price)
    FROM Books
    WHERE price < (
        SELECT MAX(price)
        FROM Books
    )
);

SELECT book_name, price
FROM (
    SELECT book_name,
           price,
           DENSE_RANK() OVER (ORDER BY price DESC) AS price_rank
    FROM Books
) AS ranked
WHERE price_rank = 3;

SELECT category
FROM Books
WHERE price = (
    SELECT MAX(price)
    FROM Books
);

WITH author_counts AS (
    SELECT author,
           COUNT(*) AS book_count
    FROM Books
    GROUP BY author
)
SELECT author, book_count
FROM author_counts
WHERE book_count = (
    SELECT MAX(book_count)
    FROM author_counts
);

WITH member_counts AS (
    SELECT Members.member_id,
           Members.member_name,
           COUNT(Borrow.book_id) AS books_borrowed
    FROM Members
    LEFT JOIN Borrow
        ON Members.member_id = Borrow.member_id
    GROUP BY Members.member_id, Members.member_name
)
SELECT *
FROM member_counts
WHERE books_borrowed = (
    SELECT MAX(books_borrowed)
    FROM member_counts
);

SELECT Books.book_name,
       COUNT(Borrow.book_id) AS borrow_count
FROM Books
INNER JOIN Borrow
    ON Books.book_id = Borrow.book_id
GROUP BY Books.book_id, Books.book_name
ORDER BY borrow_count DESC
LIMIT 1;

SELECT *FROM Books WHERE book_id NOT IN (SELECT book_id FROM Borrow);

SELECT category FROM Books GROUP BY category HAVING MIN(price) > 300;

SELECT category FROM Books GROUP BY category HAVING MAX(price) > 800;

SELECT *FROM Books WHERE price > (SELECT AVG(price)FROM Books)
AND price < ( SELECT MAX(price)FROM Books);

SELECT book_name,
       category,
       price
FROM (
    SELECT book_name,
           category,
           price,
           DENSE_RANK() OVER (
               PARTITION BY category
               ORDER BY price DESC
           ) AS price_rank
    FROM Books
) AS ranked
WHERE price_rank <= 2;

SELECT Members.member_name,
       COUNT(DISTINCT Books.category) AS category_count
FROM Members
INNER JOIN Borrow
    ON Members.member_id = Borrow.member_id
INNER JOIN Books
    ON Borrow.book_id = Books.book_id
GROUP BY Members.member_id, Members.member_name
HAVING COUNT(DISTINCT Books.category) > 1;

WITH category_values AS (
    SELECT category,
           SUM(price * stock_quantity) AS total_stock_value
    FROM Books
    GROUP BY category
)
SELECT *
FROM category_values
WHERE total_stock_value = (
    SELECT MAX(total_stock_value)
    FROM category_values
);

SELECT SUM(price * stock_quantity) AS total_inventory_value FROM Books;

SELECT category,SUM(price * stock_quantity) AS inventory_value FROM Books GROUP BY category;

SELECT category,
       SUM(price * stock_quantity) AS inventory_value,
       RANK() OVER (
           ORDER BY SUM(price * stock_quantity) DESC
       ) AS category_rank
FROM Books
GROUP BY category;

SELECT Members.member_name,
       Books.book_name,
       Books.price
FROM Members
INNER JOIN Borrow
    ON Members.member_id = Borrow.member_id
INNER JOIN Books
    ON Borrow.book_id = Books.book_id
WHERE Books.price = (
    SELECT MAX(Books.price)
    FROM Books
    INNER JOIN Borrow
        ON Books.book_id = Borrow.book_id
);

SELECT Members.member_name,
       COUNT(Borrow.book_id) AS books_borrowed,
       RANK() OVER (
           ORDER BY COUNT(Borrow.book_id) DESC
       ) AS member_rank
FROM Members
LEFT JOIN Borrow
    ON Members.member_id = Borrow.member_id
GROUP BY Members.member_id, Members.member_name;

SELECT
    Books.book_name,
    Books.author,
    Books.category,
    Books.price,
    Books.stock_quantity AS stock,
    Members.member_name,
    Borrow.borrow_date,

    CASE
        WHEN Borrow.borrow_id IS NULL THEN 'Not Borrowed'
        ELSE 'Borrowed'
    END AS borrow_status,

    CASE
        WHEN Books.price < 400 THEN 'Low'
        WHEN Books.price BETWEEN 400 AND 700 THEN 'Medium'
        ELSE 'High'
    END AS price_classification

FROM Books

LEFT JOIN Borrow
    ON Books.book_id = Borrow.book_id

LEFT JOIN Members
    ON Borrow.member_id = Members.member_id;