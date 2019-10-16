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
6. email

### 2. Customer

1. name
2. payer
3. companies 
4. user
5. email

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
2. login
3. password - sha-512 encrypted

## 2. Database Schema

![Database Schema](http://i.imgur.com/2CsZf2D.png "Database Schema")

Here's the general outline of what it should look like.

## 3. Main actions
Again, for the sake of simplicity some simple actions like
registration/authorazion are skipped.

### 1. Customer manages Companies 
Companies are a very simple CRUD entities. The only gotcha here is that
they must create an assosiated payer entity along with them.

### 2. Customer manages Invoice Templates
Customer can create as many invoice templates as he wants, setting payer for
each one and including as many companies from the list as he wants. This is
proposed to be done as a simple drag-and-drop with remaining companies on the
right side and created invoice templates on the left side. Payer is picked with
a dropdown menu.

![Manage templates mockup](http://i.imgur.com/jvV78In.png "Manage templates mockup")

Creating relationships for this case is fairly straightforward.

### 3. System sends invoices
System starts a daily script that checks which invoices are due to be sent that
day. 

``` elixir
query = 
    from it in InvoiceTemplate,
    join: p in Payer,
    on: it.id == p.id,
    left_join: cust in Customer,
    on: payer_customer.payer_id == p.id,
    left_join: comp in Company,
    on: payer_company.payer_id == p.id,
    preload: [companies: :locations],
    where: it.billing_day == ^today
```

The query above will yield all invoice templates for the day with associated
companies and payer information. After that it will form a pdf and send it to an
email stored in customer/company.

## 4. Development plan

1. authentication
2. customer CRUD 
3. company CRUD
4. invoice management
5. invoice sending


