# Employee Management System using Salesforce Apex

## Aim

To develop a console-based Employee Management System using Salesforce Apex Programming Language that performs CRUD (Create, Read, Update, Delete) operations on employee records stored in Salesforce cloud database.

---

# Technologies Used

- Salesforce Platform
- Apex Programming Language
- SOQL (Salesforce Object Query Language)
- DML Operations
- Salesforce Developer Console

---

# Employee Details Stored

The system manages the following employee fields:

| Field Label | API Name | Data Type |
|---|---|---|
| Employee Name | `Name` | Text |
| EmpId | `EmpId__c` | Number |
| Email | `Email__c` | Email |
| DOB | `DOB__c` | Date |
| Dept | `Dept__c` | Text |

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
| Label | Employee |
| Plural Label | Employees |
| Object Name | Employee |
| Record Name | Employee Name |

Save the object.

---

# Step 3: Create Custom Fields

Inside the Employee object:

```text
Fields & Relationships → New
```

Create the following fields:

---

## Field 1: EmpId

| Property | Value |
|---|---|
| Data Type | Number |
| Field Label | EmpId |
| API Name | `EmpId__c` |

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

## Field 4: Dept

| Property | Value |
|---|---|
| Data Type | Text |
| Field Label | Dept |
| API Name | `Dept__c` |

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
EmployeeManagementSystem
```

---

# Step 6: Write Apex Program

```java
public class EmployeeManagementSystem {

    // Add Employee
    public static void addEmployee(
        Integer empId,
        String empName,
        String email,
        Date dob,
        String dept
    ) {

        Employee__c emp = new Employee__c();

        emp.EmpId__c = empId;
        emp.Name = empName;
        emp.Email__c = email;
        emp.DOB__c = dob;
        emp.Dept__c = dept;

        insert emp;

        System.debug('Employee Added Successfully');
    }

    // View Employees
    public static void viewEmployees() {

        List<Employee__c> empList = [
            SELECT Id, Name, EmpId__c,
                   Email__c, DOB__c, Dept__c
            FROM Employee__c
        ];

        if(empList.isEmpty()) {
            System.debug('No Employees Found');
            return;
        }

        System.debug('Total Employees: ' + empList.size());

        for(Employee__c emp : empList) {

            System.debug(
                'EmpID: ' + emp.EmpId__c +
                ' | Name: ' + emp.Name +
                ' | Email: ' + emp.Email__c +
                ' | DOB: ' + emp.DOB__c +
                ' | Dept: ' + emp.Dept__c
            );
        }
    }

    // Update Employee using EmpId
    public static void updateEmployee(
        Integer empId,
        String newEmail,
        String newDept
    ) {

        Employee__c emp = [
            SELECT Id, Email__c, Dept__c
            FROM Employee__c
            WHERE EmpId__c = :empId
            LIMIT 1
        ];

        emp.Email__c = newEmail;
        emp.Dept__c = newDept;

        update emp;

        System.debug('Employee Updated Successfully');
    }

    // Delete Employee using EmpId
    public static void deleteEmployee(Integer empId) {

        Employee__c emp = [
            SELECT Id
            FROM Employee__c
            WHERE EmpId__c = :empId
            LIMIT 1
        ];

        delete emp;

        System.debug('Employee Deleted Successfully');
    }
}
```

Save the class using:

```text
Ctrl + S
```

---

# Step 7: Execute Apex Code

Open Execute Anonymous Window:

```text
Debug → Open Execute Anonymous Window
```

Shortcut:

```text
Ctrl + E
```

---

# Step 8: Add Employee

```java
EmployeeManagementSystem.addEmployee(
    101,
    'Sharv Mahajan',
    'sharv@gmail.com',
    Date.newInstance(2005,1,1),
    'IT'
);
```

Click Execute.

---

# Step 9: View Employees

```java
EmployeeManagementSystem.viewEmployees();
```

---

# Step 10: Open Debug Logs

After execution:

```text
Logs → Open Latest Log
```

Search:

```text
USER_DEBUG
```

Output Example:

```text
USER_DEBUG|EmpID: 101 | Name: Sharv Mahajan | Email: sharv@gmail.com | DOB: 2005-01-01 | Dept: IT
```

---

# Step 11: Update Employee

Employee can be updated using EmpId instead of Salesforce Record ID.

```java
EmployeeManagementSystem.updateEmployee(
    101,
    'updated@gmail.com',
    'HR'
);
```

---

# Step 12: Delete Employee

Employee can be deleted using EmpId.

```java
EmployeeManagementSystem.deleteEmployee(101);
```

---

# SOQL Used

```sql
SELECT Id, Name, EmpId__c,
       Email__c, DOB__c, Dept__c
FROM Employee__c
```

---

# DML Operations Used

| Operation | Purpose |
|---|---|
| INSERT | Add employee |
| UPDATE | Modify employee |
| DELETE | Remove employee |

---

# Features

- Cloud-based database storage
- Employee record management
- CRUD operations
- Apex programming
- SOQL queries
- Console-based execution
- Update/Delete using Employee ID

---

# Advantages

- Secure cloud storage
- Easy employee management
- Scalable system
- Fast data retrieval
- Centralized database
- User-friendly operations without Salesforce Record ID

---

# Conclusion

The Employee Management System was successfully implemented using Salesforce Apex. The project demonstrates the use of SOQL queries and DML operations to manage employee data stored in the Salesforce cloud platform. The system allows easy employee management using Employee IDs instead of complex Salesforce Record IDs.
