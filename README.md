# Backend Task Architecture
## 1. General Entities
There are several basic entities in this system's design. Just for the sake of
simplicity of this document. Neither entities listed nor database schema are
final representations and may be simplified in favor of outlining general
understanding of how system works. (e.g. every entity will have
created_at/updated_at timestamps, but they are not included)

Company and customer are just as described in the task document except that they're both
have a payer entity assigned to them on a one-to-one fashion.

### 1. Company
1. name
2. payer
3. invoice_template
4. locations
5. customer

### 2. Customer

1. name
2. payer
3. companies 
4. user

### 3. Location

1. company
2. name
3. address
4. fee - to be paid monthly

### 4. Payer

Payer is an entity representing the receiver of an invoice. Can be assigned
either to a customer or a company.

1. invoice_templates - payer can receive several invoices
2. company | customer

### 5. Invoice Template
Invoice template is an entity representing companies/locations that should be
paid for in a single invoice each month. Can have many companies but strictly
one payer.

1. payer
2. companies
3. invoices - all invoices get saved

### 6. Invoice
Invoice is an entity that represents an already formed and sent document that a
payer received. Current proposition on storing created document is a database blob, but further
evaluation of upsides and downsides of other solutions is advised.

1. invoice_template
2. content - saved document

### 7. User

Just a generic authentication entity, created for logic separation from a
customer. For the sake of simplicity has one-to-one relationship with customer.

1. customer

## 2. Database Schema

![Database Schema](http://i.imgur.com/4muKqgO.png "Database Schema")

Here's the general outline of what it should look like.

## 3. Pages

### 1. 

## 3. Main actions
Default CRUD operations for basic entities will be omitted here for the sake of simplicity.

### 1. Customer creates Invoice Template
Customer 
