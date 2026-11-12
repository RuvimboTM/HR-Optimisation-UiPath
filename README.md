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

-- ============================================================
-- HR Recruitment Database (cvstream)
-- Structure only, matching the ER diagram. No data, no credentials.
-- ============================================================

CREATE DATABASE IF NOT EXISTS cvstream
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci;

USE cvstream;

-- ------------------------------------------------------------
-- admin (HR staff who log in to the portal)
-- ------------------------------------------------------------
CREATE TABLE IF NOT EXISTS admin (
    adminId   INT AUTO_INCREMENT PRIMARY KEY,
    email     VARCHAR(100) NOT NULL,
    password  VARCHAR(255) NOT NULL
) ENGINE=InnoDB;

-- ------------------------------------------------------------
-- education_levels
-- ------------------------------------------------------------
CREATE TABLE IF NOT EXISTS education_levels (
    education_level_id  INT AUTO_INCREMENT PRIMARY KEY,
    level_name          VARCHAR(100) NOT NULL
) ENGINE=InnoDB;

-- ------------------------------------------------------------
-- skills
-- ------------------------------------------------------------
CREATE TABLE IF NOT EXISTS skills (
    skill_id    INT AUTO_INCREMENT PRIMARY KEY,
    skill_name  VARCHAR(100) NOT NULL
) ENGINE=InnoDB;

-- ------------------------------------------------------------
-- jobs
-- ------------------------------------------------------------
CREATE TABLE IF NOT EXISTS jobs (
    job_id               INT AUTO_INCREMENT PRIMARY KEY,
    job_title            VARCHAR(100) NOT NULL,
    department           VARCHAR(100),
    education_level_id   INT,
    education_keywords   VARCHAR(255),
    is_active            TINYINT DEFAULT 1,
    experience_keywords  VARCHAR(255),
    CONSTRAINT fk_jobs_education_level
        FOREIGN KEY (education_level_id) REFERENCES education_levels(education_level_id)
) ENGINE=InnoDB;

-- ------------------------------------------------------------
-- job_skills
-- ------------------------------------------------------------
CREATE TABLE IF NOT EXISTS job_skills (
    jobSkills_id      INT AUTO_INCREMENT PRIMARY KEY,
    job_id            INT NOT NULL,
    skill_id          INT NOT NULL,
    importance_order  INT,
    CONSTRAINT fk_job_skills_job   FOREIGN KEY (job_id)   REFERENCES jobs(job_id),
    CONSTRAINT fk_job_skills_skill FOREIGN KEY (skill_id) REFERENCES skills(skill_id)
) ENGINE=InnoDB;

-- ------------------------------------------------------------
-- applicants
-- ------------------------------------------------------------
CREATE TABLE IF NOT EXISTS applicants (
    applicant_id  INT AUTO_INCREMENT PRIMARY KEY,
    email         VARCHAR(255),
    phone         VARCHAR(20),
    education     VARCHAR(255),
    skills        VARCHAR(255)
) ENGINE=InnoDB;

-- ------------------------------------------------------------
-- applicant_skills
-- ------------------------------------------------------------
CREATE TABLE IF NOT EXISTS applicant_skills (
    id            INT AUTO_INCREMENT PRIMARY KEY,
    applicant_id  INT NOT NULL,
    skill_id      INT NOT NULL,
    CONSTRAINT fk_applicant_skills_applicant FOREIGN KEY (applicant_id) REFERENCES applicants(applicant_id),
    CONSTRAINT fk_applicant_skills_skill     FOREIGN KEY (skill_id)     REFERENCES skills(skill_id)
) ENGINE=InnoDB;

-- ------------------------------------------------------------
-- applications
-- ------------------------------------------------------------
CREATE TABLE IF NOT EXISTS applications (
    application_id       INT AUTO_INCREMENT PRIMARY KEY,
    applicant_id         INT NOT NULL,
    job_id               INT NOT NULL,
    qualification_score  INT,
    application_date     DATETIME,
    application_status   VARCHAR(50),
    cv_file_path         VARCHAR(255),
    CONSTRAINT fk_applications_applicant FOREIGN KEY (applicant_id) REFERENCES applicants(applicant_id),
    CONSTRAINT fk_applications_job       FOREIGN KEY (job_id)       REFERENCES jobs(job_id)
) ENGINE=InnoDB;

-- ------------------------------------------------------------
-- education
-- ------------------------------------------------------------
CREATE TABLE IF NOT EXISTS education (
    education_id   INT AUTO_INCREMENT PRIMARY KEY,
    applicant_id   INT NOT NULL,
    qualification  VARCHAR(100),
    CONSTRAINT fk_education_applicant FOREIGN KEY (applicant_id) REFERENCES applicants(applicant_id)
) ENGINE=InnoDB;

-- ------------------------------------------------------------
-- experience
-- ------------------------------------------------------------
CREATE TABLE IF NOT EXISTS experience (
    id            INT AUTO_INCREMENT PRIMARY KEY,
    applicant_id  INT NOT NULL,
    roles         VARCHAR(500),
    field         VARCHAR(150),
    CONSTRAINT fk_experience_applicant FOREIGN KEY (applicant_id) REFERENCES applicants(applicant_id)
) ENGINE=InnoDB;

-- ------------------------------------------------------------
-- INSERT INTO admin (email, password) VALUES ('admin@example.com', 'PASTE_HASHED_PASSWORD_HERE');


## Author

Ruvimbo Mandava
