# System-Integration

# Keycloak SSO Implementation Documentation

# 1. Introduction
This document provides a detailed guide for implementing Single Sign-On (SSO) using Keycloak on a CentOS 8 VM. It includes steps for installation, configuration, integration, testing, and troubleshooting, along with completion criteria to ensure successful implementation.

# 2. Steps Taken

2.1 Installation

Install Java Development Kit (JDK):

sudo dnf install java-11-openjdk

<img width="282" alt="4 2" src="https://github.com/user-attachments/assets/1ae982a3-409c-41f1-9572-cbc6ffb7291a">

Download Keycloak:

wget https://github.com/keycloak/keycloak/releases/download/21.0.0/keycloak-21.0.0.tar.gz


<img width="431" alt="4 3" src="https://github.com/user-attachments/assets/993eb1df-d5ae-41c6-84f6-3dde5340922c">


Extract the Keycloak Archive:

tar -xzf keycloak-21.0.0.tar.gz

Move Keycloak to the /opt Directory:

sudo mv keycloak-21.0.0 /opt/keycloak

Create a Keycloak User:

sudo useradd -r -s /sbin/nologin keycloak

sudo chown -R keycloak:keycloak /opt/keycloak

Start Keycloak:

sudo -u keycloak /opt/keycloak/bin/kc.sh start


<img width="435" alt="4 4" src="https://github.com/user-attachments/assets/05f28c8c-5d25-45ab-95d9-497ba59b6b58">


2.2 Configuration

Access Keycloak Admin Console:

URL: http://localhost:8080/admin

Default Admin Username: admin


Configure Authentication Methods:

Username/Password: Default method.



<img width="363" alt="4 6" src="https://github.com/user-attachments/assets/df142516-1da6-4711-8c5e-ba50c93582f4">


If you cannot access the server from a localhost address or just want to start Keycloak from the command line, use the KEYCLOAK_ADMIN and KEYCLOAK_ADMIN_PASSWORD environment variables to create an initial admin account.

For example:

export KEYCLOAK_ADMIN=<username>
export KEYCLOAK_ADMIN_PASSWORD=<password>

bin/kc.[sh|bat] start

Multi-Factor Authentication (MFA):

Go to Authentication -> Flows

Modify or create flows to include MFA methods.

Integrate with Active Directory:

Go to User Federation

Add LDAP provider and configure LDAP settings.

<img width="294" alt="4 7" src="https://github.com/user-attachments/assets/3fa01592-2650-487b-a0a5-b43aba4f792a">



Create a New Client:

Go to Clients -> Create

Enter Client ID and protocol (SAML or OpenID Connect)

Configure client settings.

2.3 Integration

Integrate Applications:

For OpenID Connect:

Configure applications with Keycloak’s OAuth 2.0 endpoints.

For SAML:

Provide SAML metadata and configure applications accordingly.

2.4 Testing

Test SSO Functionality:

Verify access to integrated systems after logging into one application.

Confirm that authentication redirects to Keycloak and allows seamless access.

2.3 Integration
OpenID Connect Integration
Access Keycloak Admin Console:

Log in to the Keycloak Admin Console at http://<your-keycloak-server>:8080/auth/admin/.
Select the realm where you want to configure OIDC integration.
Create an OIDC Client:

Navigate to the Clients section in the left-hand menu.
Click Create to add a new client.
Enter a Client ID (e.g., my-app), choose OpenID Connect for the Client Protocol, and click Save.
Configure Client Settings:

Access Type: Choose confidential for server-side applications or public for client-side applications.

Redirect URIs: Specify the URLs where Keycloak can redirect users after authentication. For example, https://yourapp.example.com/callback.

Web Origins: Specify allowed origins to prevent Cross-Origin Resource Sharing (CORS) issues. For example, https://yourapp.example.com.

Client Secret: Note the client secret under the Credentials tab if using confidential access type.

Set Up Mappers:

Go to the Mappers tab and configure mappings to provide necessary user attributes (e.g., roles, groups) to the client application.

Click Create to add new mappers, such as mapping user roles or other attributes.

Configure Application:

In your application, use the Keycloak’s OAuth 2.0 endpoints (such as /auth/realms/{realm}/protocol/openid-connect/token) to implement authentication flows.

Use libraries such as oidc-client for JavaScript or Keycloak Spring Boot Adapter for Java to simplify integration.

SAML Integration

Access Keycloak Admin Console:

Log in to the Keycloak Admin Console at http://<your-keycloak-server>:8080/auth/admin/.

Select the realm where you want to configure SAML integration.
Create a SAML Client:

Navigate to the Clients section.

Click Create to add a new client.

<img width="320" alt="4 9" src="https://github.com/user-attachments/assets/124a715e-5330-46ba-a946-6dfcb7b19851">


Enter a Client ID (e.g., my-saml-app), choose SAML for the Client Protocol, and click Save.

Configure SAML Client Settings:

Client ID: Should be the Entity ID provided by the application.
Name ID Format: Choose the format required by the application (e.g., email, username).

Valid Redirect URIs: Specify the URL where the SAML responses should be sent (e.g., https://yourapp.example.com/saml/acs).
Obtain SAML Metadata:

Go to the Installation tab and select SAML Metadata IDPSSODescriptor.
Download the SAML metadata file, which contains the necessary information for the application to trust Keycloak as the Identity Provider (IdP).

Configure Application:

Import the SAML metadata into your application or manually configure it using the details provided in the metadata file (such as endpoints, certificate).

2.4 Testing

Test SSO Functionality for OpenID Connect:

Login Test: Access the integrated application and initiate a login. Ensure that you are redirected to Keycloak for authentication.

Token Validation: After successful login, verify that the application receives and validates the ID token and access token provided by Keycloak.

Seamless Access: Log in once and then access other applications integrated with Keycloak without being prompted to log in again.

Test SSO Functionality for SAML:

Login Test: Access the SAML-enabled application and attempt to log in. You should be redirected to Keycloak for authentication.

Assertion Validation: Verify that Keycloak sends a valid SAML assertion to the application and that the application correctly processes the assertion to grant access.

Seamless Access: Confirm that after logging into one application, you can access other applications that trust the same SAML assertions without needing to log in again.
Check Logs and Debug:

Keycloak Logs: Check the Keycloak server logs for any errors or warnings during the authentication process. Logs are usually found in standalone/log/server.log for standalone mode or the respective log files if running in a different mode.

Application Logs: Review the logs of the integrated applications to diagnose any issues with token or assertion handling.
Verify User Attributes and Claims:


<img width="425" alt="4 8" src="https://github.com/user-attachments/assets/fc14ed9f-dc70-4211-9211-832e8c5d64e2">



Ensure that user attributes and claims (such as roles, email, etc.) are correctly passed from Keycloak to the integrated applications as expected.
Security Checks:

Check for Exposed Endpoints: Ensure that sensitive endpoints are secured and not exposed to unauthorized users.

Review Token Security: Verify that tokens are encrypted and have the appropriate expiration and refresh settings.

Documentation and Best Practices

Document Configuration Steps: Keep a record of all configuration steps, including settings for both Keycloak and the integrated applications.

Update ReadMe: Ensure that your documentation includes information about configuration files, key endpoints, and any troubleshooting steps.

Regular Updates: Regularly update and review the configuration as necessary to keep up with security patches and changes in requirements.

By following these steps, you can successfully integrate applications with Keycloak for both OpenID Connect and SAML and ensure that SSO functionality works as expected.

# 3. Configuration Settings

3.1 Keycloak Settings

Realm Settings:

Session timeout: Configure in Realm Settings -> Tokens.


<img width="252" alt="p4 11" src="https://github.com/user-attachments/assets/5bfebfbb-c87a-4314-a5e3-0416d972a5b1">



Single Logout: Configure in Realm Settings -> Logout.

Authentication Flows:

Default: Username/Password.

MFA: Set up in Authentication -> Flows.

3.2 Client Configurations

Client ID: Unique identifier for each client.

Protocol: SAML or OpenID Connect.

Redirect URIs: Set per application requirements.

Client Secrets: Securely manage and use in applications.

# 4. Testing Procedures

4.1 Functional Testing

Log into one application: Ensure it redirects to Keycloak.

Access other integrated systems: Verify SSO works without additional logins.

4.2 Security Testing

Test MFA: Verify that MFA is prompted and functions correctly.

Session Management: Check session timeout and logout functionality.

# 5. Troubleshooting Steps

5.1 Common Issues and Resolutions

Issue: Keycloak Not Starting

Resolution: Ensure Java is correctly installed and check logs for errors:

tail -f /opt/keycloak/standalone/log/server.log


<img width="425" alt="4 8" src="https://github.com/user-attachments/assets/38e6af40-8ac8-4ed6-99a7-70516da73484">



Issue: Authentication Failures

Resolution: Verify client credentials and configurations in Keycloak and application settings.

Issue: Application Integration Issues

Resolution: Check endpoint URLs, SAML metadata, or OAuth configurations.

# Key Takeaways

Versatility: Keycloak supports both SAML and OIDC, making it adaptable for various authentication needs.

Security: Provides robust security features including MFA and secure session management.

Ease of Integration: Streamlines the integration of multiple applications with a single identity provider.

Comprehensive Management: Offers detailed control over authentication flows, user federation, and role-based access.

Open Source: Being open-source, Keycloak provides a cost-effective solution with community support and regular updates.

By adhering to the outlined steps and criteria, Keycloak can effectively manage SSO for an organization, enhancing user experience and security across multiple applications.

--------------------------------------------------------------

# Tier 3 Task 2: Integration of Monitoring and Reporting Systems with Notification System

# 1. Install and Configure Grafana on CentOS 8

Step 1.1: Install Grafana

Add the Grafana repository:

sudo tee /etc/yum.repos.d/grafana.repo <<EOF
[grafana]
name=Grafana
baseurl=https://packages.grafana.com/oss/rpm
repo_gpgcheck=1
enabled=1
gpgcheck=1
gpgkey=https://packages.grafana.com/gpg.key
EOF

Install Grafana:

sudo dnf install grafana -y
Start and enable Grafana:

sudo systemctl start grafana-server
sudo systemctl enable grafana-server
Check Grafana status:

sudo systemctl status grafana-server

Step 1.2: Access Grafana Web Interface

Open a browser and go to: http://<your-server-ip>:3000

Default login credentials:

Username: admin

Password: admin (you will be prompted to change it after the first login).

# 2. Install and Configure Rocket.Chat on CentOS 8

Step 2.1: Install Rocket.Chat

Install necessary dependencies:

sudo dnf install epel-release -y
sudo dnf install curl GraphicsMagick gmp-devel -y

Install MongoDB for Rocket.Chat:

sudo tee /etc/yum.repos.d/mongodb-org-4.0.repo <<EOF
[mongodb-org-4.0]

name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/8/mongodb-org/4.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.0.asc
EOF

Then install MongoDB:

sudo dnf install mongodb-org -y
sudo systemctl start mongod
sudo systemctl enable mongod
Install Node.js:

curl -sL https://rpm.nodesource.com/setup_12.x | sudo bash -
sudo dnf install nodejs -y
Download and Install Rocket.Chat:

curl -L https://releases.rocket.chat/latest/download -o rocket.chat.tgz
tar -xzf rocket.chat.tgz
mv bundle /opt/Rocket.Chat
cd /opt/Rocket.Chat/programs/server
npm install
Create a Rocket.Chat service:

sudo tee /lib/systemd/system/rocketchat.service <<EOF
[Unit]
Description=Rocket.Chat server
After=network.target remote-fs.target nss-lookup.target mongod.target

[Service]
ExecStart=/usr/local/bin/node /opt/Rocket.Chat/main.js
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=rocketchat
User=root
Environment=MONGO_URL=mongodb://localhost:27017/rocketchat
Environment=ROOT_URL=http://localhost:3000
Environment=PORT=3000

[Install]
WantedBy=multi-user.target
EOF
Start and enable Rocket.Chat:

sudo systemctl daemon-reload
sudo systemctl start rocketchat
sudo systemctl enable rocketchat

Step 2.2: Access Rocket.Chat Web Interface
Open a browser and go to: http://<your-server-ip>:3000
Set up the admin account and workspace.

# 3. Configure Grafana to Send Alerts to Rocket.Chat

Step 3.1: Create an Incoming Webhook in Rocket.Chat

Login to Rocket.Chat as an admin.

Navigate to: Administration -> Integrations -> New Integration.

Choose Incoming Webhook and set the following:

Name: Grafana Alerts
Channel: Select a channel to receive alerts (e.g., #alerts)
Enabled: Yes
Webhook URL: Save this URL for later use in Grafana.
Step 3.2: Configure Grafana to Use the Webhook
Login to Grafana.

Go to: Alerting -> Notification Channels.

Click New Channel and fill in the following:

Name: Rocket.Chat Alerts
Type: Webhook
URL: Paste the Rocket.Chat Webhook URL from the previous step.
Send reminders: Yes (optional)
Alert OK: Yes (optional)
Click Save.

Step 3.3: Create an Alert Rule in Grafana
Go to: Alerting -> Alert Rules.

Create a New Alert:

Name: Critical Server Health
Condition: Set the metrics you want to monitor (e.g., CPU usage, memory usage, etc.)
Evaluate every: 1 minute
Send to: Select the Rocket.Chat Alerts notification channel.
Save the alert.

# 4. Testing the Integration

Step 4.1: Trigger a Test Alert

Temporarily lower the threshold for one of your monitored metrics to trigger an alert (e.g., CPU usage > 1%).

Confirm that an alert is sent to the specified Rocket.Chat channel.

Step 4.2: Verify Alert in Rocket.Chat

Check that the alert is visible in Rocket.Chat, including the correct message, severity, and the channel it's posted in.

# 5. Troubleshooting

Rocket.Chat Webhook not receiving alerts?

Check firewall settings to ensure the Grafana server can reach Rocket.Chat.

Check the Webhook URL and ensure it's correctly pasted into Grafana.

Review logs for Grafana and Rocket.Chat for any errors:

Grafana logs: /var/log/grafana/grafana.log
Rocket.Chat logs: journalctl -u rocketchat

# 6. Documenting the Integration Process
Ensure the following details are documented:

Grafana setup steps: Including repository configuration, installation, and startup.

Rocket.Chat setup steps: MongoDB, Node.js, Rocket.Chat installation, and Web Interface setup.

Webhook configuration: In Rocket.Chat, details of how the incoming Webhook was created.

Alert configuration: In Grafana, notification channel setup, alert rule creation.

Testing results: Include screenshots or logs of successful alerts being sent to Rocket.Chat.

Troubleshooting steps: Note down any issues encountered and their solutions.
This step-by-step process ensures that you can successfully integrate Grafana with Rocket.Chat on CentOS 8 for monitoring and incident notification.








