# Jenkins CI/CD Pipeline with Maven and Email Notifications

A complete CI/CD pipeline setup using Jenkins, Maven, and automated email notifications for build status.

## Project Structure

```
Maven_App/
├── src/
│   ├── main/
│   │   └── com/
│   │       └── fortune/
│   │           └── app/
│   │                   └── App.java
│   └── test/
│   │   └── com/
│   │       └── fortune/
│   │           └── app/
│   │                   └── App.java
└── pom.xml
```

## Prerequisites

- AWS EC2 instance
- Jenkins installed and running
- Maven installed on EC2
- SMTP server configured in Jenkins

## Step-by-Step Setup

### 1. Create Maven Project on EC2

```bash
# SSH into your EC2 instance
ssh -i your-key.pem ec2-user@your-ec2-ip

# Create project structure
mkdir -p Maven_App/src/main/com/fortune/app
mkdir -p Maven_App/src/test/com/fortune/app
# Navigate to project directory
cd Maven_App
```

### 2. Create pom.xml

### 3. Create Application Files

Create `App.java`:
```bash
nano src/main/com/fortune/app/App.java
```

Create `AppTest.java`:
```bash
nano src/test/com/fortune/app/App.java
```

### 4. Configure Jenkins Build Job

**Create Build Project:**

1. Open Jenkins Dashboard
2. Click "New Item"
3. Enter name: `build-project`
4. Select "Freestyle project"
5. Click OK

**Configure Build Project:**

- **Source Code Management:** Select Git/None (based on your setup)
- **Build Triggers:** Poll SCM or Build Periodically (optional)
- **Build Steps:**
  - Add build step → "Invoke top-level Maven targets"
  - Goals: `clean compile package`
  - Click Save

### 5. Configure Jenkins Test Job

**Create Test Project:**

1. Click "New Item"
2. Enter name: `test-project`
3. Select "Freestyle project"
4. Click OK

**Configure Test Project:**

- **Build Triggers:**
  - Check "Build after other projects are built"
  - Projects to watch: `build-project`
  - Trigger only if build is stable

- **Build Steps:**
  - Add build step → "Invoke top-level Maven targets"
  - Goals: `test`

- **Post-build Actions:**
  - Add post-build action → "E-mail Notification"
  - Recipients: `your-email@example.com`
  - Check "Send e-mail for every unstable build"
  - Check "Send separate e-mails to individuals who broke the build"
  - Click Save
 
  <img width="1918" height="800" alt="Screenshot 2026-01-02 203000" src="https://github.com/user-attachments/assets/9e77c2c0-87da-4e2d-b39f-0476ef458e6f" />

  <img width="1919" height="714" alt="Screenshot 2026-01-02 203025" src="https://github.com/user-attachments/assets/a8a895fb-1616-47fe-af73-eb4907ddc74d" />
  
  <img width="1919" height="825" alt="Screenshot 2026-01-02 202719" src="https://github.com/user-attachments/assets/6e3b67bf-ebef-4af8-ae06-f3bcb4e8f57a" />


### 6. Configure SMTP Server in Jenkins

1. Go to Jenkins → Manage Jenkins → Configure System
2. Scroll to "E-mail Notification" section
3. Configure SMTP settings:
   - SMTP server: `smtp.gmail.com` (for Gmail)
   - Use SMTP Authentication: ✓
   - User Name: `your-email@gmail.com`
   - Password: `your-app-password`
   - Use SSL: ✓
   - SMTP Port: `465`
   - Reply-To Address: `your-email@gmail.com`
4. Click "Test configuration by sending test e-mail"
5. Click Save


### 7. Run the Pipeline

1. Go to `build-project`
2. Click "Build Now"
3. Monitor console output
4. After successful build, `test-project` will trigger automatically
5. If tests fail, you'll receive an email notification

   <img width="1914" height="849" alt="Screenshot 2026-01-02 202703" src="https://github.com/user-attachments/assets/71e0671c-d8f9-4604-9d4d-410bf41faebc" />

## Pipeline Flow

```
build-project (compile & package)
    ↓ (if successful)
test-project (run tests)
    ↓ (if failed)
Email Notification → Recipient
```

## Email Notification

When tests fail, you'll receive an email with:
- Project name
- Build number
- Build status
- Console output link
- Failure details

## Troubleshooting

**Build fails:**
- Check Maven installation: `mvn --version`
- Verify pom.xml syntax
- Check Java version compatibility

**Email not sending:**
- Verify SMTP settings
- Use App Password for Gmail (not regular password)
- Check firewall settings on EC2
- Test email configuration in Jenkins

**Test project not triggering:**
- Verify build trigger configuration
- Check build-project completed successfully
- Review Jenkins system logs

## Benefits

✅ Automated build process  
✅ Automated testing after successful build  
✅ Instant email notifications on test failures  
✅ Quick feedback loop for developers  
✅ Reduced manual intervention

**Author:** Samarth Funde  
**Date:** 2 January 2026  
**Version:** 1.0
