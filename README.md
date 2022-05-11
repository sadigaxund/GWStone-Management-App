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

 - BILLS 
  ```
  INVOICE_NUMBER, SALES, PRICE
  ```
 - EARLIEST_ORDERS - sql view that represents a query that fetches and sorts the earliest orders
 -
 - s
 - s
 - ss
 - 
