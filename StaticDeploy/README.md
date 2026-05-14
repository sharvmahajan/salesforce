# Static Website Deployment on AWS EC2 Ubuntu using `npx serve`

## Objective

Deploy a simple static website (`index.html`) on an AWS EC2 Ubuntu instance using Node.js and `npx serve`.

---

## Technologies Used

- AWS EC2
- Ubuntu Server
- Node.js
- npm
- `serve` package

---

## Project Structure

```text
project/
├── index.html
├── style.css
└── script.js
```

---

## Step 1: Create an EC2 Instance

Open the AWS EC2 Console and launch a new instance.

Use these settings:

- **Name:** `static-website`
- **AMI:** Ubuntu Server
- **Instance Type:** `t2.micro`
- **Key Pair:** create or select an existing `.pem` file

### Security Group Rules

Allow:

- SSH (22)
- HTTP (80)

Then launch the instance.

---

## Step 2: Connect to EC2

Open terminal in the folder containing your `.pem` file and run:

```bash
ssh -i your-key.pem ubuntu@YOUR_PUBLIC_IP
```

Example:

```bash
ssh -i mykey.pem ubuntu@13.233.xx.xx
```

---

## Step 3: Update Packages

```bash
sudo apt update
```

---

## Step 4: Install Node.js and npm

```bash
sudo apt install nodejs npm -y
```

Verify installation:

```bash
node -v
npm -v
```

---

## Step 5: Create Website Files

Create a project folder:

```bash
mkdir mywebsite
cd mywebsite
```

Create the HTML file:

```bash
nano index.html
```

Add the following content:

```html
<!DOCTYPE html>
<html>
<head>
    <title>My AWS Website</title>
</head>
<body>
    <h1>Hello from AWS EC2</h1>
</body>
</html>
```

Save the file:

- Press `CTRL + O`
- Press `Enter`
- Press `CTRL + X`

---

## Step 6: Run the Website Using `npx serve`

Run the static site on port 80:

```bash
sudo npx serve -l 80
```

### What this does

- `npx` runs a package without installing it globally
- `serve` starts a lightweight static file server
- `-l 80` makes the website available on port 80

---

## Step 7: Open the Website

Open this in your browser:

```text
http://YOUR_PUBLIC_IP
```

Example:

```text
http://13.233.xx.xx
```

Your static website will be live.

---

## Step 8: Run the Website in Background

Install PM2:

```bash
sudo npm install -g pm2
```

Start the website with PM2:

```bash
sudo pm2 start "npx serve -l 80"
```

Check running processes:

```bash
pm2 list
```

Save PM2 processes:

```bash
pm2 save
```

Enable startup after reboot:

```bash
pm2 startup
```

Run the command shown in the terminal after that.

---

## Useful Commands

### Stop the Website

```bash
pm2 stop all
```

### Restart the Website

```bash
pm2 restart all
```

### Delete the Process

```bash
pm2 delete all
```

---

## Architecture

```text
Browser
   ↓
AWS EC2 Ubuntu Server
   ↓
npx serve
   ↓
Static Website Files
```

---

## Advantages

- Simple deployment
- No Apache or Nginx configuration required
- Good for practicals and demos
- Fast setup

---

## Limitations

- Suitable mainly for static websites
- Not ideal for production use
- HTTPS is not configured
- Less optimized than Nginx or Apache

---

## Viva Questions

### What is EC2?

EC2 stands for Elastic Compute Cloud. It is a virtual server service provided by AWS.

### What is a static website?

A website made of fixed HTML, CSS, and JavaScript files without server-side processing.

### What is `npx serve`?

A lightweight Node.js package used to host static files quickly.

### Difference between `npm` and `npx`

| npm | npx |
|---|---|
| Installs packages | Executes packages directly |
| Permanent install | Temporary execution |

### Why use port 80?

Port 80 is the default HTTP port used by browsers.

### Why use PM2?

PM2 keeps Node.js applications running continuously in the background.

### What is a Security Group?

A virtual firewall that controls inbound and outbound traffic for an EC2 instance.
