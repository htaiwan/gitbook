# Chapter 2: Swift Standard Library

#### 前言

在Swift Standard Library中已經幫我們實作兩個最基礎也最重要的資料結構 - Array & Dictionary，先來看看這兩個基礎資料結構的特色。

------

#### 大綱

- [Array](#1)
  - Order
  - Random-access
  - Array performance
    - Insertion location
- [Dictionary](#2)

------

<h2 id="1">Array</h2>

- 有順序性。
- **Random-access** is a trait that data structures can claim if they can handle element retrieval in **a constant amount of time**. 
- 插入
  - 末端: constant-time operation
  - 如果常常需要插入資料到Array前端，那最好換個資料結構，因為效率會很差。
  - 再過來Swift在產生Array時，是先給予固定的空間，若插入資料導致大於原本空間，就會有額外的效能耗費在空間調整。

------

<h2 id="2">Dictionary</h2>

- 無順序性。
- Key-value配對。
- 插入值，並沒有像array的問題，需要進行資料搬移動作。
- Inserting into a dictionary always takes a constant amount of time. Lookup operations also take a constant amount of time, which is significantly faster than finding a particular element in an array。
