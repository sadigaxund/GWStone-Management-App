# GWStone Management App
### Link for the App
https://apex.oracle.com/pls/apex/r/sakhund/gwstones-management108118669655/login?session=8786205007097

### Problem Definition
GWStones:  Students who attend GWU like to take away souvenirs.  So do parents and even campus visitors.  This business gives them a piece of granite that has any inscription they like carved into it.  The carving is done by a small custom laser inscriber, driven by  a computer, in the GWStones store.  People can walk right in and order a GWStone; they come in various sizes.  Typically they are picked up the day after they are ordered, but for an extra 20% the order can be rushed and done right away.  You are to design a database for tracking the entire GWStones operation.  Take the orders, including size of the stone, the inscription (different size stones can have inscriptions of different lengths), cost, regular or rush, promised delivery time.  Then indicate the date and time the customer picks up the order and the amount paid.  You need to inventory blank stones in different sizes and order more blanks when your stock drops below a 2 day supply for any size.  

The Web app collects orders and payment, and checks on delivery possibilities and promises early delivery when it is ordered and confirmed.  The management application tracks inventory as well as analyzing sales and revenue and reporting them.  The management application also pays the employees.

## Original ERD:
![image](https://user-images.githubusercontent.com/48419889/167836652-019ba128-554f-4591-b32b-119e0c6bf001.png)

## ERD After redesign:
![image (1)](https://user-images.githubusercontent.com/48419889/167837599-38b79c7c-5b6d-4afc-823d-cc4d6e2831a9.png)

## Revised Relational Schema
![image (2)](https://user-images.githubusercontent.com/48419889/167837643-9aadd23a-8106-490f-bca9-7b5b14adb453.png)

## Views
 - EARLIEST_ORDERS - sql view that represents a query that fetches and sorts the earliest orders
 ```
    SELECT   * 
    FROM     QUEUES 
    ORDER BY PICKUP_DATETIME;
  ```
 - REPLENISH - sql view used to retrieve all the stones that fall below monthly threshold of 10 pieces.
 ```
    SELECT  ROW_NUMBER() OVER(ORDER BY G."NAME" ASC) AS ID,
            G."NAME" STONE_NAME,
            S.CARATS,
            G.PRICE PRICE_PER_KARAT, 
            S.MAX_LENGTH, 
            S.SHAPE, 
            I.AMOUNT 
    FROM    INVENTORY I, 
            GWSTONE G,
            SIZES S 
    WHERE   G.ID = I.STONE_ID   AND
            I.SIZE_ID = S.ID    AND
            AMOUNT < 10;
  ```
 - READY_ORDERS, PROCESSING_ORDERS, PLACED_ORDERS - sql views that are used to simplify life of the user. They basically sort out orders based on status. Here is general script where `n` can have values 0, 1, 2 which represent orders that are just placed, in progress and finished, respectfully.
```
    SELECT  * 
    FROM    ORDERS 
    WHERE   STATUS_CODE = n;
```
 - ORDERS_DETAILS - sql view that display full details of the order. Resulted from joining multiple tables.
```
    SELECT  Q.INVOICE_NUMBER, 
            G.NAME, 
            S.CARATS, 
            Q.INSCRIPTION, 
            S.SHAPE, 
            G.PRICE PRICE_PER_CARAT, 
            S.CARATS * PRICE TOTAL_PRICE
    FROM    QUEUES Q, 
            GWSTONE G, 
            SIZES S 
    WHERE   Q.STONE_ID = G.ID AND Q.SIZE_ID = S.ID;
```
 - BILLS - sql view that retrieves all the orders and corresponding contents from the queue. When a user pays their bills that order and its contents are cleared from the queue. Of course given that they were finished.
```
    SELECT  Q.INVOICE_NUMBER, SUM(S.CARATS*PRICE) AS TOTAL_PRICE, 
            SUM(S.CARATS * PRICE)*(100 - NVL(O.DISCOUNT, 0))/100 AS DISCOUNTED_PRICE 
    FROM    QUEUES Q, 
            GWSTONE G, 
            SIZES S, 
            ORDERS O
    WHERE   Q.STONE_ID = G.ID   AND 
            Q.SIZE_ID = S.ID    AND 
            Q.INVOICE_NUMBER = O.INVOICE_NUMBER
    GROUP BY Q.INVOICE_NUMBER, O.DISCOUNT;
```
 - DUE_DATES - sql view shows all the upcoming deadlines for the stones to be finished. Used for an algorithm that sorts and decides which order to process what day, considering utmost effectiveness.
```
    SELECT  O.PLACED_DATETIME,
            RES.PICKUP_DATETIME, 
            RES.INVOICE_NUMBER
    FROM    (
                SELECT  PICKUP_DATETIME, 
                        INVOICE_NUMBER,
                        ROW_NUMBER() OVER (PARTITION BY INVOICE_NUMBER ORDER BY PICKUP_DATETIME DESC) AS RowNumber
                FROM    QUEUES
            ) RES,
            ORDERS O
    WHERE   RES.RowNumber = 1 AND 
            O.INVOICE_NUMBER = RES.INVOICE_NUMBER;
```
 - STONE_VIEW - sql view that displays an instance of gwstone in full details.
```
    SELECT  ROW_NUMBER() OVER(ORDER BY G."NAME" ASC) AS Row#,
            G."NAME" STONE_NAME, 
            S.CARATS, 
            G.PRICE PRICE_PER_KARAT, 
            S.MAX_LENGTH, 
            S.SHAPE, 
            I.AMOUNT 
    FROM    INVENTORY I, 
            GWSTONE G, 
            SIZES S
    WHERE   I.STONE_ID = G.ID AND 
            I.SIZE_ID = S.ID;
```
