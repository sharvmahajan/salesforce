# Product Inventory Management System using Salesforce Apex and Visualforce

## Aim

To develop a Product Inventory Management System in Salesforce using Apex Programming Language and Visualforce Page to manage product inventory records stored in a custom object.

The system supports:

- Add Product
- Search Product
- Update Product
- Delete Product

---

# Technologies Used

- Salesforce Platform
- Apex Programming Language
- Visualforce Page
- SOQL (Salesforce Object Query Language)
- DML Operations

---

# Product Details Stored

The system manages the following product fields:

| Field Label | API Name | Data Type |
|---|---|---|
| Product Name | `Name` | Text |
| Serial No | `Serial_No__c` | Text |
| Manufacture Date | `Manufacture_Date__c` | Date |
| Expiry Date | `Expiry_Date__c` | Date |

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
| Label | Product Inventory |
| Plural Label | Product Inventories |
| Object Name | Product_Inventory |
| Record Name | Product Name |

Save the object.

---

# Step 3: Create Custom Fields

Inside the Product Inventory object:

```text
Fields & Relationships → New
```

Create the following fields:

---

## Field 1: Serial No

| Property | Value |
|---|---|
| Data Type | Text |
| Field Label | Serial No |
| API Name | `Serial_No__c` |

---

## Field 2: Manufacture Date

| Property | Value |
|---|---|
| Data Type | Date |
| Field Label | Manufacture Date |
| API Name | `Manufacture_Date__c` |

---

## Field 3: Expiry Date

| Property | Value |
|---|---|
| Data Type | Date |
| Field Label | Expiry Date |
| API Name | `Expiry_Date__c` |

---

# Step 4: Open Developer Console

```text
Setup → Developer Console
```

---

# Step 5: Create Apex Controller

Inside Developer Console:

```text
File → New → Apex Class
```

Class Name:

```text
ProductInventoryController
```

---

# Step 6: Write Apex Controller Code

```java
public class ProductInventoryController {

    // Product Form Object
    public Product_Inventory__c productObj {get; set;}

    // Product List
    public List<Product_Inventory__c> productList {get; set;}

    // Search Text
    public String searchText {get; set;}

    // Selected Product ID
    public Id selectedProductId {get; set;}

    // Edit Mode
    public Boolean isEditMode {get; set;}

    // Constructor
    public ProductInventoryController() {

        productObj = new Product_Inventory__c();

        productList = new List<Product_Inventory__c>();

        searchText = '';

        isEditMode = false;

        loadProducts();
    }

    // Load Products
    public void loadProducts() {

        productList = [
            SELECT Id,
                   Name,
                   Serial_No__c,
                   Manufacture_Date__c,
                   Expiry_Date__c
            FROM Product_Inventory__c
        ];
    }

    // Search Product
    public void searchProduct() {

        productList = [
            SELECT Id,
                   Name,
                   Serial_No__c,
                   Manufacture_Date__c,
                   Expiry_Date__c
            FROM Product_Inventory__c
            WHERE Name LIKE :('%' + searchText + '%')
        ];
    }

    // Add Product
    public void addProduct() {

        insert productObj;

        ApexPages.addMessage(
            new ApexPages.Message(
                ApexPages.Severity.CONFIRM,
                'Product Added Successfully'
            )
        );

        clearForm();

        loadProducts();
    }

    // Edit Product
    public void editProduct() {

        selectedProductId =
            ApexPages.currentPage()
            .getParameters()
            .get('pid');

        productObj = [
            SELECT Id,
                   Name,
                   Serial_No__c,
                   Manufacture_Date__c,
                   Expiry_Date__c
            FROM Product_Inventory__c
            WHERE Id = :selectedProductId
            LIMIT 1
        ];

        isEditMode = true;
    }

    // Update Product
    public void updateProduct() {

        update productObj;

        ApexPages.addMessage(
            new ApexPages.Message(
                ApexPages.Severity.CONFIRM,
                'Product Updated Successfully'
            )
        );

        clearForm();

        loadProducts();
    }

    // Delete Product
    public void deleteProduct() {

        Id productId =
            ApexPages.currentPage()
            .getParameters()
            .get('pid');

        Product_Inventory__c prod = [
            SELECT Id
            FROM Product_Inventory__c
            WHERE Id = :productId
            LIMIT 1
        ];

        delete prod;

        ApexPages.addMessage(
            new ApexPages.Message(
                ApexPages.Severity.CONFIRM,
                'Product Deleted Successfully'
            )
        );

        loadProducts();
    }

    // Clear Form
    public void clearForm() {

        productObj = new Product_Inventory__c();

        selectedProductId = null;

        isEditMode = false;
    }
}
```

Save the class using:

```text
Ctrl + S
```

---

# Step 7: Create Visualforce Page

Inside Developer Console:

```text
File → New → Visualforce Page
```

Page Name:

```text
ProductInventoryPage
```

---

# Step 8: Write Visualforce Page Code

```html
<apex:page controller="ProductInventoryController">

    <apex:form>

        <!-- Messages -->
        <apex:pageMessages id="msg" />

        <!-- Search Section -->
        <apex:pageBlock title="Search Product">

            <apex:pageBlockSection columns="2">

                <apex:inputText
                    value="{!searchText}"
                    label="Product Name" />

            </apex:pageBlockSection>

            <apex:commandButton
                value="Search"
                action="{!searchProduct}"
                rerender="tableSection,msg" />

            <apex:commandButton
                value="Show All"
                action="{!loadProducts}"
                rerender="tableSection,msg" />

        </apex:pageBlock>

        <!-- Product Form -->
        <apex:pageBlock
            title="Product Inventory Management System"
            id="mainBlock">

            <apex:pageBlockSection columns="2">

                <apex:inputField
                    value="{!productObj.Name}" />

                <apex:inputField
                    value="{!productObj.Serial_No__c}" />

                <apex:inputField
                    value="{!productObj.Manufacture_Date__c}" />

                <apex:inputField
                    value="{!productObj.Expiry_Date__c}" />

            </apex:pageBlockSection>

            <!-- Add Button -->
            <apex:commandButton
                value="Add Product"
                action="{!addProduct}"
                rendered="{!NOT(isEditMode)}"
                rerender="mainBlock,tableSection,msg" />

            <!-- Update Button -->
            <apex:commandButton
                value="Update Product"
                action="{!updateProduct}"
                rendered="{!isEditMode}"
                rerender="mainBlock,tableSection,msg" />

        </apex:pageBlock>

        <!-- Product Table -->
        <apex:pageBlock
            title="Product Records"
            id="tableSection">

            <apex:pageBlockTable
                value="{!productList}"
                var="p">

                <apex:column
                    value="{!p.Name}"
                    headerValue="Product Name" />

                <apex:column
                    value="{!p.Serial_No__c}"
                    headerValue="Serial No" />

                <apex:column
                    value="{!p.Manufacture_Date__c}"
                    headerValue="Manufacture Date" />

                <apex:column
                    value="{!p.Expiry_Date__c}"
                    headerValue="Expiry Date" />

                <!-- Edit Button -->
                <apex:column headerValue="Edit">

                    <apex:commandLink
                        value="Edit"
                        action="{!editProduct}"
                        rerender="mainBlock,msg">

                        <apex:param
                            name="pid"
                            value="{!p.Id}" />

                    </apex:commandLink>

                </apex:column>

                <!-- Delete Button -->
                <apex:column headerValue="Delete">

                    <apex:commandLink
                        value="Delete"
                        action="{!deleteProduct}"
                        rerender="tableSection,msg">

                        <apex:param
                            name="pid"
                            value="{!p.Id}" />

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
Preview → ProductInventoryPage
```

OR

```text
https://yourDomain.apex/ProductInventoryPage
```

---

# Features

- Add Product
- Search Product
- Update Product
- Delete Product
- Product Inventory Table
- Salesforce Cloud Database Storage
- Visualforce User Interface

---

# SOQL Used

```sql
SELECT Id,
       Name,
       Serial_No__c,
       Manufacture_Date__c,
       Expiry_Date__c
FROM Product_Inventory__c
```

---

# DML Operations Used

| Operation | Purpose |
|---|---|
| INSERT | Add product |
| UPDATE | Modify product |
| DELETE | Remove product |

---

# Working Flow

1. User enters product details
2. Product is stored in Salesforce custom object
3. Products are displayed in a table
4. User can search products by product name
5. User can click Edit to modify product details
6. Form auto-fills with existing product data
7. User clicks Update Product to save changes
8. User can delete products
9. Table refreshes automatically

---

# Advantages

- Cloud-based inventory management
- User-friendly Visualforce UI
- Secure data storage
- Easy product tracking
- Centralized database
- Real-time product management

---

# Conclusion

The Product Inventory Management System was successfully implemented using Salesforce Apex and Visualforce. The application supports complete CRUD operations and search functionality for managing product inventory records stored in Salesforce custom objects.
