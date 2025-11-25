# 27520-adeline-uwera-book-managment-system
✔ A PL/SQL project demonstrating Collections, Records through a Book  Management System
CREATE TABLE book (
    book_id       NUMBER PRIMARY KEY,
    title         VARCHAR2(200),
    author        VARCHAR2(100),
    price         NUMBER
);
INSERT INTO book VALUES (1, 'Oracle SQL Basics', 'Alice M', 450);
INSERT INTO book VALUES (2, 'PL/SQL Mastery', 'John K', 700);
INSERT INTO book VALUES (3, 'Database Design', 'Chris R', 1200);
INSERT INTO book VALUES (4, 'Data Structures', 'David S', 300);
INSERT INTO book VALUES (5, 'Advanced Algorithms', 'Martha T', 1500);

COMMIT;
SET SERVEROUTPUT ON;

DECLARE

    TYPE book_table_type IS TABLE OF book%ROWTYPE;
    book_list book_table_type;

  
    TYPE book_summary_rec IS RECORD (
        total_books NUMBER,
        avg_price   NUMBER,
        high_price  NUMBER
    );

    summary book_summary_rec;

    -- Temporary variables
    b book%ROWTYPE;
    price_sum NUMBER := 0;

BEGIN
  
    SELECT * BULK COLLECT INTO book_list FROM book;

    DBMS_OUTPUT.PUT_LINE('Loaded ' || book_list.COUNT || ' book records.');

    summary.total_books := book_list.COUNT;
    summary.high_price := 0;

  
    FOR i IN 1 .. book_list.COUNT LOOP
        b := book_list(i);

        price_sum := price_sum + b.price;

        IF b.price > summary.high_price THEN
            summary.high_price := b.price;
        END IF;

        
        IF b.price > 1000 THEN
            GOTO expensive_book;
        END IF;
    END LOOP;

    
    summary.avg_price := price_sum / summary.total_books;

  
    DBMS_OUTPUT.PUT_LINE('----- BOOK SUMMARY -----');
    DBMS_OUTPUT.PUT_LINE('Total Books: ' || summary.total_books);
    DBMS_OUTPUT.PUT_LINE('Average Price: ' || summary.avg_price);
    DBMS_OUTPUT.PUT_LINE('Highest Price: ' || summary.high_price);

    RETURN;

    <<expensive_book>>
    DBMS_OUTPUT.PUT_LINE('*** EXPENSIVE BOOK DETECTED ***');
    DBMS_OUTPUT.PUT_LINE('A book priced above 1000 was found.');
    DBMS_OUTPUT.PUT_LINE('Triggering price review process...');

END;
/
