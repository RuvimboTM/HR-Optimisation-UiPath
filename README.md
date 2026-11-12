# HR Recruitment Automation (UiPath)

A UiPath automation that supports the HR department's job recruitment process. It is part of the **"Utilising Robotic Process Automation to Optimise the Job Recruitment Process"** project.

## Part of a larger project

- **Web portal (ASP.NET):** https://github.com/RuvimboTM/HR-Dept-Portal
- **UiPath automation (this repository)**
- **Power BI dashboard:** 
## What it does

- Reads incoming job application emails from Microsoft Outlook 365
- Processes attached CVs and extracts applicant details (education, experience, skills)
- Screens applicants against job requirements
- Stores the results in the MySQL database used by the HR portal and the Power BI dashboard

## Built with

- UiPath Studio
- UiPath Integration Service (Microsoft Outlook 365 connector)
- MySQL (via ODBC)
- Document Understanding / document processing (`DocumentProcessing` folder)

## Prerequisites

- UiPath Studio, signed in to your UiPath account
- A Microsoft Outlook 365 mailbox you can connect to
- **MySQL ODBC driver** installed on your machine
- Access to a MySQL database with the project schema (`cvstream`)

## Setup

### 1. Clone the repository

```
git clone https://github.com/RuvimboTM/HR-Optimisation-UiPath.git
```

Open `project.json` in UiPath Studio.

### 2. Database connection

Real credentials are **not** stored in this repository. In `Main.xaml`, open the **Connect to Database** activity and replace the placeholders in the connection string with your own values:

```
"Driver={MySQL ODBC 9.7 Unicode Driver};Server=YOUR_SERVER;Port=YOUR_PORT;Database=YOUR_DATABASE;User=YOUR_USER;Password=YOUR_PASSWORD;"
```

- If your installed MySQL ODBC driver has a different version number, change it in the `Driver={...}` part.
- **Do not commit your real values.** Put the placeholders back before committing, and check with `git diff` that no real values appear.

### 3. Email connection

This project uses the **Microsoft Outlook 365** connector from UiPath Integration Service. No email password is stored in the project.

1. In UiPath, go to **Integration Service → Connections** and create your own Microsoft Outlook 365 connection.
2. In the **Get Email List** activity, select your own connection from the dropdown.
3. Change the email folder if you don't want to use `"Inbox"`.

### 4. Input folders

The `CV_Folder` and `Processed_CVs` 
## Running the automation

1. Complete the setup steps above.
2. Open `Main.xaml` in UiPath Studio.
3. Click **Run**.

## Dashboard

A Power BI dashboard visualises the recruitment data. 

## Security notes


## Author

Ruvimbo Mandava
