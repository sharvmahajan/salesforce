# Bank Account System using Salesforce Apex

## Aim

To develop a menu-driven Bank Account System using Salesforce Apex Programming Language that manages customer records stored in Salesforce cloud database.

---

# Technologies Used

- Salesforce Platform
- Apex Programming Language
- SOQL (Salesforce Object Query Language)
- DML Operations
- Salesforce Developer Console

---

# Customer Details Stored

The system manages the following customer fields:

| Field Label | API Name | Data Type |
|---|---|---|
| Customer Name | `Name` | Text |
| Customer ID | `CustomerId__c` | Number |
| Email | `Email__c` | Email |
| Birth Date | `DOB__c` | Date |
| Department | `Department__c` | Text |

---

# Step 1: Create Salesforce Account

1. Open Salesforce Developer Signup  
2. Create a free Developer Account  
3. Verify email and login  

---

# Step 2: Create Custom Object

## Open Object Manager

```text
Setup → Object Manager → Create → Custom Object
```

## Enter Details

| Property | Value |
|---|---|
| Label | Customer |
| Plural Label | Customers |
| Object Name | Customer |
| Record Name | Customer Name |

Save the object.

---

# Step 3: Create Custom Fields

Inside the Customer object:

```text
Fields & Relationships → New
```

Create the following fields:

---

## Field 1: CustomerId

| Property | Value |
|---|---|
| Data Type | Number |
| Field Label | CustomerId |
| API Name | `CustomerId__c` |

---

## Field 2: Email

| Property | Value |
|---|---|
| Data Type | Email |
| Field Label | Email |
| API Name | `Email__c` |

---

## Field 3: DOB

| Property | Value |
|---|---|
| Data Type | Date |
| Field Label | DOB |
| API Name | `DOB__c` |

---

## Field 4: Department

| Property | Value |
|---|---|
| Data Type | Text |
| Field Label | Department |
| API Name | `Department__c` |

---

# Step 4: Open Developer Console

```text
Setup → Developer Console
```

---

# Step 5: Create Apex Class

Inside Developer Console:

```text
File → New → Apex Class
```

Class Name:

```text
BankAccountSystem
```

---

# Step 6: Write Apex Program

```java
public class BankAccountSystem {

    // Display Menu
    public static void showMenu() {

        System.debug('===== Bank Account System =====');
        System.debug('1. Add Customer');
        System.debug('2. View Customers');
        System.debug('3. Update Customer');
        System.debug('4. Delete Customer');
        System.debug('5. Exit');
    }

    // Add Customer
    public static void addCustomer(
        Integer customerId,
        String customerName,
        String email,
        Date dob,
        String department
    ) {

        Customer__c cust = new Customer__c();

        cust.CustomerId__c = customerId;
        cust.Name = customerName;
        cust.Email__c = email;
        cust.DOB__c = dob;
        cust.Department__c = department;

        insert cust;

        System.debug('Customer Added Successfully');
    }

    // View Customers
    public static void viewCustomers() {

        List<Customer__c> custList = [
            SELECT Id, Name, CustomerId__c,
                   Email__c, DOB__c, Department__c
            FROM Customer__c
        ];

        if(custList.isEmpty()) {

            System.debug('No Customers Found');
            return;
        }

        System.debug('Total Customers: ' + custList.size());

        for(Customer__c cust : custList) {

            System.debug(
                'CustomerID: ' + cust.CustomerId__c +
                ' | Name: ' + cust.Name +
                ' | Email: ' + cust.Email__c +
                ' | DOB: ' + cust.DOB__c +
                ' | Department: ' + cust.Department__c
            );
        }
    }

    // Update Customer
    public static void updateCustomer(
        Integer customerId,
        String newEmail,
        String newDepartment
    ) {

        Customer__c cust = [
            SELECT Id, Email__c, Department__c
            FROM Customer__c
            WHERE CustomerId__c = :customerId
            LIMIT 1
        ];

        cust.Email__c = newEmail;
        cust.Department__c = newDepartment;

        update cust;

        System.debug('Customer Updated Successfully');
    }

    // Delete Customer
    public static void deleteCustomer(Integer customerId) {

        Customer__c cust = [
            SELECT Id
            FROM Customer__c
            WHERE CustomerId__c = :customerId
            LIMIT 1
        ];

        delete cust;

        System.debug('Customer Deleted Successfully');
    }
}
```

Save the class using:

```text
Ctrl + S
```

---

# Step 7: Simulated Menu-Driven Execution

Since Salesforce Apex does not support interactive console input like Java or C++, menu-driven functionality is simulated using Execute Anonymous Window.

Display Menu:

```java
BankAccountSystem.showMenu();
```

Output:

```text
===== Bank Account System =====

1. Add Customer
2. View Customers
3. Update Customer
4. Delete Customer
5. Exit
```

---

# Step 8: Add Customer

```java
BankAccountSystem.addCustomer(
    101,
    'Sharv Mahajan',
    'sharv@gmail.com',
    Date.newInstance(2005,1,1),
    'Finance'
);
```

---

# Step 9: View Customers

```java
BankAccountSystem.viewCustomers();
```

---

# Step 10: Update Customer

```java
BankAccountSystem.updateCustomer(
    101,
    'updated@gmail.com',
    'HR'
);
```

---

# Step 11: Delete Customer

```java
BankAccountSystem.deleteCustomer(101);
```

---

# Step 12: View Debug Logs

After execution:

```text
Logs → Open Latest Log
```

Search:

```text
USER_DEBUG
```

Example Output:

```text
USER_DEBUG|CustomerID: 101 | Name: Sharv Mahajan | Email: sharv@gmail.com | DOB: 2005-01-01 | Department: Finance
```

---

# SOQL Used

```sql
SELECT Id, Name, CustomerId__c,
       Email__c, DOB__c, Department__c
FROM Customer__c
```

---

# DML Operations Used

| Operation | Purpose |
|---|---|
| INSERT | Add customer |
| UPDATE | Modify customer |
| DELETE | Remove customer |

---

# Features

- Cloud-based customer record management
- Menu-driven console simulation
- CRUD operations
- Apex programming
- SOQL queries
- Salesforce cloud database

---

# Advantages

- Secure cloud storage
- Easy customer management
- Fast data retrieval
- Centralized database
- User-friendly operations using Customer ID

---

# Limitations

Salesforce Apex Developer Console does not support:
- Scanner class
- stdin input
- Interactive terminal programs

Therefore, menu-driven functionality is simulated using:
- Apex methods
- Execute Anonymous Window
- System.debug() output

---

# Conclusion

The Bank Account System was successfully implemented using Salesforce Apex. The project demonstrates CRUD operations, SOQL queries, and DML operations for managing customer records stored in Salesforce cloud database through a simulated menu-driven console system.
