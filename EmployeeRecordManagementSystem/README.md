# Employee Management System using Salesforce Apex and Visualforce

## Aim

To develop an Employee Management System in Salesforce using Apex Programming Language and Visualforce Page that allows users to:

- Add Employee
- Search Employee
- Show All Employees
- Update Employee
- Delete Employee

with proper validation.

The system validates the following before saving:

- Employee Name cannot be empty and must contain at least 3 characters.
- Employee ID must be greater than 0 and should be unique.
- Salary must be greater than 10,000 and less than 500,000.
- Email cannot be empty and must follow a valid email format.
- Department must be selected from the available list.
- Joining Date cannot be a future date.

---

# Technologies Used

- Salesforce Platform
- Apex Programming Language
- Visualforce Page
- SOQL (Salesforce Object Query Language)
- DML Operations

---

# Custom Object and Fields

Create a custom object named:

```text
Employee_Record__c
```

Create the following fields:

| Field Label | API Name | Type |
|---|---|---|
| Employee Name | `Name` | Text |
| Employee ID | `EmpId__c` | Number |
| Salary | `Salary__c` | Currency |
| Email | `Email__c` | Email |
| Department | `Department__c` | Picklist/Text |
| Joining Date | `Joining_Date__c` | Date |

---

# Step 1: Create Salesforce Account

1. Open Salesforce Developer Signup
2. Create a free Developer Account
3. Verify email and login

---

# Step 2: Create Custom Object

Go to:

```text
Setup → Object Manager → Create → Custom Object
```

Enter:

| Property | Value |
|---|---|
| Label | Employee Record |
| Plural Label | Employee Records |
| Object Name | Employee_Record |
| Record Name | Employee Name |

Save the object.

---

# Step 3: Create Custom Fields

Inside Employee Record object:

```text
Fields & Relationships → New
```

Create the following fields.

---

## Employee ID

| Property | Value |
|---|---|
| Data Type | Number |
| Field Label | EmpId |
| API Name | `EmpId__c` |

---

## Salary

| Property | Value |
|---|---|
| Data Type | Currency |
| Field Label | Salary |
| API Name | `Salary__c` |

---

## Email

| Property | Value |
|---|---|
| Data Type | Email |
| Field Label | Email |
| API Name | `Email__c` |

---

## Department

| Property | Value |
|---|---|
| Data Type | Picklist/Text |
| Field Label | Department |
| API Name | `Department__c` |

Example Picklist Values:

```text
HR
IT
Finance
Admin
Sales
```

---

## Joining Date

| Property | Value |
|---|---|
| Data Type | Date |
| Field Label | Joining Date |
| API Name | `Joining_Date__c` |

---

# Step 4: Open Developer Console

Go to:

```text
Setup → Developer Console
```

---

# Step 5: Create Apex Controller

In Developer Console:

```text
File → New → Apex Class
```

Class Name:

```text
EmployeeRecordController
```

---

# Step 6: Write Apex Controller Code

```java
public class EmployeeRecordController {

    public Employee_Record__c empObj {get; set;}

    public List<Employee_Record__c> empList {get; set;}

    public List<SelectOption> deptOptions {get; set;}

    public String searchText {get; set;}

    public Boolean isEditMode {get; set;}

    // Constructor
    public EmployeeRecordController() {

        empObj = new Employee_Record__c();

        empList = new List<Employee_Record__c>();

        searchText = '';

        isEditMode = false;

        deptOptions = new List<SelectOption>();

        deptOptions.add(
            new SelectOption('', '-- Select Department --')
        );

        deptOptions.add(
            new SelectOption('HR', 'HR')
        );

        deptOptions.add(
            new SelectOption('IT', 'IT')
        );

        deptOptions.add(
            new SelectOption('Finance', 'Finance')
        );

        deptOptions.add(
            new SelectOption('Admin', 'Admin')
        );

        deptOptions.add(
            new SelectOption('Sales', 'Sales')
        );

        showEmployees();
    }

    // Save or Update Employee
    public void saveEmployee() {

        // Employee Name Validation
        if(String.isBlank(empObj.Name) ||
           empObj.Name.trim().length() < 3) {

            ApexPages.addMessage(
                new ApexPages.Message(
                    ApexPages.Severity.ERROR,
                    'Employee Name must contain at least 3 characters.'
                )
            );

            return;
        }

        // Employee ID Validation
        if(empObj.EmpId__c == null ||
           empObj.EmpId__c <= 0) {

            ApexPages.addMessage(
                new ApexPages.Message(
                    ApexPages.Severity.ERROR,
                    'Employee ID must be greater than 0.'
                )
            );

            return;
        }

        // Unique Employee ID Validation
        List<Employee_Record__c> existingEmp = [

            SELECT Id
            FROM Employee_Record__c
            WHERE EmpId__c = :empObj.EmpId__c
            AND Id != :empObj.Id
            LIMIT 1
        ];

        if(!existingEmp.isEmpty()) {

            ApexPages.addMessage(
                new ApexPages.Message(
                    ApexPages.Severity.ERROR,
                    'Employee ID must be unique.'
                )
            );

            return;
        }

        // Salary Validation
        if(empObj.Salary__c == null ||
           empObj.Salary__c <= 10000 ||
           empObj.Salary__c >= 500000) {

            ApexPages.addMessage(
                new ApexPages.Message(
                    ApexPages.Severity.ERROR,
                    'Salary must be greater than 10,000 and less than 500,000.'
                )
            );

            return;
        }

        // Email Empty Validation
        if(String.isBlank(empObj.Email__c)) {

            ApexPages.addMessage(
                new ApexPages.Message(
                    ApexPages.Severity.ERROR,
                    'Email cannot be empty.'
                )
            );

            return;
        }

        // Email Format Validation
        String emailValue = empObj.Email__c.trim();

        Pattern emailPattern = Pattern.compile(
            '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}$'
        );

        Matcher emailMatcher =
            emailPattern.matcher(emailValue);

        if(!emailMatcher.matches()) {

            ApexPages.addMessage(
                new ApexPages.Message(
                    ApexPages.Severity.ERROR,
                    'Please enter a valid email address.'
                )
            );

            return;
        }

        // Department Validation
        if(String.isBlank(empObj.Department__c)) {

            ApexPages.addMessage(
                new ApexPages.Message(
                    ApexPages.Severity.ERROR,
                    'Please select a department.'
                )
            );

            return;
        }

        // Joining Date Validation
        if(empObj.Joining_Date__c == null) {

            ApexPages.addMessage(
                new ApexPages.Message(
                    ApexPages.Severity.ERROR,
                    'Joining Date is required.'
                )
            );

            return;
        }

        if(empObj.Joining_Date__c > Date.today()) {

            ApexPages.addMessage(
                new ApexPages.Message(
                    ApexPages.Severity.ERROR,
                    'Joining Date cannot be a future date.'
                )
            );

            return;
        }

        // Insert or Update
        if(isEditMode == false) {

            insert empObj;

            ApexPages.addMessage(
                new ApexPages.Message(
                    ApexPages.Severity.CONFIRM,
                    'Employee added successfully.'
                )
            );
        }
        else {

            update empObj;

            ApexPages.addMessage(
                new ApexPages.Message(
                    ApexPages.Severity.CONFIRM,
                    'Employee updated successfully.'
                )
            );

            isEditMode = false;
        }

        // Clear Form
        empObj = new Employee_Record__c();

        // Refresh Table
        showEmployees();
    }

    // Show All Employees
    public void showEmployees() {

        empList = [

            SELECT Id,
                   Name,
                   EmpId__c,
                   Salary__c,
                   Email__c,
                   Department__c,
                   Joining_Date__c

            FROM Employee_Record__c
        ];
    }

    // Search Employees
    public void searchEmployees() {

        empList = [

            SELECT Id,
                   Name,
                   EmpId__c,
                   Salary__c,
                   Email__c,
                   Department__c,
                   Joining_Date__c

            FROM Employee_Record__c

            WHERE Name LIKE :('%' + searchText + '%')
        ];
    }

    // Edit Employee
    public void editEmployee() {

        String empId =
            ApexPages.currentPage()
            .getParameters()
            .get('empId');

        empObj = [

            SELECT Id,
                   Name,
                   EmpId__c,
                   Salary__c,
                   Email__c,
                   Department__c,
                   Joining_Date__c

            FROM Employee_Record__c

            WHERE Id = :empId
            LIMIT 1
        ];

        isEditMode = true;
    }

    // Delete Employee
    public void deleteEmployee() {

        String empId =
            ApexPages.currentPage()
            .getParameters()
            .get('empId');

        Employee_Record__c emp = [

            SELECT Id
            FROM Employee_Record__c
            WHERE Id = :empId
            LIMIT 1
        ];

        delete emp;

        ApexPages.addMessage(
            new ApexPages.Message(
                ApexPages.Severity.CONFIRM,
                'Employee deleted successfully.'
            )
        );

        showEmployees();
    }
}
```

Save the class:

```text
Ctrl + S
```

---

# Step 7: Create Visualforce Page

In Developer Console:

```text
File → New → Visualforce Page
```

Page Name:

```text
EmployeeRecordPage
```

---

# Step 8: Write Visualforce Page Code

```html
<apex:page controller="EmployeeRecordController">

    <apex:form>

        <!-- Messages -->
        <apex:pageMessages id="msgBlock" />

        <!-- Employee Form -->
        <apex:pageBlock
            title="Employee Management System"
            id="formBlock">

            <apex:pageBlockSection columns="2">

                <apex:inputField
                    value="{!empObj.Name}" />

                <apex:inputField
                    value="{!empObj.EmpId__c}" />

                <apex:inputField
                    value="{!empObj.Salary__c}" />

                <apex:inputField
                    value="{!empObj.Email__c}" />

                <!-- Department -->
                <apex:selectList
                    value="{!empObj.Department__c}"
                    size="1">

                    <apex:selectOptions
                        value="{!deptOptions}" />

                </apex:selectList>

                <apex:inputField
                    value="{!empObj.Joining_Date__c}" />

            </apex:pageBlockSection>

            <!-- Save Button -->
            <apex:commandButton
                value="{!IF(isEditMode,
                            'Update Employee',
                            'Save Employee')}"

                action="{!saveEmployee}"

                rerender="empTable,msgBlock,formBlock" />

        </apex:pageBlock>

        <!-- Search Section -->
        <apex:pageBlock title="Search Employee">

            <apex:pageBlockSection columns="2">

                <apex:inputText
                    value="{!searchText}"
                    label="Employee Name" />

            </apex:pageBlockSection>

            <apex:commandButton
                value="Search"
                action="{!searchEmployees}"
                rerender="empTable" />

            <apex:commandButton
                value="Show All Employees"
                action="{!showEmployees}"
                rerender="empTable" />

        </apex:pageBlock>

        <!-- Employee Table -->
        <apex:pageBlock
            title="Employee Records"
            id="empTable">

            <apex:pageBlockTable
                value="{!empList}"
                var="e">

                <apex:column
                    value="{!e.Name}"
                    headerValue="Employee Name" />

                <apex:column
                    value="{!e.EmpId__c}"
                    headerValue="Employee ID" />

                <apex:column
                    value="{!e.Salary__c}"
                    headerValue="Salary" />

                <apex:column
                    value="{!e.Email__c}"
                    headerValue="Email" />

                <apex:column
                    value="{!e.Department__c}"
                    headerValue="Department" />

                <apex:column
                    value="{!e.Joining_Date__c}"
                    headerValue="Joining Date" />

                <!-- Edit Button -->
                <apex:column headerValue="Edit">

                    <apex:commandLink
                        value="Edit"
                        action="{!editEmployee}"
                        rerender="formBlock,msgBlock">

                        <apex:param
                            name="empId"
                            value="{!e.Id}" />

                    </apex:commandLink>

                </apex:column>

                <!-- Delete Button -->
                <apex:column headerValue="Delete">

                    <apex:commandLink
                        value="Delete"
                        action="{!deleteEmployee}"
                        rerender="empTable,msgBlock">

                        <apex:param
                            name="empId"
                            value="{!e.Id}" />

                    </apex:commandLink>

                </apex:column>

            </apex:pageBlockTable>

        </apex:pageBlock>

    </apex:form>

</apex:page>
```

Save the page.

---

# Step 9: Run Visualforce Page

Open:

```text
Preview → EmployeeRecordPage
```

OR

```text
https://yourDomain.apex/EmployeeRecordPage
```

---

# Features

- Add Employee
- Search Employee
- Show All Employees
- Update Employee
- Delete Employee
- Validation using Apex
- Employee Records Table
- Unique Employee ID Validation
- Department Dropdown
- Error Messages
- Salesforce Cloud Storage
- Visualforce UI

---

# Validation Logic

| Validation | Condition |
|---|---|
| Employee Name | Minimum 3 characters |
| Employee ID | Greater than 0 |
| Employee ID | Must be unique |
| Salary | Between 10,000 and 500,000 |
| Email | Cannot be empty |
| Email | Must be valid format |
| Department | Must be selected |
| Joining Date | Cannot be future date |

---

# DML Operations Used

| Operation | Purpose |
|---|---|
| INSERT | Add Employee |
| UPDATE | Update Employee |
| DELETE | Delete Employee |

---

# Working Flow

```text
Add Employee
      ↓
Employee Table Refreshes
      ↓
Search Employee / Show All Employees
      ↓
Edit Employee
      ↓
Form Auto-Fills
      ↓
Update Employee
      ↓
Delete Employee
```

---

# Advantages

- Fully supported in Developer Console
- No VS Code required
- No Salesforce CLI required
- User-friendly UI
- Real-time employee table
- Proper validation
- CRUD operations support
- Search functionality

---

# Conclusion

The Employee Management System was successfully implemented using Salesforce Apex and Visualforce. The application supports complete CRUD operations including add, search, update, delete, and employee listing with proper validation.
