# Email Notification System using Salesforce Apex and Visualforce

## Aim

To develop an Apex program in Salesforce that sends an email notification to a specified email address using Salesforce Email Services.

The system should:
- accept recipient email,
- accept email subject,
- accept email body,
- optionally send attachment,
- validate email format,
- show appropriate error message for invalid email ID,
- send email using Salesforce Messaging class,
- provide frontend using Visualforce Page.

---

# Technologies Used

- Salesforce Platform
- Apex Programming Language
- Visualforce Page
- Messaging Class
- SingleEmailMessage
- EmailFileAttachment

---

# Concepts Used

| Concept | Purpose |
|---|---|
| Visualforce Page | Frontend UI |
| Apex Controller | Backend Logic |
| `Messaging.SingleEmailMessage` | Send Email |
| `EmailFileAttachment` | Add Attachment |
| Regex Validation | Validate Email |
| `ApexPages.addMessage()` | Show Messages |

---

# Step 1: Open Developer Console

Go to:

```text
Setup → Developer Console
```

---

# Step 2: Create Apex Controller

In Developer Console:

```text
File → New → Apex Class
```

Class Name:

```text
EmailNotificationController
```

---

# Step 3: Write Apex Controller Code

```java
public class EmailNotificationController {

    // Form Variables
    public String recipientEmail {get; set;}

    public String emailSubject {get; set;}

    public String emailBody {get; set;}

    public Boolean includeAttachment {get; set;}

    // Send Email Method
    public void sendEmail() {

        // Empty Email Validation
        if(String.isBlank(recipientEmail)) {

            ApexPages.addMessage(
                new ApexPages.Message(
                    ApexPages.Severity.ERROR,
                    'Email ID cannot be empty.'
                )
            );

            return;
        }

        // Email Format Validation
        Pattern emailPattern = Pattern.compile(
            '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}$'
        );

        Matcher emailMatcher =
            emailPattern.matcher(
                recipientEmail.trim()
            );

        if(!emailMatcher.matches()) {

            ApexPages.addMessage(
                new ApexPages.Message(
                    ApexPages.Severity.ERROR,
                    'Please enter a valid email ID.'
                )
            );

            return;
        }

        try {

            // Create Email Object
            Messaging.SingleEmailMessage mail =
                new Messaging.SingleEmailMessage();

            // Set Recipient
            mail.setToAddresses(
                new String[] {
                    recipientEmail.trim()
                }
            );

            // Set Subject
            mail.setSubject(
                String.isBlank(emailSubject)
                ? 'No Subject'
                : emailSubject
            );

            // Set Email Body
            mail.setPlainTextBody(
                String.isBlank(emailBody)
                ? 'No Message'
                : emailBody
            );

            // Prevent Activity Creation
            mail.setSaveAsActivity(false);

            // Optional Attachment
            if(includeAttachment == true) {

                Messaging.EmailFileAttachment attachment =
                    new Messaging.EmailFileAttachment();

                // File Name
                attachment.setFileName(
                    'SampleAttachment.txt'
                );

                // File Content
                attachment.setBody(
                    Blob.valueOf(
                        'This is sample attachment content.'
                    )
                );

                // File Type
                attachment.setContentType(
                    'text/plain'
                );

                // Add Attachment
                mail.setFileAttachments(
                    new Messaging.EmailFileAttachment[] {
                        attachment
                    }
                );
            }

            // Send Email
            Messaging.SendEmailResult[] result =
                Messaging.sendEmail(
                    new Messaging.SingleEmailMessage[] {
                        mail
                    }
                );

            // Success Check
            if(result != null &&
               result.size() > 0 &&
               result[0].isSuccess()) {

                ApexPages.addMessage(
                    new ApexPages.Message(
                        ApexPages.Severity.CONFIRM,
                        'Email Sent Successfully.'
                    )
                );
            }
            else {

                String errorMessage =
                    'Unknown Error Occurred';

                if(result != null &&
                   result.size() > 0 &&
                   result[0].getErrors() != null &&
                   result[0].getErrors().size() > 0) {

                    errorMessage =
                        result[0]
                        .getErrors()[0]
                        .getMessage();
                }

                ApexPages.addMessage(
                    new ApexPages.Message(
                        ApexPages.Severity.ERROR,
                        errorMessage
                    )
                );
            }
        }

        catch(Exception e) {

            ApexPages.addMessage(
                new ApexPages.Message(
                    ApexPages.Severity.ERROR,
                    e.getMessage()
                )
            );
        }
    }
}
```

Save the class:

```text
Ctrl + S
```

---

# Step 4: Create Visualforce Page

In Developer Console:

```text
File → New → Visualforce Page
```

Page Name:

```text
EmailNotificationPage
```

---

# Step 5: Write Visualforce Page Code

```html
<apex:page controller="EmailNotificationController">

    <apex:form>

        <!-- Messages -->
        <apex:pageMessages />

        <!-- Main Block -->
        <apex:pageBlock
            title="Email Notification System">

            <apex:pageBlockSection columns="1">

                <!-- Recipient Email -->
                <apex:inputText
                    value="{!recipientEmail}"
                    label="Recipient Email" />

                <!-- Email Subject -->
                <apex:inputText
                    value="{!emailSubject}"
                    label="Email Subject" />

                <!-- Email Body -->
                <apex:inputTextarea
                    value="{!emailBody}"
                    label="Email Message"
                    rows="5"
                    cols="60" />

                <!-- Attachment Checkbox -->
                <apex:inputCheckbox
                    value="{!includeAttachment}" />

                <apex:outputLabel
                    value="Include Attachment" />

            </apex:pageBlockSection>

            <!-- Send Button -->
            <apex:commandButton
                value="Send Email"
                action="{!sendEmail}" />

        </apex:pageBlock>

    </apex:form>

</apex:page>
```

Save the page.

---

# Step 6: Run the Visualforce Page

Open:

```text
Preview → EmailNotificationPage
```

OR

```text
https://yourDomain.apex/EmailNotificationPage
```

---

# Step 7: Configure Salesforce Email Settings

## Enable Email Deliverability

Go to:

```text
Setup → Deliverability
```

Set:

```text
Access Level = All Email
```

This step is mandatory.

---

# Step 8: Verify Salesforce Email Address

Go to:

```text
Setup → My Email Settings
```

Verify your Salesforce email address if required.

---

# Validation Logic

| Validation | Condition |
|---|---|
| Recipient Email | Cannot be empty |
| Recipient Email | Must be valid format |

---

# Features

- Send Email
- Optional Attachment
- Invalid Email Validation
- Visualforce Frontend
- Success/Error Messages
- Salesforce Messaging Service

---

# Attachment Details

| Property | Purpose |
|---|---|
| File Name | `SampleAttachment.txt` |
| Content Type | `text/plain` |
| Content | Sample text |

---

# Important Messaging Methods Used

| Method | Purpose |
|---|---|
| `setToAddresses()` | Set recipient |
| `setSubject()` | Set subject |
| `setPlainTextBody()` | Set body |
| `setFileAttachments()` | Add attachment |
| `setSaveAsActivity(false)` | Prevent activity creation |
| `sendEmail()` | Send email |

---

# Working Flow

```text
User Enters Email Details
            ↓
Email Validation Executes
            ↓
If Invalid → Error Message
            ↓
If Valid → Email Object Created
            ↓
(Optional Attachment Added)
            ↓
Messaging.sendEmail()
            ↓
Success Message Displayed
```

---

# Example Outputs

## Invalid Email

```text
Please enter a valid email ID.
```

---

## Empty Email

```text
Email ID cannot be empty.
```

---

## Successful Email

```text
Email Sent Successfully.
```

---

# Important Notes

- Emails may appear in Spam or Promotions folder.
- Use real email addresses for testing.
- `Deliverability = All Email` is required.
- Attachment is optional.

---

# Advantages

- User-friendly frontend
- Real-time validation
- Supports attachments
- Uses built-in Salesforce services
- Secure email handling
- Easy to use

---

# Applications

- Employee Notifications
- Alerts and Reminders
- Order Confirmation
- Report Distribution
- Customer Notifications
- System Alerts

---

# Conclusion

The Email Notification System was successfully implemented using Salesforce Apex and Visualforce. The application validates recipient email IDs, sends emails with or without attachments using Salesforce Messaging Services, and displays appropriate success or error messages through a frontend Visualforce interface.
