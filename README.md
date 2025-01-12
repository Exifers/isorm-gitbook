# Presentation

Isorm is an orm that focuses on managing **in-memory data structures synchronously** while **persisting** these data structures **asynchronously**.

It's **isomorphic**, meaning it can run both on the frontend and the backend, so it can be suited to perform **optimistic updates** while sharing business logic between frontend and backend.

The database schema is written using a **zod-like type inference** library, so every query is entirely **typesafe**.

It comes also with some high level features such as **derived entities management**, **row cycles constraints**, **ordered entities**, **duplication** and **populate**.

## Features

* isormorphic
* relationships
* transactions
* cascades: deletion, duplication, population
* ordered entities
* unpersisted entities
* unpersisted fields
* derived entities

